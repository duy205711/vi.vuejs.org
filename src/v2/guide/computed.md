---
title: Các computed property và Watcher
type: guide
order: 5
---

## Các Computed property

<p class="tip">Mình sẽ dùng **Computed property**. Vì từ này mình không biết dịch ra như thế nào cả. Bạn có thể hiểu rằng, computed property được hiện thực ở hình thức là một hàm hoặc một object chứa hai thuộc tính là hàm set và/hoặc hàmget. Khi bạn định nghĩa một computed property cho vm, thì bạn có thể dùng tên của computed property như một một thuộc tính của vm, không có gì khác biệt. Khi computed property được gọi, các hàm bạn hiện thực cho nó sẽ được gọi lên để xử lý.</p>

Các biểu thức ở trong template rất tiện lợi, nhưng điều đó chỉ đúng khi nó là các phép tính đơn giản. Đưa phép tính và logo phức tạp hơn vào các template của bạn có thể sẽ rất cồng kềnh và khó <span data-tooltip="maintain">bảo trì</span>. Ví dụ:

``` html
<div id="example">
  {{ message.split('').reverse().join('') }}
</div>
```

Tại thời điểm này, template không còn đơn giản và dễ hiểu nữa. Bạn phải nhìn vào nó lần thứ hai trước khi nhận ra rằng nó sẽ hiển thị giá trị `message` theo thứ tự ngược lại. Vấn đề này trở nên tồi tệ hơn khi bạn muốn hiển thị chuỗi đảo ngược này vài lần nữa.

Đó là lý do tại sao bạn nên sử dụng **computed property**.

### Ví dụ cơ bản

``` html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
```

``` js
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // a computed getter
    reversedMessage: function () {
      // `this` points to the vm instance
      return this.message.split('').reverse().join('')
    }
  }
})
```

Kết quả:

{% raw %}
<div id="example" class="demo">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
<script>
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    reversedMessage: function () {
      return this.message.split('').reverse().join('')
    }
  }
})
</script>
{% endraw %}

Như vậy, chúng ta đã khai báo computed property là `reversedMessage`. Hàm mà chúng ta đã cung cấp sẽ được sử dụng như là hàm getter cho thuộc tính `vm.reversedMessage`:

``` js
console.log(vm.reversedMessage) // -> 'olleH'
vm.message = 'Goodbye'
console.log(vm.reversedMessage) // -> 'eybdooG'
```

Bạn có thể mở console lên và vọc với `vm` này. Giá trị của `vm.reversedMessage` luôn luôn phụ thuộc vào giá trị của `vm.message`.

Bạn có thể bind các computed property trong template giống hệt như các thuộc tính bình thường khác. Vue hiểu rằng `vm.reversedMessage` phụ thuộc vào `vm.message`, nên nó sẽ tự động cập nhật giá trị được hiển thị ở những nơi bind `vm.reversedMessage` khi `vm.message` thay đổi. Và phần hay nhất khi chúng ta tạo ra nối quan hệ phụ thuộc này là: Các hàm computed này không tạo ra <span data-tooltip="side effects">các hiệu ứng lề</span>, vì sẽ dễ test hơn. 

### Cache Computed vs Hàm

Có thể bạn đã để ý rằng chúng ta có thể đạt được kết quả tương tự khi sử dụng hàm thay vì computed property:

``` html
<p>Reversed message: "{{ reverseMessage() }}"</p>
```

``` js
// in component
methods: {
  reverseMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```

Thay vì dùng computed property, chúng ta có thể khai báo một hàm tương tự. Kết quả là chúng ta có hai cách tiếp cận cho kết quả giống nhau. Tuy nhiên, sư khác biệt là **Các computed property được cache dựa vào các mối phụ thuộc của nó.** Một computed property sẽ chỉ được tính toán lại khi các giá trị mà nó phụ thuộc vào thay đổi. Có nghĩa là chỉ cần `message` không thay đổi, thì `reversedMessage` sẽ ngay lập tức trả về giá trị được tính toán trước đó mà không cần phải chạy lại hàm để tính toán lại.  

Nó còn có nghĩa là computed property trước đây sẽ không bao giờ được cập nhật, bởi vì `Date.now()` không phải reactive:

``` js
computed: {
  now: function () {
    return Date.now()
  }
}
```

Để so sánh, Một lời gọi hàm sẽ **luôn luôn** chạy mỗi khi cần re-render.

