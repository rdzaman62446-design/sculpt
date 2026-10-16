# Selfie Sculptor

Turn a front-camera selfie into a 3D relief bust you can spin, restyle and 3D print.

- **Take a selfie** opens the front camera on phones; **Choose a photo** works anywhere.
- Face tracking ([MediaPipe Face Landmarker](https://ai.google.dev/edge/mediapipe/solutions/vision/face_landmarker)) runs entirely in the browser — photos never leave the device.
- The 468 detected face points are aligned to a neutral pose, eyes and mouth are closed like a classical statue, the rim is extruded into a solid back, then the mesh is subdivided and smoothed.
- Finishes: clay, marble, bronze, or painted with your photo.
- Export a PNG snapshot or a watertight STL (millimetres, zipped).

## Run it

It's a static page. Serve the folder over HTTP (or HTTPS for camera capture on phones):

```sh
python3 -m http.server 8000
# open http://localhost:8000
```

GitHub Pages works too: Settings → Pages → deploy from the default branch.

## Files

| File | What it is |
| --- | --- |
| `index.html` | The whole app (three.js r128 and MediaPipe tasks-vision 0.10.14 load from CDNs) |
| `vision_wasm_internal.js` / `.wasm` | MediaPipe vision runtime |
| `face_landmarker_model.wasm` | The face landmarker model (`face_landmarker.task`, renamed so static hosts serve it) |

## Limits

A single photo only shows the front of the face, so the result is a relief mask rather than a full head, and depth is estimated from one image. Raise **Relief depth** if it looks flat.
