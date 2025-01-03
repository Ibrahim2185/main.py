# main.py
حسابة سن التقاعد
#!/usr/bin/env python
import tkinter as tk
from tkinter import ttk, messagebox
from datetime import datetime, timedelta

class RetirementCalculatorApp:
    def __init__(self, root):
        self.root = root
        self.root.title("حاسبة سن التقاعد")
        
        self.create_menu()
        self.create_widgets()

    def create_menu(self):
        menubar = tk.Menu(self.root)
        self.root.config(menu=menubar)

        file_menu = tk.Menu(menubar, tearoff=0)
        menubar.add_cascade(label="ملف", menu=file_menu)
        file_menu.add_command(label="خروج", command=self.root.quit)

        help_menu = tk.Menu(menubar, tearoff=0)
        menubar.add_cascade(label="مساعدة", menu=help_menu)
        help_menu.add_command(label="حول", command=self.show_about)

    def show_about(self):
        messagebox.showinfo("حول", "حاسبة سن التقاعد\nالإصدار 1.0")

    def create_widgets(self):
        # Label and entry for birth date
        ttk.Label(self.root, text="أدخل تاريخ ميلادك (DD-MM-YYYY):").grid(row=0, column=0, padx=10, pady=10)
        self.birth_date_entry = ttk.Entry(self.root, justify='right')
        self.birth_date_entry.grid(row=0, column=1, padx=10, pady=10)

        # Label and entry for hire date
        ttk.Label(self.root, text="أدخل تاريخ التعيين (DD-MM-YYYY):").grid(row=1, column=0, padx=10, pady=10)
        self.hire_date_entry = ttk.Entry(self.root, justify='right')
        self.hire_date_entry.grid(row=1, column=1, padx=10, pady=10)

        # Removed gender entry

        # Calculate button
        ttk.Button(self.root, text="احسب سن التقاعد", command=self.calculate_retirement_age).grid(row=3, column=0, columnspan=2, pady=10)

        # Result label
        self.result_label = ttk.Label(self.root, text="", wraplength=400, justify="right")
        self.result_label.grid(row=4, column=0, columnspan=2, pady=10)

        # Button to show Law 81 of 2016
        ttk.Button(self.root, text="عرض مواد قانون 81 لسنة 2016", command=self.show_law_81).grid(row=5, column=0, columnspan=2, pady=10)

        # Button to show Egyptian Pension Law
        ttk.Button(self.root, text="عرض مواد قانون المعاشات المصري", command=self.show_pension_law).grid(row=6, column=0, columnspan=2, pady=10)

    def calculate_retirement_age(self):
        try:
            # Parse inputs
            birth_date = datetime.strptime(self.birth_date_entry.get(), "%d-%m-%Y")
            hire_date = datetime.strptime(self.hire_date_entry.get(), "%d-%m-%Y")
            # Removed gender parsing

            # Validate inputs
            if birth_date.year <= 0:
                raise ValueError("يرجى إدخال قيم صحيحة لتاريخ الميلاد.")
            # Removed gender validation

            # Determine retirement age and date
            retirement_age = self.get_retirement_age(birth_date.year)
            retirement_date = birth_date.replace(year=birth_date.year + retirement_age)

            # Calculate work duration until now
            work_duration = datetime.now() - hire_date
            work_details = self.format_duration(work_duration)

            # Calculate remaining time until retirement
            remaining_duration = retirement_date - datetime.now()
            remaining_details = self.format_duration(remaining_duration)

            self.result_label.config(
                text=(
                    f"ستتقاعد في تاريخ {retirement_date.strftime('%Y-%m-%d')} في سن {retirement_age}.\n"
                    f"مدة العمل حتى تاريخه: {work_details}.\n"
                    f"المدة المتبقية للمعاش: {remaining_details}."
                )
            )
        except ValueError as e:
            self.result_label.config(text=f"خطأ: {e}")

    def get_retirement_age(self, birth_year):
        # Removed gender-based retirement age calculation
        if birth_year <= 1970:
            return 60
        elif birth_year > 1970:
            additional_years = min(65 - 60, birth_year - 1970)
            return 60 + additional_years

    @staticmethod
    def format_duration(duration):
        days = duration.days
        seconds = duration.seconds
        hours = seconds // 3600
        minutes = (seconds % 3600) // 60
        seconds = seconds % 60
        return f"{days} يوم، {hours} ساعة، {minutes} دقيقة، {seconds} ثانية"

    def show_law_81(self):
        law_81_text = (
            "قانون 81 لسنة 2016:\n"
            "المادة 70: يكون سن الإحالة للمعاش بالنسبة لشاغلي الوظائف القيادية ووظائف الإدارة العليا 60 سنة.\n"
            "المادة 71: يجوز مد الخدمة لشاغلي الوظائف القيادية ووظائف الإدارة العليا بعد بلوغ سن الإحالة للمعاش لمدة لا تجاوز ثلاث سنوات.\n"
            "المادة 72: يجوز بقرار من رئيس مجلس الوزراء بناءً على عرض الوزير المختص مد سن الإحالة للمعاش إلى 65 سنة لبعض الفئات من العاملين وفقًا للضوابط التي يحددها القرار.\n"
        )
        self.result_label.config(text=law_81_text)

    def show_pension_law(self):
        pension_law_text = (
            "قانون المعاشات المصري:\n"
            "المادة 41: يستحق المؤمن عليه المعاش عند بلوغ سن الشيخوخة مع توافر مدة اشتراك في تأمين الشيخوخة والعجز والوفاة لا تقل عن 120 شهرًا.\n"
            "المادة 42: يجوز للمؤمن عليه الذي بلغ سن الشيخوخة ولم تتوافر فيه مدة الاشتراك المشار إليها في المادة 41 أن يستمر في الاشتراك في التأمين حتى استكمال هذه المدة.\n"
            "المادة 43: يجوز بقرار من رئيس مجلس الوزراء بناءً على عرض الوزير المختص مد سن الإحالة للمعاش إلى 65 سنة لبعض الفئات من العاملين وفقًا للضوابط التي يحددها القرار.\n"
        )
        self.result_label.config(text=pension_law_text)

if __name__ == "__main__":
    root = tk.Tk()
    app = RetirementCalculatorApp(root)
    root.mainloop()
