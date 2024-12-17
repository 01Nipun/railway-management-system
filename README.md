# Railway Management System API
 This repository contains the implementation of a Railway Management System API inspired by the IRCTC platform. The system allows users to check available trains, book seats, and retrieve booking details. It features a role-based access system where admins can manage trains, and users can check availability and book seats.

# Problem Overview
 You are tasked with building an API with the following functionalities:

# User registration and login functionality.
 • Admin role for managing trains, including adding trains with source and destination details.
 • Seat availability checking for users between two stations.
 • Seat booking functionality that efficiently handles race conditions when multiple users attempt to book the same seat     simultaneously.
 • Retrieval of specific booking details.
 • The API employs JWT tokens for secure user authentication and restricts access to admin routes using an API key.

# Tech Stack
 • Backend Framework: Node.js with Express.js
 • Database: PostgreSQL
 • Authentication: JWT (JSON Web Tokens)
 • ORM: TypeORM (for PostgreSQL)
 • Password Hashing: bcrypt
 • Testing Framework: Jest, Supertest
 • Environment Variables: dotenv
# Key Features
 • User Registration: Register a new user with a username and password.
 • User Login: Login and receive a JWT token for authentication.
 • Add New Train (Admin): Admins can add new trains with details like train name, source station, destination station, and    total seats.
 • Train Availability: Users can check available trains between a given source and destination.
 • Seat Booking: Users can book a seat on a train if available.
 • Booking Details: Users can view their specific booking details.

## API Endpoints

### 1. User Routes

- **POST /api/user/register**  
  Registers a new user.
  - Request Body: `{ "username": "string", "password": "string" }`

- **POST /api/user/login**  
  Logs in the user and returns a JWT token.
  - Request Body: `{ "username": "string", "password": "string" }`
  - Response: `{ "token": "JWT Token" }`

- **GET /api/user/trains**  
  Fetches available trains between the given source and destination.
  - Query Parameters: `sourceStation` (string), `destinationStation` (string)
  - Headers: `Authorization: Bearer <JWT Token>`

- **POST /api/user/book-seat**  
  Books a seat on a particular train.
  - Request Body: `{ "trainId": "int", "seatCount": "int" }`
  - Headers: `Authorization: Bearer <JWT Token>`

- **GET /api/user/bookings/:bookingId**  
  Fetches specific booking details.
  - Path Parameters: `bookingId` (int)
  - Headers: `Authorization: Bearer <JWT Token>`

### 2. Admin Routes

- **POST /api/admin/add-train**  
  Adds a new train (accessible only by admins).
  - Request Body: `{ "trainName": "string", "sourceStation": "string", "destinationStation": "string", "totalSeats": "int" }`
  - Headers: `x-api-key: <API Key>`

# Handling Race Conditions

# Solving Race Conditions
A key challenge in this API is handling race conditions when multiple users attempt to book seats at the same time. The system ensures that only one booking succeeds in such cases.

## **How It Works:**

• The application employs pessimistic locking (setLock("pessimistic_write")) to lock the train record during booking attempts.
• This prevents other users from modifying the seat availability while a booking is being processed.
• If another user attempts to book the same seat during the lock period, the system will block the second booking attempt, ensuring only one user can successfully book the seat.
• This method guarantees consistent seat bookings even under high traffic.

### Testing Race Conditions

To verify that the system handles race conditions correctly, a **test** is included that simulates two parallel booking requests for the same seat. The test ensures that **only one booking is successful**, and the other receives a failure response.

#### **Test Details:**
- The test simulates two users attempting to book the same seat on a train at the same time.
- One booking will succeed, while the other will fail due to the race condition prevention mechanism.
  
This test guarantees that the system functions correctly under concurrent booking attempts.

## Setup and Installation

### Prerequisites
- Node.js (v16 or higher)
- PostgreSQL
- npm or yarn

### 1. Clone the Repository

```bash
git clone <repository-url>
cd railway-management-system
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Set Up Environment Variables

Create a `.env` file in the root directory and provide the necessary environment variables:

```ini
# Database Configuration
DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=your_db_password
DB_NAME=railway_management

# Server Configuration
PORT=3000

# Authentication
JWT_SECRET=your_jwt_secret
ADMIN_API_KEY=your_admin_api_key
```

### 4. Initialize the Database

Run the following command to initialize the PostgreSQL database:

```bash
npm run dev
```

### 5. Run the Application

To start the application, run the following command:

```bash
npm start
```

The server will be running on port `3000` (or the port you configured in the `.env` file).

### 6. Run Tests

To run the tests, including the race condition test, use the following command:

```bash
npm test
```

This will run all the test cases, including the one to verify that only one booking request succeeds in case of a race condition.

## Folder Structure

```
├── src
│   ├── config          # Configuration files (e.g., database setup)
│   ├── controllers     # Logic for handling requests
│   ├── middleware      # Authentication and admin verification middleware
│   ├── models          # Database entities (User, Train, Booking)
│   ├── routes          # API route definitions
│   ├── utils           # Utility functions (JWT token generation)
│   ├── app.js          # Express app setup
│   └── server.js       # Server initialization
├── .env                # Environment variables
├── package.json        # Project dependencies and scripts
└── README.md           # This file
```

## Assumptions:

• The database has been set up and is accessible from the application.
• The environment variables are correctly configured for both local development and production environments.
• The API key used for admin access should be kept confidential and not shared.

## Notes:

• This application is designed to handle race conditions during seat bookings using pessimistic locking (setLock("pessimistic_write")), ensuring that only one user can book a seat at a time.
• JWT authentication is used for user login, and access to admin routes is controlled through an API key.

## Conclusion:

This Railway Management System API provides functionalities for user registration, login, train management, seat availability, and bookings while effectively handling race conditions. The use of Node.js, PostgreSQL, and JWT authentication ensures a secure, scalable, and efficient system. The included tests validate that race conditions are properly managed, ensuring reliable seat bookings under high demand.