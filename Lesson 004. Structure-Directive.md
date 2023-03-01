# ANGULAR STRUCTURAL DIRECTIVE

## CẤU TRÚC IF-ELSE

Để hiển thị một phần view (template) theo một điều kiện, chúng ta gắn thêm một property đặc biệt vào một tag, với cú pháp chứa dấu `* (asterisk)` như sau `*ngIf=expression`

```typescript
@Component({
  selector: 'app-demo',
  template: `
    <h3>Your name: {{ user.name }}</h3>
    <p>Your name: {{ user.age }}</p>
    <div *ngIf="user.age >= 18">Bạn có thể được cấp bằng xe máy</div>
  `,
})
export class HelloComponent {
  user = {
    name: 'Tran Van A',
    age: 30,
  };
}
```

- Có thể hiển thị view tùy thuộc theo dữ liệu mà expresstion trả về. Trusthy thì hiển thị, Fasly thì không hiển thị. Với những directive được cung cấp sẵn bơi Angular, HTML template của component sẽ rất linh hoạt. 

- Mệnh đề `IF ELSE`:

```html
<div *ngIf="user.age >= 18">Bạn có thể được cấp bằng xe máy</div>
<div *ngIf="user.age < 18">Bạn không thể được cấp bằng xe máy</div>
```

- Mệnh đề `IF ELSE` với `ng-template`
    > Tag ng-template là một tag được cung cấp bới Angular, nó sẽ lưu một Template được định nghĩa bên trong cặp thẻ/đóng của nó. 
    > Những gì được định nghĩa bên trong sẽ không đc hiển thị ra view, nhưng chúng ta có thể sử dụng template đó để render bằng code. 

```html
<div *ngIf="user.age >= 18; else noPG13">Bạn có thể được cấp bằng xe máy</div>
<ng-template #noPG13>
  <div>Bạn không thể được cấp bằng xe máy</div>
</ng-template>
```
- Có thể chuyển đổi code sang dạng Property Binding

```html
<ng-template [ngIf]="user.age >= 13" [ngIfElse]="noPG13">
  <div>Bạn có thể xem nội dung PG-13</div>
</ng-template>
```

## NG-FOR

**Data**
```typescript
interface Author {
    id: number;
    firstName: string;
    lastName: string;
    email: string;
    gender: string;
    ipAddress: string;
}

export class DemoComponent{
  authors : Array<Author> = [
    {
      id: 1,
      firstName: 'Flora',
      lastName: 'Twell',
      email: 'ftwell0@phoca.cz',
      gender: 'Female',
      ipAddress: '99.180.237.33',
    },
    {
      id: 2,
      firstName: 'Priscella',
      lastName: 'Signe',
      email: 'psigne1@berkeley.edu',
      gender: 'Female',
      ipAddress: '183.243.228.65',
    },
    // more data
  ];  
}
```
### NgForOf:

```html
<div *ngFor="let author of authors">
  {{author.id}} - {{author.firstName}} {{author.lastName}}
</div>
```

### MỘT SỐ LOCAL VARIABLE TRONG MỘT NGFOROF TEMPLATE
Khi sử dụng NgforOf, ở mỗi vong lặp có thể truy xuất đến một số local variable:

| Term           | Description                                            |
| -------------- | ------------------------------------------------------ |
| \$implicit: T  | Giá trị của phần tử trong danh sách ở lần lặp hiện tại |
| index: number  | index của lần lặp hiện tại                             |
| count: number  | số lượng phần tử trong danh sách                       |
| first: boolean | True nếu đây là phần tử đầu tiên trong danh sách       |
| last: boolean  | True nếu đây là phần tử cuối cùng trong danh sách      |
| even: boolean  | True nếu đây là phần tử ở index chẵn                   |
| odd: boolean   | True nếu đây là phần tử ở index lẻ                     |

- Cách truy xuất : 
    + Đối với $implicit sẽ gắn chính biến khi khai báo: `let something of [Array]` : something = `\$implicit`
    + Các biến khác, dùng theo cú pháp:

```html
<div *ngFor="let author of authors; index as idx; count as total">
  ({{idx}})/({{total}}): {{author.id}} - {{author.firstName}}
  {{author.lastName}}
</div>
```
Lúc này biến `idx = index` và `total = count`.Tương tự đối với các biến còn lại

###  ngForTrackBy

Khi add/delete 1 `author` đối với authors. Html Template sẽ render lại toàn bộ Html template component đối với cả những object cũ đã được render và object mới chưa được render. Điều này gây ra lãng phí tài nguyên và performance. Cách xử lý sẽ dùng ngForTrackBy

- TrackByFunction():
    + Tùy chỉnh để tính toán danh tính của item
    + Nếu không được cung cấp, sẽ sử dụng item's identity(thường sẽ là id) để là key


**HTML**
```html
<div *ngFor="let author of authors; trackBy: trackByFn">
  {{author.id}} - {{author.firstName}} {{author.lastName}}
</div>
```
**TS**
```typescript
 trackByFn(id, item) {
    return item.id;
  }
```

=> Theo ví dụ trên, hệ thống sẽ kiểm tra xem thẻ `<div>` với id đã tồn tại trên DOM hay chưa, nếu đã tồn tại thì bỏ qua và chỉ render những item có id chưa tồn tại.

### CẤU TRÚC NGFOROF VÀ NG-TEMPLATE

Với cấu trúc dùng dấu (*) như trên chúng ta có thể chuyển đổi tương ứng về dạng ng-template và property binding như sau:

```html
<ng-template
  ngFor
  [ngForOf]="authors"
  let-author
  let-idx="index"
  let-total="count"
>
  <div>
    ({{idx}})/({{total}}): {{author.id}} - {{author.firstName}}
    {{author.lastName}}
  </div>
</ng-template>
```

### SỬ DỤNG NHIỀU STRUCTURE DIRECTIVE TRÊN CÙNG MỘT PHẦN TỬ
Khi đưa ra solution cho các problem sẽ thường gặp các vấn đề cần kiểm tra dữ liệu trong vòng lặp. Trong Angular, nếu đặt `NgIf` và `NgFor` trên cùng phần tử thì nó sẽ không work vì đơn giản Angular không cho phép được đặt 2 structure diretive trên cùng 1 phần tử (element).

Ví dụ : 

```js
for (let item of list) {
  if (something) {
    // more code
  }
}
```
Rõ ràng là không thể đặt for và if trong cùng một hàng. Vậy nên giải pháp cần tách ra và sử dụng thêm 1 tầng wrapper nữa

```html
<div *ngFor="let item of list">
  <div *ngIf="something">More code</div>
</div>
```
Nhưng như bên trên lại sinh ra 1 thẻ `<div>` thừa. Giải pháp là sẽ dùng ng-tempate hoặc dùng ng-container:

```html
<div *ngFor="let" item of list”>
  <ng-container *ngIf="something"> More code </ng-container>
</div>
<div *ngFor="let item of list">
  <ng-template [ngIf]="something"> More code </ng-template>
</div>
```

## LINK THAM KHẢO
- https://angular.io/guide/structural-directives
- https://angular.io/api/common/NgIf
- https://angular.io/api/common/NgForOf