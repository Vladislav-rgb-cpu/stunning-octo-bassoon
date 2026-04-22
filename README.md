# Владислав Мясоедов
import tkinter as tk
import random
import json
import os

# Константы
TASK_TYPES = ['учёба', 'спорт', 'работа', 'быт', 'развлечение']
TASKS_FILE = 'tasks.Json'

class RandomTaskGenerator:
 def __init__(self, root):
 self.Root = root
 self.Root.Title("Random Task Generator")
 self.Root.Geometry("500x600")

 # Загрузка истории задач
 self.Tasks = self.Load_tasks()

 # Интерфейс
 self.Setup_ui()

 def setup_ui(self):
 # Заголовок
 tk.Label(self.Root, text="Генератор случайных задач", font=("Arial", 16)).Pack(pady=10)

 # Поле для новой задачи
 tk.Label(self.Root, text="Новая задача:").Pack()
 self.New_task_entry = tk.Entry(self.Root, width=40)
 self.New_task_entry.Pack(pady=5)

 # Выбор типа
 tk.Label(self.Root, text="Тип задачи:").Pack()
 self.Type_var = tk.StringVar(value="учёба")
 for t in TASK_TYPES:
 tk.Radiobutton(self.Root, text=t, variable=self.Type_var, value=t).Pack(anchor="w")

 # Кнопка добавления
 tk.Button(self.Root, text="Добавить задачу", command=self.Add_task).Pack(pady=5)

 # Кнопка генерации
 tk.Button(self.Root, text="Сгенерировать задачу", command=self.Generate_task).Pack(pady=10)

 # Поле вывода задачи
 tk.Label(self.Root, text="Сгенерированная задача:").Pack()
 self.Task_label = tk.Label(self.Root, text="", wraplength=400, justify="left")
 self.Task_label.Pack(pady=5)

 # Фильтр
 tk.Label(self.Root, text="Фильтр по типу:").Pack()
 self.Filter_var = tk.StringVar(value="все")
 for t in TASK_TYPES + ["все"]:
 tk.Radiobutton(self.Root, text=t, variable=self.Filter_var, value=t).Pack(anchor="w")

 # Список задач
 tk.Label(self.Root, text="История задач:").Pack()
 self.Task_list = tk.Listbox(self.Root, width=60, height=15)
 self.Task_list.Pack(pady=5)

 # Обновление списка
 self.Update_task_list()

 def load_tasks(self):
 if os.Path.Exists(TASKS_FILE):
 with open(TASKS_FILE, 'r', encoding='utf-8') as f:
 return json.Load(f)
 return []

 def save_tasks(self):
 with open(TASKS_FILE, 'w', encoding='utf-8') as f:
 json.Dump(self.Tasks, f, ensure_ascii=False, indent=2)

 def add_task(self):
 task_text = self.New_task_entry.Get().Strip()
 if not task_text:
 return # Пустая строка — не добавляем

 task = {
 'text': task_text,
 'type': self.Type_var.Get(),
 'timestamp': self.Get_current_time()
}
 self
