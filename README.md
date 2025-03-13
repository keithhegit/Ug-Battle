# Ug战舰大乱斗 - 技术文档

## 项目概述

这是一个基于 PixiJS 和 Socket.IO 的实时多人在线海战游戏。玩家可以在澳大利亚沿海水域进行战舰对战。

## 系统架构

### 前端架构
- **渲染引擎**: PixiJS
- **网络通信**: Socket.IO Client
- **主要模块**:
  - 游戏初始化模块
  - 玩家控制模块
  - 碰撞检测模块
  - 渲染模块
  - UI界面模块

### 后端架构
- **服务器**: Node.js + Express
- **实时通信**: Socket.IO
- **主要模块**:
  - 玩家管理系统
  - 游戏状态同步
  - 碰撞处理
  - 心跳检测

## 核心游戏参数

### 游戏世界
```javascript
const WORLD_WIDTH = 3000
const WORLD_HEIGHT = 2000
const ISLAND_COUNT = 10
```

### 战舰物理参数
```javascript
const SHIP_ACCELERATION = 0.2
const SHIP_MAX_SPEED = 5
const SHIP_ROTATION_SPEED = 0.05
const SHIP_FRICTION = 0.98
```

### 战斗参数
```javascript
const BULLET_SPEED = 10
const BULLET_DAMAGE = 10
const RECOIL_FORCE = 1
const COLLISION_DAMAGE = 15
```

## 网络通信协议

### 客户端事件
1. **join**: 玩家加入游戏
2. **playerMove**: 玩家移动更新
3. **playerShoot**: 玩家射击
4. **heartbeat**: 心跳包

### 服务器事件
1. **gameState**: 游戏状态同步
2. **playerJoined**: 新玩家加入
3. **playerLeft**: 玩家离开
4. **playerDamaged**: 玩家受伤
5. **collisionOccurred**: 碰撞发生

## 游戏机制

### 玩家系统
- 唯一ID标识
- 生命值系统(100点生命)
- 位置和旋转状态
- 移动速度控制
- 射击冷却时间(1秒)

### 碰撞系统
1. **船只间碰撞**
   - 相互弹开
   - 双方受到伤害
   - 速度衰减

2. **船只与岛屿碰撞**
   - 船只弹开
   - 速度降低
   - 不造成伤害

3. **炮弹碰撞**
   - 造成伤害
   - 产生爆炸效果
   - 击中反馈

### 同步机制
1. **状态同步**
   - 位置同步
   - 旋转同步
   - 生命值同步
   - 射击同步

2. **心跳检测**
   - 间隔: 5000ms
   - 超时: 15000ms
   - 断线重连保护: 10000ms

## 待优化项目

1. **性能优化**
   - [ ] 碰撞检测算法优化
   - [ ] 网络数据包大小优化
   - [ ] 渲染性能优化

2. **游戏性优化**
   - [ ] 添加多种战舰类型
   - [ ] 实现道具系统
   - [ ] 添加团队模式

3. **视觉优化**
   - [ ] 添加战舰素材
   - [ ] 优化爆炸效果
   - [ ] 添加水面效果
   - [ ] 优化UI界面

## 已知问题

1. **战舰渲染**
   - 当前缺少战舰图形资源
   - 需要添加战舰PNG图片素材
   - 需要实现战舰的动画效果

2. **碰撞检测**
   - 当前使用简单的圆形碰撞箱
   - 需要优化为更精确的多边形碰撞

3. **网络同步**
   - 可能存在延迟问题
   - 需要添加预测补偿机制

## 开发指南

### 添加新功能流程
1. 在服务器端添加相关逻辑
2. 实现客户端对应功能
3. 添加网络通信事件
4. 进行测试和优化

### 代码规范
- 使用 ES6+ 语法
- 遵循模块化开发
- 注释关键逻辑
- 错误处理和日志记录

## 部署说明

### 开发环境
```bash
npm install
npm start
```

### 生产环境
```bash
npm install --production
NODE_ENV=production npm start
```

## 维护计划

### 短期计划
1. 完善战舰图形系统
2. 优化碰撞检测
3. 添加基础音效

### 长期计划
1. 实现排行榜系统
2. 添加成就系统
3. 优化游戏平衡性

## 贡献指南

1. Fork 项目
2. 创建功能分支
3. 提交更改
4. 发起 Pull Request

## 版本历史

### v0.1.0 (当前版本)
- 基础游戏系统
- 多人对战功能
- 碰撞检测系统

## 许可证

MIT License 

## 代码结构详解

### 游戏常量
```javascript
// 战舰物理参数
const SHIP_ACCELERATION = 0.2    // 加速度
const SHIP_MAX_SPEED = 5        // 最大速度
const SHIP_ROTATION_SPEED = 0.05 // 旋转速度
const SHIP_FRICTION = 0.98      // 摩擦系数

// 战斗参数
const BULLET_SPEED = 10         // 炮弹速度
const BULLET_DAMAGE = 10        // 炮弹伤害
const RECOIL_FORCE = 1         // 后坐力
const COLLISION_DAMAGE = 15     // 碰撞伤害

// 世界参数
const WORLD_WIDTH = 3000       // 世界宽度
const WORLD_HEIGHT = 2000      // 世界高度
const ISLAND_COUNT = 10        // 岛屿数量

// 网络参数
const PLAYER_TIMEOUT = 10000   // 玩家超时时间
const HEARTBEAT_INTERVAL = 5000 // 心跳间隔
const HEARTBEAT_TIMEOUT = 15000 // 心跳超时
```

