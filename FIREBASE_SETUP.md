# Firebase setup for audience requests

The **Live** tab lets your audience see one setlist on their phones (as tag bubbles, by scanning a QR code) and send you song requests, which pop up in the app in real time. GitHub Pages can only serve files, so the requests go through a small free Firebase database instead. You set it up once, which takes about 15 minutes. Your audience never needs an account or any setup.

What goes into Firebase: only the published setlist's **song titles and tags**, the gig's place, time and image if you add them, which songs were played and how many times each was requested, plus the requests themselves. No chords, keys, capos, links or other setlists. Your GitHub token never leaves your iPad.

The free "Spark" plan is enough. You don't need a credit card. Its limits (100 people connected at the same time, 10 GB of downloads a month) are far above what this needs, because the audience page checks for updates every 15 seconds instead of staying connected.

## 1. Create the project

1. Go to <https://console.firebase.google.com> and sign in with your Google account.
2. Click **Create a project** (or **Add project**). Name it something like `repertoire-live`, then click Continue.
3. Turn **Google Analytics** off, since it isn't needed. Click **Create project**, wait, then click **Continue**.

## 2. Create the database

1. In the left menu, open **Build → Realtime Database**. Make sure it says *Realtime Database*, not *Firestore*. Click **Create Database**.
2. Location: pick **Belgium (europe-west1)**, then click Next.
3. Choose **Start in locked mode** and click **Enable**.
4. At the top of the **Data** tab you'll see the database URL, e.g. `https://repertoire-live-default-rtdb.europe-west1.firebasedatabase.app`. **Copy it** for step 5.

## 3. Paste the security rules

These rules say: anyone may *read* the published setlist and *send* a request while requests are open, but only you can publish, read the requests, or change anything.

1. Still in Realtime Database, open the **Rules** tab.
2. Delete everything in the box and paste the rules below.
3. Replace every `YOUR_EMAIL` (there are eight) with the email address you'll sign in with in step 4, **all lowercase**, keeping the single quotes, e.g. `'me@example.com'`.
4. Click **Publish**.

```json
{
  "rules": {
    "gigs": {
      "$gig": {
        "info": {
          ".read": true,
          ".write": "auth != null && auth.token.email === 'YOUR_EMAIL'"
        },
        "songs": {
          ".read": true,
          ".write": "auth != null && auth.token.email === 'YOUR_EMAIL'"
        },
        "poster": {
          ".read": true,
          ".write": "auth != null && auth.token.email === 'YOUR_EMAIL'",
          ".validate": "newData.child('img').isString() && newData.child('img').val().beginsWith('data:image/jpeg;base64,') && newData.child('img').val().length <= 1000000"
        },
        "visits": {
          ".read": "auth != null && auth.token.email === 'YOUR_EMAIL'",
          ".write": "auth != null && auth.token.email === 'YOUR_EMAIL'",
          "$vid": {
            ".read": true,
            ".write": "newData.exists() && $vid.length >= 16 && $vid.length <= 40",
            ".validate": "newData.hasChildren(['first', 'last', 'n'])",
            "first": { ".validate": "newData.isNumber() && (data.exists() ? newData.val() === data.val() : newData.val() === now)" },
            "last":  { ".validate": "newData.isNumber() && newData.val() === now" },
            "n":     { ".validate": "newData.isNumber() && newData.val() === (data.exists() ? data.val() + 1 : 1)" },
            "$other": { ".validate": false }
          }
        },
        "likes": {
          ".read": "auth != null && auth.token.email === 'YOUR_EMAIL'",
          ".write": "auth != null && auth.token.email === 'YOUR_EMAIL'",
          "$song": {
            "$vid": {
              ".write": "$song.length <= 64 && $vid.length >= 16 && $vid.length <= 40 && root.child('gigs').child($gig).child('info').exists()",
              ".validate": "newData.val() === true"
            }
          }
        },
        "requests": {
          ".read": "auth != null && auth.token.email === 'YOUR_EMAIL'",
          ".write": "auth != null && auth.token.email === 'YOUR_EMAIL'",
          "$req": {
            ".read": true,
            ".write": "!data.exists() && newData.exists() && root.child('gigs').child($gig).child('info').child('open').val() === true",
            ".validate": "newData.hasChild('at') && (newData.hasChild('songId') || newData.hasChild('text'))",
            "at":     { ".validate": "newData.isNumber() && (data.exists() ? newData.val() === data.val() : newData.val() === now)" },
            "songId": { ".validate": "newData.isString() && newData.val().length > 0 && newData.val().length <= 64" },
            "text":   { ".validate": "newData.isString() && newData.val().length > 0 && newData.val().length <= 200" },
            "name":   { ".validate": "newData.isString() && newData.val().length <= 40" },
            "msg":    { ".validate": "newData.isString() && newData.val().length <= 200" },
            "loc": {
              ".validate": "newData.hasChildren(['lat', 'lng'])",
              "lat": { ".validate": "newData.isNumber() && newData.val() >= -90 && newData.val() <= 90" },
              "lng": { ".validate": "newData.isNumber() && newData.val() >= -180 && newData.val() <= 180" },
              "acc": { ".validate": "newData.isNumber()" },
              "$o":  { ".validate": false }
            },
            "status": { ".validate": "auth != null && auth.token.email === 'YOUR_EMAIL' && (newData.val() === 'played' || newData.val() === 'dismissed')" },
            "$other": { ".validate": false }
          }
        }
      }
    }
  }
}
```

