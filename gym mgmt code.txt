import tkinter as tk
from tkinter import messagebox, ttk
import sqlite3
from ttkbootstrap import Style

conn = sqlite3.connect('gymdb.db')

showPack, showCust = False, False
container = ''

class GymApp(tk.Tk):
    def __init__(self, *args, **kwargs):
        global container
        tk.Tk.__init__(self, *args, **kwargs)
        self.title('Gym Management System')
        self.configure(bg='#1e1e1e')
        self.geometry("1366x768")
        self.style = Style(theme="darkly")
        l1 = tk.Label(self, text = 'WELCOME TO THE GYM', font=("ARIEL", 28, "bold"), bg='#1e1e1e').pack()
        container = tk.Frame(self, bg='#EAF0F1')
        container.pack(side="top", fill="both", expand = True, pady=10)

        container.grid_rowconfigure(0, weight=1)
        container.grid_columnconfigure(0, weight=1)

        self.frames = {}

        for F in (Login, Menu, AddCustomer, AddPackage, ShowCustomers, ShowPackages, SearchCustomer, AddSubscription, AddPayment):

            print(F)
            f = str(F)
            if f == "<class '__main__.ShowPackages'>" or f == "<class '__main__.ShowCustomers'>":
                if showPack or showCust:
                    print('Inside ShowPack')
                    frame = F(container, self)
                    print(frame)
                    self.frames[F] = frame
                    frame.grid(row=0, column=0, sticky="nsew")
                else:
                    print('Outside ShowPack')
            else:
                frame = F(container, self)
                print(frame)
                self.frames[F] = frame
                frame.grid(row=0, column=0, sticky="nsew")

        self.show_frame(Login)

    def show_pack(self):
        print('Inside ShowPack')
        frame = ShowPackages(container, self)
        print(frame)
        self.frames[ShowPackages] = frame
        frame.grid(row=0, column=0, sticky="nsew")

    def show_cust(self):
        print('Inside ShowCust')
        frame = ShowCustomers(container, self)
        print(frame)
        self.frames[ShowCustomers] = frame
        frame.grid(row=0, column=0, sticky="nsew")

    def show_frame(self, cont):
        global showPack
        cont1 = str(cont)
        if cont1 == "<class '__main__.ShowPackages'>":
            showPack = True
            print(showPack)
            self.show_pack()
        elif cont1 == "<class '__main__.ShowCustomers'>":
            showCust = True
            print(showCust)
            self.show_cust()
        frame = self.frames[cont]
        frame.tkraise()

class Login(tk.Frame):

    def __init__(self, parent, controller):
        self.controller = controller
        tk.Frame.__init__(self,parent)
        self.configure(bg='#1e1e1e')
        label = tk.Label(self, text = 'Sign In', font=("Helvetica", 30), bg='#1e1e1e').pack(pady=10, padx=10)
        username = tk.Label(self, text = 'Username', font=("Times", 24)).pack(pady=20)
        self.ev1 = tk.StringVar(value = '')
        e1 = tk.Entry(self, width = 50, textvariable = self.ev1, font=("Times", 20)).pack()
        password = tk.Label(self, text = 'Password', font=("Times", 24)).pack(pady=20)
        self.ev2 = tk.StringVar(value = '')
        e2 = tk.Entry(self, width = 50, textvariable = self.ev2, font=("Times", 20))
        e2.pack()
        e2.config(show="*")
        b1 = tk.Button(self, text = 'Login', relief='raised', font=("Times", 18), width=10, command=self.authenticate).pack(pady=50)

    def authenticate(self):
        global conn
        flag = 0
        cur = conn.cursor()
        query = '''select * from managers'''
        cur.execute(query)
        r = cur.fetchall()
        for row in r:
            if self.ev1.get() == row[0] and self.ev2.get() == row[1]:
                print("Login Successful!")
                messagebox.showinfo('Login Successful', 'Welcome to Gym Management System!!')
                flag = 1
                conn.commit()
                cur.close()
                return self.controller.show_frame(Menu)
        if flag == 0:
            print('Login Failed!')
            messagebox.askretrycancel('Login Failed', 'Error Authenticating, Please Try Again!!')
        conn.commit()
        cur.close()


