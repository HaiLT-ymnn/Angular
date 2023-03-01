# TEMPLATE VARIABLE AND VIEWCHILD/VIEWCHILDREN

Chúng ta sẽ khởi tạo một component mới để minh họa, component có tên là toggle component:

```sh
ng g c toggle
```

Phần code của component sẽ được implement như sau:

**toggle.component.ts**

```ts
import { Component, OnInit, Input, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-toggle',
  templateUrl: './toggle.component.html',
  styleUrls: ['./toggle.component.css'],
})
export class ToggleComponent implements OnInit {
  @Input() checked = false;
  @Output() checkedChange = new EventEmitter<boolean>();

  constructor() {}

  ngOnInit() {}

  toggle() {
    this.checked = !this.checked;
    this.checkedChange.emit(this.checked);
  }
}
```

**toggle.component.html**

```html
<div
  class="toggle-wrapper"
  [class.checked]="checked"
  tabindex="0"
  (click)="toggle()"
>
  <div class="toggle"></div>
</div>
```

**toggle.component.css**

```css
.toggle-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.2s;
  width: 100px;
  height: 100px;
  border-radius: 50%;
  background-color: #fe4551;
  box-shadow: 0 20px 20px 0 rgba(#fe4551, 0.3);
}

.toggle-wrapper:active {
  width: 95px;
  height: 95px;
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



## Parent interacts with child via local varibale

Theo như ví dụ trên, method `toggle()` được gọi khi click thẻ div trong **toggle.component.html**. Nhưng trong thực tế phát triển phần mềm, sẽ có những yêu cầu mà cần đến phải call được method `toggle()` ở parent (AppComponent) như template dưới đây.
```html
<button (click)="doSomething">Toggle</button>

<br />

<app-toggle></app-toggle>
```

- Có thẻ sử dụng đến Template varibale như 1 giải pháp:

```html
<button (click)="toggleApp.toggle()">Toggle</button>

<br />

<app-toggle #toggleApp></app-toggle>
```

Syntax của Tempalte variable chính là sử dung `#varName` và có thể tạo nhiều varibale trong template

## Template varibale sẽ là instance của class nào?

Thông thường, một element trong template của một component sẽ có thể là một HTMLElement, hoặc một Component. Nhưng có nhiều trường hợp sẽ có nhiều Directives cùng được đặt vào một element, vây Template variable sẽ cho một object thuộc kiểu nào?

Mặc định Template varibale mà ko có có expression `#varName` sẽ cố gắng lấy về một object, trong đó đối vs HTMLElement thì object đó chính là HTMLElement, còn với Component thì chính là intance của Component đó

Trong một số trường hợp bạn cần lấy chính xác một instance của một directive/component nào đó thì bạn sẽ cần sử dụng cú pháp #varName="exportAsOfDirectiveOrComponent", ví dụ như khi làm việc với `FormsModule` và `ngModel`, bạn có thể nhìn thấy cú pháp sau:

```html
<form #nameForm="ngForm">
  <input
    type="text"
    class="form-control"
    required
    [(ngModel)]="model.name"
    name="name"
    #name="ngModel"
  />
  <button>Submit</button>
</form>
```
Ở template trên đã tạo 2 Template varibale là:
- nameForm: mong muốn lấy intance của directive có `exportAs` là `ngForm`
- name: mong muốn lấy intance của directive có `exportAs` là `ngModel`

Nếu như chúng ta khong request chính xác type chúng ta mong muốn là gì thì các Template variable trên chỉ lấy về HTMLElement thoong thường

## Parent class with ViewChild

Khi muốn gọi đến Template varibale ở trong class của một component để xử lý logic thì làm thế nào?

**Solution**
Query đến một Template varibale trong Component như sau:

HTML
```html
<button (click)="toggleInside()">Toggle inside class</button>
<br />
<br />

<app-toggle #toggleComp></app-toggle>
```
CLASS
```ts
export class AppComponent{
    @ViewChild('toggleComp') toggleComp : ToggleComponent;
    toggleInside(){
        this.toggleComp.toggle();
    }
}
```
Nếu sử dụng ViewChild cho một HTMLElement thì sẽ nhận được một `ElementRef` thay vì một `HTMLElement` như sử dụng trong template.

```html
<div #container></div>
```

```ts
export class AppComponent{
    @ViewChild('container') container: ElementRef<HTMLDivElement>
}
```


## Parent class with ViewChildren

Khi muốn query một danh sách các element thì có thể dùng `ViewChildren`

`ViewChildren` sẽ trả về một QueryList trước khi `ngAfterViewInit` được chạy. Nó chưa một số property/method để chúng ta có thể listen một số event(Observable).

```html
<app-toggle></app-toggle>
<br />
<app-toggle></app-toggle>
```

```ts
@ViewChildren(ToggleComponent) toggleList: QueryList<ToggleComponent>;


ngAfterViewInit() {
  console.log(this.toggleList);
}
```

## LINK THAM KHẢO
- https://angular.io/api/core/ViewChild
- https://angular.io/api/forms/NgModel
- https://angular.io/api/core/ViewChildren
- https://www.tiepphan.com/thu-nghiem-voi-angular-template-variable-trong-angular/
- https://www.tiepphan.com/angular-trong-5-phut-dynamic-component-rendering/