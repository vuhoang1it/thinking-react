# Các cách quản lý call API và lưu trữ data sau khi call

React nổi tiếng với việc xây dựng các giao diện người dùng phong phú và có khả năng mở rộng cao. Có nhiều cách để lấy dữ liệu từ một external API trong React. Trong bài này, chúng ta sẽ thảo luận về các cách khác nhau để quản lý các lệnh gọi API trong React. Cuối cùng, bạn sẽ có thể chọn cách tiếp cận tốt nhất tùy theo request của ứng dụng.

## 1. The Fetch API

**Fetch AP**I được tích hợp vào hầu hết các trình duyệt hiện đại trên window object (window.fetch) và cho phép thực hiện các request HTTP rất dễ dàng. Các đoạn code sau đây cho thấy một ví dụ đơn giản về việc sử dụng Fetch API trong thực tế.

```none
import {useEffect} from "react";

const fetchUsers = () => {
    // Where we're fetching data from
    return fetch("http://www.abc.cd/test")
    // We get the API response and receive data in JSON format
      .then((response) => response.json())
      .then((data) => console.log(data))
      .catch ((error) => console.error(error));
}

```

Mục tiêu duy nhất của function này là truy cập dữ liệu và convert response sang dạng json, sử dụng method response.json(). Ở đây việc sử dụng method json() để lấy response object được lưu trữ dữ liệu và được update state của users trong ứng dụng. Thực tế là Fetch dựa trên promise-nghĩa là chúng ta có thể catch errors sử dụng method .catch(). Bất kỳ lỗi nào gặp phải đều sử dụng làm giá trị để update state error. Thêm vào đó, ta thực hiện request này trong useEffect() hook với mảng phụ thuộc làm đối số thứ hai để thực hiện request một lần mà không phụ thuộc vào data khác. Dưới đây là ví dụ cách sử dụng useEffect() hook:

```none
import {useEffect} from "react";

useEffect(() => {
    fetchData()
  }, []);

```

## 2. Axios Library

**Axios** là một ứng dụng HTTP dựa trên Promise cho JavaScript có thể được sử dụng trong ứng dụng front-end và Node.js. Bằng cách sử dụng Axios, thật dễ dàng để gửi các requests HTTP không đồng bộ đến các điểm cuối REST và thực hiện các hoạt động CRUD.

Trước tiên chúng ta phải cài đặt Axios bằng cách sử dụng npm hoặc yarn và sau đó import vào parent component, như sau:

```none
npm install axios

```

Dưới đây là đoạn code nhỏ ví dụ việc sử dụng axios:

```none
import axios from "axios"

const fetchData = () => {
return axios.get("http://www.abc.cd/test")
   .then((response) => console.log(response.data));
}

```

Tương tự như Fetch API, Axios return một promise. Nhưng đối với Axios, nó sẽ return về một Json response.

## 3. Async-Await syntax

**Async / await** là một cách tương đối mới để viết code không đồng bộ một cách đồng bộ.

Từ khóa `async` trước một function có hai tác dụng:

- Make it always return a promise. (Luôn luôn return một promise)
- Allows await to be used in it. (Cho phép await để sử dụng trong đó)

Từ khóa `await` trước một promise làm cho JavaScript đợi cho đến khi promise đó hoàn thành và sau đó:

- If it’s an error, the exception is generated. (Nếu đó là một lỗi, ngoại lệ sẽ được tạo ra)
- Otherwise, it returns the result. (Nếu không, nó trả về kết quả)

Đoạn code dưới đây sẽ giúp bạn dễ hiểu hơn:

```none
async function fetchData() {
    try {
      const result = await axios.get("http://www.abc.cd/test")
      console.log(result.data));
    } catch (error) {
      console.error(error);
    }
  }

```

> Khi chúng ta sử dụng `useEffect()`, hàm `effect` (đối số đầu tiên) không thể trở thành một hàm async. Vì vậy, chúng ta có thể tạo một hàm async riêng biệt trong component, hàm này chúng ta có thể gọi sync trong `useEffect` và lấy dữ liệu cho phù hợp.

## 4. Custom React Hook

Custom Hook là một hàm JavaScript có tên bắt đầu bằng "use" và có thể gọi các Hook khác. Ý tưởng đằng sau custom hooks là extract component logic thành các functions có thể sử dụng lại. Vì vậy, hãy gọi custom hook: `useFetch`. Hook này chấp nhận hai đối số, URL mà chúng ta cần query để lấy dữ liệu và một đối tượng đại diện cho các option mà chúng ta muốn áp dụng cho request. Bây giờ, hãy xem việc lấy dữ liệu bằng hook `useEffect()`của chúng ta dễ dàng như thế nào. Chúng ta sẽ sử dụng Fetch API để thực hiện request của mình. Để làm được điều đó, ta phải pass URL và các options mà chúng tôi muốn nhận. Từ đó, ta nhận được một object có thể sử dụng để render trong ứng dụng của mình.

```none
import { useState, useEffect } from 'react';
const useFetch = (url = 'http://www.abc.cd/test', options = null) => {
  const [data, setData] = useState(null);
  useEffect(() => {
    fetch(url, options)
      .then(res => res.json())
      .then(data => setData(data));
  }, [url, options]);
  return {data}
}
export default useFetch;

```

Chúng ta có thể call bất cứ khi nào ta cần lấy dữ liệu:

```none
import useFetch from './useFetch';
  const { data } = useFetch( 'http://www.abc.cd/test' );
  console.log(data);

```

## 5. Lưu trữ data sau khi call

Ta có thể sử dụng data để xử lý các actions liên quan sau khi call thành công từ API, nhưng để sử dụng data đó để làm bộ nhớ chung cho các màn đều có thể sử dụng được thì ta nên cần quản lý và lưu trữ data đó thông qua Redux.
[Mời các bạn tham khảo chi tiết về Redux tại đây.](../redux/README.md)