class Menu(tk.Frame):

    def __init__(self, parent, controller):
        tk.Frame.__init__(self, parent)
        self.configure(bg='#1e1e1e')
        label = tk.Label(self, text = 'Start Menu', font=("Helvetica", 30), bg='#1e1e1e').pack(pady=24, padx=10)
        b1 = tk.Button(self, text = 'Add Customer', relief='raised', font=("Times", 18), width=20, command=lambda: controller.show_frame(AddCustomer)).pack(pady=4)
        b2 = tk.Button(self, text = 'Add Package', relief='raised', font=("Times", 18), width=20, command=lambda: controller.show_frame(AddPackage)).pack(pady=4)
        b3 = tk.Button(self, text = 'Show All Customers', relief='raised', font=("Times", 18), width=20, command=lambda: controller.show_frame(ShowCustomers)).pack(pady=4)
        b4 = tk.Button(self, text = 'Show All Packages', relief='raised', font=("Times", 18), width=20, command=lambda: controller.show_frame(ShowPackages)).pack(pady=4)
        b5 = tk.Button(self, text = 'Search Customer', relief='raised', font=("Times", 18), width=20, command=lambda: controller.show_frame(SearchCustomer)).pack(pady=4)
        b6 = tk.Button(self, text = 'Add Subscription', relief='raised', font=("Times", 18), width=20, command=lambda: controller.show_frame(AddSubscription)).pack(pady=4)
        b7 = tk.Button(self, text = 'Add Payment', relief='raised', font=("Times", 18), width=20, command=lambda: controller.show_frame(AddPayment)).pack(pady=4)


class AddCustomer(tk.Frame):

    def __init__(self, parent, controller):
        self.controller = controller
        tk.Frame.__init__(self, parent)
        self.configure(bg='#1e1e1e')
        label = tk.Label(self, text='Add Customer', font=("Helvetica", 30), bg='#1e1e1e').pack(pady=10, padx=10)

        # Label and Entry for Customer ID
        custID_label = tk.Label(self, text='Enter Customer ID:', font=("Times", 20), bg='#1e1e1e')
        custID_label.pack(pady=12, padx=10, anchor="w")
        self.custID = tk.StringVar(value='')
        customerID = tk.Entry(self, width=50, textvariable=self.custID, font=("Times", 20))
        customerID.pack(pady=12)

        # Label and Entry for Customer Name
        name_label = tk.Label(self, text='Enter Customer Name:', font=("Times", 20), bg='#1e1e1e')
        name_label.pack(pady=12, padx=10, anchor="w")
        self.nameVar = tk.StringVar(value='')
        name = tk.Entry(self, width=50, textvariable=self.nameVar, font=("Times", 20))
        name.pack(pady=12)

        # Label and Entry for Phone Number
        phone_label = tk.Label(self, text='Enter Phone Number:', font=("Times", 20), bg='#1e1e1e')
        phone_label.pack(pady=12, padx=10, anchor="w")
        self.phone = tk.StringVar(value='')
        phoneNo = tk.Entry(self, width=50, textvariable=self.phone, font=("Times", 20))
        phoneNo.pack(pady=12)

        # Label and Entry for Joining Date
        joining_label = tk.Label(self, text='Enter Joining Date:', font=("Times", 20), bg='#1e1e1e')
        joining_label.pack(pady=12, padx=10, anchor="w")
        self.date = tk.StringVar(value='')
        joiningDate = tk.Entry(self, width=50, textvariable=self.date, font=("Times", 20))
        joiningDate.pack(pady=12)

        # Submit button
        b1 = tk.Button(self, text='Submit', relief='raised', font=("Times", 18), width=10, command=self.addCustomer)
        b1.pack(pady=12)

        # Menu button
        b2 = tk.Button(self, text='Menu', relief='raised', font=("Times", 18), width=10, command=lambda: controller.show_frame(Menu))
        b2.pack(pady=12)

        # Text variable to display success message
        self.text = tk.StringVar(value='')
        success = tk.Label(self, text='', font=("Helvetica", 10), bg='#1e1e1e', textvariable=self.text).pack(pady=6, padx=10)

    def addCustomer(self):
        global conn
        cur = conn.cursor()
        if not self.custID.get().isdigit():
            return messagebox.showwarning('Add Customer', 'Please enter valid customer ID!')
        elif self.nameVar.get() == '':
            return messagebox.showwarning('Add Customer', "Don't leave name entry blank!")
        elif len(self.phone.get()) != 10 or not self.phone.get().isdigit():
            return messagebox.showwarning('Add Customer', "Please enter valid phone number!")
        elif len(self.date.get()) != 11 or self.date.get().isdigit() or self.date.get().isalpha():
            return messagebox.showwarning('Add Customer', 'Please enter valid date in this format DD-MMM-YYYY!')
        else:
            rec = (int(self.custID.get()), self.nameVar.get(), self.phone.get(), self.date.get())
            q1 = '''select customerID from customers'''
            cur.execute(q1)
            r = cur.fetchall()
            for row in r:
                if row[0] == int(self.custID.get()):
                    return messagebox.showwarning('Add Customer', 'Customer ID already exists!')
            query = '''insert into customers values(?, ?, ?, ?)'''
            cur.execute(query, rec)
            self.text.set("New Customer Added!!")
            conn.commit()
            cur.close()

