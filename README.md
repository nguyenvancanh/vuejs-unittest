## Unittest trong VueJS

Như các bạn biết, Unit test là một phần cơ bản của công việc phát triển phần mềm. Unit test sẽ thực hiện những đơn vị nhỏ nhất của code (đến từng function, method, ...) độc lập với những đơn vị code khác trong hệ thống. Để điều tra, phát hiện bug, cũng như là dễ dàng trong việc thêm vào những tính năng mới. Những file Vue component giúp chúng ta dễ dàng viết unit test cho chúng. Với việc viết unit test cho từng component riêng biệt, bạn sẽ tự tin thêm vào project những tính năng mới mà không sợ phá vỡ cấu trúc vốn có của các component vốn có, và với việc viết unit test cũng giúp cho những lập trình viên khác trong team của bạn có thể hiểu được compoent bạn viết có chức năng như thê nào?

## Tại sao cần test component?

Như đã nói ở phần mở đầu, với việc code của bạn có unit test, đoạn code bạn viết ra sẽ có được độ tin cây hơn rất nhiều so với đoạn code mà không có unit test. Một vài lợi ích cụ thể của việc test có thể nêu ra như sau:

- Cung cấp tài liệu và cách thức hoạt động của component
- Tiết kiệm thời gian kiểm tra thủ công
- Giảm lỗi khi phát triển các tính năng mới
- Cải thiện thiết kế
- Tạo điều kiện tái cấu trúc
- Cho phép các nhóm lớn và những lập trình viên duy trì những đoạn mã phức tạp

Một số yêu cầu cơ bản đối với unit test cho compoent các bạn nên biết

- Thời gian thực thi nhanh chóng (Fast to run)
- Code uint test cần ngắn gọn, dễ hiểu.
- Chỉ được kiểm tra ở mức độ unit

## First Test

Trước tiên, chúng ta sẽ tạo một component rồi tiến hành viết test cho nó. Hãy tạo một file trong thư mục src\components của dự án của bạn với tên là _List.vue_ với nội dung như sau:

```
<template>
  <div>
    <h1>My To Do List</h1>
    </br>
    <!--displays list -->
    <ul>
      <li v-for="item in listItems">{{ item }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'list',
  data () {
    return {
      listItems: ['buy food', 'play games', 'sleep'],
    }
  }
}
</script>
```

Xem qua code một chút, trong components bên trên, danh sách các item được lưu trong mảng _listItems_, và được hiển thị ở trong template bằng v-for. Để có thể xem được danh sách trên web, các bạn thêm một thao tác nhỏ nữa đó là định nghĩa route cho component của chúng ta. Bằng cách, mở file _src/router/index.js_và thêm đoạn code sau

```
import Vue from 'vue'
import Router from 'vue-router'
import Hello from '@/components/Hello'
import List from '@/components/List'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'Hello',
      component: Hello
    },
    {
      path: '/to-do',
      name: 'ToDo',
      component: List
    },
  ]
})

```

Bây giờ, chạy lại npm vào truy cập vào đường dẫn 

```
localhost:8080/#/to-do
```

và kiểm tra list item đã được hiển thị trên màn hình hay chưa, nếu chưa có danh sách hiện lên, hãy check thẻ Console trên trình duyện và thử debug code của bạn xem đã có lỗi gì xảy ra nhé

Việc chuẩn bị component để test đã hoàn tất, bây giờ, tôi muốn test xem dữ liệu có được hiển thị đúng hay không. Trong thư mục _test/unit/specs_ tạo file _List.spec.js_ với đoạn code sau

```
import List from '@/components/List';
import Vue from 'vue';

describe('List.vue', () => {

  it('displays items from the list', () => {
      // our test goes here
  })
})
```

Trong file này, chúng ta đã import file List.vue từ thư mục component bên trên. Nói một cách khác,chúng ta đang gọi file compoent này ra để test xem code trong đó đã hiển thị đúng dữ liệu mong muốn hay chưa. Đây là cấu trúc cơ bạn của Mocha test. Tiếp theo, hãy thêm dòng code sau dưới dòng comment // our test gose heare

```
const Constructor = Vue.extend(List);
const ListComponent = new Constructor().$mount();
```

Ở đây, chúng tea extend Vue và mount vào component List. Điều quan trọng là phải mount component, vì nó dùng để trích xuất HTML tỏng component mà chúng ta viết. Sau khi mount component hoàn tất, chúng ta bắt đầu với assertion đầu tiên. Trong ví dụ này, chúng ta sẽ sử dụng "expect", "should", "assert", cái mà "Chai assertion library" cung cấp. Thêm đoạn code sau sau khi mount compnent

