# Владислав Мясоедов
import tkinter as tk
from tkinter import ttk, messagebox
import json
import os

class BookTracker:
    def __init__(self, root):
        self.root = root
        self.root.title("Book Tracker")
        self.books = []
        self.load_data()
        self.create_widgets()

    def create_widgets(self):
        # Поля ввода
        tk.Label(self.root, text="Название книги:").grid(row=0, column=0, padx=5, pady=5, sticky="w")
        self.title_entry = tk.Entry(self.root, width=30)
        self.title_entry.grid(row=0, column=1, padx=5, pady=5)

        tk.Label(self.root, text="Автор:").grid(row=1, column=0, padx=5, pady=5, sticky="w")
        self.author_entry = tk.Entry(self.root, width=30)
        self.author_entry.grid(row=1, column=1, padx=5, pady=5)

        tk.Label(self.root, text="Жанр:").grid(row=2, column=0, padx=5, pady=5, sticky="w")
        self.genre_entry = tk.Entry(self.root, width=30)
        self.genre_entry.grid(row=2, column=1, padx=5, pady=5)

        tk.Label(self.root, text="Количество страниц:").grid(row=3, column=0, padx=5, pady=5, sticky="w")
        self.pages_entry = tk.Entry(self.root, width=30)
        self.pages_entry.grid(row=3, column=1, padx=5, pady=5)

        # Кнопка добавления
        tk.Button(self.root, text="Добавить книгу", command=self.add_book).grid(
            row=4, column=0, columnspan=2, pady=10
        )

        # Фильтр по жанру
        tk.Label(self.root, text="Фильтр по жанру:").grid(row=5, column=0, padx=5, pady=5, sticky="w")
        self.filter_genre = ttk.Combobox(self.root, values=["Все жанры"], state="readonly")
        self.filter_genre.set("Все жанры")
        self.filter_genre.grid(row=5, column=1, padx=5, pady=5)

        # Фильтр по страницам
        tk.Label(self.root, text="Фильтр > страниц:").grid(row=6, column=0, padx=5, pady=5, sticky="w")
        self.filter_pages = tk.Entry(self.root, width=30)
        self.filter_pages.insert(0, "0")
        self.filter_pages.grid(row=6, column=1, padx=5, pady=5)

        # Кнопка фильтрации
        tk.Button(self.root, text="Применить фильтры", command=self.apply_filters).grid(
            row=7, column=0, columnspan=2, pady=10
        )

        # Таблица для отображения книг
        columns = ("Title", "Author", "Genre", "Pages")
        self.tree = ttk.Treeview(self.root, columns=columns, show="headings", height=10)
        for col in columns:
            self.tree.heading(col, text=col.replace("Title", "Название").replace("Author", "Автор").replace("Genre", "Жанр").replace("Pages", "Страниц"))
            self.tree.column(col, width=120)
        self.tree.grid(row=8, column=0, columnspan=2, padx=5, pady=5, sticky="nsew")

        # Кнопки сохранения и загрузки
        tk.Button(self.root, text="Сохранить данные", command=self.save_data).grid(row=9, column=0, pady=10)
        tk.Button(self.root, text="Загрузить данные", command=self.load_data).grid(row=9, column=1, pady=10)

        self.update_table()
        self.update_genre_filter()

    def add_book(self):
        title = self.title_entry.get().strip()
        author = self.author_entry.get().strip()
        genre = self.genre_entry.get().strip()
        pages_str = self.pages_entry.get().strip()


        if not title or not author or not genre:
            messagebox.showerror("Ошибка", "Все поля должны быть заполнены!")
            return

        try:
            pages = int(pages_str)
            if pages <= 0:
                raise ValueError
        except ValueError:
            messagebox.showerror("Ошибка", "Количество страниц должно быть положительным числом!")
            return

        book = {"title": title, "author": author, "genre": genre, "pages": pages}
        self.books.append(book)

        # Очищаем поля ввода
        self.title_entry.delete(0, tk.END)
        self.author_entry.delete(0, tk.END)
        self.genre_entry.delete(0, tk.END)
        self.pages_entry.delete(0, tk.END)

        self.update_table()
        self.update_genre_filter()


    def update_table(self):
        for item in self.tree.get_children():
            self.tree.delete(item)
        for book in self.books:
            self.tree.insert("", "end", values=(book["title"], book["author"], book["genre"], book["pages"]))

    def get_genres(self):
        return list(set(book["genre"] for book in self.books))
    def update_genre_filter(self):
        genres = ["Все жанры"] + self.get_genres()
        self.filter_genre["values"] = genres
    def apply_filters(self):
        selected_genre = self.filter_genre.get()
        try:
            min_pages = int(self.filter_pages.get())
        except ValueError:
            min_pages = 0

        filtered_books = self.books
        if selected_genre != "Все жанры":
            filtered_books = [book for book in filtered_books if book["genre"] == selected_genre]
        filtered_books = [book for book in filtered_books if book["pages"] >= min_pages]

        for item in self.tree.get_children():
            self.tree.delete(item)
        for book in filtered_books:
            self.tree.insert("", "end", values=(book["title"], book["author"], book["genre"], book["pages"]))
    def save_data(self):
        with open("books.json", "w", encoding="utf-8") as f:
            json.dump(self.books, f, ensure_ascii=False, indent=4)
        messagebox.showinfo("Успех", "Данные сохранены в books.json")
    def load_data(self):
        if os.path.exists("books.json"):
            with open("books.json", "r", encoding="utf-8") as f:
                self.books = json.load(f)
            self.update_table()
            self.update_genre_filter()
            messagebox.showinfo("Успех", "Данные загружены из books.json")
        else:
            messagebox.showwarning("Внимание", "Файл books.json не найден. Создан новый список.")

if __name__ == "__main__":
    root = tk.Tk()
    app = BookTracker(root)
    root.mainloop()
    
