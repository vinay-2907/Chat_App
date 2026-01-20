# Project Functionality Explanation

This document explains how the Chat Application project works, detailing the purpose and functionality of each key file and function in both the backend and frontend.

## Backend (`/backend`)

The backend is built with **Node.js**, **Express**, and **MongoDB**. It provides APIs for authentication and messaging, and uses **Socket.IO** for real-time communication.

### Core Configuration
-   **`src/index.js`**:
    -   **Server Setup**: Initializes the Express app and HTTP server.
    -   **Middleware**: Configures CORS (for frontend communication), JSON parsing, and Cookie parsing.
    -   **Database**: Calls `connectDB()` to connect to MongoDB.
    -   **Routes**: Mounts `authRoutes` at `/api/auth` and `messageRoutes` at `/api/messages`.
    -   **Static Files**: Serves the frontend in production mode.
    -   **Listener**: Starts the server on the defined PORT.

-   **`src/lib/socket.js`**:
    -   **Socket Server**: Initializes `socket.io` with the HTTP server.
    -   **`userSocketMap`**: An object tracking online users (mapping `userId` to `socketId`).
    -   **`getReceiverSocketId`**: Helper function to find a specific user's socket ID for private messaging.
    -   **Connection Handler**: Listens for new connections, adds users to the online map, and broadcasts `getOnlineUsers` to all clients. Handles disconnection cleanup.

### Controllers (Business Logic)

#### `src/controllers/auth.controller.js`
-   **`signup(req, res)`**:
    -   Validates input (name, email, password).
    -   Checks if email already exists.
    -   Hashes password using `bcryptjs`.
    -   Creates a new `User`.
    -   Generates a JWT token and sets it as a cookie.
    -   Returns user data.
-   **`login(req, res)`**:
    -   Finds user by email.
    -   Verifies password using `bcryptjs`.
    -   Generates a new JWT token/cookie.
-   **`logout(req, res)`**:
    -   Clears the JWT cookie to log the user out.
-   **`updateProfile(req, res)`**:
    -   Uploads a new profile picture to **Cloudinary**.
    -   Updates the user's `profilePic` field in the database.
-   **`checkAuth(req, res)`**:
    -   Returns the currently authenticated user (used by frontend to persist session on reload).

#### `src/controllers/message.controller.js`
-   **`getUsersForSidebar(req, res)`**:
    -   Fetches all users from the database *except* the currently logged-in user (for the chat sidebar).
-   **`getMessages(req, res)`**:
    -   Fetches the conversation history between the logged-in user and the selected user (`userToChatId`).
-   **`sendMessage(req, res)`**:
    -   Handles sending a message (text and/or image).
    -   If an image is provided, uploads it to Cloudinary.
    -   Saves the new `Message` to the database.
    -   **Real-time**: Uses `getReceiverSocketId` and `io.to().emit()` to send the message instantly to the receiver if they are online.

### Models (Database Schema)
-   **`src/models/user.model.js`**: Defines the User schema (email, fullName, password, profilePic).
-   **`src/models/message.model.js`**: Defines the Message schema (senderId, receiverId, text, image).

---

## Frontend (`/frontend`)

The frontend is a **React** application (using **Vite**) styled with **Tailwind CSS**. It uses **Zustand** for state management.

### Core Structure
-   **`src/App.jsx`**:
    -   **Routing**: Sets up routes for Login, Signup, Home, Profile, and Settings using `react-router-dom`.
    -   **Auth Check**: Calls `checkAuth()` on mount to verify if the user is logged in.
    -   **Theme**: Applies the selected theme from `useThemeStore`.

-   **`src/lib/axios.js`**:
    -   Creates a pre-configured `axios` instance with `withCredentials: true` to handle cookies automatically.

### State Management (Zustand Stores)

#### `src/store/useAuthStore.js`
Manages authentication state.
-   **`checkAuth`**: Verifies session with backend.
-   **`signup` / `login`**: Performs API calls and updates `authUser` state.
-   **`logout`**: Calls API and disconnects socket.
-   **`updateProfile`**: Handles profile picture updates.
-   **`connectSocket`**:
    -   Establishes a socket connection if authenticated.
    -   Listens for `getOnlineUsers` event to update the list of online users.
-   **`disconnectSocket`**: Closes the socket connection.

#### `src/store/useChatStore.js`
Manages chat application state.
-   **`getUsers`**: Fetches list of available users for the sidebar.
-   **`getMessages`**: Fetches chat history for the selected user.
-   **`sendMessage`**: Sends a message to the selected user and updates the local state.
-   **`subscribeToMessages`**:
    -   Listens for real-time `newMessage` events from the server.
    -   Updates the message list instantly when a message is received from the selected user.
-   **`unsubscribeFromMessages`**: Cleans up the listener when switching chats or unmounting.
-   **`setSelectedUser`**: Updates which user is currently currently being chatted with.

### Key Components

-   **`src/components/Sidebar.jsx`**:
    -   Uses `useChatStore` to fetch and display a list of users.
    -   Shows online status indicators based on `onlineUsers` from `useAuthStore`.
-   **`src/components/ChatContainer.jsx`**:
    -   Displays the message history.
    -   Auto-scrolls to the newest message.
    -   Sets up message subscription for real-time updates.
-   **`src/components/MessageInput.jsx`**:
    -   Form for typing text and selecting images.
    -   Calls `sendMessage` on submit.
