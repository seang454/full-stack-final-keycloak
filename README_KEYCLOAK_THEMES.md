# 🎨 Keycloak Custom Themes - Complete Documentation

Welcome! This guide will help you create, build, and deploy custom Keycloak themes for your infrastructure.

---

## 📚 Documentation Overview

### 🚀 **Quick Start (30 minutes)**

**File:** [`QUICK_START_KEYCLOAKIFY.md`](QUICK_START_KEYCLOAKIFY.md)

Perfect for getting started quickly with Keycloakify. Follow this step-by-step checklist to build and deploy your first custom theme in ~30-45 minutes.

**What you'll learn:**

- ✅ Create a Keycloakify project
- ✅ Customize login page
- ✅ Build theme JAR
- ✅ Deploy to production

---

### 📖 **Complete Development Guide**

**File:** [`KEYCLOAKIFY_DEVELOPMENT_GUIDE.md`](KEYCLOAKIFY_DEVELOPMENT_GUIDE.md)

In-depth guide covering everything about Keycloakify development. Reference this when you need detailed explanations or want to implement advanced features.

**Topics covered:**

- 🎨 Full project structure
- ⚙️ Configuration details
- 🎯 Custom page examples
- 💅 Styling techniques
- 🐛 Troubleshooting guide

---

### 📦 **Deployment & Infrastructure**

**File:** [`KEYCLOAK_THEME_SETUP.md`](KEYCLOAK_THEME_SETUP.md)

How to deploy your theme to your Docker-based Keycloak infrastructure. Covers both building new themes and using existing JARs.

**What you'll learn:**

- 📁 Proper folder structure
- 🚀 GitHub Actions deployment
- 🔧 Keycloak configuration
- ✅ Testing and verification

---

### 📋 **Theme Directory Documentation**

**File:** [`keycloak/themes/README.md`](keycloak/themes/README.md)

Technical reference for the themes directory. Explains JAR structure, troubleshooting, and theme configuration.

**Reference for:**

- ✅ Correct JAR structure
- ❌ Common mistakes
- 🐛 Debugging commands
- 📝 theme.properties configuration

---

## 🎯 What Do You Want to Do?

### I want to build a NEW custom theme

1. **Start here:** [`QUICK_START_KEYCLOAKIFY.md`](QUICK_START_KEYCLOAKIFY.md)
2. **Reference:** [`KEYCLOAKIFY_DEVELOPMENT_GUIDE.md`](KEYCLOAKIFY_DEVELOPMENT_GUIDE.md)
3. **Deploy:** [`KEYCLOAK_THEME_SETUP.md`](KEYCLOAK_THEME_SETUP.md)

### I have an EXISTING theme JAR to deploy

1. **Start here:** [`KEYCLOAK_THEME_SETUP.md`](KEYCLOAK_THEME_SETUP.md) → Option B
2. **Reference:** [`keycloak/themes/README.md`](keycloak/themes/README.md)

### My theme JAR has the WRONG structure

1. **Check:** [`keycloak/themes/README.md`](keycloak/themes/README.md) → JAR Structure section
2. **Fix:** [`KEYCLOAKIFY_DEVELOPMENT_GUIDE.md`](KEYCLOAKIFY_DEVELOPMENT_GUIDE.md) → vite.config.ts section

### My theme doesn't appear in Keycloak dropdown

1. **Debug:** [`KEYCLOAK_THEME_SETUP.md`](KEYCLOAK_THEME_SETUP.md) → Troubleshooting section
2. **Verify:** [`keycloak/themes/README.md`](keycloak/themes/README.md) → Troubleshooting section

---

## ⚡ Quick Reference

### Current Setup

| Item                 | Value                              |
| -------------------- | ---------------------------------- |
| **Keycloak Version** | 26.3.1                             |
| **Container Name**   | `kc-docuhub-morning`               |
| **Theme Name**       | `keycloakify-starter`              |
| **Realm**            | `docuapi`                          |
| **Admin URL**        | https://keycloak.docuhub.me/admin/ |
| **VM Path**          | `~/docuhub-infra/`                 |

### File Locations

```
finalProjectDeploy/
├── README_KEYCLOAK_THEMES.md          ← You are here!
├── QUICK_START_KEYCLOAKIFY.md         ← Start here for new themes
├── KEYCLOAKIFY_DEVELOPMENT_GUIDE.md   ← Full dev guide
├── KEYCLOAK_THEME_SETUP.md            ← Deployment guide
├── docker-compose.yml
└── keycloak/
    └── themes/
        ├── README.md                   ← Technical reference
        └── keycloakify-starter-theme.jar  ← Your theme here
```

---

## 🎨 What is Keycloakify?

**Keycloakify** is a modern tool that lets you build Keycloak themes using **React** instead of FreeMarker templates.

### Why Use Keycloakify?

✅ **Modern Development** - Use React, TypeScript, and modern tools  
✅ **Hot Reload** - See changes instantly during development  
✅ **Component-Based** - Reusable React components  
✅ **Type-Safe** - Full TypeScript support  
✅ **Easy Styling** - Use any CSS framework you like  
✅ **Better DX** - Much better developer experience than FreeMarker

### Traditional vs Keycloakify

