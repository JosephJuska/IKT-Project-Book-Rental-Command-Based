import json
import hashlib
import time
import datetime
import os
import math
from copy import deepcopy
try:
    from pwinput import pwinput

except:
    os.system('pip install pwinput')
    from pwinput import pwinput

#CONSTANTS
FILE_PATH = os.path.dirname(os.path.abspath(__file__))
DATA_PATH = {
    'users' : f'{FILE_PATH}\\users.json',
    'books' : f'{FILE_PATH}\\books.json',
}

CLEAR_COMMAND = "cls" if os.name == "nt" else "clear"

#FUNCTIONS
def fetch_data(data_type: str):
    # Fetch data from the database
    data = []
    try:
        with open(DATA_PATH[data_type], 'r') as f:
            data = json.load(f)

    except FileNotFoundError:
        with open(DATA_PATH[data_type], 'w') as f:
            f.write(json.dumps(data, indent=4))

    return data

def post_data(data: list, data_type: str):
    #Post data to database
    with open(DATA_PATH[data_type], 'w') as f:
        data = json.dumps(data, indent=4)
        f.write(data)

def error_message(msg):
    #Display error message
    print("\n" + "-"*20)
    print("ERROR: " + msg)
    time.sleep(2)
    input("Continue...")
    os.system(CLEAR_COMMAND)

def get_id():
    return str(int(time.time()))

def get_date():
    return datetime.datetime.now().strftime("'%Y-%m-%d %H:%M:%S'")

def create_user(username, password):
    new_user = {
        'id':get_id(),
        'username': username,
        'password': password,
        'admin':False,
        'created':get_date(),
        'approved':False,
        'rented': []
    }

    return new_user

def create_book(book_title, book_genre, book_amount):
    book_id = get_id()
    copies = []
    for i in range(int(book_amount)):
        copy_id = book_id + f"_{i}"
        new_copy = {
            "id": copy_id,
            "title": book_title,
            "genre": book_genre
        }

        copies.append(new_copy)
    
    book_sn = len(copies)
    new_book = {
        "id": book_id,
        "sn": book_sn,
        "title": book_title,
        "genre": book_genre,
        "copies_count": book_sn,
        "copies":copies,
        "rentedBy":[]
    }

    return new_book

USERS = fetch_data('users')
BOOKS = fetch_data('books')

def is_login_correct(username, password):
    err_msg = ""
    if username == "":
        err_msg += "Username must be filled\n"
    
    if password == "":
        err_msg += "Password must be filled"
    
    if err_msg != "":
        return (False, err_msg)

    else:
        return (True, err_msg)

def is_register_correct(username, password, password_repeat):
    err_msg = ""
    if username == "":
        err_msg += "Username must be filled\n"
    
    elif len(username) > 20:
        err_msg += "Username must be shorter than 20 symbols\n"
    
    else:
        flag = True
        for user in USERS:
            if user['username'] == username:
                flag = False
                break
        
        if not flag:
            err_msg += "Username has been taken\n"
    
    if password == "":
        err_msg += "Password must be filled\n"
    
    elif len(password) < 8:
        err_msg += "Password must be longer than 8 symbols\n"
    
    elif password != password_repeat:
        err_msg += "Password is not correctly repeated\n"

    if err_msg != "":
        return (False, err_msg)

    else:
        return (True, err_msg)

def is_action_correct(action, data_count, page, data_type):
    page_count = math.ceil(data_count / 5)
    err_msg = ""
    if action == "":
        err_msg = "Action must be filled"
    
    elif action.lower() == "e":
        pass

    elif action.isdigit():
        if int(action) > data_count - 1:
            if data_type == 'u':
                err_msg = "Invalid User Number"
            
            if data_type == 'b':
                err_msg = "Invalid Book Number"
        
        else:
            pass
    
    elif action.startswith("p"):
        temp = action[1:]
        if temp == "":
            err_msg = "[p] command must be provided with a number"
        
        elif temp in ["n", "p"]:
            if page == 1 and temp == "p":
                err_msg = "You can't go to the previous page from the first one"

            elif page == page_count and temp == "n":
                err_msg = "You can't go to the next page from the last one"
            
            elif data_count == 0:
                err_msg = "No page is available"

            else:
                pass

        elif temp.isdigit():
            if int(temp) > page_count or int(temp) <= 0:
                err_msg = "Invalid Page Number"
            
            elif data_count == 0:
                err_msg = "No page is available"

            else:
                pass
        
        else:
            err_msg = "Invalid [p] Command"
        
    else:
        err_msg = "Invalid Action"

    if err_msg != "":
        return (False, err_msg)

    else:
        if action.isdigit():
            succ_msg = "data_number"
        
        if action.startswith("p"):
            succ_msg = "page"
        
        return (True, succ_msg)

