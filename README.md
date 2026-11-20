CampusSpace — Rooms, Labs & Roll Number Tracker

A full-stack campus facility and student allotment tracker that helps users check real-time room/lab availability, reserve time slots, and look up their assigned lab, classroom, walking directions, and roommates/lab partners using their roll number and an allotment PDF.   
Screenshot 2026-11-19 at 2.22.51 PM.png
+---------------------------+        +------------------------------------+        +-----------------------------------+
|  Client UI                | <----> |  REST API + JWT/RBAC Middleware    | <----> |  Database                         |
|  (React + TS + PDF.js)    |        |  (Node.js + Express)               |        |  (PostgreSQL / MongoDB / SQLite)  |
+---------------------------+        +------------------------------------+        +-----------------------------------+
         |                                               |                                         |
         v                                               v                                         v
  Roll No. Lookup &                               Slot Overlap Check &                        Rooms, Bookings,
  PDF Row Extraction                             Role Authorization                          Allotments & Tickets
+ 1
Overview
CampusSpace solves two everyday campus bottlenecks in one place:
Live Room & Lab Tracker: View real-time availability (Available, Occupied, Reserved Soon, Under Maintenance), filter by room type, capacity, or equipment, and book open hourly slots with overlap validation.   
Screenshot 2026-11-19 at 2.22.51 PM.png
Roll Number & PDF Allotment Lookup: Upload an allotment/timetable PDF (parsed in the browser via PDF.js) and search by roll number to see your assigned lab, classroom, batch/slot, workstation/bed, directions, and paired roommates or lab mates.
Target Audience: Students, faculty, lab coordinators, and campus admins.
System Architecture
Frontend: React, TypeScript, Vite, Tailwind CSS, Lucide React, and pdfjs-dist.   
Screenshot 2026-11-19 at 2.22.51 PM.png
Backend: Node.js and Express with JWT authentication and role-based access control.
Database & Storage: PostgreSQL / MongoDB / SQLite for persistent records, with localStorage support for quick client-side persistence.
Architecture Flow
Client UI (React + PDF.js) 
↔
↔ REST API + JWT/RBAC Middleware (Node/Express) 
↔
↔ Database (PostgreSQL/MongoDB/SQLite)
Core Modules
1. User & Role Management
Role	Permissions
Admin	Full access: manage rooms, override statuses, handle maintenance tickets, manage users, and publish allotments
Coordinator	Manage lab schedules, upload allotment PDFs, approve/cancel bookings, and update maintenance status
Faculty	Check room/lab availability, book rooms/labs for sessions, view batch allotments, and report issues
Student	Browse available spaces, book study slots, look up lab/class & roommates via roll number, and report issues
Key Features: Login/logout, JWT authentication, role-based authorization, password reset, profile management, and department/batch management.

FSD PROJECTSAMPLE SYSTEM DESIGN DOCUMENT.docx
2. Rooms & Labs Availability + Booking
Live status cards and KPI summary bars.
Search and filter by room code/name, type, status, seating capacity, and equipment.
Hourly timeline booking modal with double-booking prevention and maintenance ticket reporting.

Screenshot 2026-11-19 at 2.22.51 PM.png
3. Roll Number & PDF Allotment Lookup
Client-side PDF parsing to map roll numbers to labs, classrooms, time slots, and seats/workstations.
Instant roll number lookup showing assigned spaces, step-by-step directions, and co-assigned roommates/lab partners.
Database Schema
users: user_id (PK), name, email, password_hash, role, roll_number, department   
FSD PROJECTSAMPLE SYSTEM DESIGN DOCUMENT.docx
rooms: room_id (PK), name, type, building, floor, capacity, status, equipment, directions
bookings: booking_id (PK), room_id (FK), user_id (FK), booked_by_name, purpose, booking_date, start_time, end_time, status
student_allotments: allotment_id (PK), roll_number, student_name, batch_slot, lab_room_id (FK), class_room_id (FK), workstation_or_bed
maintenance_tickets: ticket_id (PK), room_id (FK), reported_by (FK), issue_description, priority, status
ER Overview
users (1) 
→
→ (N) bookings and maintenance_tickets   
FSD PROJECTSAMPLE SYSTEM DESIGN DOCUMENT.docx
rooms (1) 
→
→ (N) bookings, maintenance_tickets, and student_allotments
REST API Endpoints
Method	Endpoint	Purpose
POST	/api/auth/login	User login & JWT token
POST	/api/auth/register	Register new user
GET	/api/rooms	Get filtered rooms & labs
POST	/api/rooms	Add a new room/lab (Admin)
PATCH	/api/rooms/:id/status	Update room status
GET	/api/bookings	Fetch bookings
POST	/api/bookings	Create a booking (with overlap check)
DELETE	/api/bookings/:id	Cancel a booking
GET	/api/allotments/:rollNumber	Lookup lab, class, directions & roommates/lab mates
POST	/api/allotments/upload	Save parsed PDF allotment records
GET / POST / PATCH	/api/maintenance	View, report, and resolve maintenance issues
Development Roadmap
