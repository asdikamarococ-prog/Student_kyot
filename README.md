# Student_kyot
StudentInfo: A Console-Based Student Information System Using Python

import ast
import os
import pprint
from collections import Counter

# The data file is always kept next to this script, no matter which
# folder the program is started from. (Before, it was saved in whatever
# folder you happened to run from, so edits looked like they were lost.)
try:
    BASE_DIR = os.path.dirname(os.path.abspath(__file__))
except NameError:
    BASE_DIR = os.getcwd()

DATA_FILE = os.path.join(BASE_DIR, "studentinfo_data.py")
STAFF_PASSWORD = "admin123"

# Programs offered (based on the PHINMA COC program listing)
PROGRAMS = {
    # College of Management and Accountancy
    "BSA": "Bachelor of Science in Accountancy",
    "BSBA": "Bachelor of Science in Business Administration",
    "BSMA": "Bachelor of Science in Management Accounting",
    "BSHM": "Bachelor of Science in Hospitality Management",
    "BSTM": "Bachelor of Science in Tourism Management",
    # College of Engineering and Architecture
    "BSArch": "Bachelor of Science in Architecture",
    "BSCE": "Bachelor of Science in Civil Engineering",
    "BSCpE": "Bachelor of Science in Computer Engineering",
    "BSEE": "Bachelor of Science in Electrical Engineering",
    "BSME": "Bachelor of Science in Mechanical Engineering",
    # School of Criminology and Criminal Justice
    "BSCRIM": "Bachelor of Science in Criminology",
    # College of Education
    "BEED": "Bachelor of Elementary Education",
    "BECED": "Bachelor of Early Childhood Education",
    "BSED": "Bachelor of Secondary Education",
    # College of Information Technology
    "BSIT": "Bachelor of Science in Information Technology",
    # College of Allied Health Sciences & Nursing
    "BSN": "Bachelor of Science in Nursing",
    "BSMLS": "Bachelor of Science in Medical Laboratory Science",
    "BSPharm": "Bachelor of Science in Pharmacy",
    # College of Arts and Sciences
    "BSPsych": "Bachelor of Science in Psychology",
    "ABComm": "Bachelor of Arts in Communication",
}

SUBJECTS = {
    "BSA": ["ACC 101 - Fundamentals of Accounting",
            "ACC 102 - Financial Accounting and Reporting"],
    "BSBA": ["BUS 101 - Business Management",
             "BUS 102 - Principles of Marketing"],
    "BSMA": ["MA 101 - Management Accounting Basics",
             "MA 102 - Cost Accounting"],
    "BSHM": ["HM 101 - Introduction to Hospitality",
             "HM 102 - Food and Beverage Service"],
    "BSTM": ["TM 101 - Introduction to Tourism",
             "TM 102 - Tourism Planning and Development"],
    "BSArch": ["ARC 101 - Architectural Design 1",
               "ARC 102 - Architectural Graphics"],
    "BSCE": ["CE 101 - Engineering Mechanics",
             "CE 102 - Surveying"],
    "BSCpE": ["CPE 101 - Computer Engineering Fundamentals",
              "CPE 102 - Digital Logic"],
    "BSEE": ["EE 101 - Electrical Circuits 1",
             "EE 102 - Electromagnetics"],
    "BSME": ["ME 101 - Engineering Thermodynamics",
             "ME 102 - Machine Design"],
    "BSCRIM": ["CRM 101 - Introduction to Criminology",
               "CRM 102 - Philippine Criminal Justice System"],
    "BEED": ["EDU 101 - Foundations of Education",
             "EDU 103 - Child and Learner Development"],
    "BECED": ["ECE 101 - Foundations of Early Childhood Education",
              "EDU 103 - Child and Learner Development"],
    "BSED": ["EDU 101 - Foundations of Education",
             "EDU 102 - Teaching Profession"],
    "BSIT": ["ITE 260 - Computer Programming",
             "ITE 101 - Introduction to Computing",
             "GE 1 - Understanding the Self"],
    "BSN": ["NUR 101 - Fundamentals of Nursing",
            "NUR 102 - Health Assessment"],
    "BSMLS": ["MLS 101 - Introduction to Medical Laboratory Science",
              "MLS 102 - Clinical Chemistry"],
    "BSPharm": ["PHA 101 - Pharmaceutical Chemistry",
                "PHA 102 - Pharmacology"],
    "BSPsych": ["PSY 101 - Introduction to Psychology",
                "PSY 102 - Developmental Psychology"],
    "ABComm": ["COM 101 - Introduction to Communication",
               "COM 102 - Media and Information Literacy"],
}

# Example teacher assignments; replace with official school data.
# Built automatically so every subject above always has a teacher entry.
TEACHERS = {}
for _subject_list in SUBJECTS.values():
    for _subject in _subject_list:
        if _subject not in TEACHERS:
            TEACHERS[_subject] = f"Sample Teacher {len(TEACHERS) + 1}"


def load_data():
    if not os.path.exists(DATA_FILE):
        return []
    try:
        with open(DATA_FILE, "r", encoding="utf-8") as file:
            data = ast.literal_eval(file.read())
            return data if isinstance(data, list) else []
    except (ValueError, SyntaxError, OSError):
        print("Warning: Could not read the data file. Starting with empty records.")
        return []


students = load_data()


def save_data():
    # Write to a temporary file first, then swap it in, so a crash
    # in the middle of saving can never wreck the saved records.
    temp_file = DATA_FILE + ".tmp"
    with open(temp_file, "w", encoding="utf-8") as file:
        file.write(pprint.pformat(students, width=100))
    os.replace(temp_file, DATA_FILE)


def ask(prompt):
    while True:
        value = input(prompt).strip()
        if value:
            return value
        print("This field is required.")


def ask_yes_no(prompt):
    # Accepts y / yes / n / no. Anything else asks again instead of
    # silently going back to the menu.
    while True:
        answer = input(prompt).strip().lower()
        if answer in ("y", "yes"):
            return True
        if answer in ("n", "no"):
            return False
        print("Please answer y or n.")


def find_student(student_id):
    for student in students:
        if student["student_id"].lower() == student_id.lower():
            return student
    return None


def print_table(headers, rows):
    if not rows:
        print("\nNo records found.")
        return

    widths = [
        max(len(str(headers[i])),
            max(len(str(row[i])) for row in rows))
        for i in range(len(headers))
    ]

    line = "-+-".join("-" * width for width in widths)
    print()
    print(" | ".join(str(headers[i]).ljust(widths[i])
                    for i in range(len(headers))))
    print(line)

    for row in rows:
        print(" | ".join(str(row[i]).ljust(widths[i])
                        for i in range(len(headers))))


def display_student(student):
    print("\n========== COMPLETE STUDENT PROFILE ==========")
    fields = [
        ("Student ID", "student_id"),
        ("Full Name", "name"),
        ("Date of Birth", "date_of_birth"),
        ("Gender", "gender"),
        ("Email", "email"),
        ("Contact Number", "phone"),
        ("Address", "address"),
        ("Program/Course", "program"),
        ("Year Level", "year_level"),
        ("Section", "section"),
        ("School Year", "school_year"),
        ("Semester", "semester"),
        ("Enrollment Status", "status"),
        ("Subjects", "subjects"),
        ("Assigned Teachers", "teachers"),
    ]

    for label, key in fields:
        value = student.get(key, "Not provided")
        if isinstance(value, list):
            value = ", ".join(value) if value else "None assigned"
        print(f"{label}: {value}")

    print("=" * 46)


def subjects_and_teachers(program):
    subjects = SUBJECTS.get(program, [])
    teachers = list(dict.fromkeys(
        TEACHERS.get(subject, "Unassigned")
        for subject in subjects
    ))
    return subjects.copy(), teachers


