---
title: Giới thiệu
type: guide
order: 2
---

## Vue.js là gì?

Vue (phát âm là /vjuː/, giống **view**) là một **progressive framework** dùng để xây dựng giao diện người dùng ( UI ). Không giống các monolithic framework ( những framework hỗ trợ đầy đủ tất cả mọi thứ cần có để xây dựng app, trong một framework duy nhất ). Về cốt lõi, Vue chỉ tập trung vào lớp view mà thôi, và nó rất dễ để làm quen và tích hợp với các thư viện khác hoặc các project có sẵn. Mặt khác, Vue lại chứa đựng một sức mạnh rất lớn trong việc xây dựng Single-Page Applications khi được kết hợp với [công cụ build](single-file-components.html) và [các thư viện/component được xây dựng bởi cộng đồng](https://github.com/vuejs/awesome-vue#libraries--plugins).

Nếu bạn là một frontend developer có kinh nghiệm và muốn so sánh Vue với các thư viện/framework khác thì hãy xem ở đây [So sánh Vue với các framework khác](comparison.html).

## Bắt đầu

<p class="tip">Các hướng dẫn này đòi hỏi bạn phải có một kiến thức khá vững về HTML, CSS và Javascript. Nếu bạn chỉ mới làm quen với frontend, thì việc tìm hiểu một framework ở ngay giai đoạn đầu tiên này không phải là một ý hay - hãy học những điều cơ bản trước và quay lại sau! Bạn cũng nên làm quen với các framework khác, nhưng điều này không bắt buộc.</p>

Cách đơn giản nhất để thử Vue.js là xem qua [Hello World trên JSFiddle](https://jsfiddle.net/chrisvfritz/50wL7mdz/). Bạn có thể mở link đó ở một tab mới và dùng nó như một trình soạn thảo cho các ví dụ đơn giản của chúng ta. Hoặc bạn cũng có thể tạo một file `html` và chèn Vue vào như sau:

``` html
<script src="https://unpkg.com/vue/dist/vue.js"></script>
```

Trang [cài đặt](installation.html) sẽ cung cấp cho bạn nhiều tùy chọn hơn để cài đặt Vue. Lưu ý rằng chúng ta **không khuyến khích** những người mới (bắt đầu tìm hiểu Vue) sử dụng `vue-cli`, đặc biệt nếu bạn vẫn chưa rành về các công cụ build dựa trên Node.js

## Rendering

Cốt lõi của Vue.js là một hệ thống cho phép chúng ta render dữ liệu đến DOM bằng cú pháp template rõ ràng, đơn giản:

``` html
<div id="app">
  {{ message }}
</div>
```
``` js
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```
{% raw %}
<div id="app" class="demo">
  {{ message }}
</div>
<script>
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
</script>
{% endraw %}

Chúng ta vừa tạo xong một ứng dụng Vue đầu tiên! Nhìn nó có vẻ như chỉ đơn giản là render một string thôi, nhưng thực ra thì Vue đã làm khá nhiều việc ở bên dưới vẻ ngoài đơn giản đó. Dữ liệu và DOM được liên kết với nhau, bây giờ, mọi thứ đã [reactive](https://en.wikipedia.org/wiki/Reactive_programming). Làm sao chúng ta biết được điều đó? Chỉ cần mở Javascript console của trình duyệt lên, sau đó thử thay đổi giá trị của `app.message` xem, bạn sẽ thấy nội dung được render bị thay đổi theo theo.

Ngoài việc chèn text bằng cú pháp như trên, chúng ta còn có thể bind nó như ví dụ dưới đây: 

``` html
<div id="app-2">
  <span v-bind:title="message">
    Hover your mouse over me for a few seconds to see my dynamically bound title!
  </span>
</div>
```
``` js
var app2 = new Vue({
  el: '#app-2',
  data: {
    message: 'You loaded this page on ' + new Date()
  }
})
```
{% raw %}
<div id="app-2" class="demo">
  <span v-bind:title="message">
    Hover your mouse over me for a few seconds to see my dynamically bound title!
  </span>
</div>
<script>
var app2 = new Vue({
  el: '#app-2',
  data: {
    message: 'You loaded this page on ' + new Date()
  }
})
</script>
{% endraw %}

Đến đây, chúng ta vừa gặp một thứ rất mới mẻ. Thuộc tính `v-bind` mà bạn đang thấy được gọi là **directive**. Directive (chỉ thị, mình sẽ dùng từ directive vì nó như là một thuật ngữ) có một tiền tố là `v-` để biểu thị rằng đây là một thuộc tính được cung cấp bởi Vue, và như bạn có thể đoán được, các directive này cung cấp reactive behavior cho DOM. Trong ví dụ ở trên, `v-text` giống như khi nói rằng "Hãy luôn giữ cho thuộc tính title của element này luôn được cập nhật với giá trị của `message`"  

Nếu bạn mở console lên một lần nữa và nhập vào `app2.message = 'some new message'`, bạn sẽ thấy một lần nữa rằng thuộc tính title đã được cập nhật.

## Rẽ nhánh và vòng lặp


Rất đơn giản để ẩn/hiện một element:

``` html
<div id="app-3">
  <p v-if="seen">Now you see me</p>
</div>
```

``` js
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
```

{% raw %}
<div id="app-3" class="demo">
  <span v-if="seen">Now you see me</span>
</div>
<script>
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
</script>
{% endraw %}

Mở console lên và nhập `app3.seen = false`, rồi ấy enter. Bạn sẽ thấy rằng dòng thông báo sẽ ẩn đi.

Ví dụ này cho thấy rằng chúng ta không chỉ có thể bind dữ liệu vào text hoặc các attribute, mà còn có thể bind **cấu trúc** của DOM. Hơn thế nữa, Vue còn cung cấp một hệ thống transition mạnh mẽ để có thể áp dụng các [hiệu ứng transition](transitions.html) một cách tự động khi các element được chèn vào, update hoặc bị remove với Vue.

Còn có một số directive khác, mỗi directive có một chức năng riêng biệt. Ví dụ, `v-for` có thể được dùng để hiển thị một danh sách các item bằng cách sử dụng Array:

``` html
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
```
``` js
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'Learn JavaScript' },
      { text: 'Learn Vue' },
      { text: 'Build something awesome' }
    ]
  }
})
```
{% raw %}
<div id="app-4" class="demo">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
<script>
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'Learn JavaScript' },
      { text: 'Learn Vue' },
      { text: 'Build something awesome' }
    ]
  }
})
</script>
{% endraw %}