| Traditional FreeMarker    | Keycloakify             |
| ------------------------- | ----------------------- |
| Edit `.ftl` files         | Write React components  |
| Restart Keycloak to test  | Hot reload in browser   |
| Complex templating syntax | JSX/TSX syntax          |
| Manual CSS                | Modern CSS frameworks   |
| No type safety            | Full TypeScript support |

---

## 📊 Typical Workflow

### 1️⃣ **Development** (Your machine)

```bash
# Create and develop theme
npx degit keycloakify/keycloakify-starter my-theme
cd my-theme
npm install
npm run dev  # ← Hot reload development
```

### 2️⃣ **Build** (Your machine)

```bash
# Build production JAR
npm run build-keycloak-theme
# Output: dist/keycloak-theme.jar
```

### 3️⃣ **Deploy** (Your infrastructure repo)

```bash
# Copy JAR to infrastructure repo
cp dist/keycloak-theme.jar /path/to/finalProjectDeploy/keycloak/themes/

# Commit and push
git add keycloak/themes/
git commit -m "Update theme"
git push  # ← GitHub Actions deploys automatically
```

### 4️⃣ **Activate** (Keycloak Admin)

```bash
# On VM: Restart Keycloak
docker-compose restart kc-docuhub-morning

# In browser: Enable theme
# Realm Settings → Themes → Login theme: keycloakify-starter
```

### 5️⃣ **Test** (Browser)

```bash
# Open incognito window and test login
# See your beautiful custom theme! 🎉
```

---

## 🐛 Common Issues & Solutions

### Issue: Theme doesn't appear in dropdown

**Solution:**

1. Check JAR structure: `docker exec kc-docuhub-morning unzip -l /opt/keycloak/themes/your-theme.jar | head -20`
2. Should show: `keycloakify-starter/login/theme.properties` (NOT `theme/keycloakify-starter/...`)
3. Restart Keycloak: `docker-compose restart kc-docuhub-morning`

### Issue: JAR has wrong structure (extra "theme" folder)

**Solution:**

- Check `vite.config.ts` in your Keycloakify project
- Remove any extra path configurations
- Rebuild: `npm run build-keycloak-theme`

### Issue: Changes don't show after deploy

**Solution:**

1. Clear browser cache (Ctrl+Shift+Delete)
2. Use incognito/private window
3. Verify JAR was deployed: `docker exec kc-docuhub-morning ls -la /opt/keycloak/themes/`
4. Check file timestamp matches your deployment

### Issue: Theme shows but looks broken

**Solution:**

1. Open browser console (F12) → Check for JS/CSS errors
2. Verify all resources loaded: Network tab in DevTools
3. Check CSS files are present in JAR
4. Ensure `theme.properties` has correct `parent=keycloak`

---

## 🎯 Success Checklist

Before considering your theme deployment complete:

- [ ] JAR file has correct internal structure (no extra folders)
- [ ] `theme.properties` file exists and configured correctly
- [ ] JAR deployed to `keycloak/themes/` directory
- [ ] Committed and pushed to Git
- [ ] GitHub Actions workflow completed successfully
- [ ] Keycloak container restarted
- [ ] Theme appears in Keycloak admin dropdown
- [ ] Theme selected and saved in realm settings
- [ ] Custom theme displays on login page
- [ ] All form fields work correctly
- [ ] Styling looks good on desktop and mobile
- [ ] Error messages display properly
- [ ] Social login buttons styled (if applicable)

---

## 📚 External Resources

### Official Documentation

- **Keycloakify Docs:** https://docs.keycloakify.dev/
- **Keycloak Themes:** https://www.keycloak.org/docs/latest/server_development/#_themes
- **React Documentation:** https://react.dev/

### Examples & Templates

- **Keycloakify Starter:** https://github.com/keycloakify/keycloakify-starter
- **Storybook Examples:** https://storybook.keycloakify.dev/
- **Real-world Examples:** https://github.com/keycloakify/keycloakify/discussions/categories/show-and-tell

### Community

- **Discord:** https://discord.gg/mJdYJSdcm4
- **GitHub Discussions:** https://github.com/keycloakify/keycloakify/discussions

---

## 🚀 Next Steps

### For Beginners

1. Read [`QUICK_START_KEYCLOAKIFY.md`](QUICK_START_KEYCLOAKIFY.md)
2. Create your first theme
3. Deploy and test

### For Advanced Users

1. Explore [`KEYCLOAKIFY_DEVELOPMENT_GUIDE.md`](KEYCLOAKIFY_DEVELOPMENT_GUIDE.md)
2. Customize all login pages
3. Add animations and interactions
4. Implement multi-language support
5. Add custom validation logic

### For DevOps

1. Review [`KEYCLOAK_THEME_SETUP.md`](KEYCLOAK_THEME_SETUP.md)
2. Optimize deployment pipeline
3. Set up automated testing
4. Configure monitoring

---

## 💡 Tips & Best Practices

1. **Always test in incognito** - Avoids browser cache issues
2. **Use version numbers** - Name JARs like `theme-v1.2.3.jar`
3. **Keep themes simple** - Don't overcomplicate
4. **Test all pages** - Login, register, reset password, etc.
5. **Mobile-first** - Design for mobile, then desktop
6. **Accessibility** - Test with screen readers
7. **Performance** - Optimize images and assets
8. **Security** - Don't expose sensitive data

---

**Need help?** Check the relevant guide or reach out to the community! 🤝

**Happy theming!** 🎨✨
