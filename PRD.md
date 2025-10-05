# Product Requirements Document (PRD)
## UGC-Style TikTok Ad Generator

**Version:** 1.0  
**Date:** October 4, 2025  
**Product Name:** TikTok Ad Creator  
**Platform:** Echo AI Video Generation Template (Next.js)

---

## 1. Executive Summary

A web application that enables users to create user-generated content (UGC) style TikTok advertisements by uploading a product image and description. The system uses AI to research effective TikTok strategies, generate multiple script ideas with visual descriptions, and iteratively refine the final video based on user feedback.

---

## 2. Product Vision

Transform product marketing by democratizing UGC-style TikTok ad creation through AI-powered script generation and video production, enabling rapid iteration and refinement.

---

## 3. Core User Flow

```
1. User uploads product image + description (1-2 sentences)
   ↓
2. AI analyzes image & description
   ↓
3. AI generates 5 script ideas (script + visual description)
   ↓
4. First script auto-generates as video using Veo 3
   ↓
5. User reviews video → provides free-form feedback OR generates next script
   ↓
6. If feedback: regenerate same script with adjustments
   ↓
7. Repeat until user satisfied → can generate remaining scripts
   ↓
8. Final video is culmination of iterative refinement
```

---

## 4. Technical Architecture

### 4.1 Foundation
- **Base:** Echo AI's Next.js video generation template (existing, working)
- **Video Model:** Veo 3 (via Echo SDK)
- **LLM:** GPT-4o-mini (for analysis & script generation)
- **Video Duration:** 4-8 seconds (existing Echo framework constraint)

### 4.2 Key Components

#### Existing (Keep Intact)
- `/api/generate-video/route.ts` - Video generation endpoint
- `/api/generate-video/vertex.ts` - Veo 3 integration
- `/api/check-video-status/route.ts` - Operation polling
- `useVideoGeneration` hook - Video generation logic
- `useVideoHistory` hook - Video history management
- `useVideoOperations` hook - Operation polling
- Echo SDK authentication & token management
- Video history storage & display

#### New Components to Add
1. **Product Input Component** (`product-input.tsx`)
   - Image upload (single product image)
   - Text input (1-2 sentence description)
   - "Generate Scripts" button

2. **Script Analysis API** (`/api/analyze-product/route.ts`)
   - Accepts: image (base64) + description text
   - Uses GPT-4o-mini to:
     - Identify product features from image
     - Analyze target audience
     - Research effective TikTok strategies (from training data)
     - Generate 5 distinct script ideas
   - Returns: Array of 5 script objects

3. **Script Display Component** (`script-display.tsx`)
   - Shows all 5 generated scripts
   - Each script card shows:
     - Script number
     - Written script text
     - Visual description
     - "Generate Video" button
   - Highlights current active script

4. **Feedback Component** (`video-feedback.tsx`)
   - Shows after video generation
   - Free-form text input for feedback
   - Two action buttons:
     - "Regenerate with Feedback"
     - "Generate Next Script"

5. **Iteration Tracking** (`useIterationHistory` hook)
   - Track which script is active
   - Track feedback history per script
   - Track video generations per script
   - Manage iteration state

---

## 5. Detailed Feature Specifications

### 5.1 Product Input Phase

**UI Elements:**
- Single image upload area (drag & drop or click)
  - Accepts: JPEG, PNG
  - Preview uploaded image
  - Max file size: 10MB
- Text area for product description
  - Placeholder: "Describe your product in 1-2 sentences..."
  - Character limit: 500
  - Min length: 10 characters
- "Generate Scripts" button
  - Disabled until image + description provided
  - Shows loading state during generation

**Validation:**
- Must have 1 image
- Must have 10-500 characters of text
- Image must be valid format

### 5.2 Script Generation Phase

**GPT-4o-mini Prompt Structure:**
```
System: You are a TikTok marketing expert specializing in UGC-style advertisements.

User: 
Image: [base64 product image]
Description: [user description]

Task:
1. Analyze the product image and identify key features, benefits, and visual elements
2. Based on the product type, identify the target audience
3. Research effective TikTok UGC advertisement strategies for this product category
4. Generate 5 diverse script ideas (4-8 seconds each) that would be effective as TikTok ads

Each script should include:
- Hook: Opening line (first 1 second)
- Main content: Key message (2-5 seconds)
- Call to action: Closing (1-2 seconds)
- Visual description: Detailed description of what should be shown in each scene

Format as JSON array of 5 scripts.
```

