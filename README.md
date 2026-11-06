    }

    public void AddReader(String name, String surnameOrPhone, boolean isPhone) {
        if (isPhone) addReaderIfNew(name, "", surnameOrPhone);
        else addReaderIfNew(name, surnameOrPhone, "");
    }

    // Отдельная сигнатура для варианта «имя + телефон».
    public void AddReader(String name, Phone phone) {
        addReaderIfNew(name, "", phone.value);
    }

    private void addReaderIfNew(String name, String surname, String phone) {
        if (name == null || name.isBlank()) {
            System.out.println("Имя читателя не может быть пустым.");
            return;
        }
        if (IsReaderRegistered(name)) {
            System.out.println("Читатель с таким именем уже зарегистрирован.");
            return;
        }
        readers.add(new Reader(name.trim(), surname, phone));
        System.out.println("Читатель успешно зарегистрирован.");
    }

    // Задача 18: возврат без расчёта штрафа.
    public boolean ReturnBook(String title) {
        Book book = FindBook(title);
        if (book == null) {
            System.out.println("Книга не найдена.");
            return false;
        }
        if (book.isAvailable()) {
            System.out.println("Эта книга не числится выданной.");
            return false;
        }
        book.giveBack();
        System.out.println("Книга успешно возвращена.");
        return true;
    }

    // Задача 19: рекурсивный подсчёт.
    public int CountBooksRecursive() {
        return countRecursive(books, 0);
    }

    private int countRecursive(List<Book> list, int index) {
        if (index >= list.size()) return 0;
        return 1 + countRecursive(list, index + 1);
    }

    // Задача 20.
    public void SortBooksByYear() {
        books.sort(Comparator.comparingInt(Book::getYear));
        System.out.println("Книги отсортированы по году издания.");
    }

    // Задача 21.
    public boolean BorrowBook(String title, String readerName) {
        Book book = FindBook(title);
        if (book == null) {
            System.out.println("Книга не найдена.");
            return false;
        }
        if (!IsReaderRegistered(readerName)) {
            System.out.println("Читатель не зарегистрирован.");
            return false;
        }
        if (!book.isAvailable()) {
            System.out.println("Книга уже выдана.");
            return false;
        }
        book.borrow(readerName);
        System.out.println("Книга выдана читателю: " + readerName);
        return true;
    }

    // Задача 22: возврат с проверкой статуса и расчётом штрафа.
    public boolean ReturnBook(String title, int overdueDays) {
        Book book = FindBook(title);
        if (book == null) {
            System.out.println("Книга не найдена.");
            return false;
        }
        if (book.isAvailable()) {
            System.out.println("Книга уже находится в библиотеке.");
            return false;
        }
        int fine = CalculateFine(overdueDays);
        book.giveBack();
        System.out.println("Книга успешно возвращена.");
        System.out.println("Штраф за просрочку: " + fine + " денежных единиц.");
        return true;
    }

    // Задача 23.
    public void ShowStatistics() {
        int total = GetBookCount();
        int available = GetAvailableBooks();
        int borrowed = total - available;
        System.out.println("Статистика библиотеки:");
        System.out.println("Всего книг: " + total);
        System.out.println("Доступно книг: " + available);
        System.out.println("Выдано книг: " + borrowed);
        System.out.println("Зарегистрировано читателей: " + readers.size());
        System.out.println("Статический счётчик добавленных книг: " + TotalBooks);
        System.out.println("Рекурсивный подсчёт книг: " + CountBooksRecursive());
    }

    // Задача 24.
    public void ShowMenu() {
        System.out.println("==============================");
        System.out.println("       УПРАВЛЕНИЕ БИБЛИОТЕКОЙ");
        System.out.println("==============================");
        System.out.println("1. Добавить книгу");
        System.out.println("2. Показать все книги");
        System.out.println("3. Найти книгу");
        System.out.println("4. Добавить читателя");
        System.out.println("5. Показать читателей");
        System.out.println("6. Выдать книгу");
        System.out.println("7. Вернуть книгу");
        System.out.println("8. Статистика библиотеки");
        System.out.println("0. Выход");
    }

    public void ShowReaders() {
        if (readers.isEmpty()) {
            System.out.println("Зарегистрированных читателей пока нет.");
            return;
        }
        System.out.println("Список читателей:");
        for (Reader reader : readers) reader.displayInfo();
    }
}

// Вспомогательный тип, чтобы вариант перегрузки «имя + телефон» был однозначным.
class Phone {
    final String value;
    Phone(String value) { this.value = value; }
}

// Режим поиска: Java не позволяет перегружать методы только по имени параметра.
enum SearchMode { TITLE, AUTHOR }