def is_book_correct(book_title, book_genre, book_amount):
    err_msg = ""
    if book_title == "":
        err_msg += "Book title must be filled"

    for book in BOOKS:
        if book['title'] == book_title:
            err_msg += "Book title already exists"
            break
    
    if book_genre == "":
        err_msg += "Book genre must be filled"
    
    elif len(book_genre) > 20:
        err_msg += "Book genre must be shorter than 20 symbols"
    
    else:
        pass

    if book_amount == "":
        err_msg += "Book amount must be filled"
    
    elif not book_amount.isdigit():
        err_msg += "Book amount must be a number"
    
    elif int(book_amount) <= 0:
        err_msg += "Book amount must be greater than 0"
    
    elif int(book_amount) > 20:
        err_msg += "Book amount must be less than 20"

    else:
        pass

    if err_msg != "":
        return (False, err_msg)
    
    else:
        return (True, err_msg)
    
def is_book_removable(book_number):
    book = BOOKS[book_number]
    err_msg = ""
    if len(book['copies']) == book['copies_count']:
        return (True, err_msg)
    
    else:
        err_msg = "Action not possible, there is/are rented copies"
        return (False, err_msg)

def is_book_action_correct(action, book_number):
    book = BOOKS[book_number]
    err_msg = ""
    succ_msg = ""
    if action == "":
        err_msg = "Action must be filled"
    
    elif action.startswith("rmc"):
        temp = action[3:]
        if temp == "":
            err_msg = "[rmc] command must be provided with a number"
        
        elif not temp.isdigit():
            err_msg = "[rmc] command must contain only numbers"
        
        elif int(temp) <= 0 or int(temp) >= len(book['copies']):
            err_msg = "[rmc] command number is invalid"
        
        else:
            pass

    elif action.startswith("c"):
        temp = action[1:]
        if temp == "":
            err_msg = "[c] command must be provided with a number"
        
        elif not temp.isdigit():
            err_msg = "[c] command must contain only numbers"
        
        elif int(temp) <= 0 or int(temp) >= 10:
            err_msg = "[c] command number is invalid"
        
        else:
            pass
    
    elif action.startswith("title:"):
        temp = action[6:]
        if temp == "":
            err_msg = "[title] command must be provided with a title"
        
        elif book['copies_count'] != len(book['copies']):
            err_msg = "[title] command not possible, there is/are rented copies"

        else:
            pass
    
    elif action.startswith("genre:"):
        temp = action[6:]
        if temp == "":
            err_msg = "[genre] command must be provided with a genre"
        
        elif book['copies_count'] != len(book['copies']):
            err_msg = "[genre] command not possible, there is/are rented copies"

        elif len(temp) > 20:
            err_msg = "New genre must be shorter than 20 symbols"

        else:
            pass
    
    else:
        err_msg = "Invalid Action"
    
    if err_msg != "":
        return (False, err_msg)
    
    else:
        return (True, err_msg)

def has_user_rented(user_name, book):
    for rent in book['rentedBy']:
        if rent['username'] == user_name:
            return True
    
    return False

