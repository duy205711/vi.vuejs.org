---
title: Cài đặt
type: guide
order: 1
vue_version: 2.1.10
dev_size: "218.92"
min_size: "70.99"
gz_size: "25.86"
ro_gz_size: "18.01"
---

### Tương thích trình duyệt

Vue **không** hỗ trợ trình duyệt IE từ phiên bản 8 trở xuống, vì nó sử dụng những tính năng của ECMAScript5 mà trong IE8 không có. Tuy nhiên Vue sẽ hỗ trợ tất cả các [trình duyệt tương thích với ECMAScript 5](http://caniuse.com/#feat=es5).

### Những ghi chú về việc phát hành

Những thông tin chi tiết về việc phát hành cho từng phiên bản được ghi chú đầy đủ trên  [GitHub](https://github.com/vuejs/vue/releases).

## Standalone

Chỉ cần tải về và chèn vào trong thẻ script. `Vue` sẽ được đăng kí như một biến global.

<p class="tip">Không sử dụng phiên bản minified trong quá trình phát triển. Vì bạn sẽ bỏ lỡ tất cả cảnh báo của Vue khi xảy ra những lỗi thường gặp.</p>

<div id="downloads">
<a class="button" href="/js/vue.js" download>Phiên bản Development</a><span class="light info">Bao gồm đầy đủ cảnh báo và debug mode</span>

<a class="button" href="/js/vue.min.js" download>Phiên bản Production</a><span class="light info">Cảnh báo đã được loại bỏ, {{gz_size}}kb min+gzip</span>
</div>

### CDN

Khuyến khích cài đặt theo cách này: [unpkg](https://unpkg.com/vue/dist/vue.js) (chỉ cần copy đường dẫn), đây sẽ luôn là phiên bản mới nhất trùng khớp với phiên bản được phát hành trên npm. Bạn cũng có thể duyệt xem package của Vue trên npm tại [unpkg.com/vue/](https://unpkg.com/vue/).

Ngoài ra bạn cũng có thể sử dụng [jsDelivr](//cdn.jsdelivr.net/vue/{{vue_version}}/vue.js) hoặc [cdnjs](//cdnjs.cloudflare.com/ajax/libs/vue/{{vue_version}}/vue.js), nhưng hai dịch vụ này phải mất một ít thời gian để đồng bộ vì vậy phiên bản mới nhất có thể sẽ chưa được cập nhật kịp thời.

## NPM

NPM là một phương pháp cài đặt được khuyến khích sử dụng khi xây dựng những ứng dụng lớn với Vue. Nó kết hợp một cách tuyệt vời với các module bundlers như [Webpack](https://webpack.js.org/) hoặc [Browserify](http://browserify.org/). Vue cũng cung cấp các công cụ kèm theo để tạo ra [Single File Components](single-file-components.html).

``` bash
# latest stable
$ npm install vue
```

### Standalone Build và Runtime-only Build

Hiện tại có hai loại build là standalone build và runtime-only build. Sự khác biệt giữa hai loại nằm ở việc standalone build thì bao gồm **trình biên dịch template** còn runtime-only build thì không.

Trình biên dịch templates có trách nhiệm biên dịch Vue template strings thành các hàm render thuần Javascript. Nếu bạn muốn sử dụng `template` option, thì bạn cần có trình biên dịch.

- Standalone build bao gồm trình biên dịch và hỗ trợ `template` option. **Nó cũng phụ thuộc vào các API của trình duyệt, vì vậy bạn không thể sử dụng standalone build cho server-side rendering.**

- Runtime-only build không bao gồm trình biên dịch và không hỗ trợ `template` option. Bạn chỉ có thể sử dụng `render` option khi sử dụng runtime-only build, tuy nhiên nó có thể hoạt động vói các single-file component, nguyên nhân là do các template của single-file component được biên dịch trước (pre-compiled) vào trong các hàm `render` trong quá trình build, dung lượng chỉ có {{ro_gz_size}}kb min+gzip.

Mặc định, NPM package sẽ xuất ra dưới dạng **runtime-only** build. Để sử dụng standalone build, bạn cần thêm alias dưới đây vào trong phần cấu hình của Webpack:

``` js
resolve: {
  alias: {
    'vue$': 'vue/dist/vue.common.js'
  }
}
```

Đối với Browserify, bạn cần thêm một alias vào trong package.json:

``` js
"browser": {
  "vue": "vue/dist/vue.common"
},
```

<p class="tip">KHÔNG được `import Vue from 'vue/dist/vue.js'` - bởi vì một vài thư viện bên thứ 3 có thể đã import Vue rồi, nếu bạn import như vậy có thể khiến cho app phải tải cả hai dạng là runtime và standalone build cùng lúc và điều này sẽ dấn đến lỗi.</p>

### Môi trường CSP

Trong một vài môi trường như Google Chrome Apps, Content Security Policy (CSP) là những môi trường nghiêm cấm việc sử dụng `new Function()` để <span data-tooltip="evaluating expressions">đánh giá các biểu thức</span>  . Standalone build phụ thuộc vào tính năng này để biên dịch các template, vì vậy nó không thể sử dụng được trong môi trường CSP.

Mặt khác, runtime-only build thì hoàn toàn phù hợp cho môi trường CSP. Khi sử dụng runtime-only build với [Webpack + vue-loader](https://github.com/vuejs-templates/webpack-simple) hoặc [Browserify + vueify](https://github.com/vuejs-templates/browserify-simple), những template của bạn sẽ được biên dịch vào trong các hàm `render`, những hàm đó hoạt động hoàn hảo trong các môi trường CSP.

## CLI

Vue.js cung cấp một [CLI - bản chính thức](https://github.com/vuejs/vue-cli) giúp cho việc khởi tạo những Single Page Applications được nhanh chóng hơn. It provides batteries-included build setups for a modern frontend workflow. Chỉ tốn một vài phút để để khởi tạo và chạy với hot-reload, lint-on-save, and production-ready builds:

``` bash
# install vue-cli
$ npm install --global vue-cli
# create a new project using the "webpack" template
$ vue init webpack my-project
# install dependencies and go!
$ cd my-project
$ npm install
$ npm run dev
```

<p class="tip">Việc sử dụng CLI đòi hỏi bạn cần có kiến thức về Node.js và những công cụ build liên quan. Nếu bạn mới làm quen với Vue hoặc những công cụ build dành cho front-end thì chúng tôi đề nghị bạn xem qua <a href="./">hướng dẫn</a> không sử dụng bất kì công cụ build nào trước khi sử dụng CLI.</p>

## Dev Build

**Quan trọng**: những files được build ra trong thư mục `/dist` trên Github chỉ được cập nhật mỗi lần phát hành phiên bản mới. Vì vậy nếu muốn sử dụng Vue từ những files đó bạn cần phải tự build!

``` bash
git clone https://github.com/vuejs/vue.git node_modules/vue
cd node_modules/vue
npm install
npm run build
```

## Bower

``` bash
# latest stable
$ bower install vue
```

## AMD Module Loaders

Những bản tải về standalone hoặc được cài đặt từ Bower thì đã được bao bọc với <span data-tooltip="Universal Module Definition">UMD</span> vì vậy chúng có thể được sử dụng trược tiếp như một <span data-tooltip="Asynchronous Module Definition">AMD</span> module.
