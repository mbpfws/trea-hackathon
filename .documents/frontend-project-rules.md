# Frontend Project Rules - AI Language Learning Platform

## Project Overview
You are building the frontend for an AI-powered language learning platform that provides personalized, interactive language assessment and conversation practice. The platform uses advanced AI to create adaptive learning experiences with real-time feedback and multimodal content generation.

## Technology Stack

### Core Framework
- **Next.js 15.1.8** with App Router (NOT Pages Router)
- **React 19** with Server Components as default
- **TypeScript** for all code
- **Tailwind CSS** for styling
- **DaisyUI** for component library

### AI Integration
- **Vercel AI SDK (@ai-sdk/react, ai)** for chat interfaces and streaming
- **@ai-sdk/openai** for OpenAI integration
- **@ai-sdk/google** for Google Gemini integration

### Audio/Speech
- **Web Speech API** (built-in browser API)
- **ElevenLabs API** for text-to-speech
- **Deepgram API** for speech-to-text

### Forms & Validation
- **React Hook Form** for form management
- **Zod** for schema validation

### Database Client
- **@neondatabase/serverless** for Neon Database connection
- **@zilliz/milvus2-sdk-node** for Zilliz Cloud vector operations

## Architecture Principles

### Next.js 15 App Router Patterns
```typescript
// Server Components (default) - for data fetching
export default async function Page({ params, searchParams }: {
  params: Promise<{ id: string }>
  searchParams: Promise<{ [key: string]: string | string[] | undefined }>
}) {
  const resolvedParams = await params
  const resolvedSearchParams = await searchParams
  
  // Fetch data directly in Server Component
  const data = await fetch('...', { cache: 'no-store' })
  return <div>...</div>
}

// Client Components - only when needed for interactivity
'use client'
import { useState } from 'react'
```

### Async Runtime APIs (Next.js 15)
```typescript
// Always await these APIs in Next.js 15
const cookieStore = await cookies()
const headersList = await headers()
const { isEnabled } = await draftMode()
```

### Route Handlers
```typescript
// app/api/chat/route.ts
import { streamText } from 'ai'
import { openai } from '@ai-sdk/openai'

export const maxDuration = 30

export async function POST(request: Request) {
  const { messages } = await request.json()
  
  const result = await streamText({
    model: openai('gpt-4o'),
    messages,
    tools: {
      // Tool definitions
    },
  })
  
  return result.toDataStreamResponse()
}
```

## AI SDK Integration Patterns

### Chat Interface with useChat
```typescript
'use client'

import { useChat } from '@ai-sdk/react'

export default function ChatInterface() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
    api: '/api/chat',
    maxSteps: 5, // Enable multi-step interactions
  })

  return (
    <div className="flex flex-col h-full">
      <div className="flex-1 overflow-y-auto">
        {messages.map(message => (
          <div key={message.id} className="mb-4">
            <div className="font-semibold">
              {message.role === 'user' ? 'You' : 'AI'}
            </div>
            <div className="prose">{message.content}</div>
            
            {/* Handle tool invocations */}
            {message.toolInvocations?.map(toolInvocation => {
              const { toolName, toolCallId, state } = toolInvocation
              
              if (state === 'result') {
                return (
                  <div key={toolCallId} className="mt-2">
                    {/* Render tool results */}
                  </div>
                )
              }
              
              return (
                <div key={toolCallId} className="mt-2">
                  <div className="loading loading-spinner">Loading...</div>
                </div>
              )
            })}
          </div>
        ))}
      </div>
      
      <form onSubmit={handleSubmit} className="border-t pt-4">
        <div className="flex gap-2">
          <input
            value={input}
            onChange={handleInputChange}
            placeholder="Type your message..."
            className="input input-bordered flex-1"
            disabled={isLoading}
          />
          <button 
            type="submit" 
            className="btn btn-primary"
            disabled={isLoading}
          >
            Send
          </button>
        </div>
      </form>
    </div>
  )
}
```

