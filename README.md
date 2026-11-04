#include <iostream>
#include <string>
#include <algorithm>
#include <cctype>
#include <sstream>
using namespace std;

// Patient Node
struct Patient {
    int id, age;
    string name, disease, doctor, admissionDate;
    Patient* next;
};

Patient* head = NULL;

// Convert string to lowercase
string toLowerCase(string s) {
    transform(s.begin(), s.end(), s.begin(),
        [](unsigned char c) {
            return tolower(c);
        });
    return s;
}

// Safe integer input
int readInt(string message, int minValue, int maxValue) {
    int value;
    string input;

    while (true) {
        cout << message;
        getline(cin, input);

        stringstream ss(input);
        char extra;

        if (ss >> value && !(ss >> extra) &&
            value >= minValue && value <= maxValue) {
            return value;
        }

        cout << "Invalid input! Try again.\n";
    }
}

// Safe text input
string readText(string message) {
    string value;

    do {
        cout << message;
        getline(cin, value);

        if (value.empty())
            cout << "Input cannot be empty!\n";

    } while (value.empty());

    return value;
}

// Find patient by ID
Patient* findByID(int id) {
    Patient* temp = head;

    while (temp != NULL) {
        if (temp->id == id)
            return temp;

        temp = temp->next;
    }

    return NULL;
}

// Display one patient
void displayOne(Patient* p) {
    cout << "\nPatient ID: " << p->id;
    cout << "\nName: " << p->name;
    cout << "\nAge: " << p->age;
    cout << "\nDisease: " << p->disease;
    cout << "\nDoctor: " << p->doctor;
    cout << "\nAdmission Date: " << p->admissionDate;
    cout << "\n-----------------------------\n";
}

// 1. Patient Registration Module
void addPatient() {
    Patient* newPatient = new Patient();

    newPatient->id = readInt(
        "Enter Patient ID: ", 1, 1000000);

    if (findByID(newPatient->id) != NULL) {
        cout << "Patient ID already exists!\n";
        delete newPatient;
        return;
    }

    newPatient->name = readText("Enter Patient Name: ");

    newPatient->age = readInt(
        "Enter Age: ", 1, 120);

    newPatient->disease = readText("Enter Disease: ");

    newPatient->doctor = readText("Enter Doctor Name: ");

    newPatient->admissionDate =
        readText("Enter Admission Date: ");

    newPatient->next = NULL;

    // Insert node at end
    if (head == NULL) {
        head = newPatient;
    } else {
        Patient* temp = head;

        while (temp->next != NULL)
            temp = temp->next;

        temp->next = newPatient;
    }

    cout << "\nPatient Registered Successfully!\n";
}

// 2. Admitted Patient Report
void displayPatients() {
    if (head == NULL) {
        cout << "\nNo Patient Records Found!\n";
        return;
    }

    Patient* temp = head;
    int count = 0;

    cout << "\n===== ADMITTED PATIENT REPORT =====\n";

    while (temp != NULL) {
        displayOne(temp);
        count++;
        temp = temp->next;
    }

    cout << "\nTotal Admitted Patients: " << count << "\n";
}

// 3. Update Patient Module
void updatePatient() {
    int id = readInt("Enter Patient ID to Update: ",
                     1, 1000000);

    Patient* temp = findByID(id);

    if (temp == NULL) {
        cout << "\nPatient Not Found!\n";
        return;
    }

    cout << "\nEnter Updated Details:\n";

    temp->name = readText("New Name: ");
    temp->age = readInt("New Age: ", 1, 120);
    temp->disease = readText("New Disease: ");
    temp->doctor = readText("New Doctor: ");
    temp->admissionDate = readText("New Admission Date: ");

    cout << "\nPatient Updated Successfully!\n";
}

// 4. Discharge Patient Module
void dischargePatient() {
    int id = readInt("Enter Patient ID to Discharge: ",
                     1, 1000000);

    Patient* temp = head;
    Patient* prev = NULL;

    while (temp != NULL && temp->id != id) {
        prev = temp;
        temp = temp->next;
    }

    if (temp == NULL) {
        cout << "\nPatient Not Found!\n";
        return;
    }

    if (prev == NULL)
        head = temp->next;
    else
        prev->next = temp->next;

    delete temp;

    cout << "\nPatient Discharged Successfully!\n";
}

// 5. Search Module
void searchPatient() {
    cout << "\n1. Search by ID";
    cout << "\n2. Search by Name";
    cout << "\n3. Search by Disease\n";

    int choice = readInt("Enter Choice: ", 1, 3);

    Patient* temp = head;
    bool found = false;

    if (choice == 1) {
        int id = readInt("Enter Patient ID: ",
                         1, 1000000);

        temp = findByID(id);

        if (temp != NULL) {
            displayOne(temp);
            found = true;
        }
    }
    else {
        string key;

        if (choice == 2)
            key = readText("Enter Patient Name: ");
        else
            key = readText("Enter Disease: ");

        key = toLowerCase(key);

        while (temp != NULL) {
            string value;

            if (choice == 2)
                value = temp->name;
            else
                value = temp->disease;

            if (toLowerCase(value).find(key) != string::npos) {
                displayOne(temp);
                found = true;
            }

            temp = temp->next;
        }
    }

    if (!found)
        cout << "\nPatient Not Found!\n";
}