def is_rent_action_correct(action, user_name, page, data_count):
    for index, user in enumerate(USERS):
        if user['username'] == user_name:
            user_number = index

    page_count = math.ceil(data_count / 5)
    flag = False
    err_msg = ""
    if action == "":
        err_msg = "Action must be filled"
    
    elif action.lower() == "e":
        pass

    elif action.startswith("p"):
        temp = action[1:]
        if temp == "":
            err_msg = "[p] command must be provided with a number"
        
        elif temp in ["n", "p"]:
            if page == 1 and temp == "p":
                err_msg = "You can't go to the previous page from the first one"

            elif page == page_count and temp == "n":
                err_msg = "You can't go to the next page from the last one"
            
            elif data_count == 0:
                err_msg = "No page is available"

            else:
                pass

        elif temp.isdigit():
            if int(temp) > page_count or int(temp) <= 0:
                err_msg = "Invalid Page Number"
            
            elif data_count == 0:
                err_msg = "No page is available"

            else:
                pass
        
        else:
            err_msg = "Invalid [p] Command"

    else:
        for book in BOOKS:
            if book['id'] == action and len(book['copies']) != 0:
                if has_user_rented(user_name, book):
                    err_msg = "Book has already been rented"
                    flag = True
                    break

                elif len(USERS[user_number]['rented']) >= 5:
                    err_msg = "Can't rent more than five books at a time."
                    flag = True
                    break

                else:
                    flag = True
                    break
                    
        if not flag:
            err_msg = "Invalid Id"

    if err_msg != "":
        return (False, err_msg)

    else:
        return (True, err_msg)

def create_copies(book_number, amount):
    book = BOOKS[book_number]
    book_sn = book["sn"]
    for i in range(amount):
        copy_id = book["id"] + f"_{book_sn}"
        new_copy = {
            "id": copy_id,
            "title": book['title'],
            "genre": book['genre']
        }

        book["copies"].append(new_copy)
        book_sn += 1
    
    book["copies_count"] += amount
    book["sn"] = book_sn

    return book

def control_book(action, book_number):
    book = BOOKS[book_number]
    if action.startswith("rmc"):
        temp = int(action[3:])
        book['copies'] = book['copies'][temp:]
        book['copies_count'] -= temp
    
    elif action.startswith("c"):
        temp = int(action[1:])
        book = create_copies(book_number, temp)
    
    elif action.startswith("title:"):
        temp = action[6:]
        book['title'] = temp
        for copy in book['copies']:
            copy['title'] = temp
    
    elif action.startswith("genre:"):
        temp = action[6:]
        book['genre'] = temp
        for copy in book['copies']:
            copy['genre'] = temp
        
    BOOKS[book_number] = book
    post_data(BOOKS, 'books')

    return book

def is_users_action_correct(action, user):
    err_msg = ""
    if action == "":
        err_msg = "Action must be filled"
    
    elif action in ["rm", "rmrall"]:
        pass

    elif action.startswith("rmr"):
        temp = action[3:]
        if temp == "":
            err_msg = "[rmr] command must be provided with a number"
    
        else:
            flag = False
            for rent in user["rented"]:
                if temp == rent["id"]:
                    flag = True
                    break
            
            if not flag:
                err_msg = "Invalid Id"

    elif action.startswith("username:"):
        temp = action[9:]
        if temp == "":
            err_msg = "Username must be filled"
        
        elif len(temp) > 20:
            err_msg = "Username must be shorter than 20 symbols"
        
        else:
            for u in USERS:
                if u['username'] == temp:
                    err_msg = "Username is already taken"
                    break

                elif u['username'] == temp and len(temp['rented']) != 0:
                    err_msg = "Action not possible, user has rented copies"
                    break
    
    elif action.startswith("password:"):
        temp = action[9:]
        if temp == "":
            err_msg = "Password must be filled"
        
        elif len(temp) < 8:
            err_msg = "Password must be longer than 8 symbols"
        
        else:
            pass

    else:
        err_msg = "Invalid Action"
    
    if err_msg != "":
        return (False, err_msg)
    
    else:
        return (True, err_msg)

def add_rented(rented_id, user_name):
    for index, u in enumerate(USERS):
        if u['username'] == user_name:
            user_number = index
            break

    for index, b in enumerate(BOOKS):
        if b['id'] == rented_id:
            book = BOOKS[index]['copies'].pop(0)
            BOOKS[index]['rentedBy'].append({
                "username": user_name,
                "id": book["id"]
            })
            break
    
    USERS[user_number]["rented"].append({
        "id": book["id"],
        "title": book["title"],
        "genre": book["genre"]
    })

def remove_rented(rented_book, user_number):
    rented_id = rented_book['id']
    for index, rent in enumerate(USERS[user_number]['rented']):
        if rent['id'] == rented_id:
            USERS[user_number]['rented'].remove(rent)

    book_id = rented_book['id'].split('_')[0]
    for index, book in enumerate(BOOKS):
        if book['id'] == book_id:
            book['copies'].append(rented_book)
            for index, user in enumerate(book['rentedBy']):
                if user['username'] == USERS[user_number]['username']:
                    book['rentedBy'].remove(user)

