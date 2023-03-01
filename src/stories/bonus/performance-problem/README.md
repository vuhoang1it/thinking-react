# React Performance Tuning

# 1, Giới thiệu

React là một thư viện Javascript được thiết kế với mục tiêu tối ưu hiệu năng của các ứng dụng. React sử dụng một số kỹ thuật để giảm thiểu cost của các DOM operations được yêu cầu để update UI xuống mức thấp nhất có thể. React cũng cung cấp các tools và các phương pháp để tăng performance khi có yêu cầu cho từng trường hợp cụ thể. Trong bài viết này, tôi sẽ giới thiệu với các bạn cách sử dụng React Perf library và shouldComponentUpdate lifecycle method để improve performance của quá trình re-rendering của ứng dụng

# 2, Re-rendering trong React

## 2.1, Reconciliation process

Bất cứ khi nào, chúng ta thay đổi state của React component thì quá trình re-rendering lại diễn ra. Theo đó React sẽ tạo ra một virtual DOM mới đại diện cho trạng thái của ứng dụng, React sẽ dụng virtual DOM này để tìm ra các DOM elements nào cần thay đổi, thêm , sửa, xóa. Quá trình này được gọi là quá trình đồng nhất thay đổi (Reconciliation).

Trong React, bất cứ khi nào chúng ta gọi setState trên một component, thay vì update DOM ngay lập tức React chỉ đánh dấu nó như 'dirty'. Các thay đổi tới state của component sẽ không có tác dụng ngay lập tức, React sẽ dùng một event loop để render các thay đổi.

