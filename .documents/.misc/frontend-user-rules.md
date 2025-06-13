# Frontend User Rules - AI Language Learning Platform

## User Experience Philosophy
You are building an AI-powered language learning platform that prioritizes **intuitive interaction**, **immediate feedback**, and **adaptive personalization**. Every interface element should feel natural, encouraging, and supportive of the user's learning journey.

## Core User Principles

### 1. Learning-First Design
- **Minimize cognitive load** - Users should focus on learning, not navigating complex interfaces
- **Progressive disclosure** - Show information when needed, hide complexity until relevant
- **Clear learning paths** - Users should always know where they are and what comes next
- **Immediate feedback** - Provide instant responses to user actions and learning attempts

### 2. Accessibility & Inclusivity
- **Multi-language support** - Interface adapts to user's native language preferences
- **Audio-first interactions** - Support users with different learning styles and abilities
- **Keyboard navigation** - All interactions must be accessible via keyboard
- **Screen reader compatibility** - Proper ARIA labels and semantic HTML
- **Color contrast compliance** - Meet WCAG 2.1 AA standards

### 3. Responsive & Mobile-First
- **Touch-friendly interfaces** - Minimum 44px touch targets
- **Thumb-zone optimization** - Important actions within easy thumb reach
- **Offline capability** - Core features work without internet connection
- **Performance optimization** - Fast loading on slower devices and networks

## User Interface Guidelines

### Visual Hierarchy
```typescript
// Typography scale (Tailwind classes)
const typography = {
  // Headers
  h1: 'text-4xl md:text-6xl font-bold text-base-content',
  h2: 'text-3xl md:text-4xl font-semibold text-base-content',
  h3: 'text-2xl md:text-3xl font-medium text-base-content',
  h4: 'text-xl md:text-2xl font-medium text-base-content',
  
  // Body text
  body: 'text-base text-base-content leading-relaxed',
  bodyLarge: 'text-lg text-base-content leading-relaxed',
  bodySmall: 'text-sm text-base-content/80',
  
  // Interactive elements
  button: 'text-base font-medium',
  link: 'text-primary hover:text-primary-focus underline',
  
  // Learning content
  question: 'text-xl md:text-2xl font-medium text-base-content leading-relaxed',
  answer: 'text-lg text-base-content leading-relaxed',
  feedback: 'text-base text-base-content/90 leading-relaxed',
}

// Color system (DaisyUI semantic colors)
const colors = {
  // Feedback states
  success: 'text-success bg-success/10 border-success/20',
  warning: 'text-warning bg-warning/10 border-warning/20',
  error: 'text-error bg-error/10 border-error/20',
  info: 'text-info bg-info/10 border-info/20',
  
  // Learning states
  correct: 'text-success bg-success/10',
  incorrect: 'text-error bg-error/10',
  partial: 'text-warning bg-warning/10',
  neutral: 'text-base-content/70 bg-base-200',
}
```

### Spacing & Layout
```typescript
// Consistent spacing system
const spacing = {
  // Component spacing
  xs: 'p-2 gap-2',      // 8px
  sm: 'p-4 gap-4',      // 16px
  md: 'p-6 gap-6',      // 24px
  lg: 'p-8 gap-8',      // 32px
  xl: 'p-12 gap-12',    // 48px
  
  // Section spacing
  sectionY: 'py-12 md:py-16 lg:py-20',
  sectionX: 'px-4 md:px-8 lg:px-12',
  
  // Content containers
  container: 'max-w-7xl mx-auto px-4 sm:px-6 lg:px-8',
  contentWidth: 'max-w-4xl mx-auto',
  narrowContent: 'max-w-2xl mx-auto',
}
```

## Interaction Patterns

### Chat Interface Standards
```typescript
// Message display patterns
const messageStyles = {
  user: {
    container: 'chat chat-end',
    bubble: 'chat-bubble chat-bubble-primary',
    avatar: 'chat-image avatar w-10 rounded-full',
  },
  assistant: {
    container: 'chat chat-start',
    bubble: 'chat-bubble chat-bubble-secondary',
    avatar: 'chat-image avatar w-10 rounded-full',
  },
  system: {
    container: 'chat chat-start',
    bubble: 'chat-bubble chat-bubble-accent text-sm',
    avatar: 'hidden',
  },
}

// Interactive message components
interface MessageProps {
  message: {
    id: string
    role: 'user' | 'assistant' | 'system'
    content: string
    timestamp: Date
    audioUrl?: string
    feedback?: {
      type: 'correct' | 'incorrect' | 'partial'
      explanation?: string
      suggestions?: string[]
    }
  }
  onPlayAudio?: (audioUrl: string) => void
  onRequestFeedback?: (messageId: string) => void
  onRetry?: (messageId: string) => void
}

// Message interaction requirements:
// 1. Audio playback button for all messages with audio
// 2. Feedback request button for user messages
// 3. Retry button for failed messages
// 4. Copy text functionality
// 5. Timestamp display on hover/tap
```

