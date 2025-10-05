# Environment Variables Setup

## Required Environment Variables

Create a `.env.local` file in the `foundry` directory with these variables:

### Location
```
/Users/rohanbutani/Desktop/DivHacks2/foundry/.env.local
```

### Contents
```bash
# Echo SDK Configuration
# Get your app ID from https://echo.merit.systems
ECHO_APP_ID=your_echo_app_id_here

# OpenAI API Key (for TikTok Ad Creator feature)
# Get your API key from https://platform.openai.com/api-keys
OPENAI_API_KEY=sk-proj-...your_openai_api_key_here
```

---

## Step-by-Step Setup

### 1. Get Echo App ID
1. Go to https://echo.merit.systems
2. Sign in or create an account
3. Create a new app or use existing one
4. Copy your App ID
5. Paste it as `ECHO_APP_ID` value

### 2. Get OpenAI API Key
1. Go to https://platform.openai.com/api-keys
2. Sign in to your OpenAI account (or create one)
3. Click "Create new secret key"
4. Name it (e.g., "TikTok Ad Creator")
5. Copy the key (starts with `sk-proj-...` or `sk-...`)
6. Paste it as `OPENAI_API_KEY` value

⚠️ **Important:** Keep your API keys secret! Never commit the `.env.local` file to git.

### 3. Create the File

**Option A: Terminal**
```bash
cd /Users/rohanbutani/Desktop/DivHacks2/foundry
cat > .env.local << EOF
ECHO_APP_ID=your_echo_app_id_here
OPENAI_API_KEY=sk-proj-your_openai_api_key_here
EOF
```

Then edit the file to replace the placeholder values.

**Option B: Text Editor**
1. Open your code editor
2. Navigate to `foundry` folder
3. Create new file named `.env.local`
4. Copy the template above
5. Replace the placeholder values

---

## Testing Your Setup

After adding both keys to `.env.local`:

1. **Restart the dev server** (Ctrl+C, then `npm run dev`)
2. **Refresh your browser** at http://localhost:3002
3. **Try the TikTok Ad Creator**:
   - Upload a product image
   - Enter description
   - Click "Generate 5 Script Ideas"

If you see an error about missing API keys, double-check:
- File is named `.env.local` (not `.env` or `.env.txt`)
- File is in the `foundry` directory
- No spaces around the `=` sign
- Values are not wrapped in quotes
- Dev server was restarted after adding keys

---

## Cost Information

### OpenAI API Costs
The TikTok Ad Creator uses:
- **GPT-4o** for script generation and refinement
- **Vision API** for image analysis

Approximate costs per generation:
- Initial 5 scripts: ~$0.02-0.05 (with image analysis)
- Each refinement: ~$0.01-0.02

Check pricing at: https://openai.com/api/pricing

### Echo Credits
Video generation (Veo 3) uses Echo credits. Check your balance in the app header.

---

## Troubleshooting

**Error: "OpenAI API key not configured"**
- Add `OPENAI_API_KEY` to `.env.local`
- Restart dev server

**Error: "API key not configured" (Echo)**
- Add `ECHO_APP_ID` to `.env.local`
- Restart dev server

**Scripts not generating**
- Check OpenAI API key is valid
- Ensure you have credits/billing set up on OpenAI account
- Check browser console for detailed errors

**Videos not generating**
- Check Echo balance in app header
- Ensure Echo App ID is correct
- Veo 3 takes 60-120 seconds to generate

---

## Example Working Configuration

```bash
ECHO_APP_ID=app_abc123def456ghi789
OPENAI_API_KEY=sk-proj-1234567890abcdefghijklmnopqrstuvwxyz
```

Replace with your actual keys!

---

## Security Best Practices

✅ **DO:**
- Keep `.env.local` in `.gitignore` (already done)
- Use different API keys for development/production
- Rotate keys periodically
- Monitor usage in OpenAI dashboard

❌ **DON'T:**
- Commit `.env.local` to git
- Share keys publicly
- Use production keys in development
- Hardcode keys in source code

