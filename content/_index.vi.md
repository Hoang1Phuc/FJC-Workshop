---
title: "Triển khai Lambda@Edge với CloudFront Advanced Features"
date: "`r Sys.Date()`"
weight: 1
chapter: false
---

# Workshop AWS: Lambda@Edge & CloudFront Advanced Features

## Tổng quan

Trong workshop này, bạn sẽ tìm hiểu cách triển khai **Edge Computing** với AWS bằng cách sử dụng **Amazon CloudFront** kết hợp **Lambda@Edge**.  
Bạn sẽ thực hành cá nhân hoá (personalization), bảo mật (security), thực hiện A/B testing mà không cần backend, và tối ưu chi phí/hiệu năng với Edge Locations.

![CloudFront Lambda@Edge](/images/arc-log.jpg)

## Nội dung

1. [Giới thiệu](1-introduce/)
2. [Các bước chuẩn bị](2-prerequiste/)
3. [Kết nối, kiểm tra & xác minh chức năng](3-testing/)
4. [Ghi log session & đo hiệu năng CloudFront](4-logging-performance/)
5. [Phân tích chi phí & tối ưu vị trí Edge](5-cost-optimization/)
6. [Dọn dẹp tài nguyên](6-cleanup/)

---

> 💡 **Lưu ý:**
> - Đảm bảo bạn có IAM Role & Policy phù hợp để chạy Lambda@Edge và quản lý CloudFront.
> - Xóa các resource sau khi lab hoàn tất để tránh phát sinh phí không cần thiết.