### Form Interaction Standards
```typescript
// Form validation feedback
const formFeedback = {
  // Real-time validation (as user types)
  realTime: {
    valid: 'border-success text-success',
    invalid: 'border-error text-error',
    neutral: 'border-base-300 text-base-content',
  },
  
  // Submit validation
  submit: {
    success: 'alert alert-success',
    error: 'alert alert-error',
    loading: 'loading loading-spinner',
  },
}

// Required form behaviors:
// 1. Show validation state immediately after user interaction
// 2. Clear error states when user starts correcting
// 3. Disable submit button during processing
// 4. Show progress indicators for multi-step forms
// 5. Auto-save draft states for longer forms
```

### Audio Interface Standards
```typescript
// Audio control requirements
interface AudioControlProps {
  // Visual states
  idle: 'btn btn-circle btn-primary'
  recording: 'btn btn-circle btn-error animate-pulse'
  playing: 'btn btn-circle btn-success'
  loading: 'btn btn-circle btn-disabled loading loading-spinner'
  
  // Accessibility
  ariaLabel: string
  keyboardShortcut?: string
  
  // Feedback
  visualFeedback: boolean // Show waveform or level indicator
  hapticFeedback: boolean // Vibration on mobile
}

// Audio interaction patterns:
// 1. Hold-to-record vs toggle recording (user preference)
// 2. Visual feedback during recording (waveform/levels)
// 3. Playback controls (play/pause/speed)
// 4. Audio quality indicators
// 5. Retry recording option
```

## Learning-Specific UI Patterns

### Assessment Interface
```typescript
// Question display standards
interface QuestionComponentProps {
  question: {
    id: string
    type: 'multiple_choice' | 'fill_blank' | 'conversation' | 'translation'
    content: string
    options?: string[]
    audioUrl?: string
    imageUrl?: string
    difficulty: 'beginner' | 'intermediate' | 'advanced'
  }
  
  // Progress indicators
  currentQuestion: number
  totalQuestions: number
  timeRemaining?: number
  
  // Interaction handlers
  onAnswer: (answer: string) => void
  onSkip: () => void
  onRequestHint: () => void
  onPlayAudio: () => void
}

// Assessment UI requirements:
// 1. Clear progress indication (X of Y questions)
// 2. Time remaining display (if timed)
// 3. Skip option with confirmation
// 4. Hint system with usage tracking
// 5. Answer confidence indicator
// 6. Review mode for completed questions
```

### Progress Visualization
```typescript
// Progress display components
const progressComponents = {
  // Overall progress
  radialProgress: {
    component: 'radial-progress',
    colors: {
      beginner: 'text-info',
      intermediate: 'text-warning',
      advanced: 'text-success',
    },
    sizes: {
      sm: 'w-16 h-16',
      md: 'w-24 h-24',
      lg: 'w-32 h-32',
    },
  },
  
  // Skill breakdown
  skillBars: {
    component: 'progress',
    skills: ['speaking', 'listening', 'reading', 'writing', 'grammar', 'vocabulary'],
    colors: {
      speaking: 'progress-primary',
      listening: 'progress-secondary',
      reading: 'progress-accent',
      writing: 'progress-info',
      grammar: 'progress-success',
      vocabulary: 'progress-warning',
    },
  },
  
  // Streak tracking
  streakDisplay: {
    component: 'stat',
    icons: {
      fire: '🔥', // Active streak
      calendar: '📅', // Days practiced
      target: '🎯', // Goals met
    },
  },
}
```

