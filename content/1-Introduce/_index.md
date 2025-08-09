---
title : "Introduction"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
In modern web architecture, processing logic closer to users (at the network edge) can significantly reduce latency and improve performance. **AWS Lambda@Edge** allows you to run Lambda functions at **Amazon CloudFront edge locations**, enabling you to intercept and manipulate HTTP requests/responses before they reach the origin or before they are returned to the viewer.

This workshop walks you through building an **intelligent content delivery system** using CloudFront and Lambda@Edge, including the following advanced features:
- Geo-based content routing: redirect users to localized content based on country
- Serverless A/B Testing: run frontend experiments without a backend
- Enhanced security: block specific user-agents or countries; inject security headers
- Custom logging & performance monitoring: track request data directly at the edge
- Cost optimization and edge location selection: analyze cost/performance tradeoffs across CloudFront edge regions

By the end of this workshop, you'll have deployed a modern, secure, and scalable static content architecture fully optimized for global delivery.