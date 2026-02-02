# 🔌 Backend API Integration Guide

## 📡 Необходимые API Endpoints

### 1. Аутентификация

#### POST /api/auth/register
Регистрация нового пользователя

**Request:**
```json
{
  "username": "player123",
  "phone": "+77771234567",
  "referralCode": "ABC123" // опционально
}
```

**Response:**
```json
{
  "success": true,
  "userId": "usr_12345",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "referralCode": "DEF456"
}
```

#### POST /api/auth/login
Вход пользователя

**Request:**
```json
{
  "phone": "+77771234567",
  "code": "1234" // SMS код
}
```

**Response:**
```json
{
  "success": true,
  "userId": "usr_12345",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "gameState": { /* сохраненное состояние игры */ }
}
```

### 2. Игровое состояние

#### POST /api/game/save
Сохранение прогресса

**Headers:**
```
Authorization: Bearer {token}
```

**Request:**
```json
{
  "balance": 15000,
  "energy": 850,
  "maxEnergy": 1500,
  "tapValue": 5,
  "totalTaps": 10000,
  "level": 3,
  "boosts": { /* все улучшения */ },
  "achievements": [ /* достижения */ ],
  "timestamp": 1706886000000
}
```

**Response:**
```json
{
  "success": true,
  "savedAt": "2024-02-03T10:30:00Z"
}
```

#### GET /api/game/load
Загрузка прогресса

**Headers:**
```
Authorization: Bearer {token}
```

**Response:**
```json
{
  "success": true,
  "gameState": {
    "balance": 15000,
    "energy": 850,
    /* ... остальные данные */
  },
  "serverTime": 1706886000000
}
```

### 3. Выплаты

#### POST /api/withdrawals/create
Создание заявки на вывод

**Headers:**
```
Authorization: Bearer {token}
```

**Request:**
```json
{
  "amount": 5000,
  "method": "kaspi", // или "phone"
  "details": "+77771234567" // или номер Kaspi
}
```

**Response:**
```json
{
  "success": true,
  "withdrawalId": "wd_12345",
  "status": "pending",
  "amount": 5000,
  "fee": 100,
  "netAmount": 4900,
  "estimatedTime": "24 часа"
}
```

#### GET /api/withdrawals/status/:id
Проверка статуса выплаты

**Headers:**
```
Authorization: Bearer {token}
```

**Response:**
```json
{
  "success": true,
  "withdrawal": {
    "id": "wd_12345",
    "status": "completed", // pending, processing, completed, rejected
    "amount": 5000,
    "method": "kaspi",
    "details": "+77771234567",
    "createdAt": "2024-02-03T10:00:00Z",
    "completedAt": "2024-02-04T11:30:00Z",
    "transactionId": "tx_67890"
  }
}
```

#### GET /api/withdrawals/history
История всех выплат

**Headers:**
```
Authorization: Bearer {token}
```

**Query params:**
```
?page=1&limit=10
```

**Response:**
```json
{
  "success": true,
  "withdrawals": [
    {
      "id": "wd_12345",
      "amount": 5000,
      "status": "completed",
      "createdAt": "2024-02-03T10:00:00Z"
    }
  ],
  "total": 25,
  "page": 1,
  "pages": 3
}
```

### 4. Рефералы

#### GET /api/referrals/stats
Статистика по рефералам

**Headers:**
```
Authorization: Bearer {token}
```

**Response:**
```json
{
  "success": true,
  "totalReferrals": 15,
  "activeReferrals": 8,
  "totalEarnings": 25000,
  "referralCode": "ABC123"
}
```

#### GET /api/referrals/list
Список рефералов

**Headers:**
```
Authorization: Bearer {token}
```

**Response:**
```json
{
  "success": true,
  "referrals": [
    {
      "userId": "usr_67890",
      "username": "player456",
      "joinedAt": "2024-01-15T09:00:00Z",
      "isActive": true,
      "yourEarnings": 1500,
      "theirBalance": 15000
    }
  ]
}
```

### 5. Задания и достижения

#### POST /api/quests/complete
Отметить задание как выполненное

**Headers:**
```
Authorization: Bearer {token}
```

**Request:**
```json
{
  "questId": "tap_1000"
}
```

**Response:**
```json
{
  "success": true,
  "reward": 5000,
  "newBalance": 20000
}
```

#### POST /api/achievements/unlock
Разблокировать достижение

**Headers:**
```
Authorization: Bearer {token}
```

**Request:**
```json
{
  "achievementId": "first_tap"
}
```

**Response:**
```json
{
  "success": true,
  "reward": 500,
  "newBalance": 20500
}
```

### 6. Ежедневные награды

#### POST /api/daily/claim
Забрать ежедневную награду

**Headers:**
```
Authorization: Bearer {token}
```

**Response:**
```json
{
  "success": true,
  "day": 3,
  "reward": 5000,
  "newBalance": 25000,
  "nextReward": 10000,
  "canClaimAt": "2024-02-04T00:00:00Z"
}
```

### 7. Лидерборд

#### GET /api/leaderboard
Топ игроков

**Query params:**
```
?type=balance&limit=100
```

**Response:**
```json
{
  "success": true,
  "leaderboard": [
    {
      "rank": 1,
      "userId": "usr_11111",
      "username": "TopPlayer",
      "balance": 1000000,
      "level": 50
    }
  ],
  "yourRank": 156,
  "yourStats": {
    "balance": 15000,
    "level": 3
  }
}
```

## 🔐 Безопасность

### Защита от читинга

#### POST /api/anticheat/validate
Валидация игровых действий

**Headers:**
```
Authorization: Bearer {token}
```

