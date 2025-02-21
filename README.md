#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct Node {
    char logEntry[256];
    struct Node *next;
} Node;

void addNode(Node **head, const char *logEntry) {
    Node *newNode = (Node *)malloc(sizeof(Node));
    if (newNode == NULL) {
        perror("Memory allocation failed");
        exit(EXIT_FAILURE);
    }
    strcpy(newNode->logEntry, logEntry);
    newNode->next = *head;
    *head = newNode;
}

void printList(Node *head) {
    Node *current = head;
    while (current != NULL) {
        printf("%s", current->logEntry);
        current = current->next;
    }
}

void freeList(Node *head) {
    Node *current = head;
    Node *next;
    while (current != NULL) {
        next = current->next;
        free(current);
        current = next;
    }
}

int main() {
    FILE *logFile = fopen("/var/log/syslog", "r");
    if (logFile == NULL) {
        perror("Unable to open syslog file");
        return EXIT_FAILURE;
    }

    Node *head = NULL;
    char buffer[256];

    while (fgets(buffer, sizeof(buffer), logFile)) {
        addNode(&head, buffer);
    }

    fclose(logFile);
    printList(head);
    freeList(head);

    return EXIT_SUCCESS;
}
