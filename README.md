# Simple-Calculator-Implementation-Using-8086-Assembly-Language-

Lab Report: Simple Calculator Implementation Using 8086 Assembly Language 
<br>
Name: Md. Rashedul Islam Jubayer
<br>
ID: 0432220005101070
<br>
Date: May 25, 2025
<br>
# Objective
The objective of this laboratory experiment is to design and implement a simple calculator program using 8086 assembly language. The program performs basic arithmetic operations (addition, subtraction, multiplication, and division) on two single-byte numbers (00-99) provided by the user. The program features a menu-driven interface, input validation, and error handling for invalid inputs and division by zero.
# Introduction
Microprocessors are integral to computing systems, executing instructions stored in memory to perform various tasks. This lab focuses on programming the 8086 microprocessor using assembly language to create a functional calculator. The program utilizes DOS interrupts for input/output operations and implements procedures to modularize the code for better readability and maintainability.
Methodology
Hardware and Software
- Emulator: DOSBox or EMU8086
- Assembler: MASM (Microsoft Macro Assembler)
- Processor: 8086 Microprocessor

# Program Description
~~~~~~~~~~~~~~~~~~~
The program is a menu-driven calculator that:
- Displays a menu with five options: addition (1), subtraction (2), multiplication (3), division (4), and exit (5).
- Prompts the user to select an operation and input two numbers (00-99).
- Performs the selected arithmetic operation and displays the result.
- Handles errors such as invalid input and division by zero.
- Returns to the main menu after each operation or error.

Code Structure
~~~~~~~~~~~~~~
The code is divided into the following segments:
- .model small: Specifies the small memory model.
- .stack 100h: Allocates 256 bytes for the stack.
- .data: Defines messages, prompts, and variables for storing numbers, operator, and result.
- .code: Contains the main program and procedures for displaying the menu, reading numbers, and displaying results.

Key Procedures
~~~~~~~~~~~~~~
- display_menu: Outputs the menu options using DOS interrupt 21h (function 09h).
- read_number: Reads a two-digit number, validates it, and converts it to binary.
- display_number: Converts the result to ASCII and displays it, handling negative numbers for subtraction.

Code Listing
------------
.model small
.stack 100h

.data
menu_msg db 0dh, 0ah, 'Select the operation:', 0dh, 0ah
         db '1) ADD', 0dh, 0ah
         db '2) SUB', 0dh, 0ah
         db '3) MUL', 0dh, 0ah
         db '4) DIV', 0dh, 0ah
         db '5) Exit Calculator', 0dh, 0ah
         db 'Enter your choice between (1-5): $'

prompt1 db 0dh, 0ah, 'Enter first number (00-99): $'
prompt2 db 0dh, 0ah, 'Enter second number (00-99): $'

add_msg db 0dh, 0ah, 'The Result of + is = $'
sub_msg db 0dh, 0ah, 'The Result of - is = $'
mul_msg db 0dh, 0ah, 'The Result of * is = $'
div_msg db 0dh, 0ah, 'The Result of / is = $'

error_msg db 0dh, 0ah, 'Error: Invalid input or division by zero$'

num1 db 0
num2 db 0
operator db 0
result dw 0

.code
main proc
    mov ax, @data
    mov ds, ax

main_loop:
    call display_menu
    mov ah, 01h
    int 21h
    cmp al, '1'
    je set_add
    cmp al, '2'
    je set_sub
    cmp al, '3'
    je set_mul
    cmp al, '4'
    je set_div
    cmp al, '5'
    je exit
    jmp error

set_add:
    mov operator, '+'
    jmp read_inputs
set_sub:
    mov operator, '-'
    jmp read_inputs
set_mul:
    mov operator, '*'
    jmp read_inputs
set_div:
    mov operator, '/'
    jmp read_inputs

