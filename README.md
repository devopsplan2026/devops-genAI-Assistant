# 🤖 GenAI-Assistant

---

### Prerequisites

- **Node.js** (v18 or higher) - for frontend
- **Python** (3.11 or higher) - for backend
- **Docker & Docker Compose** (optional but recommended)
- **OpenAI API Key** - Get from [platform.openai.com](https://platform.openai.com)

---

## ✨ Main Features

### 🎯 1. **Interview Preparation Bot**
- AI-powered DevOps technical interview questions and answers
- Detailed explanations with real-world examples
- Expert-level knowledge base for interview preparation

### 💻 2. **Code Explainer**
- Analyze and explain code snippets in detail
- DevOps-focused explanations with practical scenarios
- Break down complex logic into understandable concepts

### 🔧 3. **DevOps Assistant**
- Troubleshoot errors and deployment issues
- Get expert guidance on resolving DevOps problems
- Detailed analysis of error logs and debugging

---

### 📡 Data Flow

1. **User Input** → Frontend captures user query/code/error
2. **API Request** → Frontend sends POST request to backend endpoint
3. **Processing** → Backend utility processes the request
4. **AI Call** → OpenAI API is called with customized prompt
5. **Response** → AI response is returned and displayed on frontend
6. **Output** → User sees formatted AI-generated response

---

## Phase 1

## 🚀 How It Works

### ⚡ Local Setup


1. Install Python & pip (if not already)

sudo apt update
sudo apt install -y python3 python3-pip python3-venv

2. Create a Virtual Environment (Optional but Recommended)

- cd backend and create a Virtual Environment
> python3 -m venv venv
> source venv/bin/activate

- Now install requirements: 
> pip install -r requirements.txt

3. Go to Frontend side and check requirements.

node -v    
Install node.js 22

4. create .env file backend side.

vi .env 

paste the api like:

OPENAI_API_KEY=<API>

5. Come on front-end side and set the backend url

cd forntend

vi .env

NEXT_PUBLIC_API_BASE_URL=http://13.232.92.206:8000

6. now we can start the backend : go to backend side and run the commmand 

uvicorn main:app --reload --host 0.0.0.0 --port 8000

6. no go to forntend side: install the dependencies 
> npm install 

now start frondend: npm run dev

go to the link: <private-ip>:3000


## Phase 2

Create a docker file for Backend and Frontend

cd Backend:
---------

# backend/Dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]

cd Frontend
----------------

# frontend/Dockerfile

# Build step
FROM node:18-alpine as builder
WORKDIR /app

COPY package.json package-lock.json ./
RUN npm install

COPY . .
RUN npm run build

# Runtime step
FROM node:18-alpine
WORKDIR /app

COPY --from=builder /app .

EXPOSE 3000
CMD ["npm", "start"]


Docker-compose file

version: '3.8'

services:
  backend:
    build:
      context: ./backend
    ports:
      - "8000:8000"
    env_file:
      - ./backend/.env

  frontend:
    build:
      context: ./frontend
    ports:
      - "3000:3000"
    environment:
      - NEXT_PUBLIC_API_BASE_URL=http://backend:8000
    depends_on:
      - backend
    
    # Run the project

    docker-compose up  --build


