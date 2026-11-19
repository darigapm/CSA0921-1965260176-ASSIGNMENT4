# Task 1 – GUI Design Review: Diagnose & Repair Fee-Payment Window

## Diagnosis
* SQL queries, JDBC connections, and sleep delays are embedded directly inside `FeeWindow.actionPerformed()`, coupling UI presentation with database and business logic[cite: 4].
* Comparing action commands using `==` instead of `.equals()` can fail for non-interned or localized strings and fall into the `else` branch that clears inputs[cite: 4].
* Parsing numeric input without validation allows `NumberFormatException` on invalid text and permits zero or negative amounts[cite: 5].
* Executing JDBC calls, SQL queries, and `Thread.sleep(3000)` on the Event-Dispatch Thread (EDT) freezes the UI and makes the window unresponsive[cite: 5].
* Clicking `Receipt` is not handled properly and falls into the `else` branch that clears fields instead of displaying a receipt[cite: 5].
* String concatenation risks SQL injection, unclosed JDBC resources leak connections, `windowClosing` is unhandled, checked exceptions (`SQLException`, `InterruptedException`) are unhandled, and status messages only print to `System.out` instead of updating the UI[cite: 5].

## Design Defence & Complexity Analysis
* Separating `FeeService` from `FeePaymentApp` keeps validation and JDBC persistence independent of Swing components so service logic can be tested directly and new actions like `Receipt` can be added cleanly[cite: 7].
* Running database operations inside `SwingWorker` keeps the EDT responsive while `PreparedStatement` and try-with-resources prevent SQL injection and connection leaks[cite: 7, 8].
* A single `if-else` action listener across $k$ buttons performs up to $O(k)$ string comparisons with $O(1)$ listener objects, whereas dedicated per-button listeners dispatch in $O(1)$ time with $O(k)$ lightweight listener instances[cite: 8].
