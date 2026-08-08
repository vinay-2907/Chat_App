# 💬 Real-Time Chat Application

A modern, full-stack, real-time messaging web application built with the **MERN** stack (MongoDB, Express, React, Node.js), **Socket.IO** for live bidirectional communication, **Cloudinary** for image storage, and **Tailwind CSS + DaisyUI** for customizable UI themes.

---

## 🚀 Features

- ⚡ **Real-Time Messaging**: Instant 1-on-1 messaging powered by **Socket.IO** with low latency.
- 🟢 **Live Online/Offline Status**: Real-time user presence tracking across all active clients.
- 🔐 **Robust Authentication**: Secure JWT-based authentication stored in `httpOnly` cookies with password hashing using `bcryptjs`.
- 📸 **Image & Media Sharing**: Upload and send images in chat conversations powered by **Cloudinary**.
- 👤 **Profile Management**: Profile picture upload and user account settings.
- 🎨 **32 Customizable Themes**: Seamless theme switching with **DaisyUI** and state persistence.
- 📱 **Fully Responsive Design**: Optimized for desktop, tablet, and mobile displays.
- 🛡️ **Protected Routes & Security**: Route protection on frontend with React Router & backend authorization middleware, XSS and CSRF mitigation.
- 🔔 **Interactive Notifications**: Clean toast notifications using `react-hot-toast`.
- 🗃️ **Global State Management**: Fast, lightweight state handling with **Zustand**.

---

## 🛠️ Tech Stack

