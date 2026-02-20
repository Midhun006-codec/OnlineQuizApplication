# 🏗️ Application Architecture

## Overview
This document explains the architecture and design decisions of the Online Quiz Application.

## Architecture Pattern

The application follows a **Modular JavaScript Architecture** with clear separation of concerns.

```
┌─────────────────────────────────────────────────────┐
│                   index.html                        │
│              (View Layer - UI Structure)            │
└─────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────┐
│                   style.css                         │
│            (Presentation Layer - Styling)           │
└─────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────┐
│                JavaScript Modules                    │
├─────────────────────────────────────────────────────┤
│  script.js    → Main Controller & Event Handlers    │
│  auth.js      → Authentication & Authorization      │
│  quiz.js      → Quiz Logic & State Management       │
│  questions.js → Data Management (CRUD)              │
│  ui.js        → UI Rendering & DOM Manipulation     │
└─────────────────────────────────────────────────────┘
```

## Module Breakdown

### 1. script.js (Main Controller)
**Purpose:** Application entry point and event coordination

**Responsibilities:**
- Initialize event listeners
- Coordinate between modules
- Handle user interactions
- Route actions to appropriate modules

**Key Functions:**
- Login form submission
- Admin form submission
- Logout handler
- Quiz submission
- Retake quiz

### 2. auth.js (Authentication Module)
**Purpose:** User authentication and session management

**Responsibilities:**
- Validate user credentials
- Manage current user session
- Role-based access control
- Login/logout operations

**Key Functions:**
```javascript
handleLogin(username, password)    // Authenticate user
handleLogout()                     // Clear session
getCurrentUser()                   // Get logged-in user
isAdmin()                          // Check admin role
isLoggedIn()                       // Check login status
```

**Data Structure:**
```javascript
users = {
    admin: { password: 'admin123', role: 'admin' },
    user: { password: 'user123', role: 'user' }
}
```

### 3. questions.js (Data Module)
**Purpose:** Manage quiz questions and CRUD operations

**Responsibilities:**
- Store questions data
- Provide data access methods
- Add/delete/update questions
- Data validation

**Key Functions:**
```javascript
getAllQuestions()                  // Get all questions
getQuestion(index)                 // Get specific question
getTotalQuestions()                // Get count
addQuestion(questionData)          // Add new question
deleteQuestion(index)              // Remove question
updateQuestion(index, data)        // Update question
```

**Data Structure:**
```javascript
question = {
    question: "Question text?",
    options: ["Option 1", "Option 2", "Option 3", "Option 4"],
    answer: 0  // Index of correct option (0-3)
}
```

### 4. quiz.js (Business Logic Module)
**Purpose:** Handle quiz flow and scoring logic

**Responsibilities:**
- Track quiz progress
- Manage user answers
- Calculate scores
- Determine quiz completion

**Key Functions:**
```javascript
initQuiz()                         // Initialize quiz state
getCurrentQuestionIndex()          // Get current position
nextQuestion()                     // Move to next
isQuizComplete()                   // Check completion
saveAnswer(qIndex, aIndex)         // Save user answer
checkAnswer(qIndex, aIndex)        // Validate answer
incrementScore()                   // Update score
getQuizStats()                     // Get final statistics
```

**State Management:**
```javascript
currentQuestionIndex = 0           // Current question
userAnswers = []                   // User's answers
score = 0                          // Current score
```

### 5. ui.js (Presentation Module)
**Purpose:** Handle all DOM manipulations and UI updates

**Responsibilities:**
- Render UI components
- Update DOM elements
- Handle page transitions
- Display visual feedback

**Key Functions:**
```javascript
showPage(pageId)                   // Switch pages
displayQuestion()                  // Render question
handleOptionClick(index, element)  // Handle selection
displayResult()                    // Show results
loadAdminQuestions()               // Render admin panel
updateProgressBar()                // Update progress
```

## Data Flow

### User Login Flow
```
1. User enters credentials
   ↓
2. script.js captures form submit
   ↓
3. auth.js validates credentials
   ↓
4. auth.js sets currentUser
   ↓
5. ui.js shows appropriate page (admin/quiz)
```

### Quiz Taking Flow
```
1. User clicks option
   ↓
2. ui.js handles click event
   ↓
3. quiz.js saves answer
   ↓
4. quiz.js checks if correct
   ↓
5. ui.js shows color feedback (green/red)
   ↓
6. quiz.js increments score if correct
   ↓
7. ui.js auto-advances to next question
   ↓
8. Repeat until quiz complete
```

