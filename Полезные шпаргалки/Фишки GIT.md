## Создание локального репозитория и связка локального с облачным 
Чтобы сделать локальную папку git репозиторием, необходимо, находясь в корне этой папки выполнить команду (она создает в корне директории папку `.git`):
`git init`

Далее для того, чтобы сделать инициирующий пуш нужно выполнить команды:
```zsh
git remote add origin git@github-second:vlasel337/documents.git
git branch -M main
git push -u origin main
```


## Переключение между учетками
Для локального переключения между профилями (SSH-ключами) в GIT нужно использовать команду: 
`git remote set-url origin git@github-second:vlasel337/dbt_scooters_new.git`

Для проверки под какой учеткой авторизован в локальном репозитоиии нужно использовать команду:
`ssh -T git@github.com`
`ssh -T git@github-vlasel337`


Файл `.gitkeep` нужен в пустой папке для того, чтобы заставить Git отслеживать пустую папку.

Команда, чтобы открыть ssh-config:
`nano ~/.ssh/config`


Команда для привязки локального репозитория к другому аккаунту (SSH-ключу):
`git remote set-url origin git@github-second:vlasel337/documents.git`

ssh -T git@github-second