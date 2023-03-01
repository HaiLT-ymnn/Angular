# CONTENT PROJECTION IN ANGULAR

Trong quá trình phát triển ứng dụng với Angular, có thể sẽ gặp lại một số dạng component giống nhau tron về phần layout, điêm khác biệt chỉ là một số label, content bên trong (Danh sách Product, danh sách câu hỏi khảo sát, danh sách hình ảnh,...). 

Có một vài phương án cho trường hợp này :
    - Sử dụng `@Input` và render dựa vào các Input đó.
    - Sử dụng `ng-content`

## NG-CONTENT

### Cách sử dụng

Giả sử ứng dụng cần làm khảo sát khách hàng. Với một loạt các câu hỏi Yes/No và nội dung question khác nhau. Phương pháp xử lý:

Tạo component Question
```bash
ng g c question --skip-tests
```

question.component.html
```html
<div class="question">
  <ng-content></ng-content>
</div>
<div class="toggle-wrapper" [class.checked]="checked" tabindex="0">
  <div class="toggle"></div>
</div>
```
question.component.ts

```typescript
import { Component, EventEmitter, Input, OnInit, Output } from '@angular/core';

@Component({
  selector: 'app-question',
  templateUrl: './question.component.html',
  styleUrls: ['./question.component.scss']
})
export class QuestionComponent implements OnInit {
  @Input() checked = true;
  @Output() checkedChange = new EventEmitter<boolean>();

  constructor() { }

  ngOnInit(): void {
  }

  toggle() {
    this.checked = !this.checked;
    this.checkedChange.emit(this.checked);
  }

}
```

question.component.scss
```css
.toggle-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.2s;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background-color: #fe4551;
  margin: 15px 0;
}

.toggle-wrapper:active {
  width: 55px;
  height: 55px;
  box-shadow: 0 15px 15px 0 rgba(#fe4551, 0.5);
}

.toggle-wrapper .toggle {
  height: 17px;
  width: 17px;
}

.toggle {
  transition: all 0.2s ease-in-out;
  height: 20px;
  width: 20px;
  background-color: transparent;
  border: 10px solid #fff;
  border-radius: 50%;
  cursor: pointer;
  animation: red 0.7s linear forwards;
}

.toggle-wrapper.checked {
  background-color: #48e98a;
  box-shadow: 0 20px 20px 0 rgba(#48e98a, 0.3);
}

.toggle-wrapper.checked:active {
  box-shadow: 0 15px 15px 0 rgba(#48e98a, 0.5);
}

.toggle-wrapper.checked .toggle {
  width: 0;
  background-color: #fff;
  border-color: transparent;
  border-radius: 30px;
  animation: green 0.7s linear forwards !important;
}
```
app.component.ts
```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  question: any = {
    question1: true,
    question2: false
  }
}
```

app.component.html
```html
<app-question [(checked)]="questions.question1">
  <span>Question 1</span>
  <ng-content></ng-content>
</app-question>

<app-question [(checked)]="questions.question2">
  <span>Question 2</span>
  <ng-content></ng-content>
</app-question>
```

Chỉ cần đặt `ng-content` vào bất kỳ đâu trong template của component. Angular sẽ tự động truyền content ở giữa cặp thẻ mở/động vào component.


### Sử dụng multiple ng-content ?

```html
<div class="question">
  <div>Content 1</div>
  <ng-content></ng-content>
</div>
<div class="question">
  <div>Content 2</div>
  <ng-content></ng-content>
</div>
```
Khi nhìn vào kết quả render, sẽ thấy content 2 được hiển thị với label truyền vào. Như vậy sử dụng `ng-content` sẽ dẫn đến kết quả không như mong muốn (gấp đôi số phần tử). Điều này hoàn toàn bình thường, giống như bao thẻ html khác như `header`, chúng ta chỉ có duy nhất 1 slot hiển thị. Vậy nên đối với `ng-content` ở dạng trên, chỉ nên có 1 tag duy nhất.


### ng-content và selector
Khi làm việc thẻ `table` của html, `thead`, `tbody`, `tfoot` dù có đặt ở bất kỳ thứ tự nào trong thẻ `table`, nó đều được đưa về đúng thứ tự là `thead` rồi đến `tbody`, sau cùng là  `tfoot`. Tương tự với `ng-content` kết hợp **selector**. Ngoài ra, chúng ta còn có thể sử dụng được nhiều `ng-content` (not  _select all_)

Các dạng của selector có thể bao gồm:
- Tag selector: `<ng-content select="some-component-selector-or-html-tag"></ng-content>`
- CSS class selector: `<ng-content select=".some-class"></ng-content>`
- Attribute selector: `<ng-content select="[some-attr]"></ng-content>`
- Combine nhiều selector: `<ng-content select="some-component-selector-or-html-tag[some-attr]"></ng-content>`

question.component.html
```html
<div>
  <ng-content select=".header"></ng-content>
</div>

<div class="question">
  <ng-content select="label"></ng-content>
</div>
<div class="toggle-wrapper" [class.checked]="checked" tabindex="0" (click)="toggle()">
  <div class="toggle"></div>
</div>
```

app.component.html
```html
<app-question [(checked)]="question.question1">
  <p class="header">Header 1</p>
  <span>Question 1</span>
  <label>Question 1'</label>
</app-question>
<app-question [(checked)]="question.question2">
  <label>Question 2'</label>
  <span>Question 2</span>
  <p class="header">Header 2</p>
</app-question>
```

### ng-content và ngProjectAs

Đang update