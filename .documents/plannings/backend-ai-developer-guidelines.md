# Backend AI Developer Guidelines
## AI-Powered Language Learning Platform

## 1. Role Definition & Core Responsibilities

### Primary Mission
You are the **Backend AI Developer** responsible for building robust APIs, integrating advanced AI services, and managing data infrastructure for our AI-powered language learning platform. Your focus is on creating scalable, intelligent backend services that power personalized learning experiences.

### Core Responsibilities
- **API Development**: Design and implement RESTful APIs using Next.js API Routes
- **AI Integration**: Integrate Google Gemini 2.5 models for conversation, assessment, and content generation
- **Vector Database Management**: Implement Zilliz Cloud for semantic search and personalized content matching
- **Data Architecture**: Design and manage PostgreSQL schemas for user data and learning progress
- **Real-time Services**: Implement WebSocket/SSE for live conversation features
- **Performance Optimization**: Ensure sub-2-second response times for AI interactions

## 2. Technology Stack & Tools

### Backend Framework
- **Next.js 15.1.8 API Routes** with App Router
- **Vercel AI SDK** for streaming AI responses
- **TypeScript** for type safety and better development experience

### AI Integration
- **Google Gemini 2.5 Pro** for complex reasoning and content generation
- **Google Gemini 2.5 Flash** for real-time interactions and quick responses
- **Gemini Live API** for bidirectional audio streaming
- **Gemini Multimodal APIs** for image, video, and audio processing
- **Vertex AI SDK** for robust model interaction

### Database Systems
- **Zilliz Cloud (Managed Milvus)** for vector embeddings and similarity search
- **Neon PostgreSQL** for relational data storage
- **Prisma ORM** or direct SQL for database operations

### Authentication & Security
- **NextAuth.js** for user authentication
- **JWT tokens** for API security
- **Environment variables** for secure credential management

### Development Tools
- **Trae AI IDE** for AI-assisted development
- **Postman/Thunder Client** for API testing
- **Database management tools** for schema design

## 3. Project Phases & Task Coordination

### Phase 1: Foundation Setup (Hours 1-6)
**Your Tasks: 3 hours total**

#### Task 1.3: Database Schema Design and Setup (60 minutes)
- Configure Neon PostgreSQL connection
- Design and implement core database schemas:
  ```sql
  -- Users table
  users: id, email, name, native_language, target_language, proficiency_level, created_at, updated_at
  
  -- Learning sessions
  learning_sessions: id, user_id, session_type, duration, performance_score, created_at
  
  -- User progress tracking
  user_progress: id, user_id, skill_type, current_level, experience_points, updated_at
  
  -- Vocabulary progress
  vocabulary_progress: id, user_id, word, familiarity_score, review_count, last_reviewed
  
  -- Assessment results
  assessment_results: id, user_id, assessment_type, score, detailed_results, created_at
  
  -- Conversation history
  conversation_history: id, user_id, session_id, message_type, content, timestamp
  ```
- Set up database connection pooling
- **Deliverable**: Database schema and connection
- **Coordination**: Provide schema documentation to Frontend Developer

#### Task 1.4: Zilliz Cloud Integration (60 minutes)
- Set up Zilliz Cloud collection for embeddings
- Configure vector similarity search with appropriate metrics
- Create embedding generation utilities using Gemini
- Implement basic CRUD operations for vector data
- **Deliverable**: Vector database connection and basic operations
- **Coordination**: Document vector search capabilities for Frontend Developer

#### Task 1.5: Gemini API Integration Foundation (60 minutes)
- Configure Google AI SDK and Vertex AI SDK
- Set up API routes for Gemini interactions
- Implement basic text generation endpoint
- Configure streaming responses using Vercel AI SDK
- **Deliverable**: Working Gemini API integration
- **Coordination**: Provide API endpoint documentation to Frontend Developer

### Phase 2: Core Features Development (Hours 7-20)

#### 2.1 Adaptive Assessment Engine (Hours 7-10)
**Your Tasks: 2 hours**