### Streaming Text Generation
```typescript
'use client'

import { useCompletion } from '@ai-sdk/react'

export default function TextGeneration() {
  const { completion, input, handleInputChange, handleSubmit, isLoading } = useCompletion({
    api: '/api/completion',
  })

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <textarea
          value={input}
          onChange={handleInputChange}
          placeholder="Enter your prompt..."
          className="textarea textarea-bordered w-full"
        />
        <button type="submit" className="btn btn-primary" disabled={isLoading}>
          Generate
        </button>
      </form>
      
      {completion && (
        <div className="mt-4 p-4 bg-base-200 rounded">
          <pre className="whitespace-pre-wrap">{completion}</pre>
        </div>
      )}
    </div>
  )
}
```

## Audio Integration Patterns

### Speech Recognition (Web Speech API)
```typescript
'use client'

import { useState, useEffect } from 'react'

export default function SpeechRecognition() {
  const [isListening, setIsListening] = useState(false)
  const [transcript, setTranscript] = useState('')
  const [recognition, setRecognition] = useState<SpeechRecognition | null>(null)

  useEffect(() => {
    if (typeof window !== 'undefined' && 'webkitSpeechRecognition' in window) {
      const recognition = new (window as any).webkitSpeechRecognition()
      recognition.continuous = true
      recognition.interimResults = true
      recognition.lang = 'en-US'
      
      recognition.onresult = (event: SpeechRecognitionEvent) => {
        let finalTranscript = ''
        for (let i = event.resultIndex; i < event.results.length; i++) {
          if (event.results[i].isFinal) {
            finalTranscript += event.results[i][0].transcript
          }
        }
        setTranscript(finalTranscript)
      }
      
      setRecognition(recognition)
    }
  }, [])

  const startListening = () => {
    if (recognition) {
      recognition.start()
      setIsListening(true)
    }
  }

  const stopListening = () => {
    if (recognition) {
      recognition.stop()
      setIsListening(false)
    }
  }

  return (
    <div>
      <button
        onClick={isListening ? stopListening : startListening}
        className={`btn ${isListening ? 'btn-error' : 'btn-primary'}`}
      >
        {isListening ? 'Stop Recording' : 'Start Recording'}
      </button>
      
      {transcript && (
        <div className="mt-4 p-4 bg-base-200 rounded">
          <p>{transcript}</p>
        </div>
      )}
    </div>
  )
}
```

### Text-to-Speech Integration
```typescript
'use client'

import { useState } from 'react'

interface TTSProps {
  text: string
  voice?: string
}

export default function TextToSpeech({ text, voice = 'en-US' }: TTSProps) {
  const [isPlaying, setIsPlaying] = useState(false)

  const speak = async () => {
    if ('speechSynthesis' in window) {
      setIsPlaying(true)
      
      const utterance = new SpeechSynthesisUtterance(text)
      utterance.lang = voice
      utterance.onend = () => setIsPlaying(false)
      
      speechSynthesis.speak(utterance)
    } else {
      // Fallback to ElevenLabs API
      try {
        const response = await fetch('/api/tts', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ text, voice })
        })
        
        const audioBlob = await response.blob()
        const audioUrl = URL.createObjectURL(audioBlob)
        const audio = new Audio(audioUrl)
        
        setIsPlaying(true)
        audio.onended = () => setIsPlaying(false)
        await audio.play()
      } catch (error) {
        console.error('TTS Error:', error)
        setIsPlaying(false)
      }
    }
  }

  return (
    <button
      onClick={speak}
      disabled={isPlaying}
      className="btn btn-circle btn-sm"
      title="Play audio"
    >
      {isPlaying ? (
        <div className="loading loading-spinner loading-xs"></div>
      ) : (
        <svg className="w-4 h-4" fill="currentColor" viewBox="0 0 20 20">
          <path d="M10 12a2 2 0 100-4 2 2 0 000 4z"/>
          <path fillRule="evenodd" d="M.458 10C1.732 5.943 5.522 3 10 3s8.268 2.943 9.542 7c-1.274 4.057-5.064 7-9.542 7S1.732 14.057.458 10zM14 10a4 4 0 11-8 0 4 4 0 018 0z" clipRule="evenodd"/>
        </svg>
      )}
    </button>
  )
}
```

