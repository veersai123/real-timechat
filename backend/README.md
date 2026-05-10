<h1 align="center">🚀 Real-Time Microservices Chat Application</h1>

<p align="center">
  <img src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white" />
  <img src="https://img.shields.io/badge/Express.js-000000?style=for-the-badge&logo=express&logoColor=white" />
  <img src="https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB" />
  <img src="https://img.shields.io/badge/MongoDB-4EA94B?style=for-the-badge&logo=mongodb&logoColor=white" />
  <img src="https://img.shields.io/badge/RabbitMQ-FF6600?style=for-the-badge&logo=rabbitmq&logoColor=white" />
  <img src="https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white" />
  <img src="https://img.shields.io/badge/Socket.io-010101?style=for-the-badge&logo=socketdotio&logoColor=white" />
</p>

Welcome to the **Real-Time Chat App**! This project is built using a highly scalable **Microservices Architecture**. It separates core business logic into independent services (User, Chat, and Mail) to ensure high availability, fault tolerance, and seamless real-time messaging.

---

## ✨ Features
- **Real-Time Messaging:** Instant message delivery and "typing..." indicators powered by Socket.io and Redis Adapters.
- **Microservices Architecture:** Independently scalable User, Chat, and Mail services.
- **Asynchronous Processing:** Email OTP verification is handled asynchronously by RabbitMQ to prevent blocking user sign-ups.
- **Media Uploads:** Send photos seamlessly, managed via Cloudinary.
- **Authentication:** Secure JWT-based authentication.
- **Scalable Caching:** Redis is used for fast caching and socket session management.

---

## 🏗️ Architecture Overview

| Service | Port | Description | Database / Broker |
| --- | --- | --- | --- |
| **Frontend** | `3000` | Next.js / React UI | N/A |
| **User Service** | `5000` | Handles Authentication and OTP Generation | MongoDB, Redis, RabbitMQ |
| **Mail Service** | `5001` | Consumes messages to send OTP emails | RabbitMQ |
| **Chat Service** | `5002` | Handles Real-time Websockets and Chats | MongoDB, Redis, Cloudinary |

---

## 🛠️ Prerequisites
You must have the following software installed on your local machine to run this project:
- **Node.js**: (Version 18+ recommended)
- **Docker Desktop**: Required to easily run RabbitMQ locally.

---

## ☁️ 1. Cloud Services Setup
To run this application, you will need to set up a few free cloud services. Create accounts for the following and keep your credentials handy:

<details>
<summary><b>1. MongoDB Atlas (Database)</b></summary>
<br>

1. Create a free account on [MongoDB Atlas](https://cloud.mongodb.com/).
2. Create a new cluster. Go to **Network Access** and add `0.0.0.0/0` (Allow Access From Anywhere). *This is crucial to prevent timeout errors.*
3. Go to **Database Access** and create a new user.
4. Click Connect, choose "Drivers", and copy your Connection String (starts with `mongodb+srv://...`).
</details>

<details>
<summary><b>2. Upstash (Redis Cache)</b></summary>
<br>

1. Log in to [Upstash](https://upstash.com/).
2. Create a new Redis database.
3. Scroll down and copy your Redis connection URL (starts with `rediss://...`).
</details>

<details>
<summary><b>3. Cloudinary (Image Storage)</b></summary>
<br>

1. Log in to [Cloudinary](https://cloudinary.com/).
2. From the Dashboard, copy your 3 essential credentials: `Cloud Name`, `API Key`, and `API Secret`.
</details>

<details>
<summary><b>4. Gmail App Password (For Sending OTPs)</b></summary>
<br>

1. Go to your Google Account's Security settings.
2. Ensure **2-Step Verification** is turned ON.
3. Search for "App Passwords" and generate a new 16-letter password (Select App -> Mail, Device -> Other). Use this 16-letter string without spaces.
</details>

---

## 🐳 2. Run RabbitMQ using Docker
RabbitMQ is the message broker that handles background tasks (like sending OTP emails) between the User and Mail services. We will run it using Docker.

Open your terminal and run the following command:
```bash
docker run -d --hostname rmq --name rabbitmq -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin123 -p 5672:5672 -p 15672:15672 rabbitmq:3-management
```
*This command will pull the RabbitMQ image and start it in the background automatically.*

---

## 🔐 3. Environment Variables (.env)
You need to create 3 separate `.env` files inside the backend folders. (If they already exist, update them with your credentials).

#### `backend/user/.env`
```env
PORT=5000
MONGO_URI=your_mongodb_connection_string_here
REDIS_URL=your_upstash_redis_url_here
Rabbitmq_Host=localhost
Rabbitmq_Username=admin
Rabbitmq_Password=admin123
JWT_SECRET=your_jwt_secret_key
```

#### `backend/chat/.env`
```env
PORT=5002
MONGO_URI=your_mongodb_connection_string_here
JWT_SECRET=the_exact_same_jwt_secret_key_from_above
USER_SERVICE=http://localhost:5000
Cloud_Name=your_cloudinary_cloud_name
Api_Key=your_cloudinary_api_key
Api_Secret=your_cloudinary_api_secret
```

#### `backend/mail/.env`
```env
PORT=5001
Rabbitmq_Host=localhost
Rabbitmq_Username=admin
Rabbitmq_Password=admin123
USER=your_gmail_address@gmail.com
PASSWORD=your_16_letter_google_app_password_here
```

---

## 🚀 4. Start The Project
Now we need to start all the servers. Open 4 separate terminal windows/tabs:

**Terminal 1 (User Service):**
```bash
cd backend/user
npm install
npm run dev
```

**Terminal 2 (Chat Service):**
```bash
cd backend/chat
npm install
npm run dev
```

**Terminal 3 (Mail Service):**
```bash
cd backend/mail
npm install
npm run dev
```

**Terminal 4 (Frontend):**
```bash
cd frontend
npm install
npm run dev
```

### 🎉 Congratulations! 
Your project is now fully functional and running locally. Open your browser and navigate to **http://localhost:3000** to use the application!

---

## 💡 Troubleshooting Tips
- **`querySrv ECONNREFUSED` Error:** If you face this error (common for some ISPs blocking MongoDB DNS), set your computer's DNS to `8.8.8.8` (Google DNS), or inject `require('dns').setServers(['8.8.8.8'])` at the very top of your backend `index.ts` files.
- **`Server selection timed out` Error:** You forgot to whitelist your IP (`0.0.0.0/0`) in MongoDB Atlas Network Access.
- **`Invalid login: 535-5.7.8` / OTP Not Sending:** Your Google App Password is wrong, has extra spaces, or has been revoked. Generate a new one and try again.
