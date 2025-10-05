# Video Generation Testing Guide

## ✅ How to Verify Video Generation is Working

### 1. **Terminal Monitoring (BEST METHOD)**

Watch your terminal for these log patterns:

#### **Initial Generation:**
```
✓ Compiled /api/generate-video in XXXms
operation: GenerateVideosOperation { name: 'projects/...' }
POST /api/generate-video 200 in ~1000ms
```
✅ This means video generation started successfully

#### **Polling (Every 5 seconds):**
```
🔍 Checking video status for operation: e74b89fd-4e7
📊 Video status: ⏳ PROCESSING
POST /api/check-video-status 200 in XXXms
```
✅ This means polling is working (NOT an infinite loop)

#### **Completion:**
```
🔍 Checking video status for operation: e74b89fd-4e7
📊 Video status: ✅ DONE
```
✅ Video is ready!

---

## 🚨 Signs of Problems

### **Infinite Loop:**
- You see NO `/api/check-video-status` requests after 10+ seconds
- Browser console shows `🔄 Polling 1 video operation(s)...` every 5s but no terminal requests

### **Stuck:**
- Polling stops completely
- Terminal shows nothing new
- Video stays in "Generating..." state forever

### **Failed:**
- `POST /api/check-video-status 500`
- `POST /api/generate-video 500`
- Error message in browser UI

---

## 📊 Browser Console Logs

Open your browser's **Developer Tools** (F12) → **Console** tab.

### **Healthy Flow:**
```
🔄 Polling 1 video operation(s)...           ← Every 5 seconds
✅ Video operation vid_17596... completed!    ← When done
🎬 Video URL ready: https://storage...        ← Video ready
```

### **Problem Signs:**
```
🔄 Polling 1 video operation(s)...
(no further logs)                             ← Stuck
```

---

## ⏱️ Expected Timeline

| Time | What Happens |
|------|--------------|
| 0s | Upload image + description |
| 5-15s | Scripts generated (GPT-4o) |
| 15-16s | Video generation starts |
| 16s+ | Polling every 5 seconds |
| **60-120s** | **Video completes** ✅ |

**Note:** Veo 3 typically takes 60-120 seconds to generate a 4-8 second video.

---

## 🧪 Quick Test

1. **Start the app:**
   ```bash
   cd /Users/rohanbutani/Desktop/DivHacks2/foundry
   npm run dev
   ```

2. **Upload a product image** (e.g., a coffee mug, phone, NOT people)

3. **Watch the terminal** - you should see:
   - ✅ Line 220: `POST /api/analyze-product 200` (scripts generated)
   - ✅ Line 225: `POST /api/generate-video 200` (video started)
   - ✅ Every 5s: `POST /api/check-video-status 200` (polling)

4. **Open browser console** - you should see:
   - `🔄 Polling 1 video operation(s)...` every 5 seconds

5. **Wait 60-120 seconds** for:
   - `📊 Video status: ✅ DONE` in terminal
   - `✅ Video operation completed!` in browser console

---

## 🐛 Troubleshooting

### Problem: No polling requests
**Cause:** Video operations not stored in correct localStorage key
**Fix:** ✅ **FIXED** - Now uses `videoOperationsStorage.store()` utility (Feb 2025)
**Verify:** Check browser console for `📹 Storing video operation for polling: vid_...`

### Problem: Polling forever (>3 minutes)
**Cause:** Veo 3 might have failed/filtered the content
**Fix:** Check for safety filter messages in terminal. Try a different product image.

### Problem: "Failed to generate video"
**Cause:** OpenAI content policy or API error
**Fix:** 
- Check terminal for specific error
- Try product image without people
- Verify API keys are set

---

## 📈 Success Indicators

✅ Terminal shows polling every 5 seconds  
✅ Browser console shows polling logs  
✅ Video appears in UI after 60-120 seconds  
✅ Polling stops after video completes  
✅ Video is playable  

## ❌ Failure Indicators

❌ No polling requests after 10+ seconds  
❌ Polling continues beyond 5 minutes  
❌ Error in terminal or browser console  
❌ "Generating..." spinner never stops  
❌ Video URL is empty or invalid  

