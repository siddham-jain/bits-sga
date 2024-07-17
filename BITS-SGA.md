##### Question 1

Shell script:- 
```
#!/bin/bash

# Student ID
STUDENT_ID="2023ebcs655"

# Check if the file exists
if [ ! -f "fruits.txt" ]; then
    echo "File fruits.txt not found! # Student ID: $STUDENT_ID"
    exit 1
fi

# Function to display fruit name and total price for 0 arguments
function display_all_fruits() {
    while IFS=" " read -r name vitamin price quantity; do
        total_price=$((price * quantity))
        echo "$name $total_price # Student ID: $STUDENT_ID"
    done < fruits.txt
}

# Function to display fruits with a specific vitamin and their total price
function display_fruits_with_vitamin() {
    local vitamin=$1
    local total_price=0
    local fruits=()

    while IFS=" " read -r name vitamin_code price quantity; do
        if [ "$vitamin_code" == "$vitamin" ]; then
            fruits+=("$name")
            total_price=$((total_price + (price * quantity)))
        fi
    done < fruits.txt

    if [ ${#fruits[@]} -eq 0 ]; then
        echo "No fruits found with vitamin $vitamin # Student ID: $STUDENT_ID"
    else
        echo "${fruits[@]}"
        echo "$total_price # Student ID: $STUDENT_ID"
    fi
}

# Function to display fruits with a unit price less than or equal to a given price
function display_fruits_with_price() {
    local max_price=$1

    while IFS=" " read -r name vitamin price quantity; do
        if [ "$price" -le "$max_price" ]; then
            echo "$name # Student ID: $STUDENT_ID"
        fi
    done < fruits.txt
}

# Function to display vitamin and unit price of a specific fruit
function display_fruit_info() {
    local fruit_name=$1
    local found=0

    while IFS=" " read -r name vitamin price quantity; do
        if [ "${name,,}" == "${fruit_name,,}" ]; then
            echo "$vitamin $price # Student ID: $STUDENT_ID"
            found=1
            break
        fi
    done < fruits.txt

    if [ $found -eq 0 ]; then
        echo "No fruit found with the name $fruit_name # Student ID: $STUDENT_ID"
    fi
}

# Main script logic
if [ $# -eq 0 ]; then
    display_all_fruits
elif [ $# -eq 1 ]; then
    arg=$1
    if [[ "$arg" =~ ^[A-Za-z]$ ]]; then
        display_fruits_with_vitamin "$arg"
    elif [[ "$arg" =~ ^[0-9]+$ ]]; then
        display_fruits_with_price "$arg"
    else
        display_fruit_info "$arg"
    fi
else
    echo "Error: Invalid number of arguments. # Student ID: $STUDENT_ID"
fi
```

1. With no arguments ```./info.sh```
![[Pasted image 20240716230342.png]]
2. Single character argument (vitamin): ```./info.sh C```
![[Pasted image 20240716230530.png]]
3. Number argument (price):  ```./info.sh 50```
![[Pasted image 20240716230649.png]]
4. String argument (fruit name): ```./info.sh Mango```
![[Pasted image 20240716230845.png]]
5. Invalid number of arguments: ```./info.sh arg1 arg2```
![[Pasted image 20240716231104.png]]

##### Question 2