#### Task 2.2: Assessment Logic and API (120 minutes)
- Develop assessment question generation with Gemini Pro
- Implement response evaluation using vector similarity
- Create proficiency scoring algorithm
- Store assessment results in Neon DB
- Implement adaptive difficulty adjustment
- **API Endpoints**:
  ```typescript
  POST /api/assessment/start
  POST /api/assessment/submit-response
  GET /api/assessment/results
  PUT /api/assessment/update-proficiency
  ```
- **Deliverable**: Complete assessment API with Zilliz integration
- **Coordination**: Provide assessment API documentation to Frontend Developer

#### 2.2 Interactive Conversation Practice (Hours 11-16)
**Your Tasks: 2.5 hours**

#### Task 2.5: Gemini Live API Integration (90 minutes)
- Implement bidirectional audio streaming with Gemini Live API
- Configure real-time conversation handling
- Integrate STT/TTS capabilities
- Handle WebSocket connections for real-time communication
- **API Endpoints**:
  ```typescript
  POST /api/conversation/start
  WebSocket /api/conversation/live
  GET /api/conversation/history
  POST /api/conversation/end
  ```
- **Deliverable**: Working voice conversation system
- **Coordination**: Coordinate WebSocket implementation with Frontend Developer

#### Task 2.6: Conversation Feedback System (60 minutes)
- Develop pronunciation analysis using Gemini audio processing
- Implement grammar correction logic
- Create contextual feedback generation
- Store conversation data and feedback in database
- **Deliverable**: Real-time feedback API
- **Coordination**: Define feedback data structures with Frontend Developer

#### 2.3 Personalized Content Generation (Hours 17-19)
**Your Tasks: 1.5 hours**

#### Task 2.9: Content Generation API (90 minutes)
- Implement Gemini-powered content creation
- Develop difficulty-based content filtering
- Create exercise generation logic (vocabulary, grammar, reading)
- Store generated content with embeddings in Zilliz
- Implement URL context processing for real-world content
- **API Endpoints**:
  ```typescript
  GET /api/content/personalized
  POST /api/content/generate
  POST /api/content/progress
  GET /api/content/recommendations
  ```
- **Deliverable**: Dynamic content generation system
- **Coordination**: Provide content API specifications to Frontend Developer

#### 2.4 Progress Tracking Dashboard (Hours 19-20)
**Your Tasks: 15 minutes**

#### Task 2.12: Progress Data API (15 minutes)
- Implement progress calculation logic
- Create dashboard data endpoints
- Aggregate user performance metrics
- **API Endpoints**:
  ```typescript
  GET /api/progress/dashboard
  GET /api/progress/achievements
  GET /api/progress/analytics
  ```
- **Deliverable**: Progress tracking API

### Phase 3: Enhancement & Polish (Hours 21-26)

#### 3.1 Multimodal Learning Features (Hours 21-23)
**Your Tasks: 1 hour**

#### Task 3.2: Multimodal Content Processing (60 minutes)
- Integrate Gemini's multimodal understanding
- Implement image/audio exercise generation
- Create scenario-based learning logic
- Process video content for learning materials
- **Deliverable**: Multimodal content system

#### 3.2 Smart Review System (Hours 23-24)
**Your Tasks: 1 hour**

#### Task 3.3: Intelligent Review Algorithm (60 minutes)
- Implement spaced repetition logic
- Create weakness identification using Zilliz vector search
- Develop adaptive review scheduling
- Optimize content recommendations based on user performance
- **Deliverable**: Smart review system

## 4. AI Integration Architecture

### Gemini Model Selection Strategy
```typescript
interface ModelSelectionStrategy {
  // Real-time interactions (< 500ms)
  realTimeInteractions: 'gemini-2.5-flash';
  
  // Complex content generation (2-5s acceptable)
  contentGeneration: 'gemini-2.5-pro';
  
  // Assessment and analysis (1-3s acceptable)
  assessment: 'gemini-2.5-pro';
  
  // Live conversation
  liveConversation: 'gemini-live-api';
  
  // Multimodal processing
  multimodal: 'gemini-2.5-pro';
}
```

