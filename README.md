# Bank-accounts
Bank account code
#include <iostream>
#include <vector>
#include <fstream>
#include <iomanip>
#include <string>

using namespace std;

// Name Class
class Name {
private:
string first_name;
string last_name;

public:
    // Constructor
Name(const string& first, const string& last) : first_name(first), last_name(last) {}

    // Accessors
string getFirstName() const { return first_name; }
string getLastName() const { return last_name; }
};

// Depositor Class
class Depositor {
private:
Name name;
string ssn;

public:
    // Constructor
Depositor(const string& first, const string& last, const string& ssn) : name(first, last), ssn(ssn) {}

    // Accessors
Name getName() const { return name; }
string getSSN() const { return ssn; }

    // Mutator
void setSSN(const string& new_ssn) { ssn = new_ssn; }
};

// BankAccount Class
class BankAccount {
private:
int account_number;
Depositor depositor;
double balance;

public:
    // Constructor
BankAccount(int acc_num, const string& first, const string& last, const string& ssn, double initial_balance)
: account_number(acc_num), depositor(first, last, ssn), balance(initial_balance) {}

    // Accessors
int getAccountNumber() const { return account_number; }
Depositor getDepositor() const { return depositor; }
double getBalance() const { return balance; }

    // Mutators
void deposit(double amount) { 
if (amount > 0) {
balance += amount;
} else {
cout << "\033[1;31mInvalid deposit amount. Please enter a positive value.\033[0m" << endl;
}
}

void withdraw(double amount) {
if (amount > 0 && amount <= balance) {
balance -= amount;
} else if (amount <= 0) {
cout << "\033[1;31mInvalid withdrawal amount. Please enter a positive value.\033[0m" << endl;
} else {
cout << "\033[1;31mInsufficient funds.\033[0m" << endl;
}
}
};

// Function to read accounts from a file
int read_accts(vector<BankAccount>& accounts, ifstream& infile) {
int num_accounts;
infile >> num_accounts;

for (int i = 0; i < num_accounts; i++) {
int acc_num;
string first, last, ssn;
double balance;

infile >> acc_num >> first >> last >> ssn >> balance;
accounts.push_back(BankAccount(acc_num, first, last, ssn, balance));
}

return num_accounts;
}

// Function to find an account by account number
int findacct(const vector<BankAccount>& accounts, int requested_account) {
for (int i = 0; i < accounts.size(); i++) {
if (accounts[i].getAccountNumber() == requested_account) {
return i;
}
}
return -1; // Account not found
}

// Function to print all account information
void print_accts(const vector<BankAccount>& accounts, ofstream& outfile) {
outfile << "Account Number" << setw(15) << "Balance" << setw(15) << "Name" << setw(15) << "SSN" << endl;
outfile << "--------------------------------------------------------------------" << endl;

for (const BankAccount& account : accounts) {
outfile << setw(12) << account.getAccountNumber() << setw(15) << fixed << setprecision(2) << account.getBalance() 
<< setw(15) << account.getDepositor().getName().getFirstName() + " " + account.getDepositor().getName().getLastName()
<< setw(15) << account.getDepositor().getSSN() << endl;
}
}

// Function to display the menu
void menu() {
cout << "\033[1;39mSelect one of the following:\033[0m" << endl;
cout << "\033[1;34mW – Withdrawal\033[0m" << endl;
cout << "\033[1;32mD – Deposit\033[0m" << endl;
cout << "\033[1;32mN - New account\033[0m" << endl;
cout << "\033[1;32mB – Balance\033[0m" << endl;
cout << "\033[1;34mI - Account Info\033[0m" << endl;
cout << "\033[1;39mQ – Quit\033[0m" << endl;
cout << "\033[1;31mX – Delete Account\033[0m" << endl;
}

// Function to handle withdrawal
void withdrawal(vector<BankAccount>& accounts) {
int acc_num;
cout << "\033[1;34mEnter account number: \033[0m";
cin >> acc_num;

int index = findacct(accounts, acc_num);
if (index != -1) {
double amount;
cout << "\033[1;34mEnter amount to withdraw: \033[0m";
cin >> amount;

accounts[index].withdraw(amount);
} else {
cout << "\033[1;31mAccount not found.\033[0m" << endl;
}
}

// Function to handle deposit
void deposit(vector<BankAccount>& accounts) {
int acc_num;
cout << "\033[1;34mEnter account number: \033[0m";
cin >> acc_num;

int index = findacct(accounts, acc_num);
if (index != -1) {
double amount;
cout << "\033[1;34mEnter amount to deposit: \033[0m";
cin >> amount;

accounts[index].deposit(amount);
} else {
cout << "\033[1;31mAccount not found.\033[0m" << endl;
}
}

