# Premium Wellness Chatbot LWC - Deployment Guide

## 🌿 Overview

This is a comprehensive floating chatbot Lightning Web Component designed for premium wellness brands with Agentforce integration and progressive loading for 20-30 second response delays.

## ✨ Key Features

### 🎨 Premium Wellness Branding
- **Color Palette**: Eggshell white (#F5F5DC) and leaf green (#6A805D)
- **Typography**: Modern, clean design with wellness-focused messaging
- **Animations**: Smooth transitions and engaging micro-interactions

### 🤖 Agentforce Integration
- **Real-time AI**: Connects to Agentforce API for intelligent responses
- **Session Management**: Proper conversation handling and context preservation
- **Error Handling**: Graceful fallbacks and user-friendly error messages

### ⏱️ Progressive Loading System
- **20-30 Second Handling**: Escalating loading messages to maintain engagement
- **Wellness Tips Carousel**: Educational content during processing
- **Skeleton Loading**: Visual feedback with product preview placeholders
- **Progress Tracking**: Real-time progress bar with percentage completion

### 🎯 Enhanced UX Features
- **Pre-Chat Greeting**: Welcoming interface with suggested questions
- **Quick Questions**: One-click common wellness queries
- **Responsive Design**: Mobile-first approach with tablet/desktop optimization
- **Accessibility**: ARIA labels, keyboard navigation, screen reader support

## 🚀 Deployment Instructions

### 1. Prerequisites

```bash
# Ensure you have the latest Salesforce CLI
sfdx --version

# Authenticate to your org
sfdx auth:web:login -r https://login.salesforce.com
```

### 2. Deploy the Component

```bash
# Deploy the LWC component
sfdx force:source:deploy -p force-app/main/default/lwc/floatingChatbot/

# Deploy the AgentforceFlowController Apex class
sfdx force:source:deploy -p force-app/main/default/classes/AgentforceFlowController.cls

# Deploy the flows (if not already deployed)
sfdx force:source:deploy -p force-app/main/default/flows/Apex_Invoke.flow-meta.xml
sfdx force:source:deploy -p force-app/main/default/flows/SalesAIOrchestrator_Agentforce_Action.flow-meta.xml
```

### 3. Experience Site Configuration

#### A. Add Component to Experience Builder

1. **Navigate to Experience Builder**
   - Go to Setup → Digital Experiences → All Sites
   - Select your Experience Site
   - Click "Edit" to open Experience Builder

2. **Add the Chatbot Component**
   - Drag and drop the "Floating Chatbot" component
   - Position it on your desired page
   - Configure the component properties

#### B. Component Properties Configuration

```javascript
// Recommended Configuration
{
    "agentId": "0XxgK000000QAVBSA4",           // Your Agentforce Agent ID
    "enableAgentForce": true,                   // Enable AI integration
    "enableProgressiveLoading": true,           // Enable 20-30s delay handling
    "wellnessBrandName": "Premium Wellness AI"  // Customize brand name
}
```

### 4. Agentforce Setup

#### A. Verify Agent Configuration

```apex
// Check your Agentforce agent is properly configured
// Agent ID: 0XxgK000000QAVBSA4
// Ensure the agent is active and has proper permissions
```

#### B. Test Agentforce Integration

```javascript
// Test the integration via Developer Console
List<AgentforceFlowController.FlowInput> inputs = new List<AgentforceFlowController.FlowInput>();
AgentforceFlowController.FlowInput input = new AgentforceFlowController.FlowInput();
input.customerQuery = 'Hello, I need skincare recommendations';
input.customerId = 'test-user';
input.consultationContext = 'wellness_chat';
inputs.add(input);

List<String> responses = AgentforceFlowController.callAgentforceFlow(inputs);
System.debug('Agentforce Response: ' + responses[0]);
```

**Note**: The Apex class now handles both the flow invocation and response formatting in a single method.
```

## 🎨 Customization Guide

### Color Palette Customization

```css
/* Update CSS variables in floatingChatbot.css */
:root {
    --wellness-primary: #F5F5DC;    /* Eggshell White */
    --wellness-secondary: #6A805D;   /* Leaf Green */
    --wellness-text: #3C4A35;        /* Deep Forest */
    --wellness-accent: #8B9B7A;      /* Sage Green */
    --wellness-shadow: rgba(106, 128, 93, 0.15);
    --wellness-light: #F8FAF5;
    --wellness-border: #E2E8E0;
}
```

### Progressive Loading Messages

```javascript
// Customize loading stages in floatingChatbot.js
loadingStages = [
    { time: 0, message: "🌱 Understanding your wellness needs...", progress: 10 },
    { time: 5000, message: "📊 Analyzing our premium product database...", progress: 25 },
    { time: 10000, message: "🧠 AI crafting your personalized recommendations...", progress: 45 },
    { time: 15000, message: "✨ Finding your perfect product matches...", progress: 70 },
    { time: 20000, message: "🎉 Finalizing your complete wellness plan...", progress: 90 },
    { time: 25000, message: "Almost ready with your premium recommendations!", progress: 95 }
];
```

### Wellness Tips Customization

```javascript
// Update wellness tips in floatingChatbot.js
wellnessTipsList = [
    "💡 Tip: Always patch test new products first",
    "🌱 Fact: Your skin repairs itself while you sleep",
    "✨ Insight: Vitamin C works best in morning routines",
    "🌿 Wellness: Hydration is key to glowing skin",
    "💎 Premium: Our products are dermatologist-tested",
    "🌟 Expert: Personalized care for your unique needs"
];
```

## 🔧 Advanced Configuration

### A. Multi-Language Support

```javascript
// Add language detection and localization
const languageConfig = {
    'en_US': {
        welcomeMessage: "🌿 Welcome to Premium Wellness AI!",
        quickQuestions: ["I have oily skin and breakouts", "Recommend anti-aging products"]
    },
    'es_MX': {
        welcomeMessage: "🌿 ¡Bienvenido a Premium Wellness AI!",
        quickQuestions: ["Tengo piel grasa y brotes", "Recomendar productos anti-envejecimiento"]
    }
};
```

### B. Analytics Integration

```javascript
// Add analytics tracking
trackEvent(eventName, data) {
    // Google Analytics 4
    if (typeof gtag !== 'undefined') {
        gtag('event', eventName, data);
    }
    
    // Salesforce Analytics
    if (typeof analytics !== 'undefined') {
        analytics.track(eventName, data);
    }
}
```

### C. Guest User Handling

```javascript
// Handle guest vs authenticated users
handleUserContext() {
    const isGuest = !this.sessionId;
    if (isGuest) {
        // Show limited features for guests
        this.showGuestMode = true;
    } else {
        // Show full features for authenticated users
        this.showFullFeatures = true;
    }
}
```

## 🧪 Testing Guide

### 1. Unit Tests

```bash
# Run Apex tests
sfdx force:apex:test:run -n AgentforceServiceTest -r human

# Run LWC tests
sfdx force:lightning:lwc:test:setup
sfdx force:lightning:lwc:test:run
```

### 2. Integration Tests

```javascript
// Test Agentforce integration
async testAgentforceIntegration() {
    const testMessage = "I need skincare recommendations for sensitive skin";
    const response = await getAgentforceResponse({ userMessage: testMessage });
    
    console.assert(response && response.length > 0, 'Agentforce response received');
    console.assert(response.includes('skincare') || response.includes('product'), 'Response is relevant');
}
```

### 3. User Experience Tests

```javascript
// Test progressive loading
async testProgressiveLoading() {
    this.enableProgressiveLoading = true;
    await this.sendMessageToAgentforce("Test message");
    
    // Verify loading stages progress
    console.assert(this.isProcessing === true, 'Processing started');
    console.assert(this.loadingProgress > 0, 'Progress bar advancing');
}
```

## 📊 Performance Optimization

### 1. Lazy Loading

```javascript
// Implement lazy loading for chat history
async loadChatHistory() {
    if (this.messages.length > 50) {
        // Load older messages on demand
        const olderMessages = await this.fetchOlderMessages();
        this.messages = [...olderMessages, ...this.messages];
    }
}
```

### 2. Message Caching

```javascript
// Cache common responses
const responseCache = new Map();

async getCachedResponse(userInput) {
    const cacheKey = userInput.toLowerCase().trim();
    if (responseCache.has(cacheKey)) {
        return responseCache.get(cacheKey);
    }
    
    const response = await this.getAgentforceResponse(userInput);
    responseCache.set(cacheKey, response);
    return response;
}
```

### 3. Debounced Input

```javascript
// Implement debounced typing indicator
handleInputChange(event) {
    this.inputText = event.target.value;
    
    // Debounce typing indicator
    clearTimeout(this.typingTimeout);
    this.typingTimeout = setTimeout(() => {
        this.showTypingIndicator();
    }, 500);
}
```

## 🚨 Troubleshooting

### Common Issues

#### 1. Agentforce Connection Errors

```javascript
// Check authentication
if (!accessToken) {
    console.error('Agentforce authentication failed');
    this.showFallbackResponse();
}
```

#### 2. Progressive Loading Not Working

```javascript
// Verify progressive loading is enabled
if (!this.enableProgressiveLoading) {
    console.warn('Progressive loading is disabled');
    this.enableProgressiveLoading = true;
}
```

#### 3. Mobile Responsiveness Issues

```css
/* Ensure mobile responsiveness */
@media (max-width: 768px) {
    .chat-window {
        width: calc(100vw - 32px);
        height: 70vh;
    }
}
```

### Debug Mode

```javascript
// Enable debug mode for troubleshooting
@api debugMode = false;

connectedCallback() {
    if (this.debugMode) {
        console.log('Premium Wellness Chatbot Debug Mode Enabled');
        console.log('Agent ID:', this.agentId);
        console.log('Agentforce Enabled:', this.enableAgentForce);
        console.log('Progressive Loading:', this.enableProgressiveLoading);
    }
}
```

## 📈 Analytics & Monitoring

### 1. Usage Analytics

```javascript
// Track chatbot usage
trackChatbotUsage(action, data) {
    const analyticsData = {
        action: action,
        timestamp: new Date().toISOString(),
        sessionId: this.sessionId,
        userType: this.isGuest ? 'guest' : 'authenticated',
        ...data
    };
    
    // Send to analytics service
    this.sendAnalytics(analyticsData);
}
```

### 2. Performance Monitoring

```javascript
// Monitor response times
async trackResponseTime(startTime) {
    const responseTime = Date.now() - startTime;
    
    if (responseTime > 30000) {
        console.warn('Slow response time detected:', responseTime + 'ms');
        this.trackPerformanceIssue('slow_response', responseTime);
    }
}
```

## 🔒 Security Considerations

### 1. Input Sanitization

```javascript
// Sanitize user input
sanitizeInput(input) {
    return input.replace(/<script\b[^<]*(?:(?!<\/script>)<[^<]*)*<\/script>/gi, '');
}
```

### 2. Rate Limiting

```javascript
// Implement rate limiting
const rateLimiter = {
    maxRequests: 10,
    timeWindow: 60000, // 1 minute
    requests: new Map()
};

checkRateLimit(userId) {
    const now = Date.now();
    const userRequests = rateLimiter.requests.get(userId) || [];
    
    // Remove old requests
    const recentRequests = userRequests.filter(time => now - time < rateLimiter.timeWindow);
    
    if (recentRequests.length >= rateLimiter.maxRequests) {
        return false; // Rate limit exceeded
    }
    
    recentRequests.push(now);
    rateLimiter.requests.set(userId, recentRequests);
    return true;
}
```

## 🎯 Best Practices

### 1. User Experience

- **Progressive Enhancement**: Ensure functionality works without JavaScript
- **Accessibility**: Follow WCAG 2.1 guidelines
- **Performance**: Optimize for Core Web Vitals
- **Mobile First**: Design for mobile devices first

### 2. Code Quality

- **Error Handling**: Implement comprehensive error handling
- **Logging**: Add structured logging for debugging
- **Testing**: Maintain high test coverage
- **Documentation**: Keep documentation up to date

### 3. Security

- **Input Validation**: Validate all user inputs
- **Output Encoding**: Encode all outputs to prevent XSS
- **Authentication**: Verify user permissions
- **Data Protection**: Follow data privacy regulations

## 📞 Support

For technical support or questions about this implementation:

1. **Documentation**: Check this deployment guide
2. **Issues**: Create an issue in the repository
3. **Community**: Post questions in Salesforce Developer Community
4. **Professional Services**: Contact for custom implementations

---

**Version**: 1.0.0  
**Last Updated**: January 2024  
**Compatibility**: Salesforce API 58.0+  
**License**: MIT License 