### AI Service Integration Patterns
```typescript
// Streaming response pattern
export async function generateContent(prompt: string) {
  const stream = await gemini.generateContentStream({
    contents: [{ role: 'user', parts: [{ text: prompt }] }],
    generationConfig: {
      temperature: 0.7,
      topK: 40,
      topP: 0.95,
      maxOutputTokens: 1024,
    },
  });
  
  return stream;
}

// Multimodal processing pattern
export async function processMultimodalContent(content: MultimodalContent) {
  const response = await gemini.generateContent({
    contents: [{
      role: 'user',
      parts: [
        { text: content.prompt },
        { inlineData: { mimeType: content.mimeType, data: content.data } }
      ]
    }]
  });
  
  return response;
}
```

## 5. Vector Database Architecture

### Zilliz Cloud Collection Schema
```typescript
interface LearningContentEmbedding {
  id: string;
  content_type: 'vocabulary' | 'grammar' | 'reading' | 'conversation';
  difficulty_level: number; // 1-10
  language: string;
  content_text: string;
  embedding_vector: number[]; // 768-dimensional
  metadata: {
    topic: string;
    skills: string[];
    estimated_time: number;
    user_ratings: number;
  };
  created_at: Date;
}

interface UserProgressEmbedding {
  user_id: string;
  skill_vector: number[]; // User's skill representation
  proficiency_level: number;
  learning_preferences: string[];
  weak_areas: string[];
  updated_at: Date;
}
```

### Vector Search Implementation
```typescript
export class VectorSearchService {
  async findSimilarContent(
    userSkillVector: number[],
    contentType: string,
    limit: number = 10
  ) {
    const searchParams = {
      collection_name: 'learning_content',
      vector: userSkillVector,
      filter: `content_type == "${contentType}"`,
      limit,
      output_fields: ['id', 'content_text', 'difficulty_level', 'metadata']
    };
    
    return await this.zillizClient.search(searchParams);
  }
  
  async updateUserProgress(userId: string, newSkillVector: number[]) {
    await this.zillizClient.upsert({
      collection_name: 'user_progress',
      data: [{
        user_id: userId,
        skill_vector: newSkillVector,
        updated_at: new Date()
      }]
    });
  }
}
```

## 6. API Design & Documentation

### RESTful API Structure
```typescript
// Authentication endpoints
POST /api/auth/signin
POST /api/auth/signup
POST /api/auth/signout
GET /api/auth/session

// Assessment endpoints
POST /api/assessment/start
POST /api/assessment/submit-response
GET /api/assessment/results
PUT /api/assessment/update-proficiency

// Conversation endpoints
POST /api/conversation/start
WebSocket /api/conversation/live
GET /api/conversation/history
POST /api/conversation/feedback

// Content endpoints
GET /api/content/personalized
POST /api/content/generate
POST /api/content/progress
GET /api/content/recommendations

// Progress endpoints
GET /api/progress/dashboard
GET /api/progress/achievements
GET /api/progress/analytics

// Vector search endpoints
POST /api/vector/search
POST /api/vector/update-profile
```

### Error Handling Strategy
```typescript
interface APIError {
  code: string;
  message: string;
  details?: any;
  timestamp: string;
}

// Standard error responses
const ErrorCodes = {
  VALIDATION_ERROR: 'VALIDATION_ERROR',
  AI_SERVICE_ERROR: 'AI_SERVICE_ERROR',
  DATABASE_ERROR: 'DATABASE_ERROR',
  AUTHENTICATION_ERROR: 'AUTHENTICATION_ERROR',
  RATE_LIMIT_ERROR: 'RATE_LIMIT_ERROR'
} as const;
```

## 7. Performance & Optimization

### Performance Targets
- **AI Response Time**: < 2s for content generation
- **Real-time Conversation**: < 500ms latency
- **Vector Search**: < 100ms for similarity queries
- **Database Queries**: < 50ms for simple operations
- **API Throughput**: 1000+ requests/minute

### Optimization Strategies
```typescript
// Caching strategy
interface CacheStrategy {
  // Redis for session data
  sessionCache: 'redis';
  
  // In-memory for frequently accessed data
  userProfiles: 'memory';
  
  // CDN for static content
  staticContent: 'vercel-edge';
  
  // Database query optimization
  queryOptimization: 'connection-pooling' | 'read-replicas';
}

// Rate limiting
export const rateLimiter = {
  ai_requests: { windowMs: 60000, max: 100 }, // 100 requests per minute
  conversation: { windowMs: 60000, max: 50 },  // 50 conversations per minute
  assessment: { windowMs: 3600000, max: 10 }   // 10 assessments per hour
};
```

