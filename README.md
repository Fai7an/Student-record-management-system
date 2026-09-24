# Student Record Management System

A simple console-based CRUD application written in C++ that manages student records with persistent file storage.

## Features

- **Add** a new student (ID, name, GPA) with duplicate-ID protection
- **View** all student records in a formatted table
- **Search** for a student by ID
- **Update** an existing student's name and GPA
- **Delete** a student record
- **Persistent storage** — records are saved to `students.txt` and automatically reloaded on the next run

## Tech Highlights

- Custom `Student` struct to model record data
- File I/O using `fstream` (`ofstream` / `ifstream`) for persistence
- Menu-driven interface with a `while(true)` loop
- Input validation to guard against non-numeric entries
- Clean separation of concerns: file helpers, core CRUD operations, and menu logic

## Getting Started

### Prerequisites

- A C++ compiler supporting C++17 (e.g. `g++`)

### Build & Run

```bash
g++ -std=c++17 -Wall -o student_manager main.cpp
./student_manager
