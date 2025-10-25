# 🎨 Keycloakify Development Guide - Building Custom Keycloak Themes

## 📋 What is Keycloakify?

**Keycloakify** lets you create Keycloak themes using **React** and modern web development tools. Instead of writing FreeMarker templates, you build with React components!

## 🚀 Quick Start - Create a New Keycloakify Project

### Option 1: Using the Official Starter Template (Recommended)

```bash
# Create new project from template
npx degit keycloakify/keycloakify-starter my-keycloak-theme
cd my-keycloak-theme

# Install dependencies
npm install

# Start development server
npm run dev
```

### Option 2: Add Keycloakify to Existing React Project

```bash
npm install --save keycloakify
```

## 📁 Project Structure

```
my-keycloak-theme/
├── package.json
├── vite.config.ts              ← IMPORTANT: Configure here
├── tsconfig.json
├── src/
│   ├── main.tsx
│   ├── keycloak-theme/         ← Your theme customization
│   │   ├── login/
│   │   │   ├── KcPage.tsx      ← Main entry point
│   │   │   ├── pages/
│   │   │   │   ├── Login.tsx   ← Custom login page
│   │   │   │   ├── Register.tsx
│   │   │   │   └── ...
│   │   │   ├── Template.tsx    ← Layout wrapper
│   │   │   └── theme.properties
│   │   └── account/             ← Account console (optional)
│   └── App.tsx
├── dist/                        ← Build output
│   └── keycloak-theme.jar      ← Generated theme JAR
└── public/
    └── logo.png
```

## ⚙️ Critical Configuration - `vite.config.ts`

This is the **MOST IMPORTANT** file to configure correctly!

### ✅ Correct Configuration (No Extra "theme" Folder)

```typescript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import { keycloakify } from "keycloakify/vite-plugin";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    react(),
    keycloakify({
      // ⚠️ CRITICAL: This becomes your theme folder name in the JAR
      themeName: "keycloakify-starter",

      // Output JAR filename
      artifactId: "keycloakify-starter-keycloak-theme",

      // Don't add any extra path configurations here!
      // The theme should be at JAR root level
    }),
  ],
});
```

### ❌ Common Mistakes to Avoid

```typescript
// ❌ DON'T DO THIS - Creates extra "theme" folder
keycloakify({
  themeName: "keycloakify-starter",
  extraThemeProperties: ["theme"], // ← WRONG!
});

// ❌ DON'T DO THIS - Wrong path configuration
keycloakify({
  themeVersion: "1.0.0",
  groupId: "theme.keycloakify-starter", // ← Adds extra folder!
});
```

## 🎨 Creating Your Custom Login Page

### 1. Main Entry Point - `src/keycloak-theme/login/KcPage.tsx`

```tsx
import { lazy, Suspense } from "react";
import type { KcContext } from "./KcContext";
import KcApp, { defaultKcProps } from "keycloakify/login";

// Lazy load your custom pages
const Login = lazy(() => import("./pages/Login"));
const Register = lazy(() => import("./pages/Register"));
const Template = lazy(() => import("./Template"));

export default function KcPage(props: { kcContext: KcContext }) {
  const { kcContext } = props;

  return (
    <Suspense>
      {(() => {
        switch (kcContext.pageId) {
          case "login.ftl":
            return <Login {...{ kcContext, ...defaultKcProps }} />;
          case "register.ftl":
            return <Register {...{ kcContext, ...defaultKcProps }} />;
          default:
            // Use default Keycloakify pages for others
            return (
              <KcApp
                kcContext={kcContext}
                {...defaultKcProps}
                Template={Template}
              />
            );
        }
      })()}
    </Suspense>
  );
}
```

### 2. Custom Login Page - `src/keycloak-theme/login/pages/Login.tsx`

