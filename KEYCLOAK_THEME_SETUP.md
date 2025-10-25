# 🎨 Keycloak Custom Theme - Quick Setup Guide

## 📋 Current Status

Your Keycloak deployment is configured and ready for custom themes!

## 📁 Project Structure

```
finalProjectDeploy/
├── docker-compose.yml
├── .github/
│   └── workflows/
│       └── deploy.yml          ← Auto-deploys to VM on push
└── keycloak/
    ├── conf/
    ├── data/
    ├── providers/
    └── themes/                  ← PUT YOUR THEME JAR HERE
        ├── README.md            ← Detailed documentation
        ├── .gitkeep
        └── keycloakify-starter-theme.jar  ← Your custom theme
```

## ✅ Correct Theme JAR Structure

```
keycloakify-starter-theme.jar
├── META-INF/
│   └── MANIFEST.MF
└── keycloakify-starter/        ← Theme folder at ROOT (not inside "theme/")
    └── login/
        ├── theme.properties    ← REQUIRED!
        ├── *.ftl files
        ├── messages/
        └── resources/
```

## 🚀 Deployment Process

### 🎨 Option A: Build Your Own Theme with Keycloakify (Recommended)

**Want to create your own custom theme?**

👉 **See:** [`QUICK_START_KEYCLOAKIFY.md`](QUICK_START_KEYCLOAKIFY.md) - Quick 30-minute guide  
👉 **See:** [`KEYCLOAKIFY_DEVELOPMENT_GUIDE.md`](KEYCLOAKIFY_DEVELOPMENT_GUIDE.md) - Complete development guide

Keycloakify lets you build themes with **React** instead of FreeMarker templates!

---

### 📦 Option B: Use Existing JAR from VM

If you already have a fixed JAR on your VM:

```bash
# On your VM
cd ~/docuhub-infra/keycloak/themes
ls -la

# If keycloak-theme-FIXED.jar exists, use it
```

### Step 2: Copy to Your Local Repository

**From VM to Windows:**

```bash
# On Windows (use WinSCP, FileZilla, or scp)
scp docuapi@your-vm-ip:~/docuhub-infra/keycloak/themes/keycloak-theme-FIXED.jar C:\Users\M\Desktop\finalProjectDeploy\keycloak\themes\keycloakify-starter-theme.jar
```

### Step 3: Commit and Push

```bash
cd C:\Users\M\Desktop\finalProjectDeploy
git add keycloak/themes/
git commit -m "Add Keycloak custom theme with correct structure"
git push origin master
```

### Step 4: GitHub Actions Auto-Deploys

Wait 1-2 minutes for the workflow to complete. Check:
`https://github.com/your-username/your-repo/actions`

### Step 5: Restart Keycloak on VM

```bash
# SSH to your VM
ssh docuapi@your-vm-ip

# Restart Keycloak
cd ~/docuhub-infra
docker-compose restart kc-docuhub-morning

# Wait 30 seconds
sleep 30

# Verify
docker exec kc-docuhub-morning ls -la /opt/keycloak/themes/
```

### Step 6: Enable Theme in Keycloak Admin

1. Open: `https://keycloak.docuhub.me/admin/`
2. Login: `kcadmin` / `qwer`
3. Select realm: **docuapi** (top-left dropdown)
4. Navigate: **Realm Settings** → **Themes** tab
5. Set **Login theme**: `keycloakify-starter`
6. Click **Save**

### Step 7: Test Your Theme

1. Open **Incognito/Private browser**
2. Go to your app's login page
3. You should see your beautiful custom theme! ✨

## 🐛 If Theme Doesn't Show in Dropdown

### Check JAR Structure:

```bash
# On VM
docker exec kc-docuhub-morning unzip -l /opt/keycloak/themes/keycloakify-starter-theme.jar | head -20
```

**You should see:**

```
keycloakify-starter/login/theme.properties  ← GOOD!
keycloakify-starter/login/login.ftl
```

**NOT:**

```
theme/keycloakify-starter/login/...  ← BAD! Extra folder!
```

### If Structure is Wrong:

You need to rebuild your Keycloakify project with the correct configuration.

**In your Keycloakify project's `vite.config.ts` or build config:**

- Ensure no extra "theme" path is added
- Theme name should be at root of JAR

## 📊 Verification Commands

```bash
# Check if Keycloak is running
docker ps | grep keycloak

# Check theme files in container
docker exec kc-docuhub-morning ls -la /opt/keycloak/themes/

# Check Keycloak logs
docker logs kc-docuhub-morning 2>&1 | tail -100

# Verify JAR structure
docker exec kc-docuhub-morning unzip -l /opt/keycloak/themes/keycloakify-starter-theme.jar | grep theme.properties
```

## 🔄 Rebuilding Your Theme

If you need to rebuild your Keycloakify theme with correct structure:

### Find the Issue in Your Build Config

Look for these files in your Keycloakify project:

- `vite.config.ts`
- `keycloakify.config.ts`
- `pom.xml` (if using Maven)

### Common Fix:

```typescript
// vite.config.ts
export default defineConfig({
  plugins: [
    keycloakify({
      themeName: "keycloakify-starter",
      // Don't add extra path prefixes
    }),
  ],
});
```

Then rebuild:

```bash
npm run build-keycloak-theme
```

## 📝 Quick Reference

| Item                 | Value                                |
| -------------------- | ------------------------------------ |
| **Theme Name**       | `keycloakify-starter`                |
| **Keycloak Version** | 26.3.1                               |
| **Container Name**   | `kc-docuhub-morning`                 |
| **Themes Directory** | `/opt/keycloak/themes/`              |
| **Admin URL**        | `https://keycloak.docuhub.me/admin/` |
| **Realm**            | `docuapi`                            |

## 🎯 Success Checklist

- [ ] JAR file has correct internal structure (no extra "theme/" folder)
- [ ] JAR copied to `keycloak/themes/` directory
- [ ] Committed and pushed to Git
- [ ] GitHub Actions deployed successfully
- [ ] Keycloak container restarted
- [ ] Theme appears in admin dropdown
- [ ] Theme selected and saved in realm settings
- [ ] Custom theme appears on login page

---

**Need Help?** Check `keycloak/themes/README.md` for detailed troubleshooting!