**Request:**
```json
{
  "action": "tap",
  "count": 100,
  "duration": 10000, // миллисекунды
  "timestamp": 1706886000000,
  "checksum": "a1b2c3d4e5f6..." // хеш данных
}
```

**Response:**
```json
{
  "valid": true,
  "message": "Action validated"
}
```

Или если обнаружен читинг:
```json
{
  "valid": false,
  "message": "Suspicious activity detected",
  "action": "warning", // или "ban"
  "reason": "Too many clicks per second"
}
```

## 📱 Интеграция в Frontend

### Создайте API клиент (api.js)

```javascript
class ApiClient {
    constructor() {
        this.baseURL = 'https://api.neoklin.app';
        this.token = localStorage.getItem('authToken');
    }
    
    async request(endpoint, options = {}) {
        const url = `${this.baseURL}${endpoint}`;
        const headers = {
            'Content-Type': 'application/json',
            ...options.headers
        };
        
        if (this.token) {
            headers['Authorization'] = `Bearer ${this.token}`;
        }
        
        try {
            const response = await fetch(url, {
                ...options,
                headers
            });
            
            if (!response.ok) {
                throw new Error(`HTTP ${response.status}`);
            }
            
            return await response.json();
        } catch (error) {
            console.error('API Error:', error);
            throw error;
        }
    }
    
    // Аутентификация
    async register(username, phone, referralCode) {
        const data = await this.request('/api/auth/register', {
            method: 'POST',
            body: JSON.stringify({ username, phone, referralCode })
        });
        
        if (data.token) {
            this.token = data.token;
            localStorage.setItem('authToken', data.token);
        }
        
        return data;
    }
    
    async login(phone, code) {
        const data = await this.request('/api/auth/login', {
            method: 'POST',
            body: JSON.stringify({ phone, code })
        });
        
        if (data.token) {
            this.token = data.token;
            localStorage.setItem('authToken', data.token);
        }
        
        return data;
    }
    
    // Игровое состояние
    async saveGame(gameState) {
        return await this.request('/api/game/save', {
            method: 'POST',
            body: JSON.stringify(gameState)
        });
    }
    
    async loadGame() {
        return await this.request('/api/game/load');
    }
    
    // Выплаты
    async createWithdrawal(amount, method, details) {
        return await this.request('/api/withdrawals/create', {
            method: 'POST',
            body: JSON.stringify({ amount, method, details })
        });
    }
    
    async getWithdrawalStatus(id) {
        return await this.request(`/api/withdrawals/status/${id}`);
    }
    
    async getWithdrawalHistory(page = 1, limit = 10) {
        return await this.request(`/api/withdrawals/history?page=${page}&limit=${limit}`);
    }
    
    // Рефералы
    async getReferralStats() {
        return await this.request('/api/referrals/stats');
    }
    
    async getReferralList() {
        return await this.request('/api/referrals/list');
    }
    
    // Задания
    async completeQuest(questId) {
        return await this.request('/api/quests/complete', {
            method: 'POST',
            body: JSON.stringify({ questId })
        });
    }
    
    // Достижения
    async unlockAchievement(achievementId) {
        return await this.request('/api/achievements/unlock', {
            method: 'POST',
            body: JSON.stringify({ achievementId })
        });
    }
    
    // Ежедневные награды
    async claimDailyReward() {
        return await this.request('/api/daily/claim', {
            method: 'POST'
        });
    }
    
    // Лидерборд
    async getLeaderboard(type = 'balance', limit = 100) {
        return await this.request(`/api/leaderboard?type=${type}&limit=${limit}`);
    }
    
    // Валидация действий
    async validateAction(action, count, duration) {
        const checksum = this.generateChecksum(action, count, duration);
        
        return await this.request('/api/anticheat/validate', {
            method: 'POST',
            body: JSON.stringify({
                action,
                count,
                duration,
                timestamp: Date.now(),
                checksum
            })
        });
    }
    
    generateChecksum(action, count, duration) {
        // Простой пример. В продакшене используй криптографические хеши
        const data = `${action}-${count}-${duration}-${this.token}`;
        return btoa(data);
    }
}

// Глобальный экземпляр
const api = new ApiClient();
```

### Использование в игре

Обновите `game.js`:

```javascript
// При сохранении игры
async saveGame() {
    try {
        // Локальное сохранение
        localStorage.setItem('neoKlinGameState', JSON.stringify(this.state));
        
        // Сохранение на сервере
        if (api.token) {
            await api.saveGame(this.state);
        }
    } catch (e) {
        console.error('Ошибка сохранения:', e);
    }
}

// При заказе выплаты
async requestWithdrawal(amount, method, details) {
    try {
        const result = await api.createWithdrawal(amount, method, details);
        
        if (result.success) {
            this.state.balance -= amount;
            this.showModal('✅ Заявка создана!', 
                `Выплата будет обработана в течение ${result.estimatedTime}`);
            this.updateUI();
            this.saveGame();
        }
        
        return result.success;
    } catch (error) {
        this.showNotification('❌ Ошибка создания заявки');
        return false;
    }
}
```

## 🔒 Важные моменты безопасности

1. **Никогда не доверяйте клиенту** - всегда валидируйте данные на сервере
2. **Используйте HTTPS** - обязательно для всех запросов
3. **Токены** - короткий срок действия + refresh tokens
4. **Rate limiting** - ограничение запросов (например, 100 req/min)
5. **Логирование** - все действия пользователей для анализа
6. **Хеширование** - для проверки целостности данных
7. **2FA** - для крупных выплат

## 📊 Мониторинг

Рекомендуемые метрики для отслеживания:
- Количество активных пользователей
- Средний lifetime value (LTV)
- Конверсия в платящих пользователей
- Подозрительная активность (читеры)
- Загрузка серверов

---

Удачи с запуском! 🚀