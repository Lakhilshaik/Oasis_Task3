# Oasis_Task3
//ATM Interface
import java.util.Scanner;

abstract class BankAccount {
    private String accountHolder;
    private double balance;

    public BankAccount(String accountHolder) {
        this.accountHolder = accountHolder;
        this.balance = 0.0;
    }

    public void deposit(double amount) {
        balance += amount;
    }

    public boolean withdraw(double amount) {
        if (balance >= amount) {
            balance -= amount;
            return true;
        }
        return false;
    }

    public double getBalance() {
        return balance;
    }
}

interface Statement {
    void displayStatement();
}

interface Loan {
    boolean applyForLoan(double amount);

    void displayLoanDetails();
}

class GeneralLoan implements Loan {
    private String loanType;
    private double loanAmount;
    private double interestRate;
    private int tenure;

    public GeneralLoan(String loanType, double loanAmount, double interestRate, int tenure) {
        this.loanType = loanType;
        this.loanAmount = loanAmount;
        this.interestRate = interestRate;
        this.tenure = tenure;
    }

    @Override
    public boolean applyForLoan(double amount) {
        if (amount <= loanAmount) {
            System.out.println(loanType + " loan approved!");
            return true;
        } else {
            System.out.println(loanType + " loan application denied. Requested amount exceeds eligible loan amount.");
            return false;
        }
    }

    @Override
    public void displayLoanDetails() {
        System.out.println(loanType + " Loan Details:");
        System.out.println("Loan Amount: $" + loanAmount);
        System.out.println("Interest Rate: " + interestRate + "%");
        System.out.println("Tenure: " + tenure + " months");
    }
}

class User extends BankAccount implements Statement {
    private String username;
    private String password;
    private String phoneNumber;
    private String transactionHistory;

    public User(String accountHolder, String username, String password, String phoneNumber) {
        super(accountHolder);
        this.username = username;
        this.password = password;
        this.phoneNumber = phoneNumber;
        this.transactionHistory = "Transaction History for " + username + ":\n";
    }

    public boolean checkPassword(String password) {
        return this.password.equals(password);
    }

    public boolean transfer(User recipient, double amount) {
        if (recipient != null && amount > 0 && amount <= getBalance()) {
            super.withdraw(amount);
            recipient.deposit(amount);
            transactionHistory += "\nTransferred: $" + amount + " to " + recipient.getUsername();
            System.out.println("Transfer successful.");
            return true;
        } else {
            System.out.println("Transfer failed. Please check the recipient and amount.");
            return false;
        }
    }

    public void updatePhoneNumber(String newPhoneNumber) {
        this.phoneNumber = newPhoneNumber;
        System.out.println("Phone number updated successfully. New Phone Number: " + newPhoneNumber);
    }

    @Override
    public void deposit(double amount) {
        super.deposit(amount);
        transactionHistory += "\nDeposited: $" + amount;
    }

    @Override
    public boolean withdraw(double amount) {
        if (super.withdraw(amount)) {
            transactionHistory += "\nWithdrawn: $" + amount;
            return true;
        }
        System.out.println("Withdrawal failed. Insufficient balance.");
        return false;
    }

    @Override
    public void displayStatement() {
        System.out.println(transactionHistory + "\nCurrent Balance: $" + getBalance());
    }

    public String getUsername() {
        return username;
    }
}

class Bank {
    private User[] users;
    private User loggedInUser;
    private boolean isLoggedIn;

    public Bank() {
        users = new User[100];
        loggedInUser = null;
        isLoggedIn = false;
    }

    public void register(String accountHolder, String username, String password, String phoneNumber) {
        for (int i = 0; i < users.length; i++) {
            if (users[i] == null) {
                User newUser = new User(accountHolder, username, password, phoneNumber);
                users[i] = newUser;
                System.out.println("Registration successful for " + username);
                return;
            }
        }
        System.out.println("User limit reached. Cannot register a new user.");
    }

    public boolean login(String username, String password) {
        for (int i = 0; i < users.length; i++) {
            User user = users[i];
            if (user != null && user.getUsername().equals(username) && user.checkPassword(password)) {
                loggedInUser = user;
                isLoggedIn = true;
                return true;
            }
        }
        return false;
    }

    public User getLoggedInUser() {
        return loggedInUser;
    }

    public boolean isLoggedIn() {
        return isLoggedIn;
    }

    public void logout() {
        loggedInUser = null;
        isLoggedIn = false;
    }

    public User[] getUsers() {
        return users;
    }
}

public class BankingApplicationn {
    static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        Bank bank = new Bank();