// 6. Sorting Module
void sortPatients() {
    if (head == NULL) {
        cout << "\nNo Records to Sort!\n";
        return;
    }

    cout << "\n1. Sort by ID";
    cout << "\n2. Sort by Name";
    cout << "\n3. Sort by Age\n";

    int choice = readInt("Enter Choice: ", 1, 3);

    for (Patient* i = head; i != NULL; i = i->next) {
        for (Patient* j = i->next; j != NULL; j = j->next) {

            bool shouldSwap = false;

            if (choice == 1 && i->id > j->id)
                shouldSwap = true;

            else if (choice == 2 &&
                     toLowerCase(i->name) >
                     toLowerCase(j->name))
                shouldSwap = true;

            else if (choice == 3 && i->age > j->age)
                shouldSwap = true;

            if (shouldSwap) {
                swap(i->id, j->id);
                swap(i->age, j->age);
                swap(i->name, j->name);
                swap(i->disease, j->disease);
                swap(i->doctor, j->doctor);
                swap(i->admissionDate, j->admissionDate);
            }
        }
    }

    cout << "\nPatients Sorted Successfully!\n";
    displayPatients();
}

// 7. Doctor-wise Report
void doctorReport() {
    if (head == NULL) {
        cout << "\nNo Patient Records Found!\n";
        return;
    }

    string doctor = toLowerCase(
        readText("Enter Doctor Name: "));

    Patient* temp = head;
    int count = 0;

    cout << "\n===== DOCTOR-WISE REPORT =====\n";

    while (temp != NULL) {
        if (toLowerCase(temp->doctor) == doctor) {
            displayOne(temp);
            count++;
        }

        temp = temp->next;
    }

    cout << "\nTotal Patients Under This Doctor: "
         << count << "\n";
}

// 8. Total Patient Count
void countPatients() {
    Patient* temp = head;
    int count = 0;

    while (temp != NULL) {
        count++;
        temp = temp->next;
    }

    cout << "\nTotal Registered Patients: " << count << "\n";
}

// 9. Reverse Linked List
void reversePatients() {
    if (head == NULL || head->next == NULL) {
        cout << "\nList is empty or has only one patient!\n";
        return;
    }

    Patient* prev = NULL;
    Patient* current = head;
    Patient* nextNode = NULL;

    while (current != NULL) {
        nextNode = current->next;
        current->next = prev;
        prev = current;
        current = nextNode;
    }

    head = prev;

    cout << "\nPatient List Reversed Successfully!\n";
    displayPatients();
}

// 10. Oldest and Youngest Patient
void ageReport() {
    if (head == NULL) {
        cout << "\nNo Patient Records Found!\n";
        return;
    }

    Patient* oldest = head;
    Patient* youngest = head;
    Patient* temp = head;

    while (temp != NULL) {
        if (temp->age > oldest->age)
            oldest = temp;

        if (temp->age < youngest->age)
            youngest = temp;

        temp = temp->next;
    }

    cout << "\n===== AGE REPORT =====\n";

    cout << "\nOldest Patient:";
    displayOne(oldest);

    cout << "\nYoungest Patient:";
    displayOne(youngest);
}

// Free allocated memory
void freeMemory() {
    while (head != NULL) {
        Patient* temp = head;
        head = head->next;
        delete temp;
    }
}

// Main Function
int main() {
    int choice;

    do {
        cout << "\n\n===================================";
        cout << "\n   HOSPITAL PATIENT RECORD SYSTEM";
        cout << "\n===================================";

        cout << "\n1. Register Patient";
        cout << "\n2. Admitted Patient Report";
        cout << "\n3. Update Patient Details";
        cout << "\n4. Discharge Patient";
        cout << "\n5. Search Patient";
        cout << "\n6. Sort Patients";
        cout << "\n7. Doctor-wise Report";
        cout << "\n8. Total Patient Count";
        cout << "\n9. Reverse Patient List";
        cout << "\n10. Oldest and Youngest Patient";
        cout << "\n11. Exit";

        choice = readInt("\nEnter Your Choice: ", 1, 11);

        switch (choice) {
            case 1: addPatient(); break;
            case 2: displayPatients(); break;
            case 3: updatePatient(); break;
            case 4: dischargePatient(); break;
            case 5: searchPatient(); break;
            case 6: sortPatients(); break;
            case 7: doctorReport(); break;
            case 8: countPatients(); break;
            case 9: reversePatients(); break;
            case 10: ageReport(); break;

            case 11:
                cout << "\nThank You for Using Our System!\n";
                break;
        }

    } while (choice != 11);

    freeMemory();

    return 0;
}
