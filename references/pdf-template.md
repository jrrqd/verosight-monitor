# PDF Report Generation with pdfkit

## Setup
```bash
npm install pdfkit
```

## Basic Structure

```javascript
const PDFDocument = require('pdfkit');
const fs = require('fs');

const doc = new PDFDocument({ size: 'A4', margin: 45 });
doc.pipe(fs.createWriteStream('report.pdf'));

// Page constants
const PAGE_W = 595.28;
const MG = 45;
const CW = PAGE_W - (MG * 2); // 505
const PAGE_H = 841.89;
const BOT = PAGE_H - 55;
```

## Key Functions

### Page Management
```javascript
let pg = 1, y = 0;

function newPage() {
  doc.addPage(); pg++; y = MG;
  // Draw header
  doc.fontSize(7).fillColor('#6B7280').text('SDVM', MG, MG - 12);
  doc.moveTo(MG, MG + 2).lineTo(PAGE_W - MG, MG + 2).strokeColor('#E5E7EB').stroke();
  y = MG + 12;
}

function footer() {
  doc.fontSize(7).fillColor('#6B7280')
    .text(`Page ${pg}`, MG, PAGE_H - 30, { align: 'center', width: CW });
}

function need(h = 60) {
  if (y + h > BOT) { footer(); newPage(); }
}
```

### Info Box (measures text FIRST, then draws box)
```javascript
function infoBox(text, bg = '#FEF2F2', border = '#DC2626') {
  const pad = 12, w = CW - pad * 2;
  doc.fontSize(9);
  const textH = doc.heightOfString(text, { width: w, lineGap: 1 });
  const boxH = textH + pad * 2;
  need(boxH + 10);
  doc.fillColor(bg).rect(MG, y, CW, boxH).fill();
  doc.rect(MG, y, CW, boxH).strokeColor(border).stroke();
  doc.fillColor('#1F2937').text(text, MG + pad, y + pad, { width: w, lineGap: 1 });
  y += boxH + 12;
}
```

### Table (auto row height calculation)
```javascript
function table(headers, rows, colW) {
  const pad = 6, fs = 8, hdrH = 24;
  doc.fontSize(fs);
  
  // Calculate row heights based on content
  const rowH = rows.map(row => {
    let maxH = 22;
    row.forEach((cell, ci) => {
      const h = doc.heightOfString(cell || '', { width: colW[ci] - pad * 2, lineGap: 1 });
      if (h + pad * 2 > maxH) maxH = h + pad * 2;
    });
    return maxH;
  });
  
  const totalH = hdrH + rowH.reduce((a, b) => a + b, 0);
  need(totalH + 15);
  
  // Draw header
  let ty = y;
  doc.fillColor('#EFF6FF').rect(MG, ty, CW, hdrH).fill();
  doc.rect(MG, ty, CW, hdrH).strokeColor('#E5E7EB').stroke();
  let cx = MG + pad;
  headers.forEach((h, i) => {
    doc.fontSize(fs).fillColor('#1F2937').text(h, cx, ty + pad + 2, { width: colW[i] - pad * 2, lineBreak: false });
    cx += colW[i];
  });
  ty += hdrH;
  
  // Draw rows
  rows.forEach((row, ri) => {
    const rh = rowH[ri];
    if (ri % 2 === 0) doc.fillColor('#F9FAFB').rect(MG, ty, CW, rh).fill();
    doc.rect(MG, ty, CW, rh).strokeColor('#E5E7EB').stroke();
    let rx = MG + pad;
    row.forEach((cell, ci) => {
      doc.fontSize(fs).fillColor('#1F2937').text(cell || '', rx, ty + pad, { width: colW[ci] - pad * 2, lineGap: 1 });
      rx += colW[ci];
    });
    ty += rh;
  });
  y = ty + 10;
}
```

### Bar Chart
```javascript
function barChart(items, maxVal) {
  const barH = 24, barMaxW = 300, lblW = 60;
  need(items.length * (barH + 10) + 15);
  
  items.forEach(item => {
    const bw = Math.max(0, (item.value / maxVal) * barMaxW);
    doc.fontSize(9).fillColor('#1F2937').text(item.label, MG, y + 6, { width: lblW });
    doc.fillColor('#F3F4F6').rect(MG + lblW, y, barMaxW, barH).fill();
    if (bw > 0) doc.fillColor(item.color || '#2563EB').rect(MG + lblW, y, bw, barH).fill();
    doc.fontSize(9).fillColor(bw > 55 ? '#FFFFFF' : '#1F2937')
      .text(item.value.toLocaleString(), MG + lblW + 6, y + 6, { width: Math.max(bw - 12, 40) });
    if (item.pct) doc.fontSize(8).fillColor('#6B7280').text(item.pct, MG + lblW + barMaxW + 10, y + 7);
    y += barH + 10;
  });
}
```

## Common Pitfalls

1. **Text overlap** — Always calculate text height BEFORE drawing boxes/containers
2. **Row height** — Pre-calculate all row heights, use the maximum per row
3. **Page breaks** — Check `need()` before drawing any multi-line element
4. **Line gap** — Use `lineGap: 1` option for multi-line text to prevent crowding
5. **Credit info** — Remove internal API details (credits, keys) from client-facing reports