def remove_all_rented(user_number):
    rented_list = deepcopy(USERS[user_number]['rented'])
    for rent in rented_list:                      
        remove_rented(rent, user_number)

def remove_user(user_number, users):
    user = users[user_number]
    main_user_id = ""
    for index, u in enumerate(USERS):
        if u['id'] == user['id']:
            main_user_number = index

    for rent in user["rented"]:
        remove_rented(rent, main_user_number)
    
    del USERS[main_user_number]

def change_credential(user_number, credential, credential_type):
    if credential_type == 'u':
        USERS[user_number]['username'] = credential
    
    elif credential_type == 'p':
        USERS[user_number]['password'] = hashlib.sha1(credential.encode("utf-8")).hexdigest()
#FUNCTIONS

#SCREENS

#MENU SCREEN
def menu_screen():
    while True:
        os.system(CLEAR_COMMAND)
        print("-"*20)
        print("Book Rental")
        print("-"*20)
        print("\n\nActions:")
        print("-"*20)
        print("1. Login")
        print("-"*20)
        print("2. Register")
        print("-"*20)
        print("3. Exit")
        print("-"*20)
        choice = input("\n:")
        if choice == "1":
            login_screen()

        elif choice == "2":
            register_screen()

        elif choice == "3":
            break

        else:
            error_message("Invalid Option")
#MENU SCREEN

#LOGIN SCREEN
def login_screen():
    while True:
        os.system(CLEAR_COMMAND)
        print("-"*20)
        print("Book Rental")
        print("-"*20)
        print("\n\nLogin Page")
        print("-"*20)

        username = input("Username('e' to exit):")
        if username.lower() == 'e':
            break

        password = pwinput(prompt="Password('e' to exit):")
        if password.lower() == 'e':
            break

        response = is_login_correct(username, password)

        if response[0]:
            password = hashlib.sha1(password.encode("utf-8")).hexdigest()
            flag = False
            for user in USERS:
                if user['username'] == username and user['password'] == password and user['approved']:
                    flag = True
                    is_admin = user['admin']
                    user_id = user['id']
                    user_name = user['username']
                    break
            
            if flag:
                user_interface(user_id, is_admin, user_name)
                break

            else:
                error_message("Username or Password is wrong")
        
        else:
            error_message(response[1])
#LOGIN SCREEN

#REGISTER SCREEN
def register_screen():
    exit_flag = False

    while True:
        os.system(CLEAR_COMMAND)
        print("-"*20)
        print("Book Rental")
        print("-"*20)
        print("\n\nRegister Page")
        print("-"*20)

        print("\nUsername must be shorter than 15 symbols")
        username = input("Username('e' to exit):")
        if username.lower() == 'e':
            break

        print("\nPassword must be longer than 8 symbols")
        password = pwinput(prompt="Password('e' to exit):")
        if password.lower() == 'e':
            break

        print("\nRepeat password")
        password_repeat = pwinput(prompt="Password('e' to exit):")
        if password_repeat.lower() == 'e':
            break

        response = is_register_correct(username, password, password_repeat)

        if response[0]:
            password = hashlib.sha1(password.encode("utf-8")).hexdigest()
            new_user = create_user(username, password)
            USERS.append(new_user)
            post_data(USERS, 'users')
            
            print("\nAccount will be created after being approved by an admin")
            time.sleep(2)
            input("Continue...")
            break

        else:
            error_message(response[1])
#REGISTER SCREEN

#USER INTERFACE
def user_interface(user_id, is_admin, user_name):
    if is_admin:
        admin_screen(user_id, user_name)
    
    else:
        user_screen(user_id, user_name)
#USER INTERFACE

#USER SCREEN
def user_screen(user_id, user_name):
    while True:
        os.system(CLEAR_COMMAND)
        print("-"*20)
        print("Book Rental")
        print("-"*20)
        print(f"\n\nWelcome {user_name}")
        print("-"*20)

        print("\n\nActions")
        print("-"*20)
        print(f"1. Rented Books")
        print("-"*20)
        print(f"2. Books List")
        print("-"*20)
        print("3. Exit")
        print("-"*20)

        choice = input("\n:")
        if choice == "1":
            rented_books(user_name)

        elif choice == "2":
            user_books_list(user_name)

        elif choice == "3":
            break

        else:
            error_message("Invalid Option")
