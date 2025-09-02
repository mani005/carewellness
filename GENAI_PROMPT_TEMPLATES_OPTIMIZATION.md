# GenAI Prompt Templates Optimization for Wellness Sales Curator Agent Bot

## Overview
This document outlines the optimized prompt templates for a fast-responding wellness sales curator agent bot. Each template is designed to work with specific data retrievers and produce structured JSON outputs that feed into the next step of the orchestration flow.

## Architecture Decision: Input vs Embedded Retrievers

### **Input Retrievers (Recommended for Dynamic Context)**
- **CustomerIntentDecoder**: Uses input retriever for flexibility in knowledge article searches
- **ConsultationBookingAgent**: Uses input retriever for dynamic consultation context
- **SalesPitchGenerator**: Uses input retriever for flexible sales strategy generation

### **Embedded Retrievers (Recommended for Static Data)**
- **CustomerIntelligenceEngine**: Uses embedded retrievers for consistent customer data access
- **SmartProductFinder**: Uses embedded retrievers for product catalog access
- **BrowSingProm**: Uses embedded retriever for browsing behavior analysis
- **TrendingProductsFallback**: Uses embedded retrievers for trending data access

## 1. CustomerIntentDecoder

### Purpose
Converts customer queries into structured product search intent and urgency signals using knowledge articles.

### Input Retriever
- **Retriever ID**: `Default_FileUDMO_SI_Retriever_1Cx_9grebdbd890` (Knowledge Articles)
- **Input**: Customer query + Customer ID (optional)

### Updated Prompt
```xml
SYSTEM INSTRUCTIONS:
You are an AI assistant that converts customer queries into structured product search intent and urgency signals.

DATA SOURCE:
Use results from "Default_FileUDMO_SI_Retriever_1Cx_9grebdbd890" to map customer problems to product categories.

INPUTS:
- user_query = {!$Input:Query}  // Customer's natural language inquiry
- customer_id = {!$User.AboutMe} // Optional customer ID for context

TASKS:
1. Analyze the customer query for:
   • Health/beauty/wellness concerns (acne, dry skin, aging, immunity, vitamin, etc.)
   • Urgency indicators (need now, urgent, ASAP, etc.)
   • Budget signals (affordable, premium, expensive, cheap)
   • Preference indicators (natural, organic, clinical, gentle)
2. Search Knowledge Articles {!$EinsteinSearch:Default_FileUDMO_SI_Retriever_1Cx_9grebdbd890.results} for matching content
3. Extract relevant product keywords and categories
4. Determine intent confidence and urgency level
5. Identify consultation indicators (complex concerns, multiple issues)
6. FALLBACK HANDLING: If no keywords found or low confidence (<0.5), set fallbackTriggered = true

OUTPUT (return exactly this JSON schema):
{
  "intent": {
    "primaryConcern": "acne | aging | dryness | sensitivity | other",
    "secondaryConcerns": ["concern1", "concern2"],
    "confidenceScore": 0.95
  },
  "urgency": {
    "level": "low | medium | high | urgent",
    "indicators": ["need_now", "event_driven"],
    "timeframe": "immediate | week | month | flexible"
  },
  "budget": {
    "signals": ["affordable", "premium", "value"],
    "priceRange": "budget | mid | premium | luxury"
  },
  "productKeywords": ["salicylic_acid", "moisturizer", "serum"],
  "categories": ["acne_treatment", "moisturizers"],
  "consultationNeeded": true,
  "consultationReason": "Complex skin concerns requiring expert advice",
  "nextAction": "product_search | consultation_offer | clarification_needed",
  "fallbackTriggered": false,
  "fallbackReason": "no_keywords_found | low_confidence | no_articles_match"
}
```

### Apex Input/Output
```apex
// Input
String customerQuery = "I have acne and need something that works fast";
String customerId = "CUST-12345";

// Expected Output JSON
{
  "intent": {
    "primaryConcern": "acne",
    "secondaryConcerns": ["urgency"],
    "confidenceScore": 0.92
  },
  "urgency": {
    "level": "high",
    "indicators": ["need_now", "fast_results"],
    "timeframe": "immediate"
  },
  "budget": {
    "signals": ["value"],
    "priceRange": "mid"
  },
  "productKeywords": ["salicylic_acid", "benzoyl_peroxide", "acne_treatment"],
  "categories": ["acne_treatment", "cleansers"],
  "consultationNeeded": false,
  "consultationReason": "",
  "nextAction": "product_search",
  "fallbackTriggered": false,
  "fallbackReason": ""
}
```

