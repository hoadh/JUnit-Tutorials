# Unit Testing - Những bước chân đầu tiên

> “Hành trình vạn dặm bắt đầu từ một bước chân.” - Lão Tử

## Giới thiệu
Chất lượng công việc là một trong những yếu tố quan trọng xác định thành công của bạn tại nơi làm việc. Có nhiều cách để làm điều này trong lĩnh vực công nghệ phần mềm. Nhưng có một cách dễ dàng và hiệu quả là áp dụng kiểm thử. Lý do đơn giản là khả năng viết mã đạt chất lượng thường quan trọng hơn nhiều so với việc viết một khối lượng lớn mã khó bảo trì và tồn tại nhiều lỗi.

Unit Test (Kiểm thử đơn vị) là kỹ thuật kiểm thử những khối thành phần nhỏ nhất trong phần mềm (thường là các hàm hoặc phương thức). Đây là một trong những cấp độ kiểm thử đơn giản và có thể bắt đầu sớm trong vòng đời phát triển phần mềm. Thậm chí, bạn có thể viết unit test trước khi viết mã. Tuy nhiên, đây không phải là một thuật ngữ mới trong lĩnh vực phần mềm.  Khái niệm unit test xuất hiện lần đầu trong ngôn ngữ lập trình Smalltalk vào những năm 1970. Đến nay, unit test gần như đã trở thành một chuẩn mực trong ngành bởi mục đích của nó là phục vụ yêu cầu nâng cao chất lượng sản phẩm phần mềm.

Với nhiều lập trình viên, dù mới vào nghề hay đã gạo cội, thì unit test là một trong những kỹ năng không thể thiếu khi làm việc. Nếu bạn chưa từng nghe qua hoặc chưa có điều kiện thực hành thì cùng bước những bước chân đầu tiên qua bài viết này nhé!

## Bài viết này có gì

Với bài viết này, bạn học được những nội dung sau:

* Một số khái niệm cơ bản trong hoạt động testing và unit testing
* Sử dụng JUnit trong dự án Java nói chung
* Kết hợp Mockito và JUnit để thực hiện việc kiểm thử trong một số tình huống thực tế
* Case Study và những bài học khi thực hiện kiểm thử

## Thuật ngữ

Để đọc hiểu nội dung hướng dẫn này, bạn cần biết đến một số thuật ngữ thường được sử dụng trong các hoạt động kiểm thử.

### Test case

Test case là các trường hợp cần kiểm thử với đầu vào và đầu ra được xác định cụ thể. Một test case thường có hai thành phần dưới đây:

* Expected value: Giá trị mà chúng ta mong đợi khối lệnh trả về
* Actual value: Giá trị thực tế mà khối lệnh trả về

Sau khi thực hiện khối lệnh cần kiểm thử, chúng ta sẽ nhận được `actual value`. Lấy giá trị đó so sánh với `expected value`. Nếu hai giá trị này trùng khớp nhau thì kết quả của test case là `PASS`. Ngược lại, kết quả là `FAIL`.

### Application (hoặc Code) Under Test

 Application Under Test (AUT)) là thuật ngữ thường được dùng để chỉ đến hệ thống/ừng dụng đang được kiểm thử. Với hoạt động unit test, các đơn vị kiểm thử của chúng ta là những thành phần nhỏ nhất trong hệ thống nên có thể dùng các thuật ngữ khác phù hợp hơn như Code Under Test (CUT), hoặc OUT (Object Under Test).

### Mock và Stub

Đây là các thành phần bên ngoài được mô phỏng hoặc giả lập trong ngữ cảnh của hoạt động kiểm thử. Thông thường, để SUT hoạt động đúng chức năng thì sẽ cần đến những thành phần bên ngoài như Web Service, Database,... Ở cấp độ unit test, chúng ta cần phải tách rời các thành phần phụ thuộc này để có thể dễ dàng thực thi test case. Phần này sẽ được giải thích rõ hơn trong mục `Sử dụng Mockito (Mocking framework)`.

