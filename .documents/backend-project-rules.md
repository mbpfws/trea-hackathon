# Backend Project Rules - AI Language Learning Platform

## Project Overview
You are building the backend for an AI-powered language learning platform that provides personalized, interactive language assessment and conversation practice. The backend orchestrates AI services, manages user data, handles vector operations, and provides real-time streaming capabilities.

## Technology Stack

### Core Framework
- **Next.js 15.1.8 API Routes** with App Router
- **TypeScript** for all code
- **Node.js 18+** runtime

### Database Layer
- **Neon Database (PostgreSQL)** for relational data
  - Use `@neondatabase/serverless` for connections
  - Serverless-optimized for edge functions
- **Zilliz Cloud (Milvus)** for vector operations
  - Use `@zilliz/milvus2-sdk-node` for vector operations
  - Store embeddings for content similarity and personalization

### AI Services Integration
- **OpenAI GPT-4** via `@ai-sdk/openai`
- **Google Gemini** via `@ai-sdk/google`
- **Vercel AI SDK** for streaming and tool orchestration
- **ElevenLabs** for text-to-speech
- **Deepgram** for speech-to-text

### Validation & Security
- **Zod** for schema validation
- **bcryptjs** for password hashing
- **jsonwebtoken** for JWT tokens
- **rate-limiter-flexible** for rate limiting

## Database Architecture

### Neon Database Schema
```sql
-- Users table
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    name VARCHAR(255) NOT NULL,
    native_language VARCHAR(50) NOT NULL,
    target_languages TEXT[] DEFAULT '{}',
    proficiency_levels JSONB DEFAULT '{}',
    learning_preferences JSONB DEFAULT '{}',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Learning sessions
CREATE TABLE learning_sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    session_type VARCHAR(50) NOT NULL, -- 'assessment', 'conversation', 'practice'
    language VARCHAR(50) NOT NULL,
    difficulty_level VARCHAR(20) NOT NULL,
    topics TEXT[] DEFAULT '{}',
    status VARCHAR(20) DEFAULT 'active', -- 'active', 'completed', 'paused'
    metadata JSONB DEFAULT '{}',
    started_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    completed_at TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Assessment results
CREATE TABLE assessments (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    session_id UUID REFERENCES learning_sessions(id) ON DELETE CASCADE,
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    language VARCHAR(50) NOT NULL,
    assessment_type VARCHAR(50) NOT NULL, -- 'placement', 'progress', 'final'
    questions JSONB NOT NULL,
    responses JSONB NOT NULL,
    scores JSONB NOT NULL,
    feedback JSONB NOT NULL,
    proficiency_level VARCHAR(20) NOT NULL,
    strengths TEXT[] DEFAULT '{}',
    weaknesses TEXT[] DEFAULT '{}',
    recommendations JSONB DEFAULT '{}',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Conversation messages
CREATE TABLE conversation_messages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    session_id UUID REFERENCES learning_sessions(id) ON DELETE CASCADE,
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    role VARCHAR(20) NOT NULL, -- 'user', 'assistant', 'system'
    content TEXT NOT NULL,
    audio_url VARCHAR(500),
    metadata JSONB DEFAULT '{}',
    feedback JSONB DEFAULT '{}',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Learning content
CREATE TABLE learning_content (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    title VARCHAR(255) NOT NULL,
    content_type VARCHAR(50) NOT NULL, -- 'lesson', 'exercise', 'conversation_starter'
    language VARCHAR(50) NOT NULL,
    difficulty_level VARCHAR(20) NOT NULL,
    topics TEXT[] DEFAULT '{}',
    content JSONB NOT NULL,
    metadata JSONB DEFAULT '{}',
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- User progress tracking
CREATE TABLE user_progress (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    language VARCHAR(50) NOT NULL,
    skill_area VARCHAR(50) NOT NULL, -- 'speaking', 'listening', 'reading', 'writing', 'grammar', 'vocabulary'
    current_level VARCHAR(20) NOT NULL,
    progress_score DECIMAL(5,2) DEFAULT 0.00,
    total_practice_time INTEGER DEFAULT 0, -- in minutes
    streak_days INTEGER DEFAULT 0,
    last_practice_date DATE,
    milestones JSONB DEFAULT '{}',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    UNIQUE(user_id, language, skill_area)
);

-- Indexes for performance
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_learning_sessions_user_id ON learning_sessions(user_id);
CREATE INDEX idx_learning_sessions_status ON learning_sessions(status);
CREATE INDEX idx_assessments_user_id ON assessments(user_id);
CREATE INDEX idx_assessments_session_id ON assessments(session_id);
CREATE INDEX idx_conversation_messages_session_id ON conversation_messages(session_id);
CREATE INDEX idx_learning_content_language_level ON learning_content(language, difficulty_level);
CREATE INDEX idx_user_progress_user_language ON user_progress(user_id, language);
```