Mở console lên và nhập `app4.todos.push({ text: 'New item' })`. Bạn sẽ thấy có một item mới được chèn vào list.

## Xử lý input của user

Để user tương tác với app của bạn, chúng ta sử dụng directive `v-on` để đính các event listener vào DOM, các listener này sẽ liên kết với các method trong đối tượng Vue của chúng ta. 

``` html
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div>
```
``` js
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
```
{% raw %}
<div id="app-5" class="demo">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div>
<script>
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
</script>
{% endraw %}

Lưu ý rằng trong method chúng ta chỉ đơn giản là cập nhật state của app chứ không tác động lên DOM - Tất cả các thao tác trực tiếp với DOM được xử lý bởi Vue, và code mà bạn viết ra chỉ tập trung vào logic. 

Vue còn cung cấp directive `v-model`, cho phép tạo ra two-way binding giữa input và state của app một cách dễ dàng: 

``` html
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
```
``` js
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
```
{% raw %}
<div id="app-6" class="demo">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
<script>
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
</script>
{% endraw %}

## Sáng tác với các Component

Hệ thống component là một khái niệm quan trọng trong Vue, bởi vì nó là một phép trừu tượng cho phép chúng ta xây dựng các dứng dụng lớn bằng cách kế hợp các component nhỏ , khép kính và khả năng sử dụng lại cao lại với nhau. Nếu nghĩ theo cách đó, thì hầu hết mọi loại giao diện đều có thể được trường tượng thành một cây của các components: 

![Component Tree](/images/components.png)

Trong Vue, một component cơ cơ bản chính là một instance ủa Vue với các option được định nghĩa sẵn trong nó. Việc đăng ký một component trong Vue rất đơn giản:

``` js
// Define a new component called todo-item
Vue.component('todo-item', {
  template: '<li>This is a todo</li>'
})
```

Bây giờ, bạn có thể sử dụng nó trong template của một component khác:

``` html
<ol>
  <!-- Create an instance of the todo-item component -->
  <todo-item></todo-item>
</ol>
```

Nhưng component này chỉ render một đoạn text giống nhau, điều đó không có gì thú vị cả. Chúng ta cần phải làm sao đó để có thể truyền dữ liệu từ component cha đến các component con. Hãy sửa lại compoent con một chút để nó có thể chấp nhận các [prop](components.html#Props):

``` js
Vue.component('todo-item', {
  // The todo-item component now accepts a
  // "prop", which is like a custom attribute.
  // This prop is called todo.
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
```

Bây giờ, chúng ta có thể truyền nội dung todo vào từng compoent bằng cách sử dụng `v-bind`: 

``` html
<div id="app-7">
  <ol>
    <!-- Now we provide each todo-item with the todo object    -->
    <!-- it's representing, so that its content can be dynamic -->
    <todo-item v-for="item in groceryList" v-bind:todo="item"></todo-item>
  </ol>
</div>
```
``` js
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})

var app7 = new Vue({
  el: '#app-7',
  data: {
    groceryList: [
      { text: 'Vegetables' },
      { text: 'Cheese' },
      { text: 'Whatever else humans are supposed to eat' }
    ]
  }
})
```
{% raw %}
<div id="app-7" class="demo">
  <ol>
    <todo-item v-for="item in groceryList" v-bind:todo="item"></todo-item>
  </ol>
</div>
<script>
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
var app7 = new Vue({
  el: '#app-7',
  data: {
    groceryList: [
      { text: 'Vegetables' },
      { text: 'Cheese' },
      { text: 'Whatever else humans are supposed to eat' }
    ]
  }
})
</script>
{% endraw %}

Đây chỉ là một ví dụ tượng trưng thôi, nhưng chúng ta đã có thể tách app ra thành nhiều đơn vị nhỏ, và các đơn vị nhỏ này có liên kết tới app của chúng thông qua props. Chúng ta có thể cải tiến component `<todo-item>` tốt hơn nữa bằng template và logic phức tạp hơn mà không làm ảnh hưởng tới component app.

Trong một ứng dụng lớn, việc tách app ra thành nhiều component có ý nghĩa quan trọng trong việc phát triển ứng dụng đó. Chúng ta sẽ nói nhiều hơn về các component [trong hướng dẫn sau](components.html), còn dưới đây là một ví dụ về template của app khi mà nó được tách ra thành nhiều component:

``` html
<div id="app">
  <app-nav></app-nav>
  <app-view>
    <app-sidebar></app-sidebar>
    <app-content></app-content>
  </app-view>
</div>
```

### Mối quan hệ với Custom Elements

Bạn có thể đã để ý thấy rằng các component của Vue rất giống với **Custom Elements** - một phần của [Web Components Spec](http://www.w3.org/wiki/WebComponents/). Đó là bởi vì cú pháp của các component trong Vue đã dựa theo [Web Components Spec](http://www.w3.org/wiki/WebComponents/). Ví dụ, Vue component có hiện thựct [Slot API](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md) và `is` là một thuộc tính đặt biệt. Tuy nhiên, có một vài điểm khác biệt mấu chốt:

1. Web Components Spec vẫn đang được phát thảo và vẫn chưa được hiện thực trong tất cả trình duyệt.  Để so sánh, Vue component không đòi hỏi bất cứ [polyfills](https://en.wikipedia.org/wiki/Polyfill) nào, và hoạt động một cách nhất quán trên tất cả các trình duyệt được hỗ trợ ( IE9 trở lên ). Khi cần thiết, các Vue component có thể được bao bởi một custom element

2. Các Vue component cung cấp nhiều tính năng quan trọng mà không có trong các custom element, đáng chú ý nhất là dữ liệu có thể được trao đổi giữa các component với nhau, custom event và các công cụ để build.

## Sẵn sàng cho nhiều thứ thú vị hơn chưa?

Chúng ta chỉ mới làm quen với những tính năng cơ bản nhất của Vue.js - phần còn lại của hướng dẫn này cũng sẽ nói kỹ về nó và những tính năng chuyên sâu khác và chi tiết hơn, vậy hãy chắc rằng bạn sẽ đọc kỹ hết những nội dung ở trên!
