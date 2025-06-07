### Linux Shell Scripting & File Permissions Mini-Project  
---

#### **Introduction**  
This project demonstrates core Linux shell scripting concepts and file permission management through a Multiplication Table Generator. Shell scripting automates repetitive tasks, while proper file permissions ensure system security by controlling access to scripts and data.  

---

### **Step-by-Step Tutorial**  

#### **1. Creating the Script File**  
```bash
pwd  # Verify working directory (/home/ubuntu)
vi bash-script-generating-multiplication-table.sh
```
![Creating Script](./img/1.%20Creating%20script%20file%20using%20vi.jpg)  
*Using `vi` to create a new script file in the home directory.*

---

#### **2. Script Structure & User Prompts**  
```bash
#!/bin/bash

display_header() {
    echo "=============================="
    echo "    MULTIPLICATION TABLE GENERATOR"
    echo "=============================="
}

main() {
    display_header
    echo "Hello! Enter a number (1-10) or choose a range (1-5, 6-10):"
}
main
```
![Initial Script](./img/2.%20Prompting%20a%20user%20using%20main%20function.jpg)  
*Core script structure with a header function and user prompt.*

---

#### **3. Setting Execute Permissions**  
```bash
chmod u+x bash-script-generating-multiplication-table.sh
ls -latt  # Verify permissions (-rwxrw-r--)
```
![Permission Settings](./img/3.%20Elevated%20permission%20for%20user.jpg)  
*Using `chmod u+x` grants execute rights to the owner only (security best practice).*

---

#### **4. Testing User Input Validation**  
```bash
# Sample validation logic (pseudocode)
validate_input() {
    if [[ $1 =~ ^[1-9]$|^10$ ]]; then
        echo "✓ Valid"
    else
        echo "✗ Invalid"
    fi
}
```
![Validation Output](./img/5.%20Validation%20function.jpg)
![Validation Output](./img/6.%20Output%20of%20Validation.jpg)  
*Tests include: valid numbers (5,10), invalid inputs (0, abc, negative).*

---

#### **5. Generating Multiplication Tables**  
**Full Table Function (Style 2):**  
```bash
generate_full_table() {
    echo "_________________________"
    echo " MULTIPLICATION TABLE FOR $1"
    echo "_________________________"
    for i in {1..10}; do
        printf "| %2d x %2d = %3d |\n" $1 $i $(($1*i))
    done
    echo "_________________________"
}
```
![Table Generation](./img/7.%20Function%20to%20generate%20and%20display%20full%20multiplication%20table.jpg)  

---

#### **6. Partial Table Range Handling**  
```bash
generate_partial_table() {
    for ((i=$2; i<=$3; i++)); do
        echo "$1 x $i = $(($1*i))"
    done
}
```
![Partial Table](./img/8.%20%20Function%20to%20generate%20and%20display%20partial%20multiplication%20table.jpg)  
*Accepts start/end ranges (e.g., 3-6) for customized output.*

---

#### **7. Testing Table Outputs**  
```bash
test_table_generation() {
    generate_full_table 5 1    # Style 1
    generate_partial_table 9 1 3 2  # Style 2, range 1-3
}
```
![Test Output](./img/9.%20Test%20function%20for%20table%20generation.jpg)  
*Verifies correct formatting and calculation logic.*

---

### **Security & Best Practices**  
1. **Least Privilege Principle**  
   - Use `chmod u+x` instead of `chmod +x` to restrict execution to owners only.  
   - Avoid global write permissions (`chmod o-w file.sh`).  

2. **Input Sanitization**  
   ```bash
   [[ $input =~ ^[1-9]$|^10$ ]] || exit 1  # Exit on invalid input
   ```

3. **Audit Trail**  
   - Log script executions: `./script.sh | tee -a /var/log/script.log`.  

4. **Troubleshooting Tips**  
   - **Error:** `Permission denied` → Run `chmod u+x script.sh`  
   - **Error:** `Unexpected operator` → Check `[[ ]]` syntax in older shells  

---

### **Advanced Notes**  
1. **Parameter Expansion**  
   Use defaults for arguments: `local format_style=${2:-1}`.  
2. **Color Output**  
   Enhance headers: `echo -e "\e[32mTITLE\e[0m"`.  
3. **Scheduled Execution**  
   Add to cron: `crontab -e` → `0 * * * * /path/to/script.sh`.  

--- 

#### **8. Format Selection Function**  
```bash
get_format_style() {
    echo "Choose a display format:"
    echo "1. Simple format"
    echo "2. Boxed format"
    echo "3. Fancy format"
    
    while true; do
        read -p "Enter choice (1-3) [default: 1]: " format_choice
        [[ -z "$format_choice" ]] && format_choice=1  # Default to 1
        
        case $format_choice in
            1|2|3) 
                echo "$format_choice"
                return
                ;;
            *) 
                echo "Invalid choice. Enter 1, 2, or 3"
                ;;
        esac
    done
}
```
![Format Selection](./img/11.%20Function%20to%20get%20table%20format%20preference.jpg)  
*Interactive format selector with default fallback and input validation.*

