# be-video

## Project configuration

  - Tạo file .env và bổ sung các thông tin sau để kết nối đến Mysql và MongoDB:
      + MONGO_HOST
      + MONGO_PORT
      + MONGO_PASSWORD (optional)
      + MONGO_USER (optional)
      + MONGO_DATABASE
      + MYSQL_HOST
      + MYSQL_PORT
      + MYSQL_USER
      + MYSQL_PASSWORD
      + MYSQL_DATABASE
      
  - Cập nhật các thông tin liên quan đến đường dẫn file ở phía client (trong file static/video/index.html)
      + Cập nhật lại đường dẫn đến file main.js
      + Cập nhật lại đường dẫn đến file style.css

  - Cập nhật thông tin ở phía client (static/video/main.js)
      + Cập nhật đường dẫn socket đến server:
        <code>
            this.socket = io('http://localhost:3000', {
              extraHeaders: {
                Authorization: this.token,
              }
            });
        </code>
   
        
