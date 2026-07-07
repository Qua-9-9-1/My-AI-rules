# MVC (Model-View-Controller) : Best Practices & Architecture

## 1. The "Fat Model, Skinny Controller" Rule
- **Models:** This is where 100% of your business logic, validation, and database interactions must live. A Model is not just a database table representation; it is the core brain of the application.
- **Controllers:** Controllers must be "dumb" and "skinny". Their only job is to receive the HTTP/User request, ask the Model for data, and pass that data to the View. Never put business rules, math, or complex conditional logic inside a Controller.

## 2. View Isolation
- **Dumb Views:** The View must contain zero business logic. It should only contain display logic (e.g., iterating over a list of items or formatting a date).
- **No Database Calls:** A View must never query the database or the Model directly. It must passively receive a pre-computed data payload (often called a ViewModel or DTO) from the Controller.

## 3. Separation of Concerns & State
- MVC is traditionally stateful, but in modern web APIs, it is often adapted to be stateless.
- **Strict Boundaries:** If a developer needs to change how a user's total cart price is calculated, they should only have to open the Model. If they need to change a button color, they only open the View. If they need to change a route URL, they only open the Controller.

## 4. Avoiding the "God Object" Anti-Pattern
- In large MVC applications, Models can become bloated "God Objects" (e.g., a `User` model with 5000 lines of code). 
- **Solution:** Break down massive Models into dedicated Service classes (Service Layer Pattern) that the Controller can call, keeping the data models strictly focused on data integrity.