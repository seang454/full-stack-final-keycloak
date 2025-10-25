# ⚡ Quick Start: Keycloakify Theme Development

## 🎯 Goal

Build a custom Keycloak login theme using React and deploy it to your infrastructure.

---

## 📋 Step-by-Step Checklist

### ✅ Step 1: Create Keycloakify Project (5 minutes)

```bash
# Create new project from official template
npx degit keycloakify/keycloakify-starter my-keycloak-theme
cd my-keycloak-theme

# Install dependencies
npm install

# Start development server
npm run dev
```

🌐 **Open:** http://localhost:5173  
✨ **You can now see and test your theme locally!**

---

### ✅ Step 2: Configure vite.config.ts (2 minutes)

Open `vite.config.ts` and ensure it looks like this:

```typescript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import { keycloakify } from "keycloakify/vite-plugin";

export default defineConfig({
  plugins: [
    react(),
    keycloakify({
      themeName: "keycloakify-starter", // ← Your theme name
      artifactId: "keycloakify-starter-keycloak-theme",
    }),
  ],
});
```

⚠️ **Critical:** Don't add any extra path configurations!

---

### ✅ Step 3: Customize Your Login Page (15-30 minutes)

**Edit:** `src/keycloak-theme/login/pages/Login.tsx`

```tsx
// Add your custom styling, logo, colors, etc.
// See full example in KEYCLOAKIFY_DEVELOPMENT_GUIDE.md
```

**Edit:** `src/keycloak-theme/login/styles.css`

```css
/* Your custom CSS */
body {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}
```

💡 **Tip:** Save and see changes instantly in dev mode!

---

### ✅ Step 4: Build Production JAR (1 minute)

```bash
# Build the theme
npm run build-keycloak-theme

# Output: dist/keycloak-theme.jar
```

---

### ✅ Step 5: Verify JAR Structure (1 minute)

**On Windows:**

```powershell
cd dist
Add-Type -AssemblyName System.IO.Compression.FileSystem
[System.IO.Compression.ZipFile]::OpenRead("$PWD\keycloak-theme.jar").Entries.FullName | Select-Object -First 20
```

**On Linux/Mac:**

```bash
unzip -l dist/keycloak-theme.jar | head -20
```

**Expected (✅ GOOD):**

```
META-INF/
keycloakify-starter/login/theme.properties
keycloakify-starter/login/login.ftl
```

**NOT (❌ BAD):**

```
theme/keycloakify-starter/...  ← Extra folder = broken!
```

---

### ✅ Step 6: Copy to Your Infrastructure Project (1 minute)

```bash
# Copy JAR to your deployment repo
cp dist/keycloak-theme.jar C:\Users\M\Desktop\finalProjectDeploy\keycloak\themes\keycloakify-starter-theme.jar
```

---

### ✅ Step 7: Deploy to VM (2 minutes)

```bash
cd C:\Users\M\Desktop\finalProjectDeploy

# Commit and push
git add keycloak/themes/keycloakify-starter-theme.jar
git commit -m "Add custom Keycloak theme"
git push origin master
```

⏳ **Wait 1-2 minutes** for GitHub Actions to deploy.

---

### ✅ Step 8: Restart Keycloak on VM (1 minute)

```bash
# SSH to your VM
ssh docuapi@your-vm-ip

# Restart Keycloak
cd ~/docuhub-infra
docker-compose restart kc-docuhub-morning

# Wait 30 seconds
sleep 30

# Verify theme is loaded
docker exec kc-docuhub-morning ls -la /opt/keycloak/themes/
```

---

### ✅ Step 9: Enable Theme in Keycloak Admin (2 minutes)

1. **Open:** https://keycloak.docuhub.me/admin/
2. **Login:** `kcadmin` / `qwer`
3. **Select Realm:** `docuapi` (top-left dropdown)
4. **Navigate:** Realm Settings → Themes tab
5. **Set Login theme:** `keycloakify-starter` ⬅️ Your theme!
6. **Click:** Save

---

### ✅ Step 10: Test Your Theme! (1 minute)

1. Open **Incognito/Private browser window**
2. Go to your app's login page
3. **🎉 See your custom theme in action!**

---

## 🎨 Common Customizations

### Add Your Logo

```tsx
// In your Login.tsx
<img src="/logo.png" alt="Company Logo" className="logo" />
```

Put `logo.png` in the `public/` folder.

### Change Colors

```css
/* In styles.css */
:root {
  --primary-color: #667eea;
  --secondary-color: #764ba2;
}

.btn-primary {
  background: var(--primary-color);
}
```

### Add Custom Background Image

```css
body {
  background-image: url("/background.jpg");
  background-size: cover;
  background-position: center;
}
```

### Customize Messages

Edit `src/keycloak-theme/login/messages/en.json`:

```json
{
  "doLogIn": "Sign In",
  "doRegister": "Create Account",
  "usernameOrEmail": "Email Address"
}
```

---

## 🐛 Quick Troubleshooting

| Problem                          | Solution                               |
| -------------------------------- | -------------------------------------- |
| Theme doesn't appear in dropdown | Check JAR structure, restart Keycloak  |
| Changes don't show               | Clear browser cache, use incognito     |
| Build errors                     | `rm -rf node_modules && npm install`   |
| Theme looks broken               | Check browser console (F12) for errors |

---

## 📚 Full Documentation

- **Detailed Guide:** See `KEYCLOAKIFY_DEVELOPMENT_GUIDE.md`
- **Deployment:** See `KEYCLOAK_THEME_SETUP.md`
- **Official Docs:** https://docs.keycloakify.dev/

---

## ⏱️ Total Time: ~30-45 minutes

✅ Project setup: 5 min  
✅ Configuration: 2 min  
✅ Customization: 15-30 min  
✅ Build & Deploy: 10 min

**You're done!** 🎉

---

## 🚀 Next Steps

1. **Customize more pages:** Register, Reset Password, etc.
2. **Add animations** and interactive elements
3. **Test on mobile** devices
4. **Add social login buttons** styling
5. **Implement multi-language** support

---

**Need help?** Check the detailed guides or reach out! 🤝
