- 👋 Hi, I’m @RafaelExtremoYT
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
RafaelExtremoYT/RafaelExtremoYT is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
# app.py
from flask import Flask, jsonify, request
import json
import os

app = Flask(__name__)

# Caminho para o arquivo de armazenamento das tarefas
TASKS_FILE = 'tasks.json'

# Função para ler as tarefas do arquivo JSON
def load_tasks():
    if not os.path.exists(TASKS_FILE):
        return []
    with open(TASKS_FILE, 'r') as file:
        return json.load(file)

# Função para salvar as tarefas no arquivo JSON
def save_tasks(tasks):
    with open(TASKS_FILE, 'w') as file:
        json.dump(tasks, file, indent=4)

# Rota para obter todas as tarefas
@app.route('/tasks', methods=['GET'])
def get_tasks():
    tasks = load_tasks()
    return jsonify(tasks)

# Rota para criar uma nova tarefa
@app.route('/tasks', methods=['POST'])
def create_task():
    data = request.get_json()

    if 'title' not in data or 'description' not in data:
        return jsonify({'error': 'Missing title or description'}), 400

    tasks = load_tasks()

    new_task = {
        'id': len(tasks) + 1,
        'title': data['title'],
        'description': data['description']
    }

    tasks.append(new_task)
    save_tasks(tasks)

    return jsonify(new_task), 201

# Rota para obter uma tarefa específica por ID
@app.route('/tasks/<int:id>', methods=['GET'])
def get_task(id):
    tasks = load_tasks()
    task = next((task for task in tasks if task['id'] == id), None)

    if task is None:
        return jsonify({'error': 'Task not found'}), 404

    return jsonify(task)

if __name__ == '__main__':
    app.run(debug=True)

