# Wellness Messaging Channel Transformation

## Overview

This document outlines the transformation of the existing `floatingChatbot` LWC into a proper messaging channel LWC bundle, following the three Salesforce articles on messaging channel customization.

## Transformation Summary

### From: `floatingChatbot` (Standalone LWC)
- **Purpose**: General floating chatbot with AgentForce integration
- **Architecture**: Custom LWC with local message handling
- **Features**: Quick questions, typing indicators, custom styling
- **Integration**: Placeholder for messaging channel (not fully implemented)

### To: `wellnessMessagingChannel` (Messaging Channel LWC Bundle)
- **Purpose**: Wellness-focused messaging channel with pre-chat form
- **Architecture**: Messaging channel LWC with real-time integration
- **Features**: Pre-chat form, rich message bubbles, session management
- **Integration**: Full messaging channel APIs + AgentForce backend

## New Bundle Structure

### 1. `wellnessMessagingChannel` (Main LWC)
**Location**: `force-app/main/default/lwc/wellnessMessagingChannel/`

**Key Features**:
- Extends messaging channel functionality
- Pre-chat form for wellness preferences
- Real-time message handling
- Session management
- AgentForce integration

**Configuration Properties**:
- `messagingChannelId` (required): Your messaging channel ID
- `deploymentId` (required): Your messaging deployment ID
- `agentId` (optional): AgentForce agent ID for backend processing

### 2. `wellnessMessageBubble` (Custom Message Component)
**Location**: `force-app/main/default/lwc/wellnessMessageBubble/`

**Message Types Supported**:
- **Text Messages**: Standard text with wellness theming
- **Product Messages**: Rich product cards with images, ratings, pricing
- **Consultation Messages**: Booking cards for wellness consultations
- **Quick Reply Messages**: Interactive option buttons

### 3. `ChatbotMessagingService` (Apex Service)
**Location**: `force-app/main/default/classes/ChatbotMessagingService.cls`

**Key Methods**:
- `initializeSession()`: Create messaging sessions
- `sendMessage()`: Send messages through channel
- `getSessionMessages()`: Retrieve session history
- `endSession()`: Close messaging sessions

## Key Transformations

### 1. Architecture Change
```javascript
// Before: Standalone chatbot
export default class FloatingChatbot extends NavigationMixin(LightningElement)

// After: Messaging channel integration
export default class WellnessMessagingChannel extends NavigationMixin(LightningElement)
// + Message channel subscription
// + Session management
// + Real-time messaging
```

### 2. Pre-chat Form Integration
```html
<!-- New pre-chat form with wellness focus -->
<div class="pre-chat-form">
    <form onsubmit={handlePreChatSubmit} class="wellness-form">
        <!-- Name, Email, Skin Type, Health Goals, Current Products -->
    </form>
</div>
```

### 3. Rich Message Bubbles
```javascript
// New message types with rich content
const messageTypes = {
    text: 'Standard text messages',
    product: 'Product recommendation cards',
    consultation: 'Consultation booking cards',
    'quick-reply': 'Interactive option buttons'
};
```

### 4. Real-time Messaging
```javascript
// Message channel subscription
subscribeToMessageChannel() {
    this.subscription = subscribe(
        this.messageContext,
        CHAT_MESSAGE_CHANNEL,
        (message) => this.handleMessageChannelUpdate(message)
    );
}
```

## Deployment Instructions

### 1. Deploy the Bundle
```bash
# Run the deployment script
chmod +x scripts/deploy-wellness-messaging-channel.sh
./scripts/deploy-wellness-messaging-channel.sh
```

### 2. Configure Messaging Channel
1. Go to **Setup > Messaging Channels**
2. Create or configure your messaging channel
3. Note the **Channel ID** and **Deployment ID**

### 3. Update LWC Configuration
```javascript
// In your page/component where wellnessMessagingChannel is used
<c-wellness-messaging-channel
    messaging-channel-id="your_channel_id"
    deployment-id="your_deployment_id"
    agent-id="your_agentforce_agent_id">
</c-wellness-messaging-channel>
```

## Features Comparison

