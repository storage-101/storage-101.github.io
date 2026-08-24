# Animation loop recipes — exact sources, timecodes, crops

Every loop was cut from the local source films in `vid/raw/` (NOT the Bynder web
derivatives — that's why frame-matching against the app's streamed films didn't lock:
same films, different encodes). The four "no match" loops all come from vgrow/vstacking.

Recipe used (per loop): trim → crf16 intermediate → optional ping-pong (fwd+reverse
concat) → crop → scale (lanczos) → libx264 main, yuv420p, fps 24, +faststart, crf 28.

| loop     | source film | in (s) | dur (s) | mode | crop w:h:x:y      | encode    |
|----------|-------------|--------|---------|------|-------------------|-----------|
| vlhero   | vgrow       | 57.0   | 5.3     | fwd  | 1280:548:0:60     | 1280x548  |
| vltaxi   | vtaxi101    | 95.3   | 1.15    | pp   | 1280:512:0:100    | 1280x512  |
| vlstk    | vstklaunch  | 11.5   | 1.78    | pp   | 406:302:0:40      | 560x416*  |
| vlflip   | vflip       | 0.3    | 4.3     | fwd  | 560:320:160:150   | 560x320   |
| vlshelf  | vgrow       | 70.7   | 4.3     | fwd  | 900:708:310:6     | 900x708   |
| vldef    | vdef        | 13.25  | 0.8     | pp   | 720:315:0:165     | 720x314   |
| vlvis    | vvista      | 36.3   | 1.75    | pp   | 720:298:0:230     | 720x298   |
| vlubed   | vvista      | 4.65   | 0.85    | pp   | 720:308:0:300     | 720x308   |
| vlstack  | vgrow       | 54.2   | 2.4     | fwd  | 1240:662:20:40    | 1240x662  |
| vlnest   | vstacking   | 50.7   | 1.7     | fwd  | 810:432:430:80    | 810x432   |
| vlmod    | vstacking   | 58.3   | 3.0     | fwd  | 1100:588:90:70    | 1100x588  |
| vlclos   | vstacking   | 52.6   | 2.4     | fwd  | 1100:588:90:90    | 1100x588  |

*vlstk is the only upscale — its source (vstklaunch) is 406x720 SD locally; the
1080x1920 Bynder original is the right re-cut source for that one.

`pp` = ping-pong loop: `split[a][b];[b]reverse[r];[a][r]concat=n=2:v=1` on the trimmed clip.
Full command template:

    ffmpeg -ss $IN -t $DUR -i vid/raw/$SRC.mp4 -an -vf "crop=$CROP,fps=24" -c:v libx264 -crf 16 -preset fast seg.mp4
    ffmpeg -i seg.mp4 -an [-filter_complex pingpong] -vf "scale=$OUT:flags=lanczos" \
      -c:v libx264 -profile:v main -pix_fmt yuv420p -crf 28 -preset slow -movflags +faststart vid/loops/$LOOP.mp4

Graininess note: local raws are SD/720p re-encodes; for a true quality jump re-cut the same
timecodes from the Bynder ORIGINALS (timecodes transfer 1:1 — same edits).

vreeltaxi / vreelugc: sourced from Kreate's Bynder social-post assets (search "reel" /
"Post" / "Story" in brand.kreate.com); originals live there, not in vid/raw.
