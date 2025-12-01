#include <stdio.h>
#include <string.h>
// CONSTANT VALUES (MAX LIMITS FOR ARRAYS)
#define MAX_USERS 50
#define MAX_PRODUCTS 50
#define MAX_CART 20
// USER DATA STRUCTURE (Email + Password)
// ============================================
struct User {
    char email[50];
     char password[20];
};

 struct User users[MAX_USERS]; 
int userCount = 0;   // kitne users register hue hain
// PRODUCT DATA (ID + Name + Price)
// ============================================
struct Product {
    int id;
    char name[30];
    int price;};


struct Product products[MAX_PRODUCTS];
int productCount = 5;   // starting me 5 products rakhe
// CART DATA STRUCTURE (Product ID + Quantity)
// ============================================
struct Cart {
    int productId;
    int qty;};
struct Cart cart[MAX_CART];
int cartCount = 0;


// USERS ko file se load karna (program start me)
// ============================================
void loadUsers() {
    FILE *fp = fopen("users.txt", "r");

    // agar file exist nahi karti 
    if (!fp) return;

    // file me jitne users hai → sab ko add karo users[] me
    while (fscanf(fp, "%s %s",
                  users[userCount].email,
                  users[userCount].password) != EOF)
    {
        userCount++;
    }

    fclose(fp);
}


// SIGNUP → user email/password ko file me save hoga
// ============================================
void signup() {
    FILE *fp = fopen("users.txt", "a");  // append mode

    printf("\n--- SIGNUP ---\n");
    printf("Enter Email: ");
    scanf("%s", users[userCount].email);

    printf("Enter Password: ");
    scanf("%s", users[userCount].password);

    // file me likh do
    fprintf(fp, "%s %s\n",
            users[userCount].email,
            users[userCount].password);

    userCount++;

    fclose(fp);

    printf("Signup Successful! (Saved in file)\n");
}


// LOGIN 
// ============================================
int login() {
    char email[50], pass[20];

    printf("\n--- LOGIN ---\n");
    printf("Enter Email: ");
    scanf("%s", email);

    printf("Enter Password: ");
    scanf("%s", pass);

    // har saved user ko check karo
    for (int i = 0; i < userCount; i++) {
        if (strcmp(email, users[i].email) == 0 &&
            strcmp(pass, users[i].password) == 0)
        {
            printf("Login Successful!\n");
            return 1;
        }
    }

    printf("Invalid Email or Password!\n");
    return 0;
}
// DEFAULT PRODUCTS LOAD KARNA
// ============================================
void loadDefaultProducts() {
    products[0] = (struct Product){1, "Shirt", 499};
    products[1] = (struct Product){2, "Pant", 799};
    products[2] = (struct Product){3, "Copy", 40};
    products[3] = (struct Product){4, "Bedsheet", 999};
    products[4] = (struct Product){5, "Pen", 10};
}
// PRODUCTS LIST DIKHANA
// ============================================
void viewProducts() {
    printf("\n--- PRODUCT LIST ---\n");

    for (int i = 0; i < productCount; i++) {
        printf("%d. %s - Rs.%d\n",
               products[i].id,
               products[i].name,
               products[i].price);
    }
}
// CART ME ITEM ADD KARNA
// ============================================
void addToCart() {
    int id, qty;

    printf("Enter Product ID: ");
    scanf("%d", &id);

    printf("Enter Quantity: ");
    scanf("%d", &qty);

    cart[cartCount].productId = id;
    cart[cartCount].qty = qty;

    cartCount++;

    printf("Item Added to Cart!\n");
}
// CART MAI KITNA  ITEMS HAI  DIKHANA
// ============================================
void viewCart() {
    if (cartCount == 0) {
        printf("Cart is Empty!\n");
        return;
    }

    printf("\n--- CART ITEMS ---\n");

    for (int i = 0; i < cartCount; i++) {
        int pid = cart[i].productId;

        // product id match karke data dhoondo
        for (int j = 0; j < productCount; j++) {
            if (products[j].id == pid) {

                printf("%d. %s x %d = Rs.%d\n",
                       i + 1,
                       products[j].name,
                       cart[i].qty,
                       products[j].price * cart[i].qty);
            }
        }
    }
}