Lưu ý: Ngoài thuật ngữ mock và stub, thỉnh thoảng bạn sẽ gặp các thuật ngữ khác như Spy và Fake. Mặc dù có phần khác nhau về khái niệm nhưng chúng ta sẽ tạm thời cần biết về Mock và Stub.

## Thiết kế test case

Trong phần này, chúng ta sẽ tìm hiểu cấu trúc thường gặp ở một test case là gì, và xem xét một số yếu tố tạo nên một test case tốt. Dựa vào các đặc tính đó, chúng ta sẽ tìm hiểu những nguyên tắc để có thể thiết kế và thực hiện được các test case tốt.

### Cấu trúc một test case

Các trúc mã mà chúng ta nên tuân thủ trong một test case là `cấu trúc AAA`. Cấu trúc này gồm 3 thành phần:

* Arrange - Chuẩn bị dữ liệu đầu vào và các điều kiện khác để thực thi test case.
* Act - Thực hiện việc gọi phương thức/hàm với đầu vào đã được chuẩn bị ở `Arrange` và nhận về kết quả thực tế.
* Assert - So sánh giá trị mong đợi và giá trị thực tế nhận được ở bước `Act`. Kết quả của test case sẽ là một trong hai trạng thái sau:
  * PASS: nếu kết quả mong đợi và kết quả thực tế khớp nhau
  * FAIL: nếu kết quả mong đợi khác với kết quả thực tế

### Thành phần cố định (Fixture)

Là những thành phần được lặp đi lặp lại qua mỗi test case và có thể chia sẻ các thao tác chung giữa các test case. Ví dụ: thiết lập cấu hình hoặc chuẩn bị dữ liệu trước khi bộ test được thực thi, và dọn dẹp bộ nhớ sau khi hoàn thành. Thành phần cố định phải được đặt lên trên cùng của bộ kiểm thử.

Có hai loại thành phần cố định chính:

#### Setup

Là thành phần được thực thi trước khi test case thực thi. Trong một số thư viện xUnit (công cụ hỗ trợ viết và thực thi unit test), chúng ta thường gặp những phương thức/hàm, hoặc annotion có tên là `beforeEach`. Thành phần này chính là **Setup**.

#### One-Time Setup

Là thành phần được thực thi đầu tiên (trước cả khi cả `setup` và `test case` được thực thi). Trong một số thư viện xUnit (công cụ hỗ trợ viết và thực thi unit test), chúng ta thưsờng gặp những phương thức/hàm, hoặc annotion có tên là `beforeAll`. Thành phần này chính là **One-Time Setup**.

#### Teardown

Là thành phần được thực thi sau khi `test case` được thực thi. Trong một số thư viện xUnit (công cụ hỗ trợ viết và thực thi unit test), chúng ta thường gặp những phương thức/hàm, hoặc annotion có tên là `afterEach`. Thành phần này chính là **Teardown**.

#### One-Time Teardown

Là thành phần được thực thi sau cùng (sau khi tất cả `test case` và `teardown` được thực thi). Trong một số thư viện xUnit (công cụ hỗ trợ viết và thực thi unit test), chúng ta thường gặp những phương thức/hàm, hoặc annotion có tên là `afterAll`. Thành phần này chính là **One-Time Teardown**.

### Đặc tính của một unit test tốt

Một ca kiểm thử tốt sẽ có những đặc tính sau đây:

* Dễ viết - Có thể bao quát được nhiều trường hợp kiểm thử mà không mất quá nhiều công sức.
* Dễ đọc - Có thể mô tả được chính xác hành vi hoặc chức năng được kiểm thử.
* Tự động hoá - Có thể thực thi lặp lại nhiều lần.
* Dễ thực thi và thực thi nhanh.
* Đồng nhất  - Luôn trả về cùng kết quả sau mỗi lần chạy (nếu không thay đổi mã nguồn bên trong).
* Cô lập - Có thể thực thi độc lập mà không phụ thuộc vào các thành phần khác trong hệ thống. *Bạn có thể tham khảo mục "Sử dụng Mockito" để làm rõ hơn ý này.*
* Khi kết quả kiểm thử thất bại (FAILED), có thể dễ dàng tìm ra giá trị mong đợi và nhanh chóng xác định được vấn đề.

