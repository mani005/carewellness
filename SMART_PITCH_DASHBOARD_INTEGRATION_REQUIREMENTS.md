# Smart Pitch Dashboard Integration Requirements

## Project Overview
Connect two separate Salesforce components into cohesive sales functionality:
- **FastAgentExecutor.cls** - AI orchestration engine
- **smartPitchDashboard** Lightning Web Component (LWC) - **ORIGINAL VERSION**
- **wellnessPitchGenerator** Lightning Web Component (LWC) - **NEW INTEGRATED VERSION**

## Business Requirements
Sales representatives need to access a dedicated LWC tab on Service Appointment records to generate AI-powered wellness sales pitches using:
1. Stored agentbot interaction responses (JSON)
2. Voice call transcripts from child records
3. Custom context input from sales reps

## Technical Architecture

### Core Components

#### 1. FastAgentExecutor.cls
- **Purpose**: AI orchestration engine for sales intelligence
- **Location**: `force-app/main/default/classes/FastAgentExecutor.cls`
- **Key Features**:
  - Parallel agent execution
  - Intent classification
  - Agent timeout management
  - Result aggregation
  - Sales brief generation

#### 2. smartPitchDashboard LWC (ORIGINAL)
- **Purpose**: Original sales pitch dashboard functionality
- **Location**: `force-app/main/default/lwc/smartPitchDashboard/`
- **Status**: ✅ **PRESERVED** - Original version maintained
- **Files**:
  - `smartPitchDashboard.js` - Original JavaScript logic
  - `smartPitchDashboard.html` - Original HTML template
  - `smartPitchDashboard.css` - Original styling
  - `smartPitchDashboard.js-meta.xml` - Original metadata

#### 3. wellnessPitchGenerator LWC (NEW INTEGRATED)
- **Purpose**: New integrated AI-powered wellness pitch generator
- **Location**: `force-app/main/default/lwc/wellnessPitchGenerator/`
- **Status**: ✅ **COMPLETED** - New integrated version created
- **Files**:
  - `wellnessPitchGenerator.js` - Integrated JavaScript logic
  - `wellnessPitchGenerator.html` - Modern HTML template
  - `wellnessPitchGenerator.css` - Enhanced styling
  - `wellnessPitchGenerator.js-meta.xml` - New metadata

### Data Objects

#### ServiceAppointment (Primary Record)
- **Fields Used**:
  - `Subject` - Appointment title
  - `Description` - General description
  - `AdditionalInformation` - Additional context
  - `Comments` - Notes and comments
  - `SchedStartTime` - Appointment start time
  - `SchedEndTime` - Appointment end time
  - `Duration` - Appointment length
  - `Status` - Current status
  - `ContactId` - Related contact
  - `AccountId` - Related account
  - `AppointmentType` - Type of appointment
  - `AppointmentMode` - Mode (in-person/virtual)
  - `OwnerId` - Assigned user

#### VoiceCall (Child Records)
- **Purpose**: Store voice transcripts for context
- **Key Fields**:
  - `Description` - Voice transcript content
  - `RelatedRecordId` - Link to ServiceAppointment

#### Contact & Account
- **Purpose**: Customer context and company information
- **Fields Used**:
  - Contact: Name, Email, Phone
  - Account: Name, Industry, Type

## Functional Requirements

### 1. Data Loading
- ✅ **COMPLETED**: Load ServiceAppointment record details
- ✅ **COMPLETED**: Load related Contact and Account information
- ⚠️ **PENDING**: Query actual VoiceCall child records (currently using mock data)

### 2. AI Context Building
- ✅ **COMPLETED**: Extract and parse existing AI data from comments/additional info
- ✅ **COMPLETED**: Combine voice call insights
- ✅ **COMPLETED**: Include custom prompt input from sales rep
- ✅ **COMPLETED**: Build comprehensive context for AI processing

### 3. AI Integration
- ✅ **COMPLETED**: Simulate FastAgentExecutor call (UI ready)
- ⚠️ **PENDING**: Implement actual Apex method call to FastAgentExecutor

### 4. Output Display
- ✅ **COMPLETED**: Sales pitch section
- ✅ **COMPLETED**: Customer insights section
- ✅ **COMPLETED**: Product bundle section
- ✅ **COMPLETED**: Voice transcript viewer modal

