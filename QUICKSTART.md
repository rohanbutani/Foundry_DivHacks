# Quick Start Guide - TikTok Ad Creator

## 🚀 Get Running in 3 Steps

### 1. Setup Environment
```bash
cd foundry

# Create .env.local file
echo "ECHO_APP_ID=your_echo_app_id_here" > .env.local
```

Replace `your_echo_app_id_here` with your actual Echo App ID from https://echo.merit.systems

### 2. Install & Run
```bash
npm install
npm run dev
```

### 3. Open App
Open [http://localhost:3000](http://localhost:3000) in your browser

---

## 📱 Using the TikTok Ad Creator

### Quick Demo Flow (5 minutes)

1. **Sign in** with Echo (click the account button)

2. **Select "TikTok Ad Creator"** tab (should be default)

3. **Upload Product**:
   - Drag & drop or click to upload a product image
   - Enter description: "Wireless noise-cancelling headphones for work and travel"
   - Click "Generate 5 Script Ideas"

4. **Wait for Scripts** (~10 seconds):
   - 5 unique TikTok ad scripts will appear
   - First script auto-generates as video

5. **Watch Video** (~60-120 seconds):
   - Video generates using Veo 3
   - Watch in the vertical player

6. **Provide Feedback**:
   - Type: "Make it more energetic and emphasize the price"
   - Click "Regenerate with Feedback"
   - Watch improved version

7. **Try Other Scripts**:
   - Click "Generate Video" on any other script card
   - Each script can be iterated independently

8. **Switch Tabs**:
   - Click "Video Generator" tab
   - Original features still work perfectly!

---

## 🎯 What You Get

### ✅ Features
- **Product Analysis**: GPT-4o-mini analyzes your product
- **5 Script Ideas**: Diverse TikTok ad strategies
- **Video Generation**: 4-8 second TikTok-style videos via Veo 3
- **Iterative Refinement**: Unlimited feedback loops
- **State Persistence**: Resume anytime

### 🎨 UI Components
- Beautiful gradient-based design
- Responsive mobile-friendly layout
- Real-time loading states
- Smooth animations

### 🔧 Technical
- Full TypeScript
- Zero linter errors
- localStorage persistence
- Proper error handling

---

## 🐛 Troubleshooting

**Scripts not generating?**
- Check Echo App ID in `.env.local`
- Refresh page and try again

**Videos taking long?**
- Veo 3 takes 60-120 seconds
- Check browser console for errors

**Lost progress?**
- Everything saves to localStorage
- Refresh should restore state

---

## 📚 Documentation

- **Full Implementation Guide**: See `TIKTOK_AD_CREATOR.md`
- **PRD**: See `PRD.md`
- **Original README**: See `foundry/README.md`

---

## 🎥 Demo Tips

For best demo results:
1. Use clear, well-lit product images
2. Write specific, benefit-focused descriptions
3. Give actionable feedback ("more energetic", "focus on price")
4. Show iteration count increasing
5. Generate multiple scripts to show variety

---

## 💡 Example Products to Try

- **Wireless Headphones**: "Premium noise-cancelling earbuds perfect for commuters and remote workers"
- **Water Bottle**: "Insulated stainless steel bottle that keeps drinks cold for 24 hours"
- **Phone Case**: "Ultra-protective phone case with built-in card holder and kickstand"
- **Skincare Product**: "Hydrating face serum with vitamin C for glowing skin"

---

**Ready to create some TikTok ads? Let's go! 🚀**