Tại sao chúng ta lại cần cache? Tưởng tượng rằng chúng ta có một computed property phức tạp là **A**, hàm này có một vòng lặp trong một array lớn và trong vòng lặp đó có rất nhiều phép tính. Sau đó chúng ta  có thể có các computed property khác, trả về giá trị của **A**. Không có cache, chúng ta có thể sẽ phải chạy hàm getter **A** nhiều lần hơn mức cần thiết! Trong trường hợp đó nếu không muốn sử dụng cache, thì bạn phải sử dụng method.

### Computed vs Watched Property

Vue cung cấp một cách chung để quan sát và phản ứng lại với sự thay đổi của dữ liệu trong một vm: **watch**. Khi bạn có một số dữ liệu cần được thay đổi dựa trên một dữ liệu khác, nó dễ cám dỗ bạn để sử dụng `watch` - Đặt biệt nếu bạn đã rành AngularJS. Tuy nhiên, thường thì tốt nhất là bạn nên sử dụng computed property thay vì phải sử dụng `watch`. Hãy xem ví dụ này:

``` html
<div id="demo">{{ fullName }}</div>
```

``` js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```

Đoạn code ở trên dở và trùng lặp nhiều. Hãy so sánh nó với computed property:

``` js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

Much better, isn't it?

### Computed Setter

Các Computed property được mặt định là chỉ cần có getter thôi, Nhưng bạn cung cũng có thể hiện thực hàm setter nếu cần:

``` js
// ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

Bây giờ khi bạn chạy `vm.fullName = 'John Doe'`, hàm setter sẽ được gọi và `vm.firstName`, `vm.lastName` sẽ được cập nhật:

## Watchers

Trong khi các computed property thích hợp trong hầu hết các trường hợp, thì watcher vẫn có lúc cần thiết. Đó là lý do tại sao Vue cung cấp một cách chung để phản ứng lại các thay đổi của dữ liệu thông qua `watch`. Nó hữu dụng nhất khi bạn muốn thực hiện một tác vụ có tính không đồng bộ hoặc phức tạp phụ thuộc vào sự thay đổi của dữ liệu.

Ví dụ:

``` html
<div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
```

``` html
<!-- Since there is already a rich ecosystem of ajax libraries    -->
<!-- and collections of general-purpose utility methods, Vue core -->
<!-- is able to remain small by not reinventing them. This also   -->
<!-- gives you the freedom to just use what you're familiar with. -->
<script src="https://unpkg.com/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://unpkg.com/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    // whenever question changes, this function will run
    question: function (newQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.getAnswer()
    }
  },
  methods: {
    // _.debounce is a function provided by lodash to limit how
    // often a particularly expensive operation can be run.
    // In this case, we want to limit how often we access
    // yesno.wtf/api, waiting until the user has completely
    // finished typing before making the ajax request. To learn
    // more about the _.debounce function (and its cousin
    // _.throttle), visit: https://lodash.com/docs#debounce
    getAnswer: _.debounce(
      function () {
        if (this.question.indexOf('?') === -1) {
          this.answer = 'Questions usually contain a question mark. ;-)'
          return
        }
        this.answer = 'Thinking...'
        var vm = this
        axios.get('https://yesno.wtf/api')
          .then(function (response) {
            vm.answer = _.capitalize(response.data.answer)
          })
          .catch(function (error) {
            vm.answer = 'Error! Could not reach the API. ' + error
          })
      },
      // This is the number of milliseconds we wait for the
      // user to stop typing.
      500
    )
  }
})
</script>
```

Kết quả:

{% raw %}
<div id="watch-example" class="demo">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
<script src="https://unpkg.com/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://unpkg.com/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    question: function (newQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.getAnswer()
    }
  },
  methods: {
    getAnswer: _.debounce(
      function () {
        var vm = this
        if (this.question.indexOf('?') === -1) {
          vm.answer = 'Questions usually contain a question mark. ;-)'
          return
        }
        vm.answer = 'Thinking...'
        axios.get('https://yesno.wtf/api')
          .then(function (response) {
            vm.answer = _.capitalize(response.data.answer)
          })
          .catch(function (error) {
            vm.answer = 'Error! Could not reach the API. ' + error
          })
      },
      500
    )
  }
})
</script>
{% endraw %}

Trong trường hợp này, Việc sử dung sử dụng `watch` cho phép bạn thực hiện một tác vụ có tính không đồng bộ(truy suất API), giới hạn mật độ thực hiện tác vụ này, và đặt các trạng thái trung gian cho đến khi chúng ta lấy được câu trả lời cuối cùng. Không có cách nào để thực hiện việc này với một computed property. 

Một điều nữa về `watch`, bạn có thể sử dụng API trung gian [vm.$watch API](../api/#vm-watch).