shell script: 
```
#!/bin/bash

# Student ID: 2023ebcs655

# File to store the to-do list
TODO_FILE="todo.txt"

# Ensure the todo.txt file exists
if [ ! -f "$TODO_FILE" ]; then
    touch "$TODO_FILE"
fi

# Function to print all tasks that are not completed
function print_not_completed_tasks() {
    awk '$3 != 2' "$TODO_FILE"
}

# Function to display all tasks with their serial numbers
function display_all_tasks() {
    cat "$TODO_FILE"
}

# Function to add a new task
function add_task() {
    task=$1
    serial_number=$(($(wc -l < "$TODO_FILE") + 1))
    echo "$serial_number $task 0" >> "$TODO_FILE"
    echo "Task added: $task # Student ID: 2023ebcs655"
}

# Function to print tasks not started yet
function print_not_started_tasks() {
    awk '$3 == 0' "$TODO_FILE"
}

# Function to print tasks in progress
function print_doing_tasks() {
    awk '$3 == 1' "$TODO_FILE"
}

# Function to print completed tasks
function print_completed_tasks() {
    awk '$3 == 2' "$TODO_FILE"
}

# Function to mark tasks as complete based on serial numbers
function mark_tasks_complete() {
    for serial_number in "$@"; do
        sed -i "${serial_number}s/ [0-1]\$/ 2/" "$TODO_FILE"
        echo "Marked task $serial_number as complete # Student ID: 2023ebcs655"
    done
}

# Function to delete tasks based on serial numbers
function delete_tasks() {
    for serial_number in "$@"; do
        sed -i "${serial_number}d" "$TODO_FILE"
        echo "Deleted task $serial_number # Student ID: 2023ebcs655"
    done
}

# Main script logic based on arguments
case "$1" in
    "")
        print_not_completed_tasks
        ;;
    "display")
        display_all_tasks
        ;;
    "add")
        if [ -z "$2" ]; then
            echo "Error: No task provided to add. # Student ID: 2023ebcs655"
        else
            add_task "$2"
        fi
        ;;
    "todo")
        print_not_started_tasks
        ;;
    "doing")
        print_doing_tasks
        ;;
    "complete")
        print_completed_tasks
        ;;
    "mark_complete")
        shift
        if [ $# -eq 0 ]; then
            echo "Error: No serial numbers provided to mark as complete. # Student ID: 2023ebcs655"
        else
            mark_tasks_complete "$@"
        fi
        ;;
    "delete")
        shift
        if [ $# -eq 0 ]; then
            echo "Error: No serial numbers provided to delete. # Student ID: 2023ebcs655"
        else
            delete_tasks "$@"
        fi
        ;;
    *)
        echo "Error: Invalid argument. # Student ID: 2023ebcs655"
        ;;
esac

```

1. Add a new task:
![[Pasted image 20240716231722.png]]
2. Print all tasks that are not completed:
![[Pasted image 20240716231754.png]]
3. Display all tasks with their serial numbers:
![[Pasted image 20240716231828.png]]
4. Print all tasks not started yet:
![[Pasted image 20240716231901.png]]
5. Print all tasks in progress:
![[Pasted image 20240716231940.png]]
6. Mark tasks as complete:
![[Pasted image 20240716232008.png]]
7. Delete tasks:
![[Pasted image 20240716232036.png]]

##### Question 3

