//# linux-history-commands
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_COMMAND_LENGTH 1024  // Maximum length of each command

char** history;  // Array to store command history
int history_count = 0;  // Counter for the number of commands
int max_history;  // Maximum number of commands to store

void add_to_history(const char* command) {
    if (history_count < max_history) {
        history[history_count] = strdup(command);
        history_count++;
    } else {
        // If history is full, remove the oldest command and shift others
        free(history[0]);
        for (int i = 1; i < max_history; i++) {
            history[i - 1] = history[i];
        }
        history[max_history - 1] = strdup(command);
    }
}

void print_history() {
    for (int i = 0; i < history_count; i++) {
        printf("%d %s\n", i + 1, history[i]);
    }
}

int main(int argc, char* argv[]) {
    if (argc != 2) {
        fprintf(stderr, "Usage: %s <max_history>\n", argv[0]);
        return 1;
    }

    max_history = atoi(argv[1]);
    if (max_history <= 0) {
        fprintf(stderr, "Error: max_history must be a positive integer.\n");
        return 1;
    }

    history = (char**)malloc(max_history * sizeof(char*));
    if (history == NULL) {
        perror("Failed to allocate memory");
        return 1;
    }

    char command[MAX_COMMAND_LENGTH];

    while (1) {
        printf("Enter a command: ");
        if (fgets(command, MAX_COMMAND_LENGTH, stdin) == NULL) {
            break;  // Exit on EOF
        }

        // Remove the newline character from the command
        command[strcspn(command, "\n")] = 0;

        // Check for "history" command
        if (strcmp(command, "history") == 0) {
            print_history();
        } else {
            add_to_history(command);
            // Here, you can add code to execute the command if needed
        }
    }

    // Free allocated memory
    for (int i = 0; i < history_count; i++) {
        free(history[i]);
    }
    free(history);

    return 0;
}