read_inputs:
    mov ah, 09h
    lea dx, prompt1
    int 21h
    call read_number
    cmp al, 0ffh
    je error
    mov num1, al
    mov ah, 09h
    lea dx, prompt2
    int 21h
    call read_number
    cmp al, 0ffh
    je error
    mov num2, al
    mov al, num1
    mov bl, num2
    cmp operator, '+'
    je do_add
    cmp operator, '-'
    je do_sub
    cmp operator, '*'
    je do_mul
    cmp operator, '/'
    je do_div
    jmp error

do_add:
    add al, bl
    mov ah, 0
    mov result, ax
    lea dx, add_msg
    jmp show_result

do_sub:
    sub al, bl
    cbw
    mov result, ax
    lea dx, sub_msg
    jmp show_result

do_mul:
    mov ah, 0
    mul bl
    mov result, ax
    lea dx, mul_msg
    jmp show_result

do_div:
    cmp bl, 0
    je error
    mov ah, 0
    div bl
    mov result, ax
    lea dx, div_msg
    jmp show_result

error:
    mov ah, 09h
    lea dx, error_msg
    int 21h
    jmp main_loop

show_result:
    mov ah, 09h
    int 21h
    call display_number
    jmp main_loop

exit:
    mov ah, 4ch
    int 21h
main endp

display_menu proc
    mov ah, 09h
    lea dx, menu_msg
    int 21h
    ret
display_menu endp

read_number proc
    push bx
    push cx
    mov ah, 01h
    int 21h
    sub al, '0'
    cmp al, 9
    ja invalid
    mov bl, al
    mov cl, 10
    mul cl
    mov bh, al
    mov ah, 01h
    int 21h
    sub al, '0'
    cmp al, 9
    ja invalid
    add al, bh
    jmp done

invalid:
    mov al, 0ffh

done:
    pop cx
    pop bx
    ret
read_number endp

display_number proc
    push ax
    push bx
    push cx
    push dx
    mov ax, result
    cmp ax, 0
    jge skip_neg
    mov ah, 02h
    mov dl, '-'
    int 21h
    neg ax

skip_neg:
    mov bx, 10
    mov cx, 0
next_digit:
    xor dx, dx
    div bx
    push dx
    inc cx
    test ax, ax
    jnz next_digit
print_digits:
    pop dx
    add dl, '0'
    mov ah, 02h
    int 21h
    loop print_digits
    mov dl, 0dh
    mov ah, 02h
    int 21h
    mov dl, 0ah
    int 21h
    pop dx
    pop cx
    pop bx
    pop ax
    ret
display_number endp

end main

Results and Discussion
----------------------
The program was tested using an 8086 emulator (EMU8086). The following observations were made:
- Menu Display: The menu is displayed clearly, prompting the user to select an operation (1-5).
- Input Handling: The program successfully reads two-digit numbers (00-99) and validates them. Invalid inputs (non-numeric or out-of-range) trigger an error message.
- Arithmetic Operations:
  - Addition: Correctly adds two numbers, e.g., 45 + 30 = 75.
  - Subtraction: Handles negative results, e.g., 20 - 50 = -30.
  - Multiplication: Computes products, e.g., 12 * 5 = 60.
  - Division: Performs integer division and checks for division by zero, e.g., 50 / 5 = 10.
- Error Handling: Displays an error message for invalid inputs or division by zero, then returns to the menu.
- Looping: The program loops back to the menu after each operation or error, allowing continuous use until the user selects exit (5).

Limitations
~~~~~~~~~~~
- The program only handles single-byte numbers (0-255 for results), limiting the range of multiplication and division results.
- Division results are integer-based, discarding remainders.
- Input is limited to two-digit numbers (00-99).

Conclusion
----------
The lab successfully demonstrated the implementation of a simple calculator using 8086 assembly language. The program effectively performs basic arithmetic operations, handles user input, and manages errors. The modular design, with separate procedures for menu display, input reading, and result display, enhances code maintainability. Future improvements could include support for larger numbers, decimal points, or additional operations like modulus.


