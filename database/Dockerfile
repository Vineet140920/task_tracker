FROM mongo:latest

COPY ./init-db.js /docker-entrypoint-initdb.d/

ENV MONGO_INITDB_DATABASE=task_db

EXPOSE 27017