## 2. CustomerIntelligenceEngine

### Purpose
Creates comprehensive customer profiles for personalized sales strategies using customer data, purchase history, and browsing behavior.

### Embedded Retrievers
- **Customer**: `Default_Customers_Retriever_1Cx_9gr612ab6c0`
- **Purchase History**: `Default_Purchase_History_Retriever_1Cx_9grbb5cc572`
- **Browsing Behavior**: `Default_Browsing_Behavior_Retriever_1Cx_9gr0eb14871`

### Updated Prompt
```xml
SYSTEM INSTRUCTIONS:
You are an AI assistant that creates comprehensive customer profiles for personalized sales strategies.

DATA SOURCES:
Use "Default_Customers_Retriever_1Cx_9gr612ab6c0", "Default_Purchase_History_Retriever_1Cx_9grbb5cc572", and "Default_Browsing_Behavior_Retriever_1Cx_9gr0eb14871".

INPUTS:
- customer_id = {!$Input:Query}  // Customer identifier
- interaction_context = Current conversation context

TASKS:
1. Retrieve customer demographics from Customer_Data {!$EinsteinSearch:Default_Customers_Retriever_1Cx_9gr612ab6c0.results}
2. Analyze purchase patterns from Purchase_History {!$EinsteinSearch:Default_Purchase_History_Retriever_1Cx_9grbb5cc572.results}
   • Calculate lifetime value
   • Identify seasonal patterns
   • Determine product preferences
   • Assess price sensitivity
3. Evaluate browsing behavior from Browsing_Behavior {!$EinsteinSearch:Default_Browsing_Behavior_Retriever_1Cx_9gr0eb14871.results}
   • Engagement levels
   • Interest categories
   • Shopping behavior patterns
4. Create customer segment and buying persona
5. Calculate sales opportunity score
6. FALLBACK HANDLING: If customer not found (guest user), return guest_user profile with demographic defaults

OUTPUT (return exactly this JSON schema):
{
  "customerProfile": {
    "customerId": "CUST-12345",
    "name": "Sarah Johnson",
    "segment": "premium_conscious_professional",
    "demographics": {
      "age": 28,
      "region": "North America",
      "preferredContact": "email"
    }
  },
  "purchaseIntelligence": {
    "lifetimeValue": 342.50,
    "averageOrderValue": 67.99,
    "purchaseFrequency": "quarterly",
    "seasonalPatterns": ["spring_skincare", "winter_moisturizing"],
    "priceSensitivity": "medium",
    "preferredCategories": ["acne_treatment", "moisturizers"]
  },
  "behaviorInsights": {
    "engagementLevel": "high",
    "browsingStyle": "research_heavy",
    "decisionSpeed": "deliberate",
    "channelPreference": "online_with_consultation"
  },
  "salesOpportunity": {
    "opportunityScore": 8.7,
    "buyingReadiness": "high",
    "recommendedApproach": "value_demonstration",
    "optimalTiming": "immediate",
    "expectedSpend": 127.50
  },
  "personalizationFactors": {
    "communicationStyle": "professional_detailed",
    "valueDrivers": ["quality", "results", "convenience"],
    "objectionPatterns": ["price_justification", "ingredient_safety"],
    "motivationTriggers": ["expert_recommendation", "proven_results"]
  },
  "isGuestUser": false,
  "guestUserDefaults": {
    "segment": "first_time_visitor",
    "assumedAge": 25,
    "assumedBudget": "mid_range",
    "communicationStyle": "friendly_informative"
  }
}
```