### Admin Question Management Flow
```
1. Admin fills question form
   ↓
2. script.js captures form submit
   ↓
3. questions.js adds new question
   ↓
4. ui.js refreshes question list
   ↓
5. Admin sees updated list
```

## Design Patterns Used

### 1. Module Pattern
Each JavaScript file acts as a module with specific responsibilities.

### 2. Separation of Concerns
- **Data** (questions.js)
- **Logic** (quiz.js, auth.js)
- **Presentation** (ui.js)
- **Control** (script.js)

### 3. Event-Driven Architecture
User interactions trigger events that flow through the system.

### 4. State Management
Centralized state in respective modules (quiz state, auth state, data state).

## Page Structure

### 1. Login Page
- Username input
- Password input
- Login button
- Credential hints

### 2. Admin Page
- Header with logout
- Add question form
- Questions list with delete option

### 3. Quiz Page
- Header with user info
- Progress bar
- Question counter
- Question text
- 4 option buttons
- Submit button

### 4. Result Page
- Score circle with percentage
- Score text
- Performance message
- Detailed statistics
- Retake and logout buttons

## Styling Architecture

### CSS Organization
```css
/* Global Styles */
- Reset and base styles
- Typography
- Color variables (via gradients)

/* Layout Components */
- Page containers
- Flexbox layouts
- Grid systems

/* UI Components */
- Buttons
- Forms
- Cards
- Progress bars

/* State Styles */
- Hover effects
- Active states
- Disabled states
- Color feedback (correct/wrong)

/* Animations */
- Transitions
- Keyframe animations
- Transform effects

/* Responsive Design */
- Media queries
- Mobile-first approach
```

## Security Considerations

### Current Implementation (Frontend Only)
- Credentials stored in JavaScript (not secure)
- No encryption
- No backend validation
- Session stored in memory only

### Production Recommendations
1. Implement backend API
2. Use JWT tokens for authentication
3. Hash passwords (bcrypt)
4. HTTPS only
5. CSRF protection
6. Rate limiting
7. Input sanitization
8. SQL injection prevention (if using database)

## Performance Optimizations

1. **Minimal DOM Manipulation**
   - Batch updates where possible
   - Use event delegation

2. **Efficient Rendering**
   - Only update changed elements
   - Use CSS for animations (GPU accelerated)

3. **Code Organization**
   - Modular structure for easy maintenance
   - Reusable functions

4. **No External Dependencies**
   - Faster load times
   - No bundle size concerns

## Scalability Considerations

### Current Limitations
- Questions stored in memory (lost on refresh)
- No persistent storage
- Limited to single user session
- No concurrent user support

### Future Scalability
1. **Backend Integration**
   - REST API or GraphQL
   - Database for questions and users
   - Session management

2. **State Management**
   - Consider Redux or similar for complex state
   - LocalStorage for persistence

3. **Component Framework**
   - React/Vue for larger scale
   - Better state management
   - Reusable components

## Testing Strategy

### Manual Testing Checklist
- [ ] Login with valid credentials
- [ ] Login with invalid credentials
- [ ] Admin can add questions
- [ ] Admin can delete questions
- [ ] User can take quiz
- [ ] Correct answers show green
- [ ] Wrong answers show red
- [ ] Score calculates correctly
- [ ] Progress bar updates
- [ ] Result page displays correctly
- [ ] Retake quiz works
- [ ] Logout works from all pages

### Recommended Automated Testing
1. **Unit Tests** (Jest)
   - Test each module function
   - Test data validation
   - Test calculations

2. **Integration Tests**
   - Test module interactions
   - Test data flow

3. **E2E Tests** (Cypress/Playwright)
   - Test complete user flows
   - Test UI interactions

## Browser Compatibility

**Supported Browsers:**
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

**Features Used:**
- ES6+ JavaScript
- CSS Grid
- Flexbox
- CSS Animations
- Arrow functions
- Template literals
- Spread operator

## Deployment

### Static Hosting Options
1. **GitHub Pages** - Free, easy setup
2. **Netlify** - Free tier, continuous deployment
3. **Vercel** - Free tier, optimized for frontend
4. **AWS S3** - Scalable, pay-as-you-go

### Deployment Steps
1. Push code to Git repository
2. Connect to hosting platform
3. Configure build settings (none needed for this project)
4. Deploy
5. Access via provided URL

---

This architecture provides a solid foundation for a quiz application while maintaining simplicity and clarity for educational purposes.