### Zilliz Cloud Collections
```typescript
// Collection schemas for vector operations
interface ContentEmbeddingSchema {
  id: string
  content_id: string
  content_type: 'lesson' | 'exercise' | 'conversation' | 'assessment_question'
  language: string
  difficulty_level: string
  topics: string[]
  text_content: string
  embedding: number[] // 1536 dimensions for OpenAI embeddings
  metadata: Record<string, any>
  created_at: number
}

interface UserInteractionSchema {
  id: string
  user_id: string
  session_id: string
  interaction_type: 'response' | 'question' | 'feedback'
  language: string
  content: string
  embedding: number[] // 1536 dimensions
  performance_score: number
  timestamp: number
}

interface LearningPathSchema {
  id: string
  user_id: string
  language: string
  skill_area: string
  proficiency_level: string
  learning_objectives: string[]
  content_preferences: string
  embedding: number[] // 1536 dimensions
  success_rate: number
  last_updated: number
}
```

## Database Connection Patterns

### Neon Database Connection
```typescript
// lib/database/neon.ts
import { neon } from '@neondatabase/serverless'

if (!process.env.NEON_DATABASE_URL) {
  throw new Error('NEON_DATABASE_URL environment variable is required')
}

export const sql = neon(process.env.NEON_DATABASE_URL)

// Type-safe query helper
export async function query<T = any>(
  text: string,
  params?: any[]
): Promise<T[]> {
  try {
    const result = await sql(text, params)
    return result as T[]
  } catch (error) {
    console.error('Database query error:', error)
    throw new Error('Database operation failed')
  }
}

// Transaction helper
export async function transaction<T>(
  queries: Array<{ text: string; params?: any[] }>
): Promise<T[]> {
  try {
    const results = await sql.transaction(queries.map(q => sql(q.text, q.params)))
    return results as T[]
  } catch (error) {
    console.error('Transaction error:', error)
    throw new Error('Transaction failed')
  }
}
```

### Zilliz Cloud Connection
```typescript
// lib/database/zilliz.ts
import { MilvusClient, DataType } from '@zilliz/milvus2-sdk-node'

if (!process.env.ZILLIZ_CLOUD_URI || !process.env.ZILLIZ_CLOUD_TOKEN) {
  throw new Error('Zilliz Cloud credentials are required')
}

class ZillizClient {
  private client: MilvusClient
  private static instance: ZillizClient

  private constructor() {
    this.client = new MilvusClient({
      address: process.env.ZILLIZ_CLOUD_URI!,
      token: process.env.ZILLIZ_CLOUD_TOKEN!,
    })
  }

  static getInstance(): ZillizClient {
    if (!ZillizClient.instance) {
      ZillizClient.instance = new ZillizClient()
    }
    return ZillizClient.instance
  }

  async createCollection(collectionName: string, schema: any) {
    try {
      const hasCollection = await this.client.hasCollection({
        collection_name: collectionName,
      })

      if (!hasCollection.value) {
        await this.client.createCollection({
          collection_name: collectionName,
          fields: schema,
        })

        // Create index for vector field
        await this.client.createIndex({
          collection_name: collectionName,
          field_name: 'embedding',
          index_type: 'IVF_FLAT',
          metric_type: 'COSINE',
          params: { nlist: 1024 },
        })

        await this.client.loadCollection({
          collection_name: collectionName,
        })
      }
    } catch (error) {
      console.error('Error creating collection:', error)
      throw error
    }
  }

  async insertVectors(collectionName: string, data: any[]) {
    try {
      const result = await this.client.insert({
        collection_name: collectionName,
        data,
      })
      return result
    } catch (error) {
      console.error('Error inserting vectors:', error)
      throw error
    }
  }

  async searchSimilar(
    collectionName: string,
    vector: number[],
    limit: number = 10,
    filter?: string
  ) {
    try {
      const result = await this.client.search({
        collection_name: collectionName,
        vectors: [vector],
        search_params: {
          anns_field: 'embedding',
          topk: limit,
          metric_type: 'COSINE',
          params: { nprobe: 10 },
        },
        output_fields: ['*'],
        filter: filter,
      })
      return result.results
    } catch (error) {
      console.error('Error searching vectors:', error)
      throw error
    }
  }
}

export const zillizClient = ZillizClient.getInstance()
```

