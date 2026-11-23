# Learnspace

Learnspace is a static HTML, CSS, and JavaScript learning community. Firebase provides email/password accounts, real-time database updates, and cloud file storage. The Firebase JavaScript SDK is loaded as browser modules from Google's CDN; no PHP, Node, Composer, or app server is used. Firebase’s CDN module setup is documented [here](https://firebase.google.com/docs/web/alt-setup).

## Connect a Firebase project

1. Create a Firebase project and register a **Web app**.
2. In **Authentication → Sign-in method**, enable **Email/Password**.
3. Create a **Realtime Database**. Copy its URL from the Realtime Database page.
4. Create the default **Cloud Storage** bucket.
5. Copy the web app configuration into `assets/firebase-config.js`. Use the exact `databaseURL` and `storageBucket` shown by Firebase. Set `appId` to the Web App App ID (typically `1:...:web:...`), not the Analytics Measurement ID (`G-...`).
6. In Realtime Database **Rules**, paste and publish `database.rules.json`.
7. In Cloud Storage **Rules**, paste and publish `storage.rules`.
8. Add the domain you use for Learnspace to Authentication’s **Authorized domains**.

Cloud Storage for Firebase currently requires the pay-as-you-go Blaze plan to create or use a bucket. Firebase says no-cost usage can still apply on Blaze; review its [current storage billing requirements](https://firebase.google.com/docs/storage/faqs-storage-changes-announced-sept-2024) before enabling the bucket.

## Run locally

Serve this folder as static files. For example:

```sh
python -m http.server 8000
```

Open <http://127.0.0.1:8000>. Use the same origin in Firebase Authentication’s authorized domains.

The app starts with an empty Firebase database. Create a teacher account, add a profile and publish classes; student accounts can then discover that teacher, message them, and enroll. New registrations and profile data are written to Realtime Database. Other signed-in users see changes through Firebase `onValue` listeners.

## Firebase data layout

- `profiles/{uid}`: public member profile fields
- `classes/{classId}` and `classRoster/{classId}/{studentUid}`: sessions and live enrollment counts
- `enrollments/{studentUid}/{classId}`: a student’s enrollments
- `posts/{postId}`: community posts, likes, and comments
- `inbox/{uid}/{messageId}`: private messages, mirrored into the participants’ inboxes
- `messageReads/{uid}/{messageId}`: per-user read state
- `files/{uid}/{fileId}`: private file metadata; file bytes live in Cloud Storage under `users/{uid}/{fileId}`

The bundled security rules scope profile and class writes, inbox reads, enrollment writes, and file access to signed-in users. Keep those rules published; the app’s Firebase web config is public and is not an access-control mechanism.

Files are limited to 20 MB each. Keep `storage.rules` aligned with any upload limit changes. Cloud Storage download links are generated only after an authenticated user selects one of their own files.
