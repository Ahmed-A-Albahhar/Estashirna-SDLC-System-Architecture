# Estashirna-SDLC-System-Architecture
End-to-end SDLC documentation, Data Flow Diagrams, and backend database architecture for the Estashirna advisory platform MVP.
# Estashirna (Consult Us): Advisory Platform System Architecture

## 📌 Project Overview
"Estashirna" is a comprehensive advisory and recommendation web platform designed to facilitate knowledge sharing across three core domains: E-commerce products, Career/Professional development, and Tourism. 

Developed as a Minimum Viable Product (MVP), the project's primary focus was establishing a rigorous backend architecture and executing a complete Software Development Life Cycle (SDLC) rather than prioritizing frontend aesthetics. The platform was engineered from scratch, featuring manual database integrations without the use of automated grid tools or wizards.

## 💡 Core Competencies Demonstrated
* **Full-Stack Execution:** Hand-coded the backend logic and database connection architecture to create a functioning web application within a highly constrained two-week development window.
* **End-to-End SDLC Management:** Led the complete software development lifecycle, from feasibility studies and requirements gathering to system design and functional deployment.
* **Complex Data Routing:** Engineered middle-tier application logic that successfully manages distinct administrative and user-level privileges for adding, reviewing, and evaluating content.

## 🏗️ Systems Architecture & UML Modeling
To ensure scalable and logical system behavior, the platform's foundation was strictly mapped using industry-standard systems analysis artifacts:

* **Entity Relationship Diagram (ERD):** Engineered the foundational database schema mapping 7 core system entities (`User`, `Admin`, `Questions`, `Items`, `Content`, `Comments`, `Notification`), defining explicit attributes, data types, primary keys, and relational cardinalities.
* **Data Flow Diagrams (DFD):** Mapped the movement of information through the system from Level 0 (Context Diagram) down to detailed Level 2 processing for Item and Question management, ensuring no logical dead-ends.
* **Functional Decomposition:** Broke down the platform's top-down structure into granular, executable functions for Guests, Users, and Administrators.
* **Use Case Modeling:** Documented specific actor-to-system interactions, detailing the preconditions, postconditions, and exact scenarios for operations like content approval, user banning, and item evaluation.

## ⚙️ Technical Stack
* **Systems Analysis:** UML Modeling (ERD, Use Case), DFDs, Functional Decomposition, Requirements Gathering.
* **Backend Engineering:** Custom application logic, manual database integration.
* **Database Management:** Relational database design, CRUD operations, User Access Management.
