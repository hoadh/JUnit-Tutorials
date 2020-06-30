

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
Feature: Là người bận rộn, tôi muốn thêm việc cần làm vào danh sách công việc để lập kế hoạch hằng ngày
```

Mỗi feature sẽ bao gồm danh sách các kịch bản kiểm thử liên quan. Các kịch bản này được gọi là `scenario`.

### Scenario (Kịch bản)

Kịch bản là nội dung nòng cốt trong cú pháp Gherkin. Đây là nơi đặc tả các thao tác kiểm thử cho một ca kiểm thử.

Kịch bản sẽ được viết bắt đầu với từ khoá `Scenario: `, theo sau là một nội dung tuỳ ý - có thể mô tả rõ ràng nội dung muốn kiểm thử. Ví dụ:

```gherkin
  Scenario: Thêm một việc cần làm vào danh sách
```

Mỗi kịch bản sẽ có nhiều mệnh đề (chúng ta gọi là `Step`)  cùng với các dữ liệu cụ thể thể (được gọi là `Example`). Các mệnh đề và dữ liệu này thường được thu thập từ các yêu cầu sử dụng và mô tả có thể mô tả lại bằng ngôn ngữ tự nhiên.

### Step (Mệnh đề)

Mệnh đề những câu mô tả rõ các thao tác thực thi. Các mệnh đề được viết theo sau các từ khoá `Given`, `When`, `Then`.

* `Given` được sử dụng để mô tả trạng thái ban đầu của hệ thống (hoặc chức năng). Mục đích của `Given` là để cung cấp các điều kiện cần thiết trước khi thực hiện các tương tác chính vào hệ thống.
* `When` là từ khoá mô tả sự kiện, hành vi, hoặc tương tác chính của tác nhân lên hệ thống.
* `Then` được sử dụng để mô tả kết quả mong muốn cuối cùng.
* Bên cạnh đó, chúng ta có thể sử dụng `And`, `But`, hoặc `*` để kết nối mệnh đề có liên quan.

Hãy xem qua ví dụ sau:

```gherkin
Scenario: Thêm một việc cần làm vào danh sách
    Given Danh sách việc cần làm ban đầu rỗng
    When Thêm việc 'Viết bài hướng dẫn Cucumber' vào danh sách
    Then Danh sách việc cần làm sẽ có 'Viết bài hướng dẫn Cucumber'
```

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

### Ví dụ hoàn chỉnh

```gherkin
Feature: Là người bận rộn, tôi muốn thêm việc cần làm vào danh sách công việc để lập kế hoạch hằng ngày

  Background:
    Given Mở ứng dụng Todos List

  Scenario: Thêm một việc cần làm vào danh sách
    Given Danh sách việc cần làm ban đầu rỗng
    When Thêm việc 'Viết bài hướng dẫn Cucumber' vào danh sách
    Then Danh sách việc cần làm sẽ có 'Viết bài hướng dẫn Cucumber'
```

Chúng ta đã viết đặc tả một tính năng cơ bản trong ứng dụng **Todos List**. Đây là ứng dụng quản lý danh sách công việc cần làm mỗi ngày. Người dùng có thể `thêm`, `sửa`, `xoá`, `cập nhật hoàn thành` các công việc hằng ngày. Chúng ta sẽ sử dụng các tính năng này trong các phần tiếp theo của bài viết:

* Link để truy cập ứng dụng là https://cg-todo-list-demo.netlify.app/
* Mã nguồn ứng dụng được tham khảo và sao chép từ bài viết https://freshman.tech/todo-list/

## Định nghĩa thao tác (JavaScript)

Chúng ta có thể bắt đầu sử dụng Cucumber với JavaScript qua hệ sinh thái Nodejs.

### Bước 1 - Cài đặt

Tạo dự án Nodejs mới và cài đặt các gói cần thiết:
```bash
mkdir automation_testing_project
cd automation_testing_project
npm init
npm install cucumber chai selenium-webdriver chromedriver --save
```

Mở file `package.json` và copy mã dưới đây để thực thi kịch bản trong Cucumber:

```json
...
"scripts": {
	"test": "./node_modules/.bin/cucumber-js features/*.feature -r step_definitions -r support"
},
...
```

Tiếp tục, chạy lệnh sau để kiểm tra kết quả:

```bash
npm test
```

Nếu kết quả hiển thị như sau là cài đặt thành công:

```bash
0 scenarios
0 steps
0m00.000s
```

### Bước 2 - Viết kịch bản với Gherkin

Tạo thư mục `features` trong dự án. Sau đó tạo file `add_todo.feature` với nội dung như sau:

```gherkin
Feature: Là người bận rộn, tôi muốn thêm việc cần làm vào danh sách công việc để lập kế hoạch hằng ngày

  Background:
    Given Mở ứng dụng Todos List

  Scenario: Thêm một việc cần làm vào danh sách
    Given Danh sách việc cần làm ban đầu rỗng
    When Thêm việc 'Viết bài hướng dẫn Cucumber' vào danh sách
    Then Danh sách việc cần làm sẽ có 'Viết bài hướng dẫn Cucumber'
```

Mở Terminal và thực thi lệnh `npm test`, kết quả trên terminal sẽ trả về như sau:

```bash
Warnings:

