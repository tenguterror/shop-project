# OCR Notes — Session 1 (2026-10-14)

## Goal
Get the heat number off a scanned MTR using local tools only. No Python yet.

## Key discovery
The sample certs are SCANNED: image-only PDFs with no text layer
(~600 DPI JPEGs inside). Copy/paste won't work, so OCR is required.

## Tools (WSL / Ubuntu)
sudo apt install poppler-utils tesseract-ocr
- pdftoppm (part of poppler-utils): turns PDF pages into images
- tesseract: OCR, turns an image into text

## Commands that worked
# Render page 3 (Nucor) at 300 DPI as PNG
pdftoppm -f 3 -l 3 -r 300 -png 4_page.pdf ocr/nucor_page
# -> ocr/nucor_page-3.png

# OCR the image
tesseract ocr/nucor_page-3.png ocr/nucor_page-3
# -> ocr/nucor_page-3.txt

# Search for the heat number
grep -n "1202077680" ocr/nucor_page-3.txt

## Result: SUCCESS
Tesseract read the heat number correctly: `Heat # | 1202077680`

## Gotchas (failed through these)
1. pdftoppm's last argument is a PREFIX, not a filename.
   It adds the page number and extension itself (nucor_page -> nucor_page-3.png).
2. Without -png, pdftoppm outputs PPM (big, raw format).
3. Output goes where the OUTPUT argument says, not next to the input.
   Forgot "ocr/" on tesseract and the .txt landed in the project root.
4. Names should tell the truth. Don't call page 3 "page_4".
5. No spaces in code filenames ('get _heat.py' -> get_heat.py).
   Python can't import a module with a space in its name.
6. Habit: PREDICT the output, RUN, then VERIFY with ls.
7. Label = clue, but labels vary, OCR misspells them, and tables put them in headers.

## The lot-number trap (first real parsing lesson)
grep found 3 matches; only 1 was the heat number:
  Lot #: 120207768061   <- lot number CONTAINS the heat number digits
  Heat # | 1202077680   <- the real one
Plain text search matches substrings. A parser must use CONTEXT
(the "Heat #" label) and BOUNDARIES (the whole number, not part of one).

## Open question for next session
If I didn't already know the heat number, what clues on the line
tell me which number is the heat number? How do I avoid grabbing
a number that's only part of a longer one?

## Next steps
- Answer the open question (on paper first)
- Try a SIDEWAYS page (page 1, Searing) and see what Tesseract does
- Set up a venv when the first real Python starts

## Predicitions
- Searing will have garbage since it is sideways 

##To DO
- [ ] rename searing_page-1_rot.txt -> _rot90.txt
- [ ] move notes.md + get_heat.py out of ocr/
- [ ] .gitignore: add *.jpeg, uppercase extensions, ignore ocr/
- [ ] git status shows nucor_page-3.txt is already committed (cert data in repo history) - decide what to do