## API Route Patterns

### Chat API with Streaming
```typescript
// app/api/chat/route.ts
import { streamText, tool } from 'ai'
import { openai } from '@ai-sdk/openai'
import { z } from 'zod'
import { query } from '@/lib/database/neon'
import { zillizClient } from '@/lib/database/zilliz'
import { generateEmbedding } from '@/lib/ai/embeddings'

export const maxDuration = 30

const assessmentTool = tool({
  description: 'Create a language assessment question based on user level and topic',
  parameters: z.object({
    language: z.string().describe('Target language'),
    level: z.enum(['beginner', 'intermediate', 'advanced']),
    topic: z.string().describe('Topic area to focus on'),
    questionType: z.enum(['multiple_choice', 'fill_blank', 'conversation', 'translation']),
  }),
  execute: async ({ language, level, topic, questionType }) => {
    try {
      // Generate embedding for the topic
      const topicEmbedding = await generateEmbedding(`${language} ${level} ${topic} ${questionType}`)
      
      // Find similar content from vector database
      const similarContent = await zillizClient.searchSimilar(
        'content_embeddings',
        topicEmbedding,
        5,
        `language == "${language}" && difficulty_level == "${level}"`
      )

      // Generate contextual assessment question
      const context = similarContent.map(item => item.text_content).join('\n')
      
      return {
        success: true,
        context,
        similarContentCount: similarContent.length,
      }
    } catch (error) {
      console.error('Assessment tool error:', error)
      return {
        success: false,
        error: 'Failed to generate assessment question',
      }
    }
  },
})

const feedbackTool = tool({
  description: 'Provide detailed feedback on user language response',
  parameters: z.object({
    userResponse: z.string().describe('User\'s response to analyze'),
    expectedLevel: z.string().describe('Expected proficiency level'),
    language: z.string().describe('Target language'),
    skillArea: z.enum(['grammar', 'vocabulary', 'pronunciation', 'fluency', 'comprehension']),
  }),
  execute: async ({ userResponse, expectedLevel, language, skillArea }) => {
    try {
      // Store user interaction in vector database for future personalization
      const responseEmbedding = await generateEmbedding(userResponse)
      
      await zillizClient.insertVectors('user_interactions', [{
        id: `interaction_${Date.now()}`,
        user_id: 'current_user', // Get from session
        session_id: 'current_session', // Get from context
        interaction_type: 'response',
        language,
        content: userResponse,
        embedding: responseEmbedding,
        performance_score: 0, // Will be calculated
        timestamp: Date.now(),
      }])

      return {
        success: true,
        analyzed: true,
        skillArea,
      }
    } catch (error) {
      console.error('Feedback tool error:', error)
      return {
        success: false,
        error: 'Failed to analyze response',
      }
    }
  },
})

export async function POST(request: Request) {
  try {
    const { messages, sessionId, userId } = await request.json()

    // Validate session
    const session = await query(
      'SELECT * FROM learning_sessions WHERE id = $1 AND user_id = $2',
      [sessionId, userId]
    )

    if (!session.length) {
      return new Response('Session not found', { status: 404 })
    }

    const result = await streamText({
      model: openai('gpt-4o'),
      messages,
      tools: {
        createAssessment: assessmentTool,
        provideFeedback: feedbackTool,
      },
      maxSteps: 5,
      system: `You are an expert language learning tutor specializing in ${session[0].language}. 
               Provide personalized, encouraging feedback and create appropriate assessment questions 
               based on the user's proficiency level: ${session[0].difficulty_level}.
               
               Focus on these topics: ${session[0].topics?.join(', ') || 'general conversation'}.
               
               Always be supportive and provide constructive feedback that helps the user improve.`,
    })

    return result.toDataStreamResponse()
  } catch (error) {
    console.error('Chat API error:', error)
    return new Response('Internal server error', { status: 500 })
  }
}
```

