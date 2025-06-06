# Настройка функции администратора

## Обзор функции

Birthday Bot теперь поддерживает отправку копий всех сгенерированных изображений администратору. Это позволяет администратору:

- 📊 Отслеживать активность пользователей
- 🎨 Видеть все генерируемые изображения
- 📝 Получать информацию о текстах поздравлений
- 🔍 Мониторить качество работы бота

## Настройка админ ID

### 1. Получение вашего Telegram ID

Чтобы узнать свой Telegram ID, воспользуйтесь одним из способов:

**Способ 1: Через @userinfobot**
1. Найдите [@userinfobot](https://t.me/userinfobot) в Telegram
2. Напишите ему любое сообщение
3. Бот ответит с вашим ID

**Способ 2: Через @getmyid_bot**
1. Найдите [@getmyid_bot](https://t.me/getmyid_bot) в Telegram
2. Отправьте команду `/start`
3. Получите свой ID

### 2. Добавление ID в конфигурацию

Откройте файл `../../secrets.yaml` и добавьте/измените секцию:

```yaml
bot:
  token: "ваш_токен_бота"
  admin_user_id: 123456789  # Замените на ваш реальный ID
```

### 3. Альтернативный способ через переменную окружения

Вместо изменения файла можно использовать переменную окружения:

```bash
export TELEGRAM_ADMIN_USER_ID=123456789
```

Или добавить в файл `.env`:
```bash
TELEGRAM_ADMIN_USER_ID=123456789
```

## Что получает администратор

### Формат сообщения администратору

Каждый раз когда пользователь генерирует изображения, администратор получает:

```
🔔 Новая генерация от пользователя:

👤 Пользователь: Иван Петров (@ivan_petrov)
🆔 ID: 123456789
📩 Тип сообщения: 📝 Текстовое (или 🎤 Голосовое)
⏰ Время: 2025-06-01 15:30:45

💬 Текст поздравления:
С днем рождения! Желаю счастья, здоровья и успехов!
```

### Типы отправки

- **Одно изображение**: Отправляется как обычное фото с подписью
- **Несколько изображений**: Отправляется как медиа-группа с подписью к первому изображению

## Проверка настройки

### 1. Валидация конфигурации

```bash
python src/main.py --validate-only
```

Вы должны увидеть:
```
✅ admin_user_configured: True
✅ admin_user_id: 123456789
```

### 2. Проверка в логах

При запуске бота в логах будет:
```
📊 Статус системы:
   ✅ admin_user_configured: True
   ✅ admin_user_id: 123456789
```

### 3. Тестирование функции

1. Запустите бота
2. Отправьте тестовое сообщение от любого пользователя
3. Проверьте, что вы получили копию в Telegram

## Отключение функции

Чтобы отключить отправку копий администратору:

### Способ 1: Удалить из конфигурации
```yaml
bot:
  token: "ваш_токен_бота"
  # admin_user_id: 123456789  # Закомментировать или удалить
```

### Способ 2: Установить в null
```yaml
bot:
  token: "ваш_токен_бота"
  admin_user_id: null
```

### Способ 3: Переменная окружения
```bash
unset TELEGRAM_ADMIN_USER_ID
```

## Безопасность

### Рекомендации

1. **Храните admin_user_id в безопасности** - это даёт доступ ко всем сообщениям пользователей
2. **Используйте файл secrets.yaml** вне репозитория для максимальной безопасности
3. **Регулярно проверяйте логи** на предмет ошибок отправки администратору
4. **Не делитесь админ ID** с посторонними

### Что видит администратор

- ✅ Текст поздравлений пользователей
- ✅ Все сгенерированные изображения
- ✅ Информацию о пользователях (имя, username, ID)
- ✅ Время генерации
- ✅ Тип сообщения (текст или голос)

### Что НЕ видит администратор

- ❌ Сами голосовые файлы
- ❌ Промежуточные данные обработки
- ❌ Личные данные кроме публичных полей Telegram

## Устранение проблем

### Администратор не получает сообщения

1. **Проверьте конфигурацию**:
   ```bash
   python src/main.py --validate-only
   ```

2. **Проверьте логи**:
   ```bash
   tail -f logs/birthday_bot_handlers.log | grep admin
   ```

3. **Убедитесь, что ID корректный**:
   - ID должен быть положительным числом
   - ID должен принадлежать реальному пользователю
   - Пользователь должен хотя бы раз писал боту

### Ошибки в логах

**"❌ Ошибка отправки копии администратору"**
- Проверьте корректность admin_user_id
- Убедитесь, что администратор не заблокировал бота
- Проверьте сетевое подключение

**"🔇 Admin ID не настроен"**
- admin_user_id не указан в конфигурации
- Это нормально, если функция не нужна

## Логирование

### Успешная отправка
```
📤 Отправка копии администратору (ID: 123456789)
✅ Копия отправлена администратору (одно изображение)
```

### Неудачная отправка
```
❌ Ошибка отправки копии администратору: Forbidden: bot was blocked by the user
```

### Отключенная функция
```
🔇 Admin ID не настроен, пропускаем отправку администратору
```

## Примеры конфигурации

### Минимальная настройка
```yaml
bot:
  token: "your_bot_token"
  admin_user_id: 123456789
```

### Полная настройка для администратора
```yaml
bot:
  token: "your_bot_token"
  admin_user_id: 123456789

# Улучшенное логирование для мониторинга
logging:
  level: "INFO"

# Оптимизация для продакшена
production:
  performance_monitoring: true
  cleanup_on_start: true

# Более качественная генерация
diffusion:
  num_images: 4
  device: "cuda"

speech:
  model_name: "medium"
```