        while (true) {
            if (!bank.isLoggedIn()) {
                System.out.println("1. Register");
                System.out.println("2. Login");
                System.out.println("3. Exit");
            } else {
                System.out.println("Logged in as: " + bank.getLoggedInUser().getUsername());
                System.out.println("4. Logout");
                System.out.println("5. Deposit");
                System.out.println("6. Withdraw");
                System.out.println("7. Transfer");
                System.out.println("8. Check Balance");
                System.out.println("9. View Statement");
                System.out.println("10. Apply for Loan");
                System.out.println("11. Update Phone Number");
                System.out.println("12. Exit");
            }

            System.out.print("Enter your choice: ");
            int choice = scanner.nextInt();
            scanner.nextLine();

            switch (choice) {
                case 1:
                    register(bank);
                    break;
                case 2:
                    login(bank);
                    break;
                case 3:
                    exit();
                    break;
                case 4:
                    bank.logout();
                    System.out.println("Logged out.");
                    break;
                case 5:
                    deposit(bank);
                    break;
                case 6:
                    withdraw(bank);
                    break;
                case 7:
                    transfer(bank);
                    break;
                case 8:
                    checkBalance(bank);
                    break;
                case 9:
                    viewStatement(bank);
                    break;
                case 10:
                    applyForLoan(bank);
                    break;
                case 11:
                    updatePhoneNumber(bank);
                    break;
                case 12:
                    exit();
                    break;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    private static void register(Bank bank) {
        System.out.print("Enter your full name: ");
        String accountHolder = scanner.nextLine();
        System.out.print("Enter your username: ");
        String regUsername = scanner.nextLine();
        System.out.print("Enter your password: ");
        String regPassword = scanner.nextLine();
        System.out.print("Enter your phone number: ");
        String regPhoneNumber = scanner.nextLine();
        bank.register(accountHolder, regUsername, regPassword, regPhoneNumber);
    }

    private static void login(Bank bank) {
        System.out.print("Enter your username: ");
        String loginUsername = scanner.nextLine();
        System.out.print("Enter your password: ");
        String loginPassword = scanner.nextLine();
        if (bank.login(loginUsername, loginPassword)) {
            System.out.println("Login successful.");
        } else {
            System.out.println("Login failed. Please check your credentials.");
        }
    }

    private static void exit() {
        System.out.print("Do you want to exit? (yes/no): ");
        String exitChoice = scanner.nextLine().trim();
        if (exitChoice.equalsIgnoreCase("yes")) {
            System.out.println("Exiting the application.");
            scanner.close();
            System.exit(0);
        }
    }

    private static void deposit(Bank bank) {
        User loggedInUser = bank.getLoggedInUser();
        if (loggedInUser != null) {
            System.out.print("Enter the amount to deposit: $");
            double depositAmount = scanner.nextDouble();
            loggedInUser.deposit(depositAmount);
            System.out.println("Deposit successful.");
        } else {
            System.out.println("Please login to access your account.");
        }
    }

    private static void withdraw(Bank bank) {
        User loggedInUser = bank.getLoggedInUser();
        if (loggedInUser != null) {
            System.out.print("Enter the amount to withdraw: $");
            double withdrawAmount = scanner.nextDouble();
            loggedInUser.withdraw(withdrawAmount);
        } else {
            System.out.println("Please login to access your account.");
        }
    }

    private static void transfer(Bank bank) {
        User loggedInUser = bank.getLoggedInUser();
        if (loggedInUser != null) {
            System.out.print("Enter the recipient's username: ");
            String recipientUsername = scanner.nextLine();

            User recipient = null;
            for (User user : bank.getUsers()) {
                if (user != null && user.getUsername().equals(recipientUsername)) {
                    recipient = user;
                    break;
                }
            }

            if (recipient != null) {
                System.out.print("Enter the amount to transfer: $");
                double transferAmount = scanner.nextDouble();
                loggedInUser.transfer(recipient, transferAmount);
            } else {
                System.out.println("Recipient not found.");
            }
        } else {
            System.out.println("Please login to access your account.");
        }
    }

    private static void checkBalance(Bank bank) {
        User loggedInUser = bank.getLoggedInUser();
        if (loggedInUser != null) {
            System.out.println("Current Balance: $" + loggedInUser.getBalance());
        } else {
            System.out.println("Please login to access your account.");
        }
    }

    private static void viewStatement(Bank bank) {
        User loggedInUser = bank.getLoggedInUser();
        if (loggedInUser != null) {
            loggedInUser.displayStatement();
        } else {
            System.out.println("Please login to access your account.");
        }
    }

    private static void applyForLoan(Bank bank) {
        System.out.println("Select the type of loan:");
        System.out.println("1. Car Loan");
        System.out.println("2. House Loan");
        System.out.println("3. Personal Loan");

        System.out.print("Enter your choice: ");
        int loanTypeChoice = scanner.nextInt();
        scanner.nextLine();

        Loan loan = null;
        switch (loanTypeChoice) {
            case 1:
                loan = new GeneralLoan("Car", 50000, 8.5, 24);
                break;
            case 2:
                loan = new GeneralLoan("House", 200000, 6.0, 120);
                break;
            case 3:
                loan = new GeneralLoan("Personal", 10000, 10.0, 12);
                break;
            default:
                System.out.println("Invalid loan type choice.");
        }

        if (loan != null) {
            System.out.print("Enter the loan amount you want to apply for: $");
            double requestedAmount = scanner.nextDouble();
            if (loan.applyForLoan(requestedAmount)) {
                loan.displayLoanDetails();
            }
        }
    }

    private static void updatePhoneNumber(Bank bank) {
        User loggedInUser = bank.getLoggedInUser();
        if (loggedInUser != null) {
            System.out.print("Enter your new phone number: ");
            String newPhoneNumber = scanner.nextLine();
            loggedInUser.updatePhoneNumber(newPhoneNumber);
        } 
        else {
            System.out.println("Please login to update your phone number.");
        }
    }
}
