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

---

## Main Entry Points

These are the starting points for the application execution:

### Backend Entry: `backend/src/index.js`
This is the heart of the backend server. When you run `npm run start` (or `dev`), this file:
1.  **Initializes the Environment**: Loads variables from `.env`.
2.  **Sets up the HTTP Server**: Imports the `server` instance from `socket.js` (which couples Express and Socket.IO).
3.  **Configures Middleware**: Sets up CORS to allow the frontend to connect, and `cookieParser` to handle authentication tokens.
4.  **Connects to Database**: Establishes a connection to MongoDB.
5.  **Defines Routes**: Maps API endpoints (like `/api/auth`) to their respective routers.
6.  **Starts Listening**: Opens the port (e.g., 5001) for incoming requests.

### Frontend Entry: `frontend/src/main.jsx`
This is the boot file for the React application.
1.  **React Root**: Finds the `#root` element in `index.html`.
2.  **Providers**: Wraps the main `App` component with `BrowserRouter` to enable client-side navigation.
3.  **Rendering**: Mounts the React component tree into the DOM.

---

## How the Code Works (End-to-End Flow)

Here is a step-by-step example of how the data flows when a user sends a message:

1.  **User Action**: The user types a message in `MessageInput.jsx` and hits send.
2.  **Frontend Logic (`useChatStore.js`)**:
    *   The `sendMessage` function is called.
    *   It sends an HTTP `POST` request to `http://localhost:5001/api/messages/send/:id` with the message content.
3.  **Backend API Handling (`message.controller.js`)**:
    *   The Express server receives the request.
    *   The `sendMessage` controller validates the data.
    *   It saves the new message to the **MongoDB** database.
4.  **Real-Time Delivery (`socket.js`)**:
    *   Inside the same controller, the backend checks if the receiver is currently online using `getReceiverSocketId`.
    *   If online, it uses `io.to(socketId).emit("newMessage", newMessage)` to push the message directly to the receiver's active socket connection.
5.  **Receiver Update (`useChatStore.js`)**:
    *   The receiver's frontend is listening for the `newMessage` event (via `subscribeToMessages`).
    *   When the event fires, the new message is instantly added to the local state (`messages` array), updating the UI in `ChatContainer.jsx` without needing to refresh.

### Handling Multiple Senders (Concurrency)

A common question is: *What happens if multiple users send messages to me at the same time? How does the app know which chat box to update?*

The socket server sends **all** messages to the receiver, regardless of who sent them. The filtering happens on the **client-side (Frontend)**:

1.  **Receiver's State**: The frontend knows which user is currently selected in the sidebar (`selectedUser`).
2.  **Event Listener Check**: In `useChatStore.js`, the `subscribeToMessages` function listens for the `newMessage` event.
3.  **Filtering Logic**:
    ```javascript
    socket.on("newMessage", (newMessage) => {
      // Check if the message is from the user currently open in the chat window
        const isMessageSentFromSelectedUser = newMessage.senderId === selectedUser._id;
        
      // If it's not from the selected user, ignore it (do not append to current view)
      if (!isMessageSentFromSelectedUser) return;

      // Otherwise, add it to the message list
      set({ messages: [...get().messages, newMessage] });
    });
    ```
This ensures that if User A sends a message while you are chatting with User B, User A's message will **not** appear in User B's chat window.

---

## Socket.IO Mechanics (Deep Dive)

### 1. How are Sockets Assigned?
When a user visits the website, the frontend (`useAuthStore.js`) attempts to connect to the socket server.
*   **Unique ID**: Socket.IO automatically assigns a unique, random string ID (e.g., `BoKBQ_w2...`) to every single new connection. This is `socket.id`.
*   **One Socket Per Tab**: If a user opens the app in 3 different tabs, they will have **3 different socket IDs**.

### 2. How do we know which socket belongs to whom?
Since `socket.id` is random, we need a way to link it to our database `userId`. We do this during the **Handshake**.

**Frontend Connection w/ Query Param:**
```javascript
// frontend/src/store/useAuthStore.js
const socket = io(BASE_URL, {
  query: {
    userId: authUser._id, // We explicitly send the User ID here
  },
});
```

**Backend Mapping:**
```javascript
// backend/src/lib/socket.js
const userSocketMap = {}; // Acts as a phonebook: { "db_user_id": "socket_id" }

io.on("connection", (socket) => {
  const userId = socket.handshake.query.userId;
  
  if (userId) {
    userSocketMap[userId] = socket.id; // Map the random socket ID to the known User ID
  }
  // ...
});
```
### 3. Trace: Where does the ID come from?
To be exact, here is the flow of the `userId`:
1.  **MongoDB**: User logs in -> Database returns the User object (`_id: "65f..."`).
2.  **Auth Store**: React saves this user in `useAuthStore` (`authUser`).
3.  **Connection**: When connecting, we pass `authUser._id` into the `query` object:
    ```javascript
    query: { userId: "65f..." }
    ```
4.  **Handshake**: The server reads this standard http query parameter from the handshake request: `socket.handshake.query.userId`.
5.  **Map**: The server uses that string ("65f...") as the **Key** in the `userSocketMap`.

### 4. Sending a Message to a Specific User
When you want to send a private message, you don't broadcast it to everyone. You look up the "phone number" (Socket ID) using the User ID.


```javascript
export function getReceiverSocketId(userId) {
  return userSocketMap[userId];
}

// inside controller
const receiverSocketId = getReceiverSocketId(receiverId);
if (receiverSocketId) {
  io.to(receiverSocketId).emit("newMessage", newMessage);
}
```
If `receiverSocketId` is `undefined`, it means the user is offline, so the message is just saved to the DB and will be loaded when they next log in.

---

## Online Status & Routing

### How do we know if a user is online?
We don't "ping" users constantly. Instead, we depend on the socket connection status.

1.  **Broadcasting**: whenever a user connects or disconnects, the **server** broadcasts an event to **everyone**:
    ```javascript
    // backend/src/lib/socket.js
    io.emit("getOnlineUsers", Object.keys(userSocketMap));
    ```
2.  **Listening**: The **frontend** listens for this event and updates its local state:
    ```javascript
    // frontend/src/store/useAuthStore.js
    socket.on("getOnlineUsers", (userIds) => {
      set({ onlineUsers: userIds });
    });
    ```
    *   `onlineUsers` is just an array of User IDs (e.g., `['user_123', 'user_456']`).
    *   To check if a specific user is online, the frontend just checks: `onlineUsers.includes(someUser._id)`.

### Does the sender need the receiver's Socket ID?
**No.** This is a common misconception.
*   **The Sender** only knows the Receiver's **User ID** (from the database).
*   **The Sender** sends the message to the **Server** via a standard HTTP POST request (or a socket event containing just the `receiverId`).
*   **The Server** is the only one who knows the `socketId`. It looks it up in the `userSocketMap` and routes the message.
*   The Sender **never** sees or needs the Receiver's socket ID.




