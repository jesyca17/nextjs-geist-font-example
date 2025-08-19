```markdown
# Detailed Implementation Plan for Invoice Printing Website

This plan covers creating a modern invoice printing website for Jesyca shop with editable invoice inputs, dynamic date display, product line items, totals, print functionality, and persistent transaction storage viewable in an Excel-like table.

---

## 1. Project Structure and File Dependencies

- **New Data File:**  
  - Create `/data/transactions.json` (at project root) to store transactions. Initialize with an empty array (`[]`).

- **Library File for Data Operations:**  
  - Create `/src/lib/db.ts` to wrap file I/O operations (read/write JSON). This file will contain helper functions:  
    - `getTransactions()`: Read and parse `/data/transactions.json`.  
    - `addTransaction(data)`: Append a new transaction and handle file errors.  

- **API Endpoint:**  
  - Create `/src/app/api/transactions/route.ts` with:  
    - **GET handler:** Returns all stored transactions as JSON.  
    - **POST handler:** Validates the incoming invoice data (customer name, address, transaction date, products, totals, bank account), then calls `addTransaction`.  
    - Use try–catch blocks for error handling and return proper HTTP status codes.

- **Invoice Page:**  
  - Create `/src/app/invoice/page.tsx` for the invoice printing form containing:  
    - **Header Section:**  
      - Static company details (“Jesyca shop”, “Jl Wilis 24 B Pare”, “0857 49201950”).  
      - Left side: Editable input fields for “Nama Pelanggan” and “Alamat Pelanggan”.  
      - Right side: A real-time display of transaction date (generated via `new Date()` in a useEffect hook).  
    - **Line Items Section:**  
      - A dynamic table or list where users can add one or more product rows (each row with inputs for “Produk yang dibeli” and “Harga”).  
      - An “Add Product” button to append rows.  
    - **Totals Section:**  
      - Automatically calculate subtotal (sum of product prices).  
      - Editable “Jumlah Dibayar” field that dynamically computes “Kurang” (difference) and displays “Total Keseluruhan” in a summary row.  
      - The very bottom displays a static “Nomor Rekening” (e.g. a placeholder like “123-456-7890”) that can later be modified if needed.  
    - **Buttons:**  
      - “Simpan Invoice” button which submits the form data to the POST API endpoint.  
      - “Cetak Invoice” button that triggers `window.print()` for printing.
    - **UI Styling:**  
      - Use modern CSS (via `/src/app/globals.css` or inline styles) for a clean layout with proper spacing, clear typography, and responsive design.  
      - Avoid external icon libraries.

- **Transactions Page:**  
  - Create `/src/app/transactions/page.tsx` to list saved transactions in a table:  
    - Fetch data from GET `/api/transactions` using `useEffect` and state.  
    - Render a styled table with columns like “Nama Pelanggan”, “Alamat”, “Tanggal Transaksi”, and “Total Harga”.  
    - Optionally include an “Export to CSV” button that converts the JSON data into a CSV file download.
  
- **Navigation and Layout Integration:**  
  - Update (or create) `/src/app/layout.tsx` (or similar global layout component) to include navigation links for “Invoice” and “Transaksi” pages for easy access.

---

## 2. Step-by-Step Changes

1. **Create Data File:**  
   - File: `/data/transactions.json`  
     - Content: `[]`
   - Ensure proper file permissions and error handling when accessed in `/src/lib/db.ts`.

2. **Create Database Utility (db.ts):**  
   - File: `/src/lib/db.ts`  
     - Import Node’s `fs/promises` and `path` modules.  
     - Write functions `getTransactions()` and `addTransaction(transactionData)` with error catching.
  
3. **Develop API Endpoint:**  
   - File: `/src/app/api/transactions/route.ts`  
     - Implement GET: Call `getTransactions()` and return a JSON response with a success status.  
     - Implement POST: Validate the request body (e.g., check required fields), then call `addTransaction()`.  
     - On error, return HTTP 500 with an error message.
  
4. **Build Invoice Page:**  
   - File: `/src/app/invoice/page.tsx`  
     - Import React hooks (`useState`, `useEffect`) and necessary UI components (inputs, buttons).  
     - Create state variables for customer name, address, products (as an array), amount paid, and computed totals.  
     - Use a useEffect hook to set and update the real-time transaction date.  
     - Add functions for handling product row addition, dynamic total calculation, form submission (fetch POST to `/api/transactions`), and print functionality.  
     - Use modern CSS classes (from `/src/app/globals.css` if desired) for a clean layout.
  
5. **Build Transactions Listing Page:**  
   - File: `/src/app/transactions/page.tsx`  
     - Use React hooks to fetch transaction data from your API on component mount.  
     - Render a table with appropriate headings and rows.  
     - Implement CSV export functionality: convert array to CSV string, create a downloadable Blob, and trigger download.
  
6. **Update Global Layout (if needed):**  
   - File: `/src/app/layout.tsx`  
     - Add navigation links for “Invoice” (linking to `/invoice`) and “Transaksi” (linking to `/transactions`).
  
7. **Testing and Error Handling:**  
   - Test API endpoints via curl:
     ```bash
     # Test GET transactions
     curl -X GET http://localhost:3000/api/transactions
     
     # Test POST transactions (example)
     curl -X POST http://localhost:3000/api/transactions \
       -H "Content-Type: application/json" \
       -d '{"customerName": "John Doe", "customerAddress": "Jl Contoh 123", "transactionDate": "'+new Date().toISOString()+'", "products": [{"name": "Produk A", "price": 100}], "subtotal": 100, "amountPaid": 50, "balance": 50, "bankAccount": "123-456-7890"}'
     ```
   - In the frontend, validate required fields and display error messages on failure.
   - Ensure print functionality works in different browsers.
  
8. **UI/UX Best Practices:**  
   - Ensure consistent spacing, margins, and responsive breakpoints using CSS flex/grid layouts.  
   - Use semantic HTML elements for better accessibility.  
   - Do not use external icon images; rely on text and minimalistic design as required.

---

## 3. Summary

- Created a JSON file (`/data/transactions.json`) for persistent transaction storage.  
- Developed `/src/lib/db.ts` to handle file operations with built-in error handling.  
- Built API endpoints in `/src/app/api/transactions/route.ts` for GET (fetch) and POST (save) functionality.  
- Implemented a modern, stylized invoice form in `/src/app/invoice/page.tsx` with editable fields, dynamic date, product rows, total calculations, printing, and submission.  
- Created a transaction listing page in `/src/app/transactions/page.tsx` that displays stored invoices in a table and allows CSV export.  
- Updated a global layout for easy navigation between pages.  
- Integrated robust error handling and testing via curl commands to validate endpoint functionality.  
```