### Feedback & Encouragement
```typescript
// Feedback message patterns
const feedbackPatterns = {
  // Immediate response feedback
  correct: {
    message: 'Excellent! That\'s correct.',
    icon: '✅',
    color: 'text-success',
    animation: 'animate-bounce',
    sound: 'success.mp3',
  },
  
  incorrect: {
    message: 'Not quite right. Let\'s try again.',
    icon: '❌',
    color: 'text-error',
    animation: 'animate-shake',
    sound: 'error.mp3',
    showCorrection: true,
  },
  
  partial: {
    message: 'You\'re on the right track!',
    icon: '⚡',
    color: 'text-warning',
    animation: 'animate-pulse',
    sound: 'partial.mp3',
    showHint: true,
  },
  
  // Motivational messages
  encouragement: {
    streak: 'Amazing! You\'re on a {days}-day streak! 🔥',
    improvement: 'Great progress! You\'ve improved by {percentage}% this week! 📈',
    milestone: 'Congratulations! You\'ve reached {level} level! 🎉',
    consistency: 'Fantastic consistency! You\'ve practiced {days} days this week! ⭐',
  },
}

// Feedback timing requirements:
// 1. Immediate feedback (< 100ms) for simple interactions
// 2. Contextual feedback (1-2s) for learning responses
// 3. Summary feedback at session end
// 4. Progress celebration at milestones
```

## Navigation & Information Architecture

### Primary Navigation
```typescript
// Main navigation structure
const navigationStructure = {
  primary: [
    {
      label: 'Learn',
      icon: '📚',
      href: '/learn',
      description: 'Interactive lessons and practice',
    },
    {
      label: 'Assess',
      icon: '📝',
      href: '/assess',
      description: 'Test your knowledge and track progress',
    },
    {
      label: 'Converse',
      icon: '💬',
      href: '/converse',
      description: 'Practice conversations with AI',
    },
    {
      label: 'Progress',
      icon: '📊',
      href: '/progress',
      description: 'View your learning analytics',
    },
  ],
  
  secondary: [
    {
      label: 'Profile',
      icon: '👤',
      href: '/profile',
    },
    {
      label: 'Settings',
      icon: '⚙️',
      href: '/settings',
    },
  ],
}

// Navigation requirements:
// 1. Clear active state indication
// 2. Breadcrumb navigation for deep pages
// 3. Quick access to current session
// 4. Progress indicators in navigation
// 5. Keyboard navigation support
```

### Content Organization
```typescript
// Page layout patterns
const layoutPatterns = {
  // Dashboard layout
  dashboard: {
    header: 'Welcome back, {userName}!',
    sections: [
      'currentStreak',
      'todaysPractice',
      'recentProgress',
      'recommendedContent',
      'upcomingGoals',
    ],
  },
  
  // Learning session layout
  session: {
    header: 'sessionProgress',
    main: 'contentArea',
    sidebar: 'hintsAndHelp',
    footer: 'navigationControls',
  },
  
  // Assessment layout
  assessment: {
    header: 'progressAndTimer',
    main: 'questionContent',
    footer: 'answerControls',
  },
}
```

## Error Handling & Edge Cases

### Error State Patterns
```typescript
// Error display standards
const errorStates = {
  // Network errors
  offline: {
    icon: '📡',
    title: 'You\'re offline',
    message: 'Don\'t worry! You can continue practicing. Your progress will sync when you\'re back online.',
    actions: ['Continue Offline', 'Retry Connection'],
  },
  
  // API errors
  serverError: {
    icon: '⚠️',
    title: 'Something went wrong',
    message: 'We\'re having trouble connecting to our servers. Please try again in a moment.',
    actions: ['Retry', 'Go Back'],
  },
  
  // Audio errors
  microphoneError: {
    icon: '🎤',
    title: 'Microphone access needed',
    message: 'To practice speaking, please allow microphone access in your browser settings.',
    actions: ['Enable Microphone', 'Skip Speaking Practice'],
  },
  
  // Content errors
  contentNotFound: {
    icon: '📄',
    title: 'Content not available',
    message: 'This lesson isn\'t available right now. Let\'s try something else!',
    actions: ['Browse Other Lessons', 'Go to Dashboard'],
  },
}

// Error handling requirements:
// 1. Never show technical error messages to users
// 2. Always provide actionable next steps
// 3. Maintain learning context when possible
// 4. Offer alternative paths forward
// 5. Log errors for debugging without exposing details
```