```tsx
import { useState } from "react";
import type { PageProps } from "keycloakify/login/pages/PageProps";
import type { KcContext } from "../KcContext";
import type { I18n } from "../i18n";

export default function Login(
  props: PageProps<Extract<KcContext, { pageId: "login.ftl" }>, I18n>
) {
  const { kcContext, i18n, doUseDefaultCss, Template, classes } = props;
  const { url, realm, message, usernameHidden, login, auth } = kcContext;
  const { msg, msgStr } = i18n;

  const [isLoginButtonDisabled, setIsLoginButtonDisabled] = useState(false);

  return (
    <Template
      {...{ kcContext, i18n, doUseDefaultCss, classes }}
      headerNode={msg("doLogIn")}
      displayMessage={!messagesPerField.existsError("username", "password")}
    >
      <div className="login-container">
        <div className="login-card">
          <h1>Sign in to your account</h1>

          <form
            id="kc-form-login"
            onSubmit={() => setIsLoginButtonDisabled(true)}
            action={url.loginAction}
            method="post"
          >
            {/* Username/Email Field */}
            <div className="form-group">
              <label htmlFor="username">
                {!realm.loginWithEmailAllowed
                  ? msg("username")
                  : !realm.registrationEmailAsUsername
                  ? msg("usernameOrEmail")
                  : msg("email")}
              </label>
              <input
                tabIndex={1}
                id="username"
                name="username"
                defaultValue={login.username ?? ""}
                type="text"
                autoFocus={true}
                autoComplete="off"
                className="form-control"
              />
            </div>

            {/* Password Field */}
            <div className="form-group">
              <label htmlFor="password">{msg("password")}</label>
              <input
                tabIndex={2}
                id="password"
                name="password"
                type="password"
                autoComplete="off"
                className="form-control"
              />
            </div>

            {/* Remember Me & Forgot Password */}
            <div className="form-options">
              {realm.rememberMe && !usernameHidden && (
                <div className="checkbox">
                  <label>
                    <input
                      tabIndex={3}
                      id="rememberMe"
                      name="rememberMe"
                      type="checkbox"
                      defaultChecked={!!login.rememberMe}
                    />
                    {msg("rememberMe")}
                  </label>
                </div>
              )}

              {realm.resetPasswordAllowed && (
                <a tabIndex={5} href={url.loginResetCredentialsUrl}>
                  {msg("doForgotPassword")}
                </a>
              )}
            </div>

            {/* Submit Button */}
            <button
              tabIndex={4}
              name="login"
              id="kc-login"
              type="submit"
              disabled={isLoginButtonDisabled}
              className="btn btn-primary"
            >
              {msgStr("doLogIn")}
            </button>

            {/* Register Link */}
            {realm.password && realm.registrationAllowed && (
              <div className="register-link">
                <span>New user? </span>
                <a tabIndex={6} href={url.registrationUrl}>
                  {msg("doRegister")}
                </a>
              </div>
            )}
          </form>
        </div>
      </div>
    </Template>
  );
}
```

### 3. Custom Styling - `src/keycloak-theme/login/styles.css`

```css
/* Modern Login Page Styling */
body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto",
    sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}

.login-container {
  width: 100%;
  max-width: 450px;
  padding: 20px;
}

.login-card {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 16px;
  padding: 40px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
}

.login-card h1 {
  margin: 0 0 30px 0;
  font-size: 24px;
  color: #1a1a1a;
  text-align: center;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  font-weight: 500;
  color: #374151;
}

.form-control {
  width: 100%;
  padding: 12px 16px;
  border: 2px solid #e5e7eb;
  border-radius: 8px;
  font-size: 15px;
  transition: all 0.3s ease;
  box-sizing: border-box;
}

.form-control:focus {
  outline: none;
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.form-options {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 24px;
}

.checkbox label {
  display: flex;
  align-items: center;
  font-size: 14px;
  color: #6b7280;
}

.checkbox input {
  margin-right: 8px;
}

.btn-primary {
  width: 100%;
  padding: 14px;
  background: #667eea;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}

.btn-primary:hover:not(:disabled) {
  background: #5568d3;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.4);
}

.btn-primary:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.register-link {
  margin-top: 20px;
  text-align: center;
  font-size: 14px;
  color: #6b7280;
}

.register-link a {
  color: #667eea;
  text-decoration: none;
  font-weight: 600;
}

.register-link a:hover {
  text-decoration: underline;
}
```

## 🔨 Building Your Theme

### Development Mode (Hot Reload)

```bash
# Start dev server - test your theme locally
npm run dev

# Opens at http://localhost:5173
# You can test different Keycloak pages
```

### Build Production JAR

```bash
# Build the theme JAR
npm run build-keycloak-theme

# Output: dist/keycloak-theme.jar
```

### Verify JAR Structure

```bash
# On Linux/Mac
unzip -l dist/keycloak-theme.jar | head -30

# On Windows PowerShell
Add-Type -AssemblyName System.IO.Compression.FileSystem
[System.IO.Compression.ZipFile]::OpenRead(".\dist\keycloak-theme.jar").Entries.FullName | Select-Object -First 30
```

**Expected output:**

```
META-INF/
META-INF/MANIFEST.MF
keycloakify-starter/login/theme.properties    ← GOOD!
keycloakify-starter/login/login.ftl
keycloakify-starter/login/resources/...
```

