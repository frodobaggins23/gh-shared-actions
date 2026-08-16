# gh-shared-actions

Shared GitHub composite actions used across my projects.

## sftp-deploy

Builds a Node.js project and syncs its build output to a folder on an SFTP host (used for kubac.website hosting on Namecheap).

### Usage

```yaml
on: workflow_dispatch
name: 🚀 Deploy website
env:
  # any project-specific build-time secrets/env vars go here, at job level,
  # so they're available to the install/build step run inside the composite action
  FILE_SERVER_HOST: https://kubac.website
jobs:
  web-deploy:
    name: 🎉 Deploy
    runs-on: ubuntu-latest
    steps:
      - uses: frodobaggins23/gh-shared-actions/sftp-deploy@main
        with:
          dist-path: dist/kubacovy-hory-ng/browser
          remote-path: hory
          ftp-user: ${{ vars.FTP_USER }}
          ftp-password: ${{ secrets.FTP_PASSWORD }}
```

`FTP_USER` must be set as a repository variable (Settings → Secrets and variables → Actions →
Variables) in every calling repo — `frodobaggins23` is a personal account, so there's no
org-level variable to share it from.

### Inputs

| Input             | Required | Default                 | Description                                             |
| ------------------ | -------- | ------------------------ | --------------------------------------------------------- |
| `node-version`    | no       | `22`                    | Node.js version                                          |
| `install-command` | no       | `npm install`           | Dependency install command                               |
| `build-command`   | no       | `npm run build`         | Build command                                             |
| `dist-path`       | **yes**  | —                        | Path to the built output to upload                        |
| `remote-path`     | **yes**  | —                        | Destination folder under `public_html/` on the FTP host  |
| `ftp-host`        | no       | `ftp.kubac.website`     | SFTP host                                                  |
| `ftp-port`        | no       | `21098`                 | SFTP port                                                  |
| `ftp-user`        | **yes**  | —                        | SFTP username — pass `${{ vars.FTP_USER }}`               |
| `ftp-password`    | **yes**  | —                        | SFTP password — pass `${{ secrets.FTP_PASSWORD }}`       |

Project-specific build-time secrets (e.g. an API key baked into the bundle) aren't inputs on this
action — set them as `env:` at the job level in the calling workflow. Job-level `env` vars are
inherited by every step of the job, including the composite action's build step, so they'll be
visible to `npm install`/`npm run build` without needing to be threaded through as inputs.
