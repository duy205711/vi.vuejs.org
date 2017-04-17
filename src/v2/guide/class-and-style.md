---
title: Class và Style Bindings
type: guide
order: 6
---

<p class="tip">Mình sử dụng **bind** như một thuật ngữ. Bạn có thể hiểu đây là ràng buộc dữ liệu, Giả sử ta có dữ liệu A, ta muốn ràng buộc nó với một DOM nào đó, sự thay đổi của hai đối tượng này bị ràng buộc vào nhau.</p>

Một nhu cầu rất thông dụng về data binding là điều khiển class và inline style của element. Nhờ các element hỗ trợ cả hai thuộc tính này, chúng ta có thể sử dụng `v-bind`để xử lý: Chúng ta chỉ cần tính toán các style, class thành một chuỗi là được. Tuy nhiên, sử dụng chỗi trong trường hợp này lại rất phiền phức và dễ gặp lỗi. Vì lý do đó, Vue cung cấp các cải tiến đặt biệt khi sử dụng `v-bind` để bind `class` và `style`. Ngoài chuỗi, các biểu thức có thể trả về các object hoặc array.

## Binding HTML Classes

### Cú pháp kiểu Object

Chúng ta có thể truyền một object vào `v-bind:class` để bật, tắt các class một cách tự động:

``` html
<div v-bind:class="{ active: isActive }"></div>
```

Cú pháp ở trên có nghĩa là sự xuất hiện của class `active` sẽ được quyết định bởi giá trị của [đúng/sai](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) của thuộc tính `isActive`.

Bạn có thể tắt/mở nhiều class bằng cách truyền vào object có nhiều <span data-tooltip="field">trường</span>. Hơn nữa, directive `v-bind:class` còn có thể cùng xuất hiện với thuộc tính `class`. Template dưới đây là một ví dụ:

``` html
<div class="static"
     v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>
```

Và thuộc tính data như sau:

``` js
data: {
  isActive: true,
  hasError: false
}
```

Kết quả:

``` html
<div class="static active"></div>
```

Khi `isActive` hoặc `hasError` thay đổi, Danh sách class sẽ cập nhật theo tương ứng. Ví dụ, Nếu `hasError` thay đổi thành `true`, danh sách class sẽ đổi thành `"static active text-danger"`.

Object này không nhất thiết phải <span data-tooltip="Kiểu khai báo như ví dụ nảy giờ">inline</span>:

``` html
<div v-bind:class="classObject"></div>
```
``` js
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

Nó sẽ render kết quả tương tự như ví dụ trước. Chúng ta cũng có thể bind với một [computed property](computed.html) nếu computed property này trả về một object. Đây là một <span data-tooltip="pattern">mẫu</span> rất hữu dụng: 

``` html
<div v-bind:class="classObject"></div>
```
``` js
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal',
    }
  }
}
```

### Cú pháp kiểu array

Chúng ta có thể truyền một array vào `v-bind:class`:

``` html
<div v-bind:class="[activeClass, errorClass]">
```
``` js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

Nó sẽ render thành:

``` html
<div class="active text-danger"></div>
```

Nếu bạn luốn bật/tắt một class theo điều kiện, bạn có thể sử dụng cú pháp điều kiện rút gọn:

``` html
<div v-bind:class="[isActive ? activeClass : '', errorClass]">
```

Class `errorClass` sẽ luôn xuất hiện, nhưng `activeClass` chỉ xuất hiện khi `isActive` là `true`

Tuy nhiên, nó có thể rườm rà nếu bạn muốn áp dụng điều kiện cho nhiều class. Đó là lý do tại sao cú pháp kiểu object được hỗ trợ bên trong cú pháp array:

``` html
<div v-bind:class="[{ active: isActive }, errorClass]">
```

### Dùng với Component

> Phần này tóm gọn kiến thức của [Vue Components](components.html). Cứ thoải mái bỏ qua nó và đọc lại sau.

Khi bạn sử dụng attribute `class` trong một component, các class này sẽ được thêm vào trong element gốc của component. Các class đang tồn tại trong element đó sẽ không bị ghi đè.

Ví dụ, nếu bạn khai báo một component như sau:

``` js
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
```

Sau đó thêm một số class khi sử dụng nó:

``` html
<my-component class="baz boo"></my-component>
```

Kết quả sẽ như sau:

``` html
<p class="foo bar baz boo">Hi</p>
```

Bind class cũng tương tự như vậy:

``` html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

Khi `isActive` có giá trị `đúng`, Code HTMl sẽ được render ra như sau:

``` html
<p class="foo bar active">Hi</p>
```

## Binding Inline Styles

### Cú pháp kiểu object

Cú pháp kiểu object cho `v-bind:style` rất đơn giản, rõ ràng - nhìn nó gần giống như CSS, ngoại trừ nó là một object trong javascript. Bạn có thể sửa dụng camelCase hoăc kebab-case (Đối với kebab-case bạn phải dùng kèm dấu nháy) để đặt tên cho các thuộc tính CSS:

``` html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
``` js
data: {
  activeColor: 'red',
  fontSize: 30
}
```

Để template được rõ ràng, bạn nên bind kiểu object:

``` html
<div v-bind:style="styleObject"></div>
```
``` js
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

Một lần nữa, cú pháp object thường được sử dụng với các computed property trả về object:

### Cú pháp kiểu array

Cú pháp kiểu array của `v-bind:style` cho phép bạn sử dụng nhiều object trong nó:

``` html
<div v-bind:style="[baseStyles, overridingStyles]">
```

### Auto-prefixing

Khi bạn sử dụng các thuộc tính đòi hỏi phải thên prefix trong `v-bind:style`, ví dụ `transform`, Vue sẽ tự động phát hiện và thêm các prefix.
