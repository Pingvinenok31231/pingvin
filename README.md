import time
import tkinter as tk

class Business:
    def __init__(self, name, purchase_price, hourly_income):
        self.name = name
        self.purchase_price = purchase_price
        self.hourly_income = hourly_income
        self.balance = 0
        self.last_tax_payment_time = time.time()
        self.num_owned_businesses = 0

    def buy_business(self, num_businesses):
        total_purchase_price = self.purchase_price * num_businesses
        self.balance += total_purchase_price
        self.num_owned_businesses += num_businesses
        print("Успешно приобретено", num_businesses, "бизнесов", self.name)
        print("Остаток на счету бизнеса:", self.balance)

    def withdraw_money(self, amount):
        if amount <= self.balance:
            self.balance -= amount
            print("С бизнеса", self.name, "снято", amount)
        else:
            print("Недостаточно средств на счету бизнеса.")

    def deposit_money(self, amount):
        self.balance += amount
        print("На бизнес", self.name, "начислено", amount)

    def get_balance(self):
        return self.balance

    def get_num_owned_businesses(self):
        return self.num_owned_businesses

    def calculate_tax(self):
        num_businesses = self.balance // self.purchase_price
        tax_amount = 2000 * num_businesses
        return tax_amount

    def pay_taxes(self):
        current_time = time.time()
        time_since_last_payment = current_time - self.last_tax_payment_time
        if time_since_last_payment >= 24 * 60 * 60:
            tax_amount = self.calculate_tax()
            if self.balance >= tax_amount:
                self.balance -= tax_amount
                self.last_tax_payment_time = current_time
                print("Налоги на бизнес", self.name, "оплачены.")
            else:
                print("Недостаточно средств для оплаты налогов на бизнес", self.name)
        else:
            print("Налоги на бизнес", self.name, "уже оплачены. Следующий платеж возможен через",
                  int((24 * 60 * 60 - time_since_last_payment) / 3600), "часов.")

class User:
    def __init__(self, name, balance):
        self.name = name
        self.balance = balance
        self.max_businesses = 100

    def add_balance(self, amount):
        self.balance += amount

    def deduct_balance(self, amount):
        if self.balance >= amount:
            self.balance -= amount
            return True
        else:
            return False

    def get_balance(self):
        return self.balance

    def calculate_max_businesses(self):
        self.max_businesses = 100

def buy_business():
    num_businesses = int(amount_entry.get())
    if user.deduct_balance(business.purchase_price * num_businesses) and user.max_businesses >= num_businesses:
        business.buy_business(num_businesses)
        user.calculate_max_businesses()
    else:
        print("Недостаточно средств или достигнуто максимальное количество бизнесов для покупки бизнесов", business.name)

def withdraw_money_from_business():
    amount = int(amount_entry.get())
    business.withdraw_money(amount)
    user.add_balance(amount)

def pay_taxes():
    business.pay_taxes()

def check_user_balance():
    print("Баланс пользователя {}: {}".format(user.name, user.get_balance()))

def check_owned_businesses():
    print("Количество купленных бизнесов {}: {}".format(business.name, business.get_num_owned_businesses()))

def exit_program():
    window.destroy()

def update_balances():
    business_balance_label.config(text="Баланс бизнеса {}: {}".format(business.name, business.get_balance()))
    user_balance_label.config(text="Баланс пользователя {}: {}".format(user.name, user.get_balance()))
    max_businesses_label.config(text="Максимальное количество бизнесов: {}".format(user.max_businesses))

user_balance = 1000000
user = User("John Doe", user_balance)
business = Business("Маринка Мандаринка", 100000, 10000)

window = tk.Tk()
window.title("Управление бизнесом")

buy_button = tk.Button(window, text="Купить бизнес", command=buy_business)
buy_button.pack()

withdraw_button = tk.Button(window, text="Снять деньги с бизнеса", command=withdraw_money_from_business)
withdraw_button.pack()

pay_taxes_button = tk.Button(window, text="Оплатить налоги", command=pay_taxes)
pay_taxes_button.pack()

check_user_balance_button = tk.Button(window, text="Проверить баланс пользователя", command=check_user_balance)
check_user_balance_button.pack()

check_owned_businesses_button = tk.Button(window, text="Просмотреть количество купленных бизнесов", command=check_owned_businesses)
check_owned_businesses_button.pack()

exit_button = tk.Button(window, text="Выйти", command=exit_program)
exit_button.pack()

amount_label = tk.Label(window, text="Количество бизнесов / Сумма:")
amount_label.pack()

amount_entry = tk.Entry(window)
amount_entry.pack()

business_balance_label = tk.Label(window, text="Баланс бизнеса {}: {}".format(business.name, business.get_balance()))
business_balance_label.pack()

user_balance_label = tk.Label(window, text="Баланс пользователя {}: {}".format(user.name, user.get_balance()))
user_balance_label.pack()

max_businesses_label = tk.Label(window, text="Максимальное количество бизнесов: {}".format(user.max_businesses))
max_businesses_label.pack()

update_balances()

window.mainloop()
