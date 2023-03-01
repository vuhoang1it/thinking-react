# Áp dụng Testing vào React cho ứng dụng của bạn như thế nào?

Khi xây dựng một ứng dụng React, việc đảm bảo tính ổn định và chất lượng của sản phẩm là điều rất quan trọng. Một ứng dụng không ổn định và không chất lượng có thể gây ra những vấn đề nghiêm trọng cho người dùng, ảnh hưởng đến trải nghiệm sử dụng và gây mất mát về thương hiệu. Vì vậy, việc áp dụng **Testing** trong quá trình phát triển React là một yêu cầu bắt buộc để đảm bảo tính đáng tin cậy của sản phẩm.

Trong bài viết này, chúng ta sẽ tập trung vào Testing trong React và những cách để đảm bảo tính đáng tin cậy và chất lượng của ứng dụng của bạn, tìm hiểu về các loại Testing khác nhau và cách áp dụng chúng vào React.

## I. Testing trong React JS là gì?

Testing trong React là quá trình kiểm tra tính đúng đắn và chức năng của các thành phần trong ứng dụng React. Nó giúp phát hiện sớm các lỗi, giảm thiểu chi phí và thời gian phát triển, đảm bảo rằng các thay đổi không gây ra vấn đề mới. Các kỹ thuật kiểm tra phổ biến cho React bao gồm Unit Testing, Integration Testing, Snapshot Testing và End-to-End Testing.

## II. Các components quan trọng trong Testing

### Hàm describe() và it()

`describe()` và `it()` là hai hàm quan trọng trong Jest, một trong những công cụ phổ biến nhất để kiểm thử ứng dụng React. Cả hai hàm này được sử dụng để định nghĩa các bài kiểm thử và tạo ra các test suites. Dưới đây là một số thông tin chi tiết về `describe()` và `it()`:

Bài viết này được đăng tại [free tuts .net]

### Hàm describe()

`describe()` được sử dụng để định nghĩa một test suite trong Jest. Test suite là một nhóm các bài kiểm thử liên quan đến nhau. Hàm `describe()` nhận vào hai tham số:

1.Một chuỗi mô tả về test suite.

2. Một hàm `callback` chứa các bài kiểm thử.

Ví Dụ:

```
describe('Example test suite', () => {
  it('should do something', () => {
    // Your test code goes here
  });
});
```

### Hàm it()

`it()` được sử dụng để định nghĩa một bài kiểm thử trong Jest. Mỗi bài kiểm thử đều phải nằm trong một test suite được định nghĩa bởi hàm `describe()`. Hàm `it()` nhận vào hai tham số:

1. Một chuỗi mô tả về bài kiểm thử.
2. Một hàm callback chứa mã kiểm thử.

Ví dụ:

```
describe('Example test suite', () => {
  it('should do something', () => {
    // Your test code goes here
  });
});
```

### Hàm expect()

`expect()` là một hàm quan trọng trong **Jest**, một trong những công cụ phổ biến nhất để kiểm thử ứng dụng React. Hàm này được sử dụng để kiểm tra các giá trị và xác định liệu chúng có đúng như mong đợi hay không. Dưới đây là một số thông tin chi tiết về `expect()`:

**Sử dụng `expect()`**

- `Hàm expect()` nhận vào một giá trị cần kiểm tra và trả về một đối tượng có các phương thức kiểm tra khác nhau. Các phương thức này bao gồm:
- `toBe(value)`: kiểm tra xem giá trị có bằng value hay không (dùng cho kiểu dữ liệu nguyên thuỷ như number, string, boolean, null, undefined).
- `toEqual(value)`: kiểm tra xem giá trị có bằng value hay không (dùng cho kiểu dữ liệu phức tạp như object, array).
- `not.toBe(value)`: kiểm tra xem giá trị có khác với value hay không.
- `toBeTruthy()`: kiểm tra xem giá trị có đúng là truthy hay không.
- `toBeFalsy()`: kiểm tra xem giá trị có đúng là falsy hay không.
- Và nhiều phương thức khác.

