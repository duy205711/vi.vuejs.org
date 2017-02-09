---
title: Cú pháp template
type: guide
order: 4
---

Vuejs sử dụng cú pháp template dựa trên HTML vì vậy nó cho phép bạn bind DOM(đã được render) với dữ liệu trong một đối tượng Vue. Tất cả template của Vue.js đều hợp lệ với HTML, nó có thể được phân tích bằng các trình duyệt và các công cụ phân tích cú pháp HTML - với điều kiện nó sử tuân thủ spec của HTML.

<span data-tooltip="Under the hood">Về mặt hiện thực của mình</span>, Vue biên dịch template thành các hàm render của Virtual DOM. Kết hợp với một hệ thống có tính chất reactive (reactivity system), Vue có khả năng tính ra số lượng nhỏ nhất các component cần được render lại và áp dụng một số lượng nhỏ thay đổi rất nhỏ trên DOM khi state của app thay đổi.

Nếu như bạn đã quen thuộc với khái niệm Virtual DOM và thích sức mạnh nguyên thủy của JavaScript(the raw power of JavaScript), thì bạn có thể [viết trực tiếp các hàm render](render-function.html) thay vì dùng template, nó có hỗ trợ cả JSX.

## Interpolations

<p class="tip">Chữ [Interpolations](http://www.dictionary.com/browse/interpolation) có nghĩa gốc là phép nội suy, nhưng chúng tôi cảm thấy khá khó để diễn đạt nó. Nên đã quyết định sử dụng từ tiếng Anh như là một thuật ngữ. Tạm thời các bạn có thể hiểu thuật ngữ này có nghĩa là một quá trình để Vue.js xác định giá trị của biến và đưa vào trong DOM.</p>

### Text

Dạng cơ bản nhất để bind data là text interpolation sử dụng cú pháp "Mustache" (Cặp hai dấu ngặc nhọn).

``` html
<span>Message: {{ msg }}</span>
```

Mustache tag sẽ bị thay thế bằng giá trị của thuộc tính `msg` trong <span  data-tooltip="Đối tượng chứa thuộc tính msg.">đối tượng data</span> tương ứng. Nó cũng sẽ được update mỗi khi giá trị của `msg` thay đổi.

Bạn có thể thực hiện interpolation một lần duy nhất và không update khi data thay đổi bằng cách sử dụng [v-once directive](../api/#v-once), nhưng hãy nhớ rằng nó cũng sẽ tác động tới tất các binding khác trong cùng node đó.

``` html
<span v-once>This will never change: {{ msg }}</span>
```

### Raw HTML

Cú pháp Mustache sẽ thông dịch dữ liệu thành <span  data-tooltip="Tất cả mọi thứ đều hiển thị ra thành text có thể đọc được, kể cả code HTML">plain text</span>, không phải html. Để có thể xuất ra code HTML, bạn cần phải sử dụng directive `v-html`: 

``` html
<div v-html="rawHtml"></div>
```

Những nội dung trong rawHtml sẽ được chèn vào DOM dạng HTML - các data binding sẽ bị bỏ qua. Lưu ý rằng bạn không thể đặt một template khác trong `v-html` nhằm mục đích render một component ra. Bởi vì Vue không phải là một template engine dựa trên string đơn thuần. Thay vào đó, các component được hiểu là một đơn vị cơ bản của UI để sử dụng lại và kết hợp với nhau.

<p class="tip">Lạm dụng việc render HTML (HTML interpolation) trên website của bạn có thể rất nguy hiểm, bởi vì nó rất dễ dẫn tới [các lỗ hổng XSS](https://en.wikipedia.org/wiki/Cross-site_scripting). Chỉ sử dụng HTML interpolation cho các nội dung đáng tin và **không bao giờ** render HTML với các nội dung được cung cấp bởi người dùng.</p>

### Các thuộc tính

Cú pháp mustaches không thể được sử dụng bên trong các thuộc tính HTML, thay vào hãy sử dụng [v-bind directive](../api/#v-bind):

``` html
<div v-bind:id="dynamicId"></div>
```

Nó còn hoạt động với các thuộc tính kiểu boolean - các thuộc tính sẽ bị remove nếu giá trị của nó có thể hiểu là <span  data-tooltip="Các giá trị được quy định là sai, ví dụ như: null, false, 0, ...">false</span>:

``` html
<button v-bind:disabled="someDynamicCondition">Button</button>
```

### Sử dụng các biểu thức Javascript

Cho đến bây giờ, chúng ta chỉ mới bind những thuộc tính đơn giản trong các template của chúng ta. Nhưng thực ra Vue.js hỗ trợ đầy đủ sức mạnh của các cú pháp JavaScript trong tất cả các <span data-tool-tip="Các kiểu binding dữ liệu">data binding</span>.

``` html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

Các biểu thức này sẽ được xử lý như JavaScript trong phạm vi dữ liệu(data scope) của Vue instance mà nó thuộc về. Có một hạn chế là với mỗi binding chỉ có thể chứa **một biểu thức đơn**, Nên ví dụ dưới đây sẽ **KHÔNG** chạy:

``` html
<!-- this is a statement, not an expression: -->
{{ var a = 1 }}

<!-- flow control won't work either, use ternary expressions -->
{{ if (ok) { return message } }}
```

<p class="tip">Các biểu thức bị đặt trong sandbox vàn chỉ có thể truy xuất các biến global trong whitelist như `Math` và `Date`. Bạn không nên cố truy xuất vào các biến global được user định nghĩa(user defined) khi đang ở trong các biểu thứ này.</p>

## Directives

Directive là các thuộc tính đặt biệt với tiền tố `v-`. Giá trị của các thuộc tính directive được kỳ vọng là một **biểu thức JavaScript đơn** ( ngoại trừ `v-for`, chúng ta sẽ nói về nó sau ). Công việc của một directive là áp dụng các <span  data-tooltip="Khi A thay đổi, kéo theo nhiều thứ khác thay đổi theo">side effect</span> từ sự thay đổi giá trị của các biểu thức (giá trị của nó) đến DOM một cách thụ động. Hãy xem lại ví dụ mà chúng ta đã gặp ở phần Giới thiệu. 

``` html
<p v-if="seen">Now you see me</p>
```

Ở dây, directive `v-if` sẽ xóa/chèn element `<p>` dựa trên giá trị đúng/sai của biểu thức `seen`.

### Các tham số

Một số directive có thể chấp nhận một "tham số", ký hiệu bằng dấu hai chấm ngay phía sau tên của nó. Ví dụ, directive `v-bind` được sử dụng để cập nhật một thuộc tính HTML như sau:

``` html
<a v-bind:href="url"></a>
```

Ở đây `href` chính là tham số, nó nói directive `v-bind` hãy bind thuộc tính `href` với giá trị của biểu thức `url`.

Một ví dụ khác là directive `v-on`, directive lắng nghe event của DOM:

``` html
<a v-on:click="doSomething">
```

Ở đây tham số chính là tên của event cần phải lắng nghe. Chúng ta cũng sẽ nói chi tiết về việc xử lý event sau. 

### Modifiers

Modifier là các hậu tố đặt biệt được ký hiệu bằng dấu chấm, nó biểu thị rằng directive nên hoạt động theo một cách đặt biệt nào đó. Ví dụ, modifier `.prevent` chỉ thị cho directive `v-on` gọi `event.preventDefault()` khi event được trigger: 

``` html
<form v-on:submit.prevent="onSubmit"></form>
```

Sau này chúng ta sẽ thấy nhiều ứng dụng của modifier hơn, khi chúng ta nhìn sâu hơn vào `v-on` và `v-model`.

## Filters

Vue.js cho phép bạn định nghĩa các filter thứ có thể được sử dụng để áp dụng định-dạng-text-thông-thường. Các filter có thể được sử dụng ở hai nơi: **mustache interpolation và biểu thức `v-bind`**. Các filter nên được thêm vào phía sau của biểu thức Javascript, ký hiệu bằng biểu tượng "dấu gạch đứng":

``` html
<!-- in mustaches -->
{{ message | capitalize }}

<!-- in v-bind -->
<div v-bind:id="rawId | formatId"></div>
```
<p class="tip">Các filter trong Vue 2.x chỉ có thể được sử dụng bên trong các mustache interpolation và các biểu thức `v-bind` (Được hỗ trợ từ phiên bản 2.1.0 trở về sau), bởi vì các filter chủ yếu được thiết kế cho các mục đích biến đổi text. Vơi các biến đổi dữ liệu phức tạo hơn, bạn nên sử dụng [Các thuộc tính computed](computed.html)</p>

Một hàm filter luôn luôn nhận giá trị của <span  data-tooltip="biểu thứ mà nó filter">biểu thức</span> trong tham số đầu tiên.

``` js
new Vue({
  // ...
  filters: {
    capitalize: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
  }
})
```

Các filter có thể được ghép với nhau thành các mắt xích:

``` html
{{ message | filterA | filterB }}
```

Filter là các hàm JavaScript, do đó chúng có thể nhận các tham số:

``` html
{{ message | filterA('arg1', arg2) }}
```

Ở đây, chuỗi `'arg1'` sẽ được truyền vào cho filter như là tham số thứ hai, và giá trị của biểu thức `arg2` sẽ được xác định và truyền vào như là tham số thứ ba.

## Cú pháp rút gọn

Tiền tố `v-` phục vụ như là một gợi ý trực quan cho việc xác định các thuộc tính nào là riêng biệt của Vue.js trong các template của bạn. Nó hữu ích khi bạn sử dụng Vue.js để áp dụng một <span  data-tooltip="Từ gốc: dynamic behavior. Có thể hiểu là khi bạn muốn dùng Vue.js để đưa một hiệu ứng, hay tạo ra nhiều thay đổi ở DOM vào một đoạn code có sẵn.">hành vi động</span> lên một vài <span  data-tooltip="Có thể trong markup đó có chứa một vài thuộc tính tùy biến khác của thư viện JavaScript khác hoặc là bạn tự code trong JavaScript, khi đó nó có thể làm bạn cảm thấy dễ nhầm lẫn giữa thuộc tính của bạn và thuộc tính của Vue.js, thậm chí là xung đột với nhau.">markup có sẵn</span>, như có thể tạo cảm giác hơi dài dòng đối với một số directive thường dùng. Cùng với đó, sự cần thiết của tiền đó `v-` trở nên kém quan trọng khi bạn xây dựng một [SPA](https://en.wikipedia.org/wiki/Single-page_application) nơi mà Vue.js quản lý tất cả template. Vì thế, Vue.js cung cấp các cách rút gọn đặt biệt cho hai directive thường dùng là `v-bind` và `v-on`:

### Rút gọn của `v-bind`

``` html
<!-- full syntax -->
<a v-bind:href="url"></a>

<!-- shorthand -->
<a :href="url"></a>
```


### Rút gọn của `v-on`

``` html
<!-- full syntax -->
<a v-on:click="doSomething"></a>

<!-- shorthand -->
<a @click="doSomething"></a>
```

Nhìn chúng có thể hơi khác biết một chút với HTML thông thường, nhưng `:` và `@` là các ký tự hợp lệ khi dùng trong tên của thuộc tính và tất cả những trình duyệt mà Vue.js hỗ trợ đều có thể phân tích nó một cách chính xác, chúng không hiển thị ra markup sau khi được render. Cú pháp rút gọn này thì hoàn toàn là tùy chọn, nhưng bạn sẽ đánh giá cao nó khi bạn tìm hiểu thêm về cách sử dụng của nó sau này. 
