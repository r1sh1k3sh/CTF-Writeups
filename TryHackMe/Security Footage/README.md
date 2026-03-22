# CTF Write-Up: [Security Footage](https://tryhackme.com/room/securityfootage)

> **Platform:** TryHackMe | **Difficulty:** Medium

---

## 🚩 Flag

<details><summary>Flag</summary><code>flag{5ebf457ea6b2877fdbca2de9ec86f31}</code></details>

---

## 1. Analysis

The challenge description states the hard drives were destroyed — the only artefact left is a network capture. From the title alone, the camera was almost certainly streaming footage over the network rather than writing directly to disk, which means the frames are recoverable from the packet data.

**Task file:** `security-footage-1648933966395.pcap`

The working hypothesis was that the capture contained either a continuous video stream or a sequence of individual JPEG frames transmitted over the wire. File carving would confirm which.

---

## 2. File Carving — foremost

`foremost` was run against the raw PCAP bytes to extract any embedded file signatures:

```bash
foremost -i security-footage-1648933966395.pcap -o Images/
```

Two items were extracted into the output directory — `audit.txt` and a `jpg/` folder containing the recovered frames.

### audit.txt

```
Foremost version 1.5.7 by Jesse Kornblum, Kris Kendall, and Nick Mikus

File: security-footage-1648933966395.pcap
Length: 5 MB (6040893 bytes)

Num   Name (bs=512)    Size    File Offset
0:    00000002.jpg     10 KB   1217
1:    00000023.jpg     10 KB   12147
2:    00000044.jpg     10 KB   22765
...
540:  00011779.jpg      9 KB   6030950

541 FILES EXTRACTED
jpg:= 541
```

541 JPEG files were recovered, each approximately 10 KB and evenly spaced throughout the capture — consistent with individual camera frames being streamed over the network rather than a compressed video container. The filenames reflect their byte offset in the PCAP rather than a sequential index, which would need fixing before video reconstruction.

✅ 541 JPEG frames extracted.

---

## 3. Renaming Frames Sequentially

`ffmpeg`'s image sequence input (`%04d.jpg`) requires a zero-padded numeric sequence starting at 1 — the offset-based names produced by `foremost` would break the pattern entirely. A short loop handled the rename:

```bash
a=1
for f in *.jpg; do
    mv "$f" "$(printf "%04d.jpg" "$a")"
    let a=a+1
done
```

The directory now held `0001.jpg` through `0541.jpg` in order, ready for video assembly.

✅ Frames renamed sequentially.

---

## 4. Reassembling the Video — ffmpeg

The 541 JPEG frames were assembled into an MP4 at 24 fps. `-stream_loop -1` told `ffmpeg` to loop the input if it ran short, and `-t 23` set the duration ceiling — 541 frames at 24 fps yields 22.54 seconds, so 23 ensures no frames are cut at the tail:

```bash
ffmpeg -framerate 24 -stream_loop -1 -i %04d.jpg -t 23 -c:v libx264 -pix_fmt yuv420p flag.mp4
```

```
Input #0, image2, from '%04d.jpg':
  Duration: 00:00:22.54, start: 0.000000, bitrate: N/A
  Stream #0:0: Video: mjpeg (Baseline), yuvj420p(pc, bt470bg/unknown/unknown),
               640x480 [SAR 1:1 DAR 4:3], 24 fps, 24 tbr, 24 tbn

[libx264] profile High, level 3.0, 4:2:0, 8-bit
[out#0/mp4] video:2124KiB audio:0KiB subtitle:0KiB
frame= 552  fps=9.7  q=-1.0  Lsize= 2131KiB  time=00:00:22.91  bitrate= 761.8kbits/s  speed=0.403x
```

```bash
file flag.mp4
flag.mp4: ISO Media, MP4 Base Media v1 [ISO 14496-12:2003]
```

Several frames produced minor mjpeg decoder warnings (`mjpeg: unsupported coding type (ce)`) during encoding — these are non-fatal decode issues in the source JPEGs and did not affect playback. `ffmpeg` processed them gracefully and the output file validated correctly.

✅ `flag.mp4` — valid MP4, 552 frames, 00:00:22.91.

---

## 5. Viewing the Recovered Footage

The assembled video was opened in a media player. Playing through the footage revealed the flag displayed on screen.

**Video:** [flag.mp4](https://github.com/r1sh1k3sh/CTF-Writeups/blob/main/TryHackMe/Security%20Footage/Videos/flag.mp4)

> GitHub is unable to preview this video directly — click the link and select **View raw** to download and play it locally.

✅ Flag recovered from footage.

---

## 6. Flag

> 🚩 `flag{5ebf457ea6b2877fdbca2de9ec86f31}`

---

*Security Footage — TryHackMe | Pwned ✅*
