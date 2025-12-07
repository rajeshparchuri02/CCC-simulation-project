#include <stdio.h>
#include <stdlib.h>

#define INIT_CAPACITY 4

int* array = NULL;
int capacity = 0;
int size = 0;

void initArray() {
    capacity = INIT_CAPACITY;
    array = (int*)malloc(capacity * sizeof(int));
    printf("Hotel initialized: %d rooms (capacity)\n\n", capacity);
}

void add(int data) {
    if (size == capacity) {
        // Realloc: Double capacity & copy (like vector resize!)
        capacity *= 2;
        int* newArray = (int*)realloc(array, capacity * sizeof(int));
        if (newArray == NULL) {
            printf("MEMORY EXHAUSTED!\n\n");
            return;
        }
        array = newArray;
        printf("RESIZED! Now %d rooms (old:%d)\n", capacity, capacity/2);
    }
    
    array[size] = data;
    size++;
    printf("Room %d filled: %d (addr: %p)\n", size-1, data, (void*)&array[size-1]);
}

void removeLast() {
    if (size == 0) {
        printf(" HOTEL EMPTY!\n\n");
        return;
    }
    size--;
    printf(" Room %d emptied (was: %d)\n", size, array[size]);
    
    // Optional: Shrink if too empty (real arrays rarely do this)
    if (size > 0 && size < capacity/4) {
        capacity /= 2;
        if (capacity < INIT_CAPACITY) capacity = INIT_CAPACITY;
        int* newArray = (int*)realloc(array, capacity * sizeof(int));
        if (newArray) array = newArray;
        printf("SHRUNK to %d rooms\n", capacity);
    }
}

void display() {
    printf("\n=== HOTEL MEMORY VIEW ===\n");
    printf("Rooms used: %d/%d | Base addr: %p\n", size, capacity, (void*)array);
    printf("┌─────┬──────┬──────────────┐\n");
    printf("│Room │Data  │Memory Addr   │\n");
    printf("├─────┼──────┼──────────────┤\n");
    
    for (int i = 0; i < capacity; i++) {
        if (i < size) {
            printf("│ %2d │ %3d │ %10p  │\n", i, array[i], (void*)&array[i]);
        } else {
            printf("│ %2d │ ---- │ %10p  │\n", i, (void*)&array[i]);
        }
    }
    printf("└─────┴──────┴──────────────┘\n\n");
}

void freeArray() {
    free(array);
    printf("All rooms cleaned up!\n");
}

int main() {
    int choice, data;
    initArray();
    
    printf("DYNAMIC ARRAY HOTEL SIMULATOR\n");
    printf("Watch contiguous memory grow/shrink live!\n\n");
    
    while (1) {
        printf("1. Check-in (add)  2. Check-out (remove)  3. Show Hotel  4. Quit\n➤ ");
        scanf("%d", &choice);
        if (choice == 4) break;
        
        switch (choice) {
            case 1: 
                printf("Guest number? "); scanf("%d", &data); 
                add(data); 
                display(); 
                break;
            case 2: 
                removeLast(); 
                display(); 
                break;
            case 3: 
                display(); 
                break;
            default: printf("Pick 1-4!\n\n");
        }
    }
    
    freeArray();
    return 0;
}