---

#### **9. Testing Format Selection**  
```bash
test_format_selection() {
    echo "Testing format selection:"
    selected_format=$(get_format_style)
    echo "Selected format: $selected_format"
    generate_full_table 6 "$selected_format"  # Demo with number 6
}
```
![Format Test](./img/12.%20Test%20function%20for%20format%20selection.jpg)  
*Integration test demonstrating format selection workflow.*

---

#### **10. User Interaction Flow**  
```bash
main() {
    display_header
    continue_program="y"
    
    while [[ "$continue_program" =~ [yY] ]]; do
        # Number input
        while true; do
            read -p "Enter number (1-10): " number
            is_valid_number "$number" && break
            echo "Error: Invalid number"
        done
        
        # Table type (full/partial)
        read -p "Full or partial table? (f/p): " table_type
        case "$table_type" in
            f|F) 
                format_style=$(get_format_style)
                generate_full_table "$number" "$format_style"
                ;;
            p|P)
                # Range input
                while true; do
                    read -p "Start (1-10): " start
                    read -p "End ($start-10): " end
                    is_valid_range "$start" "$end" && break
                    echo "Error: Invalid range"
                done
                format_style=$(get_format_style)
                generate_partial_table "$number" "$start" "$end" "$format_style"
                ;;
        esac
        
        # Continuation prompt
        read -p "Generate another? (y/n): " continue_program
    done
    echo "Goodbye!"
}
```
![Main Workflow](./img/13.%20Enter%20your%20choice%20(1-3).jpg)  
*Complete workflow with input validation and restart capability.*

---

### **User Experience Demo**  
```bash
# Sample Output
Choose a display format:
1. Simple format
2. Boxed format
3. Fancy format
Enter choice (1-3) [default: 1]: 3

*** MULTIPLICATION TABLE FOR 6 ***
6 x  1 =   6
6 x  2 =  12
...
6 x 10 =  60
```
![Format Output](./img/14.%20Output%20of%20Enter%20your%20choice%20(1-3).jpg)  
*User selects "Fancy" format (option 3) for number 6.*

---

### **Security & Best Practices (Expanded)**  
1. **Input Sanitization**  
   - Prevent code injection: `read -p "Prompt: " var` (never use `eval`)  
   - Range validation:  
     ```bash
     is_valid_range() {
         [[ $1 -ge 1 && $1 -le 10 && $2 -ge 1 && $2 -le 10 && $1 -le $2 ]]
     }
     ```  

2. **Permission Escalation Prevention**  
   - Avoid `sudo` in scripts: `chown ubuntu:ubuntu script.sh`  
   - Use principle of least privilege: `chmod 750 script.sh` (user:rwx, group:r-x)  

3. **Error Handling**  
   - Exit on critical errors: `|| { echo "Error"; exit 1; }`  
   - Log errors: `exec 2>> /var/log/script_errors.log`  

---

### **Advanced Scripting Techniques**  
1. **Color Formatting**  
   ```bash
   echo -e "\e[1;32mSUCCESS:\e[0m Table generated"  # Bold green text
   ```

2. **Date Stamping**  
   ```bash
   printf "| %-10s | %-15s |\n" "$(date +%F)" "Table for $number"
   ```

3. **ASCII Art Borders**  
   ```bash
   draw_border() {
       printf '%*s\n' "${COLUMNS:-30}" '' | tr ' ' '='
   }
   ```

![Final Interaction](./img/16.%20Output%20Main%20function%20to%20run%20the%20multiplication%20table%20generator.jpg)  
*End-to-end user interaction flow from number input to formatted output.*

### **Step-by-Step Tutorial (Final Steps)**  

#### **11. Loop Style Comparison**  
**List-Style Loop (Brace Expansion):**  
```bash
# Ascending: {1..10}
for i in {1..10}; do
    echo "$number x $i = $((number*i))"
done

# Descending: {10..1}
for i in {10..1}; do
    echo "$number x $i = $((number*i))"
done
```

**C-Style Loop (Flexible Control):**  
```bash
# Ascending
for ((i=1; i<=10; i++)); do
    echo "$number x $i = $((number*i))"
done

# Descending
for ((i=10; i>=1; i--)); do
    echo "$number x $i = $((number*i))"
done
```
![Loop Comparison](./img/19.%20Output%20%20Demonstrates%20List-Style%20vs%20C-Style%20for%20loops%20with%20order%20options.jpg)  
*Key differences: Brace expansion is simpler for fixed ranges, while C-style offers granular control over iterations.*

---

#### **12. Order Selection Implementation**  
```bash
get_display_order() {
    while true; do
        read -p "Choose order [1-Ascending, 2-Descending]: " order
        case $order in
            1) echo "asc" ;;
            2) echo "desc" ;;
            *) echo "Invalid choice" ;;
        esac
    done
}

# Usage in table function:
generate_table() {
    case $(get_display_order) in
        "asc") 
            for i in {1..10}; do ... done
            ;;
        "desc")
            for ((i=10; i>=1; i--)); do ... done
            ;;
    esac
}
```
*Adds user control over table direction with validation.*

