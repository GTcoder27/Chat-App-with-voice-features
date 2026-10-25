# Chat App with Voice Features 💬🎙️

A real-time one-to-one chat application built with **React, Node.js, Express, MongoDB, Socket.IO, and Cloudinary**. Users can create an account, sign in, chat with other registered users, share images, customize the interface theme, and use voice input to transcribe speech into a message. The backend also includes text-translation and text-to-speech endpoints.

> **Important:** This repository currently contains a frontend API URL pointing to a hosted backend. For local development, configure the frontend to use your local backend as described below.

## Features

- **Authentication:** Sign up, log in, log out, and persistent cookie-based authentication.
- **Real-time messaging:** Send and receive messages using Socket.IO.
- **Image sharing:** Upload and send images in chat; images are stored in Cloudinary.
- **Voice input:** Record audio from the microphone and send it to the backend's speech-recognition integration for transcription.
- **Translation endpoint:** Translate message text into the signed-in user's configured language.
- **Text-to-speech endpoint:** Backend endpoint for generating speech audio from text using the Bhashini pipeline.
- **Profile settings:** Update profile picture and preferred language.
- **Theme customization:** Choose from the available DaisyUI themes.
- **Responsive chat interface:** User sidebar, conversation view, loading skeletons, and toast notifications.

## Tech stack

### Frontend
- React 18
- Vite
- Tailwind CSS and DaisyUI
- Zustand for client state management
- Axios for HTTP requests
- Socket.IO Client for real-time events
- Lucide React and React Hot Toast

### Backend
- Node.js and Express
- MongoDB with Mongoose
- Socket.IO
- JWT authentication stored in an HTTP-only cookie
- bcryptjs for password hashing
- Cloudinary for image storage
- Bhashini API integration for speech recognition and text-to-speech
- Google Translate endpoint for text translation
- dotenv for environment configuration

## Project structure

```text
Chat-App-with-voice-features/
├── backend/
│   ├── src/
│   │   ├── controllers/
│   │   │   ├── auth.controller.js
│   │   │   ├── message.controller.js
│   │   │   └── translateService.js
│   │   ├── lib/
│   │   │   ├── cloudinary.js
│   │   │   ├── db.js
│   │   │   ├── socket.js
│   │   │   └── utils.js
│   │   ├── middleware/
│   │   │   └── auth.middleware.js
│   │   ├── models/
│   │   │   ├── message.model.js
│   │   │   └── user.model.js
│   │   ├── routes/
│   │   │   ├── auth.route.js
│   │   │   └── message.route.js
│   │   └── index.js
│   └── package.json
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── store/
│   │   └── lib/
│   └── package.json
├── package.json
└── README.md
```

## Prerequisites

Install or create the following:

- [Node.js](https://nodejs.org/) (Node.js LTS recommended)
- npm
- A [MongoDB Atlas](https://www.mongodb.com/atlas) cluster or a local MongoDB instance
- A [Cloudinary](https://cloudinary.com/) account
- A Bhashini API key if you want to use the speech-recognition and text-to-speech endpoints

## Setup and installation

### 1. Clone the repository

Replace the URL with your GitHub repository URL:

```bash
git clone https://github.com/<your-username>/Chat-App-with-voice-features.git
cd Chat-App-with-voice-features
```

### 2. Configure backend environment variables

Create `backend/.env`:

```env
PORT=5001
NODE_ENV=development
FRONTEND_URL=http://localhost:5173

MONGO_URI=mongodb+srv://<username>:<password>@<cluster-url>/<database-name>
JWT_SECRET=replace_with_a_long_random_secret

CLOUDINARY_CLOUD_NAME=your_cloudinary_cloud_name
CLOUDINARY_API_KEY=your_cloudinary_api_key
CLOUDINARY_API_SECRET=your_cloudinary_api_secret

BHASHINI_API_KEY=your_bhashini_api_key
```

Replace each placeholder with your own value. URL-encode special characters in your MongoDB password if required. Keep `.env` out of version control.

**MongoDB:** Create a database user and allow your development IP address in Atlas Network Access, or use a local MongoDB connection string.

**Cloudinary:** Get the cloud name, API key, and API secret from your Cloudinary dashboard.

**Bhashini:** The voice-to-text and text-to-speech routes use the Bhashini inference pipeline. Add a valid API key to enable those routes. Speech services may require valid task configuration or service IDs for your account; check the backend route configuration if a request fails.

### 3. Install dependencies

From the project root:

```bash
npm run build
```

The root `build` script installs dependencies in both the backend and frontend. Alternatively, install them separately:

```bash
npm install --prefix backend
npm install --prefix frontend
```

### 4. Configure the frontend API URL

The current `frontend/src/lib/axios.js` uses a hosted API URL. For local development, change its `baseURL` to:

```js
import axios from "axios";

export const axiosInstance = axios.create({
  baseURL: "http://localhost:5001/api",
  withCredentials: true,
});
```

The frontend Socket.IO connection also uses `"/"` as its server URL in `frontend/src/store/useAuthStore.js`. For local development on Vite's port 5173, either configure a Vite proxy for API and Socket.IO traffic or change the Socket.IO URL to `http://localhost:5001`. Ensure the backend Socket.IO CORS configuration in `backend/src/lib/socket.js` permits `http://localhost:5173`.

### 5. Start the backend

Open a terminal:

```bash
cd backend
npm run dev
```

This runs the Express and Socket.IO server using Nodemon. The backend reads `PORT` from `backend/.env`; the example above uses port `5001`.

For a regular start without Nodemon:

```bash
npm start
```

### 6. Start the frontend

Open another terminal:

```bash
cd frontend
npm run dev
```

Open the local URL printed by Vite, normally:

```text
http://localhost:5173
```

Register two accounts in separate browsers or sessions to test one-to-one messaging and real-time delivery.

## How to use the app

1. Open the frontend in your browser.
2. Create an account using your name, email, password, and language if prompted. Passwords must be at least six characters.
3. Log in with your account.
4. Select another registered user from the sidebar.
5. Type a message and press **Send** to send it.
6. Use the image button to select an image and send it in the conversation.
7. Use the microphone button and grant browser microphone permission to record speech. The app sends the recording to the configured speech-recognition endpoint and sends the returned transcript as a chat message.
8. Open profile/settings to update your profile picture or language.
9. Use the theme settings to select a preferred appearance.
10. Log out when finished.

Microphone access generally requires `localhost` or a secure HTTPS origin. Voice features require a working Bhashini configuration and compatible audio format.

## API endpoints

All endpoints are mounted under `/api`. Routes marked **Protected** require a valid login cookie.

### Authentication

| Method | Endpoint | Access | Description |
|---|---|---|---|
| `POST` | `/api/auth/signup` | Public | Create a user account |
| `POST` | `/api/auth/login` | Public | Log in |
| `POST` | `/api/auth/logout` | Public | Clear the login cookie |
| `GET` | `/api/auth/check` | Protected | Get the current authenticated user |
| `PUT` | `/api/auth/update-profile` | Protected | Update profile picture and/or preferred language |

### Messages and voice

| Method | Endpoint | Access | Description |
|---|---|---|---|
| `GET` | `/api/messages/users` | Protected | Get users for the sidebar |
| `GET` | `/api/messages/:id` | Protected | Get messages exchanged with a user |
| `POST` | `/api/messages/send/:id` | Protected | Send a text and/or image message to a user |
| `POST` | `/api/messages/translate` | Protected | Translate text into the current user's configured language |
| `POST` | `/api/messages/asr/voice_to_text` | Current code: unprotected | Send base64-encoded audio for speech recognition |
| `POST` | `/api/messages/tts/text_to_voice` | Current code: unprotected | Convert text to speech |

### Example request bodies

**Sign up** (`POST /api/auth/signup`):

```json
{
  "fullName": "Alex Example",
  "email": "alex@example.com",
  "password": "change-this-password",
  "language": "mr"
}
```

**Send a message** (`POST /api/messages/send/<receiverId>`):

```json
{
  "text": "Hello!",
  "image": null
}
```

For image messages, `image` can contain a base64 data URL supported by Cloudinary.

**Translate text** (`POST /api/messages/translate`):

```json
{
  "text": "Hello, how are you?",
  "source": "en"
}
```

The backend chooses the target language from the authenticated user's profile.

**Voice-to-text** (`POST /api/messages/asr/voice_to_text`):

```json
{
  "base64": "<base64-encoded-audio>"
}
```

**Text-to-speech** (`POST /api/messages/tts/text_to_voice`):

```json
{
  "text": "नमस्कार",
  "user_language": "mr"
}
```

## Real-time messaging

Socket.IO is used to notify connected clients when a new message arrives. The backend tracks connected user IDs and emits:

- `getOnlineUsers` — list of currently connected user IDs.
- `newMessage` — a newly saved message delivered to its recipient.

Messages and user profiles are persisted in MongoDB. Images are uploaded to Cloudinary, and the returned secure URL is stored with the message.

## Troubleshooting

- **MongoDB connection error:** Check `MONGO_URI`, database-user credentials, Atlas IP access rules, and network connectivity.
- **JWT or login problems:** Ensure `JWT_SECRET` is set and restart the backend after changing `.env`.
- **Images fail to upload:** Verify Cloudinary credentials and confirm that the frontend sends a valid image data URL.
- **Frontend calls the wrong server:** Update `frontend/src/lib/axios.js` to use the local backend URL.
- **Socket messages do not arrive:** Check the Socket.IO URL, CORS origin, and that both users are logged in.
- **Microphone is unavailable:** Use localhost or HTTPS and allow microphone permissions in the browser.
- **Voice transcription or TTS fails:** Verify `BHASHINI_API_KEY`, audio format/sample-rate expectations, and Bhashini service configuration.
- **Cookie/authentication issues:** Make sure CORS allows the frontend origin with credentials and that cookie settings match your local or production environment.

## Security and deployment checklist

- Never commit `.env`, MongoDB credentials, JWT secrets, Cloudinary API secrets, or Bhashini API keys.
- Restrict CORS to trusted frontend origins in production.
- Protect voice endpoints with authentication and apply request-size limits and rate limiting.
- Validate and limit uploaded image types and sizes.
- Use HTTPS in production and configure cookie `secure` and `sameSite` settings appropriately.
- Review the Socket.IO CORS configuration and frontend backend URLs before deploying.
- Avoid exposing internal error details to clients in production.
- Test sign-up, login, image uploads, voice input, translation, and real-time delivery end-to-end.

## Contributing

Contributions are welcome. For substantial changes, open an issue first to discuss the proposed improvement.


