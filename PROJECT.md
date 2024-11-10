# TRAKING-PRODUCT-MOVEMENT-USING-DATA-STRUCTURE-IN-C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

// Define structure for product
struct Product {
    char name[50];
    char product_id[20];
    int quantity;
    char user_id[20];
    char ordered_date[11]; // Format: YYYY-MM-DD
    struct Product* next; // Pointer to the next product node
};

// Define structure for warehouse
struct Warehouse {
    char name[20];
    char location[50];
};
// Function prototypes
void add_product(struct Product** head);
void print_product_details(struct Product* product);
void track_product_movement(struct Product* head, char* product_id, struct Warehouse warehouses[]);
void free_products(struct Product* head);

// Main function
int main() {
    // Define warehouse locations
    struct Warehouse warehouses[4] = {
        {"Warehouse A", "Location A"},
        {"Warehouse B", "Location B"},
        {"Warehouse C", "Location C"},
        {"Warehouse D", "Location D"}
    };

    // Initialize product linked list
    struct Product* head = NULL;

    int choice;

    do {
		printf("=== TRACKING PRODUCT MOVEMENT ===");
        printf("\n1. Add Product\n");
        printf("2. Track Product Movement\n");
        printf("3. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);
        switch (choice) {
            case 1:
                add_product(&head);
                break;
            case 2: {
                char product_id[20];
                printf("Enter product ID to track: ");
                scanf("%s", product_id);
                track_product_movement(head, product_id, warehouses);
                break;
            }
            case 3:
                printf("Exiting program.\n");
                free_products(head); // Free memory allocated for products
                break;
            default:
                printf("Invalid choice. Please try again.\n");
        }
    } while (choice != 3);

    return 0;
}

// Function to add a product to the linked list
void add_product(struct Product** head) {
    struct Product* new_product = (struct Product*)malloc(sizeof(struct Product));
    if (new_product == NULL) {
        printf("Memory allocation failed.\n");
        return;
    }

    printf("Enter product name: ");
    scanf("%s", new_product->name);
    printf("Enter product ID: ");
    scanf("%s", new_product->product_id);
    printf("Enter quantity: ");
    scanf("%d", &new_product->quantity);
    printf("Enter user ID: ");
    scanf("%s", new_product->user_id);
    printf("Enter ordered date (YYYY-MM-DD): ");
    scanf("%s", new_product->ordered_date);

    new_product->next = *head;
    *head = new_product;
}

// Function to print product details
void print_product_details(struct Product* product) {
    printf("\nProduct Details:\n");
    printf("Product Name: %s\n", product->name);
    printf("Product ID: %s\n", product->product_id);
    printf("Quantity: %d\n", product->quantity);
    printf("User ID: %s\n", product->user_id);
    printf("Ordered Date: %s\n", product->ordered_date);
}

// Function to track product movement
void track_product_movement(struct Product* head, char* product_id, struct Warehouse warehouses[]) {
    struct Product* current = head;
    while (current != NULL) {
        if (strcmp(current->product_id, product_id) == 0) {
            // Product found, track its movement
            time_t now = time(NULL);
            struct tm* tm_now = localtime(&now);
            print_product_details(current);
            struct tm ordered_tm;
            sscanf(current->ordered_date, "%d-%d-%d", &ordered_tm.tm_year, &ordered_tm.tm_mon, &ordered_tm.tm_mday);
            ordered_tm.tm_year -= 1900;
            ordered_tm.tm_mon--;
            ordered_tm.tm_hour = 0;
            ordered_tm.tm_min = 0;
            ordered_tm.tm_sec = 0;
            time_t ordered_time = mktime(&ordered_tm);
            double seconds = difftime(now, ordered_time);
            int days = seconds / (60 * 60 * 24);
            if (days < 0) {
                printf("Product is still in transit.\n");
            } else if (days == 0) {
                printf("Product is in %s .\n", warehouses[0].name);
            } else if (days == 1) {
                printf("Product is in %s .\n", warehouses[1].name);
            } else if (days == 2) {
                printf("Product is in %s .\n", warehouses[2].name);
            } else if (days == 3) {
                printf("Product is in %s .\n", warehouses[3].name);
            } else if (days == 4) {
                printf("Your order will be shipped today. Expected delivery tomorrow.\n");
            } else {
                printf("Product has been delivered.\n");
            }
            if (days >= 0 && days <= 3) {
                printf("Expected Delivery Date: %d-%02d-%02d ", tm_now->tm_year + 1900, tm_now->tm_mon + 1, tm_now->tm_mday + days);
                printf("Working Hours: 8:00 AM to 5:00 PM\n");
            }
            return;
        }
        current = current->next;
    }
    printf("Product with ID '%s' not found.\n", product_id);
}

// Function to free memory allocated for product linked list
void free_products(struct Product* head) {
    struct Product* current = head;
    while (current != NULL) {
        struct Product* temp = current;
        current = current->next;
        free(temp);
    }
}