### 游戏状态变量
```javascript
// 核心游戏状态
let app                     // PIXI应用实例
let socket                  // Socket.IO连接
let gameStarted = false    // 游戏是否开始
let selfId                 // 当前玩家ID
let selfShip               // 当前玩家战舰

// 游戏对象容器
let ships = {}            // 所有战舰
let bullets = []          // 所有炮弹
let islands = []          // 所有岛屿
let collisionAnimations = [] // 碰撞动画

// 输入状态
let keys = {}             // 按键状态
let isMouseDown = false   // 鼠标按下状态
let mousePosition = { x: 0, y: 0 } // 鼠标位置
let isAimingMode = false  // 瞄准模式

// 游戏计时器
let lastShootTime = 0     // 上次射击时间
let shootCooldown = 2000  // 射击冷却时间
```

### 玩家状态管理
```javascript
// 玩家数据结构
const players = {
    [socketId]: {
        id: playerId,           // 玩家ID
        x: position.x,          // X坐标
        y: position.y,          // Y坐标
        rotation: 0,            // 旋转角度
        speed: 0,              // 当前速度
        health: 100,           // 生命值
        lastShot: 0,           // 上次射击时间
        lastActive: Date.now(), // 最后活动时间
        lastHeartbeat: Date.now() // 最后心跳时间
    }
}

// 断线重连保护
const disconnectedPlayers = {
    [socketId]: {
        player: playerData,
        disconnectTime: timestamp,
        timeoutId: timeoutHandle
    }
}
```

### 岛屿系统
```javascript
// 岛屿生成
function generateIslands() {
    for (let i = 0; i < ISLAND_COUNT; i++) {
        islands.push({
            id: `island-${i}`,
            x: Math.random() * WORLD_WIDTH,
            y: Math.random() * WORLD_HEIGHT,
            radius: 50 + Math.random() * 100,
            type: Math.floor(Math.random() * 3)
        })
    }
}

// 岛屿类型
const ISLAND_TYPES = {
    BEACH: 0,
    FOREST: 1,
    ROCK: 2
}
```

### 碰撞系统
```javascript
// 碰撞检测参数
const EXPLOSION_RADIUS = 20    // 爆炸半径
const EXPLOSION_DURATION = 0.3 // 爆炸持续时间
const BULLET_FLIGHT_TIME = 2   // 炮弹飞行时间

// 碰撞效果
function createWaterExplosion(x, y) {
    // 创建爆炸效果
    const explosion = new PIXI.Container()
    // ... 爆炸动画逻辑
}

// 碰撞处理
function handleCollision(object1, object2) {
    // ... 碰撞响应逻辑
}
```

### 网络通信系统
```javascript
// Socket.IO事件处理
socket.on('gameState', onGameState)
socket.on('playerJoined', onPlayerJoined)
socket.on('playerLeft', onPlayerLeft)
socket.on('playerMoved', onPlayerMoved)
socket.on('playerShot', onPlayerShot)
socket.on('playerDamaged', onPlayerDamaged)
socket.on('playerDied', onPlayerDied)
socket.on('playerRespawned', onPlayerRespawned)
socket.on('collisionOccurred', onCollision)
socket.on('heartbeatRequest', onHeartbeatRequest)
```

### 游戏循环系统
```javascript
// 游戏主循环
function gameLoop(delta) {
    if (!gameStarted) return
    
    // 更新玩家位置
    updatePlayerPosition()
    
    // 更新炮弹位置
    updateBullets()
    
    // 检测碰撞
    checkCollisions()
    
    // 更新UI
    updateGameInfo()
}

// 初始化游戏
function initGame() {
    // 创建PIXI应用
    app = new PIXI.Application({
        width: window.innerWidth,
        height: window.innerHeight,
        backgroundColor: 0x0a64a0,
        resolution: window.devicePixelRatio || 1
    })
    
    // 设置事件监听
    setupEventListeners()
    
    // 开始游戏循环
    app.ticker.add(gameLoop)
}
```

### UI系统
```javascript
// DOM元素
const loginScreen = document.getElementById('loginScreen')
const playerId = document.getElementById('playerId')
const startButton = document.getElementById('startButton')
const gameInfo = document.getElementById('gameInfo')
const healthValue = document.getElementById('healthValue')
const speedValue = document.getElementById('speedValue')
const playerCount = document.getElementById('playerCount')
const miniMap = document.getElementById('miniMap')
<<<<<<< HEAD
``` 
=======
``` 
>>>>>>> c71be78e079185a6e40486ef197ec3e5b28cc0a1