def add_student():
    print("\n========== ADD STUDENT ==========")
    student_id = ask("Student ID: ")

    if find_student(student_id):
        print("Error: Student ID already exists.")
        return

    name = ask("Full name: ")
    program = ask("Program code (e.g., BSIT): ").upper()

    if program not in PROGRAMS:
        print("Program not in the sample program list.")
        print("Available:", ", ".join(PROGRAMS))
        return

    while True:
        try:
            year = int(ask("Year level (1-5): "))
            if 1 <= year <= 5:
                break
            print("Enter a year level from 1 to 5.")
        except ValueError:
            print("Please enter a number.")

    section = ask("Section: ")
    school_year = ask("School year (e.g., 2026-2027): ")
    semester = ask("Semester (1st, 2nd, or Summer): ")

    subjects, assigned_teachers = subjects_and_teachers(program)

    student = {
        "student_id": student_id,
        "name": name,
        "date_of_birth": ask("Date of birth (YYYY-MM-DD): "),
        "gender": ask("Gender: "),
        "email": ask("Email address: "),
        "phone": ask("Contact number: "),
        "address": ask("Address: "),
        "program": program,
        "year_level": year,
        "section": section,
        "school_year": school_year,
        "semester": semester,
        "status": "Active",
        "subjects": subjects,
        "teachers": assigned_teachers,
    }

    students.append(student)
    save_data()
    print("\nStudent record added successfully.")
    display_student(student)


def view_all_students():
    rows = [
        [s["student_id"], s["name"], s["program"],
         s["year_level"], s["section"], s["school_year"],
         s["semester"], s["status"]]
        for s in students
    ]
    print_table(
        ["ID", "Name", "Program", "Year", "Section",
         "School Year", "Semester", "Status"],
        rows
    )


def search_student():
    keyword = ask("Enter student ID or name: ").lower()
    matches = [
        s for s in students
        if keyword in s["student_id"].lower()
        or keyword in s["name"].lower()
    ]

    if not matches:
        print("No matching student found.")
        return []

    for student in matches:
        display_student(student)

    return matches


def update_student(student=None):
    # If a student was already picked (for example from a search),
    # use that record. Otherwise ask for the ID.
    if student is None:
        student_id = ask("Enter the student's ID: ")
        student = find_student(student_id)

        if not student:
            print("Student not found.")
            return

    display_student(student)

    fields = {
        "1": ("Full name", "name"),
        "2": ("Email", "email"),
        "3": ("Contact number", "phone"),
        "4": ("Address", "address"),
        "5": ("Program", "program"),
        "6": ("Year level", "year_level"),
        "7": ("Section", "section"),
        "8": ("School year", "school_year"),
        "9": ("Semester", "semester"),
        "10": ("Enrollment status", "status"),
    }

    while True:
        print("\n========== UPDATE STUDENT ==========")
        print(f"Updating: {student['student_id']} - {student['name']}")
        for number, (label, key) in fields.items():
            print(f"{number}. {label} (current: {student.get(key, 'Not provided')})")
        print("0. Finish updating")

        choice = input("Type the NUMBER of the field to update (0 to finish): ").strip()

        if choice == "0":
            break
        if choice not in fields:
            print("Invalid option. Please type a number from the list above (0-10).")
            continue

        label, key = fields[choice]
        old_value = student.get(key, "Not provided")

        if key == "program":
            new_value = ask("Enter new program code (e.g., BSIT): ").upper()
            if new_value not in PROGRAMS:
                print("Program not in the sample program list.")
                print("Available:", ", ".join(PROGRAMS))
                continue
            student[key] = new_value
            # A new program means new subjects and teachers.
            student["subjects"], student["teachers"] = subjects_and_teachers(new_value)

        elif key == "year_level":
            try:
                new_value = int(ask("Enter new year level (1-5): "))
            except ValueError:
                print("Please enter a number.")
                continue
            if not 1 <= new_value <= 5:
                print("Enter a year level from 1 to 5.")
                continue
            student[key] = new_value

        else:
            new_value = ask(f"Enter new {label}: ")
            student[key] = new_value

        save_data()
        print(f"\n{label} updated and saved: {old_value} -> {new_value}")
        display_student(student)

    print("Finished updating.")


def academic_assignments():
    print("\n========== ACADEMIC INFORMATION ==========")
    for program, description in PROGRAMS.items():
        print(f"\n{program} - {description}")
        for subject in SUBJECTS.get(program, []):
            print(f"  {subject} | Teacher: "
                  f"{TEACHERS.get(subject, 'Unassigned')}")