// Function to create a new account
int new_acct(vector<BankAccount>& accounts) {
static int next_account_number = 1000; 
  
string first, last, ssn;
double initial_balance;
cout << "\033[1;34mEnter first name:\033[0m";
cin >> first; // Use cin to read the first name
cout << "\033[1;34mEnter last name:\033[0m";
cin >> last; // Use cin to read the last name
cout << "\033[1;34mEnter SSN:\033[0m";
cin >> ssn;  // Use cin to read the SSN
cout << "\033[1;34mEnter initial deposit:\033[0m";
cin >> initial_balance; // You can use cin for the initial deposit
  
    // Generate unique account number
int account_number = next_account_number++;
    // Check if the account number is already in use
if (findacct(accounts, account_number) == -1) {
accounts.push_back(BankAccount(account_number, first, last, ssn, initial_balance));
cout << "\033[1;32mNew account created. Your account number is: \033[0m" << account_number << endl;
return accounts.size();
} else {
cout << "\033[1;31mAccount number already exists. Please try a different number.\033[0m" << endl;
return accounts.size();
}
}

// Function to delete an account
int delete_acct(vector<BankAccount>& accounts) {
int acc_num;
cout << "\033[1;34mEnter account number to delete: \033[0m";
cin >> acc_num;

int index = findacct(accounts, acc_num);
if (index != -1) {
if (accounts[index].getBalance() == 0) {
accounts.erase(accounts.begin() + index);
return accounts.size(); // Return the new number of accounts
} else {
cout << "\033[1;31mCannot delete account with a non-zero balance.\033[0m" << endl;
}
} else {
cout << "\033[1;31mAccount not found.\033[0m" << endl;
}
return accounts.size(); // Return the same number of accounts
}

// Function to display account balance
void balance(const vector<BankAccount>& accounts) {
int acc_num;
cout << "\033[1;34mEnter account number: \033[0m";
cin >> acc_num;

int index = findacct(accounts, acc_num);
if (index != -1) {
cout << "\033[1;32mBalance: $\033[0m" << fixed << setprecision(2) << accounts[index].getBalance() << endl;
} else {
cout << "\033[1;31mAccount not found.\033[0m" << endl;
}
}

// Function to display account information
void account_info(const vector<BankAccount>& accounts) {
string ssn;
cout << "\033[1;34mEnter Social Security Number: \033[0m";
cin >> ssn;

for (const BankAccount& account : accounts) {
if (account.getDepositor().getSSN() == ssn) {
cout << "\033[1;34mAccount Number:\033[0m" << account.getAccountNumber() << endl;
cout << "\033[1;32mBalance:$\033[0m" << fixed << setprecision(2) << account.getBalance() << endl;
cout << "\033[1;34mName:\033[0m" << account.getDepositor().getName().getFirstName() << " " 
<< account.getDepositor().getName().getLastName() << endl;
cout << "\033[1;32mSSN: \033[0m" << setw(9) << setfill('0') << account.getDepositor().getSSN().substr(account.getDepositor().getSSN().length() - 4, 4) << endl;
return;
}
}
cout << "\033[1;31mNo account found with the given SSN.\033[0m" << endl;
}

int main() {
ifstream infile("accounts.txt");
ofstream outfile("transaction_log.txt"); 

vector<BankAccount> accounts;
// No need for num_accounts here
read_accts(accounts, infile);  // Just read the accounts
outfile << "Initial Account Database:" << endl;
print_accts(accounts, outfile); 

char choice;
do {
menu();
cout << "\033[1;32mEnter your choice:\033\0m";
cin >> choice;

switch (choice) {
case 'W':
withdrawal(accounts);
break;
case 'D':
deposit(accounts);
break;
case 'N':
new_acct(accounts);
break;
case 'B':
balance(accounts);
break;
case 'I':
account_info(accounts);
break;
case 'X':
delete_acct(accounts);
break;
case 'Q':
cout << "\033[1;31mExiting program...\033[0m" << endl;
break;
default:
cout << "\033[1;31mInvalid choice. Please try again.\033[0m" << endl;
}

if (choice != 'Q') { 
outfile << "\nTransaction Details:" << endl;
print_accts(accounts, outfile);
}
} while (choice != 'Q');

outfile << "\nFinal Account Database:" << endl;
print_accts(accounts, outfile); 

infile.close();
outfile.close();

return 0;
}
