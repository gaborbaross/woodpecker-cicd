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
      - npm run build
  - name: deploy
    image: alpine
    volumes:
      - /opt/dest_html:/var/www/pages
    commands:
      - mkdir -p /var/www/pages/$CI_REPO_OWNER
      - rm -rf /var/www/pages/$CI_REPO_OWNER/*
      - cp -r dist/. /var/www/pages/$CI_REPO_OWNER/
    when:
      branch: main
```
