## 项目介绍与IM系统需求分析及拆分
### 基本需求
- 发送/接受
- 实现群聊
- 高并发=单机最好+分布式+弹性扩容

### 实现基本的功能界面

### 发送需求拆分、实现资源标准化编码

### 接受消息并解析显示
- 接受到消息体(JSON)并进行解析
- 区分不同显示形式(图片/文字/语音)
- 界面显示自己发的和别人发的

### 群聊需求拆分
群聊的特殊需求
- 基础功能上无区别
- 一条消息多个参与群聊的终端及时接收到
- 服务器流量计算

服务器负载分析举例说明，假设在A在群聊里发送了一张图片512k，那么100个在线群人员同时接收到512kb * 100=1024kb*50=50M.1024个群50M * 1024 = 50G
压力巨大，一台服务器承受不住。提出几个解决办法：（1）使用缩略图(51.2k)提高单图下载和渲染速度。（2）提高资源服务并法能力使用云服务(qos/alioss),100ms以内。（3）压缩消息体，发送文件路径而不是整个文件。

### 高并发需求拆分
高并发
- 单机并发性能最优
- 海量用户分布式部署
- 应对突发事件弹性扩容

## 建表 sql
```sql
create table users (
  id         serial primary key,
  uuid       varchar(64) not null unique,
  name       varchar(255),
  email      varchar(255) not null unique,
  password   varchar(255) not null,
  created_at timestamp not null
);
    
create table sessions (
  id         serial primary key,
  uuid       varchar(64) not null unique,
  email      varchar(255),
  user_id    integer references users(id),
  created_at timestamp not null
);
    
create table threads (
  id         serial primary key,
  uuid       varchar(64) not null unique,
  topic      text,
  user_id    integer references users(id),
  created_at timestamp not null
);
    
create table posts (
  id         serial primary key,
  uuid       varchar(64) not null unique,
  body       text,
  user_id    integer references users(id),
  thread_id  integer references threads(id),
  created_at timestamp not null
);
```