**Script Object Structure:**
```typescript
interface Script {
  id: string;
  number: number; // 1-5
  title: string; // Brief title
  hook: string;
  mainContent: string;
  callToAction: string;
  visualDescription: string; // Detailed scene description
  estimatedDuration: number; // 4-8 seconds
}
```

**Strategy Types (AI should vary these):**
- Product demonstration
- Problem/solution narrative
- Testimonial style
- Lifestyle integration
- Comparison/before-after

### 5.3 Video Generation Phase

**Process:**
1. Auto-generate first script (#1) immediately after scripts created
2. Construct Veo 3 prompt by combining:
   - Product image (as first frame)
   - Full script text (hook + main + CTA)
   - Visual description
   - TikTok-specific instructions (vertical format, engaging, UGC style)

**Veo 3 Prompt Template:**
```
Create a 4-8 second TikTok-style product advertisement video.

Product: [from image]
Script: [hook + main + CTA]
Visual Style: [visualDescription]

Requirements:
- Vertical 9:16 format
- User-generated content aesthetic
- Fast-paced, engaging
- Clear focus on product
- Natural, authentic feel
```

### 5.4 Feedback & Iteration Phase

**Feedback UI:**
- Appears immediately after video completes
- Shows:
  - Generated video (looping)
  - Current script text
  - Feedback iteration count (e.g., "Iteration 2 of Script 1")
  - Free-form feedback text area
  - Action buttons

**Feedback Processing:**
When user provides feedback:
1. Append feedback to iteration history
2. Construct new GPT-4o-mini prompt:
   ```
   Original Script: [original script]
   Previous Video Prompt: [previous Veo prompt]
   User Feedback: [user feedback]
   
   Task: Adjust the script and visual description based on feedback.
   Maintain the core message but incorporate requested changes.
   Return updated script object.
   ```
3. Generate new video with updated prompt
4. Increment iteration counter

**Iteration Limits:**
- No hard limit on iterations per script
- Show iteration count for transparency
- Each iteration stored in history

### 5.5 Multi-Script Management

**Script Navigation:**
- Users can switch between scripts at any time
- Each script maintains its own iteration history
- Can generate any of the 5 scripts in any order
- Can return to previous scripts and iterate further

**State Management:**
```typescript
interface IterationState {
  productImage: string;
  productDescription: string;
  scripts: Script[];
  activeScriptId: string;
  scriptIterations: {
    [scriptId: string]: {
      iterations: Array<{
        videoId: string;
        feedback?: string;
        prompt: string;
        timestamp: Date;
      }>;
      currentIteration: number;
    };
  };
}
```

---

## 6. Data Flow

### 6.1 Product Analysis Flow
```
POST /api/analyze-product
Body: { image: string, description: string }

→ GPT-4o-mini analyzes product
→ Generates 5 script ideas
→ Returns: { scripts: Script[] }
```

### 6.2 Video Generation Flow (Existing, Enhanced)
```
POST /api/generate-video
Body: {
  prompt: string (constructed from script),
  model: "veo-3.0-fast-generate-preview",
  durationSeconds: 4-8,
  image: string (product image)
}

→ Veo 3 generates video
→ Returns: GenerateVideosOperation
→ Poll /api/check-video-status until complete
```

### 6.3 Feedback Refinement Flow
```
User provides feedback
→ POST /api/refine-script
Body: {
  originalScript: Script,
  feedback: string,
  previousPrompt: string
}

→ GPT-4o-mini adjusts script
→ Returns: { refinedScript: Script }
→ Trigger new video generation with refined script
```

---

## 7. User Interface Design

### 7.1 Main Layout
```
┌─────────────────────────────────────────┐
│  Header: Echo TikTok Ad Creator         │
├─────────────────────────────────────────┤
│                                         │
│  [Step 1: Product Input]                │
│  ┌─────────────┐  ┌──────────────────┐ │
│  │             │  │ Description:     │ │
│  │   Upload    │  │                  │ │
│  │   Image     │  │ [Text Area]      │ │
│  │             │  │                  │ │
│  └─────────────┘  └──────────────────┘ │
│         [Generate Scripts]              │
│                                         │
├─────────────────────────────────────────┤
│  [Step 2: Script Selection]             │
│  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌────┐│
│  │Sc 1 │ │Sc 2 │ │Sc 3 │ │Sc 4 │ │Sc 5││
│  │[⚡] │ │     │ │     │ │     │ │    ││
│  └─────┘ └─────┘ └─────┘ └─────┘ └────┘│
│                                         │
│  [Script Details & Generate Button]     │
│                                         │
├─────────────────────────────────────────┤
│  [Step 3: Video & Feedback]             │
│  ┌───────────────┐  ┌────────────────┐ │
│  │               │  │ Iteration 2    │ │
│  │  Video Player │  │                │ │
│  │  (9:16)       │  │ Feedback:      │ │
│  │               │  │ [Text Area]    │ │
│  │               │  │                │ │
│  └───────────────┘  └────────────────┘ │
│  [Regenerate] [Generate Next Script]    │
│                                         │
├─────────────────────────────────────────┤
│  [Video History]                        │
│  [Previous generations...]              │
└─────────────────────────────────────────┘
```

### 7.2 Component States

**Product Input:**
- Empty state: Show upload prompt
- With content: Show preview + edit option
- Generating: Loading spinner on button
- Complete: Success message, show scripts

**Script Cards:**
- Ungenerated: Neutral state, "Generate" button
- Active/Generating: Highlighted, loading state
- Generated: Show checkmark, "View/Regenerate"
- Current: Bold border, "Active" badge

**Video Player:**
- Loading: Skeleton + progress indicator
- Playing: Autoplay on loop, controls visible
- Error: Error message + retry button

---

## 8. Technical Implementation Details

### 8.1 New API Routes

**`/api/analyze-product/route.ts`**
- Rate limit: 10 requests/minute per user
- Timeout: 60 seconds
- Error handling: Retry logic for GPT-4o-mini
- Caching: Cache results for same image hash

**`/api/refine-script/route.ts`**
- Rate limit: 20 requests/minute per user
- Timeout: 30 seconds
- Store iteration history in session storage

### 8.2 State Management

**Local Storage Keys:**
- `tiktok_ad_product`: Current product data
- `tiktok_ad_scripts`: Generated scripts
- `tiktok_ad_iterations`: Iteration history
- Merge with existing `video_history` storage

### 8.3 OpenAI Integration

**Configuration:**
```typescript
import OpenAI from 'openai';

const openai = new OpenAI({
  apiKey: await getEchoToken(), // Use Echo token
  baseURL: 'https://echo.router.merit.systems/v1',
});

// For analysis
const completion = await openai.chat.completions.create({
  model: "gpt-4o-mini",
  messages: [...],
  response_format: { type: "json_object" },
  temperature: 0.7,
});
```

### 8.4 Prompt Engineering

**Image Analysis Prompt:**
- Use vision capabilities of GPT-4o-mini
- Include image as base64 data URL
- Request structured JSON output
- Include examples of good TikTok scripts

**Script Refinement Prompt:**
- Provide full context (original + feedback)
- Emphasize maintaining core message
- Request specific changes only
- Preserve TikTok best practices

---

## 9. Success Metrics

### 9.1 User Engagement
- Average scripts generated per session
- Average iterations per script
- Completion rate (product → final video)
- Time from upload to first video

### 9.2 Quality Metrics
- User satisfaction with generated scripts
- Average feedback iterations to satisfaction
- Video download/export rate
- Repeat usage rate

### 9.3 Technical Metrics
- Script generation latency (<10s)
- Video generation success rate (>90%)
- API error rate (<1%)
- System uptime (>99%)

---

## 10. Non-Functional Requirements

### 10.1 Performance
- Script generation: <10 seconds
- Video generation: 30-120 seconds (Veo 3 dependent)
- Page load time: <2 seconds
- Feedback processing: <5 seconds

### 10.2 Reliability
- Graceful degradation if APIs unavailable
- Auto-retry failed video generations
- Save progress automatically
- Session recovery on refresh

### 10.3 Security
- Echo SDK authentication (existing)
- Secure token handling (existing)
- Image upload validation
- Rate limiting on all endpoints
- Input sanitization

### 10.4 Scalability
- Stateless API design
- Horizontal scaling ready
- Efficient caching strategy
- Background job processing for videos

---

## 11. Constraints & Assumptions

### 11.1 Constraints
- Video length: 4-8 seconds (Echo framework)
- Veo 3 generation time: ~60-120 seconds
- Single product per session
- Must maintain existing functionality

### 11.2 Assumptions
- Users have basic understanding of TikTok
- Product images are clear and well-lit
- Users can articulate feedback
- GPT-4o-mini has sufficient TikTok knowledge
- Echo SDK tokens work with OpenAI

### 11.3 Dependencies
- Echo SDK availability
- Veo 3 API stability
- OpenAI GPT-4o-mini API
- Next.js runtime environment

---

## 12. Future Enhancements (Out of Scope for MVP)

### Phase 2
- Web search integration for real-time TikTok trends
- Voice-over generation for scripts
- Music/audio selection
- Multiple product images in single video
- A/B testing framework for scripts

### Phase 3
- Brand voice customization
- Competitor analysis
- Performance analytics integration
- Batch generation (multiple products)
- Template library

### Phase 4
- Multi-language support
- Platform optimization (Instagram Reels, YouTube Shorts)
- Collaborative editing
- Campaign management

---

## 13. Implementation Phases

### Phase 1: Core Infrastructure (Week 1)
- [ ] Product input component
- [ ] GPT-4o-mini integration
- [ ] Script analysis API
- [ ] Basic script display
- [ ] Integration with existing video generation

### Phase 2: Iteration System (Week 1)
- [ ] Feedback component
- [ ] Script refinement API
- [ ] Iteration tracking
- [ ] State management
- [ ] Progress persistence

### Phase 3: Polish & Testing (Week 2)
- [ ] UI/UX refinement
- [ ] Error handling
- [ ] Loading states
- [ ] Responsive design
- [ ] End-to-end testing

### Phase 4: Optimization (Week 2)
- [ ] Performance tuning
- [ ] Caching implementation
- [ ] Rate limiting
- [ ] Documentation
- [ ] Deployment

---

## 14. Open Questions & Decisions Needed

1. ✅ **Video iteration approach** - Regenerate same script (DECIDED)
2. ✅ **LLM choice** - GPT-4o-mini (DECIDED)
3. ✅ **Feedback format** - Free-form text (DECIDED)
4. ✅ **Video duration** - 4-8 seconds (DECIDED)
5. ⚠️ **Cost management** - How to handle API costs per user?
6. ⚠️ **Storage limits** - How many videos to keep in history?
7. ⚠️ **Demo vs Production** - What features to cut for demo?

---

## 15. Success Criteria for Demo

### Must Have
- ✅ User can upload product image + description
- ✅ System generates 5 distinct script ideas
- ✅ First script auto-generates as video
- ✅ User can provide feedback and regenerate
- ✅ Iteration tracking visible
- ✅ Existing video generation still works

### Nice to Have
- Generate other 4 scripts on demand
- Switch between scripts
- Download final video
- Share video URL

### Demo Flow
```
1. Show homepage (30s)
2. Upload sample product image (15s)
3. Enter product description (15s)
4. Generate scripts - show loading (10s)
5. Display 5 scripts (30s)
6. Auto-generate first video (60s)
7. Provide feedback (30s)
8. Show refined video (60s)
9. Show iteration history (30s)
Total: ~5 minutes
```

---

## 16. Risk Assessment

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Veo 3 slow generation | High | Medium | Set expectations, show progress |
| GPT-4o-mini poor scripts | High | Low | Prompt engineering, examples |
| Cost overruns | Medium | Medium | Rate limiting, quotas |
| Breaking existing features | High | Low | Comprehensive testing |
| Poor user feedback quality | Medium | Medium | Guidance, examples |

---

## Appendix A: Example Script Output

```json
{
  "scripts": [
    {
      "id": "script_1",
      "number": 1,
      "title": "Problem Solver",
      "hook": "Tired of tangled headphones?",
      "mainContent": "These wireless earbuds automatically connect and never tangle.",
      "callToAction": "Get yours today!",
      "visualDescription": "Open with frustrated person untangling wired headphones. Cut to clean shot of wireless earbuds. Show person easily putting them on and smiling. Close-up of product logo.",
      "estimatedDuration": 6
    },
    {
      "id": "script_2",
      "number": 2,
      "title": "Lifestyle Integration",
      "hook": "Perfect sound for every moment",
      "mainContent": "From workouts to work calls, crystal clear audio all day.",
      "callToAction": "Upgrade your audio experience",
      "visualDescription": "Quick cuts: person jogging with earbuds, working at desk, on video call, cooking. All showing product in natural use. Product shot at end.",
      "estimatedDuration": 7
    }
  ]
}
```

---

## Appendix B: Tech Stack Summary

**Frontend:**
- Next.js 15.4.7 (App Router)
- React 19.1.0
- TypeScript 5
- Tailwind CSS 4
- Radix UI components
- Tanstack Query

**Backend:**
- Next.js API Routes
- Echo SDK for authentication
- OpenAI GPT-4o-mini (via Echo)
- Google Veo 3 (via Echo)

**Infrastructure:**
- Vercel deployment (assumed)
- Client-side storage (localStorage)
- Echo token management

---

**Document End**

