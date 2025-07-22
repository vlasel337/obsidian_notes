[Ссылка на платформу](https://fly.io/dashboard)

Запуск приложения:
```bash
flyctl scale count 1 -a hft-bot
``` 

Остановка приложения:
```bash
flyctl scale count 0 -a hft-bot
``` 
  
Запуск приложения без использования кэша образа:
```bash
flyctl deploy -a hft-bot --no-cache
``` 

Проверка статуса:
```bash
flyctl status -a hft-bot
``` 

Проверка статусов конкретной машины:
```bash
flyctl vm status 3d8d433db36608 -a hft-bot
``` 