**NOT:**

```
theme/keycloakify-starter/...  ← BAD! Extra folder
```

## 📦 Package.json Configuration

```json
{
  "name": "my-keycloak-theme",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "build-keycloak-theme": "npm run build && keycloakify build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "keycloakify": "^11.9.3"
  },
  "devDependencies": {
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "@vitejs/plugin-react": "^4.2.0",
    "typescript": "^5.3.0",
    "vite": "^5.0.0"
  }
}
```

## 🎯 theme.properties Configuration

Create `src/keycloak-theme/login/theme.properties`:

```properties
# Parent theme (inherit from base Keycloak theme)
parent=keycloak

# Supported locales
locales=en,es,fr,de,ar,ca,cs,da,el,fi,hu,it,ja,lt,lv,nl,no,pl,pt-BR,pt,ru,sk,sv,th,tr,uk,zh-CN,zh-TW

# Enable dark mode support (optional)
darkMode=true

# Custom properties (optional)
# These can be accessed in your React components
customProp=value
```

## 🚀 Deployment Workflow

### 1. Build the Theme

```bash
cd my-keycloak-theme
npm run build-keycloak-theme
```

### 2. Copy JAR to Your Infrastructure Project

```bash
# Copy to your Keycloak themes directory
cp dist/keycloak-theme.jar /path/to/finalProjectDeploy/keycloak/themes/keycloakify-starter-theme.jar
```

### 3. Commit and Deploy

```bash
cd /path/to/finalProjectDeploy
git add keycloak/themes/keycloakify-starter-theme.jar
git commit -m "Update Keycloak theme"
git push origin master
```

### 4. GitHub Actions Deploys Automatically

Your `.github/workflows/deploy.yml` will auto-deploy to the VM.

### 5. Restart Keycloak on VM

```bash
cd ~/docuhub-infra
docker-compose restart kc-docuhub-morning
```

### 6. Enable in Keycloak Admin

1. Go to: `https://keycloak.docuhub.me/admin/`
2. Realm Settings → Themes
3. Login theme: `keycloakify-starter`
4. Save

## 🎨 Customization Tips

### Access Keycloak Context Data

```tsx
// The kcContext gives you access to Keycloak data
const { url, realm, message, login, auth, social } = kcContext;

// Examples:
url.loginAction; // Form submit URL
realm.name; // Realm name
realm.internationalizationEnabled;
login.username; // Pre-filled username
auth.attemptedUsername; // Last login attempt
social.providers; // Social login providers
message?.summary; // Error messages
```

### Add Custom Images/Assets

```tsx
// Put images in public/ folder
<img src="/logo.png" alt="Logo" />;

// Or import them
import logo from "./assets/logo.png";
<img src={logo} alt="Logo" />;
```

### Customize All Pages

You can customize any Keycloak page:

- `login.ftl` - Login page
- `register.ftl` - Registration
- `login-reset-password.ftl` - Reset password
- `login-update-password.ftl` - Update password
- `login-verify-email.ftl` - Email verification
- `error.ftl` - Error page
- And many more!

## 📚 Resources

- **Keycloakify Docs:** https://docs.keycloakify.dev/
- **GitHub:** https://github.com/keycloakify/keycloakify
- **Storybook Examples:** https://storybook.keycloakify.dev/
- **Discord Community:** https://discord.gg/mJdYJSdcm4

## 🐛 Troubleshooting

### JAR has wrong structure (extra "theme" folder)

**Fix:** Check `vite.config.ts` - remove any extra path configurations

### Theme doesn't show in dropdown

1. Verify JAR structure: `unzip -l your-theme.jar`
2. Check `theme.properties` exists
3. Restart Keycloak container
4. Check logs: `docker logs kc-docuhub-morning`

### Changes don't appear

1. Clear browser cache (Ctrl+Shift+Delete)
2. Use incognito mode
3. Restart Keycloak with cache disabled (already configured in your docker-compose.yml)

### Build errors

```bash
# Clean install
rm -rf node_modules package-lock.json
npm install

# Clear cache
npm run build -- --force
```

## 🎯 Next Steps

1. **Create your Keycloakify project**
2. **Customize the login page** with your branding
3. **Build the JAR** with correct structure
4. **Deploy to your infrastructure**
5. **Enable in Keycloak admin**
6. **Test and iterate!**

---

**Ready to start?** Run `npx degit keycloakify/keycloakify-starter my-theme` and begin customizing! 🚀