class AddPackage(tk.Frame):

    def __init__(self, parent, controller):
        self.controller = controller
        tk.Frame.__init__(self, parent)
        self.configure(bg='#1e1e1e')
        label = tk.Label(self, text='Add Package', font=("Helvetica", 30), bg='#1e1e1e').pack(pady=10, padx=10)

        # Label and Entry for Package Name
        package_name_label = tk.Label(self, text='Enter Package Name:', font=("Times", 20), bg='#1e1e1e')
        package_name_label.pack(pady=12, padx=10, anchor="w")
        self.package_name = tk.StringVar(value='')
        package_name_entry = tk.Entry(self, width=50, textvariable=self.package_name, font=("Times", 20))
        package_name_entry.pack(pady=12)

        # Label and Entry for Package Price
        package_price_label = tk.Label(self, text='Enter Package Price:', font=("Times", 20), bg='#1e1e1e')
        package_price_label.pack(pady=12, padx=10, anchor="w")
        self.package_price = tk.StringVar(value='')
        package_price_entry = tk.Entry(self, width=50, textvariable=self.package_price, font=("Times", 20))
        package_price_entry.pack(pady=12)

        # Submit button
        submit_button = tk.Button(self, text='Submit', relief='raised', font=("Times", 18), width=10, command=self.addPackage)
        submit_button.pack(pady=12)

        # Menu button
        menu_button = tk.Button(self, text='Menu', relief='raised', font=("Times", 18), width=10, command=lambda: controller.show_frame(Menu))
        menu_button.pack(pady=12)

        # Text variable to display success message
        self.text = tk.StringVar(value='')
        success_label = tk.Label(self, text='', font=("Helvetica", 10), bg='#1e1e1e', textvariable=self.text).pack(pady=6, padx=10)

    def addPackage(self):
        global conn
        cur = conn.cursor()
        if not self.packID.get().isdigit():
            return messagebox.showwarning('Add Package', 'Please enter valid package ID!')
        elif self.typeVar.get() == '':
            return messagebox.showwarning('Add Package', "Don't leave type entry blank!")
        elif self.facil.get() == '':
            return messagebox.showwarning('Add Package', "Don't leave facilities entry blank!")
        elif not self.costVar.get().isdigit():
            return messagebox.showwarning('Add Package', 'Please enter valid cost!')
        else:
            rec = (int(self.packID.get()), self.typeVar.get(), self.facil.get(), int(self.costVar.get()))
            q1 = '''select packageID from packages'''
            cur.execute(q1)
            r = cur.fetchall()
            for row in r:
                if row[0] == int(self.packID.get()):
                    return messagebox.showwarning('Add Package', 'Package ID already exists!')
            query = '''insert into packages values(?, ?, ?, ?)'''
            cur.execute(query, rec)
            self.text.set("New Package Added!!")
            conn.commit()
            cur.close()

