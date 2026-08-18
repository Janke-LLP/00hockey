# Lace 'Em Up registration — backend setup

The form on `lace-em-up.html` needs a place to send data. This uses a free Google Sheet as the "database," with a small Google Apps Script acting as the receiving endpoint. No paid service, no server to maintain, and exporting to CSV is a built-in Google Sheets feature.

This is a one-time, ~10 minute setup.

## 1. Create the Sheet

1. Go to [sheets.google.com](https://sheets.google.com) and create a new blank spreadsheet. Name it something like **Lace 'Em Up 2026 Registrations**.
2. In row 1, add these column headers, one per cell: `Timestamp`, `Player Name`, `Player DOB`, `Parent Name`, `Parent Email`, `Parent Phone`, `Consent`.

## 2. Add the Apps Script

1. In the Sheet, go to **Extensions → Apps Script**.
2. Delete any starter code in the editor and paste this in:

   ```javascript
   function doPost(e) {
     var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
     var params = e.parameter;

     // Honeypot: if this hidden field has a value, it's almost certainly a bot.
     // Return a fake "success" so bots don't know they were caught, but skip the row.
     if (params.website) {
       return ContentService.createTextOutput(JSON.stringify({ result: 'ok' }))
         .setMimeType(ContentService.MimeType.JSON);
     }

     sheet.appendRow([
       new Date(),
       params.playerName || '',
       params.playerDob || '',
       params.parentName || '',
       params.parentEmail || '',
       params.parentPhone || '',
       params.consent || ''
     ]);

     return ContentService.createTextOutput(JSON.stringify({ result: 'success' }))
       .setMimeType(ContentService.MimeType.JSON);
   }
   ```

3. Click the disk icon (or Ctrl/Cmd+S) to save the project. Give it a name like "Lace Em Up Form Handler" when prompted.

## 3. Deploy it as a Web App

1. Click **Deploy → New deployment**.
2. Click the gear icon next to "Select type" and choose **Web app**.
3. Set:
   - **Execute as:** Me (your Google account)
   - **Who has access:** Anyone
4. Click **Deploy**. Google will ask you to authorize the script the first time — approve it (it's your own script, acting only on your own Sheet).
5. Copy the **Web app URL** it gives you (ends in `/exec`).

## 4. Connect the form to it

Open `lace-em-up.html` and find this line near the top of the `<form>` tag:

```html
<form id="laceForm" class="lace-form" action="PASTE_YOUR_APPS_SCRIPT_WEB_APP_URL_HERE" method="POST" target="hidden_iframe">
```

Replace `PASTE_YOUR_APPS_SCRIPT_WEB_APP_URL_HERE` with the URL you copied. Save, and redeploy the site (push to GitHub as usual).

## 5. Test it

Submit the form yourself with test info. Within a few seconds a new row should appear in the Sheet. If it doesn't show up:

- Double check the URL was pasted in full, including `https://` and ending in `/exec`.
- Make sure the deployment's access is set to "Anyone," not "Only myself."
- Check **Extensions → Apps Script → Executions** (left sidebar) for error logs.

## Exporting to CSV

Any time you want the data as a CSV: open the Sheet, **File → Download → Comma Separated Values (.csv)**. That's the whole export process — no extra tooling needed.

## Notes

- The form has a hidden "honeypot" field to catch basic spam bots. Real visitors never see it; if it's filled in, that submission is silently dropped.
- Because the Apps Script only *writes* data (there's no `doGet` that reads it back), the public web app URL can't be used to pull registration data out — only to add a new row. The Sheet itself, where the real data lives, stays private to your Google account.
- If you'd rather not manage a Google Sheet at all, a Google Form (which auto-populates a Sheet the same way, with an even simpler setup) is a reasonable alternative — just with a more generic look that's harder to match to the site's branding.
