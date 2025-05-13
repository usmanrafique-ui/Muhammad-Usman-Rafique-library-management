# Muhammad-Usman-Rafique-library-management
#include <iostream>
#include <string>
#include <ctime>
using namespace std;

class book {
protected:
    int id;
    string title, author;
    bool issued;
    int issueday, issuemonth, issueyear;
public:
    book(int id, string title, string author, bool issued) {
        this->id = id;
        this->title = title;
        this->author = author;
        this->issued = issued;
        this->issueday = 0;
        this->issuemonth = 0;
        this->issueyear = 0;
    }

    int getid() {
        return id; 
    }
    string gettitle() { 
        return title;
    }
    string getauthor() { 
        return author; 
    }
    bool getissued() { 
        return issued; 
    }

    void returnbook() {
        issued = false;
    }

    void display() {
        cout << endl;
        cout << "ID: " << id <<endl<< "Title: " << title <<endl<< "Author: " << author << endl;
    }

     int timeofissue(int startday, int startmonth, int startyear, int endday, int endmonth, int endyear) {
        struct tm issuedon = { 0 }, returnon = { 0 };
        issuedon.tm_mday = startday;
        issuedon.tm_mon = startmonth - 1;
        issuedon.tm_year = startyear - 1900;

        returnon.tm_mday = endday;
        returnon.tm_mon = endmonth - 1;
        returnon.tm_year = endyear - 1900;

        time_t start = mktime(&issuedon);
        time_t end = mktime(&returnon);
        double seconds = difftime(end, start);
        
        return seconds / 86400;
    }

    int calculatefine(int returnday, int returnmonth, int returnyear) {
        const int issuetime = 7;
        int numdays = timeofissue(issueday, issuemonth, issueyear, returnday, returnmonth, returnyear);
        if (numdays > issuetime) {
            return (numdays - issuetime) * 10;
        }
        else {
            return 0;
        }
    }

    
    friend class commonuser;
    friend class Admin;
};


class user {
protected:
    string username;
public:
    user(string username) {
        this->username = username;
    }
    virtual void showmenu() = 0; 
};


class Admin : public user {
public:
    Admin(string username) : user(username) {}

    void showmenu() override {
        cout << "THIS IS AN ADMIN MENU ::" << endl;
        cout << "1. ADD BOOKS" << endl;
        cout << "2. VIEW ALL BOOKS" << endl;
        cout << "3. SEARCH FOR A SPECIFIC BOOK" << endl;
        cout << "4. VIEW ALL ISSUED BOOKS" << endl;
        cout << "5. MAKE THIS PAIN END --------------------" << endl;
    }

    void addBook(book* books[], int& bookCount, int id, const string title, const string author) {
        books[bookCount++] = new book(id, title, author, false);
        cout << "SUCCESSFULLY ADDED THE BOOK :: " << endl<<endl;
    }

    void viewAllBooks(book* books[], int bookCount) {
        cout << "ALL BOOKS IN THE LIBRARY CURRENTLY AVAILABLE :: " << endl << endl;
        for (int i = 0; i < bookCount; i++) {
            books[i]->display();
        }
    }

    void viewIssuedBooks(book* books[], int bookCount) {
        for (int i = 0; i < bookCount; i++) {
            if (books[i]->getissued()==true) {
                books[i]->display();
            }
        }
    }
};


class commonuser : public user {
public:
    commonuser(string username) : user(username) {}

    void showmenu() override {
        cout << "THIS IS THE USER MEN :: " << endl;
        cout << "1. VIEW BOOKS" << endl;
        cout << "2. SEARCH FOR A SPECIFIC BOOK" << endl;
        cout << "3. SEARCH FOR A SPECIFIC AUTHOR" << endl;
        cout << "4. ISSUE BOOK" << endl;
        cout << "5. RETURN BOOK" << endl;
        cout << "6. EXIT" << endl;
    }

    void issuebook(book* books[], int bookCount, int id, int day, int month, int year) {
        for (int i = 0; i < bookCount; i++) {
            if (books[i]->getid() == id && !books[i]->getissued()) {
                books[i]->issued = true;
                books[i]->issueday = day;
                books[i]->issuemonth = month;
                books[i]->issueyear = year;
                cout << "BOOK ISSUED SUCCESSFULLY :: " << endl;
                return;
            }
        }
        cout << "BOOK YOU ARE SEARCHING FOR IS NOT AVAILABLE (SKILL ISSUE)" << endl<<endl;
    }

    void returnbook(book* books[], int bookCount, int id, int returnDay, int returnMonth, int returnYear) {
        for (int i = 0; i < bookCount; i++) {
            if (books[i]->getid() == id && books[i]->getissued()==true) {
                int fine = books[i]->calculatefine(returnDay, returnMonth, returnYear);
                books[i]->returnbook();
                cout << "BOOK RETURNED SUCCESSFULLY :: " << endl<<endl;
                if (fine > 0) {
                    cout << "FINE OF " << fine << " TO BE ISSUED :: " << endl<<endl;
                }
                return;
            }
        }
        cout << "NO BOOK EXISTS WITH ID :: " << id << endl<<endl;
    }

