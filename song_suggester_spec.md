# Song Suggester AI - Application Specification

## 1. Overview

The Song Suggster AI is a web-based application designed to provide users with personalized song and lyric suggestions based on their current emotional state or a described scenario. Leveraging advanced AI, it aims to offer highly relevant and contextually appropriate musical recommendations.

## 2. Example User Prompts

To guide development and testing, here are sample prompts the application should handle effectively:

### 2.1 Basic Emotional States

* "I'm feeling nostalgic about my childhood"
* "I'm really angry about a breakup"
* "I'm excited about starting a new job tomorrow"
* "I'm feeling melancholy on a rainy afternoon"

### 2.2 Specific Scenarios

* "I need music for a road trip with friends"
* "I'm studying late at night and need focus music"
* "I just moved to a new city and feel lonely"
* "I'm getting ready for a workout at the gym"

### 2.3 Detailed Request Format

* "I want 3 song suggestions with 3 lines of lyrics and then 2-3 sentences explaining why chosen"
* "Give me songs that match my current mood: stressed but hopeful about the future"
* "I need uplifting songs with meaningful lyrics for someone going through depression"

### 2.4 Personality-Based Requests

* "As an INFP, what songs would resonate with me when I'm feeling creative?"
* "I'm a Scorpio feeling introspective - suggest something deep and emotional"
* "Give me music that fits my personality: introverted, analytical, but romantic"

### 2.5 Refinement Prompts

* "That's not quite right, something more upbeat"
* "I like the first suggestion but want something from the 90s instead"
* "Can you give me alternatives that are less mainstream?"

## 3. Core Functionality

*   **User Input Processing:** Accepts natural language descriptions of emotions, situations, or themes from the user.
*   **Song & Lyric Generation:** Identifies and suggests real, existing songs along with a relevant lyric excerpt.
*   **Contextual Explanation:** Provides a concise explanation of why the chosen song and lyric are appropriate for the user's input.
*   **Personality Integration (Optional):** Can adapt suggestions based on an explicit user personality profile (e.g., astrological sign, MBTI, Big Five traits).
*   **Refinement & Iteration:** Allows users to request refinements or alternative suggestions if the initial output is not satisfactory.

## 4. Technical Stack

*   **Cloud Platform:** Google Cloud Platform (GCP)
    *   **Reasoning:** Offers robust AI/ML services, scalable compute, and integrated data solutions.
*   **Artificial Intelligence:** Google Gemini (latest SDK)
    *   **Reasoning:** State-of-the-art multimodal AI model capable of understanding nuanced language, generating creative text, and performing complex contextual reasoning required for song selection and explanation.
*   **Backend:**
    *   **Language:** Node.js (with Express.js or Fastify framework)
    *   **Reasoning:** Excellent for building scalable, high-performance APIs, especially for I/O-bound tasks like API calls to Gemini and database interactions. Large ecosystem of packages.
    *   **Deployment:** Cloud Run (serverless container platform, ideal for scalable, cost-effective API deployment with auto-scaling based on traffic).
*   **Database:** Cloud SQL (PostgreSQL or MySQL)
    *   **Reasoning:** Managed relational database service, providing robust data integrity, familiar SQL interface, and easy integration with Node.js applications on GCP. Suitable for structured user profiles, interaction history, and potential song metadata caching.
*   **Frontend (Optional, for Web UI):**
    *   **Framework:** React, Vue.js, or Svelte (for reactive and engaging user interfaces).
    *   **Deployment:** Cloud Storage (for hosting static assets) served via Cloud CDN (for global low-latency content delivery).
*   **Authentication:** Firebase Authentication (seamless integration with GCP, supports various authentication methods like email/password, social logins).
*   **Monitoring & Logging:** Cloud Monitoring and Cloud Logging (integrated services for application health, performance, and debugging).

## 5. High-Level Design

The application will follow a microservices-oriented architecture, leveraging GCP's managed services for scalability and ease of management.

*   **User Interface (Frontend):** A web-based application (e.g., React SPA) will serve as the primary interface for users. It will handle user input, display AI-generated suggestions, and manage user sessions. This frontend will communicate with the backend API via RESTful or GraphQL endpoints.
*   **Backend API (Node.js on Cloud Run):** This is the core application logic.
    *   **API Gateway (Optional but Recommended):** Cloud Endpoints or an internal Load Balancer can manage API traffic, authentication, and rate limiting before requests hit the Cloud Run service.
    *   **Request Handling:** Receives user input from the frontend.
    *   **AI Integration Layer:** Makes calls to the Gemini API with carefully crafted prompts, transforming user input and personality data into AI requests. Handles Gemini's responses.
    *   **Database Interaction Layer:** Communicates with Cloud SQL to store and retrieve user profiles, interaction history, and potentially a cache of song metadata.
    *   **Authentication & Authorization:** Integrates with Firebase Authentication for user management.
