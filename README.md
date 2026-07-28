# library-management-system
library management system that efficiently manages books, members and borrowing records 
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <iomanip>

class Book {
private:
    int id;
    std::string title;
    std::string author;
    bool isIssued;

public:
    Book(int bookId, std::string bookTitle, std::string bookAuthor) 
        : id(bookId), title(bookTitle), author(bookAuthor), isIssued(false) {}

    int getId() const { return id; }
    std::string getTitle() const { return title; }
    std::string getAuthor() const { return author; }
    bool getIssuedStatus() const { return isIssued; }

    void issueBook() { isIssued = true; }
    void returnBook() { isIssued = false; }

    void display() const {
        std::cout << std::left << std::setw(10) << id 
                  << std::setw(30) << title 
                  << std::setw(25) << author 
                  << (isIssued ? "Issued" : "Available") << "\n";
    }
};

class Library {
private:
    std::vector<Book> books;

public:
    void addBook() {
        int id;
        std::string title, author;

        std::cout << "\nEnter Book ID (Integer): ";
        while (!(std::cin >> id)) {
            std::cout << "Invalid input. Enter a numerical ID: ";
            std::cin.clear();
            std::cin.ignore(123, '\n');
        }
        std::cin.ignore();

        for (const auto& book : books) {
            if (book.getId() == id) {
                std::cout << "Error: A book with ID " << id << " already exists.\n";
                return;
            }
        }

        std::cout << "Enter Book Title: ";
        std::getline(std::cin, title);
        std::cout << "Enter Author Name: ";
        std::getline(std::cin, author);

        books.emplace_back(id, title, author);
        std::cout << "Book added successfully!\n";
    }

    void viewBooks() const {
        if (books.empty()) {
            std::cout << "\nThe library inventory is empty.\n";
            return;
        }

        std::cout << "\n" << std::string(75, '-') << "\n";
        std::cout << std::left << std::setw(10) << "ID" 
                  << std::setw(30) << "Title" 
                  << std::setw(25) << "Author" 
                  << "Status\n";
        std::cout << std::string(75, '-') << "\n";

        for (const auto& book : books) {
            book.display();
        }
        std::cout << std::string(75, '-') << "\n";
    }

    void searchBook() const {
        if (books.empty()) {
            std::cout << "\nNo books available to search.\n";
            return;
        }

        int choice;
        std::cout << "\nSearch by:\n1. Book ID\n2. Title\nEnter choice: ";
        std::cin >> choice;
        std::cin.ignore();

        if (choice == 1) {
            int id;
            std::cout << "Enter Book ID to search: ";
            std::cin >> id;
            
            auto it = std::find_if(books.begin(), books.end(), [id](const Book& b) { return b.getId() == id; });
            if (it != books.end()) {
                std::cout << "\nBook Found:\n";
                it->display();
            } else {
                std::cout << "Book not found.\n";
            }
        } else if (choice == 2) {
            std::string title;
            std::cout << "Enter Book Title to search: ";
            std::getline(std::cin, title);

            bool found = false;
            for (const auto& book : books) {
                if (book.getTitle() == title) {
                    if (!found) std::cout << "\nMatches found:\n";
                    book.display();
                    found = true;
                }
            }
            if (!found) std::cout << "No matching books found.\n";
        } else {
            std::cout << "Invalid search selection.\n";
        }
    }

    void issueBook() {
        int id;
        std::cout << "\nEnter Book ID to issue: ";
        std::cin >> id;

        for (auto& book : books) {
            if (book.getId() == id) {
                if (book.getIssuedStatus()) {
                    std::cout << "This book is already checked out.\n";
                } else {
                    book.issueBook();
                    std::cout << "Book issued successfully!\n";
                }
                return;
            }
        }
        std::cout << "Book ID not found.\n";
    }

    void returnBook() {
        int id;
        std::cout << "\nEnter Book ID to return: ";
        std::cin >> id;

        for (auto& book : books) {
            if (book.getId() == id) {
                if (!book.getIssuedStatus()) {
                    std::cout << "This book is already inside the library.\n";
                } else {
                    book.returnBook();
                    std::cout << "Book returned successfully!\n";
                }
                return;
            }
        }
        std::cout << "Book ID not found.\n";
    }

    void deleteBook() {
        int id;
        std::cout << "\nEnter Book ID to remove: ";
        std::cin >> id;

        auto it = std::remove_if(books.begin(), books.end(), [id](const Book& b) { return b.getId() == id; });
        
        if (it != books.end()) {
            books.erase(it, books.end());
            std::cout << "Book removed from inventory.\n";
        } else {
            std::cout << "Book ID not found.\n";
        }
    }
};

int main() {
    Library systemLibrary;
    int userChoice;

    while (true) {
        std::cout << "\n===== LIBRARY MANAGEMENT SYSTEM =====\n"
                  << "1. Add New Book\n"
                  << "2. View All Books\n"
                  << "3. Search For a Book\n"
                  << "4. Issue a Book\n"
                  << "5. Return a Book\n"
                  << "6. Delete a Book\n"
                  << "7. Exit Application\n"
                  << "=====================================\n"
                  << "Select an option (1-7): ";
        
        if (!(std::cin >> userChoice)) {
            std::cout << "Please select a valid menu operation.\n";
            std::cin.clear();
            std::cin.ignore(123, '\n');
            continue;
        }

        switch (userChoice) {
            case 1: systemLibrary.addBook(); break;
            case 2: systemLibrary.viewBooks(); break;
            case 3: systemLibrary.searchBook(); break;
            case 4: systemLibrary.issueBook(); break;
            case 5: systemLibrary.returnBook(); break;
            case 6: systemLibrary.deleteBook(); break;
            case 7: std::cout << "\nClosing Application. Goodbye!\n"; return 0;
            default: std::cout << "Selection out of bounds. Try again.\n";
        }
    }
    return 0;
}
