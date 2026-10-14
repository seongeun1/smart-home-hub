# Smart Home Picture Frame

Open `index2.html` in your browser. The original smart home dashboard remains in `index.html`. The page displays a continuous, shuffled 12-second photo slideshow with the current temperature, time, and date overlaid on a transparent background. Photos fit the entire screen without cropping; vertical photos have empty space at the sides. Weather defaults to Atlanta; the clock uses the device timezone.

## Photos from Google Photos

Download your selected photos or an album from https://photos.google.com, extract any ZIP file, then use **Photos** or **Folder** on the frame. Selected photos are copied into IndexedDB in this browser and restored automatically after reloading. New selections are added to the slideshow; reselecting the same file (name, size, and modification time) skips duplicates. **Clear photos** removes the browser copies without changing your original files. Use the same browser profile and page address to access saved photos. Clearing browser data, private browsing, or browser storage eviction can remove them. If storage is unavailable or full, the page reports that new photos are available for the current visit only. Hover over the photo area (or focus a control using Tab) to pause, change photos, or enter full screen.

For automatic loading on page open, put pictures in a local `photos/` directory and add their paths to your existing `CONFIG` object in `config.js`:

```js
PHOTO_URLS: ['photos/family.jpg', 'photos/vacation.jpg'],
SLIDESHOW_SECONDS: 12,
LOCATION_NAME: 'Atlanta',
LATITUDE: 33.7490,
LONGITUDE: -84.3880,
TIME_ZONE: 'America/New_York',
```

Keep any existing config properties. Photo paths must reference actual files; the browser cannot automatically enumerate a directory.

A Google Photos shared album URL is not an image URL or a supported slideshow embed. Live access to your Google Photos library requires a Google Cloud project, OAuth sign-in, and the Google Photos Picker API to select photos. Google media URLs expire, so permanent frame use also requires managing photo retrieval and storage. This page does not implement Google sign-in or live album sync.

Google documentation: https://developers.google.com/photos/picker/guides/get-started-picker

## Watch a local folder

Click **Watch folder** in desktop Chrome or Edge and grant read access. The frame remembers the directory handle in IndexedDB and scans the folder, including subfolders, every 30 seconds while the page is visible. Adding, changing, or removing an image in that folder updates the slideshow automatically. Folder contents are read locally rather than uploaded or copied into browser storage. Only one folder is watched at a time; **Change watched folder** selects a replacement.

On reopening, the saved folder is restored. If the browser requires permission again, click **Reconnect folder**. **Stop watching** forgets the folder without deleting your files. **Clear saved photos** clears only the separate photos imported with **Add photos** or **Add folder**. **Add folder** remains a one-time saved snapshot, not a folder watcher.

If Watch folder is unavailable when opening the HTML directly, serve this directory locally:

```sh
python3 -m http.server 8000 --bind 127.0.0.1
```

Then open http://localhost:8000/index2.html. Keep the same address and browser profile for stored selections; file URLs and localhost have separate browser storage. Safari and Firefox users can use the saved-photo import controls instead.

API reference: https://developer.chrome.com/docs/capabilities/web-apis/file-system-access

Photos play in random rounds: each photo appears once per round, and the order is shuffled again afterward. With multiple photos, round boundaries do not repeat the same photo back-to-back; with three or more, consecutive rounds do not use identical orders. Changing the watched folder contents starts a fresh round.
