# Habitly

## Upload to GitHub
Upload `index.html` to the root of your Habitly repository.

## Firebase Authentication
Enable Google under Authentication > Sign-in method. Add your exact GitHub Pages hostname, such as `YOUR-USERNAME.github.io`, under Authentication > Settings > Authorized domains.

## Firestore
Create a Firestore Database. Open Firestore Database > Rules, replace the default rules with the contents of `firestore.rules`, and publish.

The app uses Firebase Google popup authentication and stores each user in `users/{Firebase UID}`.
