# Media stack

Shared `media` namespace: jellyfin, radarr, sabnzbd, sharing the `media-data` PVC mounted at `/data`.

Apps that only need part of the shared volume (jellyfin, sabnzbd) use `subPath` to scope their mount to just that subtree, but keep the **same absolute path** as the full `/data` mount (e.g. sabnzbd mounts `subPath: usenet` at `mountPath: /data/usenet`, not `/usenet`) — so Radarr sees identical paths to what sabnzbd reports, with no remote path mapping needed. Keep this pattern for any future app added here.

## Post-deploy manual setup

Some app settings aren't configurable via manifests/env vars and must be set once through each app's web UI after first deploy (persisted on their `/config` PVC afterwards).

### Folder structure

`media-data` starts as an empty PVC. Kubelet only auto-creates each container's `subPath` mount target (e.g. `usenet/`, `media/`) — it does not create subdirectories apps expect inside those (e.g. `media/movies`). sabnzbd creates its own category folders under `usenet/` automatically when you save them in its config, but neither Jellyfin's nor Radarr's UI will create missing folders — create the `media/` ones once manually via `kubectl exec` into any pod with `/data` mounted before configuring root folders below:

```sh
kubectl exec -n media deploy/radarr -- mkdir -p /data/media/movies
```

Since it's shared storage, a folder only needs to be created once — it's immediately visible to every app that mounts the volume.

### sabnzbd

Config → Folders:
- Temporary Download Folder: `/data/usenet/incomplete`
- Completed Download Folder: `/data/usenet`

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
