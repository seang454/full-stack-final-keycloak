# Keycloak Custom Theme Deployment Guide

## 📁 Required Folder Structure

```
keycloak/
└── themes/
    └── keycloakify-starter-theme.jar  ← Your custom theme JAR
```

## ✅ Correct JAR Internal Structure

Your JAR file MUST have this structure inside:

```
keycloakify-starter-theme.jar
├── META-INF/
│   └── MANIFEST.MF
└── keycloakify-starter/              ← Theme name at ROOT level
    └── login/                         ← Theme type
        ├── theme.properties           ← REQUIRED!
        ├── messages/
        │   ├── messages_en.properties
        │   └── ...
        ├── resources/
        │   ├── css/
        │   ├── img/
        │   ├── js/
        │   └── dist/
        └── *.ftl (FreeMarker templates)
```

## ❌ Common Mistakes to Avoid

### Wrong Structure #1 - Extra "theme" folder:

```
❌ WRONG:
your-theme.jar
└── theme/                    ← Extra folder breaks it!
    └── keycloakify-starter/
```

### Wrong Structure #2 - Missing theme.properties:

```
❌ WRONG:
your-theme.jar
└── keycloakify-starter/
    └── login/
        └── (no theme.properties file)
```

## 🔧 How to Verify Your JAR Structure

### On Linux/VM:

```bash
unzip -l keycloakify-starter-theme.jar | head -30
```

### On Windows:

```powershell
Add-Type -AssemblyName System.IO.Compression.FileSystem
[System.IO.Compression.ZipFile]::OpenRead(".\keycloakify-starter-theme.jar").Entries.FullName | Select-Object -First 30
```

**Expected output:**

```
META-INF/
META-INF/MANIFEST.MF
keycloakify-starter/login/theme.properties    ← GOOD!
keycloakify-starter/login/login.ftl
keycloakify-starter/login/resources/css/login.css
...
```

**NOT:**

```
theme/keycloakify-starter/login/...           ← BAD!
```

## 🚀 Deployment Steps

### 1. Build Your Theme (in your Keycloakify project)

```bash
npm run build-keycloak-theme
# or
mvn clean package
```

### 2. Copy JAR to This Directory

```bash
# Copy the built JAR to this directory
cp path/to/your/dist/keycloak-theme.jar ./keycloak/themes/keycloakify-starter-theme.jar
```

### 3. Commit and Push

```bash
git add keycloak/themes/keycloakify-starter-theme.jar
git commit -m "Update Keycloak custom theme"
git push origin master
```

### 4. GitHub Actions Will Auto-Deploy

The `.github/workflows/deploy.yml` will automatically deploy to your VM.

### 5. Restart Keycloak (on VM)

```bash
cd ~/docuhub-infra
docker-compose restart kc-docuhub-morning
```

### 6. Apply Theme in Keycloak Admin Console

1. Go to: `https://keycloak.docuhub.me/admin/`
2. Login: `kcadmin` / `qwer`
3. Select your realm (e.g., "docuapi") from top-left dropdown
4. Navigate to: **Realm Settings** → **Themes** tab
5. Set **Login theme** to: `keycloakify-starter`
6. Click **Save**

### 7. Test

Open an incognito window and try logging in. Your custom theme should appear!

## 🐛 Troubleshooting

### Theme Doesn't Appear in Dropdown

**Check if JAR is mounted:**

```bash
docker exec kc-docuhub-morning ls -la /opt/keycloak/themes/
```

**Check JAR structure:**

```bash
docker exec kc-docuhub-morning unzip -l /opt/keycloak/themes/keycloakify-starter-theme.jar | head -30
```

**View Keycloak logs:**

```bash
docker logs kc-docuhub-morning 2>&1 | grep -i "theme\|error"
```

**Clear cache and restart:**

```bash
cd ~/docuhub-infra
docker-compose down
docker-compose up -d
```

### Theme Shows But Looks Broken

**Check browser console for errors (F12)**

**Clear browser cache:**

- Chrome: Ctrl+Shift+Delete
- Or use Incognito mode

**Verify resources are loading:**
Check Network tab in browser DevTools

## 📝 theme.properties Template

Minimum required content:

```properties
parent=keycloak

# Optional: Enable multiple languages
locales=en,es,fr,de

# Optional: Dark mode
darkMode=true
```

## 🔐 Docker Compose Configuration

Your `docker-compose.yml` should have:

```yaml
services:
  kc-docuhub-morning:
    image: quay.io/keycloak/keycloak:26.3.1
    volumes:
      - ./keycloak/themes:/opt/keycloak/themes
    environment:
      # Disable theme caching for development
      KC_SPI_THEME_CACHE_THEMES: "false"
      KC_SPI_THEME_CACHE_TEMPLATES: "false"
```

## 📚 Additional Resources

- [Keycloak Theme Documentation](https://www.keycloak.org/docs/latest/server_development/#_themes)
- [Keycloakify Documentation](https://docs.keycloakify.dev/)
- [FreeMarker Template Guide](https://freemarker.apache.org/docs/)

## 🎨 Current Theme

**Theme Name:** `keycloakify-starter`  
**Theme Types:** Login  
**Keycloak Version:** 26.3.1

---

**Last Updated:** October 25, 2024
