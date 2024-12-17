# Mapgenie.io: No-Login-Markers

This modification allows you to track unlimited markers without requiring a Pro account or logging in. It simulates a logged-in user and stores location data in localStorage, bypassing Map Genie's Pro restrictions. You can save and track more than 100 locations.

## Pre-Setup Instructions

### For Google Chrome, Edge, Brave, Opera:
1. **Open mapgenie.io**  
   Navigate to [mapgenie.io](https://mapgenie.io) in your browser.

2. **Open Developer Tools**  
   Press `Ctrl + Shift + I` (or `Cmd + Option + I` on Mac) to open the Developer Tools (DevTools).

3. **Go to the 'Sources' Tab**  
   Click the **'Sources'** tab in DevTools.

4. **Enable Overrides**  
   - In the top left of DevTools, click **'Overrides'** (it might be hidden behind two arrows pointing left ⏩).
   - Select any folder on your system where you’d like to store the overridden files.
   - Click **'Allow'** at the top of the screen to give DevTools permission to store files in that folder.

5. **Navigate to the Correct File**  
   In the top left of DevTools, click **'Page'** to navigate through the file structure.  
   - Go to **cdn.mapgenie.io** → **js** → **map.js?id=abcxyz** (the exact path may vary, but it should look something like this).

### For Firefox:
1. **Install the Override Add-on**  
   - Look for an extension/add-on like **"Override Page Resources"** or **"Requestly"** in the Firefox Add-ons store.
   
2. **Set up Overrides in Firefox**  
   - After installing the add-on, follow the extension's instructions to set up an override for **map.js**.
   - You may need to specify a path for the override or create a custom rule to intercept the script and apply the modifications.

By following these steps, you’ll be able to override `map.js` and implement your changes without requiring a login or Pro account.


## Setup Instructions

**Step 0: Easy Way (For Beginners)**  
If you find all this too complicated, you can skip the steps and just do the following:

1. Delete everything inside your `map.js` file.
2. Copy and paste content from: https://raw.githubusercontent.com/StackSmithLabs/Map-Genie-No-Login-Markers/refs/heads/main/map.js  
3. Press `Ctrl + S` to save the file.
4. Reload the page, and you’re done!

**Step 1: Add Fake Login Code**
1. Open the `map.js` file.
2. At the very top of the file, right after `( () => {`, add the following code:
```
// Initialize fake user login details
window.user = { 
    hasPro: true,
    id: 1033,
    role: "user",
    locations: JSON.parse(localStorage.getItem('locations')) || {},
    trackedCategoryIds: [12017]
};

// Set gameLocationsCount based on locations
window.user.gameLocationsCount = Object.keys(window.user.locations).length;

// Handle location request
function handleLocationRequest(b, e, k) {
    let locations = JSON.parse(localStorage.getItem("locations")) || {};
    const id = extractIdFromUrl(k);

    if (id) {
        if (e.method === "delete") {
            delete locations[id];
            delete window.user.locations[id];
        } else if (e.method === "put") {
            locations[id] = true;
            window.user.locations[id] = true;
        }

        // Save updated locations to localStorage
        localStorage.setItem("locations", JSON.stringify(locations));
    }
}

// Extract ID from the URL
function extractIdFromUrl(url) {
    const match = url.match(/\/locations\/(\d+)/);
    return match ? match[1] : null;
}
```

**Step 2: Modify the Protocol Check** 
1. In the `map.js` file, find this line:
`C && -1 === ["http", "https", "file"].indexOf(C) ? n(new d("Unsupported protocol " + C + ":",d.ERR_BAD_REQUEST,e)) : b.send(h);`
2. Replace it with the following code: 
`if (C && -1 === ["http", "https", "file"].indexOf(C)) { n(new d("Unsupported protocol " + C + ":", d.ERR_BAD_REQUEST, e)); } else { handleLocationRequest(b, e, k); }`

Step 3: Add Error Handling (Optional)  
If you run into issues, add this code **right under** the function that contains `var k = s(e.baseURL, e.url);`  
1. `if (b.status === 401) { t(i); v(); b = null; return; }`

**Last Step: Save the File**  
1. Press `Ctrl + S` to save your changes in the `map.js` file.

## Troubleshooting  
- Ensure `b.send(h);` is removed.  
- Double-check that the `locations` data is correctly added to **localStorage**.  
- Verify that `handleLocationRequest` is being triggered properly.
- **Check Parameter Variables:**  
  Ensure that the parameter variables (`b`, `e`, `k`) in the `handleLocationRequest` function are correctly set up according to the surrounding code:

  - **`b`** corresponds to `var b = new XMLHttpRequest;`. Ensure that `b` is assigned as an instance of `XMLHttpRequest`.
  - **`e`** corresponds to `e.exports = function(e)`. You’ll need to pass the correct value from whatever `e` is set to in your code (likely the object or data from the function's context).
  - **`k`** corresponds to `var k = s(e.baseURL, e.url);`. Make sure you correctly set `k` based on the `.baseURL` and `.url` properties.

  If the surrounding code uses different variables, you’ll need to update your function call accordingly. For example, if the code is using `handleLocationRequest(x, y, z)` instead of `handleLocationRequest(b, e, k)`, you’ll need to update the function parameters to match.

## Disclaimer  
This modification is for personal use only and bypasses **Map Genie Pro**. Consider purchasing the Pro version to support the developers.
