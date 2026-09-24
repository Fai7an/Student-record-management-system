[main.cpp](https://github.com/user-attachments/files/32587567/main.cpp)

// Student Record Management System
// A simple console-based CRUD application in C++
// Demonstrates: structs, file I/O (fstream), menu-driven logic, search & delete

#include <iostream>
#include <fstream>
#include <sstream>
#include <vector>
#include <string>
#include <iomanip>
#include <limits>

using namespace std;

const string DATA_FILE = "students.txt";

// ---------- Data Structure ----------
struct Student {
    int id;
    string name;
    float gpa;
};

// ---------- File Helpers ----------

// Save the entire vector of students to file (overwrites file)
void saveToFile(const vector<Student>& students) {
    ofstream outFile(DATA_FILE);
    if (!outFile) {
        cerr << "Error: could not open file for writing.\n";
        return;
    }
    for (const auto& s : students) {
        // Using '|' as a delimiter since names may contain spaces
        outFile << s.id << '|' << s.name << '|' << s.gpa << '\n';
    }
    outFile.close();
}

// Load all students from file into a vector
vector<Student> loadFromFile() {
    vector<Student> students;
    ifstream inFile(DATA_FILE);
    if (!inFile) {
        // File doesn't exist yet — that's fine on first run
        return students;
    }

    string line;
    while (getline(inFile, line)) {
        if (line.empty()) continue;
        stringstream ss(line);
        string idStr, name, gpaStr;

        getline(ss, idStr, '|');
        getline(ss, name, '|');
        getline(ss, gpaStr, '|');

        try {
            Student s;
            s.id = stoi(idStr);
            s.name = name;
            s.gpa = stof(gpaStr);
            students.push_back(s);
        } catch (...) {
            // Skip malformed lines rather than crashing
            continue;
        }
    }
    inFile.close();
    return students;
}

// ---------- Utility ----------

void clearInputBuffer() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int getIntInput(const string& prompt) {
    int value;
    while (true) {
        cout << prompt;
        cin >> value;
        if (cin.fail()) {
            clearInputBuffer();
            cout << "Invalid input. Please enter a number.\n";
        } else {
            clearInputBuffer();
            return value;
        }
    }
}
[README.pdf](https://github.com/user-attachments/files/32587573/README.pdf)

float getFloatInput(const string& prompt) {
    float value;
    while (true) {
        cout << prompt;
        cin >> value;
        if (cin.fail()) {
            clearInputBuffer();
            cout << "Invalid input. Please enter a number.\n";
        } else {
            clearInputBuffer();
            return value;
        }
    }
}

void printStudent(const Student& s) {
    cout << left << setw(8) << s.id
         << setw(25) << s.name
         << fixed << setprecision(2) << s.gpa << '\n';
}

void printHeader() {
    cout << left << setw(8) << "ID"
         << setw(25) << "Name"
         << "GPA\n";
    cout << string(40, '-') << '\n';
}

// ---------- Core Operations ----------

void addStudent(vector<Student>& students) {
    Student s;
    s.id = getIntInput("Enter student ID: ");

    // Prevent duplicate IDs
    for (const auto& existing : students) {
        if (existing.id == s.id) {
            cout << "A student with ID " << s.id << " already exists.\n";
            return;
        }
    }

    cout << "Enter student name: ";
    getline(cin, s.name);

    s.gpa = getFloatInput("Enter GPA: ");

    students.push_back(s);
    saveToFile(students);
    cout << "Student added successfully.\n";
}

void viewAllStudents(const vector<Student>& students) {
    if (students.empty()) {
        cout << "No student records found.\n";
        return;
    }
    printHeader();
    for (const auto& s : students) {
        printStudent(s);
    }
}

void searchStudent(const vector<Student>& students) {
    int id = getIntInput("Enter student ID to search: ");
    for (const auto& s : students) {
        if (s.id == id) {
            printHeader();
            printStudent(s);
            return;
        }
    }
    cout << "No student found with ID " << id << ".\n";
}

void updateStudent(vector<Student>& students) {
    int id = getIntInput("Enter student ID to update: ");
    for (auto& s : students) {
        if (s.id == id) {
            cout << "Enter new name (current: " << s.name << "): ";
            getline(cin, s.name);
            s.gpa = getFloatInput("Enter new GPA: ");
            saveToFile(students);
            cout << "Student updated successfully.\n";
            return;
        }
    }
    cout << "No student found with ID " << id << ".\n";
}

void deleteStudent(vector<Student>& students) {
    int id = getIntInput("Enter student ID to delete: ");
    for (size_t i = 0; i < students.size(); i++) {
        if (students[i].id == id) {
            students.erase(students.begin() + i);
            saveToFile(students);
            cout << "Student deleted successfully.\n";
            return;
        }
    }
    cout << "No student found with ID " << id << ".\n";
}

// ---------- Menu ----------

void showMenu() {
    cout << "\n===== Student Record Management System =====\n";
    cout << "1. Add Student\n";
    cout << "2. View All Students\n";
    cout << "3. Search Student\n";
    cout << "4. Update Student\n";
    cout << "5. Delete Student\n";
    cout << "6. Exit\n";
    cout << "==============================================\n";
}

int main() {
    vector<Student> students = loadFromFile();

    while (true) {
        showMenu();
        int choice = getIntInput("Enter your choice: ");

        switch (choice) {
            case 1:
                addStudent(students);
                break;
            case 2:
                viewAllStudents(students);
                break;
            case 3:
                searchStudent(students);
                break;
            case 4:
                updateStudent(students);
                break;
            case 5:
                deleteStudent(students);
                break;
            case 6:
                cout << "Goodbye!\n";
                return 0;
            default:
                cout << "Invalid choice. Please select 1-6.\n";
        }
    }

    return 0;
}
