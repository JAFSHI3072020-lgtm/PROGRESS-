#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <time.h>

const int BAR_LENGTH = 30;   // Longer bar for better visuals
const int MAX_TASKS = 8;     // Fewer tasks for clarity

typedef struct {
    int id;
    int progress;
    int step;
    int done;
} Task;

void print_bar(Task task);
void clear_screen();
void print_stop_time();

int main() {
    Task tasks[MAX_TASKS];
    srand((unsigned)time(NULL));

    // Initialization
    for (int i = 0; i < MAX_TASKS; i++) {
        tasks[i].id = i + 1;
        tasks[i].progress = 0;
        tasks[i].step = rand() % 8 + 1; // random speed
        tasks[i].done = 0;
    }

    int tasks_incomplete = 1;
    while (tasks_incomplete) {
        tasks_incomplete = 0;
        clear_screen();
        printf("=== Multi-task Progress Simulation ===\n\n");

        for (int i = 0; i < MAX_TASKS; i++) {
            if (!tasks[i].done) {
                tasks[i].progress += tasks[i].step;
                if (tasks[i].progress >= 100) {
                    tasks[i].progress = 100;
                    tasks[i].done = 1;
                } else {
                    tasks_incomplete = 1;
                }
            }
            print_bar(tasks[i]);
        }

        // Sleep for 0.3 seconds (cross-platform)
#ifdef _WIN32
        Sleep(300); // Windows Sleep is in milliseconds
#else
        usleep(300000); // POSIX usleep is in microseconds
#endif
    }

    printf("\nAll tasks completed successfully!\n");
    print_stop_time(); // show completion time
    return 0;
}

void clear_screen() {
#ifdef _WIN32
    system("cls");
#else
    system("clear");
#endif
}

void print_bar(Task task) {
    int bars_to_show = (task.progress * BAR_LENGTH) / 100;
    printf("Task %2d: [", task.id);
    for (int i = 0; i < BAR_LENGTH; i++) {
        if (i < bars_to_show) {
            printf("█"); // solid block for progress
        } else {
            printf(" ");
        }
    }
    printf("] %3d%% %s\n", task.progress, task.done ? "✅ Done" : "⏳ Running");
}

void print_stop_time() {
    time_t now = time(NULL);
    struct tm *t = localtime(&now);
    char buffer[100];
    strftime(buffer, sizeof(buffer), "%A %d %B %Y %H:%M:%S", t);
    printf("Simulation finished at: %s\n", buffer);
}

