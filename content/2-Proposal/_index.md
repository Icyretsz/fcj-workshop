---
title: "Proposal"
date: "2025-09-15"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---
# Real-Time Typing Challenge Platform
*A Multiplayer [Monkeytype](https://monkeytype.com) Clone Built on AWS Serverless Architecture*

---

## 1. Executive Summary

The **Real-Time Typing Challenge Platform** is an interactive web application that replicates the core experience of [Monkeytype](https://monkeytype.com) while introducing **multiplayer functionality** for live competitive or collaborative typing sessions. Designed as a showcase of real-time web technologies and scalable cloud architecture, the platform combines a modern frontend experience with a serverless backend powered by AWS.

Users can create or join typing rooms, compete in real time, and view performance metrics such as Words Per Minute (WPM), accuracy, and rankings. The application leverages **Next.js** for the frontend, **AWS Lambda** and **API Gateway (WebSocket)** for real-time synchronization, and **Amazon DynamoDB** for player and session data. **Amazon Cognito** secures user access and authentication. The solution demonstrates how cloud-native services can deliver fast, scalable, and cost-efficient real-time applications without the need for traditional server management.

---

## 2. Problem Statement

### What’s the Problem?
Existing typing platforms are often limited to single-player functionality, lacking real-time multiplayer interaction or open implementation examples. Most multiplayer solutions require dedicated servers or complex infrastructure, making them costly and difficult to maintain. For developers and enthusiasts, there is no accessible, serverless example demonstrating real-time synchronization, event-driven updates, and secure user management in one integrated system.

### The Solution
This project provides a **serverless, real-time typing platform** that allows multiple users to connect, type simultaneously, and see instant feedback on their progress. It is designed to be lightweight, cost-efficient, and scalable:

- **AWS API Gateway (WebSocket):** Enables bi-directional communication for live match updates.
- **AWS Lambda:** Handles game logic, score calculation, and event broadcasting.
- **Amazon DynamoDB:** Stores user profiles, ongoing sessions, and leaderboards with low latency.
- **Next.js with AWS Amplify:** Delivers a modern, responsive frontend optimized for performance.
- **Amazon Cognito:** Manages authentication, allowing users to register, sign in, and track personal stats securely.

Key features include:
- Real-time multiplayer rooms
- Individual and team typing modes
- Live leaderboards
- Performance history tracking

---

## 3. Benefits and Return on Investment

The project serves as both a **developer learning platform** and a **user-facing application**. It demonstrates best practices in event-driven architecture, WebSocket implementation, and cloud-based scalability — all at minimal operational cost.

For users, it provides an engaging, social typing experience. For developers, it offers an educational reference for building serverless real-time systems. The estimated running cost, based on AWS’s free tier and minimal Lambda/DynamoDB usage, is under **$1 per month**.

The platform requires no ongoing server maintenance, ensuring:
- **Long-term sustainability**
- **Rapid scalability**
- **Low operational overhead**

This makes it ideal for workshops, demos, or portfolio projects.

---

