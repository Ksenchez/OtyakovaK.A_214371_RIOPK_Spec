# **Программное средство анализа политики ценообразования продукции на предприятии и автоматизации расчетной методики**

Цель проекта — Оптимизация политики ценообразования предприятия за счёт автоматизации анализа рыночных данных, себестоимости продукции и конкурентной среды, что позволит повысить прибыльность, конкурентоспособность и обоснованность ценовых решений.

**Сервер**: [https://github.com/Ksenchez/OtyakovaK.A_214371_RIOPK_Server](https://github.com/Ksenchez/OtyakovaK.A_214371_RIOPK_Server)  
**Клиент**: [https://github.com/Ksenchez/OtyakovaK.A_214371_RIOPK_Front](https://github.com/Ksenchez/OtyakovaK.A_214371_RIOPK_Front)

---

## **Содержание**

1. [Архитектура](#архитектура)
2. [Функциональные возможности](#функциональные-возможности)
3. [Детали реализации](#детали-реализации)
4. [Тестирование](#тестирование)
5. [Установка и запуск](#установка-и-запуск)
6. [Лицензия](#лицензия)
7. [Контакты](#контакты)

---

## **Архитектура**

### C4-модель

#### Контекстный уровень
![Контекст](png/1.png)

#### Контейнерный уровень
![Контейнеры](png/1.png)

#### Компонентный уровень
![Компоненты](png/3.png)

#### Кодовый уровень
![Код](docs/Кодовый_уровень.png)

### Схема данных
![ER-модель](docs/ER_Diagram.png)

```sql
-- SQL-скрипт для создания базы данных
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    email TEXT UNIQUE NOT NULL,
    hashed_password TEXT NOT NULL,
    role TEXT NOT NULL
);

CREATE TABLE requests (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    user_id INTEGER NOT NULL,
    content TEXT NOT NULL,
    status TEXT DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY(user_id) REFERENCES users(id)
);

CREATE TABLE staff_actions (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    request_id INTEGER NOT NULL,
    staff_id INTEGER NOT NULL,
    action TEXT NOT NULL,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY(request_id) REFERENCES requests(id),
    FOREIGN KEY(staff_id) REFERENCES users(id)
);
```

---

## **Функциональные возможности**

### Диаграмма вариантов использования
![Диаграмма вариантов использования](docs/UseCase.png)

### User-flow диаграмма

![User Flow](docs/User_Flow.png)


---

## **Детали реализации**

### UML-диаграммы

#### Диаграмма классов
![Диаграмма классов](docs/UML_Class_Diagram.png)

#### Диаграмма последовательностей
![Диаграмма последовательностей](docs/UML_Sequence_Diagram.png)

#### Диаграмма компонентов
![Диаграмма компонентов](docs/UML_Component_Diagram.png)

### Спецификация API

Открыть в браузере:  
[http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)

OpenAPI YAML-файл: `openapi.json` генерируется автоматически.

### Безопасность

Использована JWT-аутентификация:
```python
from jose import jwt

def create_access_token(data: dict, expires_delta: timedelta = None):
    to_encode = data.copy()
    expire = datetime.utcnow() + (expires_delta or timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES))
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
```

### Оценка качества кода

Анализ с использованием `flake8`, `pylint` и `radon`:
- Cyclomatic Complexity (radon): нормальный уровень
- Ошибки статики (`flake8`): отсутствуют
- Code Rating (`pylint`): > 8.0

![Оценка_качества_кода](docs/Оценка_качества_кода.png)

---

## **Тестирование**

### Unit-тесты

Покрытие:
- Проверка хеширования пароля
- Проверка генерации токена
- Проверка корневого эндпоинта `/`
- Проверка `/me`
- Проверка обработки входа

Файл: `app/tests/test_unit_auth.py`
```python
def test_verify_password():
    assert verify_password("123", get_password_hash("123"))
```

### Интеграционные тесты

Файл: `app/tests/test_integration_auth.py`, `app/tests/test_integration_protected.py`
```python
def test_login_user():
    response = client.post("/login", data={"email": "test@example.com", "password": "test"})
    assert response.status_code == 200
```

![Тесты](docs/Тесты.png)

---

## **Установка и запуск**

### Манифесты для сборки docker образов

Файл `Dockerfile`:
```dockerfile
FROM python:3.10
WORKDIR /app
COPY . .
RUN pip install --no-cache-dir -r requirements.txt
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Манифесты для развертывания k8s кластера

Файл `deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: riopk-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: riopk
  template:
    metadata:
      labels:
        app: riopk
    spec:
      containers:
        - name: riopk
          image: riopk:latest
          ports:
            - containerPort: 8000
```

---

## **Лицензия**

Этот проект лицензирован по лицензии MIT – подробности в файле [LICENSE.md](LICENSE.md)

---

## **Контакты**

Автор: Иван Коцуба  
Email: deadpool.minsk.2016@gmail.com
