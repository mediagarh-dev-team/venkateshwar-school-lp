# Google Apps Script Setup — TVS Admission Enquiry Form

## Step 1 — Create a Google Sheet

1. Go to [sheets.google.com](https://sheets.google.com) and create a new spreadsheet.
2. Name it **TVS Admission Enquiries** (or anything you prefer).
3. In **Row 1**, add these exact column headers (in order):

| A    | B     | C      | D     | E        | F    |
|------|-------|--------|-------|----------|------|
| Name | Email | Mobile | Class | Page URL | Date |

---

## Step 2 — Open Apps Script

1. Inside the sheet, click **Extensions → Apps Script**.
2. Delete any existing code in the editor.
3. Paste the code below and click **Save** (💾).

---

## Apps Script Code

```javascript
const SHEET_NAME = "Sheet1"; // Change if your sheet tab has a different name

function doPost(e) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName(SHEET_NAME);

    const data = JSON.parse(e.postData.contents);

    sheet.appendRow([
      data.name    || "",
      data.email   || "",
      data.mobile  || "",
      data.class   || "",
      data.pageUrl || "",
      new Date()
    ]);

    return ContentService
      .createTextOutput(JSON.stringify({ result: "success" }))
      .setMimeType(ContentService.MimeType.JSON);

  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ result: "error", message: err.message }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

// Optional: test via GET
function doGet() {
  return ContentService
    .createTextOutput("TVS Enquiry endpoint is live.")
    .setMimeType(ContentService.MimeType.TEXT);
}
```

---

## Step 3 — Deploy as Web App

1. Click **Deploy → New deployment**.
2. Click the ⚙️ gear icon → select **Web app**.
3. Fill in:
   - **Description:** TVS Enquiry Form v1
   - **Execute as:** Me
   - **Who has access:** Anyone
4. Click **Deploy**.
5. Copy the **Web App URL** — it looks like:
   ```
   https://script.google.com/macros/s/XXXXXXXXXXXXXXXXXX/exec
   ```

---

## Step 4 — Paste URL in index.html

In `index.html`, find this line inside the `<script>` block:

```js
const APPSCRIPT_URL = "PASTE_YOUR_APPSCRIPT_URL_HERE";
```

Replace `PASTE_YOUR_APPSCRIPT_URL_HERE` with the URL you copied in Step 3.

---

## Sheet Column Reference

| Column | Field     | Source                        |
|--------|-----------|-------------------------------|
| A      | Name      | Form input — name             |
| B      | Email     | Form input — email            |
| C      | Mobile    | Form input — phone            |
| D      | Class     | Form dropdown — class         |
| E      | Page URL  | Auto-captured — window.href   |
| F      | Date      | Auto-set by Apps Script       |

---

## Notes

- Each form submission adds **one new row** at the bottom of the sheet.
- The `Date` column is set server-side by Apps Script (IST timezone by default — change in Apps Script Project Settings if needed).
- If you redeploy after code changes, the URL stays the same — no need to update `index.html` again.
