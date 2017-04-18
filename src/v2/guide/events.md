---
title: Xử lý event
type: guide
order: 9
---

## Lắng nghe Events

> Mình sẽ sử dụng từ `event` như là một thuật ngữ, vì bạn sẽ gặp event ở rất nhiều tài liệu khác, và nó cũng thuận tiện cho bạn hơn khi code và khi tìm kiếm tài liệu.

Chúng ta có thể sử dụng directive `v-on` để lắng nghe các event của DOM và chạy một đoạn JavaScript nào đó khi các sự kiện này được trigger.

Ví dụ:

``` html
<div id="example-1">
  <button v-on:click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
```
``` js
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
```

Kết quả:

{% raw %}
<div id="example-1" class="demo">
  <button v-on:click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
<script>
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
</script>
{% endraw %}

## Hàm xử lý event

Logic để xử lý một event có thể sẽ trở nên rất phức tạp, vì vậy đặt một đoạn JavaScript dài trong trong attribute `v-on` là không khả thi. Đó là lý do tại sao bạn có thể truyền tên của một method vào `v-on`.

Ví dụ:

``` html
<div id="example-2">
  <!-- `greet` is the name of a method defined below -->
  <button v-on:click="greet">Greet</button>
</div>
```

``` js
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  // define methods under the `methods` object
  methods: {
    greet: function (event) {
      // `this` inside methods points to the Vue instance
      alert('Hello ' + this.name + '!')
      // `event` is the native DOM event
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})

// you can invoke methods in JavaScript too
example2.greet() // -> 'Hello Vue.js!'
```

Kết quả:

{% raw %}
<div id="example-2" class="demo">
  <button v-on:click="greet">Greet</button>
</div>
<script>
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  methods: {
    greet: function (event) {
      alert('Hello ' + this.name + '!')
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})
</script>
{% endraw %}

## Gọi hàm inline

Thay vì gọi trực tiếp method thông qua tên của nó, bạn cũng có thể gọi nó và truyền tham số vào như cách sử dụng hàm trong JavaScript:

``` html
<div id="example-3">
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>
```
``` js
new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message)
    }
  }
})
```

Kết quả:
{% raw %}
<div id="example-3" class="demo">
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>
<script>
new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message)
    }
  }
})
</script>
{% endraw %}

Đôi khi chúng ta cũng cần truy xuất vào các <span data-tooltip="Native Event, các tham số được HTML truyền vào hàm xử lý event.">tham số mặc định</span> của event. Bạn có thể sử dụng biến `$event` để truyền vào method:

``` html
<button v-on:click="warn('Form cannot be submitted yet.', $event)">Submit</button>
```

``` js
// ...
methods: {
  warn: function (message, event) {
    // now we have access to the native event
    if (event) event.preventDefault()
    alert(message)
  }
}
```

## Các Event Modifier

Có một nhu cầu rất phổ biến là gọi method `event.preventDefault()` hoặc `event.stopPropagation()` trong khi xử lý event. Mặc dù chúng ta có thể thực hiện điều này rất đơn giản trong method, nhưng sẽ tốt hơn nếu như method của chúng ta chỉ chứa các logic để xử lý dữ liệu, thay vì chứa luôn code xử lý event của DOM.

Để xử lý vấn đề này, Vue cung cấp các **event modifier** cho  `v-on`. Hãy nhớ lại rằng các modifier được sử dụng phía sau tên của directive và phân cách bởi dấu chấm.

- `.stop`
- `.prevent`
- `.capture`
- `.self`
- `.once`

``` html
<!-- the click event's propagation will be stopped -->
<a v-on:click.stop="doThis"></a>

<!-- the submit event will no longer reload the page -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- modifiers can be chained -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- just the modifier -->
<form v-on:submit.prevent></form>

<!-- use capture mode when adding the event listener -->
<div v-on:click.capture="doThis">...</div>

<!-- only trigger handler if event.target is the element itself -->
<!-- i.e. not from a child element -->
<div v-on:click.self="doThat">...</div>
```

> Điểm mới trong 2.1.4


``` html
<!-- the click event will be triggered at most once -->
<a v-on:click.once="doThis"></a>
```

Không giống như các modifier khác, chỉ hoạt động với các event mặc định của DOM. Modifier `.once` có thể được sử dụng cho các [component event](components.html#Using-v-on-with-Custom-Events). Nếu như bạn chưa bao giờ đọc về components, đừng lo về nó, bạn sẽ có thể đọc về nó sau.

## Các Modifier Cho Phím

Khi bạn lắng nghe các sự kiện của bàn phím, chúng ta thường phải kiểm tra các mã phím thông dụng. Vue cho phép chúng ta thêm các modifier cho phím vào `v-on` khi lắng nghe các sự kiện về phím:

``` html
<!-- only call vm.submit() when the keyCode is 13 -->
<input v-on:keyup.13="submit">
```

Nhớ tất cả các mã code thì rất đau đầu, vì vậy Vue cung cấp các <span data-tooltip="bí danh">alias</span> cho các phím thông dụng:

``` html
<!-- same as above -->
<input v-on:keyup.enter="submit">

<!-- also works for shorthand -->
<input @keyup.enter="submit">
```

Đây là sanh sách các bí danh thông dụng:

- `.enter`
- `.tab`
- `.delete` (Dùng cho cả phím "Delete" và "Backspace")
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

Bạn cũng có thể [tự định nghĩa các alias](../api/#keyCodes) cho riêng bạn thông qua `config.keyCodes`:

``` js
// enable v-on:keyup.f1
Vue.config.keyCodes.f1 = 112
```

## Modifier Keys

> Điểm mới trong 2.1.0

Bạn có thể sử dụng các modifier dưới đây để giới hạn các các event của chuột hoặc bàn phím, để nó chỉ được trigger khi các phím modifier này được ấn:

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

> Lưu ý: Trên bàn phím của hệ điều hành Macintosh, phím meta là phím command (⌘), còn của Windows nó là phím windows (⊞), của Sun Microsystems thì nó được đánh dấu là một hình thoi đậm màu (◆). Trên các bàn phím nhất định, đặt biệt là bàn phím của máy MIT và Lisp, như bàn phím Knight, space-cadet thì phím meta có ký hiệu là chữ "META". Trên bàn phím Symbolics, phím meta cũng được ký hiệu là "META" hoặc "Meta".

Ví dụ:

```html
<!-- Alt + C -->
<input @keyup.alt.67="clear">

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

## Tại sao phải lắng nghe event trong code HTML?

Bạn có thể sẽ lo lắng rằng cách lắng nghe event trong code html này sẽ vi phạm các quy luật về <span data-tooltip="Tách biệt các mối e ngại">separation of concerns</span>. Hãy yên tâm - vì tất cả các hàm xử lý và biểu thức đều bị ràng buộc chặt chẽ bên trong ViewModel chịu trách nhiệm về view hiện tại, nó sẽ không gây ra bất cứ khó khăn nào cho việc bảo trì. Trong thực tế, có rất nhiều lợi ích khi sử dụng `v-on`: 

1. Dễ dàng hơn để xác định vị trí hiện thực hàm xử lý trong code JS của bạn bằng cách đơn giản là lướt qua HTML template.

2. Vì bạn không cần phải attach các hàm xử lý event trong code JS, ViewMode của bạn có thể có logic trong sáng hơn và không tác động lên DOM. Như vậy việc test sẽ dễ hơn.

3. Khi một ViewModel bị destroy, tất cả hàm xử lý event sẽ tự động bị loại bỏ. Bạn không cần phải lo lắng về việc loại bỏ nó một cách thủ công.