class ShowCustomers(tk.Frame):

    def __init__(self, parent, controller):
        self.controller = controller
        tk.Frame.__init__(self, parent)
        self.configure(bg='#1e1e1e')
        label = tk.Label(self, text = 'List of Customers', font=("Helvetica", 30), bg='#1e1e1e').pack(padx=10)

        list1 = tk.Listbox(self, height=8, width=160, selectmode='multiple', font=("Times", 28), bd = 6, relief='raised', bg='#1BCA9B', fg='#2C3335')
        list1.pack(padx=25, pady=16)
        b2 = tk.Button(self, text = 'Menu', relief='raised', font=("Times", 18), width=10, command=lambda: controller.show_frame(Menu)).pack()
        b3 = tk.Button(self, text = 'Delete', relief='raised', font=("Times", 18), width=10, command=lambda:self.deleteCustomers(list1)).pack(pady=8)
        self.view_command(list1)

    def __str__(self):
        return 'ShowCustomers'

    def showCustomers(self):
        global conn
        conn = sqlite3.connect('gymdb.db')
        cur = conn.cursor()
        query = '''select * from customers'''
        cur.execute(query)
        r = cur.fetchall()
        print(r)
        conn.commit()
        cur.close()
        count = len(r)
        if count > 0:
            return r
        else:
            messagebox.showinfo("List of Customers", "No customer records found.")

    def view_command(self, list1):
        list1.delete(0,tk.END)
        for row in self.showCustomers():
            list1.insert(tk.END,row)

    def deleteCustomers(self, list1):
        sel = list1.curselection()
        cur = conn.cursor()
        q1 = '''select * from customers'''
        cur.execute(q1)
        r = cur.fetchall()
        query = '''delete from customers where customerID = ?'''
        if len(sel) == 0:
            return messagebox.showwarning('Delete Customer', 'No customer is selected!')
        for i in range(len(sel)):
            cur.execute(query, (r[sel[i]][0],))
        conn.commit()
        cur.close()
        for index in sel[::-1]:
            list1.delete(index)

class ShowPackages(tk.Frame):

    def __init__(self, parent, controller):
        self.controller = controller
        tk.Frame.__init__(self, parent)
        self.configure(bg='#1e1e1e')
        label = tk.Label(self, text = 'List of Packages', font=("Helvetica", 30), bg='#1e1e1e').pack(padx=10)

        list1 = tk.Listbox(self, height=8, width=160, selectmode='multiple', font=("Times", 28), bd = 6, relief='raised', bg='#1BCA9B', fg='#2C3335')
        list1.pack(padx=25, pady=16)
        b2 = tk.Button(self, text = 'Menu', relief='raised', font=("Times", 18), width=10, command=lambda: controller.show_frame(Menu)).pack()
        b3 = tk.Button(self, text = 'Delete', relief='raised', font=("Times", 18), width=10, command=lambda:self.deletePackages(list1)).pack(pady=8)
        self.view_command(list1)

    def __str__(self):
        return 'ShowPackages'

    def showPackages(self):
        global conn
        conn = sqlite3.connect('gymdb.db')
        cur = conn.cursor()
        query = '''select * from packages'''
        cur.execute(query)
        r = cur.fetchall()
        print(r)
        count = len(r)
        if count > 0:
            return r
        else:
            messagebox.showinfo("List of Packages", "No package records found.")

    def view_command(self, list1):
        list1.delete(0,tk.END)
        for row in self.showPackages():
            list1.insert(tk.END,row)

    def deletePackages(self, list1):
        sel = list1.curselection()
        cur = conn.cursor()
        q1 = '''select * from packages'''
        cur.execute(q1)
        r = cur.fetchall()
        query = '''delete from packages where packageID = ?'''
        if len(sel) == 0:
            return messagebox.showwarning('Delete Package', 'No package is selected!')
        for i in range(len(sel)):
            cur.execute(query, (r[sel[i]][0],))
        conn.commit()
        cur.close()
        for index in sel[::-1]:
            list1.delete(index)

