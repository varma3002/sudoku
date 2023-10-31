# sudoku
 Implement a Sudoku Solver
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Define a structure to represent a contact
struct Contact {
    char name[100];
    char phone[20];
    char email[100];
};

// Function to add a new contact
void addContact(struct Contact *contacts, int *contactCount) {
    if (*contactCount < 100) {
        printf("Enter contact details:\n");
        printf("Name: ");
        scanf(" %[^\n]", contacts[*contactCount].name);
        printf("Phone: ");
        scanf(" %[^\n]", contacts[*contactCount].phone);
        printf("Email: ");
        scanf(" %[^\n]", contacts[*contactCount].email);
        (*contactCount)++;
        printf("Contact added successfully!\n");
    } else {
        printf("Contact list is full. Delete some contacts to make space.\n");
    }
}

// Function to display the contact list
void viewContacts(struct Contact *contacts, int contactCount) {
    printf("Contact List:\n");
    for (int i = 0; i < contactCount; i++) {
        printf("Contact %d:\n", i + 1);
        printf("Name: %s\n", contacts[i].name);
        printf("Phone: %s\n", contacts[i].phone);
        printf("Email: %s\n", contacts[i].email);
        printf("\n");
    }
}

// Function to edit a contact
void editContact(struct Contact *contacts, int contactCount) {
    int contactIndex;
    printf("Enter the index of the contact you want to edit: ");
    scanf("%d", &contactIndex);
    if (contactIndex >= 1 && contactIndex <= contactCount) {
        printf("Enter new contact details:\n");
        printf("Name: ");
        scanf(" %[^\n]", contacts[contactIndex - 1].name);
        printf("Phone: ");
        scanf(" %[^\n]", contacts[contactIndex - 1].phone);
        printf("Email: ");
        scanf(" %[^\n]", contacts[contactIndex - 1].email);
        printf("Contact edited successfully!\n");
    } else {
        printf("Invalid contact index.\n");
    }
}

// Function to delete a contact
void deleteContact(struct Contact *contacts, int *contactCount) {
    int contactIndex;
    printf("Enter the index of the contact you want to delete: ");
    scanf("%d", &contactIndex);
    if (contactIndex >= 1 && contactIndex <= *contactCount) {
        for (int i = contactIndex - 1; i < *contactCount - 1; i++) {
            contacts[i] = contacts[i + 1];
        }
        (*contactCount)--;
        printf("Contact deleted successfully!\n");
    } else {
        printf("Invalid contact index.\n");
    }
}

// Function to save contacts to a file
void saveContactsToFile(struct Contact *contacts, int contactCount) {
    FILE *file = fopen("contacts.txt", "w");
    if (file == NULL) {
        printf("Error opening the file for writing.\n");
        return;
    }
    
    for (int i = 0; i < contactCount; i++) {
        fprintf(file, "%s,%s,%s\n", contacts[i].name, contacts[i].phone, contacts[i].email);
    }
    
    fclose(file);
    printf("Contacts saved to 'contacts.txt'.\n");
}

// Function to load contacts from a file
void loadContactsFromFile(struct Contact *contacts, int *contactCount) {
    FILE *file = fopen("contacts.txt", "r");
    if (file == NULL) {
        printf("No contacts found. 'contacts.txt' does not exist.\n");
        return;
    }
    
    *contactCount = 0;
    while (*contactCount < 100 && fscanf(file, "%99[^,],%19[^,],%99[^\n]\n", 
        contacts[*contactCount].name, contacts[*contactCount].phone, contacts[*contactCount].email) == 3) {
        (*contactCount)++;
    }
    
    fclose(file);
    printf("Contacts loaded from 'contacts.txt'.\n");
}

int main() {
    struct Contact contacts[100];
    int contactCount = 0;
    int choice;

    loadContactsFromFile(contacts, &contactCount);

    while (1) {
        printf("\nContact Management System\n");
        printf("1. Add a new contact\n");
        printf("2. View contact list\n");
        printf("3. Edit a contact\n");
        printf("4. Delete a contact\n");
        printf("5. Save contacts to a file\n");
        printf("6. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                addContact(contacts, &contactCount);
                break;
            case 2:
                viewContacts(contacts, contactCount);
                break;
            case 3:
                editContact(contacts, contactCount);
                break;
            case 4:
                deleteContact(contacts, &contactCount);
                break;
            case 5:
                saveContactsToFile(contacts, contactCount);
                break;
            case 6:
                exit(0);
            default:
                printf("Invalid choice. Please try again.\n");
        }
    }

    return 0;
}


#include <stdio.h>

#define N 9

// Function to print the Sudoku grid
void printSudoku(int grid[N][N]) {
    for (int i = 0; i < N; i++) {
        for (int j = 0; j < N; j++) {
            printf("%d ", grid[i][j]);
        }
        printf("\n");
    }
}

// Function to check if a number can be placed in a given cell
int isSafe(int grid[N][N], int row, int col, int num) {
    // Check if the number is already in the row or column
    for (int i = 0; i < N; i++) {
        if (grid[row][i] == num || grid[i][col] == num) {
            return 0;
        }
    }

    // Check if the number is already in the 3x3 grid
    int startRow = row - row % 3;
    int startCol = col - col % 3;

    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            if (grid[i + startRow][j + startCol] == num) {
                return 0;
            }
        }
    }

    return 1;
}

// Function to solve the Sudoku puzzle using backtracking
int solveSudoku(int grid[N][N]) {
    int row, col;

    // Find an empty cell
    int isEmpty = 1;
    for (row = 0; row < N && isEmpty; row++) {
        for (col = 0; col < N; col++) {
            if (grid[row][col] == 0) {
                isEmpty = 0;
                break;
            }
        }
    }

    // If no empty cell is found, the puzzle is solved
    if (isEmpty) {
        return 1;
    }

    // Try placing numbers from 1 to 9 in the cell
    for (int num = 1; num <= 9; num++) {
        if (isSafe(grid, row, col, num)) {
            grid[row][col] = num;

            if (solveSudoku(grid)) {
                return 1; // If a solution is found, return true
            }

            grid[row][col] = 0; // If the current placement doesn't lead to a solution, reset the cell
        }
    }

    return 0; // No solution exists for this puzzle
}

int main() {
    int grid[N][N] = {
        {5, 3, 0, 0, 7, 0, 0, 0, 0},
        {6, 0, 0, 1, 9, 5, 0, 0, 0},
        {0, 9, 8, 0, 0, 0, 0, 6, 0},
        {8, 0, 0, 0, 6, 0, 0, 0, 3},
        {4, 0, 0, 8, 0, 3, 0, 0, 1},
        {7, 0, 0, 0, 2, 0, 0, 0, 6},
        {0, 6, 0, 0, 0, 0, 2, 8, 0},
        {0, 0, 0, 4, 1, 9, 0, 0, 5},
        {0, 0, 0, 0, 8, 0, 0, 7, 9}
    };

    if (solveSudoku(grid)) {
        printf("Solved Sudoku:\n");
        printSudoku(grid);
    } else {
        printf("No solution exists for the given Sudoku puzzle.\n");
    }

    return 0;
}`
