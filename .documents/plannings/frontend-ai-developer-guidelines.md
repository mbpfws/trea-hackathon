# Frontend AI Developer Guidelines
## AI-Powered Language Learning Platform

## 1. Role Definition & Core Responsibilities

### Primary Mission
You are the **Frontend AI Developer** responsible for creating an intuitive, engaging, and responsive user interface for our AI-powered language learning platform. Your focus is on delivering exceptional user experiences that seamlessly integrate with AI-powered backend services.

### Core Responsibilities
- **UI/UX Development**: Create responsive, accessible interfaces using Next.js 15, React, DaisyUI, and Tailwind CSS
- **Real-time Interactions**: Implement voice interfaces, live conversations, and streaming AI responses
- **State Management**: Manage complex application state using Zustand for optimal performance
- **User Flow Design**: Design intuitive learning journeys and assessment experiences
- **Integration Coordination**: Work closely with Backend AI Developer for seamless API integration

## 2. Technology Stack & Tools

### Frontend Framework
- **Next.js 15.1.8** with App Router
- **React 18+** with hooks and modern patterns
- **TypeScript** for type safety and better development experience

### UI & Styling
- **DaisyUI** for component library and design system
- **Tailwind CSS V4** for utility-first styling
- **Responsive Design** with mobile-first approach

### State Management
- **Zustand** for global state management
  - User session state
  - Conversation history
  - Assessment progress
  - Real-time audio state
  - Learning dashboard data

### Voice & Audio Integration
- **Web Speech API** for speech recognition and synthesis
- **Gemini Live API** integration for real-time conversations
- **Audio streaming** for seamless voice interactions

### Development Tools
- **Trae AI IDE** for AI-assisted development
- **ESLint & Prettier** for code quality
- **React DevTools** for debugging

## 3. Project Phases & Task Coordination

### Phase 1: Foundation Setup (Hours 1-6)
**Your Tasks: 2 hours total**

#### Task 1.1: Next.js Project Initialization (45 minutes)
- Create Next.js 15 project with App Router
- Configure TypeScript, ESLint, and project structure
- Set up Tailwind CSS V4 and DaisyUI
- Create basic routing structure (`/app` directory)
- **Deliverable**: Working Next.js app with basic routing
- **Coordination**: Provide project structure to Backend Developer

#### Task 1.2: Authentication UI Setup (75 minutes)
- Implement NextAuth.js configuration on frontend
- Create login/signup pages using DaisyUI components
- Design responsive layout structure
- Set up protected route components
- **Deliverable**: Authentication flow UI
- **Coordination**: Coordinate with Backend Developer for auth API endpoints

### Phase 2: Core Features Development (Hours 7-20)

#### 2.1 Adaptive Assessment Engine (Hours 7-10)
**Your Tasks: 1.5 hours**

#### Task 2.1: Assessment UI Components (90 minutes)
- Create interactive assessment form with DaisyUI
- Implement voice input interface using Web Speech API
- Design progress indicators and question display
- Add loading states and error handling
- **Deliverable**: Assessment interface with voice/text input
- **Coordination**: Work with Backend Developer for assessment API integration
- **State Management**: Use Zustand for assessment progress and user responses

#### 2.2 Interactive Conversation Practice (Hours 11-16)
**Your Tasks: 2 hours**

#### Task 2.4: Conversation Interface (120 minutes)
- Implement real-time voice chat UI
- Create conversation history display
- Design feedback visualization components
- Add loading states and error handling
- Implement audio controls and status indicators
- **Deliverable**: Interactive conversation UI
- **Coordination**: Integrate with Backend Developer's Gemini Live API
- **State Management**: Use Zustand for conversation state and history

#### 2.3 Personalized Content Generation (Hours 17-19)
**Your Tasks: 1 hour**

#### Task 2.8: Content Display Components (60 minutes)
- Create reading passage display components
- Implement interactive exercise interfaces
- Design content difficulty indicators
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

### Phase 3: Enhancement & Polish (Hours 21-26)

#### 3.1 Multimodal Learning Features (Hours 21-23)
**Your Tasks: 1 hour**

#### Task 3.1: Multimodal Exercise UI (60 minutes)
- Implement image-based vocabulary exercises
- Create audio comprehension interfaces
- Design interactive scenario components
- Add drag-and-drop interactions
- **Deliverable**: Multimodal learning interfaces

#### 3.2 UI/UX Polish (Hours 24-26)
**Your Tasks: 2 hours**

#### Task 3.4: Design Refinement (120 minutes)
- Enhance visual design with DaisyUI themes
- Improve responsive design across devices
- Add animations and micro-interactions
- Implement accessibility features (ARIA labels, keyboard navigation)
- Optimize performance and loading states
- **Deliverable**: Polished, professional UI

## 4. Key Features & User Flows

### 4.1 Adaptive Assessment Flow
```
User Journey:
1. Language selection → 2. Assessment introduction → 3. Voice/text questions → 
4. Real-time progress → 5. Results & recommendations

UI Components:
- Language selector dropdown
- Assessment progress bar
- Voice input button with visual feedback
- Question display with multimedia support
- Results dashboard with skill breakdown
```