shell script:-
```
#!/bin/bash

# Student ID: 2023ebcs655

# File to store the student data
DATA_FILE="data.txt"

# Ensure the data.txt file exists
if [ ! -f "$DATA_FILE" ]; then
    touch "$DATA_FILE"
fi

# Function to add a student record
add_student_record() {
    while true; do
        echo -n "Enter Roll Number (4 digits): "
        read roll_number
        if [[ ! "$roll_number" =~ ^[0-9]{4}$ ]]; then
            echo "Invalid Roll Number. Please enter a 4-digit number."
            continue
        fi

        echo -n "Enter Name: "
        read name
        if [[ -z "$name" ]]; then
            echo "Name cannot be empty. Please enter a valid name."
            continue
        fi

        echo -n "Enter Marks in History (0-100): "
        read history
        if [[ ! "$history" =~ ^[0-9]{1,2}$|100 ]]; then
            echo "Invalid marks. Please enter a number between 0 and 100."
            continue
        fi

        echo -n "Enter Marks in Geography (0-100): "
        read geography
        if [[ ! "$geography" =~ ^[0-9]{1,2}$|100 ]]; then
            echo "Invalid marks. Please enter a number between 0 and 100."
            continue
        fi

        echo -n "Enter Marks in Civics (0-100): "
        read civics
        if [[ ! "$civics" =~ ^[0-9]{1,2}$|100 ]]; then
            echo "Invalid marks. Please enter a number between 0 and 100."
            continue
        fi

        echo "$roll_number $name $history $geography $civics" >> "$DATA_FILE"
        echo "Student record added successfully. # Student ID: 2023ebcs655"
        break
    done
}

# Function to print students above passing marks
print_students_above_passing() {
    echo "Students above passing marks: # Student ID: 2023ebcs655"
    awk '$3 >= 33 && $4 >= 33 && $5 >= 33 {print $0 " # Student ID: 2023ebcs655"}' "$DATA_FILE"
}

# Function to print students with their divisions
print_students_with_divisions() {
    echo "Students with their divisions: # Student ID: 2023ebcs655"
    while IFS=" " read -r roll_number name history geography civics; do
        total_marks=$((history + geography + civics))
        average_marks=$((total_marks / 3))
        if [ "$history" -ge 33 ] && [ "$geography" -ge 33 ] && [ "$civics" -ge 33 ]; then
            if [ "$average_marks" -ge 75 ]; then
                division="Ist division"
            elif [ "$average_marks" -ge 60 ]; then
                division="IInd division"
            elif [ "$average_marks" -ge 33 ]; then
                division="IIIrd division"
            fi
        else
            division="Fail"
        fi
        echo "$roll_number $name $division # Student ID: 2023ebcs655"
    done < "$DATA_FILE"
}

# Function to delete a student record
delete_student_record() {
    echo -n "Enter Roll Number to delete: "
    read roll_number
    if grep -q "^$roll_number " "$DATA_FILE"; then
        grep -v "^$roll_number " "$DATA_FILE" > temp.txt && mv temp.txt "$DATA_FILE"
        echo "Student record deleted successfully. # Student ID: 2023ebcs655"
    else
        echo "Record not found. # Student ID: 2023ebcs655"
    fi
}

# Main menu loop
while true; do
    echo "Menu: # Student ID: 2023ebcs655"
    echo "1. Add a student record"
    echo "2. Print the list of students above passing marks"
    echo "3. Print the list of students with their divisions"
    echo "4. Delete a student record"
    echo "5. Exit"
    echo -n "Enter your choice (1/2/3/4/5): "
    read choice

    case $choice in
        1)
            add_student_record
            ;;
        2)
            print_students_above_passing
            ;;
        3)
            print_students_with_divisions
            ;;
        4)
            delete_student_record
            ;;
        5)
            echo "Exiting. # Student ID: 2023ebcs655"
            exit 0
            ;;
        *)
            echo "Invalid choice. Please enter a number between 1 and 5. # Student ID: 2023ebcs655"
            ;;
    esac
done

```

1. Add a student record:
![[Pasted image 20240716232856.png]]
2. Print the list of students above passing marks:
![[Pasted image 20240716232935.png]]
3. Delete a student record:
![[Pasted image 20240716233030.png]]

##### Question 4

Shell script:-
```
#!/bin/bash

# Student ID: 2023ebcs655

# Files to store the emails and valid emails
EMAIL_FILE="emails.txt"
VALID_EMAIL_FILE="valid_emails.txt"

# Ensure the emails.txt file exists
if [ ! -f "$EMAIL_FILE" ]; then
    touch "$EMAIL_FILE"
fi

# Function to add an email
add_email() {
    echo -n "Enter email address: "
    read email
    echo "$email" >> "$EMAIL_FILE"
    echo "Email added: $email # Student ID: 2023ebcs655"
}

# Function to get valid emails
get_valid_emails() {
    grep -E '^[a-zA-Z0-9]+@[a-zA-Z0-9]+\.[cC][oO][mM]$' "$EMAIL_FILE" > "$VALID_EMAIL_FILE"
    echo "Valid emails have been saved to $VALID_EMAIL_FILE # Student ID: 2023ebcs655"
}

# Main menu loop
while true; do
    echo "Menu: # Student ID: 2023ebcs655"
    echo "1. Add email"
    echo "2. Get valid emails"
    echo "3. Exit"
    echo -n "Enter your choice (1/2/3): "
    read choice

    case $choice in
        1)
            add_email
            ;;
        2)
            get_valid_emails
            ;;
        3)
            echo "Exiting. # Student ID: 2023ebcs655"
            exit 0
            ;;
        *)
            echo "Invalid choice. Please enter a number between 1 and 3. # Student ID: 2023ebcs655"
            ;;
    esac
done

```

1. Add email id:
![[Pasted image 20240716233343.png]]
2. Get valid emails:
![[Pasted image 20240716233439.png]]