### Loading States
```typescript
// Loading state patterns
const loadingStates = {
  // Content loading
  contentLoading: {
    skeleton: 'skeleton h-32 w-full',
    message: 'Preparing your lesson...',
    timeout: 10000, // 10 seconds
  },
  
  // AI processing
  aiProcessing: {
    animation: 'loading loading-dots loading-lg',
    message: 'AI is analyzing your response...',
    timeout: 30000, // 30 seconds
  },
  
  // Audio processing
  audioProcessing: {
    animation: 'loading loading-spinner loading-md',
    message: 'Processing audio...',
    timeout: 15000, // 15 seconds
  },
  
  // Assessment scoring
  assessmentScoring: {
    animation: 'loading loading-ring loading-lg',
    message: 'Calculating your results...',
    timeout: 20000, // 20 seconds
  },
}

// Loading state requirements:
// 1. Show loading state immediately (< 100ms)
// 2. Provide context about what's happening
// 3. Set reasonable timeouts with fallbacks
// 4. Allow cancellation for long operations
// 5. Show progress when possible
```

## Personalization & Adaptation

### User Preference Management
```typescript
// User preference categories
interface UserPreferences {
  // Learning preferences
  learning: {
    nativeLanguage: string
    targetLanguages: string[]
    difficultyPreference: 'adaptive' | 'challenging' | 'comfortable'
    sessionLength: 'short' | 'medium' | 'long' // 5min, 15min, 30min
    practiceTime: string // preferred time of day
    reminderFrequency: 'daily' | 'weekly' | 'none'
  }
  
  // Interface preferences
  interface: {
    theme: 'light' | 'dark' | 'auto'
    fontSize: 'small' | 'medium' | 'large'
    reducedMotion: boolean
    highContrast: boolean
    audioAutoplay: boolean
  }
  
  // Accessibility preferences
  accessibility: {
    screenReader: boolean
    keyboardNavigation: boolean
    audioDescriptions: boolean
    subtitles: boolean
    slowSpeech: boolean
  }
  
  // Notification preferences
  notifications: {
    practiceReminders: boolean
    progressUpdates: boolean
    achievementAlerts: boolean
    weeklyReports: boolean
  }
}

// Preference application requirements:
// 1. Apply preferences immediately without page reload
// 2. Sync preferences across devices
// 3. Respect system preferences (dark mode, reduced motion)
// 4. Provide easy reset to defaults
// 5. Explain impact of each preference
```

### Adaptive Interface Elements
```typescript
// Dynamic content adaptation
const adaptiveElements = {
  // Difficulty adjustment
  difficultyIndicators: {
    tooEasy: {
      trigger: 'consecutiveCorrect >= 5',
      action: 'suggestLevelIncrease',
      message: 'You\'re doing great! Ready for a challenge?',
    },
    tooHard: {
      trigger: 'consecutiveIncorrect >= 3',
      action: 'offerHelp',
      message: 'Let\'s slow down and practice this concept.',
    },
  },
  
  // Content recommendations
  contentSuggestions: {
    basedOnWeakness: 'Practice more {skillArea} to improve your overall score',
    basedOnInterest: 'You seem to enjoy {topic}. Here are more lessons like this!',
    basedOnGoals: 'This lesson will help you reach your goal of {goal}',
  },
  
  // Interface adaptations
  interfaceAdaptations: {
    slowLearner: {
      moreTime: true,
      extraHints: true,
      simplifiedInstructions: true,
    },
    fastLearner: {
      skipBasics: true,
      advancedOptions: true,
      efficientNavigation: true,
    },
  },
}
```

## Performance & Optimization

### User-Perceived Performance
```typescript
// Performance optimization strategies
const performanceStrategies = {
  // Perceived speed improvements
  optimisticUpdates: {
    // Show immediate feedback before server confirmation
    answerSubmission: 'Show correct/incorrect immediately',
    progressUpdate: 'Update progress bar immediately',
    streakIncrement: 'Show new streak count immediately',
  },
  
  // Preloading strategies
  contentPreloading: {
    nextQuestion: 'Preload while user answers current question',
    audioFiles: 'Preload audio for current session',
    images: 'Lazy load images with blur placeholder',
  },
  
  // Caching strategies
  clientCaching: {
    userProgress: 'Cache in localStorage, sync periodically',
    preferences: 'Cache in localStorage, immediate updates',
    completedLessons: 'Cache for offline access',
  },
}

// Performance requirements:
// 1. First meaningful paint < 1.5s
// 2. Time to interactive < 3s
// 3. Smooth animations (60fps)
// 4. Responsive interactions (< 100ms feedback)
// 5. Efficient memory usage (< 50MB)
```