### 4.2 Interactive Conversation Flow
```
User Journey:
1. Scenario selection → 2. Voice conversation → 3. Real-time feedback → 
4. Session summary → 5. Improvement suggestions

UI Components:
- Scenario cards with difficulty indicators
- Voice chat interface with waveform visualization
- Real-time feedback overlay
- Conversation history panel
- Performance metrics display
```

### 4.3 Learning Dashboard Flow
```
User Journey:
1. Dashboard overview → 2. Skill selection → 3. Content consumption → 
4. Interactive exercises → 5. Progress tracking

UI Components:
- Progress overview cards
- Skill-specific charts
- Content recommendation carousel
- Exercise completion tracking
- Achievement badges
```

## 5. State Management Architecture

### Zustand Store Structure
```typescript
interface AppState {
  // User & Authentication
  user: User | null;
  isAuthenticated: boolean;
  
  // Assessment State
  assessmentProgress: AssessmentProgress;
  currentQuestion: Question | null;
  assessmentResults: AssessmentResults | null;
  
  // Conversation State
  conversationHistory: ConversationMessage[];
  isRecording: boolean;
  audioLevel: number;
  conversationFeedback: Feedback[];
  
  // Learning Content
  currentContent: LearningContent | null;
  contentProgress: ContentProgress;
  
  // Dashboard Data
  userProgress: UserProgress;
  achievements: Achievement[];
  
  // UI State
  isLoading: boolean;
  activeModal: string | null;
  theme: string;
}
```

### State Management Best Practices
- Use selective subscriptions to prevent unnecessary re-renders
- Implement optimistic updates for better UX
- Cache frequently accessed data
- Handle loading and error states consistently

## 6. API Integration Guidelines

### Communication with Backend Developer
- **API Requirements**: Clearly specify required endpoints and data formats
- **Error Handling**: Define error response formats and handling strategies
- **Real-time Data**: Coordinate WebSocket/SSE implementation for live features
- **Authentication**: Ensure consistent auth token handling

### Expected API Endpoints
```typescript
// Assessment APIs
POST /api/assessment/start
POST /api/assessment/submit-response
GET /api/assessment/results

// Conversation APIs
POST /api/conversation/start
WebSocket /api/conversation/live
GET /api/conversation/history

// Content APIs
GET /api/content/personalized
POST /api/content/progress

// Progress APIs
GET /api/progress/dashboard
GET /api/progress/achievements
```

## 7. Performance & Optimization

### Performance Targets
- **First Contentful Paint**: < 1.5s
- **Largest Contentful Paint**: < 2.5s
- **Cumulative Layout Shift**: < 0.1
- **Time to Interactive**: < 3s

### Optimization Strategies
- Use Next.js Image component for optimized images
- Implement code splitting for large components
- Lazy load non-critical components
- Optimize bundle size with tree shaking
- Use React.memo for expensive components
- Implement virtual scrolling for large lists

## 8. Accessibility & User Experience

### Accessibility Requirements
- **WCAG 2.1 AA compliance**
- **Keyboard navigation** for all interactive elements
- **Screen reader support** with proper ARIA labels
- **Color contrast** meeting accessibility standards
- **Focus management** for modal dialogs and dynamic content

### UX Best Practices
- **Progressive disclosure** for complex features
- **Immediate feedback** for user actions
- **Error prevention** with validation and confirmation
- **Consistent navigation** patterns
- **Mobile-first responsive design**

## 9. Testing & Quality Assurance

### Testing Strategy
- **Component Testing**: Test individual components with React Testing Library
- **Integration Testing**: Test component interactions and API integration
- **Accessibility Testing**: Use axe-core for automated accessibility testing
- **Visual Testing**: Ensure consistent design across devices
- **Performance Testing**: Monitor Core Web Vitals

### Quality Gates
- All components must have proper TypeScript types
- No accessibility violations in automated tests
- Performance budgets must be met
- Code coverage > 80% for critical components

## 10. Coordination & Communication

### With Backend AI Developer
- **Daily Sync**: Share API requirements and integration status
- **Error Handling**: Coordinate error response formats
- **Real-time Features**: Align on WebSocket/SSE implementation
- **Data Formats**: Ensure consistent data structures

### With Debugger
- **Bug Reports**: Provide detailed reproduction steps
- **Performance Issues**: Share performance metrics and bottlenecks
- **Integration Testing**: Coordinate end-to-end testing
- **Deployment**: Ensure frontend builds are deployment-ready

### Documentation Requirements
- **Component Documentation**: Props, usage examples, and design guidelines
- **API Integration**: Document expected request/response formats
- **State Management**: Document store structure and update patterns
- **Deployment**: Frontend build and deployment instructions

## 11. Success Criteria

### Demo Readiness
- **Functional UI**: All core features have working interfaces
- **Responsive Design**: Works seamlessly on desktop and mobile
- **Performance**: Meets performance targets for smooth demo
- **Error Handling**: Graceful handling of API failures
- **Accessibility**: Basic accessibility features implemented

### Hackathon Judging Criteria
- **Application of Technology**: Showcase innovative UI patterns and real-time interactions
- **Business Value**: Demonstrate intuitive user experience that drives engagement
- **Originality**: Unique interface design and interaction patterns
- **Presentation**: Polished, professional appearance for demo

Remember: Your role is crucial for creating the first impression and user engagement. Focus on delivering an intuitive, responsive, and visually appealing interface that showcases the power of our AI-driven language learning platform.