**Ví dụ:**

```
test('adds 1 + 2 to equal 3', () => {
  expect(1 + 2).toBe(3);
});
```

### So sánh với toBe() và toEqual()

Khi so sánh giá trị, bạn có thể sử dụng `toBe()` hoặc `toEqual()`. Tuy nhiên, có một điều cần lưu ý rằng:

- `toBe()` so sánh địa chỉ vùng nhớ của các giá trị. Do đó, nó chỉ hoạt động cho các giá trị nguyên thuỷ (number, string, boolean, null, undefined).
- `toEqual()` so sánh giá trị thực sự của các giá trị. Do đó, nó hoạt động cho cả giá trị nguyên thuỷ và phức tạp (**object, array**).
  Ví dụ:
  ```
  test('object assignment', () => {
  const data = { one: 1 };
  data['two'] = 2;
  expect(data).toEqual({ one: 1, two: 2 });
  });
  ```
  Trong ví dụ trên, nếu bạn sử dụng `toBe()` thay vì `toEqual()`, bài kiểm thử sẽ không được chấp nhận, bởi vì `toBe()`không so sánh được các giá trị phức tạp như object.

### Hàm render()

`render()` là một trong những phương thức quan trọng nhất trong **Jest** và React Testing Library, giúp bạn render một component React và trả về một đối tượng wrapper. Dưới đây là một số thông tin chi tiết về `render()`:

**Sử dụng `render()`**

Phương thức `render()` được sử dụng để render một component React vào DOM ảo (virtual DOM) của Jest. Kết quả trả về là một đối tượng wrapper chứa các phương thức để tìm kiếm và tương tác với các phần tử trong component.
Ví dụ:

```
import { render } from '@testing-library/react';

test('renders a heading', () => {
  const { getByRole } = render(<MyComponent />);
  const heading = getByRole('heading', { name: /hello/i });
  expect(heading).toBeInTheDocument();
});
```

Trong ví dụ trên, `render()` được sử dụng để render component `<MyComponent />` vào DOM ảo. Kết quả trả về được lưu vào biến `{ getByRole }`, sau đó được sử dụng để tìm kiếm phần tử heading và kiểm tra xem nó có tồn tại trong DOM hay không.

**Tham số của `render()`**

Phương thức `render()` nhận vào một số tham số để cấu hình cách render component, bao gồm:

- **component**: component React cần render.
- **options**: tùy chọn cấu hình, bao gồm các thuộc tính như **wrapper, baseElement, queries, container, hydrate, wrapperOptions** và **renderOptions.**
  Ví dụ:

  ```
  import { render } from '@testing-library/react';
  test('renders a component with props', () => {
  const { getByRole } = render(
  <MyComponent name="John" age={30} />
   );
  const heading = getByRole('heading', { name: /hello/i });
  expect(heading).toBeInTheDocument();
  });

  ```

### Hàm act()

Trong React Testing, **act()** là một phương thức quan trọng để đảm bảo rằng các thao tác giao diện (user interactions) và cập nhật state của component được thực hiện đồng bộ và đúng cách.

Ví dụ, khi một component React được render, React sẽ đưa component đó vào trong một hàng đợi (queue) để xử lý. Nếu ta muốn thực hiện một thao tác giao diện như click vào một nút, chúng ta cần đảm bảo rằng các hành động này được xử lý đồng bộ, tức là chúng ta phải chờ đợi component được update trước khi kiểm tra kết quả của test.

Ví dụ sau sẽ minh họa cách sử dụng **act()** để kiểm tra một component React đơn giản:

Ví Dụ:

```
import { render, screen, act } from '@testing-library/react';
import MyComponent from './MyComponent';

test('renders the component with the correct text', async () => {
await act(async () => {
  render(<MyComponent />);
});

expect(screen.getByText('Hello, world!')).toBeInTheDocument();
});
```

Ở ví dụ này, chúng ta sử dụng `act()` để đảm bảo rằng component MyComponent đã được render hoàn toàn trước khi kiểm tra kết quả của test. Chúng ta sử dụng **async/await** để đợi cho `act()` hoàn thành việc thực hiện các hành động.

