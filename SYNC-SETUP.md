# Turn on cross-device sync (free)

By default `index.html` saves data only in the browser you use it in.
To see the **same data on your phone and laptop**, connect it to a free
Firebase project and sign in with Google. No credit card, no cost at this
usage.

Time needed: ~10 minutes, all in the Firebase website.

---

## 1. Create the Firebase project

1. Go to **https://console.firebase.google.com** and sign in with your Google account.
2. Click **Add project** (or **Create a project**).
3. Name it `wedding-ledger`. Continue.
4. **Google Analytics: turn it OFF** (not needed). Create project. Wait for it to finish, then **Continue**.

## 2. Register a web app and copy the config

1. On the project overview page, click the **`</>`** (web) icon — "Add app".
2. App nickname: `wedding-ledger`. **Do not** tick "Firebase Hosting". Click **Register app**.
3. You'll see a code block with `const firebaseConfig = { ... }`. Keep this tab open — you need four values from it:
   - `apiKey`
   - `authDomain`
   - `projectId`
   - `appId`

## 3. Enable Google sign-in

1. Left sidebar → **Build → Authentication** → **Get started**.
2. **Sign-in method** tab → click **Google** → toggle **Enable**.
3. Pick your email as the "support email" → **Save**.

## 4. Allow your GitHub Pages address

1. Still in **Authentication** → **Settings** tab → **Authorized domains**.
2. Click **Add domain** and enter your Pages domain, e.g. `kaif01.github.io`
   (just the domain, no `https://`, no path).
3. Save. (`localhost` is already listed so you can test on your computer.)

## 5. Create the database

1. Left sidebar → **Build → Firestore Database** → **Create database**.
2. Choose **Start in production mode** → **Next**.
3. Pick the location closest to you → **Enable**. Wait for it to create.

## 6. Set the security rules

1. In Firestore Database, open the **Rules** tab.
2. Replace everything there with this, then click **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /wallets/{uid} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```

This means: only the signed-in owner can read or write their own budget.
Nobody else — not other users, not the public — can touch it.

## 7. Put the config into the site

1. Open `index.html` in a text editor.
2. Near the top, find:

```js
   window.FIREBASE_CONFIG = {
     apiKey: "",
     authDomain: "",
     projectId: "",
     appId: ""
   };
```

3. Paste your four values between the quotes, for example:

```js
   window.FIREBASE_CONFIG = {
     apiKey: "AIzaSyD-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
     authDomain: "wedding-ledger-1a2b3.firebaseapp.com",
     projectId: "wedding-ledger-1a2b3",
     appId: "1:123456789012:web:abcdef1234567890"
   };
```

4. Save the file and re-upload it to your GitHub repo (replace the old `index.html`).

## 8. Use it

- Open your site. You'll now see **"Sign in with Google"**.
- Sign in. Your current entries upload to your account.
- On your phone, open the same URL, tap **Sign in with Google**, choose the **same account** — the same data loads. Edits on either device appear on the other within a second.
- **Sign out** is in the top bar. "Use on this device only" (on the sign-in screen) keeps the old passcode-and-local-storage mode.

---

## Questions

**Is the `apiKey` a secret?** No. It's safe to have it in a public repo — it only identifies the project. Your data is protected by the rules in step 6 (only your signed-in account can access `wallets/<your id>`).

**Cost?** Firebase's free "Spark" plan covers this many times over (50,000 reads and 20,000 writes per day free; a wedding budget uses a handful). No card required.

**Want your partner to have access too?** Easiest: share one Google account. Or send me both Google account IDs and I'll widen the rules to allow both.

**Go back to device-only?** Blank out the four config values again and re-upload.