### Apex Input/Output
```apex
// Input
String customerId = "CUST-12345";

// Expected Output JSON
{
  "customerProfile": {
    "customerId": "CUST-12345",
    "name": "Sarah Johnson",
    "segment": "premium_conscious_professional",
    "demographics": {
      "age": 28,
      "region": "North America",
      "preferredContact": "email"
    }
  },
  "purchaseIntelligence": {
    "lifetimeValue": 342.50,
    "averageOrderValue": 67.99,
    "purchaseFrequency": "quarterly",
    "seasonalPatterns": ["spring_skincare", "winter_moisturizing"],
    "priceSensitivity": "medium",
    "preferredCategories": ["acne_treatment", "moisturizers"]
  },
  "behaviorInsights": {
    "engagementLevel": "high",
    "browsingStyle": "research_heavy",
    "decisionSpeed": "deliberate",
    "channelPreference": "online_with_consultation"
  },
  "salesOpportunity": {
    "opportunityScore": 8.7,
    "buyingReadiness": "high",
    "recommendedApproach": "value_demonstration",
    "optimalTiming": "immediate",
    "expectedSpend": 127.50
  },
  "personalizationFactors": {
    "communicationStyle": "professional_detailed",
    "valueDrivers": ["quality", "results", "convenience"],
    "objectionPatterns": ["price_justification", "ingredient_safety"],
    "motivationTriggers": ["expert_recommendation", "proven_results"]
  },
  "isGuestUser": false,
  "guestUserDefaults": null
}
```

## 3. SmartProductFinder

### Purpose
Finds optimal products and bundles based on customer intent and maximizes revenue potential.

### Embedded Retrievers
- **Product List**: `Product_Neo_Retriever_1Cx_9gr30293855`
- **Product Bundle**: `Default_Product_Bundle_Lookup_Retriever_1Cx_9gr5d65cb3f`

### Updated Prompt
```xml
SYSTEM INSTRUCTIONS:
You are an AI assistant that finds optimal products and bundles based on customer intent and maximizes revenue potential.

DATA SOURCES:
Use "Product_Neo_Retriever_1Cx_9gr30293855" and "Default_Product_Bundle_Lookup_Retriever_1Cx_9gr5d65cb3f" for product matching and bundling.

INPUTS:
- intent_data = {!$Input:Query}  // JSON from Customer Intent Decoder
- customer_profile = Optional customer context

TASKS:
1. Search Product_List {!$EinsteinSearch:Product_Neo_Retriever_1Cx_9gr30293855.results} using keywords from intent_data
2. Match products based on:
   • Health domain alignment
   • Severity level appropriateness  
   • Customer's budget range
   • Consultation requirements
3. Find complementary products using Product_Bundle {!$EinsteinSearch:Default_Product_Bundle_Lookup_Retriever_1Cx_9gr5d65cb3f.results}
4. Calculate bundle pricing and discounts
5. Determine upsell opportunities
6. Score each recommendation for relevance
7. FALLBACK HANDLING: If no products found or low match scores (<0.4), trigger fallback with category alternatives

OUTPUT (return exactly this JSON schema):
{
  "primaryProducts": [
    {
      "productCode": "ACS-401",
      "name": "Acne Control Serum Pro",
      "price": 34.99,
      "matchScore": 0.94,
      "matchReason": "Perfect for oily, acne-prone skin with clinical ingredients",
      "healthDomain": "acne_treatment",
      "severityLevel": 7,
      "consultationRequired": false
    }
  ],
  "recommendedBundles": [
    {
      "bundleId": "ACNE-STARTER-001",
      "name": "Complete Acne Solution",
      "products": ["ACS-401", "OFC-201", "MAT-301"],
      "originalPrice": 78.97,
      "bundlePrice": 63.99,
      "discount": 19,
      "bundleScore": 0.89
    }
  ],
  "upsellOpportunities": [
    {
      "productCode": "ACS-501",
      "name": "Premium Acne Serum",
      "additionalPrice": 25.00,
      "upsellReason": "3x faster results with advanced peptides",
      "conversionProbability": 0.67
    }
  ],
  "totalRevenueOpportunity": 127.50,
  "recommendationConfidence": 0.91,
  "consultationUpsell": {
    "recommended": true,
    "value": 75.00,
    "reason": "Professional skin analysis maximizes treatment effectiveness"
  },
  "fallbackTriggered": false,
  "fallbackReason": "no_products_found | low_match_scores | out_of_stock",
  "needsTrendingFallback": false
}
```

