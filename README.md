# be-video

## Overview
Mô phỏng chức năng gọi video giữa khách hàng (customer) và người giao hàng (shipping) thuộc 1 đơn hàng.

## Project configuration
  - Tạo file ```.env``` và bổ sung các thông tin sau để kết nối đến Mysql và MongoDB:
```bash
# ------------------- MONGODB CONFIGURATION -------------------
MONGO_HOST=localhost
MONGO_PORT=27017
MONGO_PASSWORD= #optional
MONGO_USER= #optional
MONGO_DATABASE=bevideo
# ------------------- MYSQL CONFIGURATION -------------------
MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_USER=root
MYSQL_PASSWORD=sapassword
MYSQL_DATABASE=bevideo
```
  - Vào file ```app.module.ts``` thêm thuộc tính ``` synchronize: true``` ở trong ```TypeOrmModule.forRootAsync```với mục đích tự tạo table trong database (note: Tạo database trước tương ứng với ```MYSQL_DATABASE``` được khai báo trong file .env)
```
  ....
  TypeOrmModule.forRootAsync({
      imports: [ConfigModule],
      inject: [ConfigService],
      useFactory: (configService: ConfigService) => ({
        type: 'mysql',
        host: configService.get('MYSQL_HOST'),
        port: Number(configService.get('MYSQL_PORT')),
        username: configService.get('MYSQL_USER'),
        password: configService.get('MYSQL_PASSWORD'),
        database: configService.get('MYSQL_DATABASE'),
        entities: [
          join(__dirname, 'entities/mysql/', '**', '*.entity{.ts,.js}'),
        ],
        synchronize: true, // dòng này nè...
      }),
    }),
  ....
```
  - Vào file ```user.service.ts``` thực hiện thêm method ```async findById(id: string) : Promise<User | null>```
```
  
@Injectable()
export class UserService {
  constructor(
    private readonly userRepository: UserRepository,
    @InjectRepository(Profile) private profileRepository: Repository<Profile>,
    @InjectRepository(User) private userRepository2: Repository<User> // thêm dòng này
  ) {}

  ...
  async findById(id: string) : Promise<User | null>{
    return await this.userRepository2.findOne(id);
  }
  ...
}
```
  - Mở file ```user.module.ts``` thực hiện import ```TypeOrmModule.forFeature([UserOrm])```
```
@Module({
  imports: [
    MongooseModule.forFeature([{ name: User.name, schema: UserSchema }]),
    TypeOrmModule.forFeature([UserOrm]) // dòng này nè
  ],
  providers: [UserService, UserRepository],
})
export class UserModule {}
```
  - Vào file ```video.gateway.ts``` cập nhật như sau:
```
async handleConnection(socket: Socket) {
    // throw new WsException('Invalid credentials.');
    const token = socket.handshake.headers.authorization;
    // const user = await this.userService.findByToken(token);
    const user = await this.userService.findById(token); // xác thực người dùng thông qua id thay vì token.
    console.log("USER::::", user);
    if (_.isNull(user)) {
      _.remove(VideoGateway.activeSockets, (obj) => {
        return _.get(obj, 'socket_id') == socket.id;
      });
      return;
    }
    // Disable user status
    // const status = 'AC';
    // await this.userService.updateStatuShipper(token, status);

    const existingSocket = _.find(VideoGateway.activeSockets, (obj) => {
      return _.get(obj, 'socket_id') == socket.id;
    });

    if (!existingSocket) {
      VideoGateway.activeSockets.push({
        socket_id: socket.id,
        user_id: user.id,
        user: user,
      });
    }
    console.log('Connect');
    console.log('Active Sockets:');
    console.table(VideoGateway.activeSockets);
  }
```
  - Cập nhật các thông tin liên quan đến đường dẫn file ở phía client trong file ```static/video/index.html```
```
# Cập nhật lại đường dẫn đến file style.css
<link rel="stylesheet" href="./styles.css">

#                    ...

# Cập nhật lại đường dẫn đến file main.js
<script src="./main.js"></script>

```
  - Cập nhật thông tin ở phía client ```static/video/main.js```
```
# Cập nhật đường dẫn socket đến server (connectSocket()):
this.socket = io('http://localhost:3000', {
  extraHeaders: {
  Authorization: this.token,
  }
});
```

```
# Với mục đích thực hiện demo hiệu quả hơn. Thay thế giá trị token1, token2 tương ứng với lại userId
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
- Thực hiện chạy câu lệnh ``` npm run start``` ở trong terminal.
- Sau khi chạy server thành công, vào database mysql thêm các dữ liệu mẫu ở tất cả các bảng để có thể thực hiện demo (Lưu ý: table ```odr_hdr```, table ```users``` có id trùng với data ở bên client.
- Data mẫu:
  + table ```users```:
  
![image](https://github.com/clemence128/be-video/assets/86718789/f8231d4d-6ee5-403c-b44f-e6a0bb7016dd)


  + table ```profiles```:
    
![image](https://github.com/clemence128/be-video/assets/86718789/b4c42949-6eed-489f-b771-ffb3090be4bb)

  + table ```odr_hdr```
    
![image](https://github.com/clemence128/be-video/assets/86718789/526da0b9-f7fb-4ba0-9bbd-8e5354445c5f)

  + table ```customers```
    
![image](https://github.com/clemence128/be-video/assets/86718789/e22c76dd-65ed-4b68-bcc7-a11d2c9c6779)




        