### Assessment API
```typescript
// app/api/assessment/create/route.ts
import { NextRequest } from 'next/server'
import { z } from 'zod'
import { query } from '@/lib/database/neon'
import { generateEmbedding } from '@/lib/ai/embeddings'
import { zillizClient } from '@/lib/database/zilliz'

const createAssessmentSchema = z.object({
  userId: z.string().uuid(),
  language: z.string().min(2),
  assessmentType: z.enum(['placement', 'progress', 'final']),
  topics: z.array(z.string()).optional(),
  difficultyLevel: z.enum(['beginner', 'intermediate', 'advanced']).optional(),
})

export async function POST(request: NextRequest) {
  try {
    const body = await request.json()
    const validatedData = createAssessmentSchema.parse(body)

    // Create learning session
    const sessionResult = await query(
      `INSERT INTO learning_sessions (user_id, session_type, language, difficulty_level, topics, status)
       VALUES ($1, $2, $3, $4, $5, $6)
       RETURNING id`,
      [
        validatedData.userId,
        'assessment',
        validatedData.language,
        validatedData.difficultyLevel || 'intermediate',
        validatedData.topics || [],
        'active'
      ]
    )

    const sessionId = sessionResult[0].id

    // Generate personalized assessment questions based on user history
    const userHistory = await query(
      `SELECT a.*, up.current_level, up.progress_score 
       FROM assessments a 
       JOIN user_progress up ON a.user_id = up.user_id AND a.language = up.language
       WHERE a.user_id = $1 AND a.language = $2 
       ORDER BY a.created_at DESC 
       LIMIT 5`,
      [validatedData.userId, validatedData.language]
    )

    // Find similar assessment content from vector database
    const queryEmbedding = await generateEmbedding(
      `${validatedData.language} ${validatedData.assessmentType} ${validatedData.topics?.join(' ') || ''}`
    )

    const similarContent = await zillizClient.searchSimilar(
      'content_embeddings',
      queryEmbedding,
      10,
      `language == "${validatedData.language}" && content_type == "assessment_question"`
    )

    return Response.json({
      success: true,
      sessionId,
      userHistory: userHistory.length,
      recommendedContent: similarContent.length,
      message: 'Assessment session created successfully'
    })

  } catch (error) {
    if (error instanceof z.ZodError) {
      return Response.json(
        { success: false, error: 'Invalid input data', details: error.errors },
        { status: 400 }
      )
    }

    console.error('Create assessment error:', error)
    return Response.json(
      { success: false, error: 'Failed to create assessment' },
      { status: 500 }
    )
  }
}
```

### Text-to-Speech API
```typescript
// app/api/tts/route.ts
import { NextRequest } from 'next/server'
import { z } from 'zod'

const ttsSchema = z.object({
  text: z.string().min(1).max(5000),
  voice: z.string().optional().default('en-US'),
  speed: z.number().min(0.5).max(2).optional().default(1),
  provider: z.enum(['elevenlabs', 'browser']).optional().default('elevenlabs'),
})

export async function POST(request: NextRequest) {
  try {
    const body = await request.json()
    const { text, voice, speed, provider } = ttsSchema.parse(body)

    if (provider === 'elevenlabs') {
      if (!process.env.ELEVENLABS_API_KEY) {
        throw new Error('ElevenLabs API key not configured')
      }

      const response = await fetch('https://api.elevenlabs.io/v1/text-to-speech/21m00Tcm4TlvDq8ikWAM', {
        method: 'POST',
        headers: {
          'Accept': 'audio/mpeg',
          'Content-Type': 'application/json',
          'xi-api-key': process.env.ELEVENLABS_API_KEY,
        },
        body: JSON.stringify({
          text,
          model_id: 'eleven_monolingual_v1',
          voice_settings: {
            stability: 0.5,
            similarity_boost: 0.5,
            style: 0.5,
            use_speaker_boost: true,
          },
        }),
      })

      if (!response.ok) {
        throw new Error(`ElevenLabs API error: ${response.statusText}`)
      }

      const audioBuffer = await response.arrayBuffer()
      
      return new Response(audioBuffer, {
        headers: {
          'Content-Type': 'audio/mpeg',
          'Content-Length': audioBuffer.byteLength.toString(),
        },
      })
    }

    // Fallback for browser TTS (return instructions)
    return Response.json({
      success: true,
      provider: 'browser',
      instructions: 'Use browser Speech Synthesis API',
      text,
      voice,
      speed,
    })

  } catch (error) {
    if (error instanceof z.ZodError) {
      return Response.json(
        { success: false, error: 'Invalid input data', details: error.errors },
        { status: 400 }
      )
    }

    console.error('TTS API error:', error)
    return Response.json(
      { success: false, error: 'Text-to-speech generation failed' },
      { status: 500 }
    )
  }
}
```

