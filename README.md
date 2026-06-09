## 🚀 Woodpecker CI/CD default config

Place `.woodpecker.yml` file to GH repo root path

> [!WARNING]
> DO NOT MODIFY THE FILE JUST RENAME TO `.woodpecker.yml`

```yaml
# .woodpecker.yml
# Ezt a fájlt a tanuló a React projekt gyökerébe teszi.
# Push után Woodpecker automatikusan buildel és deployol.

steps:
  - name: build
    image: node:22-alpine
    commands:
      - npm ci
      - VITE_BASE_PATH=frontend/$CI_REPO_OWNER npm run build
  - name: deploy
    image: alpine
    commands:
      - apk add --no-cache curl tar
      - |
        tar -cz -C dist . | curl -f -X POST "http://deploy-api:3000/deploy/$CI_REPO_OWNER" \
          -H "Content-Type: application/octet-stream" \
          --data-binary @-
    when:
      branch: main
```
