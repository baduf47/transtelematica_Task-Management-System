Необходимо иметь на устройстве:  Docker и Docker Compose; Node.js (рекомендуется версия 22.x). !пример их установки показан после запуска сомого проекта!

для запуска разорхивировать  ttm-hackathon(with_ico).tar.gz

cd ttm-hackathon

если будут какие то ошибки по ходу:

docker-compose down -v (остановит и удалит контейнер)



docker-compose up -d --build

docker exec -it ollama ollama pull llama3.2:3b

(Опционально) docker exec -i taskdb psql -U taskuser taskdb < backup_db.sql


cd frontend


npm install   # только при первом запуске


npm run dev



Откройте в браузере

Фронтенд: http://localhost:5173





Пример инструкции установки Docker и Docker Compose

# Обновление списка пакетов
sudo apt update

# Установка зависимостей
sudo apt install -y ca-certificates curl

# Добавление официального GPG-ключа Docker
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Добавление репозитория Docker
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Установка Docker Engine и Compose
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo usermod -aG docker $USER


Установка Node.js:


curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs


