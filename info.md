# be-video

## Project configuration
  - Tạo file .env và bổ sung các thông tin sau để kết nối đến Mysql và MongoDB:
```bash
# ------------------- MONGODB CONFIGURATION -------------------
MONGO_HOST
MONGO_PORT
MONGO_PASSWORD (optional)
MONGO_USER (optional)
MONGO_DATABASE
# ------------------- MYSQL CONFIGURATION -------------------
MYSQL_HOST
MYSQL_PORT
MYSQL_USER
MYSQL_PASSWORD
MYSQL_DATABASE
```
  - Cập nhật các thông tin liên quan đến đường dẫn file ở phía client (trong file static/video/index.html)
```html
# Cập nhật lại đường dẫn đến file style.css
<link rel="stylesheet" href="./styles.css">

#                    ...

# Cập nhật lại đường dẫn đến file main.js
<script src="./main.js"></script>

```
      + Cập nhật lại đường dẫn đến file main.js
      + Cập nhật lại đường dẫn đến file style.css

  - Cập nhật thông tin ở phía client (static/video/main.js)
```bash
# Cập nhật đường dẫn socket đến server (connectSocket()):
this.socket = io('http://localhost:3000', {
  extraHeaders: {
  Authorization: this.token,
  }
});
```

```bash
# Với mục đích dễ dàng thực hiện demo. Thay thế giá trị token1, token2 tương ứng với lại userId
data: {
    token: null,
    token1: '1',
    token2: '2',
    order_id: 3,
    socket: null,
    socketOptions: null,
    localStream: null,
    remoteStream: null,
    isRoomCreator: false,
    rtcPeerConnection: null,
    logMessages: [],
    callerName: null,
    isCalling: false,
    currentCall: null,
    isShowCancel: false,
  },
```
   
        