![](https://images.viblo.asia/ac97834d-d95e-4abd-8569-968b05ff5b2f.png)

Event loop là một tiến trình trong Javascript mà sẽ lặp vô hạn. Nó kiểm tra tất cả các event handlers và các lifecycle methods mà cần được gọi. Bằng việc xử lý hàng loạt các tiến trình đồng nhất thay đổi, DOM được update chỉ một lần cho một event loop, đây chính là chìa khóa cho việc xây dựng một ứng dụng tối ưu.

## 2.2, Sub-tree rendering

Khi event loop kết thúc, React sẽ re-render các dirty components và các component con của nó, kể cả khi chúng không xảy ra sự thay đổi. Như vậy, để cho tối ưu hơn, chúng ta cần tìm ra cách chỉ re-rendering lại các component thực sự thay đổi. React cung cấp một cách để giải quyết vấn đề tối ưu này đó là sử dụng shouldComponentUpdate để chặn các component con bị re-rendering. Trước khi re-rendering một component con React sẽ gọi shouldComponentUpdate method. Mặc định thì shouldComponentUpdate sẽ luôn trả về true, nhưng nếu chúng ta reimplement nó và trả về false, React sẽ bỏ qua việc re-render component này và con của nó.

## 2.3, React Perf

React Perf là một tool mà sẽ cung cấp một cái nhìn tổng quan về performance của ứng dụng, nó giúp cho chúng ta xác định được các component nào chúng ta cần implement shouldComponentUpdate lifecycle hooks để tăng performance của ứng dụng. Cách sử dụng React Perf rất đơn giản, nó chỉ có 4 methods chính như tôi giới thiệu ở bảng dưới

Perf.start(), Perf.stop()

start/stop quá trình phân tích performance

Perf.printInclusive()

In ra toàn bộ thời gian mà component cần để mount

Perf.printExclusive()

In ra thời gian mà không được dùng để mount: thời gian xử lý props, gọi componentWillMount, componentDidMount

Perf.printWasted()

In ra thời gian mà component dùng để re-rendering lại các component con mà không có xảy ra sự thay đổi

# 3, Test Application

Chúng ta sẽ xây dựng một ứng dụng mà có vần đề về performance và sau đó sử dụng React Perf tool và shouldComponentUpdate lifecycle method để tối ưu lại nó. Ứng dụng này rất đơn giản, nó chỉ để show ra thời gian hiện tại. Nó sẽ có 3 components: App, Clock và Digit.

Trước hết, chúng ta cần cài đặt React Perf tool: thêm 'react-addons-perf' tới devDependencies của packages.json file sau đó chạy lệnh: npm install

## 3.1, Digit Component

```Javascript
import React, {Component, PropTypes} from 'react';

class Digit extends Component {
  render() {
    let digitStyle = {
      display: 'inline-block',
      fontSize: 20,
      padding: 10,
      margin: 5,
      background: '#eeeeee'
    };

    let displayValue = this.props.value < 10 ? ('0' + this.props.value) : this.props.value;
    return <div style={digitStyle}>{displayValue}</div>;
  }
}

Digit.propTypes = {
  value: PropTypes.number.isRequired
};

export default Digit;

```

Digit component được dùng để show ra giá trị của value property được truyền vào trong nó

### 3.2, Clock component

```Javascript
import React, {Component, PropTypes} from 'react';
import Digit from './Digit';

class Clock extends Component {
  render() {
    return (
      <div>
        <Digit value={this.props.hours} />{' : '}
        <Digit value={this.props.minutes} />{' : '}
        <Digit value={this.props.seconds} /> {' . '}
        <Digit value={this.props.tenths} />
      </div>
    );
  }
}

Clock.propTypes = {
  hours: PropTypes.number.isRequired,
  minutes: PropTypes.number.isRequired,
  seconds: PropTypes.number.isRequired,
  tenths: PropTypes.number.isRequired
};

export default Clock;

```

Ở trên, chúng ta đã tạo ra 4 Digit component instances đại diện cho 4 đơn vị: giờ, phút , giây của thời gian, số lượng tenths của milliseconds

### 3.3, App component

```Javascript
import React, {Component, PropTypes} from 'react';
import {render} from 'react-dom';
import Perf from 'react-addons-perf';
import Clock from './Clock';

class App extends Component {
  constructor() {
    super(...arguments);
    this.state = this.getTime();
  }

  componentDidMount() {
    setInterval(() => {
      this.setState(this.getTime())
    }, 10);
  }

  getTime() {
    let now = new Date();
    return {
      hours: now.getHours(),
      minutes: now.getMinutes(),
      seconds: now.getSeconds(),
      tenths: parseInt(now.getMilliseconds()/10)
    };
  }

  render() {
    let clocks = [];
    for(var i = 0; i < 200; i++) {
    clocks.push(<Clock key={i} hours={this.state.hours} minutes={this.state.minutes}
      seconds={this.state.seconds} tenths={this.state.tenths} />);
    }
    return (
      <div>
        {clocks}
      </div>
    );
  }
}

Perf.start();
render(<App />, document.getElementById('root'));
setTimeout(() => {
  Perf.stop();
  Perf.printWasted();
}, 2000);

```

Ở đây, chúng ta đã import 'react-addons-perf' vào trong app component và đã tạo một chu kỳ cứ 10 milliseconds lại thay đổi state của component một lần trong componentDidMount lifecycle method. Chúng ta cũng đã tạo ra 200 instances của Clock component.

Chạy lại ứng dụng trên Browser, có thể thấy được rằng tốc độ của nó rất chậm. Bật console của Browser để quan sát output từ React Perf

![](https://images.viblo.asia/863f3c36-fb10-4c66-971e-8b5cc32830e9.png)

Có thể thấy tổng số wasted time đã lên tới gần 0,4 giây, số lượng wasted component đã lên tới 13400 tức là có 13400 instances của Digit Component không xảy ra sự thay đổi do đó không cần phải re-rendering lại chúng. Để thực hiện điều này, chúng ta cần implement shouldComponentUpdate method trong Digit component

```Javascript
import React, {Component, PropTypes} from 'react';

class Digit extends Component {

  shouldComponentUpdate(nextProps, nextState) {
    return nextProps.value !== this.props.value;
  }

  render() {
    …................
  }
}

…............

export default Digit;

```

Trong shouldComponentUpdate method chúng ta đã thực hiện so sánh value của next Props và props hiện tại nếu chúng khác nhau thì mới thực hiện re-rendering lại Digit Component. Chạy lại ứng dụng trên Browser, có thể thấy nó đã nhanh lên rất nhiều và output của React Perf chỉ ra rằng không có wasted components nào cả.

![](https://images.viblo.asia/5e49b186-914c-4cf2-91f3-3b7078a2fd5c.png)

Vậy là quá trình đi tìm cách optimize performance ứng dụng của chúng ta đã cho những kết quả tích cực nhờ việc chúng ta đã kết hợp sử dụng React Perf tool và shouldComponentUpdate lifecycle method ở đúng nơi, đúng chỗ.

# 4, Kết luận

React được thiết kế trên tư tưởng tối ưu performance, nên trong thực tế tốc độ của các ứng dụng đã rất tốt rồi. Việc sử dụng shouldComponentUpdate chỉ thực sự cần thiết cho các ứng dụng quá phức tạp và nhiều vòng lặp, để tránh code trở nên phức tạp, khó debug. Bài viết của tôi đến đây là hết, cảm ơn các bạn đã theo dõi, hẹn gặp lại ở các bài viết tiếp theo.
