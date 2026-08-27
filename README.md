import json
import os
from datetime import datetime

FILE_NAME = "budget.json"

def load_data():
    if os.path.exists(FILE_NAME):
        try:
            with open(FILE_NAME, "r") as file:
                return json.load(file)
        except json.JSONDecodeError:
            return []
    return []

def save_data(data):
    with open(FILE_NAME, "w") as file:
        json.dump(data, file, indent=4)

def add_transaction(data):
    while True:
        transaction_type = input("Type (income/expense): ").strip().lower()
        if transaction_type == "income" or transaction_type == "expense":
            break
        print("Wrong type. Please type 'income' or 'expense'.")

    while True:
        amount_text = input("Amount: ").strip()
        try:
            amount = float(amount_text)
            if amount > 0:
                break
            print("Error: Amount must be greater than zero")
        except ValueError:
            print("Error: Amount must be a valid number")

    while True:
        category = input("Category: ").strip()
        if category != "":
            break
        print("Category cannot be blank")

    while True:
        transaction_date = input("Date (YYYY-MM-DD) [today]: ").strip()
        if transaction_date == "":
            transaction_date = datetime.today().strftime("%Y-%m-%d")
            break
        try:
            datetime.strptime(transaction_date, "%Y-%m-%d")
            break
        except ValueError:
            print("Invalid date format. Please use YYYY-MM-DD.")

    note = input("Note: ").strip()

    new_transaction = {
        "type": transaction_type,
        "amount": amount,
        "category": category,
        "date": transaction_date,
        "note": note
    }

    data.append(new_transaction)
    save_data(data)
    print("Transaction added successfully.")

def view_summary(data):
    total_income = 0.0
    total_expense = 0.0

    for item in data:
        if item["type"] == "income":
            total_income = total_income + item["amount"]
        elif item["type"] == "expense":
            total_expense = total_expense + item["amount"]

    net_balance = total_income - total_expense

    print("\n     Summary     ")
    print(f"Total Income:   ₹{total_income:.2f}")
    print(f"Total Expenses: ₹{total_expense:.2f}")
    print(f"Net Balance:    ₹{net_balance:.2f}")

def view_by_category(data):
    category_totals = {}

    for item in data:
        cat = item["category"]

        if cat not in category_totals:
            category_totals[cat] = {"income": 0.0, "expense": 0.0}

        if item["type"] == "income":
            category_totals[cat]["income"] = category_totals[cat]["income"] + item["amount"]
        elif item["type"] == "expense":
            category_totals[cat]["expense"] = category_totals[cat]["expense"] + item["amount"]

    print("\n    By Category   ")
    for cat in category_totals:
        income = category_totals[cat]["income"]
        expense = category_totals[cat]["expense"]
        print(f"[{cat}] Income: ₹{income:.2f} | Expense: ₹{expense:.2f}")

def delete_transaction(data):
    if len(data) == 0:
        print("No transactions to delete.")
        return

    counter = 1
    for item in data:
        print(f"{counter}. {item['date']} - {item['type']} - {item['category']} - ₹{item['amount']:.2f}")
        counter = counter + 1

    choice_text = input("\nEnter number to delete: ").strip()

    try:
        choice = int(choice_text)
        if choice >= 1 and choice <= len(data):
            index_to_delete = choice - 1
            data.pop(index_to_delete)
            save_data(data)
            print("Transaction deleted.")
        else:
            print("Number not found in list.")
    except ValueError:
        print("Error: You did not enter a valid number.")

def main():
    data = load_data()
    print("Welcome to Budget Tracker!")

    while True:
        print("\n1. Add transaction")
        print("2. View summary")
        print("3. View by category")
        print("4. Delete transaction")
        print("5. Exit")

        choice = input("> ").strip()

        if choice == '1':
            add_transaction(data)
        elif choice == '2':
            view_summary(data)
        elif choice == '3':
            view_by_category(data)
        elif choice == '4':
            delete_transaction(data)
        elif choice == '5':
            print("Goodbye!")
            break
        else:
            print("Invalid choice. Please pick a number from 1 to 5.")

if __name__ == "__main__":
    main()
