//Bank-Management-System
//A simple console-based bank management system written in C. It allows users to create accounts, deposit and withdraw money, and check account balances using file-based storage.
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define ACCOUNTFILE "account.dat"

// Structure to store account details
typedef struct {
 char name[50];
 int accno;
 float balance;
} account;

// Function declarations
void createAccount();
void deposit();
void withdraw();
void checkBalance();

int main() {
 int option;

 while (1) {
 printf("\n**** Bank Management System ****\n");
 printf("1. Create Account\n");
 printf("2. Deposit Money\n");
 printf("3. Withdraw Money\n");
 printf("4. Check Balance\n");
 printf("5. Exit\n");
 printf("\nChoose an option: ");
 scanf("%d", &option);

 switch (option) {
 case 1:
 createAccount();
 break;
 case 2:
 deposit();
 break;
 case 3:
 withdraw();
 break;
 case 4:
 checkBalance();
 break;
 case 5:
 printf("\nClosing the Bank. Thanks for your visit.\n");
 return 0;
 default:
 printf("\nInvalid Choice! Please enter a valid option.\n");
 break;
 }
 }
}

// Function to create a new account
void createAccount() {
 account acc;
 FILE *file = fopen(ACCOUNTFILE, "ab+");
 if (file == NULL) {
 printf("\nError: Unable to open file!\n");
 return;
 }

 while (getchar() != '\n'); // Clear input buffer

 printf("\nEnter your name: ");
 fgets(acc.name, sizeof(acc.name), stdin);
 acc.name[strcspn(acc.name, "\n")] = '\0'; // Remove newline character

 printf("Enter your account number: ");
 scanf("%d", &acc.accno);

 // Initialize balance
 acc.balance = 0.00;

 // Write to file
 fwrite(&acc, sizeof(acc), 1, file);
 fclose(file);
 
 printf("\nAccount created successfully!\n");
}

// Function to deposit money
void deposit() {
 FILE *file = fopen(ACCOUNTFILE, "rb+");
 if (file == NULL) {
 printf("\nError: Unable to open account file!\n");
 return;
 }

 int accno;
 float money;
 account acct;
 int found = 0;

 printf("\nEnter your account number: ");
 scanf("%d", &accno);

 printf("Enter amount to deposit: ");
 scanf("%f", &money);

 while (fread(&acct, sizeof(acct), 1, file)) {
 if (acct.accno == accno) {
 acct.balance += money;
 fseek(file, -sizeof(acct), SEEK_CUR);
 fwrite(&acct, sizeof(acct), 1, file);
 fclose(file);

 printf("\nDeposit successful! New balance: Rs. %.2f\n", acct.balance);
 found = 1;
 break;
 }
 }

 if (!found) {
 printf("\nError: Account number %d not found!\n", accno);
 }

 fclose(file);
}

// Function to withdraw money
void withdraw() {
 FILE *file = fopen(ACCOUNTFILE, "rb+");
 if (file == NULL) {
 printf("\nError: Unable to open account file!\n");
 return;
 }

 int accno;
 float money;
 account acct;
 int found = 0;

 printf("\nEnter your account number: ");
 scanf("%d", &accno);

 printf("Enter amount to withdraw: ");
 scanf("%f", &money);

 while (fread(&acct, sizeof(acct), 1, file)) {
 if (acct.accno == accno) {
 if (acct.balance >= money) {
 acct.balance -= money;
 fseek(file, -sizeof(acct), SEEK_CUR);
 fwrite(&acct, sizeof(acct), 1, file);
 fclose(file);

 printf("\nWithdrawal successful! Remaining balance: Rs. %.2f\n", acct.balance);
 found = 1;
 } else {
 printf("\nError: Insufficient balance!\n");
 fclose(file);
 }
 return;
 }
 }

 if (!found) {
 printf("\nError: Account number %d not found!\n", accno);
 }

 fclose(file);
}

// Function to check account balance
void checkBalance() {
 FILE *file = fopen(ACCOUNTFILE, "rb");
 if (file == NULL) {
 printf("\nError: Unable to open file!\n");
 return;
 }

 int accno;
 account acct;
 int found = 0;

 printf("\nEnter your account number: ");
 scanf("%d", &accno);

 while (fread(&acct, sizeof(acct), 1, file)) {
 if (acct.accno == accno) {
 printf("\nYour account balance is Rs. %.2f\n", acct.balance);
 found = 1;
 break;
 }
 }

 if (!found) {
 printf("\nError: Account number %d not found!\n", accno);
 }

 fclose(file);
}
