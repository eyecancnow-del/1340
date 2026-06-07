# Sun Fix Plan

## Problem
- "Different layer" visible on the sun (maroon thick border at edge)
- Caused by `::before` overlay + `inset box-shadow` creating a warm ring

## Solution
1. **Remove** `.ambient-sun::before{...}` pseudo-element (line 320)
2. **Remove** `inset 0 0 40px 20px rgba(255,160,60,0.15)` from box-shadow (line 319)
3. **Add** radial-gradient mask to feather the sun's edge to transparent:
   ```css
   -webkit-mask: radial-gradient(circle, #000 90%, transparent 100%);
   mask: radial-gradient(circle, #000 90%, transparent 100%);
   ```

## Result
- No visible overlay layers — just the photo + glow
- Outer 10% of the sun radius smoothly fades to transparent
- Any maroon edge pixels from the photo are masked away
- Box-shadow glow stays clean (not affected by mask since it's on the same element)

## Files Changed
- `index.html` — line 319-320
