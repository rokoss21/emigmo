# 🚀 БЫСТРЫЙ СТАРТ ДЕПЛОЯ ПРОДАКШН СЕРВЕРА

## ⚡ АВТОМАТИЧЕСКАЯ НАСТРОЙКА (Рекомендуется)

### Шаг 1: Подготовка сервера
```bash
# Подключитесь к вашему серверу по SSH
ssh user@your-server-ip

# Скачайте скрипт настройки
wget https://raw.githubusercontent.com/your-repo/enigmo/main/setup_production_server.sh
chmod +x setup_production_server.sh
```

### Шаг 2: Запуск настройки
```bash
# Запустите скрипт с sudo
sudo bash setup_production_server.sh

# Введите ваш домен и email когда спросят
# Например:
# yourdomain.com
# admin@yourdomain.com
```

### Шаг 3: Деплой приложения
```bash
# Следуйте инструкциям из /opt/enigmo/deploy_instructions.txt
# Основные команды:
cd /opt/enigmo
git clone https://github.com/your-repo/enigmo.git
cd enigmo/enigmo_server
dart pub get
dart compile exe bin/anongram_server.dart -o bin/server

# Создайте сервис
sudo cp /opt/enigmo/deploy_instructions.txt /etc/systemd/system/enigmo.service
sudo systemctl enable enigmo
sudo systemctl start enigmo
```

### Шаг 4: Тестирование
```bash
# Скачайте скрипт тестирования
wget https://raw.githubusercontent.com/your-repo/enigmo/main/test_production_server.sh
chmod +x test_production_server.sh

# Запустите тестирование
bash test_production_server.sh yourdomain.com
```

## 🔧 РУЧНАЯ НАСТРОЙКА (Если нужны кастомные настройки)

### Что делает автоматический скрипт:

✅ **Обновляет систему** и устанавливает необходимое ПО
✅ **Настраивает HTTPS** с Let's Encrypt
✅ **Устанавливает TURN сервер** (coturn)
✅ **Настраивает nginx** reverse proxy
✅ **Открывает необходимые порты** в firewall
✅ **Создает конфигурационные файлы**

### После настройки:

1. **Обновите клиентское приложение** с вашими TURN серверами
2. **Протестируйте** звонки между разными сетями
3. **Мониторьте** логи сервера

## 📋 ЧТО УСТАНАВЛИВАЕТСЯ

| Компонент | Назначение | Порты |
|-----------|------------|-------|
| **nginx** | Reverse proxy + SSL | 80, 443 |
| **coturn** | TURN/STUN сервер | 3478 (UDP), 5349 (TCP) |
| **Dart App** | Enigmo сервер | 8081 |
| **Let's Encrypt** | SSL сертификаты | Автопродление |

## 🎯 ТЕСТИРОВАНИЕ ГОТОВНОСТИ

### Быстрая проверка:
```bash
curl https://yourdomain.com/api/health
# Должен вернуть: {"status":"ok",...}
```

### Полная проверка:
```bash
bash test_production_server.sh yourdomain.com
```

## ⚠️ ВАЖНЫЕ ЗАМЕЧАНИЯ

### Перед запуском:
- Убедитесь, что ваш домен указывает на IP сервера
- Сервер должен быть Ubuntu/Debian с sudo доступом
- Email должен быть реальным для SSL сертификатов

### После настройки:
- **ОБЯЗАТЕЛЬНО** обновите клиентское приложение с TURN серверами
- Протестируйте звонки между разными браузерами/устройствами
- Мониторьте логи на наличие ошибок

### Безопасность:
- Скрипт создает пользователя `enigmo:enigmo123` для TURN
- **Рекомендуется** изменить пароль после настройки
- SSL сертификаты автоматически продляются

## 🚨 НЕ ЗАБУДЬТЕ ОБНОВИТЬ КЛИЕНТ!

После настройки сервера **ОБЯЗАТЕЛЬНО** обновите файл:
`enigmo/enigmo_app/lib/services/audio_call_service.dart`

Замените комментарии на реальные TURN серверы:
```dart
{
  'urls': 'turn:yourdomain.com:3478',
  'username': 'enigmo',
  'credential': 'enigmo123'
}
```

## 📞 ПОДДЕРЖКА

Если возникнут проблемы:
1. Проверьте логи: `sudo journalctl -u nginx -u coturn -u enigmo --no-pager -n 50`
2. Протестируйте компоненты: `bash test_production_server.sh yourdomain.com`
3. Проверьте статус сервисов: `sudo systemctl status nginx coturn enigmo`

## 🎉 ГОТОВО!

После выполнения всех шагов ваши звонки будут работать в продакшне! 🎊