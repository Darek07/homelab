# Media stack

Shared `media` namespace: jellyfin, radarr, sabnzbd, sharing the `media-data` PVC mounted at `/data`.

## Post-deploy manual setup

Some app settings aren't configurable via manifests/env vars and must be set once through each app's web UI after first deploy (persisted on their `/config` PVC afterwards).

### sabnzbd

Config → Folders:
- Temporary Download Folder: `/usenet/incomplete`
- Completed Download Folder: `/usenet`

Config → Categories:
- `movies` → Folder: `movies`
- `tv` → Folder: `tv`
- `audio` → Folder: `audio`
- `books` → Folder: `books`

### radarr

Settings → Media Management → Root Folders:
- Add `/data/media/movies`

Settings → Download Clients → Add SABnzbd:
- Host: `sabnzbd` (in-cluster service name), Port: `8080`
- API Key: from sabnzbd's Config → General
- Category: `movies`

Settings → Indexers:
- Add indexers directly, or sync from Prowlarr once it's added to the stack
