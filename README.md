## 🚀 Woodpecker CI/CD default config
Configs for Woodpecker CI/CD React frontend builder & deployer

> [!NOTE]
> Mivel a build-elt frontend-ek nem aldomain-en vannak hanem path-route alapon, ezért
> 2 fájlt módosítani kell még

Place `.woodpecker.yml` file to GH repo root path

> [!WARNING]
> DO NOT MODIFY THE FILE JUST RENAME TO `.woodpecker.yml`

> [!IMPORTANT]
> srx.tsx

```yaml
# .woodpecker.yml
# Ezt a fájlt a tanuló a React projekt gyökerébe teszi.
# Push után a Woodpecker automatikusan buildel és deployol.
# Az első build előtt vár 30 mp-et, hogy az auto-trust script elvégezze a módosítást a repo-n,
# ezt csak egyszer kell megcsinálni
# CI_REPO_OWNER = a tanuló GitHub felhasználóneve

when:
  event: [push, manual]
  branch: main

steps:
  - name: first-run-wait
    image: alpine
    commands:
      - |
        if [ "$CI_PIPELINE_NUMBER" = "1" ]; then
          echo "Első build, várakozás 30 másodpercet..."
          sleep 30
        else
          echo "Nem az első build, folytatás..."
        fi

  - name: build
    image: node:22-alpine
    commands:
      - npm ci
      - VITE_BASE_PATH=frontend/$CI_REPO_OWNER npm run build

  - name: deploy
    image: alpine
    volumes:
      - /opt/dest_html:/var/www/pages
    commands:      
      - mkdir -p /var/www/pages/$CI_REPO_OWNER
      - rm -rf /var/www/pages/$CI_REPO_OWNER/*
      - cp -r dist/. /var/www/pages/$CI_REPO_OWNER/

```
`src/main.tsx` edit:
```diff
createRoot(document.getElementById('root')).render(
  <StrictMode>
+    <BrowserRouter basename={import.meta.env.BASE_URL}>
...
```

`vite.config.js` edit:
```diff
export default defineConfig({
+  base: process.env.VITE_BASE_PATH ? `/${process.env.VITE_BASE_PATH}/` : '/',}
  plugins: [react()],
})
```