| Feature | Old floatingChatbot | New wellnessMessagingChannel |
|---------|-------------------|------------------------------|
| **Architecture** | Standalone LWC | Messaging Channel LWC |
| **Message Handling** | Local storage | Real-time messaging |
| **Session Management** | None | Full session lifecycle |
| **Pre-chat Form** | None | Wellness-focused form |
| **Message Types** | Text only | Rich bubbles (text, product, consultation, quick-reply) |
| **Styling** | Basic wellness theme | Advanced wellness theming |
| **Integration** | AgentForce placeholder | Full AgentForce + Messaging APIs |
| **Responsive Design** | Basic | Enhanced mobile support |

## Wellness-Specific Enhancements

### 1. Pre-chat Form Fields
- **Name & Email**: Basic contact information
- **Skin Type**: Dry, Oily, Combination, Normal, Sensitive
- **Health Goals**: Anti-aging, Hydration, Acne treatment, etc.
- **Current Products**: Optional text area for existing routine

### 2. Message Types
- **Product Recommendations**: Rich cards with images, ratings, pricing
- **Consultation Booking**: Interactive booking cards
- **Quick Replies**: Wellness-focused question buttons
- **Text Messages**: Standard wellness-themed bubbles

### 3. Wellness Theme Colors
```css
--wellness-primary: #4CAF50;
--wellness-secondary: #45a049;
--wellness-accent: #8BC34A;
--wellness-light: #C8E6C9;
--wellness-dark: #2E7D32;
```

## Integration Points

### 1. AgentForce Integration
```javascript
// Maintains existing AgentForce integration
async getAgentForceResponse(userInput) {
    // Call AgentForceService for responses
    return await AgentForceService.getResponse(userInput);
}
```

### 2. Messaging Channel APIs
```javascript
// New messaging channel integration
await this.callApexMethod('ChatbotMessagingService.sendMessage', {
    sessionId: this.sessionId,
    messageText: messageText,
    isFromBot: false
});
```

### 3. Real-time Updates
```javascript
// Subscribe to messaging channel updates
handleMessageChannelUpdate(message) {
    if (message.type === 'BOT_RESPONSE') {
        this.addBotMessage(message.text);
    }
}
```

## Testing Checklist

### Pre-deployment
- [ ] Messaging channel configured in Salesforce
- [ ] Channel ID and Deployment ID available
- [ ] AgentForce agent ID configured (if using)

### Post-deployment
- [ ] Pre-chat form displays correctly
- [ ] Form data collection works
- [ ] Messaging session initialization
- [ ] Real-time message sending/receiving
- [ ] Rich message bubbles display properly
- [ ] AgentForce integration functional
- [ ] Session persistence across page reloads
- [ ] Mobile responsive design

## Migration Guide

### From floatingChatbot to wellnessMessagingChannel

1. **Replace Component Usage**:
```html
<!-- Old -->
<c-floating-chatbot></c-floating-chatbot>

<!-- New -->
<c-wellness-messaging-channel
    messaging-channel-id="your_channel_id"
    deployment-id="your_deployment_id">
</c-wellness-messaging-channel>
```

2. **Update Configuration**:
   - Add messaging channel configuration
   - Configure pre-chat form fields as needed
   - Test AgentForce integration

3. **Customize Styling**:
   - Update wellness theme colors if needed
   - Modify pre-chat form fields
   - Customize message bubble styles

## Troubleshooting

### Common Issues

1. **Messaging Channel Not Found**
   - Verify channel ID and deployment ID
   - Check messaging channel configuration in Setup

2. **Pre-chat Form Not Displaying**
   - Check `showPreChatForm` property
   - Verify form validation

3. **Messages Not Sending**
   - Check session initialization
   - Verify AgentForce integration
   - Check browser console for errors

4. **Rich Messages Not Rendering**
   - Verify message type configuration
   - Check wellnessMessageBubble component deployment

## References

- [Customize Text Message Bubble](https://developer.salesforce.com/docs/service/messaging-web/guide/customize-text-message-bubble.html)
- [Create Custom Pre-chat Form](https://developer.salesforce.com/docs/service/messaging-web/guide/create-custom-pre-chat-form.html)
- [Send Message LWC](https://developer.salesforce.com/docs/service/messaging-web/guide/send-message-lwc.html)

## Support

For issues or questions about the transformation:
1. Check the troubleshooting section above
2. Review the three Salesforce articles referenced
3. Test with the deployment script provided
4. Verify messaging channel configuration in your org 