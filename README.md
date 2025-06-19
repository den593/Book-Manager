# Book-Manager
#include <iostream>
#include <fstream>
#include <cstring>
using namespace std;

const int MAX_BOOKS = 100;

struct Book {
    int id;
    char title[100];
    char author[100];
    int pages;
    int year;
};

void addBook(Book books[], int& count);
void listBooks(const Book books[], int count);
void saveToFile(const Book books[], int count, const char* filename);
void loadFromFile(Book books[], int& count, const char* filename);
void searchByTitle(const Book books[], int count, const char* keyword);
void sortByYear(Book books[], int count);
void deleteBook(Book books[], int& count, int id);

int main() {
    Book books[MAX_BOOKS];
    int count = 0;
    int choice;
    char filename[] = "books.txt";

    loadFromFile(books, count, filename);

    do {
        cout << "\n=== Меню ===\n";
        cout << "1. Додати книгу\n";
        cout << "2. Показати всі книги\n";
        cout << "3. Пошук за назвою\n";
        cout << "4. Сортувати за роком\n";
        cout << "5. Видалити книгу\n";
        cout << "6. Зберегти в файл\n";
        cout << "0. Вихід\n";
        cout << "Ваш вибір: ";
        cin >> choice;
        cin.ignore(); 

        switch (choice) {
        case 1:
            addBook(books, count);
            break;
        case 2:
            listBooks(books, count);
            break;
        case 3: {
            char keyword[100];
            cout << "Введіть ключове слово: ";
            cin.getline(keyword, 100);
            searchByTitle(books, count, keyword);
            break;
        }
        case 4:
            sortByYear(books, count);
            break;
        case 5: {
            int id;
            cout << "Введіть ID книги для видалення: ";
            cin >> id;
            deleteBook(books, count, id);
            break;
        }
        case 6:
            saveToFile(books, count, filename);
            break;
        case 0:
            saveToFile(books, count, filename);
            cout << "Програма завершена.\n";
            break;
        default:
            cout << "Невірний вибір!\n";
        }

    } while (choice != 0);

    return 0;
}


void addBook(Book books[], int& count) {
    if (count >= MAX_BOOKS) {
        cout << "База заповнена!\n";
        return;
    }

    cout << "\n=== Додавання книги ===\n";
    cout << "ID: ";
    cin >> books[count].id;
    cin.ignore();
    cout << "Назва: ";
    cin.getline(books[count].title, 100);
    cout << "Автор: ";
    cin.getline(books[count].author, 100);
    cout << "Кількість сторінок: ";
    cin >> books[count].pages;
    cout << "Рік видання: ";
    cin >> books[count].year;
    count++;
    cout << "Книгу додано!\n";
}

void listBooks(const Book books[], int count) {
    cout << "\n=== Список книг ===\n";
    if (count == 0) {
        cout << "Немає книг у базі.\n";
        return;
    }
    for (int i = 0; i < count; i++) {
        cout << "\nID: " << books[i].id;
        cout << "\nНазва: " << books[i].title;
        cout << "\nАвтор: " << books[i].author;
        cout << "\nСторінки: " << books[i].pages;
        cout << "\nРік: " << books[i].year << "\n";
    }
}


void saveToFile(const Book books[], int count, const char* filename) {
    ofstream file(filename);
    if (!file) {
        cout << "Помилка при збереженні у файл!\n";
        return;
    }
    for (int i = 0; i < count; i++) {
        file << books[i].id << ";"
            << books[i].title << ";"
            << books[i].author << ";"
            << books[i].pages << ";"
            << books[i].year << "\n";
    }
    file.close();
    cout << "Книги збережено у файл.\n";
}


void loadFromFile(Book books[], int& count, const char* filename) {
    ifstream file(filename);
    if (!file) {
        return; 
    }
    count = 0;
    while (!file.eof() && count < MAX_BOOKS) {
        char buffer[300];
        file.getline(buffer, 300);
        if (strlen(buffer) == 0) continue;

        char* token = strtok(buffer, ";");
        if (!token) continue;
        books[count].id = atoi(token);
token = strtok(NULL, ";");
        if (!token) continue;
        strcpy(books[count].title, token);

        token = strtok(NULL, ";");
        if (!token) continue;
        strcpy(books[count].author, token);

        token = strtok(NULL, ";");
        if (!token) continue;
        books[count].pages = atoi(token);

        token = strtok(NULL, ";");
        if (!token) continue;
        books[count].year = atoi(token);

        count++;
    }
    file.close();
}


void searchByTitle(const Book books[], int count, const char* keyword) {
    cout << "\n=== Результати пошуку ===\n";
    bool found = false;
    for (int i = 0; i < count; i++) {
        if (strstr(books[i].title, keyword)) {
            cout << "\nID: " << books[i].id;
            cout << "\nНазва: " << books[i].title;
            cout << "\nАвтор: " << books[i].author;
            cout << "\nСторінки: " << books[i].pages;
            cout << "\nРік: " << books[i].year << "\n";
            found = true;
        }
    }
    if (!found)
        cout << "Нічого не знайдено.\n";
}


void sortByYear(Book books[], int count) {
    for (int i = 0; i < count - 1; i++) {
        for (int j = i + 1; j < count; j++) {
            if (books[i].year > books[j].year) {
                swap(books[i], books[j]);
            }
        }
    }
    cout << "Список відсортовано за роком.\n";
}


void deleteBook(Book books[], int& count, int id) {
    for (int i = 0; i < count; i++) {
        if (books[i].id == id) {
            for (int j = i; j < count - 1; j++) {
                books[j] = books[j + 1];
            }
            count--;
            cout << "Книгу видалено.\n";
            return;
        }
    }
    cout << "Книга з таким ID не знайдена.\n";