## Form Handling with React Hook Form

```typescript
'use client'

import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'

const assessmentSchema = z.object({
  language: z.string().min(1, 'Language is required'),
  level: z.enum(['beginner', 'intermediate', 'advanced']),
  topics: z.array(z.string()).min(1, 'Select at least one topic'),
})

type AssessmentForm = z.infer<typeof assessmentSchema>

export default function AssessmentSetup() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
    watch,
  } = useForm<AssessmentForm>({
    resolver: zodResolver(assessmentSchema),
    defaultValues: {
      language: '',
      level: 'beginner',
      topics: [],
    },
  })

  const onSubmit = async (data: AssessmentForm) => {
    try {
      const response = await fetch('/api/assessment/create', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data),
      })
      
      if (!response.ok) throw new Error('Failed to create assessment')
      
      // Handle success
    } catch (error) {
      console.error('Error:', error)
    }
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-6">
      <div className="form-control">
        <label className="label">
          <span className="label-text">Target Language</span>
        </label>
        <select
          {...register('language')}
          className="select select-bordered"
        >
          <option value="">Select a language</option>
          <option value="spanish">Spanish</option>
          <option value="french">French</option>
          <option value="german">German</option>
        </select>
        {errors.language && (
          <label className="label">
            <span className="label-text-alt text-error">
              {errors.language.message}
            </span>
          </label>
        )}
      </div>

      <div className="form-control">
        <label className="label">
          <span className="label-text">Current Level</span>
        </label>
        <div className="flex gap-4">
          {['beginner', 'intermediate', 'advanced'].map((level) => (
            <label key={level} className="label cursor-pointer">
              <input
                type="radio"
                {...register('level')}
                value={level}
                className="radio radio-primary"
              />
              <span className="label-text ml-2 capitalize">{level}</span>
            </label>
          ))}
        </div>
      </div>

      <button
        type="submit"
        disabled={isSubmitting}
        className="btn btn-primary w-full"
      >
        {isSubmitting ? (
          <span className="loading loading-spinner"></span>
        ) : (
          'Create Assessment'
        )}
      </button>
    </form>
  )
}
```

## Styling Guidelines

### DaisyUI Component Usage
```typescript
// Use DaisyUI classes for consistent styling
<div className="card bg-base-100 shadow-xl">
  <div className="card-body">
    <h2 className="card-title">Assessment Results</h2>
    <p>Your progress in Spanish conversation</p>
    <div className="card-actions justify-end">
      <button className="btn btn-primary">Continue Learning</button>
    </div>
  </div>
</div>

// Progress indicators
<div className="radial-progress text-primary" style={{"--value": 70}}>
  70%
</div>

// Loading states
<div className="skeleton h-32 w-full"></div>
<span className="loading loading-spinner loading-lg"></span>

// Alerts and notifications
<div className="alert alert-success">
  <svg className="stroke-current shrink-0 h-6 w-6" fill="none" viewBox="0 0 24 24">
    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"></path>
  </svg>
  <span>Assessment completed successfully!</span>
</div>
```

### Responsive Design
```typescript
// Mobile-first responsive classes
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
  <div className="card">
    {/* Content */}
  </div>
</div>

// Responsive text sizing
<h1 className="text-2xl md:text-4xl lg:text-6xl font-bold">
  Language Learning Platform
</h1>

// Responsive spacing
<div className="p-4 md:p-8 lg:p-12">
  {/* Content */}
</div>
```

## Error Handling

