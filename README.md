для запуска разорхивировать 

cd ttm-hackathon
docker-compose up -d --build
docker exec -it ollama ollama pull llama3.2:3b

(Опционально) docker exec -i taskdb psql -U taskuser taskdb < backup_db.sql


cd frontend
npm install   # только при первом запуске
npm run dev


 Откройте в браузере
Фронтенд: http://localhost:5173
