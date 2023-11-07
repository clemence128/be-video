# be-video

## Overview
Mô phỏng chức năng gọi video giữa khách hàng (customer) và người giao hàng (shipping) thuộc 1 đơn hàng.

## Project configuration
  - Tạo file .env và bổ sung các thông tin sau để kết nối đến Mysql và MongoDB:
```bash
# ------------------- MONGODB CONFIGURATION -------------------
MONGO_HOST=
MONGO_PORT=
MONGO_PASSWORD= #optional
MONGO_USER= #optional
MONGO_DATABASE=
# ------------------- MYSQL CONFIGURATION -------------------
MYSQL_HOST=
MYSQL_PORT=
MYSQL_USER=
MYSQL_PASSWORD=
MYSQL_DATABASE=
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
        synchronize: true,
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
   
        