def reports_insights():
    if not students:
        print("No student records available for reporting.")
        return

    print("\n========== ADMIN REPORTS & INSIGHTS ==========")
    print("Total student records:", len(students))

    by_program = Counter(s["program"] for s in students)
    by_year = Counter(s["year_level"] for s in students)
    by_school_year = Counter(s["school_year"] for s in students)
    by_semester = Counter(s["semester"] for s in students)
    by_status = Counter(s["status"] for s in students)

    print("\nSTUDENTS PER PROGRAM")
    print_table(
        ["Program", "Program Name", "Student Count"],
        [[code, PROGRAMS.get(code, "Other"),
          by_program[code]] for code in sorted(by_program)]
    )

    print("\nSTUDENTS PER YEAR LEVEL")
    print_table(
        ["Year Level", "Student Count"],
        [[year, by_year[year]] for year in sorted(by_year)]
    )

    print("\nSTUDENTS PER SCHOOL YEAR")
    print_table(
        ["School Year", "Student Count"],
        [[year, count] for year, count in sorted(by_school_year.items())]
    )

    print("\nSTUDENTS PER SEMESTER")
    print_table(
        ["Semester", "Student Count"],
        [[sem, count] for sem, count in sorted(by_semester.items())]
    )

    print("\nENROLLMENT STATUS")
    print_table(
        ["Status", "Student Count"],
        [[status, count] for status, count in sorted(by_status.items())]
    )


def enrollment_lists():
    program = ask("Program code: ").upper()
    school_year = ask("School year: ")
    semester = ask("Semester (1st, 2nd, or Summer): ")

    matches = [
        s for s in students
        if s["program"].upper() == program
        and s["school_year"].lower() == school_year.lower()
        and s["semester"].lower() == semester.lower()
    ]

    print(f"\n{program} | School Year {school_year} | {semester}")
    print(f"Total matching students: {len(matches)}")

    print_table(
        ["Student ID", "Name", "Year", "Section", "Status"],
        [[s["student_id"], s["name"], s["year_level"],
          s["section"], s["status"]] for s in matches]
    )


def school_staff_menu():
    while True:
        print("\n========== SCHOOL STAFF MENU ==========")
        print("1. View Student Records")
        print("2. Add Student")
        print("3. Search and Update Student")
        print("4. View Programs, Subjects and Teachers")
        print("5. Reports and Insights")
        print("6. Enrollment Lists by Program and Semester")
        print("0. Return to Main Menu")

        choice = input("Choose an option: ").strip()

        if choice == "1":
            view_all_students()
        elif choice == "2":
            add_student()
        elif choice == "3":
            matches = search_student()
            if matches and ask_yes_no("Update a record? (y/n): "):
                if len(matches) == 1:
                    update_student(matches[0])
                else:
                    update_student()
        elif choice == "4":
            academic_assignments()
        elif choice == "5":
            reports_insights()
        elif choice == "6":
            enrollment_lists()
        elif choice == "0":
            break
        else:
            print("Invalid choice. Please try again.")


def student_portal():
    student_id = ask("Enter your student ID: ")
    student = find_student(student_id)

    if not student:
        print("Student record not found.")
        return

    print("\n========== STUDENT PORTAL ==========")
    display_student(student)
    print("\nYour subjects and assigned teachers:")
    print_table(
        ["Subject", "Assigned Teacher"],
        [[subject, TEACHERS.get(subject, "Unassigned")]
         for subject in student.get("subjects", [])]
    )


def teacher_portal():
    print("\n========== TEACHER PORTAL ==========")
    print("This demonstration allows teachers to search records.")
    search_student()


def main():
    print("Records are saved in:", DATA_FILE)

    while True:
        print("\n========================================")
        print(" STUDENTINFO: STUDENT INFORMATION SYSTEM")
        print("========================================")
        print("1. Student Portal")
        print("2. School Staff / Admin")
        print("3. Teacher Portal")
        print("0. Exit")

        choice = input("Select your role: ").strip()

        if choice == "1":
            student_portal()
        elif choice == "2":
            password = input("Enter staff password: ")
            if password == STAFF_PASSWORD:
                school_staff_menu()
            else:
                print("Incorrect password.")
        elif choice == "3":
            teacher_portal()
        elif choice == "0":
            print("Thank you for using StudentInfo.")
            break
        else:
            print("Invalid choice.")


if __name__ == "__main__":
    main()