### Speech-to-Text API
```typescript
// app/api/stt/route.ts
import { NextRequest } from 'next/server'
import { z } from 'zod'

const sttSchema = z.object({
  audioUrl: z.string().url().optional(),
  language: z.string().optional().default('en-US'),
  model: z.enum(['nova-2', 'enhanced', 'base']).optional().default('nova-2'),
})

export async function POST(request: NextRequest) {
  try {
    const formData = await request.formData()
    const audioFile = formData.get('audio') as File
    const language = formData.get('language') as string || 'en-US'
    const model = formData.get('model') as string || 'nova-2'

    if (!audioFile) {
      return Response.json(
        { success: false, error: 'Audio file is required' },
        { status: 400 }
      )
    }

    if (!process.env.DEEPGRAM_API_KEY) {
      throw new Error('Deepgram API key not configured')
    }

    const audioBuffer = await audioFile.arrayBuffer()

    const response = await fetch('https://api.deepgram.com/v1/listen', {
      method: 'POST',
      headers: {
        'Authorization': `Token ${process.env.DEEPGRAM_API_KEY}`,
        'Content-Type': audioFile.type,
      },
      body: audioBuffer,
    })

    if (!response.ok) {
      throw new Error(`Deepgram API error: ${response.statusText}`)
    }

    const result = await response.json()
    const transcript = result.results?.channels?.[0]?.alternatives?.[0]?.transcript || ''
    const confidence = result.results?.channels?.[0]?.alternatives?.[0]?.confidence || 0

    return Response.json({
      success: true,
      transcript,
      confidence,
      language,
      model,
      duration: result.metadata?.duration || 0,
    })

  } catch (error) {
    console.error('STT API error:', error)
    return Response.json(
      { success: false, error: 'Speech-to-text conversion failed' },
      { status: 500 }
    )
  }
}
```

## AI Service Integration

### Embedding Generation
```typescript
// lib/ai/embeddings.ts
import { openai } from '@ai-sdk/openai'
import { embed } from 'ai'

export async function generateEmbedding(text: string): Promise<number[]> {
  try {
    const { embedding } = await embed({
      model: openai.embedding('text-embedding-3-small'),
      value: text,
    })
    
    return embedding
  } catch (error) {
    console.error('Embedding generation error:', error)
    throw new Error('Failed to generate embedding')
  }
}

export async function generateBatchEmbeddings(texts: string[]): Promise<number[][]> {
  try {
    const embeddings = await Promise.all(
      texts.map(text => generateEmbedding(text))
    )
    
    return embeddings
  } catch (error) {
    console.error('Batch embedding generation error:', error)
    throw new Error('Failed to generate batch embeddings')
  }
}
```

### Content Personalization
```typescript
// lib/ai/personalization.ts
import { zillizClient } from '@/lib/database/zilliz'
import { generateEmbedding } from './embeddings'
import { query } from '@/lib/database/neon'

interface PersonalizationContext {
  userId: string
  language: string
  currentLevel: string
  learningGoals: string[]
  weakAreas: string[]
  preferredTopics: string[]
}

export async function getPersonalizedContent(
  context: PersonalizationContext,
  contentType: 'lesson' | 'exercise' | 'conversation',
  limit: number = 10
) {
  try {
    // Get user's learning history and preferences
    const userProgress = await query(
      `SELECT * FROM user_progress 
       WHERE user_id = $1 AND language = $2`,
      [context.userId, context.language]
    )

    // Create personalization query embedding
    const queryText = [
      context.language,
      context.currentLevel,
      ...context.learningGoals,
      ...context.preferredTopics,
      contentType
    ].join(' ')

    const queryEmbedding = await generateEmbedding(queryText)

    // Find similar content based on user preferences
    const similarContent = await zillizClient.searchSimilar(
      'content_embeddings',
      queryEmbedding,
      limit * 2, // Get more to filter
      `language == "${context.language}" && content_type == "${contentType}"`
    )

    // Filter content based on user's weak areas (prioritize improvement)
    const prioritizedContent = similarContent.filter(content => {
      const contentTopics = content.topics || []
      return context.weakAreas.some(weakArea => 
        contentTopics.includes(weakArea)
      )
    }).slice(0, limit)

    // Fill remaining slots with general relevant content
    const remainingSlots = limit - prioritizedContent.length
    if (remainingSlots > 0) {
      const generalContent = similarContent
        .filter(content => !prioritizedContent.includes(content))
        .slice(0, remainingSlots)
      
      prioritizedContent.push(...generalContent)
    }

    return prioritizedContent
  } catch (error) {
    console.error('Personalization error:', error)
    throw new Error('Failed to get personalized content')
  }
}

export async function updateUserLearningProfile(
  userId: string,
  language: string,
  interactionData: {
    skillArea: string
    performance: number
    timeSpent: number
    content: string
  }
) {
  try {
    // Generate embedding for the interaction
    const interactionEmbedding = await generateEmbedding(interactionData.content)

    // Store interaction in vector database
    await zillizClient.insertVectors('user_interactions', [{
      id: `interaction_${userId}_${Date.now()}`,
      user_id: userId,
      session_id: 'current_session',
      interaction_type: 'learning',
      language,
      content: interactionData.content,
      embedding: interactionEmbedding,
      performance_score: interactionData.performance,
      timestamp: Date.now(),
    }])

    // Update user progress in relational database
    await query(
      `INSERT INTO user_progress (user_id, language, skill_area, current_level, progress_score, total_practice_time, last_practice_date)
       VALUES ($1, $2, $3, $4, $5, $6, CURRENT_DATE)
       ON CONFLICT (user_id, language, skill_area)
       DO UPDATE SET 
         progress_score = GREATEST(user_progress.progress_score, EXCLUDED.progress_score),
         total_practice_time = user_progress.total_practice_time + EXCLUDED.total_practice_time,
         last_practice_date = CURRENT_DATE,
         updated_at = NOW()`,
      [
        userId,
        language,
        interactionData.skillArea,
        'intermediate', // This should be calculated based on performance
        interactionData.performance,
        interactionData.timeSpent
      ]
    )

    return { success: true }
  } catch (error) {
    console.error('Learning profile update error:', error)
    throw new Error('Failed to update learning profile')
  }
}
```