### Frontend
- **Framework**: [React 19](https://react.dev/) with [Vite](https://vitejs.dev/)
- **Styling**: [Tailwind CSS](https://tailwindcss.com/) & [DaisyUI](https://daisyui.com/)
- **Icons**: [Lucide React](https://lucide.dev/)
- **State Management**: [Zustand](https://zustand-demo.pmnd.rs/)
- **Routing**: [React Router DOM v7](https://reactrouter.com/)
- **HTTP Client**: [Axios](https://axios-http.com/)
- **Real-Time Client**: [Socket.IO Client](https://socket.io/docs/v4/client-api/)
- **Notifications**: [React Hot Toast](https://react-hot-toast.com/)

### Backend
- **Runtime**: [Node.js](https://nodejs.org/) (ES Modules)
- **Framework**: [Express.js](https://expressjs.com/)
- **Database**: [MongoDB](https://www.mongodb.com/) with [Mongoose](https://mongoosejs.com/)
- **Real-Time Server**: [Socket.IO](https://socket.io/)
- **Authentication**: [JSON Web Tokens (JWT)](https://jwt.io/) & [bcryptjs](https://www.npmjs.com/package/bcryptjs)
- **Cloud Storage**: [Cloudinary SDK](https://cloudinary.com/)
- **Utilities**: `cookie-parser`, `cors`, `dotenv`, `nodemon`

---

## 📂 Project Structure

```text
Chat_App/
├── backend/
│   ├── src/
│   │   ├── controllers/
│   │   │   ├── auth.controller.js      # Signup, login, logout, profile update, auth check
│   │   │   └── message.controller.js   # Sidebar users, message history, sending messages
│   │   ├── lib/
│   │   │   ├── cloudinary.js          # Cloudinary configuration
│   │   │   ├── db.js                  # MongoDB connection setup
│   │   │   ├── socket.js              # Socket.IO initialization & user socket mapping
│   │   │   └── utils.js               # JWT token generation helper
│   │   ├── middleware/
│   │   │   └── auth.middleware.js     # Protected route middleware verifying JWT
│   │   ├── models/
│   │   │   ├── message.model.js       # Message Mongoose schema
│   │   │   └── user.model.js          # User Mongoose schema
│   │   ├── routes/
│   │   │   ├── auth.route.js          # /api/auth endpoints
│   │   │   └── message.route.js       # /api/messages endpoints
│   │   └── index.js                   # Express app entry & server listener
│   ├── .env                           # Backend environment variables
│   └── package.json
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   │   ├── skeletons/             # Loading skeletons (Message, Sidebar)
│   │   │   ├── AuthImagePattern.jsx   # Auth page visual side-pattern
│   │   │   ├── ChatContainer.jsx      # Active chat messages & auto-scroll
│   │   │   ├── ChatHeader.jsx         # Selected user header with status
│   │   │   ├── MessageInput.jsx       # Message composer & image attachment
│   │   │   ├── Navbar.jsx             # Top navigation bar
│   │   │   ├── NoChatSelected.jsx     # Empty state placeholder
│   │   │   └── Sidebar.jsx            # User list with online badges & filters
│   │   ├── constants/                 # Theme definitions
│   │   ├── lib/
│   │   │   └── axios.js               # Axios instance with credentials
│   │   ├── pages/
│   │   │   ├── HomePage.jsx           # Main chat layout
│   │   │   ├── LoginPage.jsx          # Login screen
│   │   │   ├── ProfilePage.jsx        # User profile & avatar update
│   │   │   ├── SettingsPage.jsx       # Theme selector & preview
│   │   │   └── SignUpPage.jsx         # User registration screen
│   │   ├── store/
│   │   │   ├── useAuthStore.js        # Auth state, session check, & socket connection
│   │   │   ├── useChatStore.js        # Messages, sidebar users, real-time subscriptions
│   │   │   └── useThemeStore.js       # Active theme state & persistence
│   │   ├── App.jsx                    # Root routes & theme wrapper
│   │   ├── index.css                  # Global Tailwind imports
│   │   └── main.jsx                   # React entry point
│   ├── package.json
│   ├── tailwind.config.js
│   └── vite.config.js
│
├── PROJECT_EXPLANATION.md             # Deep-dive architecture and explanation document
└── README.md                          # Project documentation
```

---

## ⚙️ Environment Variables

Create a `.env` file in the `/backend` directory and configure the following variables:

```env
# Server Port
PORT=5001

# MongoDB Connection String (Local or MongoDB Atlas)
MONGODB_URI=mongodb+srv://<username>:<password>@cluster0.mongodb.net/chat_db?retryWrites=true&w=majority

# JWT Secret Key
JWT_SECRET=your_super_secret_jwt_key_here

# Environment Mode (development | production)
NODE_ENV=development

# Cloudinary Configuration (for image uploads)
CLOUDINARY_CLOUD_NAME=your_cloudinary_cloud_name
CLOUDINARY_API_KEY=your_cloudinary_api_key
CLOUDINARY_API_SECRET=your_cloudinary_api_secret
```

---

## 🚦 Getting Started

### 1. Prerequisites
Ensure you have the following installed:
- [Node.js](https://nodejs.org/) (v18+ recommended)
- [MongoDB](https://www.mongodb.com/) (local instance or cloud cluster like MongoDB Atlas)
- [Cloudinary](https://cloudinary.com/) free account for media storage

---

### 2. Installation

Clone the repository and install dependencies for both the backend and frontend:

```bash
# Clone the repository
git clone https://github.com/your-username/Chat_App.git
cd Chat_App

# Install backend dependencies
cd backend
npm install

# Install frontend dependencies
cd ../frontend
npm install
```

---

### 3. Running in Development Mode

Open two terminal windows/tabs:

**Terminal 1 (Backend):**
```bash
cd backend
npm run dev
```
*The backend server will start on `http://localhost:5001`.*

**Terminal 2 (Frontend):**
```bash
cd frontend
npm run dev
```
*The frontend development server will start on `http://localhost:5173`.*

Open [http://localhost:5173](http://localhost:5173) in your browser.

---

### 4. Building for Production

To build the frontend and serve it through the Express backend in production:

```bash
# Build the frontend
cd frontend
npm run build

# Start the production backend server
cd ../backend
NODE_ENV=production npm start
```

---

## 📡 API Endpoints

### 🔐 Authentication (`/api/auth`)

| Method | Endpoint | Description | Protected |
| :--- | :--- | :--- | :---: |
| `POST` | `/api/auth/signup` | Register a new user | No |
| `POST` | `/api/auth/login` | Authenticate an existing user | No |
| `POST` | `/api/auth/logout` | Log out and clear session cookie | No |
| `PUT` | `/api/auth/update-profile` | Update profile picture (Cloudinary) | **Yes** |
| `GET` | `/api/auth/check` | Verify authenticated session | **Yes** |

### 💬 Messaging (`/api/messages`)

| Method | Endpoint | Description | Protected |
| :--- | :--- | :--- | :---: |
| `GET` | `/api/messages/users` | Fetch all users for the chat sidebar | **Yes** |
| `GET` | `/api/messages/:id` | Fetch chat history with a specific user | **Yes** |
| `POST` | `/api/messages/send/:id` | Send a text/image message to a user | **Yes** |

---

## 🔌 Real-Time Socket Events

| Event Name | Direction | Payload | Description |
| :--- | :--- | :--- | :--- |
| `connection` | Client ➡️ Server | `query: { userId }` | Client initiates connection with their User ID |
| `getOnlineUsers` | Server ➡️ Client | `string[]` (Array of User IDs) | Broadcasts list of currently active user IDs |
| `newMessage` | Server ➡️ Client | `Message` object | Delivers real-time message to target receiver |
| `disconnect` | Client ➡️ Server | — | Removes user from online map and updates clients |

---

## 🎨 Theme Customization

This application includes **32 curated DaisyUI themes** that can be selected from the Settings page:
`light`, `dark`, `cupcake`, `bumblebee`, `emerald`, `corporate`, `synthwave`, `retro`, `cyberpunk`, `valentine`, `halloween`, `garden`, `forest`, `aqua`, `lofi`, `pastel`, `fantasy`, `wireframe`, `black`, `luxury`, `dracula`, `cmyk`, `autumn`, `business`, `acid`, `lemonade`, `night`, `coffee`, `winter`, `dim`, `nord`, `sunset`.

---

## 🔒 Security Best Practices Implemented

- **HTTP-Only Cookies**: Prevents client-side JavaScript access to authentication tokens (XSS protection).
- **Password Hashing**: Passwords stored as salted hashes using `bcryptjs`.
- **CORS Setup**: Restricted cross-origin resource sharing configured specifically for trusted client domains.
- **Route Guards**: Middleware verification ensuring unauthorized requests are rejected before touching database controllers.

---

## 📄 License

This project is licensed under the [ISC License](LICENSE).