    void searchbook(book* books[], int bookCount, string& tofind) {
        bool found = false;
        for (int i = 0; i < bookCount; i++) {
            if (books[i]->gettitle().find(tofind) != string::npos) {
                books[i]->display();
                found = true;
            }
        }
        if (!found) {
            cout << "NO SUCH BOOK EXISTS:: " << tofind << endl<<endl;
        }
    }

    void searchByAuthor(book* books[], int bookCount, const string& tofind) {
        bool found = false;
        for (int i = 0; i < bookCount; i++) {
            if (books[i]->getauthor().find(tofind) != string::npos) {
                books[i]->display();
                found = true;
            }
        }
        if (found==false) {
            cout << "NO SUCH AUTHOR EXISTS :: " << tofind << endl;
        }
    }
};



class Library {
private:
    book* books[100];
    int bookCount;
public:
    Library() {
        bookCount = 0;
    }

    book** getBooks() {
        return books;
    }

    int& getBookCount() {
        return bookCount;
    }
};


int main() {
    Library lib;
    book** books = lib.getBooks();
    int& bookCount = lib.getBookCount();
    cout << "WELCOME TO LIBRARY ";
    cout << endl << "---------------------------------------------------------------------------" << endl;
    while (true) {
      
        cout << "PRESS 1 IF YOU ARE A LIBRARIAN (NO LYING) " << endl;
        cout << "PRESS 2 FOR USER" << endl;
        cout << "PRESS 3 TO MAKE THIS PAIN GO AWAY " << endl;
        cout << "INPUT :: ";

        int input;
        cin >> input;
        cin.ignore(); 

        if (input == 3) {
            break; 
        }
        if (input == 1) {
            Admin* a1 = new Admin("admin");
            while (true) {
                a1->showmenu();
                cout << "ENTER CHOICE 0 TO CHANGE MOD :: ";
                int choice;
                cin >> choice;

                if (choice == 0) {
                    delete a1;
                    break; 
                }
                else if (choice == 1) {
                    int id;
                    string title, author;
                    cout << "BOOK ID ::  ";
                    cin >> id;
                    cin.ignore();
                    cout << "BOOK TITLE :: ";
                    getline(cin, title);
                    cout << "BOOK AUTHOR :: ";
                    getline(cin, author);
                    a1->addBook(books, bookCount, id, title, author);
                }
                else if (choice == 2) {
                    a1->viewAllBooks(books, bookCount);
                }
                else if (choice == 3) {
                    string searchTitle;
                    cin.ignore();
                    cout << "WHAT BOOK DO YOU WANT TO FIND :: ";
                    getline(cin, searchTitle);
                    commonuser dummy("temp");
                    dummy.searchbook(books, bookCount, searchTitle);
                }
                else if (choice == 4) {
                    a1->viewIssuedBooks(books, bookCount);
                }
                else if (choice == 5) {
                    delete a1;
                    break;
                }
                else {
                    cout << "WRONG CHOICE :: ";
                }
            }
        }
        
        else if (input == 2) {
            commonuser* u1 = new commonuser("user");
            while (true) {
                u1->showmenu();
                cout << "ENTER CHOICE PRESS 0 TO CHNGE THE MOD ::  ";
                int choice;
                cin >> choice;

                if (choice == 0) {
                    delete u1;
                    break; 
                }
                else if (choice == 1) {
                    for (int i = 0; i < bookCount; i++) {
                        books[i]->display();
                    }
                }
                else if (choice == 2) {
                    string searchTitle;
                    cin.ignore();
                    cout << "ENTER TITLE OF BOOK :: ";
                    getline(cin, searchTitle);
                    u1->searchbook(books, bookCount, searchTitle);
                }
                else if (choice == 3) {
                    string authorname;
                    cin.ignore();
                    cout << "ENTER AUTHOR NAME YOU WANT TO SEARCH :: ";
                    getline(cin, authorname);
                    u1->searchByAuthor(books, bookCount, authorname);
                }
                else if (choice == 4) {
                    int id, day, month, year;
                    cout << "ENTER BOOK ID ::  ";
                    cin >> id;
                    cout << "ENTER DATE (eg. DD MM YYYY)";
                    cin >> day >> month >> year;
                    u1->issuebook(books, bookCount, id, day, month, year);
                }
                else if (choice == 5) {
                    int id, day, month, year;
                    cout << "ENTER ID TO RETURN";
                    cin >> id;
                    cout << "ENTER DATE (eg. 05 08 2025) ";
                    cin >> day >> month >> year;
                    u1->returnbook(books, bookCount, id, day, month, year);
                }
                else if (choice == 6) {
                    delete u1;
                    break;
                }
                else {
                    cout << "WRONG CHOICE :: ";
                }
            }
        }
        else {
            cout << "WRONG CHOICE :: ";
        }
    }
    
   
    for (int i = 0; i < bookCount; ++i) {
        delete books[i];
    }

    return 0;
}
