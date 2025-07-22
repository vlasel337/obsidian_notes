Backup .dump 
```bash
pg_dump -U admin -h dpg-d0o41m6mcj7s73e3q1r0-a.frankfurt-postgres.render.com -p 5432 -Fc -f hft_db_backup.dump hft_db
```

```bash
/opt/homebrew/opt/postgresql@16/bin/pg_dump -U admin -h dpg-d0o41m6mcj7s73e3q1r0-a.frankfurt-postgres.render.com -p 5432 -Fc -f hft_db_backup.dump hft_db
```

Backup .sql БД Render:
```bash
/opt/homebrew/Cellar/postgresql@16/16.9/bin/pg_dump -U admin -h dpg-d05l1pq4d50c73f4qqfg-a.frankfurt-postgres.render.com -p 5432 -f hft_db_backup.sql hft_0yd2
```

Просмотр файла бэкапа:
 ```bash
ls -lh hft_db_backup.dump
``` 

Восстановление БД из бэкапа:
```bash
/opt/homebrew/opt/postgresql@16/bin/pg_restore -U admin -h localhost -p 5432 -d postgres hft_db_backup.dump --no-privileges
```



