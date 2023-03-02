# Dependency Injection trong Angular

Hiện nay có rất nhiều Frontend Framework có support Dependency Injection(DI), Angular sử dụng rất nhiều và nó có riêng một DI framework được thiết kế để đáp ứng được yêu cầu về mặt kiến trúc hệ thống của Angular.

## Dependency Injection(DI) là gì?

Denpendency Injection một kiểu thiết kế phần mềm, trong có các đổi tượng được cung cấp cho một đối tượng khác (gọi là client) thông qua việc `inject`(tiêm) chúng vào. Trong Angular DI là một tính năng quan trọng giúp tăng tính linh hoạt và bảo trì cho ứng dụng.

Okie, xong khái niệm. Hơi kiểu hàm lâm và chung chung nhỉ? Cùng làm rõ vẫn đề nào.
- Câu hỏi 1: DI có quan trọng không?
    + Rất quan trọng.
- Câu hỏi 2: Không có DI hệ thống có dừng không?
    + Không, vẫn chạy ổn.

=> Vậy thì làm sao lại cần DI. Đơn giản thôi so sánh khi không sử dụng và sử dụng DI để làm rõ ưu điểm và nhược điểm 2 phương pháp

### Không sử dụng DI

Giả sử chúng ta có y/c về đăng ký User.

- **Khởi tạo UserService**
```bash
ng g s user --skip-tests
```
```ts
// UserService class
export class UserService {
  private users: string[] = [];

  public registerUser(username: string): boolean {
    if (this.users.includes(username)) {
      return "exist";
    } else {
      this.users.push(username);
      return "notexist";
    }
  }
}

```
- **Khởi tạo ComponentA**
UserService sẽ được khởi tạo một instance bằng từ khóa `new`. Và có một method là `register()` gọi đến method `registerUser()` của UserService.

```ts
// Component A
export class ComponentA {
  
  private userService: UserService = new UserService();

  public register(username: string): void {
    const result = this.userService.registerUser(username);
    console.log(`Component A register result: ${result}`);
  }
}
```

- **Khởi tạo ComponentB**
UserService sẽ được khởi tạo một instance bằng từ khóa `new`. Và có một method là `register()` gọi đến method `registerUser()` của UserService.

```ts
// Component B
export class ComponentB {
  private userService: UserService = new UserService();

  public register(username: string): void {
    const result = this.userService.registerUser(username);
    console.log(`Component B register result: ${result}`);
  }
}
```

- **app.component.ts**
```ts
const componentA = new ComponentA();
componentA.register('user');
// Output: Component A register result: notexist 

const componentB = new ComponentB();
componentB.register('user');
// Output: Component B register result: notexist ? Why?

componentA.register('user');
// Output: Component A register result: exist

componentB.register('user');
// Output: Component B register result: exist

```
Như trong trường hợp trên, rõ ràng là ComponentA và ComponentB bị phụ thuộc vào(depend on) class UserService. Các class bị ràng buộc chặt chẽ với nhau(Tight Coupling).
Tight Coupling là một khái niệm sử dụng để chỉ sự phụ thuộc chặt chẽ giữa các thành phần trong một hệ thống phần mềm. Khi hai hoặc nhiều thành phần phụ thuộc chặt chẽ vào nhau, điều này có thể dẫn đến các vấn đề như khó khắn trong việc kiểm thử, bảo trì và mở rộng hệ thống.

Tight Coupling giữa server và component có thể dẫn đến các vấn đề như sau :

- Khó khăn trong việc tái sử dụng: Khi cùng một service được sử dụng bởi nhiều component, bạn sẽ cần khởi tạo service trong mỗi component đó. Điều này sẽ làm cho việc tái sử dụng service trở nên khó khăn và làm giảm tính tái sử dụng của ứng dụng của bạn.

- Khó khăn trong việc kiểm thử: Khi các thành phần của ứng dụng của bạn phụ thuộc chặt chẽ vào nhau, việc kiểm thử các thành phần đó trở nên khó khăn hơn. Khi kiểm thử một component, bạn sẽ cần kiểm thử service được sử dụng bởi component đó. Nếu service đó không được inject bằng cách sử dụng Dependency Injection, bạn sẽ phải khởi tạo service một cách rời rạc trong kiểm thử, dẫn đến việc kiểm thử trở nên khó khăn hơn.

Ngoài Tight Coupling thì khai báo bằng từ khóa `new` sẽ dẫn đến mỗi instance của `UserService` trong mỗi component là hoàn toàn độc lập và không chia sẻ thông tin với(Nguyên nhân gây ra kết quả sai lệch ở ví dụ trên). Điều này có nghĩa chúng ta sẽ phải quản lý có các instance của service 1 cách thủ công. Đây chắc chắn là một vấn đề lớn liên quan đến việc bảo trì và mở rộng ứng dụng.


### Sử dụng DI
Trong Angular, DI bao gồm 3 thành phần sau đây:
- Injector: là một object có chứa các API để chúng ta có thể lấy về các instances đã tạo hoặc tạo các instances của các phụ thuộc.
- Provider: giống như một công thư để Injector có thể biết làm thế nào để một instance của một phụ thuộc
- Depencency: là một object (có thể là function, một value thông thường) của một kiểu dữ liệu cần phải khởi tạo.

Bạn có thể cung cấp `injections` với `provider` ở nhiều level khác nhau trong app, bằng một trong ba cách sau:
- Trong `@Injectable()` decorator cho service đó.
- Trong `@NgModule()` decorator (providers array) đối vs NgModule.
- Trong `@Component()` decorator (providers array) đối với component hoặc directive.

Ví dụ về cách Provider `UserService`:

```ts
@Injectable({
  providedIn: 'root',
})
export class UserService {
  // properties and methods
}
```
**ComponentA**
```ts
export class ComponentA implements OnInit {
  constructor(private userService: UserService) {}

  ngOnInit() {
    console.log(this.userService.register());
  }
}
```
**ComponentB**
```ts
export class ComponentB implements OnInit {
  constructor(private userService: UserService) {}

  ngOnInit() {
    console.log(this.userService.register());
  }
}
```