## Security & Validation

### Rate Limiting
```typescript
// lib/security/rate-limit.ts
import { RateLimiterMemory } from 'rate-limiter-flexible'

const rateLimiters = {
  chat: new RateLimiterMemory({
    keyPrefix: 'chat_limit',
    points: 50, // Number of requests
    duration: 60, // Per 60 seconds
  }),
  
  assessment: new RateLimiterMemory({
    keyPrefix: 'assessment_limit',
    points: 10, // Number of assessments
    duration: 3600, // Per hour
  }),
  
  tts: new RateLimiterMemory({
    keyPrefix: 'tts_limit',
    points: 100, // Number of TTS requests
    duration: 3600, // Per hour
  }),
}

export async function checkRateLimit(
  type: keyof typeof rateLimiters,
  identifier: string
): Promise<{ allowed: boolean; remainingPoints?: number; msBeforeNext?: number }> {
  try {
    const limiter = rateLimiters[type]
    const result = await limiter.consume(identifier)
    
    return {
      allowed: true,
      remainingPoints: result.remainingPoints,
      msBeforeNext: result.msBeforeNext,
    }
  } catch (rejRes: any) {
    return {
      allowed: false,
      remainingPoints: rejRes.remainingPoints,
      msBeforeNext: rejRes.msBeforeNext,
    }
  }
}

// Middleware for API routes
export function withRateLimit(type: keyof typeof rateLimiters) {
  return async (request: Request, identifier: string) => {
    const rateLimit = await checkRateLimit(type, identifier)
    
    if (!rateLimit.allowed) {
      return new Response(
        JSON.stringify({
          error: 'Rate limit exceeded',
          retryAfter: Math.round(rateLimit.msBeforeNext! / 1000),
        }),
        {
          status: 429,
          headers: {
            'Content-Type': 'application/json',
            'Retry-After': Math.round(rateLimit.msBeforeNext! / 1000).toString(),
          },
        }
      )
    }
    
    return null // Continue processing
  }
}
```

