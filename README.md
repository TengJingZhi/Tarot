# 塔罗牌战斗卡牌游戏

一款基于塔罗牌（Tarot）题材的回合制双人对战卡牌游戏，玩法借鉴「三国杀」的出牌 / 响应 / 交锋机制。玩家使用 56 张小阿卡那牌对战，并通过 22 张大阿卡那（人物牌）获得独特的角色技能。

- **后端**：Python 3.12 + Flask
- **前端**：原生 HTML / CSS / JavaScript（无需构建）
- **对战**：玩家一（人类） vs 人机（AI）

---

## 目录结构

```
塔罗牌/
├── config/                  # 牌库配置（JSON）
│   ├── major_arcana.json    # 22 张大阿卡那（人物牌）
│   └── minor_arcana.json    # 56 张小阿卡那
├── src/
│   ├── models/              # 数据模型
│   │   ├── card.py          # 花色 / 点数 / 属性 / 卡牌
│   │   ├── character.py     # 人物牌
│   │   └── deck.py          # 牌堆
│   ├── entities/
│   │   └── player.py        # 玩家与技能状态
│   ├── engine/              # 游戏引擎
│   │   ├── skill.py         # 技能注册表与效果实现
│   │   ├── stage.py         # 回合阶段 / 事件栈 / 交锋结算
│   │   ├── gameengine.py    # 交互 API（出牌 / 响应 / 技能…）
│   │   └── result.py        # ActionResult
│   ├── web/                 # Flask Web 界面
│   │   ├── server.py        # 服务端 + REST API
│   │   └── static/          # 前端页面
│   ├── ui/console.py        # 控制台（无界面）入口
│   └── main.py
├── tests/
└── docs/
    └── 游戏规则.md          # 完整游戏规则
```

---

## 运行方式

### 环境要求

- Python 3.12（项目自带 `.venv` 虚拟环境）

### 1. 启动 Web 服务（推荐）

在项目根目录 `c:\Users\39392\Desktop\塔罗牌` 下执行：

```powershell
.venv\Scripts\python.exe -m src.web.server
```

或先激活虚拟环境：

```powershell
.venv\Scripts\Activate.ps1
python -m src.web.server
```

> 若 PowerShell 提示“禁止运行脚本”，先执行：
> `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`

启动成功后，浏览器打开：

```
http://127.0.0.1:8000
```

- 端口为 **8000**（5000 已被占用）。
- 玩家 1 是人类（你），玩家 2 是人机。
- 页面顶部有「重新开始」按钮，点击可重置对局。

### 2. 控制台游玩（无界面）

```powershell
.venv\Scripts\python.exe -m src.main
```

---

## 玩法速览

1. 每位玩家初始 **4 点生命**、**4 张手牌**，每回合 **3 点行动点**。
2. 出牌阶段打出手牌（可被对方用同花色牌响应交锋），用宝剑攻击、圣杯回复、星币购买人物、权杖增伤或翻面。
3. 用星币购买「人物牌」，在交换阶段装备后获得角色技能。
4. 回合结束需弃牌至：**明牌数 ≤ 生命值**、**暗牌数 ≤ 明牌数**。
5. 击败对手（生命归零）或获得「世界」人物牌即可获胜。

完整规则见 [docs/游戏规则.md](docs/游戏规则.md)。

---

## 技术要点

- **技能注册表**：`src/engine/skill.py` 中 `Skill` / `CardSkill` 用装饰器注册全部大阿卡那技能与小阿卡那花色效果。
- **回合驱动**：`src/engine/stage.py` 的 `Turnmanager` 管理阶段机（`TurnPhase` 枚举）与出牌事件栈、响应队列。
- **交互 API**：`src/engine/gameengine.py` 提供 `play_card` / `respond` / `use_skill` / `resolve_choice` 等命令，由 Flask 路由转发。
- **数据配置**：牌与人物全部由 `config/` 下的 JSON 驱动，修改描述 / 数值无需改代码。