### Apex Input/Output
```apex
// Input
String intentData = '{"intent":{"primaryConcern":"acne","confidenceScore":0.92},"urgency":{"level":"high"},"budget":{"priceRange":"mid"}}';

// Expected Output JSON
{
  "primaryProducts": [
    {
      "productCode": "ACS-401",
      "name": "Acne Control Serum Pro",
      "price": 34.99,
      "matchScore": 0.94,
      "matchReason": "Perfect for oily, acne-prone skin with clinical ingredients",
      "healthDomain": "acne_treatment",
      "severityLevel": 7,
      "consultationRequired": false
    }
  ],
  "recommendedBundles": [
    {
      "bundleId": "ACNE-STARTER-001",
      "name": "Complete Acne Solution",
      "products": ["ACS-401", "OFC-201", "MAT-301"],
      "originalPrice": 78.97,
      "bundlePrice": 63.99,
      "discount": 19,
      "bundleScore": 0.89
    }
  ],
  "upsellOpportunities": [],
  "totalRevenueOpportunity": 63.99,
  "recommendationConfidence": 0.91,
  "consultationUpsell": {
    "recommended": false,
    "value": 0,
    "reason": ""
  },
  "fallbackTriggered": false,
  "fallbackReason": "",
  "needsTrendingFallback": false
}
```

## 4. ConsultationBookingAgent

### Purpose
Determines when to offer consultations and generates booking form data for LWC integration.

### Input Retriever
- **Retriever ID**: Dynamic based on context (Customer, Product, Knowledge Articles)
- **Input**: JSON containing customer query, product context, customer profile, and urgency signals

### Updated Prompt
```xml
SYSTEM INSTRUCTIONS:
You are an AI assistant that determines when to offer consultations and generates booking form data for LWC integration.

INPUTS:
- query_data = {!$Input:Query}  // JSON containing all required data:
  {
    "customer_query": "Customer's original inquiry",
    "product_context": "Products being considered (JSON)",
    "customer_profile": "Customer information (JSON)",
    "urgency_signals": "Urgency indicators from other agents (JSON)"
  }

TASKS:
1. Parse the JSON input to extract:
   • customer_query from query_data
   • product_context from query_data
   • customer_profile from query_data
   • urgency_signals from query_data
2. Analyze consultation triggers:
   • Complex skin concerns (multiple issues)
   • High-value products (>$75)
   • Products requiring ConsultationOffered__c = true
   • Customer hesitation signals
   • First-time complex purchase
3. Determine consultation type and urgency
4. Generate booking form configuration for LWC
5. Create compelling consultation offer messaging emphasizing FREE consultation
6. Set appropriate consultation incentives (all FREE)
7. Include privacy assurance message
8. Generate thank you message for booking confirmation

OUTPUT (return exactly this JSON schema):
{
  "consultationNeeded": true,
  "consultationType": "skin_analysis | product_consultation | premium_consultation",
  "urgencyLevel": "low | medium | high | urgent",
  "bookingForm": {
    "showForm": true,
    "formTitle": "Book Your Free Skin Consultation",
    "formFields": [
      {
        "name": "preferredDate",
        "type": "date",
        "label": "Preferred Date",
        "required": true,
        "minDate": "2025-08-06"
      }
    ]
  },
  "consultationOffer": {
    "headline": "Get Expert Advice - FREE Virtual Consultation",
    "benefits": [
      "Personalized skin analysis",
      "Custom product recommendations", 
      "Professional skincare routine",
      "30-day satisfaction guarantee"
    ],
    "urgencyMessage": "Limited slots available - Book now!",
    "valueProposition": "Worth $75 - Yours FREE with any purchase",
    "ctaText": "Book My Free Consultation",
    "privacyAssurance": "Your information is used solely for consultation scheduling. No marketing emails or promotional content will be sent.",
    "thankYouMessage": "Thank you for booking your FREE consultation! We'll contact you shortly to confirm your appointment details."
  },
  "incentives": {
    "freeConsultation": true,
    "discountOnProducts": 15,
    "bonusProducts": ["travel_size_cleanser"],
    "guaranteeExtension": "60_day_guarantee",
    "noFees": "100% FREE consultation - no hidden costs"
  }
}
```