### Error Boundaries
```typescript
'use client'

import { Component, ReactNode } from 'react'

interface Props {
  children: ReactNode
  fallback?: ReactNode
}

interface State {
  hasError: boolean
  error?: Error
}

export class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props)
    this.state = { hasError: false }
  }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error }
  }

  componentDidCatch(error: Error, errorInfo: any) {
    console.error('Error caught by boundary:', error, errorInfo)
  }

  render() {
    if (this.state.hasError) {
      return (
        this.props.fallback || (
          <div className="alert alert-error">
            <svg className="stroke-current shrink-0 h-6 w-6" fill="none" viewBox="0 0 24 24">
              <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z"></path>
            </svg>
            <span>Something went wrong. Please try again.</span>
          </div>
        )
      )
    }

    return this.props.children
  }
}
```

### API Error Handling
```typescript
// Custom hook for API calls
import { useState } from 'react'

interface ApiState<T> {
  data: T | null
  loading: boolean
  error: string | null
}

export function useApi<T>() {
  const [state, setState] = useState<ApiState<T>>({
    data: null,
    loading: false,
    error: null,
  })

  const execute = async (apiCall: () => Promise<T>) => {
    setState({ data: null, loading: true, error: null })
    
    try {
      const result = await apiCall()
      setState({ data: result, loading: false, error: null })
      return result
    } catch (error) {
      const errorMessage = error instanceof Error ? error.message : 'An error occurred'
      setState({ data: null, loading: false, error: errorMessage })
      throw error
    }
  }

  return { ...state, execute }
}
```

## Performance Optimization

### Code Splitting and Lazy Loading
```typescript
import { lazy, Suspense } from 'react'

// Lazy load heavy components
const AssessmentInterface = lazy(() => import('./AssessmentInterface'))
const ConversationPractice = lazy(() => import('./ConversationPractice'))

export default function LearningDashboard() {
  return (
    <div>
      <Suspense fallback={<div className="skeleton h-96 w-full"></div>}>
        <AssessmentInterface />
      </Suspense>
      
      <Suspense fallback={<div className="skeleton h-96 w-full"></div>}>
        <ConversationPractice />
      </Suspense>
    </div>
  )
}
```

### Memoization
```typescript
import { memo, useMemo, useCallback } from 'react'

interface MessageProps {
  message: {
    id: string
    content: string
    role: 'user' | 'assistant'
    timestamp: Date
  }
  onReply: (messageId: string) => void
}

const Message = memo(({ message, onReply }: MessageProps) => {
  const formattedTime = useMemo(() => {
    return message.timestamp.toLocaleTimeString()
  }, [message.timestamp])

  const handleReply = useCallback(() => {
    onReply(message.id)
  }, [message.id, onReply])

  return (
    <div className="chat chat-start">
      <div className="chat-header">
        {message.role}
        <time className="text-xs opacity-50 ml-2">{formattedTime}</time>
      </div>
      <div className="chat-bubble">{message.content}</div>
      <div className="chat-footer">
        <button onClick={handleReply} className="btn btn-xs">
          Reply
        </button>
      </div>
    </div>
  )
})

Message.displayName = 'Message'
```

## Security Best Practices

### Input Sanitization
```typescript
import DOMPurify from 'isomorphic-dompurify'

// Sanitize HTML content
const sanitizeHtml = (html: string) => {
  return DOMPurify.sanitize(html, {
    ALLOWED_TAGS: ['p', 'br', 'strong', 'em', 'ul', 'ol', 'li'],
    ALLOWED_ATTR: [],
  })
}

// Safe HTML rendering
interface SafeHtmlProps {
  html: string
  className?: string
}

export function SafeHtml({ html, className }: SafeHtmlProps) {
  const sanitizedHtml = useMemo(() => sanitizeHtml(html), [html])
  
  return (
    <div
      className={className}
      dangerouslySetInnerHTML={{ __html: sanitizedHtml }}
    />
  )
}
```

