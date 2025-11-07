# Samrt-Expense-teacker-application-
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

class ExpenseTracker:
    def __init__(self, file_name="expenses.csv"):
        self.file_name = file_name
        try:
            self.data = pd.read_csv(file_name)
        except:
            self.data = pd.DataFrame(columns=["Date", "Amount", "Category", "Description"])

    # 1. Add Expense (with validation)
    def add_expense(self, date, amount, category, description):
        if amount <= 0:
            print("Error: Amount must be positive.")
            return
        
        new_expense = {"Date": date, "Amount": amount, "Category": category, "Description": description}
        self.data = self.data.append(new_expense, ignore_index=True)
        self.data.to_csv(self.file_name, index=False)
        print("Expense added successfully!")

    # 2. Summary (using NumPy)
    def get_summary(self):
        total = np.sum(self.data["Amount"])
        average = np.mean(self.data["Amount"])
        category_totals = self.data.groupby("Category")["Amount"].sum()
        
        print("\n---- Expense Summary ----")
        print(f"Total Spending: ₹{total:.2f}")
        print(f"Average Spending: ₹{average:.2f}\n")
        print("Spending by Category:")
        print(category_totals)

    # 3. Filter Expenses
    def filter_expenses(self, category=None, date_range=None):
        filtered = self.data
        if category:
            filtered = filtered[filtered["Category"] == category]
        if date_range:
            start, end = date_range
            filtered = filtered[(filtered["Date"] >= start) & (filtered["Date"] <= end)]
        print(filtered)

    # 4. Generate Visualizations
    def generate_report(self):
        if self.data.empty:
            print("No data available.")
            return
        
        # Bar Chart
        plt.figure()
        self.data.groupby("Category")["Amount"].sum().plot(kind='bar')
        plt.title("Total Expense by Category")
        plt.xlabel("Category")
        plt.ylabel("Total Expense")
        plt.show()

        # Line Chart
        plt.figure()
        self.data.groupby("Date")["Amount"].sum().plot(kind='line')
        plt.title("Daily Spending Trend")
        plt.xlabel("Date")
        plt.ylabel("Amount")
        plt.show()

        # Pie Chart
        plt.figure()
        self.data.groupby("Category")["Amount"].sum().plot(kind='pie', autopct='%1.1f%%')
        plt.title("Spending Distribution by Category")
        plt.ylabel("")
        plt.show()

        # Histogram
        plt.figure()
        self.data["Amount"].plot(kind='hist')
        plt.title("Expense Amount Frequency")
        plt.xlabel("Amount")
        plt.show()


# ----------- Main Menu for User Interaction -----------

def main():
    tracker = ExpenseTracker()

    while True:
        print("\n==== Smart Expense Tracker ====")
        print("1. Add Expense")
        print("2. Show Summary")
        print("3. Filter Expenses")
        print("4. Generate Visual Report")
        print("5. Exit")

        choice = input("Enter choice: ")

        if choice == "1":
            date = input("Enter Date (YYYY-MM-DD): ")
            amount = float(input("Enter Amount: "))
            category = input("Enter Category (Food/Transport/Utilities/Other): ")
            description = input("Enter Description: ")
            tracker.add_expense(date, amount, category, description)

        elif choice == "2":
            tracker.get_summary()

        elif choice == "3":
            cat = input("Enter category to filter (or press enter to skip): ")
            tracker.filter_expenses(cat if cat else None)

        elif choice == "4":
            tracker.generate_report()

        elif choice == "5":
            print("Exiting Program...")
            break

        else:
            print("Invalid choice. Try again.")

if __name__ == "__main__":
    main()