class SearchCustomer(tk.Frame):

    def __init__(self, parent, controller):
        self.controller = controller
        tk.Frame.__init__(self, parent)
        self.configure(bg='#1e1e1e')
        label = tk.Label(self, text='Search Customer', font=("Helvetica", 30), bg='#1e1e1e').pack(padx=10, pady=15)

        # Customer Name/ID Entry
        search_label = tk.Label(self, text='Enter Customer Name:', font=("Times", 20), bg='#1e1e1e')
        search_label.pack(pady=12, padx=10, anchor="w")
        self.searchVar = tk.StringVar(value='')
        search_entry = tk.Entry(self, width=50, textvariable=self.searchVar, font=("Times", 20))
        search_entry.pack(pady=12)

        # Search button
        search_button = tk.Button(self, text='Search', relief='raised', font=("Times", 18), width=10, command=self.searchCustomer)
        search_button.pack(pady=8)

        # Menu button
        menu_button = tk.Button(self, text='Menu', relief='raised', font=("Times", 18), width=10, command=lambda: controller.show_frame(Menu))
        menu_button.pack(pady=8)

        # Text variable to display search results
        self.search_result = tk.StringVar(value='')
        search_result_label = tk.Label(self, text='', font=("Helvetica", 10), bg='#1e1e1e', textvariable=self.search_result).pack(pady=6, padx=10)

    def searchCustomer(self):
        global conn
        cur = conn.cursor()
        search_value = self.searchVar.get()
        q1 = '''SELECT * FROM customers WHERE name = ?'''
        cur.execute(q1, (search_value,))
        result = cur.fetchall()
        if not result:
            self.search_result.set("Customer not found!")
        else:
            self.search_result.set("Customer found: " + str(result))
        conn.commit()
        cur.close()


    def view_command(self, list1):
        list1.delete(0,tk.END)
        for row in self.searchCustomer():
            list1.insert(tk.END,row)


class AddSubscription(tk.Frame):

    def __init__(self, parent, controller):
        self.controller = controller
        tk.Frame.__init__(self, parent)
        self.configure(bg='#1e1e1e')
        months = ['01 Month', '03 Months', '06 Months', '12 Months', '24 Months']
        label = tk.Label(self, text='Add Subscription', font=("Helvetica", 30), bg='#1e1e1e').pack(padx=10, pady=15)

        # Subscription ID Entry
        subsID_label = tk.Label(self, text='Enter Subscription ID:', font=("Times", 20), bg='#1e1e1e')
        subsID_label.pack(pady=12, padx=10, anchor="w")
        self.subsID = tk.StringVar(value='')
        subscriptionID_entry = tk.Entry(self, width=50, textvariable=self.subsID, font=("Times", 20))
        subscriptionID_entry.pack(pady=12)

        # Customer Name Entry
        custName_label = tk.Label(self, text='Enter Customer Name:', font=("Times", 20), bg='#1e1e1e')
        custName_label.pack(pady=12, padx=10, anchor="w")
        self.custName = tk.StringVar(value='')
        customerName_entry = tk.Entry(self, width=50, textvariable=self.custName, font=("Times", 20))
        customerName_entry.pack(pady=12)

        # Package ID Entry
        packID_label = tk.Label(self, text='Enter Package ID:', font=("Times", 20), bg='#1e1e1e')
        packID_label.pack(pady=12, padx=10, anchor="w")
        self.packID = tk.StringVar(value='')
        packageID_entry = tk.Entry(self, width=50, textvariable=self.packID, font=("Times", 20))
        packageID_entry.pack(pady=12)

        # Number of Months Dropdown
        month_label = tk.Label(self, text='Select No. of Months:', font=("Times", 20), bg='#1e1e1e')
        month_label.pack(pady=18, padx=10, anchor="w")
        self.monthVar = tk.StringVar(value='Select No. of Months')
        month_dropdown = ttk.Combobox(self, width=49, textvariable=self.monthVar, font=("Times", 20), values=months)
        month_dropdown.pack(pady=18)

        # Submit button
        submit_button = tk.Button(self, text='Submit', relief='raised', font=("Times", 18), width=10, command=self.addSubscription)
        submit_button.pack(pady=8)

        # Menu button
        menu_button = tk.Button(self, text='Menu', relief='raised', font=("Times", 18), width=10, command=lambda: controller.show_frame(Menu))
        menu_button.pack(pady=8)

        # Text variable to display success message
        self.text = tk.StringVar(value='')
        success_label = tk.Label(self, text='', font=("Helvetica", 10, "italic"), bg='#1e1e1e', textvariable=self.text).pack(pady=6, padx=10)

    def addSubscription(self):
        global conn
        cur = conn.cursor()
        q1 = '''select * from customers where name = ?'''
        cur.execute(q1, (self.custName.get(),))
        r1 = cur.fetchall()
        q2 = '''select * from packages where packageID = ?'''

        if not self.subsID.get().isdigit():
            return messagebox.showwarning('Add Subscription', 'Please enter valid subscription ID!')
        elif len(r1) == 0:
            return messagebox.showwarning('Add Subscription', 'Customer Not Found!')
        elif not self.packID.get().isdigit():
            return messagebox.showwarning('Add Subscription', 'Please enter valid package ID!')
        elif self.monthVar.get() == 'Select No. of Months':
            return messagebox.showwarning('Add Subscription', 'Please select a option from dropdown menu!')
        else:
            cur.execute(q2, (int(self.packID.get()),))
            r2 = cur.fetchall()
            q1 = '''select subsID from subscriptions'''
            cur.execute(q1)
            r = cur.fetchall()
            for row in r:
                if row[0] == int(self.subsID.get()):
                    return messagebox.showwarning('Add Subscription', 'Subscription ID already exists!')
            if len(r2) == 0:
                return messagebox.showwarning('Add Subscription', 'Package Not Found!')
            rec = (int(self.subsID.get()), self.custName.get(), int(self.packID.get()), int(self.monthVar.get()[0:2]))
            query = '''insert into subscriptions values(?, ?, ?, ?)'''
            cur.execute(query, rec)
            self.text.set("New Subscription Added!!")
            messagebox.showinfo("Success", "New Subscription Added!!")
            conn.commit()
            cur.close()