##### Question 5

Shell script:-
```
#!/bin/bash

# Student ID: 2023ebcs655

SOURCE_DIR="source"
DEST_DIR="destination"

# Ensure both directories exist
if [ ! -d "$SOURCE_DIR" ] || [ ! -d "$DEST_DIR" ]; then
    echo "Source or destination directory does not exist. # Student ID: 2023ebcs655"
    exit 1
fi

# Function to compare files
compare_files() {
    echo "Identical files: # Student ID: 2023ebcs655"
    for src_file in "$SOURCE_DIR"/*; do
        filename=$(basename "$src_file")
        dest_file="$DEST_DIR/$filename"
        if [ -f "$dest_file" ] && cmp -s "$src_file" "$dest_file"; then
            echo "$filename # Student ID: 2023ebcs655"
        fi
    done
}

# Function to copy files
copy_files() {
    for src_file in "$SOURCE_DIR"/*; do
        filename=$(basename "$src_file")
        dest_file="$DEST_DIR/$filename"
        if [ ! -f "$dest_file" ]; then
            cp "$src_file" "$DEST_DIR"
            echo "Copied $filename # Student ID: 2023ebcs655"
        else
            echo "Skipped $filename (already exists) # Student ID: 2023ebcs655"
        fi
    done
}

# Function to move files
move_files() {
    for src_file in "$SOURCE_DIR"/*; do
        mv "$src_file" "$DEST_DIR"
        filename=$(basename "$src_file")
        echo "Moved $filename # Student ID: 2023ebcs655"
    done
}

# Function to copy files with specific extension
copy_files_with_extension() {
    extension=$1
    for src_file in "$SOURCE_DIR"/*."$extension"; do
        if [ -f "$src_file" ]; then
            filename=$(basename "$src_file")
            dest_file="$DEST_DIR/$filename"
            if [ ! -f "$dest_file" ]; then
                cp "$src_file" "$DEST_DIR"
                echo "Copied $filename # Student ID: 2023ebcs655"
            else
                echo "Skipped $filename (already exists) # Student ID: 2023ebcs655"
            fi
        fi
    done
}

# Main logic based on arguments
case "$1" in
    "")
        compare_files
        ;;
    "copy")
        if [ -z "$2" ]; then
            copy_files
        else
            copy_files_with_extension "$2"
        fi
        ;;
    "move")
        move_files
        ;;
    *)
        echo "Invalid argument. # Student ID: 2023ebcs655"
        ;;
esac

```

1. Compare files:
![[Pasted image 20240716233729.png]]
2. Copy all files from source to destination (ignoring identical files):
![[Pasted image 20240716233816.png]]
3. Move all files from source to destination:
![[Pasted image 20240716233839.png]]
4. Copy files with a specific extension from source to destination:
![[Pasted image 20240716234025.png]]

##### Question 6

Shell script:-
```
#!/bin/bash

# Student ID: 2023ebcs655

FILE="paragraph.txt"

# Ensure the file exists
if [ ! -f "$FILE" ]; then
    echo "File $FILE does not exist. # Student ID: 2023ebcs655"
    exit 1
fi

# Function to count words
count_words() {
    word_count=$(wc -w < "$FILE")
    echo "Number of words: $word_count # Student ID: 2023ebcs655"
}

# Function to count lines
count_lines() {
    line_count=$(wc -l < "$FILE")
    echo "Number of lines: $line_count # Student ID: 2023ebcs655"
}

# Function to lookup a word
lookup_word() {
    if [ -z "$1" ]; then
        echo "No word provided for lookup. # Student ID: 2023ebcs655"
        exit 1
    fi
    word_occurrences=$(grep -wo "$1" "$FILE" | wc -l)
    echo "Occurrences of '$1': $word_occurrences # Student ID: 2023ebcs655"
}

# Function to replace words
replace_word() {
    if [ -z "$1" ] || [ -z "$2" ]; then
        echo "Both words must be provided for replacement. # Student ID: 2023ebcs655"
        exit 1
    fi
    sed -i "s/\b$1\b/$2/g" "$FILE"
    echo "Replaced all occurrences of '$1' with '$2'. # Student ID: 2023ebcs655"
}

# Main logic based on arguments
case "$1" in
    "words")
        count_words
        ;;
    "lines")
        count_lines
        ;;
    "lookup")
        lookup_word "$2"
        ;;
    "replace")
        replace_word "$2" "$3"
        ;;
    *)
        echo "Invalid argument. Use words, lines, lookup <text>, or replace <text1> <text2>. # Student ID: 2023ebcs655"
        ;;
esac

```

