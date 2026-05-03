# Live Location Tracker

A hobby project to track real-time live locations using WebSockets (Socket.io) and Apache Kafka for reliable message streaming.

## Architecture & How Things Work

1. **Clients (Browsers/Apps)** connect to the Node.js server via **Socket.io**.
2. **Location Updates**: Clients emit their location data to the server (`client:location:update`).
3. **Kafka Producer**: The Node.js server acts as a Kafka producer, sending the received location data into the `location-updates` Kafka topic.
4. **Kafka Consumers**:
   - **Socket Server Consumer**: The server also consumes the `location-updates` topic and broadcasts the locations back to all connected clients via Socket.io (`server:location:update`).
   - **Database Processor**: A separate worker (`database-processor.js`) independently consumes the same `location-updates` topic to persist the location data into a database (currently mocked).

This architecture ensures that high-volume location updates are reliably buffered, processed asynchronously, and broadcasted efficiently.

## Prerequisites

- **Node.js** (v18+ recommended)
- **Docker** and **Docker Compose** (for running Kafka)

## How to Start Locally

Follow these steps to run the complete setup on your local machine:

### 1. Install Dependencies
```bash
npm install
```

### 2. Start Kafka Infrastructure
Start the Apache Kafka broker using Docker Compose:
```bash
docker compose up -d
```

### 3. Setup Kafka Topics
Once Kafka is running, initialize the required topic (`location-updates`):
```bash
node kafka-admin.js
```
*You should see a message confirming that the Kafka admin connected and topics were created.*

### 4. Start the Application Services
You will need to open two terminal windows to run both services:

**Terminal 1: Start the Main Server**
This will start the Express web server and Socket.io handler.
```bash
npm run dev
# or
npm start
```
*The server will start on port 5000 (default) and connect to Kafka.*

**Terminal 2: Start the Database Processor**
This simulates the worker that processes and saves locations to a database.
```bash
node database-processor.js
```

## Exploring the App

Once everything is running:
- The server exposes a health check endpoint at `http://localhost:5000/health`.
- Any frontend files placed in the `public/` directory will be served automatically at `http://localhost:5000/`.
- Connect your Socket.io client to `http://localhost:5000` to start emitting and listening to location events!