### Apex Input/Output
```apex
// Input
String queryData = '{"customer_query":"I have acne and sensitive skin","product_context":{"products":[{"price":34.99,"consultationRequired":false}]},"urgency_signals":{"level":"medium"}}';

// Expected Output JSON
{
  "consultationNeeded": true,
  "consultationType": "skin_analysis",
  "urgencyLevel": "medium",
  "bookingForm": {
    "showForm": true,
    "formTitle": "Book Your Free Skin Consultation",
    "formFields": [
      {
        "name": "preferredDate",
        "type": "date",
        "label": "Preferred Date",
        "required": true,
        "minDate": "2025-08-06"
      }
    ]
  },
  "consultationOffer": {
    "headline": "Get Expert Advice - FREE Virtual Consultation",
    "benefits": [
      "Personalized skin analysis",
      "Custom product recommendations", 
      "Professional skincare routine",
      "30-day satisfaction guarantee"
    ],
    "urgencyMessage": "Limited slots available - Book now!",
    "valueProposition": "Worth $75 - Yours FREE with any purchase",
    "ctaText": "Book My Free Consultation",
    "privacyAssurance": "Your information is used solely for consultation scheduling. No marketing emails or promotional content will be sent.",
    "thankYouMessage": "Thank you for booking your FREE consultation! We'll contact you shortly to confirm your appointment details."
  },
  "incentives": {
    "freeConsultation": true,
    "discountOnProducts": 15,
    "bonusProducts": ["travel_size_cleanser"],
    "guaranteeExtension": "60_day_guarantee",
    "noFees": "100% FREE consultation - no hidden costs"
  }
}
```

## 5. SalesPitchGenerator

### Purpose
Creates personalized sales strategies and consultation briefs for sales representatives.

### Input Retriever
- **Retriever ID**: Dynamic based on context (Product, Knowledge Articles, Purchase History)
- **Input**: JSON containing customer intelligence, product recommendations, browsing analysis, and consultation context

### Updated Prompt
```xml
SYSTEM INSTRUCTIONS:
You are an AI assistant that creates personalized sales strategies and consultation briefs for sales representatives.

INPUTS:
- query_data = {!$Input:Query}  // JSON containing all required data:
  {
    "customer_intelligence": "JSON from Customer Intelligence Engine",
    "product_recommendations": "JSON from Smart Product Finder",
    "browsing_analysis": "JSON from Browsing Behavior Analyzer (optional)",
    "consultation_context": "Meeting context and objectives (optional)",
    "voice_call_history": "Previous voice calls with sales reps (optional)",
    "email_context": "If agent requests email draft (optional)"
  }

TASKS:
1. Parse the JSON input to extract all required data
2. Analyze customer profile and create personalized sales approach
3. Map product benefits to customer's specific needs and motivations
4. Identify potential objections and prepare responses
5. Calculate optimal pricing and discount strategies
6. Define conversation flow and key talking points
7. Set realistic sales targets and close strategies
8. If voice_call_history exists, incorporate previous call insights into strategy
9. If email_context is requested, generate professional email draft with subject line

OUTPUT (return exactly this JSON schema):
{
  "consultationBrief": {
    "customerId": "CUST-12345",
    "customerName": "Sarah Johnson",
    "opportunityScore": 8.7,
    "expectedRevenue": 127.50,
    "consultationDuration": "30min",
    "preparationTime": "5min"
  },
  "customerInsights": {
    "personality": "analytical_professional",
    "communicationStyle": "detail_oriented",
    "decisionFactors": ["proven_results", "expert_validation", "value_for_money"],
    "buyingPattern": "research_then_commit",
    "priceRangeComfort": "50-150"
  },
  "salesStrategy": {
    "primaryApproach": "consultative_solution_selling",
    "openingTactic": "empathy_and_expertise",
    "valueProposition": "Professional-grade results with expert guidance",
    "closeStrategy": "assumption_close_with_guarantee",
    "backupStrategy": "smaller_commitment_with_upgrade_path"
  },
  "productPresentation": {
    "leadProduct": {
      "code": "ACS-401",
      "presentationAngle": "clinical_efficacy",
      "benefitMapping": "Addresses your specific acne concerns with dermatologist-recommended ingredients",
      "socialProof": "89% of professionals like you see results within 14 days"
    }
  },
  "objectionHandling": {
    "priceObjection": {
      "response": "I understand budget is important. Consider this: the cost per day is less than your morning coffee, but the confidence boost lasts all day.",
      "alternatives": ["payment_plan", "starter_bundle", "guarantee_emphasis"]
    }
  },
  "conversationFlow": {
    "discovery": [
      "Tell me about your current skincare routine",
      "What results are you hoping to achieve?",
      "What's worked or not worked for you before?"
    ],
    "presentation": [
      "Based on what you've shared, here's what I recommend and why",
      "This addresses your specific concerns because...",
      "Here's what you can expect in the first 30 days"
    ],
    "close": [
      "Which package makes the most sense for your goals?",
      "Should we start with the complete system or would you prefer to begin with the essentials?"
    ]
  },
  "successMetrics": {
    "primaryGoal": "close_127_dollar_sale",
    "minimumSuccess": "63_dollar_bundle_sale",
    "stretchGoal": "180_dollar_premium_package",
    "followUpOpportunity": "quarterly_replenishment_program"
  }
}
```