1. Number Of words
![[Pasted image 20240716234238.png]]
2. Count lines
![[Pasted image 20240716234301.png]]
3. Lookup a word
![[Pasted image 20240716234329.png]]
4. Replace a word
![[Pasted image 20240716234354.png]]

##### Question 7 

shell script:- 
```
#!/bin/bash

# Student ID: 2023ebcs655

FILE="paragraph.txt"
BEGINNING_FILE="beginning.txt"
ENDING_FILE="ending.txt"

# Ensure the file exists
if [ ! -f "$FILE" ]; then
    echo "File $FILE does not exist. # Student ID: 2023ebcs655"
    exit 1
fi

# Function to extract words beginning with a vowel
extract_beginning_with_vowel() {
    grep -io '\b[aeiou][a-zA-Z]*\b' "$FILE" > "$BEGINNING_FILE"
    echo "Words beginning with a vowel have been saved to $BEGINNING_FILE # Student ID: 2023ebcs655"
}

# Function to extract words ending with a vowel
extract_ending_with_vowel() {
    grep -io '\b[a-zA-Z]*[aeiou]\b' "$FILE" > "$ENDING_FILE"
    echo "Words ending with a vowel have been saved to $ENDING_FILE # Student ID: 2023ebcs655"
}

# Function to find words that begin and end with the same letter
find_begin_end_same_letter() {
    grep -io '\b\([a-zA-Z]\)\w*\1\b' "$FILE" | tr '[:lower:]' '[:upper:]' | sort -u
}

# Main logic based on arguments
case "$1" in
    "beginning")
        extract_beginning_with_vowel
        ;;
    "ending")
        extract_ending_with_vowel
        ;;
    "same_letter")
        find_begin_end_same_letter
        ;;
    *)
        echo "Invalid argument. Use beginning, ending, or same_letter. # Student ID: 2023ebcs655"
        ;;
esac

```

1. Extract words beginning with a vowel:
![[Pasted image 20240716234802.png]]
2. Extract words ending with a vowel:
![[Pasted image 20240716234831.png]]
3. Find words that begin and end with the same letter (case insensitive):
![[Pasted image 20240716234858.png]]

##### Question 8

SHell script:- 
```
#!/bin/bash

# Student ID: 2023ebcs655

# Function to organize files by extension
organize_files() {
    directory="$1"

    # Check if directory exists
    if [ ! -d "$directory" ]; then
        echo "Directory '$directory' does not exist. # Student ID: 2023ebcs655"
        exit 1
    fi

    # Loop through each file in the directory
    find "$directory" -type f | while read file; do
        # Get the file extension
        extension="${file##*.}"
        
        # Skip if the file has no extension
        if [ -z "$extension" ]; then
            continue
        fi
        
        # Create subdirectory for the extension if not already created
        if [ ! -d "$directory/$extension\_files" ]; then
            mkdir "$directory/$extension\_files"
            echo "Created directory '$directory/$extension\_files'. # Student ID: 2023ebcs655"
        fi
        
        # Move the file to the corresponding subdirectory using exec with mv
        exec mv "$file" "$directory/$extension\_files/"
    done
}

# Main script execution starts here

# Check if argument is provided
if [ $# -ne 1 ]; then
    echo "Usage: $0 <directory_path> # Student ID: 2023ebcs655"
    exit 1
fi

# Call function to organize files
organize_files "$1"

echo "Files organized successfully. # Student ID: 2023ebcs655"
```

1. test directory contents:-
![[Pasted image 20240716235233.png]]
2. Run the script:-
![[Pasted image 20240716235304.png]]
3. Output of the script:-
![[Pasted image 20240716235349.png]]

##### Question 9

shell script:- 