// CHECKOUT → BILL PRINT KAREGA
// ============================================
void checkout() {
    if (cartCount == 0) {
        printf("Cart is Empty!\n");
        return;
    }

    int total = 0;

    printf("\n--- FINAL BILL ---\n");

    for (int i = 0; i < cartCount; i++) {
        int pid = cart[i].productId;

        for (int j = 0; j < productCount; j++) {
            if (products[j].id == pid) {

                int cost = products[j].price * cart[i].qty;

                printf("%s x %d = Rs.%d\n",
                       products[j].name,
                       cart[i].qty,
                       cost);

                total += cost;
            }
        }
    }

    printf("\nTOTAL BILL = Rs.%d\n", total);

    cartCount = 0;  // cart ko empty kar do
    printf("Checkout Complete!\n");
}
// ADMIN PANEL → Add / Remove / Update Products
// ============================================
void adminPanel() {
    char pass[20];

    printf("Enter Admin Password: ");
    scanf("%s", pass);

    // admin password check
    if (strcmp(pass, "admin123") != 0) {
        printf("Incorrect Admin Password!\n");
        return;
    }

    int ch;

    while (1) {
        printf("\n===== ADMIN PANEL =====\n");
        printf("1. View Products\n");
        printf("2. Add Product\n");
        printf("3. Remove Product\n");
        printf("4. Update Product Price\n");
        printf("5. Exit Admin Panel\n");
        printf("Choose: ");
        scanf("%d", &ch);

        // 1. VIEW PRODUCTS
        if (ch == 1) {
            viewProducts();
        }

        // 2. ADD NEW PRODUCT
        else if (ch == 2) {
            int id, price;
            char name[30];

            printf("Enter New Product ID: ");
            scanf("%d", &id);

            printf("Enter Product Name: ");
            scanf("%s", name);

            printf("Enter Product Price: ");
            scanf("%d", &price);

            products[productCount].id = id;
            strcpy(products[productCount].name, name);
            products[productCount].price = price;

            productCount++;

            printf("Product Added Successfully!\n");
        }

        // 3. REMOVE PRODUCT
        else if (ch == 3) {
            int id, found = 0;

            printf("Enter Product ID to Remove: ");
            scanf("%d", &id);

            for (int i = 0; i < productCount; i++) {
                if (products[i].id == id) {

                    // shift products left
                    for (int j = i; j < productCount - 1; j++) {
                        products[j] = products[j + 1];
                    }

                    productCount--;
                    found = 1;
                    printf("Product Removed!\n");
                    break;
                }
            }

            if (!found)
                printf("Product Not Found!\n");
        }

        // 4. UPDATE PRODUCT PRICE
        else if (ch == 4) {
            int id, newPrice, found = 0;

            printf("Enter Product ID to Update: ");
            scanf("%d", &id);

            for (int i = 0; i < productCount; i++) {
                if (products[i].id == id) {

                    printf("Enter New Price: ");
                    scanf("%d", &newPrice);

                    products[i].price = newPrice;
                    found = 1;

                    printf("Product Updated Successfully!\n");
                    break;
                }
            }

            if (!found)
                printf("Product Not Found!\n");
        }

        // 5. EXIT ADMIN PANEL
        else if (ch == 5) {
            printf("Exiting Admin Panel...\n");
            break;
        }

        // WRONG INPUT
        else {
            printf("Invalid Option!\n");
        }
    }
}
// MAIN MENU → Signup / Login / Admin / Exit
// ============================================
int main() {
    loadUsers();           // file se users load karna
    loadDefaultProducts(); // 5 default products load

    int choice;

    while (1) {
        printf("\n===== MAIN MENU =====\n");
        printf("1. Signup\n");
        printf("2. Login\n");
        printf("3. Admin Login\n");
        printf("4. Exit\n");
        printf("Choose: ");
        scanf("%d", &choice);

        // 1. SIGNUP
        if (choice == 1)
            signup();

        // 2. USER LOGIN
        else if (choice == 2) {
            if (login()) {

                int opt;

                while (1) {
                    printf("\n--- USER MENU ---\n");
                    printf("1. View Products\n");
                    printf("2. Add to Cart\n");
                    printf("3. View Cart\n");
                    printf("4. Checkout\n");
                    printf("5. Logout\n");
                    printf("Choose: ");
                    scanf("%d", &opt);

                    if (opt == 1) viewProducts();
                    else if (opt == 2) addToCart();
                    else if (opt == 3) viewCart();
                    else if (opt == 4) checkout();
                    else if (opt == 5) break;
                }
            }
        }

        // 3. ADMIN LOGIN
        else if (choice == 3)
            adminPanel();

        // 4. EXIT
        else if (choice == 4)
            break;

        else
            printf("Invalid Choice!\n");
    }

    return 0;
}
