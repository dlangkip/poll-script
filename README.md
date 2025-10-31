# Auto Prefill Mobiliser Defaults 

This userscript automatically fills in default values when creating a new Mobiliser on  
[`https://polls.ideliver.ke/mobilizers/create`](https://polls.ideliver.ke/mobilizers/create).

It saves time by pre-selecting fields such as **Group**, **Sub County**, **Ward**, and **Gender** —  
while leaving **Polling Station** and **Super Agent** empty for you to fill manually or set as constants later.

---

## 🧩 Features

- Automatically selects constant dropdown values using the Select2 API.
- Defaults to:
  - **Group:** Traders  
  - **Sub County:** Mbeere North  
  - **Ward:** Evurore  
  - **Gender:** Male  
- **Polling Station** and **Super Agent** can be left blank or given constant values.
- Runs automatically after the form loads.

---

## 🦊 Recommended Browser

**Firefox** is recommended because userscripts generally run more reliably there,  
especially with Select2-based dropdowns like in this form.

---

## ⚙️ Installation Instructions

1. **Install Firefox**  
   If you don’t already have it, download it from [https://www.mozilla.org/firefox/](https://www.mozilla.org/firefox/).

2. **Install a Userscript Manager**  
   - Go to [https://addons.mozilla.org/firefox/addon/violentmonkey/](https://addons.mozilla.org/firefox/addon/violentmonkey/)  
   - Click **Add to Firefox** to install **Violentmonkey**.

3. **Add the Script**
   - Open the Violentmonkey dashboard (`Extensions → Violentmonkey → Dashboard`).
   - Click **+ New Script**.
   - Delete any placeholder text and **paste the script** below:

     ```js
     // ==UserScript==
     // @name         Auto Prefill Mobiliser Defaults 
     // @namespace    https://dlang.benfex.net/
     // @version      1.1
     // @description  Auto-select constant fields in the Mobiliser creation form using Select2 API
     // @match        https://polls.ideliver.ke/mobilizers/create
     // @run-at       document-idle
     // ==/UserScript==

     (function () {
       'use strict';

       const CONSTANTS = {
         group: 'Traders',
         subCounty: 'Mbeere North',
         ward: 'Evurore',
         gender: 'Male',
         pollingStation: '',
         superAgent: ''
       };

       const $ = window.jQuery;

       function select2SetByText(selectSelector, text) {
         if (!text) return false;
         const select = $(selectSelector);
         if (!select.length) return false;
         if (!select.hasClass('select2-hidden-accessible')) return false;

         const option = select.find('option').filter(function () {
           return $(this).text().trim().toLowerCase() === text.toLowerCase();
         });

         if (option.length) {
           select.val(option.val()).trigger('change');
           console.log(`Set ${selectSelector} to "${text}"`);
           return true;
         } else {
           console.warn(`"${text}" not found in ${selectSelector}`);
           return false;
         }
       }

       function applyPrefill() {
         select2SetByText('select[name="role_id"]', CONSTANTS.group);
         const subSet = select2SetByText('select[name="sub_county_id"]', CONSTANTS.subCounty);
         if (subSet) {
           setTimeout(() => {
             const wardSet = select2SetByText('select[name="ward_id"]', CONSTANTS.ward);
             if (!wardSet) {
               console.log('Ward options not ready yet, retrying...');
               let retryCount = 0;
               const retryInterval = setInterval(() => {
                 retryCount++;
                 const done = select2SetByText('select[name="ward_id"]', CONSTANTS.ward);
                 if (done || retryCount > 5) clearInterval(retryInterval);
               }, 1500);
             }
           }, 2000);
         }

         select2SetByText('select[name="gender"]', CONSTANTS.gender);
         select2SetByText('select[name="pstation_code"]', CONSTANTS.pollingStation);
         select2SetByText('select[name="super_agent"]', CONSTANTS.superAgent);
       }

       setTimeout(applyPrefill, 2000);
     })();
     ```

   - Click **Save**.

4. **Visit the Mobiliser Creation Page**  
   Go to [https://polls.ideliver.ke/mobilizers/create](https://polls.ideliver.ke/mobilizers/create).  
   After a short delay (2–3 seconds), the script will automatically fill in your default fields.

---

## 📝 Customization

You can modify default values inside the `CONSTANTS` block:

```js
const CONSTANTS = {
  group: 'Traders',
  subCounty: 'Mbeere North',
  ward: 'Evurore',
  gender: 'Male',
  pollingStation: 'Kanyuambora Primary School',  // optional
  superAgent: 'Jane Doe'                         // optional
};