### Input Validation
```typescript
// lib/validation/schemas.ts
import { z } from 'zod'

export const userSchema = z.object({
  email: z.string().email('Invalid email format'),
  password: z.string().min(8, 'Password must be at least 8 characters'),
  name: z.string().min(2, 'Name must be at least 2 characters'),
  nativeLanguage: z.string().min(2, 'Native language is required'),
  targetLanguages: z.array(z.string()).optional().default([]),
})

export const sessionSchema = z.object({
  userId: z.string().uuid('Invalid user ID'),
  sessionType: z.enum(['assessment', 'conversation', 'practice']),
  language: z.string().min(2, 'Language is required'),
  difficultyLevel: z.enum(['beginner', 'intermediate', 'advanced']),
  topics: z.array(z.string()).optional().default([]),
})

export const messageSchema = z.object({
  sessionId: z.string().uuid('Invalid session ID'),
  userId: z.string().uuid('Invalid user ID'),
  role: z.enum(['user', 'assistant', 'system']),
  content: z.string().min(1, 'Message content is required').max(5000, 'Message too long'),
  audioUrl: z.string().url().optional(),
})

export const assessmentSchema = z.object({
  sessionId: z.string().uuid('Invalid session ID'),
  userId: z.string().uuid('Invalid user ID'),
  language: z.string().min(2, 'Language is required'),
  assessmentType: z.enum(['placement', 'progress', 'final']),
  questions: z.array(z.object({
    id: z.string(),
    type: z.enum(['multiple_choice', 'fill_blank', 'conversation', 'translation']),
    question: z.string(),
    options: z.array(z.string()).optional(),
    correctAnswer: z.string(),
    difficulty: z.enum(['beginner', 'intermediate', 'advanced']),
  })),
  responses: z.array(z.object({
    questionId: z.string(),
    userAnswer: z.string(),
    timeSpent: z.number().positive(),
    confidence: z.number().min(0).max(1).optional(),
  })),
})

// Validation middleware
export function validateRequest<T>(schema: z.ZodSchema<T>) {
  return async (request: Request): Promise<{ data: T; error?: never } | { data?: never; error: Response }> => {
    try {
      const body = await request.json()
      const data = schema.parse(body)
      return { data }
    } catch (error) {
      if (error instanceof z.ZodError) {
        return {
          error: new Response(
            JSON.stringify({
              success: false,
              error: 'Validation failed',
              details: error.errors,
            }),
            {
              status: 400,
              headers: { 'Content-Type': 'application/json' },
            }
          ),
        }
      }
      
      return {
        error: new Response(
          JSON.stringify({
            success: false,
            error: 'Invalid request format',
          }),
          {
            status: 400,
            headers: { 'Content-Type': 'application/json' },
          }
        ),
      }
    }
  }
}
```

## Error Handling & Logging

### Centralized Error Handler
```typescript
// lib/errors/handler.ts
export class AppError extends Error {
  public readonly statusCode: number
  public readonly isOperational: boolean

  constructor(message: string, statusCode: number = 500, isOperational: boolean = true) {
    super(message)
    this.statusCode = statusCode
    this.isOperational = isOperational

    Error.captureStackTrace(this, this.constructor)
  }
}

export class ValidationError extends AppError {
  constructor(message: string) {
    super(message, 400)
  }
}

export class NotFoundError extends AppError {
  constructor(resource: string) {
    super(`${resource} not found`, 404)
  }
}

export class UnauthorizedError extends AppError {
  constructor(message: string = 'Unauthorized') {
    super(message, 401)
  }
}

export class RateLimitError extends AppError {
  constructor(retryAfter: number) {
    super('Rate limit exceeded', 429)
  }
}

export function handleApiError(error: unknown): Response {
  console.error('API Error:', error)

  if (error instanceof AppError) {
    return new Response(
      JSON.stringify({
        success: false,
        error: error.message,
        statusCode: error.statusCode,
      }),
      {
        status: error.statusCode,
        headers: { 'Content-Type': 'application/json' },
      }
    )
  }

  // Unknown error
  return new Response(
    JSON.stringify({
      success: false,
      error: 'Internal server error',
      statusCode: 500,
    }),
    {
      status: 500,
      headers: { 'Content-Type': 'application/json' },
    }
  )
}
```

### Structured Logging
```typescript
// lib/logging/logger.ts
interface LogContext {
  userId?: string
  sessionId?: string
  requestId?: string
  endpoint?: string
  method?: string
  [key: string]: any
}

class Logger {
  private context: LogContext = {}

  setContext(context: LogContext) {
    this.context = { ...this.context, ...context }
  }

  private log(level: 'info' | 'warn' | 'error', message: string, data?: any) {
    const logEntry = {
      timestamp: new Date().toISOString(),
      level,
      message,
      context: this.context,
      data,
    }

    console.log(JSON.stringify(logEntry))
  }

  info(message: string, data?: any) {
    this.log('info', message, data)
  }

  warn(message: string, data?: any) {
    this.log('warn', message, data)
  }

  error(message: string, error?: any) {
    this.log('error', message, {
      error: error instanceof Error ? {
        name: error.name,
        message: error.message,
        stack: error.stack,
      } : error,
    })
  }
}

export const logger = new Logger()
```

## Performance Optimization

