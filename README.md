#include <iostream>
#include <fstream>
#include <string>
#include <vector>
#include <iomanip>

using namespace std;

// Structure to store Student details
struct Student {
    int rollNumber;
    string name;
    int age;
    string course;
};

// Function prototypes
void addStudent();
void displayAllStudents();
void searchStudent();
void updateStudent();
void deleteStudent();

const string FILE_NAME = "students.txt";

int main() {
    int choice;

    while (true) {
        cout << "\n=========================================\n";
        cout << "       STUDENT MANAGEMENT SYSTEM         \n";
        cout << "=========================================\n";
        cout << "1. Add Student Record\n";
        cout << "2. Display All Student Records\n";
        cout << "3. Search Student Record\n";
        cout << "4. Update Student Record\n";
        cout << "5. Delete Student Record\n";
        cout << "6. Exit\n";
        cout << "-----------------------------------------\n";
        cout << "Enter your choice (1-6): ";
        cin >> choice;

        if (cin.fail()) {
            cin.clear();
            cin.ignore(10000, '\n');
            cout << "Invalid input. Please enter a number between 1 and 6.\n";
            continue;
        }

        switch (choice) {
            case 1:
                addStudent();
                break;
            case 2:
                displayAllStudents();
                break;
            case 3:
                searchStudent();
                break;
            case 4:
                updateStudent();
                break;
            case 5:
                deleteStudent();
                break;
            case 6:
                cout << "\nExiting System. Good luck!\n";
                return 0;
            default:
                cout << "Invalid choice! Please try again.\n";
        }
    }

    return 0;
}

// 1. Add Student Record to File
void addStudent() {
    Student s;
    ofstream outFile(FILE_NAME, ios::app);

    if (!outFile) {
        cout << "Error opening file for writing!\n";
        return;
    }

    cout << "\n--- Add New Student ---\n";
    cout << "Enter Roll Number: ";
    cin >> s.rollNumber;
    cin.ignore();

    cout << "Enter Full Name: ";
    getline(cin, s.name);

    cout << "Enter Age: ";
    cin >> s.age;
    cin.ignore();

    cout << "Enter Course: ";
    getline(cin, s.course);

    // Save record delimited by '|'
    outFile << s.rollNumber << "|" << s.name << "|" << s.age << "|" << s.course << "\n";
    outFile.close();

    cout << "Student record added successfully!\n";
}

// Helper function to read all records from file
vector<Student> readAllStudents() {
    vector<Student> students;
    ifstream inFile(FILE_NAME);

    if (!inFile) {
        return students; // Returns empty vector if file doesn't exist
    }

    string line;
    while (getline(inFile, line)) {
        if (line.empty()) continue;

        Student s;
        size_t pos1 = line.find('|');
        size_t pos2 = line.find('|', pos1 + 1);
        size_t pos3 = line.find('|', pos2 + 1);

        if (pos1 != string::npos && pos2 != string::npos && pos3 != string::npos) {
            s.rollNumber = stoi(line.substr(0, pos1));
            s.name = line.substr(pos1 + 1, pos2 - pos1 - 1);
            s.age = stoi(line.substr(pos2 + 1, pos3 - pos2 - 1));
            s.course = line.substr(pos3 + 1);
            students.push_back(s);
        }
    }

    inFile.close();
    return students;
}

// Helper function to write vector back to file
void saveAllStudents(const vector<Student>& students) {
    ofstream outFile(FILE_NAME, ios::trunc);

    for (const auto& s : students) {
        outFile << s.rollNumber << "|" << s.name << "|" << s.age << "|" << s.course << "\n";
    }

    outFile.close();
}

// 2. Display All Student Records
void displayAllStudents() {
    vector<Student> students = readAllStudents();

    if (students.empty()) {
        cout << "\nNo records found!\n";
        return;
    }

    cout << "\n---------------------------------------------------------------\n";
    cout << left << setw(12) << "Roll No"
         << setw(25) << "Name"
         << setw(8)  << "Age"
         << setw(15) << "Course" << "\n";
    cout << "---------------------------------------------------------------\n";

    for (const auto& s : students) {
        cout << left << setw(12) << s.rollNumber
             << setw(25) << s.name
             << setw(8)  << s.age
             << setw(15) << s.course << "\n";
    }
    cout << "---------------------------------------------------------------\n";
}

// Search Student Record
void searchStudent() {
    int rollNo;
    cout << "\nEnter Roll Number to search: ";
    cin >> rollNo;

    vector<Student> students = readAllStudents();
    bool found = false;

    for (const auto& s : students) {
        if (s.rollNumber == rollNo) {
            cout << "\nRecord Found:\n";
            cout << "Roll Number : " << s.rollNumber << "\n";
            cout << "Name        : " << s.name << "\n";
            cout << "Age         : " << s.age << "\n";
            cout << "Course      : " << s.course << "\n";
            found = true;
            break;
        }
    }

    if (!found) {
        cout << "Student with Roll Number " << rollNo << " not found.\n";
    }
}

// 3. Update Student Record
void updateStudent() {
    int rollNo;
    cout << "\nEnter Roll Number to update: ";
    cin >> rollNo;

    vector<Student> students = readAllStudents();
    bool found = false;

    for (auto& s : students) {
        if (s.rollNumber == rollNo) {
            found = true;
            cout << "\nEnter New Details for " << s.name << ":\n";
            cin.ignore();

            cout << "Enter New Name: ";
            getline(cin, s.name);

            cout << "Enter New Age: ";
            cin >> s.age;
            cin.ignore();

            cout << "Enter New Course: ";
            getline(cin, s.course);

            break;
        }
    }

    if (found) {
        saveAllStudents(students);
        cout << "Record updated successfully!\n";
    } else {
        cout << "Student with Roll Number " << rollNo << " not found.\n";
    }
}

// 4. Delete Student Record
void deleteStudent() {
    int rollNo;
    cout << "\nEnter Roll Number to delete: ";
    cin >> rollNo;

    vector<Student> students = readAllStudents();
    vector<Student> updatedList;
    bool found = false;

    for (const auto& s : students) {
        if (s.rollNumber == rollNo) {
            found = true;
        } else {
            updatedList.push_back(s);
        }
    }

    if (found) {
        saveAllStudents(updatedList);
        cout << "Record deleted successfully!\n";
    } else {
        cout << "Student with Roll Number " << rollNo << " not found.\n";
    }
}

