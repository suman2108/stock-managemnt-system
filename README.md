#include <stdio.h>
#include <string.h>

struct item 
{
    char itemName[50];
    int quantity;
    float price;
    int sharesAvailable;
};

struct customer 
{
    char customerName[50];
    char address[100];
};

struct purchase 
{
    char itemName[50];
    int quantity;
    float totalPrice;
    char customerName[50];
};

void addItem(struct item *stock, int *n) 
{
    printf("Enter item name: ");
    fgets(stock[*n].itemName, 50, stdin);
    stock[*n].itemName[strcspn(stock[*n].itemName, "\n")] = 0; 
    printf("Enter quantity: ");
    scanf("%d", &stock[*n].quantity);
    printf("Enter price per share: ");
    scanf("%f", &stock[*n].price);
    printf("Enter shares available: ");
    scanf("%d", &stock[*n].sharesAvailable);
    getchar(); 
    (*n)++;
}

void displayItems(struct item *stock, int n)
{
    printf("\n%-20s%-10s%-10s%-10s\n", "Item Name", "Quantity", "Price", "Shares Available");
    
    for(int i = 0; i < n; i++) {
        printf("%-20s%-10d%-10.2f%-10d\n", stock[i].itemName, stock[i].quantity, stock[i].price, stock[i].sharesAvailable);
    }
}

void searchItem(struct item *stock, int n)
{
    char search[50];
    printf("Enter item name to search: ");
    fgets(search, 50, stdin);
    search[strcspn(search, "\n")] = 0; 
    
    printf("\n%-20s%-10s%-10s%-10s\n", "Item Name", "Quantity", "Price", "Shares Available");
    
    for(int i = 0; i < n; i++) {
        if(strcmp(stock[i].itemName, search) == 0) {
            printf("%-20s%-10d%-10.2f%-10d\n", stock[i].itemName, stock[i].quantity, stock[i].price, stock[i].sharesAvailable);
            return;
        }
    }
    
    printf("Item not found.\n");
}

void buyShares(struct item *stock, int n, struct customer *cust, struct purchase *purchaseHistory, int *numPurchases)
{
    char buyItem[50];
    int buyQuantity;
    
    printf("Enter item name to buy shares: ");
    fgets(buyItem, 50, stdin);
    buyItem[strcspn(buyItem, "\n")] = 0;
    
    for(int i = 0; i < n; i++) {
        if(strcmp(stock[i].itemName, buyItem) == 0) {
            printf("Enter the quantity of shares to buy: ");
            scanf("%d", &buyQuantity);
            
            if(buyQuantity <= stock[i].sharesAvailable) {
                printf("\nPurchase Details:\n");
                printf("%-20s%-10s%-15s%-20s\n", "Item Name", "Quantity", "Total Price", "Customer Name");
                printf("%-20s%-10d%-15.2f%-20s\n", stock[i].itemName, buyQuantity, stock[i].price * buyQuantity, cust->customerName);
                
                // Update stock details
                stock[i].quantity += buyQuantity;
                stock[i].sharesAvailable -= buyQuantity;
                
                // Add purchase to history
                strcpy(purchaseHistory[*numPurchases].itemName, stock[i].itemName);
                purchaseHistory[*numPurchases].quantity = buyQuantity;
                purchaseHistory[*numPurchases].totalPrice = stock[i].price * buyQuantity;
                strcpy(purchaseHistory[*numPurchases].customerName, cust->customerName);
                (*numPurchases)++;
                
                printf("\nSuccessfully bought %d shares of %s.\n", buyQuantity, stock[i].itemName);
            } else {
                printf("Not enough shares available for purchase.\n");
            }
            
            return;
        }
    }
    
    printf("Item not found.\n");
}

void getCustomerDetails(struct customer *cust)
{
    printf("Enter customer name: ");
    fgets(cust->customerName, 50, stdin);
    cust->customerName[strcspn(cust->customerName, "\n")] = 0;
    
    printf("Enter customer address: ");
    fgets(cust->address, 100, stdin);
    cust->address[strcspn(cust->address, "\n")] = 0;
}

void displayPurchaseHistory(struct purchase *purchaseHistory, int numPurchases)
{
    printf("\nPurchase History:\n");
    printf("%-20s%-10s%-15s%-20s\n", "Item Name", "Quantity", "Total Price", "Customer Name");
    
    for(int i = 0; i < numPurchases; i++) {
        printf("%-20s%-10d%-15.2f%-20s\n", purchaseHistory[i].itemName, 
               purchaseHistory[i].quantity, purchaseHistory[i].totalPrice, 
               purchaseHistory[i].customerName);
    }
}

int main() 
{
    struct item stock[50];
    struct customer cust;
    struct purchase purchaseHistory[50];
    int numPurchases = 0;
    int n = 0;
    
    while(1) {
        printf("\n1. Add Item\n2. Display Items\n3. Search Item\n4. Buy Shares\n5. Display Buyers\n6. Exit\n");
        printf("Enter your choice: ");
        int choice;
        scanf("%d", &choice);
        getchar(); 
        
        switch(choice) {
            case 1:
                addItem(stock, &n);
                break;
            case 2:
                displayItems(stock, n);
                break;
            case 3:
                searchItem(stock, n);
                break;
            case 4:
                getCustomerDetails(&cust);
                buyShares(stock, n, &cust, purchaseHistory, &numPurchases);
                break;
            case 5:
                displayPurchaseHistory(purchaseHistory, numPurchases);
                break;
            case 6:
                return 0;
            default:
                printf("Invalid choice.\n");
        }
    }
    
    return 0;
}
