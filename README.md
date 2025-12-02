# Ex03 To-Do List using JavaScript
## Date:

## AIM
To create a To-do Application with all features using JavaScript.

## ALGORITHM
### STEP 1
Build the HTML structure (index.html).

### STEP 2
Style the App (style.css).

### STEP 3
Plan the features the To-Do App should have.

### STEP 4
Create a To-do application using Javascript.

### STEP 5
Add functionalities.

### STEP 6
Test the App.

### STEP 7
Open the HTML file in a browser to check layout and functionality.

### STEP 8
Fix styling issues and refine content placement.

### STEP 9
Deploy the website.

### STEP 10
Upload to GitHub Pages for free hosting.

## PROGRAM
## INDEX.HTML
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Beautiful To-Do App</title>
  <link rel="stylesheet" href="todo.css"/>
</head>
<body>
  <div class="app-container">
    <h1 class="title">To-Do List</h1>

    <div class="input-section">
      <input type="text" id="taskInput" placeholder="Enter a new task...">
      <button id="addBtn">Add</button>
    </div>

    <div class="filter-section">
      <button class="filter-btn active" data-filter="all">All</button>
      <button class="filter-btn" data-filter="active">Active</button>
      <button class="filter-btn" data-filter="completed">Completed</button>
    </div>

    <ul id="taskList" class="task-list"></ul>
    
    <button id="clearAll" class="clear-btn">Clear All</button>
  </div>

  <script src="todo.js"></script>
</body>
</html>

```
## todo.css
```
body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: linear-gradient(135deg, #22edfb, #82d3f6);
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0;
}

.app-container {
  background: #fff;
  width: 400px;
  padding: 20px;
  border-radius: 20px;
  box-shadow: 0 10px 25px rgba(0,0,0,0.2);
  animation: fadeIn 1s ease;
}

@keyframes fadeIn {
  from {opacity: 0; transform: scale(0.9);}
  to {opacity: 1; transform: scale(1);}
}

.title {
  text-align: center;
  margin-bottom: 20px;
  color: #333;
}

.input-section {
  display: flex;
  gap: 10px;
}

.input-section input {
  flex: 1;
  padding: 10px;
  border: 2px solid #5de8f2;
  border-radius: 10px;
  outline: none;
}

.input-section button {
  background: #0ae7f3;
  border: none;
  padding: 10px 15px;
  border-radius: 10px;
  color: #fff;
  cursor: pointer;
  transition: 0.3s;
}

.input-section button:hover {
  background: #1ecdef;
}

.filter-section {
  display: flex;
  justify-content: center;
  margin: 15px 0;
  gap: 10px;
}

.filter-btn {
  background: #eee;
  border: none;
  padding: 6px 12px;
  border-radius: 20px;
  cursor: pointer;
  transition: 0.3s;
}

.filter-btn.active,
.filter-btn:hover {
  background: #14e3f2;
  color: #fff;
}

.task-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.task-list li {
  background: #f9f9f9;
  padding: 12px;
  border-radius: 12px;
  margin-bottom: 10px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  transition: 0.3s;
}

.task-list li.completed {
  text-decoration: line-through;
  opacity: 0.6;
}

.task-text {
  flex: 1;
  cursor: pointer;
}

.actions {
  display: flex;
  gap: 8px;
}

.actions button {
  border: none;
  background: none;
  cursor: pointer;
  font-size: 16px;
}

.actions .edit {
  color: #ffa502;
}

.actions .delete {
  color: #ff4757;
}

.clear-btn {
  margin-top: 15px;
  width: 100%;
  padding: 10px;
  border: none;
  border-radius: 12px;
  background: #ff4757;
  color: #fff;
  cursor: pointer;
  transition: 0.3s;
}

.clear-btn:hover {
  background: #e84118;
}

```
## todo.js
```
const taskInput = document.getElementById("taskInput");
const addBtn = document.getElementById("addBtn");
const taskList = document.getElementById("taskList");
const filterBtns = document.querySelectorAll(".filter-btn");
const clearAllBtn = document.getElementById("clearAll");

let tasks = JSON.parse(localStorage.getItem("tasks")) || [];
addBtn.addEventListener("click", () => {
  const taskText = taskInput.value.trim();
  if (taskText === "") return;
  const newTask = { id: Date.now(), text: taskText, completed: false };
  tasks.push(newTask);
  saveTasks();
  renderTasks();

  taskInput.value = "";
});
function renderTasks(filter = "all") {
  taskList.innerHTML = "";

  let filteredTasks = tasks;
  if (filter === "active") filteredTasks = tasks.filter(t => !t.completed);
  if (filter === "completed") filteredTasks = tasks.filter(t => t.completed);

  filteredTasks.forEach(task => {
    const li = document.createElement("li");
    li.className = task.completed ? "completed" : "";

    const span = document.createElement("span");
    span.textContent = task.text;
    span.className = "task-text";
    span.addEventListener("click", () => toggleTask(task.id));

    const actions = document.createElement("div");
    actions.className = "actions";

    const editBtn = document.createElement("button");
    editBtn.className = "edit";
    editBtn.innerHTML = "✏️";
    editBtn.addEventListener("click", () => editTask(task.id));

    const deleteBtn = document.createElement("button");
    deleteBtn.className = "delete";
    deleteBtn.innerHTML = "🗑️";
    deleteBtn.addEventListener("click", () => deleteTask(task.id));

    actions.appendChild(editBtn);
    actions.appendChild(deleteBtn);

    li.appendChild(span);
    li.appendChild(actions);
    taskList.appendChild(li);
  });
}
function toggleTask(id) {
  tasks = tasks.map(task =>
    task.id === id ? { ...task, completed: !task.completed } : task
  );
  saveTasks();
  renderTasks(getActiveFilter());
}
function editTask(id) {
  const task = tasks.find(t => t.id === id);
  const newText = prompt("Edit task:", task.text);
  if (newText !== null && newText.trim() !== "") {
    task.text = newText.trim();
    saveTasks();
    renderTasks(getActiveFilter());
  }
}
function deleteTask(id) {
  tasks = tasks.filter(task => task.id !== id);
  saveTasks();
  renderTasks(getActiveFilter());
}
clearAllBtn.addEventListener("click", () => {
  if (confirm("Delete all tasks?")) {
    tasks = [];
    saveTasks();
    renderTasks();
  }
});

filterBtns.forEach(btn => {
  btn.addEventListener("click", () => {
    document.querySelector(".filter-btn.active").classList.remove("active");
    btn.classList.add("active");
    renderTasks(btn.dataset.filter);
  });
});

function saveTasks() {
  localStorage.setItem("tasks", JSON.stringify(tasks));
}

function getActiveFilter() {
  return document.querySelector(".filter-btn.active").dataset.filter;
}

renderTasks();

```
## OUTPUT
<img width="1913" height="1079" alt="image" src="https://github.com/user-attachments/assets/42ca4412-cf84-487a-a1bb-db0c0b49a11c" />

<img width="1912" height="1078" alt="Screenshot 2025-09-09 213513" src="https://github.com/user-attachments/assets/ad8ddea8-4e6e-4985-b54f-15811c7ed51f" />

## RESULT
The program for creating To-do list using JavaScript is executed successfully.
