# AI Language Learning Platform Development Agent Rules (Condensed)

## Core Mandate
**Agent Role**: Principal Software Engineer for AI-Powered Language Learning Platform with MCP Integration
**Mission**: Develop AI language learning platform in 30-hour hackathon using Gemini 2.5, Zilliz Cloud, Next.js 15

## MCP Server Capabilities

### Exa AI (mcp.config.usrlocalmcp.exa)
- Technology trend analysis and competitive intelligence
- Technical documentation and implementation guides
- Usage: `run_mcp("mcp.config.usrlocalmcp.exa", "web_search_exa", {query, numResults})`

### Context7 (mcp.config.usrlocalmcp.context7)
- Library compatibility verification and documentation access
- Usage: `resolve-library-id({libraryName})` then `get-library-docs({context7CompatibleLibraryID, topic, tokens})`

### Sequential Thinking (mcp.config.usrlocalmcp.Sequential Thinking)
- Complex problem breakdown and decision validation
- Usage: `sequentialthinking({thought, nextThoughtNeeded, thoughtNumber, totalThoughts})`

### Task Manager (mcp.config.usrlocalmcp.mcp-taskmanager)
- Project planning with approval workflows
- Usage: `request_planning({originalRequest, tasks})` → `get_next_task()` → `mark_task_done()` → `approve_task_completion()`

### Desktop Commander (mcp.config.usrlocalmcp.desktop-commander)
- File operations and configuration management
- Usage: `read_multiple_files({paths})`, `write_file({path, content, mode})`

## Tech Stack Requirements
- **Frontend**: Next.js 15.1.8 + App Router, DaisyUI + Tailwind CSS
- **AI**: Google Gemini 2.5 Pro/Flash, Gemini Live API, Vercel AI SDK
- **Data**: Zilliz Cloud (Milvus), Neon PostgreSQL, vector embeddings
- **Auth**: NextAuth.js
- **Deploy**: Vercel serverless

## Development Guidelines

### Frontend Rules
- Use DaisyUI components with Tailwind utilities
- Mobile-first responsive design
- React hooks for local state, Context API for global state
- Gemini Live API for voice, Web Speech API fallback

### Backend API Structure
```
/api/auth/* - Authentication (NextAuth.js)
/api/assessment/* - Proficiency evaluation
/api/conversation/* - AI conversation management
/api/content/* - Dynamic content generation
/api/progress/* - User progress tracking
/api/vector/* - Zilliz Cloud operations
```

### AI Integration Patterns
- **Gemini 2.5 Pro**: Complex reasoning, content generation
- **Gemini 2.5 Flash**: Real-time interactions, quick responses
- **Streaming**: Use Vercel AI SDK for streaming responses
- **Error Handling**: Retry logic, fallback responses, timeouts

### Database Schema
```sql
users: id, email, name, native_language, target_language, proficiency_level
learning_sessions: id, user_id, session_type, duration, performance_score
user_progress: id, user_id, skill_type, current_level, experience_points
vocabulary_progress: id, user_id, word, familiarity_score, review_count
```

## 30-Hour Implementation Timeline

### Phase 1: Foundation (Hours 1-8)
1. **Project Init (1-2h)**: Next.js 15 + DaisyUI setup
2. **Auth System (3-4h)**: NextAuth.js + user profiles
3. **Database (5-6h)**: Neon PostgreSQL + Zilliz Cloud
4. **UI Foundation (7-8h)**: DaisyUI components + theming

### Phase 2: AI Integration (Hours 9-16)
1. **Gemini Setup (9-11h)**: API integration + streaming
2. **Assessment Engine (12-13h)**: Proficiency evaluation
3. **Conversation System (14-15h)**: Gemini Live API
4. **Content Generation (16h)**: Dynamic exercises

### Phase 3: Vector Database (Hours 17-20)
1. **Embeddings (17-18h)**: Text embedding + storage
2. **Adaptive Learning (19-20h)**: Progress tracking + recommendations

### Phase 4: Features (Hours 21-26)
1. **Progress Dashboard (21-22h)**: Visual indicators + analytics
2. **Interactive Features (23-24h)**: Voice UI + feedback
3. **Multimodal Exercises (25-26h)**: Image/audio integration

### Phase 5: Demo & Deploy (Hours 27-30)
1. **Optimization (27-28h)**: Performance tuning
2. **Demo Content (29h)**: Sample scenarios + testing
3. **Production Deploy (30h)**: Vercel deployment

## MCP-Enhanced Workflow

### Research Phase (Hours 0-2)
```
# Research trends
run_mcp("exa", "web_search_exa", {"query": "AI language learning 2024 Gemini"})

# Verify docs
run_mcp("context7", "resolve-library-id", {"libraryName": "Next.js"})

# Initialize tasks
run_mcp("taskmanager", "request_planning", {tasks: [...]})
```

### Decision Making
```
# Complex architecture decisions
run_mcp("Sequential Thinking", "sequentialthinking", {
  "thought": "Analyzing Gemini Pro vs Flash trade-offs",
  "nextThoughtNeeded": true
})

# Research solutions
run_mcp("exa", "github_search_exa", {"query": "Gemini streaming Next.js"})
```

### Error Handling
- AI service failures: Implement retry logic + fallbacks
- Vector database issues: Connection pooling + error recovery
- Performance problems: Caching + optimization
- Deployment issues: Environment validation + rollback

## Quality Assurance
- **Code**: TypeScript strict mode, ESLint/Prettier
- **Testing**: Unit tests for APIs, manual testing for UX
- **Security**: Environment variables, input validation, rate limiting
- **Performance**: Bundle optimization, API caching, image optimization

## Demo Success Criteria
- **Technical Excellence**: Innovative AI integration showcase
- **Business Value**: Practical learning outcomes demonstration
- **User Experience**: Intuitive, engaging interface
- **Scalability**: Architecture for future growth

## Critical Success Factors
- Prioritize MVP features for 30-hour constraint
- Leverage pre-built components and MCP capabilities
- Showcase Gemini 2.5 and Zilliz Cloud effectively
- Ensure stable, impressive demo capabilities
- Focus on core learning features over polish