Ngoài ra, `act()` còn hỗ trợ cho việc update state của component React. Ví dụ sau minh họa cách sử dụng `act()` để cập nhật state của một component:

```
import { render, screen, act } from '@testing-library/react';
import MyComponent from './MyComponent';

test('renders the component with the updated state', async () => {
  await act(async () => {
    render(<MyComponent />);
  });

  const button = screen.getByRole('button');
  expect(button).toBeInTheDocument();

  await act(async () => {
    button.click();
  });

  expect(screen.getByText('Clicked!')).toBeInTheDocument();
});
```

Ở ví dụ này, chúng ta sử dụng `act()` để cập nhật state của component khi người dùng click vào một nút. Chúng ta sử dụng **async/await** để đợi cho `act()` hoàn thành việc thực hiện các hành động và kiểm tra kết quả của test.

`act()` là một công cụ quan trọng giúp chúng ta kiểm tra các component React của mình một cách chính xác và đáng tin cậy. Tuy nhiên, cần lưu ý rằng việc sử dụng `act()` không phải là bắt buộc cho tất cả các test. Chúng ta nên sử dụng `act()` trong những trường hợp cần thiết.

## Kết Luận

Testing là một phần quan trọng trong quá trình phát triển ứng dụng React. Với các công cụ và kỹ thuật kiểm tra như Jest, React Testing Library, Enzyme, Snapshot Testing và Integration Testing, bạn có thể đảm bảo rằng ứng dụng của mình hoạt động đúng như mong đợi và đáp ứng được nhu cầu của người dùng. Điều này sẽ giúp cải thiện chất lượng và độ tin cậy của ứng dụng của bạn, tạo ra trải nghiệm tốt hơn cho người dùng.

## Top những câu hỏi được hỏi nhiều nhất về Testing trong React Js

Tại sao chúng ta cần Testing trong React?

Chúng ta cần Testing trong React để đảm bảo rằng các thành phần, hàm và tương tác giữa chúng hoạt động đúng như mong đợi, tránh được các lỗi khó phát hiện, nâng cao chất lượng của ứng dụng, giảm thiểu rủi ro và tiết kiệm thời gian và chi phí cho việc sửa chữa lỗi sau này.

### Các loại Testing nào có thể áp dụng trong React?

Các loại Testing mà có thể áp dụng trong React gồm:

- **Unit Testing**: kiểm tra từng thành phần (component), hàm, và module một cách độc lập, phân tích kết quả trả về và đảm bảo tính đúng đắn của chúng.
- **Integration Testing**: kiểm tra các thành phần tương tác với nhau, đảm bảo tính đồng bộ và hợp lý của chúng.
- **End-to-end Testing**: kiểm tra tính năng và tương tác của các thành phần, hàm, và mô-đun từ góc độ người dùng cuối, đảm bảo ứng dụng hoạt động đúng và đáp ứng được yêu cầu của người dùng.

### Jest là gì và tại sao nó được sử dụng trong Testing trong React?

Jest là một framework testing được sử dụng phổ biến trong React và các ứng dụng JavaScript khác. Jest cung cấp nhiều tính năng hữu ích để kiểm tra React components và các hàm JavaScript, như snapshot testing, mocking, và coverage report. Nó được tích hợp sẵn với React và được hỗ trợ bởi Facebook.

### Enzyme là gì và tại sao nó được sử dụng trong Testing trong React?

Enzyme là một thư viện testing được sử dụng cho React, cho phép kiểm tra các components và các tương tác giữa chúng trong môi trường rendering ảo (virtual DOM). Enzyme cung cấp nhiều API đơn giản và dễ sử dụng cho việc kiểm tra components, với khả năng tương thích với các framework testing khác như Jest, Mocha, hoặc Chai. Enzyme cho phép kiểm tra các thành phần bên trong components, tạo ra các sự kiện giả lập và xác thực các props và state của components.