### Apex Input/Output
```apex
// Input
String queryData = '{"customer_intelligence":{"customerProfile":{"name":"Sarah Johnson"},"salesOpportunity":{"expectedSpend":127.50}},"product_recommendations":{"primaryProducts":[{"productCode":"ACS-401","name":"Acne Control Serum Pro"}]}}';

// Expected Output JSON
{
  "consultationBrief": {
    "customerId": "CUST-12345",
    "customerName": "Sarah Johnson",
    "opportunityScore": 8.7,
    "expectedRevenue": 127.50,
    "consultationDuration": "30min",
    "preparationTime": "5min"
  },
  "customerInsights": {
    "personality": "analytical_professional",
    "communicationStyle": "detail_oriented",
    "decisionFactors": ["proven_results", "expert_validation", "value_for_money"],
    "buyingPattern": "research_then_commit",
    "priceRangeComfort": "50-150"
  },
  "salesStrategy": {
    "primaryApproach": "consultative_solution_selling",
    "openingTactic": "empathy_and_expertise",
    "valueProposition": "Professional-grade results with expert guidance",
    "closeStrategy": "assumption_close_with_guarantee",
    "backupStrategy": "smaller_commitment_with_upgrade_path"
  },
  "productPresentation": {
    "leadProduct": {
      "code": "ACS-401",
      "presentationAngle": "clinical_efficacy",
      "benefitMapping": "Addresses your specific acne concerns with dermatologist-recommended ingredients",
      "socialProof": "89% of professionals like you see results within 14 days"
    }
  },
  "objectionHandling": {
    "priceObjection": {
      "response": "I understand budget is important. Consider this: the cost per day is less than your morning coffee, but the confidence boost lasts all day.",
      "alternatives": ["payment_plan", "starter_bundle", "guarantee_emphasis"]
    }
  },
  "conversationFlow": {
    "discovery": [
      "Tell me about your current skincare routine",
      "What results are you hoping to achieve?",
      "What's worked or not worked for you before?"
    ],
    "presentation": [
      "Based on what you've shared, here's what I recommend and why",
      "This addresses your specific concerns because...",
      "Here's what you can expect in the first 30 days"
    ],
    "close": [
      "Which package makes the most sense for your goals?",
      "Should we start with the complete system or would you prefer to begin with the essentials?"
    ]
  },
  "successMetrics": {
    "primaryGoal": "close_127_dollar_sale",
    "minimumSuccess": "63_dollar_bundle_sale",
    "stretchGoal": "180_dollar_premium_package",
    "followUpOpportunity": "quarterly_replenishment_program"
  }
}
```

## 6. BrowSingProm

### Purpose
Analyzes a shopper's recent browsing behavior to decide whether a specific product should be recommended.

### Embedded Retriever
- **Browsing Behavior**: `Default_Browsing_Behavior_Retriever_1Cx_9gr0eb14871`