### Environment Variables
```typescript
// Use environment variables for configuration
const config = {
  apiUrl: process.env.NEXT_PUBLIC_API_URL || 'http://localhost:3000',
  elevenlabsApiKey: process.env.ELEVENLABS_API_KEY, // Server-side only
  deepgramApiKey: process.env.DEEPGRAM_API_KEY, // Server-side only
}

// Type-safe environment variables
interface Env {
  NEXT_PUBLIC_API_URL: string
  ELEVENLABS_API_KEY: string
  DEEPGRAM_API_KEY: string
}

declare global {
  namespace NodeJS {
    interface ProcessEnv extends Env {}
  }
}
```

## Testing Guidelines

### Component Testing
```typescript
import { render, screen, fireEvent, waitFor } from '@testing-library/react'
import { vi } from 'vitest'
import ChatInterface from './ChatInterface'

// Mock the useChat hook
vi.mock('@ai-sdk/react', () => ({
  useChat: vi.fn(() => ({
    messages: [],
    input: '',
    handleInputChange: vi.fn(),
    handleSubmit: vi.fn(),
    isLoading: false,
  })),
}))

describe('ChatInterface', () => {
  it('renders chat input and submit button', () => {
    render(<ChatInterface />)
    
    expect(screen.getByPlaceholderText('Type your message...')).toBeInTheDocument()
    expect(screen.getByRole('button', { name: 'Send' })).toBeInTheDocument()
  })

  it('handles message submission', async () => {
    const mockHandleSubmit = vi.fn()
    vi.mocked(useChat).mockReturnValue({
      messages: [],
      input: 'Hello',
      handleInputChange: vi.fn(),
      handleSubmit: mockHandleSubmit,
      isLoading: false,
    })

    render(<ChatInterface />)
    
    const form = screen.getByRole('form')
    fireEvent.submit(form)
    
    await waitFor(() => {
      expect(mockHandleSubmit).toHaveBeenCalled()
    })
  })
})
```

## File Structure
```
app/
├── (dashboard)/
│   ├── assessment/
│   │   ├── page.tsx
│   │   └── components/
│   ├── conversation/
│   │   ├── page.tsx
│   │   └── components/
│   └── layout.tsx
├── api/
│   ├── chat/
│   │   └── route.ts
│   ├── assessment/
│   │   └── route.ts
│   └── tts/
│       └── route.ts
├── components/
│   ├── ui/
│   ├── chat/
│   ├── audio/
│   └── forms/
├── lib/
│   ├── utils.ts
│   ├── validations.ts
│   └── constants.ts
├── hooks/
│   ├── useApi.ts
│   ├── useSpeechRecognition.ts
│   └── useTextToSpeech.ts
└── types/
    ├── api.ts
    ├── assessment.ts
    └── conversation.ts
```

## Critical Rules

1. **Always use Server Components by default** - Only add 'use client' when absolutely necessary for interactivity
2. **Await async APIs** - All Next.js 15 runtime APIs (cookies, headers, params) must be awaited
3. **Use TypeScript everywhere** - No JavaScript files allowed
4. **Follow DaisyUI patterns** - Use DaisyUI classes for consistent styling
5. **Handle loading and error states** - Every async operation must have proper loading and error handling
6. **Optimize for performance** - Use lazy loading, memoization, and code splitting
7. **Sanitize user input** - Always sanitize HTML content and validate forms
8. **Use proper error boundaries** - Wrap components that might throw errors
9. **Follow accessibility guidelines** - Use proper ARIA attributes and semantic HTML
10. **Test components thoroughly** - Write unit tests for all interactive components

## Environment Variables Required
```env
# Public (client-side)
NEXT_PUBLIC_API_URL=http://localhost:3000

# Private (server-side only)
OPENAI_API_KEY=sk-...
GOOGLE_GENERATIVE_AI_API_KEY=...
ELEVENLABS_API_KEY=...
DEEPGRAM_API_KEY=...
NEON_DATABASE_URL=postgresql://...
ZILLIZ_CLOUD_URI=https://...
ZILLIZ_CLOUD_TOKEN=...
```