### Database Query Optimization
```typescript
// lib/database/optimized-queries.ts
import { query } from './neon'

// Optimized user progress query with aggregations
export async function getUserProgressSummary(userId: string, language: string) {
  return query(`
    SELECT 
      up.language,
      up.skill_area,
      up.current_level,
      up.progress_score,
      up.total_practice_time,
      up.streak_days,
      up.last_practice_date,
      COUNT(ls.id) as total_sessions,
      AVG(CASE WHEN a.scores IS NOT NULL THEN (a.scores->>'overall')::numeric END) as avg_assessment_score
    FROM user_progress up
    LEFT JOIN learning_sessions ls ON up.user_id = ls.user_id AND up.language = ls.language
    LEFT JOIN assessments a ON ls.id = a.session_id
    WHERE up.user_id = $1 AND up.language = $2
    GROUP BY up.language, up.skill_area, up.current_level, up.progress_score, 
             up.total_practice_time, up.streak_days, up.last_practice_date
    ORDER BY up.skill_area
  `, [userId, language])
}

// Batch insert for conversation messages
export async function insertConversationMessages(messages: any[]) {
  const values = messages.map((msg, index) => 
    `($${index * 6 + 1}, $${index * 6 + 2}, $${index * 6 + 3}, $${index * 6 + 4}, $${index * 6 + 5}, $${index * 6 + 6})`
  ).join(', ')
  
  const params = messages.flatMap(msg => [
    msg.sessionId,
    msg.userId,
    msg.role,
    msg.content,
    msg.audioUrl,
    JSON.stringify(msg.metadata || {})
  ])

  return query(`
    INSERT INTO conversation_messages (session_id, user_id, role, content, audio_url, metadata)
    VALUES ${values}
    RETURNING id
  `, params)
}
```

### Caching Strategy
```typescript
// lib/cache/redis.ts (if using Redis, or in-memory for simplicity)
class SimpleCache {
  private cache = new Map<string, { data: any; expiry: number }>()

  set(key: string, data: any, ttlSeconds: number = 300) {
    const expiry = Date.now() + (ttlSeconds * 1000)
    this.cache.set(key, { data, expiry })
  }

  get(key: string): any | null {
    const item = this.cache.get(key)
    if (!item) return null
    
    if (Date.now() > item.expiry) {
      this.cache.delete(key)
      return null
    }
    
    return item.data
  }

  delete(key: string) {
    this.cache.delete(key)
  }

  clear() {
    this.cache.clear()
  }
}

export const cache = new SimpleCache()

// Cache wrapper for expensive operations
export async function withCache<T>(
  key: string,
  fn: () => Promise<T>,
  ttlSeconds: number = 300
): Promise<T> {
  const cached = cache.get(key)
  if (cached) return cached
  
  const result = await fn()
  cache.set(key, result, ttlSeconds)
  return result
}
```

## Environment Variables
```env
# Database
NEON_DATABASE_URL=postgresql://username:password@host/database
ZILLIZ_CLOUD_URI=https://your-cluster.zillizcloud.com
ZILLIZ_CLOUD_TOKEN=your-token

# AI Services
OPENAI_API_KEY=sk-...
GOOGLE_GENERATIVE_AI_API_KEY=...
ELEVENLABS_API_KEY=...
DEEPGRAM_API_KEY=...

# Security
JWT_SECRET=your-jwt-secret
BCRYPT_ROUNDS=12

# Application
NEXT_PUBLIC_API_URL=http://localhost:3000
NODE_ENV=development
```

## Critical Backend Rules

1. **Always validate input** - Use Zod schemas for all API endpoints
2. **Implement rate limiting** - Protect against abuse and excessive usage
3. **Use transactions** - For operations that modify multiple tables
4. **Handle errors gracefully** - Return appropriate HTTP status codes and messages
5. **Log everything** - Structured logging for debugging and monitoring
6. **Optimize database queries** - Use indexes, avoid N+1 queries, batch operations
7. **Cache expensive operations** - Vector searches, AI model calls, complex queries
8. **Secure API endpoints** - Validate authentication and authorization
9. **Use environment variables** - Never hardcode secrets or configuration
10. **Monitor performance** - Track response times, error rates, and resource usage

## File Structure
```
app/
├── api/
│   ├── chat/
│   │   └── route.ts
│   ├── assessment/
│   │   ├── create/
│   │   │   └── route.ts
│   │   └── submit/
│   │       └── route.ts
│   ├── tts/
│   │   └── route.ts
│   ├── stt/
│   │   └── route.ts
│   └── user/
│       ├── progress/
│       │   └── route.ts
│       └── profile/
│           └── route.ts
lib/
├── database/
│   ├── neon.ts
│   ├── zilliz.ts
│   └── optimized-queries.ts
├── ai/
│   ├── embeddings.ts
│   ├── personalization.ts
│   └── assessment.ts
├── security/
│   ├── rate-limit.ts
│   ├── auth.ts
│   └── validation.ts
├── errors/
│   └── handler.ts
├── logging/
│   └── logger.ts
└── cache/
    └── simple-cache.ts
```