*   **Database (Cloud SQL):** Stores persistent data:
    *   `users` table: User IDs, authentication data (managed by Firebase), personality profiles (MBTI, astrological sign, Big Five traits, etc.).
    *   `user_interactions` table: History of user requests, AI responses, and feedback.
    *   `song_metadata` table (optional cache): Pre-populated or dynamically cached song titles, artists, and possibly lyric snippets to optimize AI retrieval or provide a fallback.
*   **AI Model (Gemini API):** Google's large language model is accessed via its API. It processes the textual prompts from the backend, understands context, and generates the relevant song and lyric suggestions.
*   **Logging & Monitoring:** All components will emit logs to Cloud Logging, and metrics to Cloud Monitoring for operational visibility.

```
+------------+        +-----------------+        +---------------------+        +--------------+
| User       |        | Frontend (Web)  |        | Backend API (Node.js|        | Gemini API   |
| (Browser)  +------->+ (Cloud Storage, +------->+ on Cloud Run)       +------->+ (AI Model)   |
+------------+        |  Cloud CDN)     |        |                     |        |              |
                      +-----------------+        |                     |        |              |
                                                 | (Auth, Logic, DB)   |        +--------------+
                                                 |                     |
                                                 +----------+----------+
                                                            |
                                                            v
                                                  +----------------+
                                                  | Cloud SQL (DB) |
                                                  +----------------+
```

## 6. High-Level Implementation Plan

This plan outlines the key phases for developing and deploying the Song Suggster AI.

### Phase 1: Foundation & Setup

*   **1.1 GCP Project Setup:** Create a new GCP project, enable necessary APIs (Cloud Run, Cloud SQL, Gemini API, Firebase).
*   **1.2 Cloud SQL Instance:** Provision a Cloud SQL instance (PostgreSQL/MySQL), configure databases and initial schemas (e.g., `users`, `user_interactions`).
*   **1.3 Firebase Authentication:** Set up Firebase Authentication for user registration and login.
*   **1.4 Local Development Environment:** Set up Node.js, database client, and Gemini SDK locally.

### Phase 2: Backend API Development (Node.js)

*   **2.1 API Skeleton:** Initialize Node.js project with Express.js/Fastify. Define basic API routes (e.g., `/suggest`, `/history`, `/profile`).
*   **2.2 Database Integration:** Implement database connection and CRUD (Create, Read, Update, Delete) operations for `users` and `user_interactions` with Cloud SQL. Use an ORM (e.g., Sequelize, Prisma) or a query builder.
*   **2.3 Gemini API Integration:**
    *   Develop a module for interacting with the Gemini API.
    *   Implement functions to construct dynamic prompts based on user input and personality data.
    *   Handle Gemini API responses and parse relevant song/lyric data.
*   **2.4 Core Suggestion Logic:** Develop the main `/suggest` endpoint logic, integrating user input, personality data (if provided), Gemini API calls, and saving interactions to the database.
*   **2.5 Authentication & Authorization:** Implement middleware to secure API endpoints using Firebase Authentication tokens.

### Phase 3: Frontend Development (Optional, if Web UI is required)

*   **3.1 UI Framework Setup:** Initialize a React/Vue/Svelte project.
*   **3.2 User Interface Components:** Design and build components for input forms, suggestion display, and user profile management.
*   **3.3 API Integration:** Connect frontend to the backend API endpoints.
*   **3.4 User Authentication Flow:** Implement user registration, login, and session management using Firebase SDK for the client.

### Phase 4: Testing & Deployment

*   **4.1 Unit & Integration Testing:** Write comprehensive tests for backend API endpoints, database interactions, and AI prompt logic.
*   **4.2 Dockerization:** Create a Dockerfile for the Node.js backend application.
*   **4.3 Cloud Run Deployment:** Deploy the Dockerized backend to Cloud Run. Configure environmental variables, scaling settings, and IAM roles for database and Gemini API access.
*   **4.4 Frontend Deployment (if applicable):** Deploy static frontend assets to Cloud Storage and configure Cloud CDN.
*   **4.5 Monitoring & Logging Configuration:** Set up custom metrics and alerts in Cloud Monitoring, and configure log sinks in Cloud Logging.
*   **4.6 End-to-End Testing:** Conduct full system tests to ensure all components work together seamlessly.

### Phase 5: Iteration & Optimization

*   **5.1 Performance Tuning:** Optimize database queries, API response times, and Gemini API call efficiency.
*   **5.2 Prompt Engineering Refinement:** Continuously refine AI prompts for better relevance, creativity, and adherence to specific output formats.
*   **5.3 Feature Enhancements:** Implement additional features like song history, favorite lists, or advanced search.
*   **5.4 Cost Optimization:** Monitor GCP resource usage and optimize configurations for cost efficiency.
