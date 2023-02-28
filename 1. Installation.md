# PREPARE THE WORKING ENVIRONMENT

## TIẾN HÀNH

### NODEJS

Đầu tiên các bạn vào trang https://nodejs.org/en/download/ để tải về Nodejs và cài đặt vào máy. Các bạn có thể tải về bản Long Term Support (LTS) hoặc Current đều được. Hiện tại bản LTS mới nhất là version 12, với Angular version 9 thì đã hoàn toàn phù hợp rồi.
Đối với các bạn nào quen thuộc với terminal thì mình khuyến cáo sử dụng NVM để cài đặt và quản lý nhiều phiên bản Nodejs trên cùng 1 máy. Như thế các bạn sẽ linh động khi làm việc với nhiều dự án khác nhau có đòi hỏi khác nhau về version của Nodejs.
Sau khi cài đặt thành công các bạn có thể verify lại bằng cách mở Terminal/PowerShell/CMD (mình sẽ gọi chung là Terminal) và gõ các lệnh sau, nếu nó hiển thị ra được version thì đã cài đặt thành công.

```bash
node -v
npm -v
```

### ANGULAR CLI

Để phát triển dự án Angular, chúng ta sẽ sử dụng công cụ chính thức từ team Angular là Angular CLI (một tool chạy từ các lệnh Terminal), và chúng ta sẽ cài đặt thông qua lệnh npm bằng câu lệnh sau:

`npm install -g @angular/cli@lastest`

Sau khi cài đặt thành công, có thể kiểm tra bằng lệnh:
`ng version`

Hiện tại version mới nhất của Angular là version 15

Lưu ý :
- Không thể chạy `ng version` vì nó báo `'ng' is not recognized as an internal or external command.` thì cần phải thêm npm global vào PATH.
- Sử dụng PowerShell có thể bị báo lỗi

`File C:\Users\< username >\AppData\Roaming\npm\ng.ps1 cannot be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at https:/go.microsoft.com/fwlink/?LinkID=135170.`

Cần phải `enable policy` để chạy được command. Để enable mở `Powershell as Administrator` và chạy lệnh này `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope LocalMachine` hoặc mở link ở thông báo lỗi để tìm hiểu thêm.

## KHỞI TẠO DỰ ÁN

```
ng new <tên-project>
```

Ví dụ: `ng new demo-app`

- `ng serve`   
    Mặc định sẽ chạy ở port 4200, nếu muốn đổi port thì thêm tham số

- `ng serve --port=other-port`
Ví dụ : `ng serve --port=5002`

## LINK THAM KHẢO
- https://angular.io/guide/setup-local
- https://angular.io/tutorial/toh-pt0