#USER SCREEN

#RENTED BOOKS SCREEN
def rented_books(user_name):
    for index, u in enumerate(USERS):
        if u['username'] == user_name:
            user_number = index
            break

    while True:
        os.system(CLEAR_COMMAND)
        print("-"*20)
        print("Book Rental")
        print("-"*20)
        print(f"\n\nWelcome {user_name}")
        print("-"*20)

        if len(USERS[user_number]['rented']) == 0:
            print("No rented books")
        
        else:
            print('[')
            for index, rented in enumerate(USERS[user_number]['rented']):
                print(f"\n{index}\n")
                for key, value in rented.items():
                    print(f'{key} : {value}')
            
            print('\n]')
        
        print("\n" + "-"*20)
        print('''[number]: remove book from rented list
e: exit''')
        action = input("\nAction:")
        if action == "":
            error_message("Action must be filled")
        
        elif action.lower() == "e":
            break

        elif action.isdigit():
            if (int(action) < 0 or int(action) >= len(USERS[user_number]['rented'])) or len(USERS[user_number]['rented']) == 0:
                error_message("Invalid number")
            
            else:
                temp = int(action)
                remove_rented(USERS[user_number]['rented'][temp], user_number)
                post_data(USERS, 'users')
                post_data(BOOKS, 'books')

                print("\nBook has been removed from the list")
                time.sleep(2)
                input("Continue...")
        
        else:
            error_message("Invalid Action")
#RENTED BOOKS SCREEN

#USER BOOKS LIST
def user_books_list(user_name):
    page = 1
    while True:
        os.system(CLEAR_COMMAND)
        print("-"*20)
        print("Book Rental")
        print("-"*20)
        print(f"\n\nWelcome {user_name}")
        print("-"*20)

        print("\n\nBooks List")
        print("-"*20)
        pagination(page, len(BOOKS), BOOKS, 'ub')

        print("\n" + "-"*20)
        print('''[id]: rent the book with the id
p[number]: get to chosen page
pn: get to next page
pp: get to previous page
e: exit''')
        action = input("\nAction:")
        if action == "":
            error_message("Action must be filled")
        
        if action == "e":
            break
        
        response = is_rent_action_correct(action, user_name, page, len(BOOKS))
        if response[0]:
            if action == "pn":
                page += 1
            
            elif action == "pp":
                page -= 1
            
            elif action.startswith("p"):
                temp = action[1:]
                page = int(temp)
            
            else:
                add_rented(action, user_name)
                post_data(USERS, 'users')
                post_data(BOOKS, 'books')

                print("\nNew book has been rented")
                time.sleep(2)
                input("Continue...")

        else:
            error_message(response[1])
#USER BOOK LIST

#ADMIN SCREEN
def admin_screen(user_id, user_name):
    unapproved_users = []
    for user in USERS:
        if not user['approved']:
            unapproved_users.append(user)
    
    while True:
        os.system(CLEAR_COMMAND)
        print("-"*20)
        print("Book Rental")
        print("-"*20)
        print(f"\n\nWelcome {user_name}")
        print("-"*20)

        print("\n\nActions")
        print("-"*20)
        print(f"1. Approve Users[Count: {len(unapproved_users)}]")
        print("-"*20)
        print(f"2. Control Users")
        print("-"*20)
        print(f"3. Books List")
        print("-"*20)
        print("4. Exit")
        print("-"*20)

        choice = input("\n:")
        if choice == "1":
            approve_users(user_name, unapproved_users)

        elif choice == "2":
            users_screen(user_name)

        elif choice == "3":
            books_list(user_name)

        elif choice == "4":
            break

        else:
            error_message("Invalid Option")
#ADMIN SCREEN

#USERS SCREEN
def users_screen(user_name):
    users = []
    page = 1
    for user in USERS:
        if user['username'] != user_name:
            users.append(user)

    while True:
        os.system(CLEAR_COMMAND)
        print("-"*20)
        print("Book Rental")
        print("-"*20)
        print(f"\n\nWelcome {user_name}")
        print("-"*20)

        print("\n\nUsers List")
        print("-"*20)
        pagination(page, len(users), users, 'u')

        print("\n" + "-"*20)
        print('''[number]: select user
p[number]: get to chosen page
pn: get to next page
pp: get to previous page
e: exit''')
        action = input("\nAction:")
        if action == "":
            error_message("Action must be filled")
        
        if action == "e":
            break

        response = is_action_correct(action, len(users), page, 'u')

        if response[0]:
            if response[1] == "data_number":
                user_number = int(action)
                users_sub_screen(user_name, user_number, users)

            else:
                if action == "pn":
                    page += 1
                
                elif action == "pp":
                    page -= 1
                
                else:
                    temp = action[1:]
                    page = int(temp)

        else:
            error_message(response[1])
#USERS SCREEN

#USERS SUB SCREEN
def users_sub_screen(user_name, user_number, users):
    while True:
        os.system(CLEAR_COMMAND)
        print("-"*20)
        print("Book Rental")
        print("-"*20)
        print(f"\n\nWelcome {user_name}")
        print("-"*20)

        print(f"\n\nUser Name:{users[user_number]['username']}")
        print("-"*20)

        for key, value in users[user_number].items():
            if key in ["approved", "password"]:
                continue

            if key == "rented":
                if len(value) == 0:
                    print(f'{key} : None')
                
                else:
                    print(f'{key} :')
                    print('[\n\n')
                    for v in value:
                        for key2, value2 in v.items():
                            print(f'{key2} : {value2}')

                        print('\n')

                    print(']')
                continue
            
            else:
                print(f'{key} : {value}')

        print("\n" + "-"*20)
        print('''rm: remove the user
username:[username]: change the username
password:[password]: change the password
rmrall: remove all rented books
rmr[id]: remove a rented book with the specified id
admin: make the user admin
e: exit''')
        action = input("\nAction:")
        if action == "":
            error_message("Action must be filled")
        
        if action == "e":
            break

        if action == "admin":
            for index, u in enumerate(USERS):
                if u['username'] == users[user_number]['username']:
                    u['admin'] = True
                    main_number = index
                    break
            
            remove_all_rented(main_number)
            users[user_number]['rented'].clear()
            users[user_number]['admin'] = True
            post_data(USERS, 'users')
            post_data(BOOKS, 'books')

            print("\nUser has been made the admin")
            time.sleep(2)
            input("Continue...")

        else:
            user = users[user_number]
            response = is_users_action_correct(action, user)
            if response[0]:
                for index, u in enumerate(USERS):
                    if u['id'] == users[user_number]['id']:
                        main_user_number = index
                        break

                if action == "rm":
                    remove_user(user_number, users)
                    del users[user_number]
                    post_data(USERS, 'users')
                    post_data(BOOKS, 'books')

                    print("\nUser has been removed")
                    time.sleep(2)
                    input("Continue...")
                    break

                elif action == "rmrall":    
                    remove_all_rented(main_user_number)
                    users[user_number]['rented'].clear()
                    succ_msg = "\nAll rented books has been removed"
                
                elif action.startswith("rmr"):
                    temp = action[3:]
                    
                    for rent in users[user_number]['rented']:
                        if rent['id'] == temp:
                            remove_rented(rent, main_user_number)
                            succ_msg = "\nRented book has been removed"
                            break
                
                elif action.startswith("username:"):
                    temp = action[9:]
                    change_credential(main_user_number, temp, 'u')
                    users[users.index(USERS[main_user_number])]['username'] = temp
                    succ_msg = "\nUsername has been changed"

                elif action.startswith("password:"):
                    temp = action[9:]
                    change_credential(main_user_number, temp, 'p')
                    users[users.index(USERS[main_user_number])]['password'] = hashlib.sha1(temp.encode("utf-8")).hexdigest()
                    succ_msg = "\nPassword has been changed"
                
                post_data(USERS, 'users')
                post_data(BOOKS, 'books')
                print(succ_msg)
                time.sleep(2)
                input("Continue...")

            else:
                error_message(response[1])
#USERS SUB SCREEN

