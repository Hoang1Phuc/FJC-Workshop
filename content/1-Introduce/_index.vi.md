---
title : "Giới thiệu"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
Trong kiến trúc hiện đại, việc xử lý logic gần người dùng (tại biên mạng) giúp giảm độ trễ, cải thiện hiệu suất và trải nghiệm người dùng. **Lambda@Edge** là một dịch vụ của AWS cho phép bạn chạy các hàm Lambda tại **edge location của Amazon CloudFront**, hỗ trợ xử lý **HTTP request và response** trước khi đến hoặc rời khỏi origin (ví dụ: S3, API Gateway…).

Workshop này sẽ hướng dẫn bạn xây dựng một hệ thống **phân phối nội dung thông minh**, tích hợp các tính năng nâng cao của CloudFront và Lambda@Edge bao gồm:
- Phân phối nội dung theo vị trí địa lý (Geo-based routing): chuyển hướng người dùng đến nội dung phù hợp với quốc gia của họ.
- A/B Testing không cần backend: phân phối nội dung thử nghiệm (A/B) hoàn toàn serverless
- Tăng cường bảo mật: chặn truy cập theo User-Agent hoặc quốc gia, thêm HTTP security headers
- Ghi log & theo dõi hiệu năng: thu thập dữ liệu và giám sát CloudFront bằng log tùy biến
- Tối ưu chi phí & lựa chọn vùng edge: đánh giá chi phí và độ trễ tại các vị trí biên.

Kết quả cuối cùng, bạn sẽ triển khai được kiến trúc phân phối tĩnh hiện đại, bảo mật, có khả năng mở rộng cao và giảm chi phí cho ứng dụng web.