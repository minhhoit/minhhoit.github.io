---
layout: post
title: Mysql Indexes, what are the best practice?
categories: [mysql, perfomance]
tags: [mysql, perfomance]
---

Để tăng tốc cho một trang web thì kỹ năng tôi nghĩ là quan trọng nhất là mysql query tuning. Chắc nhiều bạn đã biết Mysql sử dụng B-Tree để lưu index, chúng ta chỉ cần hiểu các ý điểm quan trọng sau về B-Tree:
- Hỗ trợ range query
- Pointer giữa các leaf

**Vậy câu hỏi đặt ra là làm thế nào để đánh index cho tốt?**

## Thứ tự khi đánh index

Hiện nay Mysql đã hỗ trợ Index Condition PushDown Optimization, nên thứ tự index không còn ảnh hưởng nhiều, nhưng sẽ đặc biệt ảnh hưởng lớn khi dùng với câu lênh **LIKE**
### Tham khảo tại đây:
+ [http://dev.mysql.com/doc/refman/5.7/en/multiple-column-indexes.html](http://dev.mysql.com/doc/refman/5.7/en/multiple-column-indexes.html)
+ [http://stackoverflow.com/questions/24315151/does-order-of-fields-of-multi-column-index-in-mysql-matter](http://stackoverflow.com/questions/24315151/does-order-of-fields-of-multi-column-index-in-mysql-matter)


## Vậy Index ảnh hưởng như thế nào với OR query?

Với _AND query_ thì mọi thứ khá chân thật để bạn hình dung là 1 index có hoạt động với query hay không. Nhưng sẽ không dễ dàng với _OR query_
`SELECT val FROM table WHERE key1 = 1 OR key2 = 2;`

Điểm khác biệt lớn nhất giữ `AND` và `OR` là với `AND` bạn có thể dùng nhiều column index còn `OR` thì không. Vậy thì đánh index như thế nào nhĩ???
Câu trả lời là chỉ cần đánh index cho 2 field `key1` và `key2`. Nguyên là mysql có 1 cơ chế là `merge index`, tức là đầu tiên nó sẽ tìm tất cả dữ liệu liên quan tới index của `key1`, sau đó sẽ tìm tất cả dữ liệu liên quan đến index của `key2` sau đó thì merge kết quả lại với nhau.

### Tham khảo tại:
+ [http://dev.mysql.com/doc/refman/5.7/en/index-merge-optimization.html](http://dev.mysql.com/doc/refman/5.7/en/index-merge-optimization.html)

## Covering Index là gì?

Là một loại index đặc biệt là nó chứa luôn cả **data** cần tìm kiếm. Với covering Index thì mọi thứ bạn cần đã ở ngay đó. Thông thương B-Tree là một cái cây, mà lá của nó sẽ là key(index) và field information. Do đó khi mọi thứ bạn tìm (trong cấu query Select) được sử dụng trong index( nằm trong WHERE) thì mysql sẽ chỉ không phải đi đâu xa để tìm kiếm các thông tin bạn cần.

Tóm lại bạn nên cố gắng chỉ SELECT những thứ nằm trong WHERE, và làm thế nào để mọi thứ trong WHERE đều được index thì sẽ tiết kiệm được rất nhiều random I/O
Tham khảo thêm tại: 
+ [http://planet.mysql.com/entry/?id=661727](http://planet.mysql.com/entry/?id=661727)

## Hạn chế sử dụng Query Function khi cần index

Lý do rất đơn giản là **Mysql** không thông minh tới mức hiểu được những biểu thức trong _WHERE_ của các field **index** là giống nhau.

## Nên dùng Auto Increment cho Primary Key

Tất cả các điều nêu ở trên đều nhằm mục đích là làm sau query nhanh nhất, hay là tối ưu nào cho thao tác **đọc**. Vậy thì câu hỏi đặt ra là làm thế nào để việc **ghi** (INSERT) là nhanh nhất.
Đối với thao tác insert dữ liệu vào một table non-sorted data-structure thì chúng ta chỉ cần insert dữ liệu vào cuối là được. Nhưng đối với table index thì cần phải xem là nên insert vào đâu, vì vậy nếu ta set primary key là auto-increment thì mysql chỉ cần thao tác như với table no-sorted data-structure.

## Có nên gán giá trị mặc định là **null**?

Mysql hỗ trợ cho giá trị NULL, tức là tồn tại một trạng thái không dữ liệu. Để hỗ trợ trang thái này thì MYSQL sẽ phải lưu thêm thông tin mỗi dòng, điều này sẽ khiến cho dữ liệu bị phình to ra. Do đó chúng ta không nên dùng mặc định **null** cho tất cả các field.

## Cấu hình buffer pool

Innodb sử dụng một vùng nhớ gọi là buffer pool cho mục đích cache data và lưu index. Vùng nhớ này lưu theo đơn vị là page( default là 16kb) và sử dụng LRU Algorithm để evict cache. Về cơ bản thì buffer càng lớn càng tốt, buffer lớn thì query cache được càng nhiều dẫn đến query không cần hit IO 

### Tham khảo tại:
+ [http://dba.stackexchange.com/questions/27328/how-large-should-be-mysql-innodb-buffer-pool-size](http://dba.stackexchange.com/questions/27328/how-large-should-be-mysql-innodb-buffer-pool-size)
+ [https://dev.mysql.com/doc/refman/5.7/en/innodb-buffer-pool.html](https://dev.mysql.com/doc/refman/5.7/en/innodb-buffer-pool.html)
+ [http://dev.mysql.com/doc/refman/5.7/en/glossary.html#glos_page_size](http://dev.mysql.com/doc/refman/5.7/en/glossary.html#glos_page_size)


## Nguồn tham khảo:
+ Internet