```
expect(ListComponent.$el.textContent).to.contain('play games');
```
Chúng ta có thể lấy HTML của component bằng cách sử dụng _ListComponent.$el_, và muốn lấy duy nhất text trên HTML thì chúng ta sử dụng _ListComponent.$el.textContent_. Câu lệnh bên trên nghĩa là chúng ta đang kiểm tra xem những text đang hiển thị trên màn hình có bao gồm dòng chữ _play games _ hay không? "play games" là dữ liệu có trong listItems trong component List.vue mà chúng ta đã định nghĩa ở bước đầu tiên, các bạn có thể kéo lên để kiểm tra lại.

Để chạy file test chúng ta vừa tạo ra, sử dụng lệnh

```
npm run unit
```

Kết quả sẽ được hiển thị trên màn hình console của bạn, nếu tất cả các check đều pass, bạn sẽ thấy màn hình hiện chữ màu xanh, nếu có bất kỳ lỗi nào thì sẽ có thông báo màu đỏ, và hiển thị lỗi kèm theo. lúc đó hãy chịu khó ngồi đọc những dòng thông báo lỗi và debug nó nhé. 

## Simulating User Input 

Với ví dụ bên trên, chắc các bạn cũng đã hiểu được cơ bản về unit test trong Vuejs rồi đúng không? Trong mục tiếp theo này, chúng ta sẽ cho phép người dùng nhập thêm item mới vào danh sách item ở component List.vue. Và cuối cùng, đương nhiên là chúng ta sẽ thực hiện viết unit test cho việc nhập input này. 

Trước hết, để chương trình của chúng ta có thể input được dữ liệu mới, thì các bạn cần update code trong file List.vue như sau

```
<template>
  <div>
    <h1>My To Do List</h1>
    </br>
    <input v-model="newItem" >
    <button @click="addItemToList">Add</button>
    <!-- displays list --> 
    <ul>
      <li v-for="item in listItems">{{ item }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'test',
  data () {
    return {
      listItems: ['buy food', 'play games', 'sleep'],
      newItem: ''
    }
  },
  methods: {
      addItemToList() {
        this.listItems.push(this.newItem);
        this.newItem = '';
      }
  }
}
</script>

```
Chúng ta dùng v-model để bind dữ liệu vào biến newItem, cái được lưu lại trong component data, với giá trị default là blank, các bạn có thể thấy methods _addItemToList_, trong method này chúng ta sẽ tiến hành push giá trị mới được nhật vào từ newItem vào mảng listItems. Chạy lại web để test xem có lỗi gì không nhé. 

Để tiến hành test cho hành động add new items này, bạn hãy tạo mới empty test trong file List.spec.js như sau

```
it('adds a new item to list on click', () => {
    // our test goes here
})
```

Thay vì hành động input ở giao diện người dùng, thì trong unit test chúng ta sẽ đặt gíá trị cho biến newItem bằng cách sau

```
// build component
const Constructor = Vue.extend(List);
const ListComponent = new Constructor().$mount();

// set value of new item
ListComponent.newItem = 'brush my teeth';
```

Tiếp theo, chúng ta cần click vào button Add, chúng ta có thể tìm butotn đó trên HTML bằng cách sau: 

```
// find button
const button = ListComponent.$el.querySelector('button');
```

Sau khi đã tìm được button, thì sự kiện click lên butotn đó sẽ được viết như sau

```
// simulate click event
const clickEvent = new window.Event('click');
button.dispatchEvent(clickEvent);
ListComponent._watcher.run();
```

Và cuối cùng, để test của chúng ta có ý nghĩa, thì cần asert một số điều kiện để check nữa chứ

```
//assert list contains new item
expect(ListComponent.$el.textContent).to.contain('brush my teeth');
expect(ListComponent.listItems).to.contain('brush my teeth');
```
Đây là full đonạ code mà mình đã diễn giải tưng phần ở trên

```
import List from '@/components/List';
import Vue from 'vue';

describe('List.vue', () => {
  it('displays items from the list', () => {
    const Constructor = Vue.extend(List);
    const ListComponent = new Constructor().$mount();
    expect(ListComponent.$el.textContent).to.contain('play games');
  })

  it('adds a new item to list on click', () => {
    // build component
    const Constructor = Vue.extend(List);
    const ListComponent = new Constructor().$mount();

    // set input value
    ListComponent.newItem = 'brush my teeth';

    // simulate click event
    const button = ListComponent.$el.querySelector('button');
    const clickEvent = new window.Event('click');
    button.dispatchEvent(clickEvent);
    ListComponent._watcher.run();

    // assert list contains new item
    expect(ListComponent.$el.textContent).to.contain('brush my teeth');
    expect(ListComponent.listItems).to.contain('brush my teeth');
  })
})
```

Bây giờ, bạn có thể chạy lại test và kiểm tra kết quả nhé.

Hy vọng bài chia sẻ của mình có thể giúp các bạn hiểu cơ bản được về unit test trong Vuejs, hãy đào xâu nghiên cứu và áp dụng cho dự án của riêng bạn nhé. Chúc c
