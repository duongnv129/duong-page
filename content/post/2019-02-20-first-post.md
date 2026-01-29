---
title: The AI call center system design
subtitle: The call center implements based on Livekit server and Agent
date: 2019-02-20
tags: ["me"]
---

In this post we will talk about the system architecture for building the AI call center. The design build for running on GCP environment with tech-stack Golang, GCP PubSub, Redis, Postgres and Livekit self-hosting. We use Websocket, Restful, gRPC, event-driven and microservices for horizontal scaling our system.