### 5. User Actions
- ✅ **COMPLETED**: Generate Wellness Pitch button
- ✅ **COMPLETED**: View voice transcripts
- ⚠️ **PENDING**: Add to Notes functionality
- ⚠️ **PENDING**: Send Email functionality

## Technical Implementation

### Files Created/Modified

#### 1. wellnessPitchGenerator.js (NEW)
- **Status**: ✅ **COMPLETED**
- **Key Features**:
  - `@api` properties for record integration
  - `@wire` decorators for data retrieval
  - State management with `@track` properties
  - Mock voice call data (ready for real implementation)
  - AI context building logic
  - UI interaction handlers

#### 2. wellnessPitchGenerator.html (NEW)
- **Status**: ✅ **COMPLETED**
- **Key Features**:
  - Responsive layout with context cards
  - Input fields for custom prompts
  - Sections for AI-generated content
  - Voice transcript modal
  - Loading states and error handling

#### 3. wellnessPitchGenerator.css (NEW)
- **Status**: ✅ **COMPLETED**
- **Key Features**:
  - Modern gradient design
  - Responsive grid layouts
  - Hover effects and animations
  - Mobile-friendly design
  - Salesforce Lightning Design System compliance

#### 4. wellnessPitchGenerator.js-meta.xml (NEW)
- **Status**: ✅ **COMPLETED**
- **Key Features**:
  - API version 58.0
  - Exposed for multiple targets
  - Configured for ServiceAppointment record pages

### Integration Points

#### Data Flow
1. LWC loads → Retrieves ServiceAppointment record
2. LWC loads → Retrieves related Contact/Account data
3. LWC loads → Queries VoiceCall child records (pending)
4. User inputs custom context → Combines with existing data
5. Generate button clicked → Calls FastAgentExecutor (pending)
6. AI returns results → Displays in three sections

#### API Calls
- **Current**: Mock implementation for demonstration
- **Target**: Real Apex method calls to FastAgentExecutor

## Component Bifurcation Status

### ✅ Original smartPitchDashboard (PRESERVED)
- **Location**: `force-app/main/default/lwc/smartPitchDashboard/`
- **Status**: Unchanged, original functionality maintained
- **Purpose**: Legacy sales pitch functionality

### ✅ New wellnessPitchGenerator (COMPLETED)
- **Location**: `force-app/main/default/lwc/wellnessPitchGenerator/`
- **Status**: New integrated AI-powered component
- **Purpose**: Modern wellness pitch generation with AI integration

## Pending Tasks

### High Priority
1. **VoiceCall Query Implementation**
   - Replace mock data with real SOQL query
   - Implement relationship query between ServiceAppointment and VoiceCall

2. **FastAgentExecutor Integration**
   - Create Apex method for LWC to call
   - Pass constructed AI context to AI engine
   - Handle response parsing and display

### Medium Priority
3. **Custom JSON Field**
   - Consider creating dedicated field for agentbot responses
   - Evaluate if existing text fields are sufficient

4. **Action Implementations**
   - Add to Notes functionality
   - Send Email functionality

## Demo Readiness Status

### ✅ Ready for Demo
- Complete UI framework
- Data loading and display
- Context building logic
- Error handling and loading states
- Responsive design
- **Two separate LWC components available**

### ⚠️ Demo Limitations
- Mock voice call data
- Simulated AI responses
- Placeholder action buttons

## Technical Notes

### LWC Best Practices
- Uses `@wire` for reactive data loading
- Implements proper error handling
- Follows Salesforce Lightning Design System
- Responsive and accessible design

### Performance Considerations
- Lazy loading of voice transcripts
- Efficient data retrieval with field selection
- Minimal re-renders with proper state management

### Security
- Field-level security respected
- User context maintained
- Proper error message handling

## Future Enhancements

### Phase 2 Features
- Real-time AI processing
- Advanced voice analytics
- Integration with other AI services
- Enhanced product recommendations

### Phase 3 Features
- Machine learning insights
- Predictive sales analytics
- Multi-language support
- Mobile optimization

## Contact & Support
This integration connects Salesforce's AI capabilities with sales intelligence, enabling data-driven wellness sales pitches through a modern, responsive interface.

---
**Last Updated**: Current session
**Status**: Core UI Complete, AI Integration Pending
**Component Status**: ✅ Original preserved, ✅ New integrated version complete
**Next Milestone**: Real VoiceCall data and FastAgentExecutor integration 