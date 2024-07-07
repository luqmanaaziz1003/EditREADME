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
    gh repo clone github/markup
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

'''
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
'''