1) Scenario: Thêm một việc cần làm vào danh sách # features/add_todo.feature:6
   ? Given Mở ứng dụng Todos List
       Undefined. Implement with the following snippet:

         Given('Mở ứng dụng Todos List', function () {
           // Write code here that turns the phrase above into concrete actions
           return 'pending';
         });
#....
#.... kết quả khá dài nên mình đã ẩn bớt đi
#....

1 scenario (1 undefined)
4 steps (4 undefined)
0m00.000s
npm ERR! Test failed.  See above for more details.
```

Kết quả việc thực hiện kịch bản trên là `failed` vì chúng ta chưa định nghĩa thao tác để tương tác với ứng dụng.

### Bước 3 - Định nghĩa thao tác

Ở bước này, chúng ta sẽ cùng nhau cài đặt các thao tác mô phỏng hành vi người dùng trên ứng dụng web như: *gõ phím, click vào button, kiểm tra text,...* qua một framework có tên là **Selenium WebDriver**.

#### Cấu hình Selenium WebDriver

Tạo thư mục `support` và file `world.js` bên trong với nội dung như sau:

```javascript
const seleniumWebdriver = require('selenium-webdriver');
const { setWorldConstructor } = require('cucumber');

class CustomWorld {
  constructor() {
    this.driver = new seleniumWebdriver.Builder()
    .forBrowser('chrome')
    .build()
    
    this.waitForElement = function(locator) {
      const condition = seleniumWebdriver.until.elementLocated(locator)
      return this.driver.wait(condition)
    }
  }
}

setWorldConstructor(CustomWorld);
```
#### Bổ sung định nghĩa

Tạo thư mục `step_definitions` tại thư mục gốc của dự án. Tạo file `add_todo.js` với nội dung lần lượt theo kịch bản ở file `add_todo.feature`.

Trước tiên, chúng ta phải import các thư viện cần thiết:

```javascript
const { Given, When, Then, After } = require('cucumber');
const { Key } = require('selenium-webdriver');
const { expect } = require('chai');
```

Với mệnh đề `Given Mở ứng dụng Todos List`, chúng ta định nghĩa như sau để thư viện **Selenium WebDriver** có thể khởi động trình duyệt và truy cập link ứng dụng:

```javascript
Given('Mở ứng dụng Todos List', async function () {
  await this.driver.get('https://cg-todo-list-demo.netlify.app/');
});
```

Mệnh đề `Given Danh sách việc cần làm ban đầu rỗng` được định nghĩa với các dòng dưới đây. Chúng ta sẽ sử dụng `expect` thuộc thư viện `chai` để kiểm tra kết quả thực tế hiển thị trên ứng dụng với mong đợi. Mong muốn của chúng ta là: *ở lần đầu tiên truy cập, danh sách công việc phải rỗng (chưa có công việc nào)*.

```javascript
Given('Danh sách việc cần làm ban đầu rỗng', async function () {
  const todolist = await this.driver.findElement({css: '.todo-list'});
  const items = await todolist.findElements({css: 'li'});
  expect(items).to.have.lengthOf(0);
});
```

Dưới đây là mệnh đề `When Thêm việc 'Viết bài hướng dẫn Cucumber' vào danh sách`. Để thao tác này có thể được tái sử dụng ở kịch bản khác, chúng ta khai báo tham số `{string}` với tên công việc.

```javascript
When('Thêm việc {string} vào danh sách', async function (todoText) {
  const todoInput = await this.driver.findElement({css: '.js-todo-input'});
  await todoInput.sendKeys(todoText + Key.ENTER);
});
```

Cuối cùng, định nghĩa mệnh đề `Then Danh sách việc cần làm sẽ có 'Viết bài hướng dẫn Cucumber'` như sau:

```javascript
Then('Danh sách việc cần làm sẽ có {string}', async function (todoText) {
  const todolist = await this.driver.findElement({css: '.todo-list'});
  const items = await todolist.findElements({css: 'li'});
  const todo = await items[0].getText();
  expect(items).to.have.lengthOf(1);
  expect(todo).to.equal(todoText);
});
```

Sau khi thực hiện sau kịch bản, nếu muốn trình duyệt tự động tắt đi thì chúng ta có thể sử dụng `After`:

```javascript
After(async function() {
  await this.driver.close();
});
```

Có một lưu ý nhỏ khi cài đặt các định nghĩa thao tác trên JavaScript. Các thao tác của chúng ta được Selenium WebDriver thực hiện bất đồng bộ và kết quả trả về là `Promise`. Vì vậy, để mã nguồn dễ đọc, chúng ta nên sử dụng async/await như các ví dụ ở trên.

Bạn có thể tham khảo lại mã nguồn tại đây: https://github.com/hoadh/demo-todo-list-cucumber-js

## Kinh nghiệm triển khai

## Link tham khảo

* Tài liệu Cucumber framework https://cucumber.io/docs/guides/
* Cú pháp Gherkin https://cucumber.io/docs/gherkin/
* Thư viện Chaijs https://www.chaijs.com/
* Tài liệu sử dụng Selenium WebDriver: https://www.selenium.dev/documentation/en/