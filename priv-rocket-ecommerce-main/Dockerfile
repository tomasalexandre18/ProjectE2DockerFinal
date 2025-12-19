FROM node:18-slim AS npm-dependencies
WORKDIR /app
COPY package*.json .
RUN npm i

COPY . .
RUN npm run build

FROM python:3.11-slim
WORKDIR /app
# set environment variables
ENV PYTHONDONTWRITEBYTECODE 1 && PYTHONUNBUFFERED 1

COPY requirements.txt .
# install python dependencies
RUN pip install --upgrade pip && pip install --no-cache-dir -r requirements.txt

COPY . .
COPY --from=npm-dependencies /app/static/dist /app/static/dist

# Manage Assets & DB 
RUN python manage.py collectstatic --no-input && python manage.py makemigrations && python manage.py migrate

# gunicorn
CMD ["gunicorn", "--config", "gunicorn-cfg.py", "core.wsgi"]