#APPROVE USERS SCREEN
def approve_users(user_name, unapproved_users):
    page = 1
    while True:
        os.system(CLEAR_COMMAND)
        print("-"*20)
        print("Book Rental")
        print("-"*20)
        print(f"\n\nWelcome {user_name}")
        print("-"*20)

        print("\n\nUnapproved Users")
        print("-"*20)
        pagination(page, len(unapproved_users), unapproved_users, 'uu')
        
        print("\n" + "-"*20)
        print('''[number]: select user
p[number]: get to chosen page
pn: get to next page
pp: get to previous page
e: exit''')
        action = input("\nAction:")
        if action == "":
            error_message("Action must be filled")
        
        if action == "e":
            break
        
        response = is_action_correct(action, len(unapproved_users), page, 'u')
        if response[0]:
            if response[1] == "data_number":
                user_number = int(action)
                approve_users_sub(user_number, unapproved_users, user_name)

            else:
                if action == "pn":
                    page += 1
                
                elif action == "pp":
                    page -= 1
                
                else:
                    temp = action[1:]
                    page = int(temp)

        else:
            error_message(response[1])
#APPROVE USERS SCREEN

#APPROVE USER SUB SCREEN
def approve_users_sub(user_number, unapproved_users, user_name):
    flag = False
    while True:
        os.system(CLEAR_COMMAND)
        print("-"*20)
        print("Book Rental")
        print("-"*20)
        print(f"\n\nWelcome {user_name}")
        print("-"*20)

        print(f"\n\nUnapproved User {unapproved_users[user_number]['username']}")
        print("-"*20)

        print(f"\nApprove the user?(y, n or e for 'exit')")
        choice = input(':')

        if choice == "":
            error_message("Make a choice")
        
        elif choice.lower() == "y":
            user_id = unapproved_users[user_number]['id']
            unapproved_users.pop(user_number)

            for user in USERS:
                if user['id'] == user_id:
                    user['approved'] = True
                    flag = True
                    post_data(USERS, 'users')
                    break
            
        elif choice.lower() == "n":
            user_id = unapproved_users[user_number]['id']
            unapproved_users.pop(user_number)

            for index, user in enumerate(USERS):
                if user['id'] == user_id:
                    USERS.pop(index)
                    flag = True
                    post_data(USERS, 'users')
                    break
        
        elif choice.lower() == "e":
            flag = True

        else:
            error_message("Invalid Choice")
        
        if flag:
            break
#APPROVE USER SUB SCREEN

#BOOKS LIST
def books_list(user_name):
    page = 1
    while True:
        os.system(CLEAR_COMMAND)
        print("-"*20)
        print("Book Rental")
        print("-"*20)
        print(f"\n\nWelcome {user_name}")
        print("-"*20)

        print("\n\nBooks List")
        print("-"*20)
        pagination(page, len(BOOKS), BOOKS, 'b')

        print("\n" + "-"*20)
        print('''[number]: select book
p[number]: get to chosen page
pn: get to next page
pp: get to previous page
add: add a new book
e: exit''')
        action = input("\nAction:")
        if action == "":
            error_message("Action must be filled")
        
        if action == "e":
            break

        if action.lower() == "add":
            add_book_screen(user_name)

        else:
            response = is_action_correct(action, len(BOOKS), page, 'b')
            if response[0]:
                if response[1] == "data_number":
                    book_number = int(action)
                    book(book_number, user_name)

                else:
                    if action == "pn":
                        page += 1
                    
                    elif action == "pp":
                        page -= 1
                    
                    else:
                        temp = action[1:]
                        page = int(temp)

            else:
                error_message(response[1])
#BOOKS LIST

