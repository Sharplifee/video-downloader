# Video Downloader Notes

## Handjob-HD.net / Femdom.zone / Wayshare.cc

**Problem:** yt-dlp hits Cloudflare 403. KVS scan finds nothing.

**Solution (2026-11-12):**
1. Fetch the page with curl_cffi (chrome impersonation).
2. Extract the wayshare.cc link: `https://wayshare.cc/<hash>/<filename>.mp4`
3. Fetch that wayshare page (Referer = original URL).
4. Extract the real CDN URL: `https://sNN.wayshare.cc:PORT/d/<token>/video.mp4`
5. Download with `requests` + `verify=False` (incomplete SSL chain on the CDN).
6. Remux with `ffmpeg -c copy -movflags +faststart`.
7. Upload to release `videos-2026-08-04` (ID 364592193).

This path is now in `.github/workflows/download-video.yml`.

## Beeg

yt-dlp Beeg extractor is currently broken (JSON API 400).
Working path:
- Strip leading zeros from the ID.
- `GET https://store.externulls.com/video/play_url/<id>`
- Build the 1080p m3u8 from the returned key.
- Download every segment individually with retries (standard tools crash on the mixed playlist).
- Concatenate and remux.

## HQPorner

Works via the mydaddy embed path already in the workflow.

## Upload 403

If GITHUB_TOKEN returns 403 on release upload, the workflow retries with the PAT.
If the asset already exists the upload returns 422; the existing browser_download_url is valid.