---

#### **13. Final Permission Management**  
```bash
chmod u+x bash-script-generating-multiplication-table-Cstyle.sh
ls -l  # Verify permissions: -rwxrw-r--
```
![Final Permissions](./img/20.%20Elevated%20permission.jpg)  
*Secure permission settings: Owner (execute), Group (read), Others (no access).*

---

### **Loop Comparison & Best Practices**  
| **Feature**               | **List-Style Loop**               | **C-Style Loop**               |
|---------------------------|-----------------------------------|--------------------------------|
| **Syntax**                | `for i in {1..10}`                | `for ((i=1; i<=10; i++))`      |
| **Range Flexibility**     | Fixed sequences only              | Dynamic bounds (`i<=$end`)     |
| **Increment Control**     | Fixed step (+1/-1)                | Custom steps (`i+=2`)          |
| **Readability**           | Beginner-friendly                 | Familiar to C/Java developers  |
| **Use Case**              | Simple iterations                 | Complex conditions             |

**Recommendation:**  
- Use **list-style** for static ranges (e.g., 1-10 multiplication tables)  
- Use **C-style** for dynamic ranges (e.g., user-defined start/end)  

---

### **Advanced Loop Examples**  
**Custom Increments (C-Style):**  
```bash
# Print even multiples only
for ((i=2; i<=20; i+=2)); do
    echo "$number x $i = $((number*i))"
done
```

**Nested Loops (List-Style):**  
```bash
# Generate tables for numbers 1-3
for num in {1..3}; do
    for multiplier in {1..10}; do
        echo "$num x $multiplier = $((num*multiplier))"
    done
done
```

---

### **Security Hardening Checklist**  
1. **Permission Audit**  
   ```bash
   find . -name "*.sh" -exec ls -l {} \;  # List all script permissions
   ```
2. **Remove World-Write Access**  
   ```bash
   chmod o-w *.sh  # Revoke others' write access
   ```
3. **Set Owner/Group**  
   ```bash
   sudo chown ubuntu:developers script.sh  # Assign ownership
   ```
4. **Integrity Checks**  
   ```bash
   sha256sum script.sh > checksum.txt  # Monitor unauthorized changes
   ```

---

### **Conclusion**   
This project reinforces essential Linux administration skills: script automation, permission governance, and defensive input handling. Modular design patterns and strict permission controls ensure scalable and secure deployments
It demonstrates professional-grade shell scripting with:  
- **User Experience**: Interactive prompts, format/order selection  
- **Technical Depth**: Loop optimization, permission governance  
- **Security**: Principle of least privilege, input sanitization  
- **Maintainability**: Modular functions, validation layers  

The final script supports ascending/descending displays using both loop styles, providing flexibility while maintaining security through strict file permissions.  

# Review

👍🏽 Excellent
Review
The project thoroughly implements the instructor's objectives. Here's a detailed evaluation:

Purpose: The script successfully achieves the goal of generating multiplication tables based on user input, offering both full and partial tables, formatted display options, and ascending/descending order functionality. It solidifies scripting principles such as loops, user input handling, and modular design, meeting the instructional objectives. Grade: 25/25

Requirement: Core requirements of implementing list-style and C-style loops for multiplication table generation are well-met, and error handling is incorporated via input validation. The script applies secure permission settings (chmod u+x) and uses modular functions for clear logic separation. Input validation ensures robust handling of incorrect input values, meeting the project requirements. Grade: 25/25

Use Case: The script showcases practical Bash scripting scenarios, enhancing user interaction with interactive prompts and format selection. It accurately demonstrates the utility of list and C-style loops for dynamic table generation. Advanced features like input sanitization, security principles, and modular function usage make this script applicable to real-world tasks where repetitive calculations need efficient scripting. Grade: 25/25

Performance: Robustness and accuracy in output are demonstrated through validations and comprehensive tests included in the project (e.g., invalid and valid inputs, range handling). The script also integrates security best practices like restricting permissions, preventing privilege escalation, and adding error-logging capabilities. It provides clear, readable outputs and efficient execution without runtime errors. Grade: 25/25

Artifacts Review:

The screenshots and code snippets are detailed, visually verifying each major step, including file creation, permission adjustment, validation testing, table formatting, and outputs. The integration of inputs demonstrates correctness.
Images showing ascending/descending order and loop comparison enhance the educational objectives, explicitly contrasting list-style and C-style loops.
Final Grading: The project fulfills all the instructor’s objectives and incorporates advanced features and security enhancements. The script is interactive, well-structured, and includes detailed documentation of outputs and key concepts. The student demonstrates professional-level practice in Bash scripting, input handling, and maintaining system integrity.

Grade: 100/100


Feed Back
The inclusion of advanced techniques like modular design, colorful outputs, and security best practices significantly enhances the usability of the script. Consider further expanding input validation by including checks for special characters or scripting injection vulnerabilities for additional robustness. Additionally, scheduling use case examples (e.g., adding the script to cron jobs) could illustrate its practical application better.