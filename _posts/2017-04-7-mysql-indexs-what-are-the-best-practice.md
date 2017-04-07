---
layout: post
title: Mysql Indexes, what are the best practice?
categories: [mysql, perfomance]
tags: [mysql, perfomance]
---

Để tăng tốc cho một trang web thì kỹ năng tôi nghĩ là quan trọng nhất là mysql query tuning. Chắc nhiều bạn đã biết Mysql sử dụng B-Tree để lưu index, chúng ta chỉ cần hiểu các điều sau về B-Tree:
- Nó hỗ trợ range query
- Để có thể hỗ trợ range query nó sẽ có pointer giữa các leaf (thay vì chỉ pointer từ cha sang con như một khái niệm cây thông thường)

Vậy câu hỏi đặc ra là làm thế nào để đánh index cho tốt?
1 - Thứ tự đánh Index rất quan trọng
Pointer giữ các leaf sẽ được chỉ từ trái sang phải. Do đó index của bạn củng phải tương tự
