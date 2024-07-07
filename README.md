# Expression Tree Management System

This project implements an expression tree management system in C. It allows users to create, display, update, and evaluate expression trees. The expression trees can contain arithmetic operations (`+`, `-`, `*`, `/`) and numeric values.

## Table of Contents

1. [Description](#description)
2. [Features](#features)
3. [Installation](#installation)
4. [Usage](#usage)

## Description

This project allows users to create and manage expression trees interactively. The main functionalities include:
- Inserting new expression trees
- Displaying all stored expression trees
- Searching for specific nodes within the trees
- Updating node values
- Removing nodes
- Evaluating the results of the expression trees

## Features

- **Interactive Tree Creation**: Build expression trees by specifying values and operations.
- **Insertion and Deletion**: Add or remove nodes from the trees.
- **Search and Update**: Find specific nodes and update their values.
- **Evaluation**: Calculate the result of the expression trees.

## Installation

1. **Clone the repository:**
    ```sh
    https://github.com/yourusername/expression-tree-management.git](https://github.com/luqmanaaziz1003/EditREADME/blob/main/README.md)
    ```
2. **Navigate to the project directory:**
    ```sh
    cd expression-tree-management
    ```
3. **Compile the code:**
    ```sh
    gcc -o expr_tree main.c
    ```

## Usage

1. **Run the compiled program:**
    ```sh
    ./expr_tree
    ```
2. **Follow the interactive prompts to manage expression trees:**
    - Insert new expression trees
    - Display all expression trees
    - Search, update, and remove nodes
    - Evaluate the result of the expression trees

### Menu Options:
1. Insert record
2. Remove record
3. Update record
4. Search record
5. Display records
6. Build and evaluate expression tree
7. Exit
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct Node {
    char value[12];
    struct Node* left;
    struct Node* right;
} Node;

typedef struct Record {
    Node* treeNode;
    struct Record* next;
} Record;

typedef struct {
    Record* head;
} RecordList;

void initList(RecordList* list);
Node* createTreeNode(const char* value);
Record* createRecord(Node* treeNode);
void insertRecord(RecordList* list, Node* treeNode);
void displayRecords(RecordList* list);
Record* searchRecord(RecordList* list, const char* value);
void removeRecord(RecordList* list, const char* value);
void updateRecord(Node* root, const char* oldValue, const char* newValue);
void freeRecords(RecordList* list);
void printBoldTitle(const char* title);
Node* buildExpressionTreeInteractive();
double evaluate(Node* root);
void freeTree(Node* root);
void handleInsertRecord(RecordList* list);
void handleRemoveRecord(RecordList* list);
void handleUpdateRecord(RecordList* list);
void handleSearchRecord(RecordList* list);
void handleBuildAndEvaluateExpressionTree(RecordList* list);
void displayTree(Node* root);
void removeNode(Node** root, const char* value);
int searchTree(Node* root, const char* value);

void initList(RecordList* list) {
    list->head = NULL;
}

Node* createTreeNode(const char* value) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    strcpy(newNode->value, value);
    newNode->left = newNode->right = NULL;
    return newNode;
}

Record* createRecord(Node* treeNode) {
    Record* newRecord = (Record*)malloc(sizeof(Record));
    newRecord->treeNode = treeNode;
    newRecord->next = NULL;
    return newRecord;
}

void insertRecord(RecordList* list, Node* treeNode) {
    Record* newRecord = createRecord(treeNode);
    newRecord->next = list->head;
    list->head = newRecord;
}

void displayRecords(RecordList* list) {
    Record* current = list->head;
    while (current != NULL) {
        printf("Expression Tree:\n");
        displayTree(current->treeNode);
        printf("\n");
        current = current->next;
    }
}

void displayTree(Node* root) {
    if (root != NULL) {
        displayTree(root->left);
        printf("%s ", root->value);
        displayTree(root->right);
    }
}

Record* searchRecord(RecordList* list, const char* value) {
    Record* current = list->head;
    while (current != NULL) {
        if (searchTree(current->treeNode, value)) {
            return current;
        }
        current = current->next;
    }
    return NULL;
}

int searchTree(Node* root, const char* value) {
    if (root == NULL) {
        return 0;
    }
    if (strcmp(root->value, value) == 0) {
        return 1;
    }
    return searchTree(root->left, value) || searchTree(root->right, value);
}

void removeNode(Node** root, const char* value) {
    if (*root == NULL) return;

    if (strcmp((*root)->value, value) == 0) {
        Node* temp = *root;
        *root = (*root)->right ? (*root)->right : (*root)->left;
        free(temp);
    } else {
        removeNode(&((*root)->left), value);
        removeNode(&((*root)->right), value);
    }
}

void removeRecord(RecordList* list, const char* value) {
    Record* current = list->head;
    Record* prev = NULL;
    while (current != NULL) {
        removeNode(&(current->treeNode), value);
        current = current->next;
    }
}

void updateRecord(Node* root, const char* oldValue, const char* newValue) {
    if (root != NULL) {
        if (strcmp(root->value, oldValue) == 0) {
            strcpy(root->value, newValue);
        }
        updateRecord(root->left, oldValue, newValue);
        updateRecord(root->right, oldValue, newValue);
    }
}

void freeRecords(RecordList* list) {
    Record* current = list->head;
    while (current != NULL) {
        Record* temp = current;
        current = current->next;
        freeTree(temp->treeNode);
        free(temp);
    }
    list->head = NULL;
}

void printBoldTitle(const char* title) {
    printf("\n\n");
    printf("%s\n", title);
    for (int i = 0; title[i] != '\0'; i++) {
        printf("-");
    }
    printf("\n");
}

Node* buildExpressionTreeInteractive() {
    char nodeType;
    printf("\nEnter 'a' for leaf (value):\n");
    printf("Enter 'b' for nonleaf (operation):\n");
    printf("Enter your choice: ");
    scanf(" %c", &nodeType);

    if (nodeType == 'a') {
        int value;
        printf("Enter value for leaf: ");
        scanf("%d", &value);

        char valueStr[12];
        snprintf(valueStr, sizeof(valueStr), "%d", value);

        return createTreeNode(valueStr);
    } else if (nodeType == 'b') {
        char operation;
        printf("Choose an operation (+, -, * or /): ");
        scanf(" %c", &operation);

        char opStr[2] = {operation, '\0'};
        Node* operatorNode = createTreeNode(opStr);

        printf("Enter left child\n");
        operatorNode->left = buildExpressionTreeInteractive();

        printf("Enter right child\n");
        operatorNode->right = buildExpressionTreeInteractive();

        return operatorNode;
    }

    return NULL;
}

double evaluate(Node* root) {
    if (root->left == NULL && root->right == NULL) {
        return atof(root->value);
    }
    double leftVal = evaluate(root->left);
    double rightVal = evaluate(root->right);
    switch (root->value[0]) {
        case '+': return leftVal + rightVal;
        case '-': return leftVal - rightVal;
        case '*': return leftVal * rightVal;
        case '/': return leftVal / rightVal;
        default: return 0;
    }
}

void freeTree(Node* root) {
    if (root != NULL) {
        freeTree(root->left);
        freeTree(root->right);
        free(root);
    }
}

void handleInsertRecord(RecordList* list) {
    printf("Insert a new expression tree:\n");
    Node* newTree = buildExpressionTreeInteractive();
    insertRecord(list, newTree);
}

void handleRemoveRecord(RecordList* list) {
    char value[12];
    printf("Enter the value of the node to remove: ");
    scanf("%s", value);
    removeRecord(list, value);
}

void handleUpdateRecord(RecordList* list) {
    char oldValue[12], newValue[12];
    printf("Enter the value of the node to update: ");
    scanf("%s", oldValue);
    printf("Enter the new value: ");
    scanf("%s", newValue);

    Record* current = list->head;
    while (current != NULL) {
        updateRecord(current->treeNode, oldValue, newValue);
        current = current->next;
    }
}

void handleSearchRecord(RecordList* list) {
    char value[12];
    printf("Enter the value of the node to search: ");
    scanf("%s", value);
    Record* result = searchRecord(list, value);
    if (result != NULL) {
        printf("Found record with value: %s\n", value);
    } else {
        printf("Record with value %s not found.\n", value);
    }
}

void handleBuildAndEvaluateExpressionTree(RecordList* list) {
    double result = 0.0;
    Record* current = list->head;
    while (current != NULL) {
        result += evaluate(current->treeNode);
        current = current->next;
    }
    printf("Result of your expression trees: %.2f\n", result);
}

int main() {
    RecordList list;
    initList(&list);

    int choice;
    do {
        printBoldTitle("Expression Tree");
        printf("\nMenu:\n");
        printf("1. Insert record\n");
        printf("2. Remove record\n");
        printf("3. Update record\n");
        printf("4. Search record\n");
        printf("5. Display records\n");
        printf("6. Build and evaluate expression tree\n");
        printf("7. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                handleInsertRecord(&list);
                break;
            case 2:
                handleRemoveRecord(&list);
                break;
            case 3:
                handleUpdateRecord(&list);
                break;
            case 4:
                handleSearchRecord(&list);
                break;
            case 5:
                displayRecords(&list);
                break;
            case 6:
                handleBuildAndEvaluateExpressionTree(&list);
                break;
            case 7:
                break;
            default:
                printf("Invalid choice. Please try again.\n");
        }
    } while (choice != 7);

    freeRecords(&list);
    return 0;
}
