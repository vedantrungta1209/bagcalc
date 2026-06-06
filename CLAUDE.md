# Bag Price Calculator — Claude Code Deployment Guide

## Project Overview
A mobile-first woven PP bag pricing calculator for Vee's business.
Single HTML file app with local storage for saved calculations.
Needs to be deployed as a publicly accessible web app.

---

## Your Tasks (run in order)

### 1. Read the existing calculator
The file `bag-calculator.html` is in this same folder.
Read it fully before making any changes.

### 2. Final QA checks — fix these issues
Go through the file and verify/fix all of the following:

**Calculations:**
- Bag weight formula: `W × (L+1) × effGMS × 2.54 ÷ 100`, round UP, add 1g thread
- effGMS = inputGMS + 0.1 (always) + 0.5 if laminated
- Material cost = bagWeight × (matRate + convRate) ÷ 1000
- BOPP cost = (boppWeight × boppRate) ÷ 1000  (no conversion added)
- Liner cost = (linerWeight × linerRate ÷ 1000) + linerConversionPerPiece
- Printing cost = per bag flat rate (single or double side)
- Total cost = matCost + printCost + boppCost + linerCost
- Final price = totalCost × (1 + margin%)
- GST price = finalPrice × (1 + GST%)
- Override: if any value is manually overridden, cascade the overrides upward correctly

**Reverse Calculator:**
- Must work with ONLY size + target price + toggles
- Shows required combined rate (mat+conv per kg) for EVERY standard GMS
- Standard GMS list: 1.6, 1.7, 1.9, 2.0, 2.25, 2.5, 3.0, 3.5, 4.0, 4.5
- If known bag weight is entered → use that weight instead of formula weight
- If known GMS is entered → show only that GMS result
- If conversion rate is entered → split combined rate into mat rate needed
- Material rate is NEVER required as input in reverse calculator

**UI:**
- No fields should have pre-filled/default values on first load
- All inputs start blank with placeholder hints only
- Saved records load correctly when Edit is tapped
- GST toggle works in both main and reverse calculator
- Override mode shows ✎ on overridden values

### 3. Deploy to Netlify (free, no account needed for CLI)

Run these commands in order:

```bash
# Install Netlify CLI
npm install -g netlify-cli

# Create a dist folder with just the one file
mkdir -p dist
cp bag-calculator.html dist/index.html

# Deploy to Netlify (creates a live URL instantly, no login needed)
npx netlify-cli deploy --dir=dist --prod
```

If Netlify CLI asks to log in or authenticate:
- Use `netlify deploy --dir=dist --prod --auth $NETLIFY_AUTH_TOKEN` if token is available
- Otherwise try: `npx netlify deploy --dir=dist` for a draft URL first

### 4. If Netlify is not available, try these alternatives in order:

**Option A — Surge.sh (zero config)**
```bash
npm install -g surge
mkdir -p dist && cp bag-calculator.html dist/index.html
cd dist && surge . bag-calculator.surge.sh
```

**Option B — Python simple server (local network only)**
```bash
mkdir -p dist && cp bag-calculator.html dist/index.html
cd dist && python3 -m http.server 8080
# App available at http://localhost:8080
# On local network: http://YOUR_IP:8080
```

**Option C — Just output a clean final file**
```bash
cp bag-calculator.html dist/index.html
echo "File ready at dist/index.html — open in any browser or upload to any host"
```

### 5. Output a summary
After deployment, print:
- The live URL (or file path if local)
- File size
- Any issues found and fixed during QA
- How to add it to iPhone home screen

---

## Business Rules (do not change these)

| Rule | Value |
|------|-------|
| Length adjustment | Always add +1 inch for bottom stitch |
| GMS adjustment | Always add +0.1 to input GMS |
| Lam GMS adjustment | Add extra +0.5 on top of +0.1 |
| Thread weight | Always add 1 gram to calculated fabric weight |
| Weight rounding | Always round UP (Math.ceil) |
| Conversion cost | Added to material rate per kg, NOT per bag |
| BOPP cost formula | (weight × rate) ÷ 1000 only, no conversion |
| Liner conversion | Flat per piece rate |
| Printing cost | Per bag flat rate |
| GST options | 12% or 18%, added on top of final price |

## Standard GMS chips
1.6, 1.7, 1.9, 2.0, 2.25, 2.5, 3.0, 3.5, 4.0, 4.5 + Custom input field

## Tabs
1. Calculator (main forward calculator)
2. Reverse (solve for material rate or conversion from target price)
3. Saved (timestamped saved calculations with edit/reload)

---

## Tech Stack
- Pure HTML + CSS + Vanilla JS (no frameworks, no build step)
- localStorage for saved records
- Works fully offline after first load
- Mobile-first, tested on iPhone Safari
