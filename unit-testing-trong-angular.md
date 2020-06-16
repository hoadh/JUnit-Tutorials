# Unit Testing trong Angular

> Nếu bạn chưa biết Unit Testing và những khái niệm cơ bản liên quan, hãy tìm đọc bài viết **Unit Test - Những bước chân đầu tiên** trong Tạp Chí Lập Trình Vol.4.

Kiểm thử giúp đảm bảo những thay đổi hoặc bổ sung không gây ảnh hưởng đến các tính năng cũ của ứng dụng hoặc phát sinh lỗi mới. Chúng ta tiếp tục tìm hiểu cách viết unit test cho các ứng dụng được xây dựng với framework Angular. Cụ thể, qua bài viết này, chúng ta sẽ đạt được các mục tiêu sau:

* Hiểu biết cơ bản về testing trong Angular
* Có thể viết unit test cho component
* Có thể viết unit test cho service
* Có thể giả lập service phụ thuộc trong các component

Để đọc hiểu bài viết này, bạn cần những kiến thức cơ bản về lập trình với ngôn ngữ JavaScript và sử dụng được framework Angular.

## Tổng quan

Angular hỗ trợ hai loại kiểm thử: Unit testing (kiểm thử đơn vị) và End-to-end testing (e2e). Với kiểm thử đơn vị, Jasmine là framework được sử dụng mặc định. Karma là thành phần thực thi các bộ test. Sau khi thực thi các bộ test, Karma sẽ tạo file báo cáo kết quả với định dạng HTML. Với kiểm thử e2e, nhóm phát triển Angular gợi ý sử dụng Protractor. Đây là thư viện được xây dựng dựa trên Selenium WebDriver.

Để thực thi kiểm thử đơn vị, chúng ta sử dụng lệnh `ng serve`. Để thực thi kiểm thử e2e, sử dụng lệnh `ng e2e`.

## Tìm hiểu Jasmine

Jasmine là framework kiểm thử cho các ứng dụng được xây dựng trên JavaScript. Với cú pháp đơn giản, rõ ràng, đây là một trong những framework mạnh mẽ để viết các unit test cho mã JavaScript. Nó có thể chạy độc lập, không phụ thuộc vào các thư viện khác và không yêu cầu DOM.

Ví dụ:

```javascript
describe('TestSuiteName', () => {
  // suite of tests here

  it('should do some stuff', () => {
    // this is the body of the test
  });
});
```

### describe

Hàm `describe` được sử dụng để nhóm các đặc tả hoặc kiểm thử có liên quan. Đầu vào là 2 tham số:

* một chuỗi mô tả mục đích của nhóm kiểm thử
* một hàm callback chứa các đặc tả, hoặc các bộ kiểm thử

Ví dụ:

```javascript
describe("TestSuiteName", function() {
	...
});
```

Các khối `describe` có thể lồng nhau. Ví dụ:

```javascript
describe("A suite", function() {
	...
	
	describe("Another suite inside", function() {
    ...
  });
  
  ...
});
```

*Để tạm thời dừng thực thi một khối `describe` nào đó, chúng ta có thể thay thế hàm thành `xdescribe`. Để chỉ thực thi một khối `describe` nào đó, chúng ta sử dụng `fdescribe`.*

### it

Đây là hàm chứa các đặc tả hoặc kiểm thử cụ thể.

```javascript
it("contains spec with an expectation", function() {
	expect(myOrg).toBe('CodeGym');
});
```

*Để tạm thời dừng một test case nào đó, chúng ta sử dụng hàm `xit` thay thế `it`. Và để chỉ thực thi một test case nào đó, chúng ta sử dụng `fit` thay thế.*

### expect

Hàm expect được sử dụng để đánh giá kết quả một đợi và kết quả thực tế của một kiểm thử.

```javascript
	expect(myOrg).toBe('CodeGym');
```

Như ví dụ trên, hàm `expect` mong đợi giá trị của biến `my_variable` có bằng `true` hay không. Nếu giá trị thực tế của biến `my_variable` là `true` thì kết quả của test case này là `PASS`; ngược lại, kết quả là `FAIL`.

### Setup và Teardown

* Setup là thành phần được thực thi trước tất cả (hoặc mỗi) test case.
* Teardown là thành phần được thực thi sau tất cả (hoặc mỗi) test case.

Trong Jasmine, để thiết lập **Setup** và Teardown, chúng ta có các hàm sau:

* beforeEach
* beforeAll
* afterEach
* afterAll

Thứ tự thực hiện của các hàm trên trong Jasmine được thể hiện như khối hình dưới đây.

![jasmine_functions.png](_img/jasmine_functions.png)

### done

Xét tình huống chúng ta muốn kiểm tra một hàm xử lý bất đồng bộ (async function). Việc thực thi hàm này có thể mất thời gian chờ đợi. Hàm `done()` được sử dụng trong trường hợp này để báo Jasmine biết thời điểm kết thúc việc thực thi.

Hãy xem qua ví dụ sau:

```javascript
it('should wait 3 seconds', (done) => {
  const weAre = 'CodeGym';
  setTimeout( () => {
    expect(weAre).toBe('CodeGym');
    done();
  }, 3000);
});
```

Chúng ta muốn jasmine đợi 5 giây trước khi đánh giá kết quả của biến `weAre`.

Với một hàm bất đồng bộ trả về kiểu Promise, chúng ta có thể viết kiểm thử như sau:

```javascript
it('should wait until getting result', (done) => {
	doSomething()
    .then( result => {
	    expect(result).toBe('CodeGym');
	    done();
	})
    .catch(error => {
      fail();
      done();
  });
});
```

## Tìm hiểu TestBed

`TestBed` là một tiện ích được Angular cung cấp để tạo một môi trường kiểm thử phù hợp cho các thành phần của Angular như: Component, Service, Pipe, Directive,...

Với TestBed, lập trình viên có thể khởi tạo một module kiểm thử với phương thức `configureTestingModule`. Lý do là mọi component trong Angular cần được chạy trong một module cụ thể nào đó.

Tham số cung cấp cho `configureTestingModule` khi khởi tạo module là những metadata cần thiết cho một module như imports, providers, declarations,...

Chúng ta thường tạo mới một module kiểm thử trước khi thực hiện các test case liên quan tới component trong hàm beforeEach (như đã giới thiệu ở trên). Ví dụ: 

```typescript
beforeEach(async(() => {
  TestBed.configureTestingModule({
    imports: [
      RouterTestingModule
    ],
    declarations: [
      AppComponent
    ],
  }).compileComponents();
}));
```

Nếu bộ kiểm thử đang thực thi phụ thuộc vào một service nào đó, chúng ta khai báo tên service vào metadata `providers` như sau:

```typescript
beforeEach(async(() => {
  TestBed.configureTestingModule({
    ...
    providers: [OneService]
  }).compileComponents();
}));
```

## Các tình huống viết unit test trong Angular

### Viết unit test cho component
### Viết unit test cho service
### Viết unit test cho component có phụ thuộc service


