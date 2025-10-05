# Testing Checklist - Video Generation Fix

## 🎯 Critical Fix Applied

**Problem**: Videos generated but didn't appear in UI (ID mismatch)  
**Solution**: Use real Veo operation IDs consistently throughout

---

## ✅ Pre-Flight Checklist

1. **Clear Browser Data** (Critical!)
   - Open Browser Console (F12)
   - Run: `localStorage.clear()`
   - Run: `location.reload()`
   - This removes old operations with wrong IDs

2. **Verify Server is Running**
   - Check terminal for: `Ready in XXXms`
   - Note the port (likely 3000 or 3005)
   - URL: `http://localhost:XXXX`

---

## 🧪 Test Sequence

### Test 1: Basic Flow ✅

1. **Upload Product Image**
   - Use image WITHOUT people (avoid content policy)
   - Example: Coffee mug, product shot, object

2. **Add Description**
   - Example: "A beautiful ceramic coffee mug"

3. **Generate Scripts**
   - Click "Generate Scripts"
   - **Expected**:
     - Loading spinner
     - 5 scripts appear after ~10-15 seconds
     - First script card shows "Generating..."

4. **Monitor Browser Console**
   - **MUST SEE**:
     ```
     🎬 Video generation started with operation ID: 7e394e92-...
     💾 Stored operation 7e394e92-... (done: false)
     📦 Total operations in storage: 1
     🔎 useVideoOperations: Found 1 pending operations
     📋 Pending operations: ['7e394e92-...']
     ```

5. **Watch Polling (Every 5 seconds)**
   - **MUST SEE**:
     ```
     🔄 Polling 1 video operation(s)...
     ```
   - **Then after 60-120 seconds**:
     ```
     ✅ Video operation 7e394e92-... completed!
     🎬 Video URL ready: https://...
     🔗 Linked to script: script_...
     🔗 Updating iteration for script ..., video 7e394e92-...
     📦 Update data: { videoUrl: '...', isLoading: false }
     ```

6. **Verify Video Appears**
   - Video player should appear in UI
   - Video should auto-play
   - Controls should work

### Test 2: Feedback Loop ✅

1. **After video appears**, provide feedback:
   - Example: "Make it more energetic"

2. **Click "Regenerate with Feedback"**

3. **Monitor Console**:
   - **MUST SEE**:
     ```
     🎬 Refined video generation started with operation ID: abc123-...
     💾 Stored operation abc123-...
     🔎 useVideoOperations: Found 1 pending operations
     ```

4. **Wait for new video** (60-120 seconds)
   - New video should replace previous one
   - Iteration count should increment

### Test 3: Generate Next Script ✅

1. **Click "Generate Next Script"**

2. **Verify**:
   - Second script card shows "Generating..."
   - Console shows NEW operation ID (different from first)
   - Polling starts for new operation

3. **Wait for video**
   - Video appears in second script's section

---

## 🚨 What to Look For

### ✅ GOOD Signs

- **Consistent IDs**: All logs for one video use the SAME operation ID
- **Polling happens**: See `🔄` every 5 seconds
- **Completion logged**: See `✅ Video operation ... completed!`
- **Update triggered**: See `🔗 Updating iteration`
- **Video appears**: Player shows in UI

### ❌ BAD Signs (Report These!)

- **ID mismatch**: Different IDs in different logs
- **No polling**: No `🔄` logs after video starts
- **No completion**: Polling forever without `✅`
- **No update**: `✅` but no `🔗 Updating iteration`
- **No video**: Update logged but video doesn't appear
- **Wrong iteration**: Video appears under wrong script

---

## 📊 Terminal Monitoring

Watch your terminal for:

```
POST /api/analyze-product 200 in ~12000ms  ← Scripts generated
POST /api/generate-video 200 in ~1000ms    ← Video started
🔍 Checking video status for operation: 7e394e92-...
📊 Video status: ⏳ PROCESSING
POST /api/check-video-status 200 in ~500ms
(repeat every 5 seconds)
📊 Video status: ✅ DONE                   ← Video ready!
```

---

## 🔧 If Something Goes Wrong

### Issue: No polling logs

**Check**:
1. Browser console → any errors?
2. localStorage → check `video-operations` key
3. Run: `localStorage.clear()` and retry

### Issue: Polling but no completion

**Check**:
1. Terminal → any 500 errors?
2. Browser console → any red errors?
3. Wait 3-5 minutes (Veo can be slow)

### Issue: Completion logged but no video

**Check**:
1. Browser console → look for `🔗 Updating iteration`
2. If missing → **THIS IS THE BUG** - report full logs
3. If present → Check localStorage `tiktok_ad_state`

### Issue: Video under wrong script

**Check**:
1. Console → verify `🔗 Linked to script: script_...`
2. Verify script ID matches active script
3. Clear localStorage and retry

---

## 📸 Success Criteria

✅ Videos generate successfully  
✅ Polling starts automatically  
✅ Videos appear in UI after completion  
✅ Feedback loop works (regeneration)  
✅ Multiple scripts work independently  
✅ No ID mismatches in logs  

---

## 🎯 Key Debug Logs to Share

If it still doesn't work, share:

1. **Full browser console** (starting from upload)
2. **Terminal logs** (from video generation onwards)
3. **localStorage dump**:
   ```javascript
   console.log(JSON.parse(localStorage.getItem('video-operations')));
   console.log(JSON.parse(localStorage.getItem('tiktok_ad_state')));
   ```

---

## 🚀 Expected Timeline

- Script generation: **10-15 seconds**
- Video generation start: **Immediate (1-2 seconds)**
- Video completion: **60-120 seconds**
- Total: **~2 minutes** from upload to first video

Good luck! 🎉