## 8. Security & Data Privacy

### Security Implementation
```typescript
// Input validation
import { z } from 'zod';

const AssessmentRequestSchema = z.object({
  userId: z.string().uuid(),
  responses: z.array(z.object({
    questionId: z.string(),
    answer: z.string().max(1000),
    timestamp: z.date()
  })),
  sessionId: z.string().uuid()
});

// API key management
const secureHeaders = {
  'X-API-Key': process.env.INTERNAL_API_KEY,
  'Authorization': `Bearer ${process.env.GEMINI_API_KEY}`,
  'Content-Type': 'application/json'
};
```

### Data Privacy Compliance
- **User Data Encryption**: Encrypt sensitive user data at rest
- **API Security**: Implement proper authentication and authorization
- **Data Retention**: Implement data retention policies
- **Audit Logging**: Log all data access and modifications

## 9. Testing & Quality Assurance

### Testing Strategy
```typescript
// Unit tests for AI integration
describe('GeminiService', () => {
  test('should generate appropriate content for beginner level', async () => {
    const content = await geminiService.generateContent({
      level: 'beginner',
      topic: 'greetings',
      language: 'spanish'
    });
    
    expect(content).toBeDefined();
    expect(content.difficulty).toBeLessThanOrEqual(3);
  });
});

// Integration tests for vector search
describe('VectorSearchService', () => {
  test('should return relevant content for user skill level', async () => {
    const results = await vectorService.findSimilarContent(
      mockUserSkillVector,
      'vocabulary',
      5
    );
    
    expect(results).toHaveLength(5);
    expect(results[0].score).toBeGreaterThan(0.8);
  });
});
```

### Quality Gates
- **API Response Time**: All endpoints must respond within target times
- **Error Rate**: < 1% error rate for AI services
- **Test Coverage**: > 85% code coverage for critical paths
- **Security Scan**: No high-severity security vulnerabilities

## 10. Coordination & Communication

### With Frontend AI Developer
- **API Contracts**: Provide detailed OpenAPI specifications
- **Real-time Updates**: Coordinate WebSocket/SSE implementation
- **Error Handling**: Define consistent error response formats
- **Data Formats**: Ensure type-safe data structures

### With Debugger
- **Performance Metrics**: Provide API performance data
- **Error Logs**: Implement comprehensive logging
- **Integration Testing**: Support end-to-end testing
- **Deployment**: Ensure backend services are deployment-ready

### Documentation Requirements
- **API Documentation**: Complete OpenAPI/Swagger documentation
- **Database Schema**: Entity relationship diagrams and migration scripts
- **AI Integration**: Model selection rationale and prompt engineering
- **Deployment**: Infrastructure setup and environment configuration

## 11. Monitoring & Observability

### Monitoring Implementation
```typescript
// Performance monitoring
interface PerformanceMetrics {
  aiResponseTime: number;
  databaseQueryTime: number;
  vectorSearchTime: number;
  errorRate: number;
  throughput: number;
}

// Health check endpoints
GET /api/health/status
GET /api/health/ai-services
GET /api/health/database
GET /api/health/vector-db
```

### Logging Strategy
- **Structured Logging**: Use JSON format for all logs
- **Error Tracking**: Implement error aggregation and alerting
- **Performance Tracking**: Monitor API response times and throughput
- **User Activity**: Track learning progress and engagement metrics

## 12. Success Criteria

### Demo Readiness
- **Functional APIs**: All core endpoints working reliably
- **AI Integration**: Seamless Gemini model interactions
- **Real-time Features**: Stable WebSocket connections
- **Data Persistence**: Reliable database operations
- **Performance**: Meeting response time targets

### Hackathon Judging Criteria
- **Application of Technology**: Innovative use of Gemini 2.5 and Zilliz Cloud
- **Business Value**: Scalable architecture supporting real user needs
- **Originality**: Creative AI integration patterns and personalization algorithms
- **Presentation**: Robust backend supporting impressive demo features

Remember: Your backend services are the foundation that enables the AI-powered learning experience. Focus on building reliable, performant, and intelligent APIs that showcase the full potential of modern AI technologies in education.