### Nguyên tắc viết kiểm thử

1. Mỗi test case là một phương thức độc lập, có thể thực thi mà không phụ thuộc vào bất kỳ test case nào khác.
2. Thứ tự thực hiện của mỗi test case không được ảnh hưởng đến kết quả thực thi.

### Quy ước đặt tên

#### Tên lớp chứa mã kiểm thử

Tên lớp chứa mã kiểm thử thường sử dụng hậu tố "Test" sau tên lớp được kiểm thử. Ví dụ: tên lớp là StockService thì tên lớp chứa mã kiểm thử sẽ là StockServiceTest.

#### Tên phương thức kiểm thử (test case)

Theo nguyên tắc, tên phương thức kiểm thử phải giải thích nhiệm vụ rõ ràng. Có thể tham khảo một số quy ướt đặt tên cho phương thức như sau:

1. Sử dụng từ **should**. Ví dụ: **favouriteStocksShouldbeSaved**, **todayPriceShouldBeShowed**.

```java
@Test
public void favouriteStocksShouldbeSaved() {}
```

2. Viết theo mẫu **Given[Đầu-Vào]When[Hành-Vi]Then[Kết-Quả-Mong-Đợi]**. Ví dụ:

```java
@Test
public void GivenNullUsernameWhenCreateStudentThenShouldThrowException() {}
```

3. Viết theo mẫu **when[hành-vi]_then[Kết-quả]**

```java
@Test
public void whenEnterValidUsernameAndPassword_thenLoginSuccessfully() {}
```

## Sử dụng JUnit

Hiện nay, JUnit được tích hợp và hỗ trợ ở phần lớn các IDE hiện tại cho Java. Việc sử dụng JUnit trong các dự án Java không khó. Các bạn có thể tìm hiểu cách cài đặt thư viện cho dự án của mình qua những hướng dẫn trên mạng.

Trong mục này, chúng ta sẽ cùng lượt qua những tính năng được hỗ trợ trong JUnit 5 - phiên bản mới nhất hiện nay.

### Các annotation trong JUnit

Sơ đồ dưới đây thể hiện thứ tự thực hiện các phương thức khi được đánh dấu với annotion tương ứng:

![quá trình thực hiện](_img/junit_annotations_2.png)

Các annotaion `@BeforeAll`, `@BeforeEach`,`@AfterEach`, `@AfterAll` là những **thành phần cố định**. Thực hiện các chức năng lặp đi lặp lại.

Annotation `@Test` được dùng để xác định một test case.

### Assertions

Assertions là lớp chứa các phương thức hỗ trợ đánh giá các điều kiện trong kiểm thử.

So với phiên bản trước, JUnit 5 vẫn giữ các phương thức cũ và thêm một số phương thức mới tận dụng những tích năng của Java 8.

Dưới đây là danh sách những phương thức assertion có trong JUnit5.

#### assertTrue và assertFalse

Phương thức `assertTrue` được dùng để kiểm tra kết quả của điều kiện có bằng `true` hay không. Ví dụ:

```java
@Test
public void whenAssertingConditions_thenVerified() {
    assertTrue(10 > 5, "10 lớn hơn 5");
}
```
Ngược lại, phương thức `assertFalse` được dùng để kiểm tra kết quả của điều kiện có bằng `false` hay không.

```java
@Test
public void whenAssertingConditions_thenVerified() {
    assertTrue(5 > 10, "5 không lớn hơn 10");
}
```

#### assertEquals và assertNotEquals

Phương thức `assertEquals` được dùng để kiểm tra giá trị mong đợi và thực tế có bằng nhau hay không. Ví dụ:

```java
@Test
public void whenAssertingConditions_thenVerified() {
  	String actual = new String("CodeGym").toUpperCase();
  	String expected = "CODEGYM";
  	assertEquals(expected, actual);
}
```

Ngược lại, Phương thức `assertNotEquals` được dùng để kiểm tra giá trị mong đợi và thực tế có bằng nhau hay không. Chúng ta cập nhật lại ví dụ ở trên:

```java
@Test
public void whenAssertingConditions_thenVerified() {
  	// thay thế phương thức toUpperCase() thành toLowerCase()
  	String actual = new String("CodeGym").toLowerCase();
  	String expected = "CODEGYM";
  	assertNotEquals(expected, actual);
}
```

Với trường hợp các giá trị chúng ta đang so sánh thuộc kiểu Object, `assertEquals` và `assertNotEquals` sẽ gọi phương thức  `equals` để so sánh giá trị.

Có một điểm cần lưu ý nếu giá trị là kiểu số thực (`float` hoặc `double`). Trên thực tế,  có nhiều trường hợp mà giá trị số thực mong đợi và thực tế có thể chênh lệch với nhau trong khoảng chấp nhận được. Với tình huống này, phương thức `assertEquals` và `assertNotEquals` hỗ trợ tham số thứ ba là `delta` (bên cạnh `expected` và `actual`). Chúng ta cùng xem qua ví dụ dưới đây:

```java
@Test
public void whenAssertingConditions_thenVerified() {
    float actual = 12 / 3.0001f;
    float expected = 4;
    assertEquals(expected, actual, 0.001f);
}
```

Kết quả của phép chia 12 cho 3.001 thì sẽ là một số thực 3.9998667. Kết quả của ca kiểm thử ví dụ trên là **PASSED** vì chúng ta đã cho phép mức chênh lệch tối đa là 0.001.

#### assertArrayEquals

Phương thức `assertArrayEquals` có thể xác nhận mảng mong đợi và thực tế có bằng nhau hay không. Chúng ta cùng xem xét ví dụ dưới đây:

```java
public void whenAssertingArraysEquality_thenEqual() {
    char[] expected = { 'C', 'o', 'd', 'e', 'G', 'y', 'm' };
    char[] actual = "CodeGym".toCharArray();
 
    assertArrayEquals(expected, actual, "Mảng phải giống nhau");
}
```

#### assertSame và assertNotSame

Khu chúng ta muốn xác nhận giá trị mong đợi và thực tế tham chiếu đến cùng một đối tượng hay không, chúng ta phải dùng `assertSame ` hoặc `assertNotSame`:

```java
@Test
public void whenAssertingSameObject_thenVerified() {
    String actual = new String("CodeGym");
    String expected = "CodeGym";
  
    assertSame(expected, actual);
}
```

Kết quả của phương thức test trên là **FAILED** vì hai biến `actual` và `expected` đang tham chiếu đến hai đối tượng khác nhau trong bộ nhớ.

Chúng ta nên lưu ý về sự khác nhau giữa `assertSame` và `assertEquals` (đã tìm hiểu ở ví dụ trước):

* `assertEquals` chỉ quan tâm đến giá trị có bằng nhau không (thông qua phương thức `equals`) mà không cần biết hai giá trị được so sánh có phải cùng là một đối tượng hay không.
* `assertSame` sẽ trả về PASSED chỉ khi cả hai biến cùng tham chiếu đến một đối tượng.

####  assertIterableEquals

`assertIterableEquals` so sánh các giá trị được chứa bên trong hai đối tượng kiểu Iterable. Để trả về kết quả PASSED, hai iterable phải trả về bằng số phân tử, giá trị của các phần tử đó và cả vị trí của các phần tử. Hãy cùng xem ví dụ dưới đây:

```java
@Test
public void givenTwoLists_whenAssertingIterables_thenEquals() {
    Iterable<String> al = new ArrayList<>(asList("CodeGym", "Coding", "Bootcamp", "Java"));
    Iterable<String> ll = new LinkedList<>(asList("CodeGym", "Coding", "Bootcamp", "Java"));

    assertIterableEquals(al, ll);
}
```

Kết quả của test case trên là **PASSED**. Phương thức `assertIterableEquals` chỉ so sánh giá trị các phần tử bên trong mà không quan tâm đến việc các phần tử này đang được lưu trữ tại hai biến thuộc kiểu khác nhau (ArrayList và LinkedList).

####  assertThrows

Để có thể xác nhận được phương thức đang kiểm thử có ném ra một ngoại lệ hay không, chúng ta có thể sử dụng `assertThrows`. Giả sử chúng ta có một phương thức như sau:

```java
static void throwAnException() {
	  throw new IllegalArgumentException("Tham số không hợp lệ");
}

```

Dùng cách thức dưới đây để kiểm tra xem phương thức `throwAnExcepiont()` có ném ra một ngoại lệ hay không, và ngoại lệ đó có phải là `IllegalArgumentException` hay không:

```java
@Test
void whenAssertingException_thenThrown() {
    Exception e = assertThrows(IllegalArgumentException.class, () -> throwAnException());
    assertEquals("Tham số không hợp lệ", e.getMessage());
}
```

#### Các assertion khác
* assertLinesMatch
* fail
* assertNotNull và assertNull
* assertAll
* assertTimeout và assertTimeoutPreemptively

## Sử dụng Mockito (Mocking framework)

Khi xây dựng phần mềm, SUT sẽ phụ thuộc vào các thành phần bên ngoài như cơ sở dữ liệu, API, hệ thống file,... Các thành phần phụ thuộc này có thể chưa sẵn sàng hoặc thậm chí chưa tồn tại ở thời điểm chúng ta viết Unit Test. Ngay cả khi những thành phần này đã được chuẩn bị sẵn sàng thì việc thực thi một test case có phụ thuộc sẽ chậm hơn vì phải cần thời gian đợi và tương tác với thành phần bên ngoài.

![thành phần phụ thuộc](_img/mock_diagram.png)

Cô lập SUT là một trong những kỹ thuật giúp giải quyết vấn đề trên. Và lúc này, chúng ta sẽ phải cần đến các mocking framework (tạm dịch là khung mô phỏng) để giả lập các thành phần bên ngoài, nhờ đó có thể cô lập và kiểm thử SUT dễ dàng hơn. Đối tượng mô phỏng này sẽ không gây phá vỡ cấu trúc mã nguồn khi đối tượng thật được thiết kế và triển khai.  Hình dưới đây thể hiện việc tạo hai đối tượng mô phỏng là Mock WS và Mock DB để thay thế sự phụ thuộc vào WebService và Database.

![thành phần phụ thuộc](_img/mock_diagram_using.png)

Việc tìm hiểu cách thiết lập và sử dụng các mocking framework này là bước quan trọng giúp mở rộng Unit Test cho các hệ thống lớn và phức tạp. Với lập trì viên Java, Mockito là một công cụ không thể thiếu.

###  Tạo đối tượng mô phỏng

Phương thức `mock()` cho phép chúng ta tạo đối tượng mô phỏng từ một class hoặc interface. Phương thức này không yêu cầu thêm gì khi sử dụng. Và nó có thể tạo các thuộc tính class mô phỏng hoặc các đối tượng mô phỏng cần dùng trong phương thức. Ví dụ dưới đây thể hiện cách tạo một đối tượng mô phỏng kiểu `UserRepository` (đã được định nghĩa trước):

```java
StockRepository stockRepository = mock(StockRepository.class);
```

### Mô phỏng hành vi

Sử dụng phương thức `when()` để mô phỏng hành vi của đối tượng. Để xác định kết quả thực hiện, chúng ta có thể sử dụng `thenReturn()` hoặc `thenThrow()`.

* `thenReturn()` trả về kết quả
* `thenThrow()` sẽ ném ra một ngoại lệ

```java
when(stockRepository.count()).thenReturn(100);
```

Nếu muốn trả về nhiều kết quả cho nhiều lần gọi, chúng ta sử dụng `thenReturn()` nhiều lần như sau;

```java
when(stockRepository.count())
  .thenReturn(50)
  .thenReturn(100)
  .thenReturn(200);

// Kết quả in ra màn hình sẽ là:
System.out.println(stockRepository.count()); // 50
System.out.println(stockRepository.count()); // 100
System.out.println(stockRepository.count()); // 200
```

### Kiểm chứng

Chúng ta có thể kiểm tra xem phương thức/hàm có được gọi hay không qua phương thức `verify()`.

```java
verify(stockRepository).count();
```

Mockito hỗ trợ những tham số giúp chúng ta có thể mở rộng khả năng kiểm chứng việc gọi phương thức như:

* Số lần gọi với `times()`
* Thời gian thực hiện với `timeout()`, giúp kiểm chứng thời gian thực hiện thuật toán có đảm bảo yêu cầu

Ví dụ:

```java
verify(stockRepository, times(2)).count(); 	// gọi 2 lần
verify(stockRepository, timeout(10)).count(); // 10 mili giây
```

### Kết hợp JUnit

Đây là đoạn mã ví dụ cách kết hợp Mockito và JUnit để viết mã kiểm thử đơn vị:

```java
@Test
public void tryMockitoMock() {
  StockRepository stockRepository = mock(StockRepository.class);

		when(stockRepository.count())
     .thenReturn(10);
 
    long stockCount = stockRepository.count();
  
    Assertions.assertEquals(10, stockCount);
    verify(stockRepository).count();
}
```

Ở phương thức trên, chúng ta mô phỏng hành vi lấy số lượng user thông qua phương thức `count()` được định nghĩa trong UserRepository. Khi `count()` được gọi, đối tượng mô phỏng sẽ trả về kết quả là `111` thay vì phải truy vấn vào cơ sở dữ liệu để lấy thông tin.

## Case Study

Dự án chúng ta thực hiện là một ứng dụng tra cứu danh mục đầu tư dành cho các nhà đầu tư cổ phiếu chứng khoán.

Ứng dụng cần có các tính năng sau:

1. Tra cứu giá các mã cổ phiếu đang đầu tư (dữ liệu được lấy về từ API miễn phí)
2. Quản lý danh sách các mã cổ phiếu đang quan tâm (bao gồm tìm kiếm theo mã, lưu mã vào danh sách quan tâm, xoá một mã nào đó khỏi danh sách)

Trước khi thực sự viết mã, hãy hình dung trong đầu và vẽ ra một bảng thiết kế bao gồm những interface, class và các phương thức có thể cần để thực hiện được ứng dụng này. Dựa vào bản thiết kế, hãy viết các unit test case cho ứng dụng.

### Hướng dẫn

#### Tạo một class mô phỏng với tên là StockDB

Để mô phỏng hành vi sau:

1. Lưu mã cổ phiếu (kiểu chuỗi) và trả về `true`
2. Lấy thông tin mã cổ phiếu và trả về giá trị kiểu chuỗi

Các bước thức hiện:

1. Tạo interface StockDB
2. Viết test case và code

#### Tạo class mô phỏng StockAPI

Các tình huống khi test

1. Phương thức cần test là `void` (không trả về dữ liệu)
2. Phương thức cần test gọi đến một phương thức khác cùng lớp
   Chức năng: Lưu danh sách mã cổ phiếu quan tâm/đang đầu tư (gửi request lên DB)

Hai tình huống trên có thể sử dụng `spy()` kết hợp `doReturn()`.

Nếu đây là lần đầu tiên bạn viết unit test, có lẽ sẽ gặp nhiều khó khăn. Nhưng đừng nản chí nhé! Hành trình phía trước sẽ còn ẩn chứa nhiều điều thú vị.

## Chặng đường tiếp theo

Cảm ơn bạn đã đồng hành cùng bài viết đến đây. Hy vọng những bước chân đầu tiên này sẽ mang lại nhiều ý nghĩa cho chặng đường học hỏi tiếp theo của bạn. Hãy tìm tòi và thực hành nhiều hơn để có thể làm chủ được kỹ năng Unit Test nói riêng và automation testing nói chung. Tới đây, chúng ta nên làm gì để học và thực hành hiệu quả hơn ở kỹ năng này?

Câu châm ngôn của mình là "Thế giới này thật là rộng lớn.. và có quá nhiều sách để đọc". Nên gợi ý đầu tiên luôn là đọc những đầu sách hay về Unit Test, Test-Driven Development và những chủ đề liên quan. Các bạn xem qua danh sách gợi ý bên dưới nhé!

### Sách nên tham khảo

* Test Driven Development: By Example - Tác giả: Kent Beck
* The Art of Unit Testing - 

### Website nên tham khảo
