# 🎨 Инструкция по добавлению ресурсов

## 📁 Структура папок для ресурсов

### /img/ - Изображения
Рекомендуемые файлы для добавления:

```
img/
├── logo.png          (512x512px) - Логотип игры
├── icon-192.png      (192x192px) - Иконка для PWA
├── icon-512.png      (512x512px) - Большая иконка
├── badge.png         (96x96px)   - Значок для уведомлений
├── coin.png          (256x256px) - Изображение монеты
├── bg-pattern.png    - Фоновый паттерн (опционально)
└── achievements/     - Иконки достижений
    ├── first-tap.png
    ├── tap-master.png
    └── millionaire.png
```

### /sounds/ - Звуки
Рекомендуемые звуковые эффекты (формат .mp3 или .ogg):

```
sounds/
├── tap.mp3           - Звук клика по монете
├── coin.mp3          - Звук получения монет
├── level-up.mp3      - Звук повышения уровня
├── purchase.mp3      - Звук покупки улучшения
├── achievement.mp3   - Звук получения достижения
├── notification.mp3  - Звук уведомления
└── reward.mp3        - Звук получения награды
```

## 🔊 Добавление звуков в игру

### 1. Создайте менеджер звуков

Добавьте в начало файла `js/game.js`:

```javascript
class SoundManager {
    constructor() {
        this.sounds = {
            tap: new Audio('sounds/tap.mp3'),
            coin: new Audio('sounds/coin.mp3'),
            levelUp: new Audio('sounds/level-up.mp3'),
            purchase: new Audio('sounds/purchase.mp3'),
            achievement: new Audio('sounds/achievement.mp3'),
            notification: new Audio('sounds/notification.mp3'),
            reward: new Audio('sounds/reward.mp3')
        };
        
        this.enabled = true;
        this.volume = 0.5;
        
        // Установка громкости
        Object.values(this.sounds).forEach(sound => {
            sound.volume = this.volume;
        });
    }
    
    play(soundName) {
        if (!this.enabled || !this.sounds[soundName]) return;
        
        const sound = this.sounds[soundName].cloneNode();
        sound.volume = this.volume;
        sound.play().catch(e => console.log('Ошибка воспроизведения:', e));
    }
    
    setVolume(volume) {
        this.volume = Math.max(0, Math.min(1, volume));
        Object.values(this.sounds).forEach(sound => {
            sound.volume = this.volume;
        });
    }
    
    toggle() {
        this.enabled = !this.enabled;
        return this.enabled;
    }
}

// Инициализация в конструкторе игры
const soundManager = new SoundManager();
```

### 2. Добавьте звуки к действиям

В методе `tap()`:
```javascript
tap(clientX, clientY) {
    // ... существующий код ...
    soundManager.play('tap');
    soundManager.play('coin');
}
```

В методе `buyBoost()`:
```javascript
buyBoost(boostType) {
    // ... после успешной покупки ...
    soundManager.play('purchase');
}
```

В методе `checkProgress()`:
```javascript
if (achievement.progress >= achievement.target) {
    // ...
    soundManager.play('achievement');
}
```

## 🖼️ Где скачать бесплатные ресурсы

### Изображения:
1. **Иконки** - [Flaticon](https://www.flaticon.com/)
2. **Текстуры** - [Freepik](https://www.freepik.com/)
3. **Паттерны** - [Subtle Patterns](https://www.toptal.com/designers/subtlepatterns/)
4. **Монеты** - [Game-Icons.net](https://game-icons.net/)

### Звуки:
1. **Freesound.org** - огромная база звуков
2. **Zapsplat.com** - бесплатные звуковые эффекты
3. **Mixkit.co** - звуки для игр
4. **Soundbible.com** - звуки по категориям

### Генерация звуков:
- **Bfxr** (bfxr.net) - генератор игровых звуков
- **ChipTone** (sfbgames.itch.io/chiptone) - 8-bit звуки

## 📐 Требования к изображениям

### Логотип (logo.png)
- Размер: 512x512px
- Формат: PNG с прозрачностью
- Фон: прозрачный
- Стиль: минималистичный, читаемый

### Иконки для PWA
```javascript
// В manifest.json укажи:
"icons": [
  {
    "src": "img/icon-192.png",
    "sizes": "192x192",
    "type": "image/png",
    "purpose": "any maskable"
  },
  {
    "src": "img/icon-512.png",
    "sizes": "512x512",
    "type": "image/png",
    "purpose": "any maskable"
  }
]
```

### Монета (coin.png)
- Размер: 256x256px или 512x512px
- Формат: PNG с прозрачностью
- Стиль: объемная, с бликами и тенями
- Центрирована по холсту

## 🎨 Оптимизация изображений

Перед добавлением в проект:

1. **TinyPNG** (tinypng.com) - сжатие PNG без потери качества
2. **Squoosh** (squoosh.app) - онлайн оптимизатор
3. **ImageOptim** (imageoptim.com) - для Mac

Целевой размер файлов:
- Иконки: < 50 KB
- Логотип: < 100 KB
- Фоны: < 200 KB

## 🔧 Интеграция в код

### Добавление изображения монеты

В `css/style.css`:
```css
.coin-button {
    background: url('../img/coin.png') center/contain no-repeat,
                var(--gradient-gold);
}
```

Или замените текст "NK" на изображение в `index.html`:
```html
<button class="coin-button" id="coinButton">
    <img src="img/coin.png" alt="Neo Klin" class="coin-image">
</button>
```

И добавьте стили:
```css
.coin-image {
    width: 100%;
    height: 100%;
    object-fit: contain;
    pointer-events: none;
}
```

### Добавление фонового паттерна

```css
body {
    background: var(--bg-primary) url('../img/bg-pattern.png') repeat;
}
```

## 📱 Favicon

Добавьте в `<head>` в index.html:
```html
<link rel="icon" type="image/png" sizes="32x32" href="img/favicon-32x32.png">
<link rel="icon" type="image/png" sizes="16x16" href="img/favicon-16x16.png">
<link rel="apple-touch-icon" sizes="180x180" href="img/apple-touch-icon.png">
```

Генератор favicon: [Favicon.io](https://favicon.io/)

## 🎯 Советы по выбору ресурсов

### Звуки:
- **Короткие** (0.1-0.5 сек) - для частых действий (клики)
- **Средние** (0.5-1 сек) - для покупок, наград
- **Длинные** (1-2 сек) - для достижений, уровней
- **Не раздражающие** - игроки будут слышать их много раз

### Изображения:
- **Единый стиль** - все иконки в одном стиле (flat, 3D, pixel art)
- **Читаемость** - элементы должны быть понятны на маленьких экранах
- **Контраст** - хороший контраст с фоном
- **Оптимизация** - сжатые файлы для быстрой загрузки

## 🚀 Готовые наборы

### Для быстрого старта можно использовать:

1. **Game UI Kit** - готовые наборы интерфейсов
2. **Kenney Assets** (kenney.nl) - бесплатные игровые ресурсы
3. **OpenGameArt.org** - открытые игровые ресурсы

---

После добавления всех ресурсов протестируйте игру на разных устройствах!