# Frontend AI Developer Guidelines
## AI-Powered Language Learning Platform

> **📋 Reference**: See `project-rules.md` for comprehensive technical standards, architecture guidelines, and project constraints.

## 1. Role & Mission

**Primary Role**: Frontend AI Developer for AI-powered language learning platform  
**Mission**: Create intuitive, responsive UI with seamless AI integration within 30-hour hackathon timeline

### Core Responsibilities
- **UI/UX Development**: Next.js 15 + DaisyUI + Tailwind CSS responsive interfaces
- **Real-time Interactions**: Voice interfaces, live conversations, streaming AI responses
- **State Management**: Zustand for optimal application state management
- **User Experience**: Intuitive learning journeys and assessment flows
- **API Integration**: Coordinate with Backend Developer for seamless connectivity

## 2. Technology Stack

### Core Technologies
- **Framework**: Next.js 15.1.8 (App Router) + React 18+ + TypeScript
- **UI/Styling**: DaisyUI + Tailwind CSS V4 (mobile-first)
- **State**: Zustand (user session, conversation, assessment, audio)
- **Voice**: Web Speech API + Gemini Live API
- **Tools**: Trae AI IDE, ESLint, Prettier, React DevTools

## 3. Development Phases & Tasks

### Phase 1: Foundation (Hours 1-6) - 2 hours total

**Task 1.1: Project Setup (45 min)**
- Next.js 15 + App Router + TypeScript + DaisyUI setup
- Basic routing structure (`/app` directory)
- **Deliverable**: Working Next.js foundation

**Task 1.2: Authentication UI (75 min)**
- NextAuth.js frontend integration
- Login/signup pages with DaisyUI components
- Protected route components
- **Deliverable**: Complete auth flow UI
- **Coordination**: Backend auth API endpoints

### Phase 2: Core Features (Hours 7-20)

**Assessment Engine (1.5 hours)**
- Interactive assessment forms with voice/text input
- Progress indicators and question display
- Loading states and error handling
- **State**: Zustand for assessment progress
- **Coordination**: Backend assessment API

**Conversation Interface (2 hours)**
- Real-time voice chat UI with Gemini Live API
- Conversation history display
- Feedback visualization and audio controls
- **State**: Zustand for conversation management
- **Coordination**: Backend WebSocket integration

**Content Display (1 hour)**
- Reading passage and exercise interfaces
- Content difficulty indicators
- Interactive learning components
- Add multimodal content support (images, audio)
- **Deliverable**: Content presentation UI
- **Coordination**: Work with Backend Developer for content API integration

#### 2.4 Progress Tracking Dashboard (Hours 19-20)
**Your Tasks: 45 minutes**

#### Task 2.11: Progress Dashboard UI (45 minutes)
- Create visual progress indicators using DaisyUI charts
- Implement achievement displays and badges
- Design skill-specific metrics visualization
- Add responsive dashboard layout
- **Deliverable**: Progress tracking interface
- **State Management**: Use Zustand for dashboard data management

### Phase 3: Enhancement (Hours 21-30) - 2 hours total

**Progress Dashboard (60 min)**
- User progress visualization with charts
- Learning analytics and achievement indicators
- **State**: Zustand for progress data

**UI Polish (60 min)**
- Performance optimization and responsive improvements
- Animations and accessibility testing
- **Coordination**: Debugger for performance validation

## 4. Key Implementation Areas

### State Management (Zustand)
```typescript
// Core Stores
UserStore: user, proficiencyLevel, targetLanguage
ConversationStore: messages, isRecording, audioLevel
AssessmentStore: currentQuestion, responses, score
ProgressStore: overallProgress, achievements, streakCount
```

### API Integration Patterns
- **Authentication**: NextAuth.js with JWT tokens
- **Real-time**: WebSocket for conversation features
- **Streaming**: Vercel AI SDK for AI responses
- **Error Handling**: Retry logic and user-friendly messages

### Performance Optimization
- **React.memo**: Memoize stable components
- **Code Splitting**: Route-based lazy loading
- **Image Optimization**: Next.js Image with WebP
- **Bundle Analysis**: Monitor and optimize bundle size

## 5. Quality Standards

### Accessibility (WCAG 2.1 AA)
- Keyboard navigation and screen reader support
- Color contrast and focus management
- Voice interface alternatives

### Testing Strategy
- **Unit**: Component testing with React Testing Library
- **Integration**: API and state management testing
- **E2E**: Complete user flows with Playwright

### Performance Targets
- Page load: < 3 seconds
- API responses: < 2 seconds
- Voice response: < 1.5 seconds

## 6. Success Criteria

### Technical Deliverables
- [ ] Responsive UI across all devices
- [ ] Voice features with Web Speech API + Gemini Live
- [ ] Optimized state management with Zustand
- [ ] Complete API integration
- [ ] Accessibility compliance
- [ ] Performance targets met

### Demo Preparation
- [ ] All user flows tested
- [ ] Demo scenarios prepared
- [ ] Fallback plans for network issues
- [ ] Real-time performance monitoring

> **📋 Reference**: See `project-rules.md` for detailed technical specifications, error handling patterns, and deployment guidelines.

## 7. Communication & Coordination

### Team Collaboration
- **Backend Developer**: API specifications, real-time features, auth flow
- **Debugger**: Component testing, performance metrics, integration testing
- **Documentation**: Component library, state management, API patterns

### Final Deliverables
- [ ] Responsive UI with voice integration
- [ ] Optimized state management (Zustand)
- [ ] Complete API integration
- [ ] WCAG 2.1 AA accessibility compliance
- [ ] Performance targets met
- [ ] Demo scenarios tested
- [ ] Production-ready deployment

> **📋 Reference**: See `project-rules.md` for comprehensive project guidelines, technical specifications, and detailed coordination protocols.

Remember: Your role is crucial for creating the first impression and user engagement. Focus on delivering an intuitive, responsive, and visually appealing interface that showcases the power of our AI-driven language learning platform.