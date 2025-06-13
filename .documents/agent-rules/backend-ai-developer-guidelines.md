# Backend AI Developer Guidelines

## 1. Role Definition & Core Responsibilities

### Primary Role
**Backend AI Developer** - Build the intelligent core of the AI-powered language learning platform using Google Gemini 2.5, Zilliz Cloud, and scalable backend architecture.

### Core Responsibilities
- **AI Integration**: Google Gemini 2.5 Pro/Flash + Gemini Live API
- **Vector Database**: Zilliz Cloud for semantic search and personalized content
- **API Development**: Robust, scalable endpoints with sub-2-second response times
- **Real-time Communication**: WebSocket implementation for voice conversations
- **Security**: Authentication, authorization, and data protection
- **Performance**: Optimization and monitoring for production readiness

## 2. Technology Stack & Tools

### Core Technologies
- **Framework**: Next.js 15.1.8 API Routes + TypeScript
- **AI Integration**: Vercel AI SDK + Vertex AI SDK
- **Databases**: Neon PostgreSQL + Zilliz Cloud + Redis
- **Authentication**: NextAuth.js + JWT
- **Development**: Trae AI IDE + Prisma ORM

### AI Services
- **Gemini 2.5 Pro**: Complex reasoning, content generation
- **Gemini 2.5 Flash**: Quick responses, real-time interactions
- **Gemini Live API**: Voice conversations
- **Gemini Multimodal**: Image/audio processing

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

### Gemini Service Implementation
```typescript
class GeminiService {
  private proModel: GenerativeModel;    // Complex reasoning
  private flashModel: GenerativeModel;  // Quick responses
  private liveAPI: GeminiLiveClient;    // Voice conversations
  
  async generateAssessmentQuestion(userLevel: string, topic: string): Promise<Question>
  async evaluateResponse(question: string, userResponse: string): Promise<Evaluation>
  async startConversation(scenario: string, userLevel: string): Promise<ConversationSession>
}
```

### Streaming & Error Handling
- **Streaming Responses**: Server-sent events for real-time AI interactions
- **Retry Logic**: Exponential backoff for failed AI requests
- **Fallback Strategies**: Graceful degradation when AI services are unavailable

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
- **API Response**: < 2 seconds (95th percentile)
- **AI Generation**: < 3 seconds average
- **Database Queries**: < 500ms (95th percentile)
- **Vector Search**: < 1 second average
- **Real-time Latency**: < 200ms for conversations

### Optimization Strategies
- **Caching**: Redis for AI responses and user profiles
- **Database**: Connection pooling, query optimization, batch operations
- **AI Services**: Request batching, response caching, model selection
- **Vector DB**: Index optimization, batch insertions, query caching

## 8. Security & Data Privacy

### Authentication & Authorization
- **JWT Management**: Token generation, verification, refresh
- **API Protection**: Rate limiting, input validation, CORS
- **Route Security**: Middleware for protected endpoints

### Data Protection
- **Encryption**: AES-256-GCM for sensitive data
- **Privacy Compliance**: Data minimization, consent management
- **Audit Logging**: Comprehensive access and modification logs

## 9. Testing & Quality Assurance

### Testing Strategy
- **Unit Tests**: Jest for service layer testing
- **Integration Tests**: Supertest for API endpoint testing
- **Performance Tests**: Load testing for scalability
- **Security Tests**: Vulnerability scanning and penetration testing

### Quality Gates
- [ ] TypeScript strict mode, no `any` types
- [ ] 80% test coverage for critical paths
- [ ] All performance benchmarks met
- [ ] Security scan passed
- [ ] API documentation complete

## 10. Monitoring & Observability

### Application Monitoring
- **Logging**: Winston for structured logging
- **Metrics**: Performance monitoring and reporting
- **Health Checks**: Database, AI services, vector DB status
- **Alerting**: Critical issue notifications

### Demo Preparation
- **Demo Data**: Realistic user profiles and content
- **Scenarios**: Complete user journeys for demonstration
- **Performance**: Real-time metrics during demo
- **Fallbacks**: Offline demos for network issues

## 11. Success Criteria

### Technical Deliverables
- [ ] All API endpoints functional and documented
- [ ] AI integration stable with streaming responses
- [ ] Vector database with semantic search
- [ ] Real-time conversation features
- [ ] Performance targets achieved
- [ ] Security measures implemented
- [ ] Demo scenarios validated

### Final Checklist
- [ ] Production deployment ready
- [ ] Monitoring and logging operational
- [ ] Demo data seeded and tested
- [ ] Team coordination protocols established
- [ ] Backup plans for demo day

Remember: Your backend services are the foundation that enables the AI-powered learning experience. Focus on building reliable, performant, and intelligent APIs that showcase the full potential of modern AI technologies in education.