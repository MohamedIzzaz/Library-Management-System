# Digital Library Management System - Documentation

Welcome to the Digital Library Management System! This document explains how the application works, the underlying workflows, how to navigate the website, and the roles available.

## Table of Contents
1. [Overview & Tech Stack](#overview--tech-stack)
2. [Roles & Usage](#roles--usage)
3. [Core Workflows](#core-workflows)
4. [Website Navigation](#website-navigation)
5. [Database Schema](#database-schema)

---

## Overview & Tech Stack

This application is a complete, lightweight digital library platform. It is designed with a professional UI and utilizes modern web technologies.

- **Frontend & Backend:** Next.js 16 (App Router)
- **Styling:** Tailwind CSS & shadcn/ui (for clean, minimal, accessible components)
- **Database:** SQLite (local file-based DB) via Prisma ORM
- **Authentication:** Custom JWT (JSON Web Tokens) stored in HTTP-only cookies

---

## Roles & Usage

The system has a built-in role-based access control (RBAC) system with two main roles: **ADMIN** and **MEMBER**.

### 1. ADMIN (Librarian/Manager)
**How to get this role:** The *very first user* to register in the system is automatically granted the `ADMIN` role. 
**Capabilities:**
- Access to the **Admin Dashboard**.
- View total library statistics (Total Books, Total Active Loans).
- **Add new books** to the library catalog (specifying Title, Author, Category, and Total Copies).
- **Delete books** from the catalog.
- Monitor all users' borrowing activities.

### 2. MEMBER (Library User)
**How to get this role:** Every user who registers after the first user is automatically granted the `MEMBER` role.
**Capabilities:**
- Browse the public Book Catalog.
- **Borrow books** (if there are available copies).
- Access the **Member Dashboard** ("My Books").
- View their borrowing history and due dates.
- **Return books** they have borrowed.

---

## Core Workflows

### The Borrowing Workflow
1. A **Member** browses the homepage catalog.
2. If a book shows as **Available**, the Member clicks "Borrow".
3. The system checks the inventory. If `availableCopies > 0`, it deducts `1` from the book's available copies.
4. A new `Loan` record is created for that user, marking the due date 14 days into the future.
5. The button instantly updates to reflect the new state.

### The Return Workflow
1. A **Member** goes to their **My Books** dashboard.
2. They see a list of actively borrowed books.
3. They click "Return Book" next to the respective title.
4. The system updates the `Loan` record status to `RETURNED` and stamps the return date.
5. The system adds `1` back to the book's `availableCopies`.

---

## Website Navigation

The top navigation bar adapts dynamically based on whether you are logged in and what role you have.

- **Logo ("LibraryMS")**: Clicking this always takes you to the **Home Page (Catalog)**.
- **Login / Register**: Visible to unauthenticated users. Use these to create an account or sign in.
- **Dashboard (Admin only)**: Takes you to `/admin`, where you can manage the book inventory and view library statistics.
- **My Books (Member only)**: Takes you to `/dashboard`, where you can see the books you've checked out and return them.
- **Logout**: Ends your session and redirects you to the login page.

### Page Routes breakdown:
- `/` - The main catalog where all books are listed.
- `/login` - User login portal.
- `/register` - Account creation portal.
- `/admin` - The Admin dashboard (protected).
- `/dashboard` - The Member's personal books dashboard (protected).

---

## Database Schema (How data is connected)

The application uses three main models:

1. **User**: Stores login credentials (hashed with bcrypt), email, name, and their `Role` (ADMIN/MEMBER).
2. **Book**: Stores book metadata (Title, Author, Category). It crucially tracks two inventory numbers: `totalCopies` and `availableCopies`.
3. **Loan**: The link between a `User` and a `Book`. It tracks the `borrowDate`, `dueDate`, `returnDate`, and the current `status` (BORROWED vs RETURNED).