## 4. Create your sign-in

1. In the left menu, open **Build → Authentication** and click **Get started**.
2. On the **Sign-in method** tab, click **Email/Password**, switch on the first toggle (*Email/Password*, not *Email link*) and click **Save**.
3. Open the **Users** tab and click **Add user**. Enter the same email you put in the rules and a password of your choice, then click **Add user**.

## 5. Copy the Web API key

1. Click the **gear icon** next to *Project Overview* (top left), then **Project settings**.
2. On the **General** tab, copy the **Web API key** (it starts with `AIza`).
   - If it says there's no key yet, scroll down to *Your apps*, click the **`</>`** (Web) icon, give it any nickname, click **Register app**, then come back to this page, where the key is now shown.

This key isn't a secret. It only identifies the project, and the rules from step 3 are what protect it.

## 6. Connect the app

On the iPad, open Repertoire and tap the **Live** tab:

1. Paste the **Web API key** (step 5) and the **Database URL** (step 2), then tap **Save**.
2. Sign in with the email and password from step 4. You stay signed in on that device.
3. Choose the setlist to show and tap **📣 Publish to audience**.
4. Show the QR code (**⤢ Show big**) or print it. Each profile's link never changes, so a printed QR code keeps working for every gig.

The app remembers where the iPad was when you tap **● Start live** (or when you first publish) as the venue, and marks each request **📍 In the audience** or **⚠ Not in the audience** by comparing it with the phone's location, if the person allowed their phone to share it. Tap **Set here** in the Live tab to set the venue again. The venue location stays on the iPad and is never sent to Firebase.

Before each gig, tap **Clear requests** to start with an empty list, an empty "played" board and no likes.

The Live tab also shows how many phones opened the audience page (👀 phones · visits · today · last hour). A visit is counted when a phone opens the page, or comes back to it, at least 30 minutes after its last counted visit. Tap **Reset** there to start counting from zero, e.g. before a gig.

The audience can tap a ♡ next to the "Now playing" songs (the current one and up to two before it) to like it; the Live tab shows **❤ N** on each liked song and the total, with **Reset** to set them back to zero. **🎶 Now playing** in the Live tab (on by default; **■ Stop** / **▶ Start** / **Clear**) shows the song open in your chord reader and the two before it at the top of the audience page.

## Troubleshooting

- **"Wrong email or password"**: check the user in Authentication → Users. You can reset the password there.
- **"Permission denied — check the Firebase rules"**: the email in the rules must match your sign-in email exactly, in lowercase, and the rules must be published.
- **"Email/Password sign-in is not enabled"**: redo step 4.2.
- **Every request says "📍 No location"**: if you set up Firebase before the location check was added, open Realtime Database → Rules, paste the rules from step 3 again (with your email) and click **Publish**. Until then requests still arrive, just without a location. It's also normal for people who refuse the location prompt.
- **"Published, but the image failed"** or the gig image doesn't show: your rules are from before the gig image was added. Open Realtime Database → Rules, paste the rules from step 3 again (with your email) and click **Publish**, then tap Update audience page.
- **"L'hai chiesta tu" stays on a phone after you deleted the request**: your rules are from before phones could check their own requests. Paste the rules from step 3 again (with your email) and click **Publish**.
- **"Visits aren't counted yet"** in the Live tab: your rules are from before the visit counter. Paste the rules from step 3 again (with your email) and click **Publish**.
- **"Likes aren't counted yet"** in the Live tab, or hearts on the audience page jump back empty: your rules are from before likes. Paste the rules from step 3 again (with your email) and click **Publish**.
- **The audience page says "Nothing on right now"**: nothing is published for that profile. Tap Publish in the Live tab.