### Updated Prompt
```xml
SYSTEM INSTRUCTIONS:
You are an AI assistant that analyzes a shopper's recent browsing behavior to decide whether a specific product should be recommended.

DATA SOURCE:
Only use results returned by the "Default_Browsing_Behavior_Retriever_1Cx_9gr0eb14871" retriever.

INPUTS:
- user_query = {!$Input:Query}  // Natural-language question from the shopper can also contain product code
- productCode = Extracted from user_query or provided context

TASKS:
1. Search Product_Browsing_Data {!$EinsteinSearch:Default_Browsing_Behavior_Retriever_1Cx_9gr0eb14871.results} where productCode = {{productCode}}.
2. Aggregate the last 90 days of events. Compute:
   • viewCount – total views  
   • averageSessionTime – mean HH:MM:SS spent on product pages  
   • lastInteraction – most-recent ISO timestamp  
   • actionsTaken – unique list of interaction types (viewed, added_to_cart, removed_from_cart, wishlisted)
3. Derive engagementLevel:  
   • viewCount < 2 or session < 1 min → "low"  
   • viewCount 2-4 or session 1-2 min → "medium"  
   • viewCount 5-7 or session 2-4 min → "high"  
   • viewCount > 7 or session > 4 min → "very_high"
4. Calculate recommendationScore (0–1):  
   recommendationScore = MIN(1, (viewCount / 10) + (averageSessionTime_minutes / 10))
5. Determine nextBestAction:  
   – score >= 0.7        → "immediate_recommendation"  
   – 0.4 <= score < 0.7     → "nurture_sequence"  
   – score < 0.4         → "show_alternatives"
6. (Optional) If nextBestAction = "show_alternatives", list up to 3 other SKUs the customer viewed most.

OUTPUT (return exactly this JSON schema):
{
 "productCode": "{{productCode}}",
 "recommendationScore": 0.00,
 "engagementLevel": "low | medium | high | very_high",
 "lastInteraction": "YYYY-MM-DDTHH:MM:SSZ",
 "viewCount": 0,
 "averageSessionTime": "HH:MM:SS",
 "actionsTaken": ["viewed"],
 "recommendationReason": "…one-sentence justification…",
 "nextBestAction": "immediate_recommendation | nurture_sequence | show_alternatives",
 "alternativeProducts": ["SKU123","SKU456"]
}
```

### Apex Input/Output
```apex
// Input
String userQuery = "I'm interested in ACS-401 acne serum";
String productCode = "ACS-401";

// Expected Output JSON
{
 "productCode": "ACS-401",
 "recommendationScore": 0.75,
 "engagementLevel": "high",
 "lastInteraction": "2025-08-06T14:30:00Z",
 "viewCount": 5,
 "averageSessionTime": "00:02:30",
 "actionsTaken": ["viewed", "wishlisted"],
 "recommendationReason": "High engagement with 5 views and 2.5 minutes average session time",
 "nextBestAction": "immediate_recommendation",
 "alternativeProducts": []
}
```

## 7. TrendingProductsFallback

### Purpose
Provides trending and popular products when primary product search fails or for guest users.

### Embedded Retrievers
- **Purchase History**: `Default_Purchase_History_Retriever_1Cx_9grbb5cc572`
- **Browsing Behavior**: `Default_Browsing_Behavior_Retriever_1Cx_9gr0eb14871`
- **Product List**: `Product_Neo_Retriever_1Cx_9gr30293855`

