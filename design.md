# System Design Document: Smart City Complaint Categorizer (CivicLens)

## 1. Executive Summary
This document outlines the technical architecture for **CivicLens**, an AI-powered civic grievance redressal system. The solution leverages **FastAPI** for high-performance backend processing, a **Pre-trained Deep Learning Model** for image classification, and **PostgreSQL** for structured data persistence. The system is designed to be deployed on **AWS** to ensure scalability and reliability.

---

## 2. High-Level Architecture
The system follows a **Microservices-based Layered Architecture**, separating the client presentation, business logic (API + AI), and data persistence layers.

### **Core Components:**
1.  **Client Layer:** Mobile Application (Citizen interface) and Web Dashboard (Admin interface).
2.  **API Gateway / Backend:** A FastAPI application that handles routing, authentication, and orchestrates the AI inference.
3.  **Intelligence Layer:** A pre-trained Convolutional Neural Network (CNN) integrated directly into the backend service for real-time inference.
4.  **Data Layer:** PostgreSQL for relational data (users, complaints) and AWS S3 for unstructured data (images).

---

## 3. Detailed Component Design

### A. Backend (FastAPI)
*   **Framework:** FastAPI (Python 3.9+)
*   **Responsibilities:** 
    *   Request validation (Pydantic models).
    *   Asynchronous image processing.
    *   JWT Authentication for users and admins.
    *   Interfacing with the AI model.
*   **Why FastAPI?** Native support for concurrency (async/await) makes it ideal for handling ML inference requests without blocking the main thread.

### B. AI Engine (Pre-trained Model)
*   **Model Architecture:** ResNet50 / EfficientNet (Pre-trained on ImageNet).
*   **Fine-tuning strategy:** The top layers are frozen, and the final classification layer is fine-tuned on a dataset of civic issues.
*   **Classes:** `Pothole`, `Garbage_Dump`, `Water_Logging`, `Broken_Streetlight`, `Normal_Road`.
*   **Inference Latency:** Optimized to < 200ms using quantization.

### C. Database (PostgreSQL)
*   **Type:** Relational Database Management System (RDBMS).
*   **Hosting:** AWS RDS (Relational Database Service) or self-hosted on EC2.
*   **Data Integrity:** Uses Foreign Keys to link Complaints to specific Municipal Departments.
*   **Geo-Spatial:** Uses PostGIS extension (optional) for advanced location querying.

### D. File Storage
*   **Service:** AWS S3 (Simple Storage Service).
*   **Usage:** Stores raw images uploaded by users. The Database only stores the S3 URL string.

---

## 4. Process Flow Architecture (Flowchart)

This diagram represents the lifecycle of a single complaint from the user to the database.
![Sequence Diagram](https://raw.githubusercontent.com/amankadam-coditas/ai-for-bharat-hackathon/refs/heads/main/assets/sequence-diagram.png)