### Offline Capability
```typescript
// Offline functionality requirements
const offlineCapabilities = {
  // Available offline
  availableOffline: [
    'Previously accessed lessons',
    'Downloaded audio files',
    'Progress tracking (local)',
    'Basic practice exercises',
    'Cached assessment questions',
  ],
  
  // Sync when online
  syncWhenOnline: [
    'Progress updates',
    'Completed assessments',
    'New content downloads',
    'Preference changes',
    'Achievement unlocks',
  ],
  
  // Offline indicators
  offlineUI: {
    indicator: 'Show offline badge in header',
    limitations: 'Explain what\'s not available offline',
    syncStatus: 'Show sync progress when reconnected',
  },
}
```

## Testing & Quality Assurance

### User Testing Scenarios
```typescript
// Critical user journeys to test
const testScenarios = {
  // New user onboarding
  onboarding: {
    steps: [
      'Account creation',
      'Language selection',
      'Skill assessment',
      'First lesson completion',
      'Progress review',
    ],
    successCriteria: 'User completes first lesson within 10 minutes',
  },
  
  // Daily practice session
  dailyPractice: {
    steps: [
      'Login/resume session',
      'Select practice type',
      'Complete exercises',
      'Review feedback',
      'View progress update',
    ],
    successCriteria: 'User completes session and feels motivated to continue',
  },
  
  // Assessment flow
  assessment: {
    steps: [
      'Start assessment',
      'Answer questions',
      'Submit responses',
      'View results',
      'Understand next steps',
    ],
    successCriteria: 'User understands their level and feels confident about recommendations',
  },
}

// Accessibility testing requirements:
// 1. Screen reader compatibility
// 2. Keyboard-only navigation
// 3. Color contrast validation
// 4. Touch target size verification
// 5. Voice control compatibility
```

### Quality Metrics
```typescript
// User experience metrics to track
const uxMetrics = {
  // Engagement metrics
  engagement: {
    sessionDuration: 'Average time spent per session',
    returnRate: 'Percentage of users returning within 7 days',
    completionRate: 'Percentage of started sessions completed',
    streakLength: 'Average consecutive days of practice',
  },
  
  // Usability metrics
  usability: {
    taskSuccess: 'Percentage of tasks completed successfully',
    errorRate: 'Number of user errors per session',
    helpUsage: 'Frequency of help/hint usage',
    navigationEfficiency: 'Steps to complete common tasks',
  },
  
  // Performance metrics
  performance: {
    loadTime: 'Time to first meaningful content',
    responseTime: 'Time from user action to feedback',
    errorRecovery: 'Time to recover from errors',
    offlineUsage: 'Percentage of offline interactions',
  },
}
```

## Critical Frontend User Rules

1. **Learning comes first** - Every design decision should support the learning experience
2. **Immediate feedback** - Users should never wonder if their action was registered
3. **Progressive disclosure** - Show complexity gradually as users become more comfortable
4. **Accessible by default** - Design for all users, not just the majority
5. **Mobile-first thinking** - Most users will access on mobile devices
6. **Offline resilience** - Core functionality should work without internet
7. **Performance matters** - Slow interfaces kill motivation
8. **Error recovery** - Always provide a path forward when things go wrong
9. **Personalization** - Adapt to individual learning styles and preferences
10. **Encouragement** - Celebrate progress and maintain motivation

## Component Library Standards

### Reusable Component Requirements
```typescript
// All components must include:
interface ComponentStandards {
  // Accessibility
  ariaLabel: string
  keyboardSupport: boolean
  screenReaderSupport: boolean
  
  // Responsiveness
  mobileOptimized: boolean
  touchFriendly: boolean
  
  // Performance
  lazyLoaded: boolean
  memoized: boolean
  
  // Theming
  darkModeSupport: boolean
  customizable: boolean
  
  // Testing
  testId: string
  documented: boolean
}

// Component documentation requirements:
// 1. Usage examples
// 2. Props documentation
// 3. Accessibility notes
// 4. Performance considerations
// 5. Browser compatibility
```

Remember: You are building for learners, not just users. Every interaction should feel supportive, encouraging, and focused on helping users achieve their language learning goals.