### Updated Prompt
```xml
SYSTEM INSTRUCTIONS:
You are a fallback AI assistant that provides trending and popular products when primary product search fails or for guest users.

DATA SOURCES:
Use "Default_Purchase_History_Retriever_1Cx_9grbb5cc572", "Default_Browsing_Behavior_Retriever_1Cx_9gr0eb14871", and "Product_Neo_Retriever_1Cx_9gr30293855" for trending analysis.

INPUTS:
- query_data = {!$Input:Query}  // JSON containing all required data:
  {
    "fallback_reason": "Why fallback triggered: no_keywords | no_products_found | guest_user | low_inventory",
    "category_hint": "Optional category context",
    "season_context": "Current season/time context"
  }

TASKS:
1. Parse the JSON input to extract:
   • fallback_reason from query_data
   • category_hint from query_data
   • season_context from query_data
2. Analyze recent purchase trends from Purchase_History {!$EinsteinSearch:Default_Purchase_History_Retriever_1Cx_9grbb5cc572.results}
   • Most purchased products last 30 days
   • Seasonal trending patterns
   • Category-specific popularity
3. Evaluate browsing patterns from Browsing_Behavior {!$EinsteinSearch:Default_Browsing_Behavior_Retriever_1Cx_9gr0eb14871.results}
   • Most viewed products
   • High engagement products
   • Products with high conversion rates
4. Get current product inventory and availability from Product_List {!$EinsteinSearch:Product_Neo_Retriever_1Cx_9gr30293855.results}
5. Create trending product recommendations with social proof
6. Include category alternatives and cross-category suggestions

OUTPUT (return exactly this JSON schema):
{
  "fallbackReason": "{{fallback_reason}}",
  "trendingProducts": [
    {
      "productCode": "HS-102",
      "name": "Hydrating Serum Pro",
      "price": 34.99,
      "category": "serums",
      "trendScore": 0.95,
      "socialProof": "89% of customers repurchase",
      "trendingReason": "315% increase in purchases this month",
      "urgencyFactor": "Only 12 left in stock"
    }
  ],
  "categoryAlternatives": [
    {
      "category": "moisturizers",
      "topProduct": {
        "productCode": "MOI-201",
        "reason": "Popular alternative in same skin category"
      }
    }
  ],
  "socialProofMetrics": {
    "totalCustomersServed": 15420,
    "averageRating": 4.7,
    "repurchaseRate": 73,
    "thisMonthPurchases": 1247
  },
  "recommendationStrategy": {
    "primary": "trending_popularity",
    "secondary": "seasonal_relevance", 
    "tertiary": "category_similarity"
  },
  "guestUserSpecial": {
    "welcomeOffer": "First-time customer discount",
    "discountPercent": 20,
    "freeShipping": true,
    "sampleProducts": ["travel_size_cleanser", "sample_moisturizer"]
  }
}
```

### Apex Input/Output
```apex
// Input
String queryData = '{"fallback_reason":"no_products_found","category_hint":"acne_treatment","season_context":"summer"}';

// Expected Output JSON
{
  "fallbackReason": "no_products_found",
  "trendingProducts": [
    {
      "productCode": "HS-102",
      "name": "Hydrating Serum Pro",
      "price": 34.99,
      "category": "serums",
      "trendScore": 0.95,
      "socialProof": "89% of customers repurchase",
      "trendingReason": "315% increase in purchases this month",
      "urgencyFactor": "Only 12 left in stock"
    }
  ],
  "categoryAlternatives": [
    {
      "category": "moisturizers",
      "topProduct": {
        "productCode": "MOI-201",
        "reason": "Popular alternative in same skin category"
      }
    }
  ],
  "socialProofMetrics": {
    "totalCustomersServed": 15420,
    "averageRating": 4.7,
    "repurchaseRate": 73,
    "thisMonthPurchases": 1247
  },
  "recommendationStrategy": {
    "primary": "trending_popularity",
    "secondary": "seasonal_relevance", 
    "tertiary": "category_similarity"
  },
  "guestUserSpecial": {
    "welcomeOffer": "First-time customer discount",
    "discountPercent": 20,
    "freeShipping": true,
    "sampleProducts": ["travel_size_cleanser", "sample_moisturizer"]
  }
}
```

## Implementation Recommendations

### 1. Retriever Configuration
- **Input Retrievers**: Use for templates that need dynamic context or multiple data sources
- **Embedded Retrievers**: Use for templates that consistently access the same data sources

### 2. Performance Optimization
- Cache frequently accessed data (customer profiles, product catalogs)
- Use parallel processing for independent data retrieval
- Implement fallback mechanisms for retriever failures

### 3. Error Handling
- Add retry logic for retriever failures
- Implement graceful degradation when data is unavailable
- Log retriever performance metrics for optimization

### 4. Testing Strategy
- Unit test each prompt template with sample data
- Integration test the complete orchestration flow
- Performance test with realistic data volumes
- A/B test different prompt variations for optimization

This optimized setup ensures fast response times while maintaining data accuracy and personalization capabilities for the wellness sales curator agent bot. 