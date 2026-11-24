# WebChat 97

A 90s-style website where you create a username, search for other usernames, and message them in real time.

GitHub Pages only hosts static files, so it can't store accounts or messages by itself. This project uses **Firebase** (free tier) as the backend. Setup takes about 10 minutes.

## Files

- `index.html` - the whole site (HTML, CSS, JS)
- `config.js` - your Firebase settings (you fill this in)
- `firestore.rules` - security rules to paste into Firebase

## 1. Create the Firebase project

1. Go to https://console.firebase.google.com and click **Add project**.
2. In **Build > Authentication > Get started**, open **Sign-in method** and enable **Email/Password**.
3. In **Build > Firestore Database > Create database**, pick any location. Start in **production mode**.
4. Open the **Rules** tab, replace everything with the contents of `firestore.rules`, and click **Publish**.
5. In **Project settings (gear icon) > Your apps**, click the web icon `</>`, register an app, and copy the `firebaseConfig` values.
6. Paste those values into `config.js`.

## 2. Deploy on GitHub Pages

1. Create a new GitHub repository and upload `index.html`, `config.js` and `firestore.rules` to the root.
2. Go to **Settings > Pages**, set **Source** to **Deploy from a branch**, choose `main` and `/ (root)`, and save.
3. After a minute your site is live at `https://YOUR-NAME.github.io/REPO-NAME/`.

## 3. Allow your domain in Firebase

In **Authentication > Settings > Authorized domains**, add `YOUR-NAME.github.io`. Without this, sign-in will fail on the live site.

## How it works

- Usernames are 3-16 lowercase letters and numbers. Behind the scenes, each account signs in with `username@webchat.local` (no real email is used or sent).
- The visitor counter and friends list need the updated rules below.
- Each username gets a profile document in `users/`, which is what search looks up (prefix match).
- A conversation lives at `chats/{userA_userB}` (names sorted alphabetically) with its messages in a `messages` subcollection.
- The rules only let people read and write chats they belong to, and only let you send messages as yourself.

## Good to know

- The Firebase config values in `config.js` are not secret. The security rules protect the data.
- There is no password reset, because there are no real emails. A forgotten password means a new account.
- Friends: click **Add Friend** on a search result. Your friends list is private to you and is a shortcut list; you can still message any username you find.
- Search: results update as you type (prefix match on username).
- Who's Online: every signed-in browser writes a "last seen" time to `presence/{username}` every 30 seconds. Anyone seen in the last ~100 seconds shows as online. Closing a tab without signing out leaves someone listed for up to ~2 minutes.
- Visitor counter: stored in `meta/visitors` in Firestore. It counts each browser once (using `localStorage`), so refreshing doesn't inflate it. Because the site is static, anyone technical could bump it by hand, so treat it as for fun.
- **If you set up the earlier version, re-paste `firestore.rules` into Firebase and click Publish.** The new rules are required for friends and the counter.
