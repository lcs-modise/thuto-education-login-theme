

## Login page theme (Keycloak)

The sign-in page's look (campus photo background, **Thuto Education** title, white inputs with
blue and black accents) is a Keycloak login theme built with [Keycloakify](https://keycloakify.dev).
Its source, Dockerfile and realm settings are packaged in **`thuto-theme.zip`** at the repo root.

Inside the zip (`thuto-theme/`):

| Path                               | What it is                                                         |
| ---------------------------------- | ------------------------------------------------------------------ |
| `Dockerfile`                       | Compiles the theme and runs Keycloak 26.8.2 with it on port 4200   |
| `docker/realm-thuto.json`          | Realm `Thuto Education` (login theme `thuto`, client `thuto-ui`)   |
| `src/login/Template.tsx`           | Page layout: top bar, "Thuto Education" title, sign-in card        |
| `src/login/main.css`               | Styling: white inputs, black borders, blue (`#1d4ed8`) accents     |
| `src/login/assets/thuto-hero.jpg`  | Background photo                                                   |
| `src/login/i18n.ts`                | Page text                                                          |
| `vite.config.ts`                   | Theme name `thuto` and its setting `THUTO_HOME_URL`                |

### Preview the login page in Docker (port 4200)

Port 4200 must be free: stop the app container first (`docker rm -f thuto-app`) or any `ng serve`.

1. Unzip the theme (creates a `thuto-theme/` folder):

   ```bash
   tar -xf thuto-theme.zip
   ```

2. Build the theme image. This compiles the theme inside Docker, so no Node, Maven or Java is
   needed on your machine (a few minutes the first time):

   ```bash
   docker build -t thuto-theme thuto-theme
   ```

3. Run it on port 4200 (choose your own admin password):

   ```bash
   docker run -d --name thuto-theme -p 4200:4200 -e KC_BOOTSTRAP_ADMIN_PASSWORD=change-me thuto-theme
   ```

4. Wait until `docker ps` shows the container as `(healthy)` (about 30 seconds), then open the
   sign-in page: <http://localhost:4200/realms/Thuto%20Education/account>

   The admin console is at <http://localhost:4200/admin> (user `admin`). Its own sign-in page uses
   Keycloak's default look: only the `Thuto Education` realm uses the Thuto theme.

5. When you're done, remove it to free port 4200:

   ```bash
   docker rm -f thuto-theme
   ```

### Change the theme

Edit the files in `thuto-theme/src/login/` (to change the photo, save a new one over
`src/login/assets/thuto-hero.jpg`), then rebuild and rerun:

```bash
docker rm -f thuto-theme
docker build -t thuto-theme thuto-theme
docker run -d --name thuto-theme -p 4200:4200 -e KC_BOOTSTRAP_ADMIN_PASSWORD=change-me thuto-theme
```

### Install it on the hosted Keycloak (secure.omangwaio.co.za)

1. Build the theme file (needs Node 20+, Maven and JDK 17), from `thuto-theme/`:

   ```bash
   npm ci
   npm run build-keycloak-theme
   ```

   This creates `dist_keycloak/thuto-keycloak-theme.jar`.

2. Copy `thuto-keycloak-theme.jar` into the server's `/opt/keycloak/providers/` folder and restart Keycloak.
3. Set `THUTO_HOME_URL` on the server to the app's address (where the title and "Back to home" link go).
4. In the admin console: realm **Thuto Education** → **Realm settings → Themes → Login theme → thuto** → Save.

| Theme setting    | Default                  | Used for                             |
| ---------------- | ------------------------ | ------------------------------------ |
| `THUTO_HOME_URL` | `http://localhost:4200/` | "Thuto Education" title and "Back to home" link |
