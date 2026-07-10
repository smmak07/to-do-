#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_TASKS 100
#define TASK_LENGTH 100
#define FILE_NAME "todo.txt"

typedef struct {
    char description[TASK_LENGTH];
    int is_completed;
} Task;

Task tasks[MAX_TASKS];
int task_count = 0;

// Function declarations
void load_tasks();
void save_tasks();
void add_task();
void view_tasks();
void mark_completed();
void delete_task();

int main() {
    int choice;
    load_tasks();

    while (1) {
        printf("\n--- 📝 C To-Do List Application ---\n");
        printf("1. Add Task\n");
        printf("2. View Tasks\n");
        printf("3. Mark Task as Completed\n");
        printf("4. Delete Task\n");
        printf("5. Save & Exit\n");
        printf("Enter your choice (1-5): ");
        
        if (scanf("%d", &choice) != 1) {
            printf("Invalid input. Exiting.\n");
            break;
        }
        getchar(); // Clear newline buffer

        switch (choice) {
            case 1: add_task(); break;
            case 2: view_tasks(); break;
            case 3: mark_completed(); break;
            case 4: delete_task(); break;
            case 5: 
                save_tasks(); 
                printf("Tasks saved safely. Goodbye!\n"); 
                exit(0);
            default: printf("Invalid choice! Please try again.\n");
        }
    }
    return 0;
}

void add_task() {
    if (task_count >= MAX_TASKS) {
        printf("To-Do list is full!\n");
        return;
    }
    printf("Enter the task description: ");
    fgets(tasks[task_count].description, TASK_LENGTH, stdin);
    
    // Remove trailing newline character
    tasks[task_count].description[strcspn(tasks[task_count].description, "\n")] = 0;
    tasks[task_count].is_completed = 0;
    
    task_count++;
    printf("Task added successfully!\n");
}

void view_tasks() {
    if (task_count == 0) {
        printf("\nYour To-Do list is empty!\n");
        return;
    }
    printf("\n--- Current Tasks ---\n");
    for (int i = 0; i < task_count; i++) {
        printf("%d. [%s] %s\n", i + 1, tasks[i].is_completed ? "✔" : " ", tasks[i].description);
    }
}

void mark_completed() {
    view_tasks();
    if (task_count == 0) return;

    int index;
    printf("Enter task number to mark as completed: ");
    scanf("%d", &index);

    if (index < 1 || index > task_count) {
        printf("Invalid task number!\n");
        return;
    }
    tasks[index - 1].is_completed = 1;
    printf("Task marked as completed!\n");
}

void delete_task() {
    view_tasks();
    if (task_count == 0) return;

    int index;
    printf("Enter task number to delete: ");
    scanf("%d", &index);

    if (index < 1 || index > task_count) {
        printf("Invalid task number!\n");
        return;
    }

    for (int i = index - 1; i < task_count - 1; i++) {
        tasks[i] = tasks[i + 1];
    }
    task_count--;
    printf("Task deleted successfully!\n");
}

void load_tasks() {
    FILE *file = fopen(FILE_NAME, "r");
    if (file == NULL) return; // No previous file exists

    while (fscanf(file, "%d|[^\n]\n", &tasks[task_count].is_completed) != EOF) {
        fgets(tasks[task_count].description, TASK_LENGTH, file);
        tasks[task_count].description[strcspn(tasks[task_count].description, "\n")] = 0;
        task_count++;
    }
    fclose(file);
}

void save_tasks() {
    FILE *file = fopen(FILE_NAME, "w");
    if (file == NULL) {
        printf("Error saving tasks to file!\n");
        return;
    }
    for (int i = 0; i < task_count; i++) {
        fprintf(file, "%d\n%s\n", tasks[i].is_completed, tasks[i].description);
    }
    fclose(file);
}