class AddPayment(tk.Frame):

    def __init__(self, parent, controller):
        self.controller = controller
        tk.Frame.__init__(self, parent)
        self.configure(bg='#1e1e1e')
        label = tk.Label(self, text='Add Payment', font=("Helvetica", 30), bg='#1e1e1e').pack(pady=10, padx=10)

        # Label and Entry for Customer ID
        custID_label = tk.Label(self, text='Enter Customer ID:', font=("Times", 20), bg='#1e1e1e')
        custID_label.pack(pady=12, padx=10, anchor="w")
        self.custID = tk.StringVar(value='')
        customerID_entry = tk.Entry(self, width=50, textvariable=self.custID, font=("Times", 20))
        customerID_entry.pack(pady=12)

        # Label and Entry for Amount
        amount_label = tk.Label(self, text='Enter Amount:', font=("Times", 20), bg='#1e1e1e')
        amount_label.pack(pady=12, padx=10, anchor="w")
        self.amount = tk.StringVar(value='')
        amount_entry = tk.Entry(self, width=50, textvariable=self.amount, font=("Times", 20))
        amount_entry.pack(pady=12)

        # Submit button
        submit_button = tk.Button(self, text='Submit', relief='raised', font=("Times", 18), width=10, command=self.addPayment)
        submit_button.pack(pady=12)

        # Menu button
        menu_button = tk.Button(self, text='Menu', relief='raised', font=("Times", 18), width=10, command=lambda: controller.show_frame(Menu))
        menu_button.pack(pady=12)

        # Text variable to display success message
        self.text = tk.StringVar(value='')
        success_label = tk.Label(self, text='', font=("Helvetica", 10), bg='#1e1e1e', textvariable=self.text).pack(pady=6, padx=10)

    def addPayment(self):
        global conn
        cur = conn.cursor()
        q1 = '''select * from customers where name = ?'''
        cur.execute(q1, (self.custName.get(),))
        r1 = cur.fetchall()
        if not self.payID.get().isdigit():
            return messagebox.showwarning('Add Payment', 'Please enter valid payment ID!')
        elif len(r1) == 0:
            return messagebox.showwarning('Add Payment', 'Customer Not Found!')
        else:
            q2 = '''select * from subscriptions where custName = ?'''
            cur.execute(q2, (self.custName.get(),))
            r2 = cur.fetchall()
            if len(r2) == 0:
                return messagebox.showwarning('Add Payment', 'Customer Found But Not Yet Subscribed To Any Packages!')
            else:
                q1 = '''select paymentID from payments'''
                cur.execute(q1)
                r = cur.fetchall()
                for row in r:
                    if row[0] == int(self.payID.get()):
                        return messagebox.showwarning('Add Payment', 'Payment ID already exists!')
                q3 = '''select * from packages where packageID = ?'''
                cur.execute(q3, (r2[0][2],))
                r3 = cur.fetchall()
                bill = r2[0][3] * r3[0][3]
                self.text.set("New Payment Added!!")
                conn.commit()
                cur.close()
                return self.invoice.set('Payment ID : {}\nCustomer Name : {}\nBill Amount : ₹ {}'.format(int(self.payID.get()), self.custName.get(), bill))


app = GymApp()
app.mainloop()
conn.close()
