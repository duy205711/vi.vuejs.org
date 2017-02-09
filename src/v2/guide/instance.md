---
title: Vue Instance
type: guide
order: 3
---

## Constructor

Mỗi một Vue <span data-tooltip="Viết tắt của ViewModel">vm</span> được khởi tạo (bootstrapped) bằng cách tạo một **root Vue instance** với hàm khởi tạo `Vue`:

``` js
var vm = new Vue({
  // options
})
```

Mặc dù không có mối liên hệ mật thiết với [MVVM](https://en.wikipedia.org/wiki/Model_View_ViewModel), nhưng thiết kế của Vue một phần được lấy cảm hứng từ nó. Để cho tiện, chúng thường sử dụng biến `vm` (viết tắc của ViewModel) để ám chỉ cho các Vue instance.

Khi khởi tạo một Vue instance, bạn cần phải truyền vào một **options object** có chứa các thuộc tính như data, template, element để <span data-tooltip="Element này giúp Vue instance xác định đúng DOM mà nó sẽ được gắn vào">gắn vào</span> (mount on), phương thức, các hàm callback của lifecyle và nhiều thứ nữa. Bạn có thể xem danh sách các option đầy đủ tại [API reference](../api).

`Vue` constructor có thể được kế thừa để tạo ra các **component constructor** có khả năng tái sử dụng với các option được định nghĩa trước:

``` js
var MyComponent = Vue.extend({
  // extension options
})

// all instances of `MyComponent` are created with
// the pre-defined extension options
var myComponentInstance = new MyComponent()
```

Mặc dù việc kế thừa như trên là hoàn toàn khả thi, tuy nhiên thông thường đều khuyến thích việc đó nên được khai báo rõ ràng trong các template như là những element tùy chỉnh (custom elements). Chúng ta sẽ nói chi tiết về [hệ thống component](components.html) trong phần sau. Bây giờ, bạn chỉ cần biết rằng tất cả component trong Vue thì bản chất của nó là đều được kế thừa từ Vue instance.

## Property và Method

<p class="tip">Hai từ Property và Method có thể hiểu trong ngữ cảnh của một Vue instance là các thành phần, phương thức của instance đó. Tuy nhiên chúng tôi quyết định sử dụng tiếng Anh như là một thuật ngữ để bạn đọc có thể dễ hiểu và quen thuộc hơn.</p>

Mỗi một Vue instance sẽ **đại diện (proxies)** cho tất cả các property có trong `data` của instance đó.

``` js
var data = { a: 1 }
var vm = new Vue({
  data: data
})

vm.a === data.a // -> true

// setting the property also affects original data
vm.a = 2
data.a // -> 2

// ... and vice-versa
data.a = 3
vm.a // -> 3
```

Nên nhớ rằng chỉ có những property nào được đại diện thì mới **reactive**. Nếu bạn thêm một property mới vào instance sau khi nó được khởi tạo, thì property đó sẽ không bắt được bất kì sự thay đổi nào của view. Chúng ta sẽ thảo luận chi tiết về hệ thống reactive trong phần sau.

Ngoài các property của `data` ra, thì Vue instance cũng cung cấp một số <span data-tooltip="Những property và method này không nằm trong data">property và method</span> hữu ích khác, chúng được dánh dấu bằng tiền tố là `$` để phân biệt với các property của `data`. Ví dụ như:

``` js
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // -> true
vm.$el === document.getElementById('example') // -> true

// $watch is an instance method
vm.$watch('a', function (newVal, oldVal) {
  // this callback will be called when `vm.a` changes
})
```

<p class="tip">Không được sử dụng [các hàm mũi tên (arrow functions)](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions) trong các property hoặc hàm callback của instance (ví dụ `vm.$watch('a', newVal => this.myMethod())`). Những hàm mũi tên này bị bind vào trong <span data-tooltip="Chỗ này có thể hiểu là this sẽ không phải là vm mà sẽ là this của đoạn code cha bao ngoài hàm mũi tên">parent context</span>, `this` sẽ không phải là một Vue instance như bạn mong dợi và `this.myMethod` sẽ không thể xác định được (undefined).</p>

Xem thêm [API reference](../api) để nắm hết danh sách các thành phần và phương thức của một instance.

## Các Hook của một Instance Lifecycle

Mỗi một Vue instance sẽ phải trải qua nhiều bước trước khi nó được tạo - lấy ví dụ, nó cần phải trải qua các bước như set up dữ liệu, biên dịch template, gắn instance vào DOM, cập nhật lại DOM khi dữ liệu thay đổi. Trong suốt quá trình đó, Vue instance cũng sẽ gọi ra một số **lifecycle hooks**, những hook này sẽ giúp chúng ta có thể thực thi những hành động logic. Ví dụ như hook `created` được gọi sau khi tạo instance:

``` js
var vm = new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` points to the vm instance
    console.log('a is: ' + this.a)
  }
})
// -> "a is: 1"
```

Cũng sẽ có thêm những hook khác được gọi ở những phiên khác nhau trong vòng đời của một instance (instance's lifecycle), ví dụ như [`mounted`](../api/#mounted), [`updated`](../api/#updated), và [`destroyed`](../api/#destroyed). Tất cả các lifecycle hook được gọi ra với ngữ cảnh `this` đang trỏ tới Vue instance đang gọi chúng. Có thể bạn sẽ thắc mắc rằng khái niệm "controllers" sẽ tồn tại ở đâu trong thế giới của Vue và câu trả lời là: chẳng có controllers nào cả. Những hành động logic của bạn cho một component sẽ được chia ra trong các lifecycle hook này.

## Biểu đồ về vòng đời

Bên dưới đây là một biểu đồ cho vòng đời của một instance. Bạn không cần phải hiểu hoàn toàn mọi thứ trong biểu đồ ngay bây giờ, tuy nhiên bạn cứ xem qua vì nó sẽ giúp ích rất nhiều trong tương lai.

![Lifecycle](/images/lifecycle.png)
