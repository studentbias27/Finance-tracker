"""
My Finance Tracker
Uses: pandas (data storage) + numpy (stats)
Run: python expense_tracker.py
"""

import pandas as pd
import numpy as np
import os
from datetime import date
FILES = {
    "1": {"file": "my_income.csv", "name": "Income", 
        "categories": ["Main", "Side", "Other"]},
    
    "2": {"file": "my_expenses.csv", "name": "Expenses", 
        "categories": ["Home", "Invesment", "Self", "Other"]}
}

def load_data(file_key):
    file_path = FILES[file_key]["file"]
    if os.path.exists(file_path):
        return pd.read_csv(file_path)
    return pd.DataFrame(columns=["Date", "Category", "Amount"])


def save_data(df, file_key):
    file_path = FILES[file_key]["file"]
    df.to_csv(file_path, index=False)


def add_expense(df, file_key):
    categories = FILES[file_key]["categories"]
    print("\n Categories:", ", ".join(f"{i+1}.{c}" for i, c in enumerate(categories)))
    
    try:
        choice = int(input(f"Pick category (1-{len(categories)}): ")) - 1
        category = categories[choice]
    except (ValueError, IndexError):
        print(" Invalid choice."); return df

    try:
        amount = float(input("Amount (₹): "))
        if amount <= 0: raise ValueError
    except ValueError:
        print(" Enter a valid positive amount."); return df

    today = str(date.today())

    new_row = pd.DataFrame([{"Date": today, "Category": category,
                            "Amount": amount}])
    df = pd.concat([df, new_row], ignore_index=True)
    save_data(df, file_key)
    print(f" ₹{amount:.0f} added under {category}!")
    return df


def view_expenses(df):
    if df.empty:
        print("\n No expenses yet. Start adding!"); return

    print(f"\n{'─'*52}")
    print(f"{'Date':<12} {'Category':<14} {'Amount':>8}  {'Note'}")
    print(f"{'─'*52}")
    for _, row in df.iterrows():
        print(f"{row['Date']:<12} {row['Category']:<14} ₹{row['Amount']:>7.0f}")
    print(f"{'─'*52}")



def show_summary(df, file_key):
    if df.empty:
        print("\n No data to summarize."); return

    amounts = df["Amount"].to_numpy()  
    tracker_name = FILES[file_key]["name"]
    is_income = tracker_name == "Income"
    total_label = "Total earned" if is_income else "Total spent"
    max_label = "Biggest income" if is_income else "Biggest spend"
    min_label = "Smallest income" if is_income else "Smallest spend"
    category_label = "Income by Category" if is_income else "Expense by Category"

    print(f"\n{'═'*40}")
    print(f" {tracker_name.upper()} SUMMARY")
    print(f"{'═'*40}")
    print(f"  {total_label:<14}: ₹{np.sum(amounts):,.0f}")
    print(f"  Average/entry : ₹{np.mean(amounts):,.0f}")
    print(f"  {max_label:<14}: ₹{np.max(amounts):,.0f}")
    print(f"  {min_label:<14}: ₹{np.min(amounts):,.0f}")
    print(f"  No. of entries: {len(amounts)}")
    print(f"\n {category_label}:")
    breakdown = df.groupby("Category")["Amount"].sum().sort_values(ascending=False)
    for cat, total in breakdown.items():
        max_val = max(breakdown) if len(breakdown) > 0 else 1
        bar = "█" * int(total / max_val * 15)
        print(f"  {cat:<14} ₹{total:>7.0f}  {bar}")
    print(f"{'═'*40}")

    df["Date"] = pd.to_datetime(df["Date"])
    monthly = df.groupby(df["Date"].dt.month)["Amount"].sum()

    print("\n Monthly Trend:")
    for m, val in monthly.items():
        print(f"  Month {m}: ₹{val:.0f}")



def show_overview():
    income_df = load_data("1")
    expense_df = load_data("2")

    total_income = income_df["Amount"].sum() if not income_df.empty else 0
    total_expense = expense_df["Amount"].sum() if not expense_df.empty else 0
    balance = total_income - total_expense
    balance_label = "Money left" if balance >= 0 else "Over budget"

    print(f"\n{'═'*40}")
    print(" OVERVIEW")
    print(f"{'═'*40}")
    print(f"  Total income : ₹{total_income:,.0f}")
    print(f"  Total expense: ₹{total_expense:,.0f}")
    print(f"  {balance_label:<13}: ₹{abs(balance):,.0f}")
    print(f"{'═'*40}")


def delete_last(df, file_key):
    if df.empty:
        print("\nNothing to delete."); return df
    print(f"\nRemoving: {df.iloc[-1].to_dict()}")
    df = df.iloc[:-1].reset_index(drop=True)
    save_data(df, file_key)
    print("Last entry deleted.")
    return df

def main():
    print("\n My Finance Tracker")
    
    while True:
        print("\n SELECT TRACKER:")
        print("  1. Income")
        print("  2. Expenses")
        print("  3. Overview")
        print("  0. Exit")
        
        file_choice = input("\nChoose tracker (0-3): ").strip()
        
        if file_choice == "0":
            print("Bye!")
            break

        if file_choice == "3":
            show_overview()
            continue
        
        if file_choice not in FILES:
            print("Invalid choice. Try again.")
            continue
        file_name = FILES[file_choice]["name"]
        print(f"\n {file_name.upper()} MODE-\n")
        
        df = load_data(file_choice)

        while True:
            print(f"\n  1. Add {file_name.lower()}")
            print("  2. View all entries")
            print("  3. Show summary")
            print("  4. Delete last entry")
            print("  5. Back to tracker selection")
            
            choice = input("\nYour choice: ").strip()

            if choice == "1":   df = add_expense(df, file_choice)
            elif choice == "2": view_expenses(df)
            elif choice == "3": show_summary(df, file_choice)
            elif choice == "4": df = delete_last(df, file_choice)
            elif choice == "5": break
            else: print("Enter 1-5 only.")

if __name__ == "__main__":
    main()
