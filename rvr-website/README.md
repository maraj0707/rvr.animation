# Scroll-Controlled Video Website

A full-screen website where scrolling controls the client video (`RVR1.mp4`).
Scroll down and the video moves forward. Scroll up and it moves back. The video never plays on its own.

## Files

```
rvr-website/
├── index.html      page structure (one section with the video)
├── style.css       full-screen, sticky video layout
├── script.js       turns the scroll position into the video's time
└── assets/
    └── RVR1.mp4    the client's original video (unchanged)
```

If `assets/RVR1.mp4` is missing, copy the client's original `RVR1.mp4` into the `assets` folder.
The name must be exactly `RVR1.mp4` (capital letters matter on most web hosts).

## How to run it locally

**Best way: run it through a local server.** Pick one of these:

- **VS Code:** install the "Live Server" extension, open the `rvr-website` folder,
  right-click `index.html` and choose **Open with Live Server**.
- **Python:** open a terminal in the `rvr-website` folder and run
  `python -m http.server 8000`, then open <http://localhost:8000> in your browser.
- **Node.js:** open a terminal in the `rvr-website` folder and run
  `npx serve .`, then open the address it prints.

**Quick way:** double-click `index.html`. This also works, but through a server is
closer to how the real website will behave.

## Settings you can change

| What | Where | Default |
|---|---|---|
| How much scrolling plays the whole video (bigger = slower video) | `--scroll-length` in `style.css` | `1200vh` (≈ 11 screen-heights) |
| How soft the motion feels (1 = follows the scroll exactly, lower = smoother) | `EASE` in `script.js` | `0.18` |

## Adding more sections later

Put new sections in `index.html` where it says
`<!-- Future sections go here, below the video section. -->`.
When the user scrolls past the end of the video, the page scrolls normally to them.

## Technical notes about the video

The original file is used exactly as delivered: not compressed, resized or re-encoded.

| Property | Value |
|---|---|
| Resolution | 1920 × 1080 |
| Frame rate | 24 fps (1,117 frames) |
| Length | 46.5 seconds |
| Codec | H.264 (plays in Chrome, Edge, Safari and Firefox) |
| File size | 157 MB (about 27 Mbit/s) |
| Keyframes | 1 per second |

Here's how these affect scroll scrubbing:

- **Keyframes every second.** To show any frame, the browser decodes forward from the
  nearest keyframe before it (up to 23 frames). On normal computers and phones this is done by the graphics hardware and
  takes about 10–40 ms per seek, so scrubbing stays smooth. Scrolling backwards costs a little more than forwards.
  The script waits for each seek to finish before starting the next one, so frames never go blank or freeze.
- **Seeking from memory.** After the page opens, the script downloads the whole video once and plays it from
  memory, so every seek is instant instead of waiting on the network. The first frame shows straight away
  from the normal file while this happens.
- **157 MB file.** This is fine locally, but on a live website visitors on slow connections will wait
  for it to download before scrubbing is fully smooth. That's because of the file size, not the code.
- **The file's index is stored at the end.** The video's index ("moov" box) is at the end of the file, so
  browsers need one extra request before the first frame appears on a live server. This can be fixed
  without any quality change by moving the index to the front ("faststart"). That only rearranges the
  file; the video data stays byte-for-byte the same.
- **Phones in portrait.** The video is 16:9 and fills the screen without stretching, so on a tall phone screen
  the left and right sides are cropped. That's how full-screen "cover" works.