#BOOK
def book(book_number, user_name):
    flag = False
    book = BOOKS[book_number]
    while True:
        os.system(CLEAR_COMMAND)
        print("-"*20)
        print("Book Rental")
        print("-"*20)
        print(f"\n\nWelcome {user_name}")
        print("-"*20)

        print(f"\n\nTitle: {book['title']}")
        print("-"*20)

        print('\n')
        for key, value in book.items():
            if key == "sn":
                continue

            if key in ['rentedBy', "copies"]:
                print(key + ':')
                print('[\n')
                for k in value:
                    for key2, value2 in k.items():
                        print(f'{key2} : {value2}')
                    
                    print('\n')
                print(']\n')
            
            else:
                print(f'{key} : {value}')
        
        print("\n" + "-"*20)
        print('''rm: remove the book
rmc[number]: remove an amount of copies
title:[title]: change the title
genre:[genre]: change the genre
c[number]: add an amount of copies
e: exit''')
        action = input("\nAction:")
        if action == "":
            error_message("Action must be filled")

        elif action.lower() == "e":
            break
            
        elif action.lower() == "rm":
            response = is_book_removable(book_number)
            if response[0]:
                del BOOKS[book_number]
                post_data(BOOKS, 'books')

                print("\nBook has been removed")
                time.sleep(2)
                input("Continue...")
                break

            else:
                error_message(response[1])
        
        else:
            response = is_book_action_correct(action, book_number)
            if response[0]:
                book = control_book(action, book_number)

                print("\nChanges has been made succesfully")
                time.sleep(2)
                input("Continue...")

            else:
                error_message(response[1])
#BOOK

#ADD BOOK SCREEN
def add_book_screen(user_name):
    while True:
        os.system(CLEAR_COMMAND)
        print("-"*20)
        print("Book Rental")
        print("-"*20)
        print(f"\n\nWelcome {user_name}")
        print("-"*20)

        print('\n')
        book_title = input("Book Title('e' to exit):")
        if book_title.lower() == 'e':
            break
        
        print("\nBook genre must be shorter than 20 symbols")
        book_genre = input("Book Genre('e' to exit):")
        if book_genre.lower() == 'e':
            break

        print("\nBook amount must be greater than 1")
        book_amount = input("Book Amount('e' to exit):")
        if book_amount.lower() == 'e':
            break

        response = is_book_correct(book_title, book_genre, book_amount)

        if response[0]:
            new_book = create_book(book_title, book_genre, book_amount)
            BOOKS.append(new_book)
            post_data(BOOKS, 'books')

            print("\nNew Book added successfully!")
            time.sleep(2)
            input("Continue...")
            break

        else:
            error_message(response[1])
#ADD BOOK SCREEN

#PAGINATION
def pagination(page, data_count, data, data_type):
    page_count = math.ceil(data_count/5)
    element_start = page * 5 - 5
    element_end = min(page * 5, data_count)
    for i in range(element_start, element_end):
        if len(data) == 0:
            break

        print(f"\n{i}.\n")
        for key, value in data[i].items():
            if data_type == 'u':
                if key in ["approved", "password"]:
                    continue

                if key == "rented":
                    if len(value) == 0:
                        print(f'{key} : None')
                    
                    else:
                        print(f'{key} :')
                        print('[\n\n')
                        for v in value:
                            for key2, value2 in v.items():
                                print(f'{key2} : {value2}')

                            print('\n')

                        print(']')
                    continue
                
                else:
                    print(f'{key} : {value}')

            if data_type == 'uu':
                if key in ["password", "rented"]:
                    continue
                    
                else:
                    print(f'{key} : {value}')
                
            if data_type == 'b':
                if key in ["sn", "copies"]:
                    continue
                
                if key == "rentedBy":
                    print(key + ':')
                    print('[\n')
                    for k in value:
                        for key2, value2 in k.items():
                            print(f'{key2} : {value2}')
                        
                        print('\n')
                    print(']\n')
                
                else:
                    print(f'{key} : {value}')
            
            if data_type == 'ub':
                if key in ['sn', 'copies_count', 'rentedBy']:
                    continue

                if key == 'copies':
                    print(f"Amount : {len(value)}")
                
                else:
                    print(f'{key} : {value}')

    print("\n")
    print("||", end="")
    if data_count == 0:
        print(" None ||")
    
    else:
        temp = page // 5
        if page % 5 == 0:
            temp -= 1

        if element_start != 0 and temp != 0:
            print("<< ", end="")

        for i in range(temp * 5 + 1, (temp + 1) * 5 + 1):
            if i > page_count:
                break

            if i == page:
                print(f" |{i}| ", end="")
            
            else:
                print(f" {i} ", end="")
        
        temp2 = page_count // 5
        if page_count % 5 == 0:
            temp2 -= 1

        if temp != temp2:
            print(f" ...{page_count} >>", end="")

        print("||")
#PAGINATION
def main():
    menu_screen()

if __name__ == "__main__":
    main()
