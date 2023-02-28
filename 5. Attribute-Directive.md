# ATTRIBUTE DIRECTIVE

Attibute directive là gì? Đối với Structure directive, nó có thể thêm, sửa, xóa, thay đổi cấu trúc DOM. Còn Attribute directive sẽ thay đổi cách hiện thị(style) hoặc hành vi của một DOM element/Component/Directive khác. Đây là sự khác biệt của chúng.

## CLASS BINDING

Trong quá trình coding, DOM sẽ cần thay đổi (thêm, xóa) môt số class tùy thuộc và điều kiên nào đó.

### CSS
```css
.layout {
    height: 400px;
    width: 400px;
    background-color: #000;
}

.layout .layout-white{
    background-color: #fff;
}
```

### HTML
```html
<div class="layout" [class.layout-white]="isWhite">some content</div>
```

### TS
```typescript
export class DemoComponent{
    isWhite: boolean = true;
}
```

Nhìn qua thì chỉ là property binding, với giá trị isWhite là true là classList của div đó sẽ tồn tại class `layout-white`, nếu trả về false thì sẽ không tồn tại.

- Ngoài ra chúng ta còn có thể sử dụng cứ pháp :

```
[class]="classExpr"
```
Với classExpr có thể là string, array tring hoặc object - nếu key nào object là truthy thì sẽ thêm vào, nếu falsy thì sẽ xóa đi.


| Type         | Value                                |
| ------------ | ------------------------------------ |
| String       | `"class-1 class-2 class-3"`          |
| Array String | `['class-1', 'class-2']`             |
| Object       | `{'class-1': true, 'class-2': false}`|

Tương tự với classBinding thì có thể sử dụng như ngClass nhưng từ Angular 9+ thì đã không có sự khác biệt. Và cách sử dụng Class Binding vẫn được khuyến cáo sử dụng hơn ngClass

## STYLE BINDING

Khi cần thiết, có thể sẽ cần binding cho style property (inline style), lúc này có thể sử dụng Style binding.

- Cấu trúc style binding như sau : `[style.property]="expresstion"`. Với expresstion sẽ tính toán về các giá trị kiểu `string | undefined | null`.

```html
<div [style.width]="someValue"></div>
```

- Tiếp theo là cứ pháp kèm theo unit: `[style.property.unit]="expression"`
    + Với expresstion sẽ tính toán về các giá trị kiểu `number | undefined | null`. Ví dụ : `[style.height.%]="containerHeight"`

- Cuối cùng là cú pháp dạng : `[style]="styleExpr"`

    + Với styleExpr sẽ là các dạng :

| Type         | Value                                                                                              |
| ------------ | ---------------------------------------------------------------------------------------------------|
| String       | `"width: 100%; height: 100%"`                                                                      |
| Array String | `['width', '100%']`                                                                                |
| Object       | `{[key: string]: string | undefined | null} như {width: '100px', height: '100px'}`                 |


`ngStyle` với cách dùng gần giống thế, nhưng vẫn được khuyến cáo là sử style binding thay thế.

**Lưu ý :**
Một số style property có thể dùng cả kiểu dash-key hoặc camelCase, ví dự : font-size hoặc fontSize đều được

## LINH THAM KHẢO

- https://angular.io/guide/template-syntax#attribute-class-and-style-bindings
- https://angular.io/api/common/NgClass
- https://angular.io/api/common/NgStyle