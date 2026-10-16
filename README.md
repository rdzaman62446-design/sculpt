# Selfie Sculptor

Scan your head, or your whole body clothes and all, with your phone's front camera and watch it turn into a sculpture as you move.

**Live: https://rdzaman62446-design.github.io/sculpt/**

## Live head scan

1. Tap **Scan my head** and allow the camera.
2. **Look straight at the camera** and hold still. Until then the sculpture mirrors your face in real time.
3. **Slowly roll your head in a circle** until the ring around your face is full, then **turn all the way left and right**. The bust builds up on screen as you go.
4. It finishes on its own, or tap **Finish sculpt** whenever you like (after 4 views).

How it works, all on your device:

- [MediaPipe Face Landmarker](https://ai.google.dev/edge/mediapipe/solutions/vision/face_landmarker) tracks 468 points on your face in 3D every frame. A rigid fit of those points gives the head's pose, so every frame knows where the camera was relative to your head.
- Whenever the camera reaches a new angle (about 7° from any earlier view), the [selfie segmenter](https://ai.google.dev/edge/mediapipe/solutions/vision/image_segmenter) cuts your outline out of the frame. The app then carves away every bit of a 0.4 cm voxel grid that falls outside that outline (shape-from-silhouette). Hair and the back of the head come from these outlines.
- The tracked face surface carves in the face detail. The grid is smoothed and turned into a closed mesh with surface nets.
- **Painted** colours each part of the sculpture from the video frame that saw it most directly.

## Live body scan

1. Enter **your height** (it sets the real size) and tap **Scan my body**.
2. Prop the phone up at about waist height and step back 2–3 m. The app **talks you through it**: step back until your head and feet are in view, face the phone, hold your arms a little away from your body (an A shape), then a 3-2-1 countdown.
3. **Turn slowly on the spot, all the way round.** It tells you when you're a quarter of the way, halfway and almost there, and tells you to slow down if you go too fast. The figure builds up live on screen.
4. It stops by itself once you're back facing the phone, or tap **Finish sculpt**.

How it works:

- [MediaPipe Pose Landmarker](https://ai.google.dev/edge/mediapipe/solutions/vision/pose_landmarker) tracks 33 body points in 3D and cuts out your full-body outline. The angle of your shoulder and hip line gives how far you've turned.
- Every 5° of turn, your outline is carved into a 1 cm voxel grid sized to your height. Your clothes are part of the outline, so they're part of the sculpture.
- **Loop closure:** when you come back round, your outline matches the very first view again. The app finds that moment, stretches the measured turn so it closes at exactly 360°, and carves everything again with the corrected angles.
- The figure is shown as a 30 cm statuette. The STL is a **1:10 scale figure** (17 cm for a 170 cm person), and **Painted** colours it from the video.

Limits: keep your arms still while turning, or they blur. Hollows (the gap between your legs, under your arms, deep folds) fill in, so stand with your feet a little apart. Detail is about 1 cm, so faces are soft. Use the head scan for that.

## Other modes

- **Selfie relief** / **From a photo**: one photo becomes a relief mask, as before.

Finishes: clay, marble, bronze or painted. You can save a PNG snapshot or a closed STL in millimetres for 3D printing.

## Tips

- Use even, bright light. A plain background helps the outline cut-out.
- Keep your whole head in the frame, including your hair.
- Turning your head works best. Moving the phone around your head also works.
- The back of your head is never seen directly, so its shape comes from your side outlines and a rounded head prior.

## Run it locally

It's a static site. The camera needs HTTPS or `localhost`:

```sh
python3 -m http.server 8000
# open http://localhost:8000
```

## Files

| File | What it is |
| --- | --- |
| `index.html` | The whole app (three.js r128 and MediaPipe tasks-vision 0.10.14 load from CDNs) |
| `canon.js` | MediaPipe's canonical face mesh: 468 reference points and their triangles |
| `vision_wasm_internal.js` / `.wasm` | MediaPipe vision runtime |
| `face_landmarker_model.wasm` | Face landmarker model (`face_landmarker.task`, renamed) |
| `selfie_segmenter.tflite` | Person segmentation model used to cut out your outline (head scan) |
| `pose_landmarker_lite.task` | Body tracking and full-body outline model (body scan) |
