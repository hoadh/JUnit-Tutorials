# Thực hành BDD với Cucumber

Behavior-driven development (BDD) là một quy trình phát triển phần mềm Agile. Quy trình này khuyến khích cộng tác giữa các vai trò kỹ-thuật (như QA, lập trình viên,...) với những vai trò phi-kỹ thuật (như chuyên gia lĩnh vực, người dùng,...) để chia sẻ một cách hiểu chung về những tính năng cần làm trong dự án.

Để áp dụng BDD vào nhóm dự án, bên cạnh việc thực hành quy trình nhuần nhuyễn giữa các vai thì nghiên cứu sử dụng công cụ hỗ trợ cũng là một việc làm không thể thiếu. Trong phạm vi bài viết này, chúng ta sẽ tìm hiểu về Cucumber - một trong số những công cụ như thế.

Bài viết sẽ có những nội dung chính sau:
* Viết kịch bản với cú pháp Gherkin 
* Định nghĩa thao tác thực thi với ngôn ngữ JavaScript
* Tự động hoá kiểm thử giao diện với Selenium WebDriver

## Tổng quan

Cucumber là một framework hỗ trợ xây dựng các đặc tả với ngôn ngữ tự nhiên và hướng nghiệp vụ, giúp các thành viên có vai trò như QA, BA, Tester, chuyên gia ngành,... có thể dễ dàng viết và cộng tác cùng các thành viên có vai trò xây dựng hệ thống.

Cucumber hỗ trợ nhiều nền tảng công nghệ khác nhau như Java, JavaScript, C#, Ruby, C++,... Vì vậy,  nó có thể tích hợp được vào hầu hết các dự án hiện đại.

Việc triển khai các kịch bản kiểm thử với Cucumber được thực hiện gồm 2 phần:

1. QA (hoặc chuyên gia ngành) định nghĩa các kịch bản kiểm thử bằng ngôn ngữ tự nhiên với cú pháp **Gherkin**. Từ đó các kịch bản kiểm thử được thể hiện thông qua một tập hợp tuần tự các mệnh đề (sẽ được mô tả chi tiết trong mục **Cú pháp Gherkin**).
2. Cách mệnh đề trong kịch bản kiểm thử sau khi được định nghĩa bằng **Gherkin** sẽ được cài đặt bằng các đoạn mã bằng ngôn ngữ lập trìng tương ứng phù hợp với nền tảng của ứng dụng (xem mục **Định nghĩa thao tác**).

## Cú pháp Gherkin

Cú pháp Gherkin bao gồm những thành phần sau:

* Feature
* Scenario
* Step

### Feature (Tính năng)

Thành phần cơ bản trong một dự án là đặc tả tính năng. Các đặc tả này được viết vào một tập tin có đuôi `.feature` với cú pháp `Gherkin`.

Dòng đầu tiên của file sẽ bắt đầu với từ khoá `Feature: ` và theo sau bởi dòng lùi vào. Ví dụ:

```gherkin
Feature: Tên tính năng cần được kiểm thử.
```

Mỗi feature sẽ bao gồm danh sách các kịch bản kiểm thử liên quan. Các kịch bản này được gọi là `scenario`.

### Scenario (Kịch bản)

Kịch bản là nội dung nòng cốt trong cú pháp Gherkin. Đây là nơi đặc tả các thao tác kiểm thử cho một ca kiểm thử.

Kịch bản sẽ được viết bắt đầu với từ khoá `Scenario: `, theo sau là một nội dung tuỳ ý - có thể mô tả rõ ràng nội dung muốn kiểm thử.

Mỗi kịch bản sẽ có nhiều mệnh đề (chúng ta gọi là `Step`)  cùng với các dữ liệu cụ thể thể (được gọi là `Example`). Các mệnh đề và dữ liệu này thường được thu thập từ các yêu cầu sử dụng và mô tả có thể mô tả lại bằng ngôn ngữ tự nhiên.

### Step (Mệnh đề)

Mệnh đề những câu mô tả rõ các thao tác thực thi. Các mệnh đề được viết theo sau các từ khoá `Given`, `When`, `Then`.

* `Given` được sử dụng để mô tả trạng thái ban đầu của hệ thống (hoặc chức năng). Mục đích của `Given` là để cung cấp các điều kiện cần thiết trước khi thực hiện các tương tác chính vào hệ thống.
* `When` là từ khoá mô tả sự kiện, hành vi, hoặc tương tác chính của tác nhân lên hệ thống.
* `Then` được sử dụng để mô tả kết quả mong muốn cuối cùng.
* Bên cạnh đó, chúng ta có thể sử dụng `And`, `But`, hoặc `*` để kết nối mệnh đề có liên quan.

Các mệnh đề sẽ được định nghĩa với các các mã lệnh kiểm thử dựa trên một ngôn ngữ lập trình cụ thể. Vì trên thực thế, Cucumber không tự biết làm thế nào để thực thi các tính năng, kịch bản để mô tả.

Khi Cucumber thực hiện các mệnh đề trong kịch bản, nó sẽ tìm các `Định nghĩa thao tác` (Step definitions) phù hợp để thực thi.

### Background (Bối cảnh)

`Background` là từ khoá cho phép thêm ngữ cảnh vào các kịch bản trong một tính năng. Nội dung bối cảnh sẽ được viết giống kịch bản, có thể có các mệnh đề nhưng không có tiêu đề.

Về thứ tực thực hiện, một bối cảnh sẽ được thực hiện trước mỗi kịch bản (và lặp lại nếu có nhiều kịch bản trong một tính năng).

Ví dụ, một số tính năng yêu cầu thực hiện đăng nhập vào hệ thống thì mới có thể thực hiện. Chúng ta sẽ đưa thao tác đăng nhập này vào mục bối cảnh như sau:

```gherkin
Feature: ...

Background:
  Given Truy cập trang quản lý của hệ thống
  And Đăng nhập vào hệ thống
```



## Định nghĩa thao tác

### Sử dụng JavaScript

### Sử dụng Java

## Bài học

