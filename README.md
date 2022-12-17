Course Registration System

We are trying to create a collection of RESTful web services to automate the process of course
registration for Compro students. Each Compro student should be able to create registration requests
for the future blocks during the registration period. Faculty should be able to “view” the registrations for
any student. Admins should be able to add new courses and offerings and edit any enrollment record.

Requirements (Domain Driven Design)

Create a UML class diagram of the domain based on the concepts below
1 Every Faculty has an id, name, email and title (e.g “Professor”, “Associate Professor”, etc.)

2 Every Student has an id, student ID (e.g. “61-1234”), name, email and mailing and home address

3 Every Address record has id, street, city, postalCode, state-province and country-region
(addresses can be shared by multiple students)

4 Every Course has an id, code (e.g. “CS544”), name (e.g. “Enterprise Architecture”) and
description (e.g. “Frameworks and best practices used in building large scale software systems”)

5 Every AcademicBlock has an id, code (e.g. “2022-12A-12D”), name (e.g. “December 2022”),
semester (“Spring”, “Summer” or “Winter”), start date and end date

6 Every CourseOffering of a course has an id, code (e.g. “CS544-2022-12A-12D-XX”), Course,
Faculty, Block and the XX is the faculty first name and last initials for example (“PS” for Payman
Salek) and has a list of students taking the course (it might be best if you make this relationship
unidirectional from student to course offering). The association class is Registration.

7 Each CourseOffering also has a capacity and a calculated field “availableSeats”. AvailableSeats is
calculated based on (Capacity – Number of students registered)

8 To allow students to register for courses, we form a RegistrationEvent, each RegistrationEvent
may have several RegistrationGroups. Each RegistrationGroup then is associated with a list of
students and a list of blocks. RegistrationEvent has a start and end DateTime.

9 During a registration event, every student must make a RegistrationRequest for every course
that is offered in every block. Each RegistrationRequest has priority number (1 – n, where n is
number of courses for that single block).

10 When the registration event is over, the admin logs in and calls “Process Registrations” which
then converts RegistrationRequests into Registrations based on available seats, pre-requisites
and other rules. For every student for every block, only one of his/her manyRegistrationRequests wins
and is converted into a Registration.

Use-cases

1. Students should be able to GET /registration-events/latest and receive the most recent
RegistrationEvent. By looking at the start and end datetime, you should be able to tell if registration
is open, closed or in progress.

2. RegistrationEvent is a complex object that consists of multiple RegistrationGroups and each group
consists of a list of students and a list of blocks. Each block has a list of course offerings.

3. SysAmin should be able to perform CRUD operations on RegistrationEvent. You can break
RegistrationEvent down to pieces and let the user create/update these parts piece by piece.

4. When students read RegistrationEvent, system filters the list of registration groups to the one that
student is a member of. Furthermore, the list of students will be masked for student view. So,
students see RegistrationEvent -> RegistrationGroups -> AcademicBlocks -> CourseOfferings

5. Students can submit a list of RegistrationRequest objects. There should be one object per course
offering for all blocks available to select from (you should validate that). The system will allow the
student to save the list during an active RegistrationEvent (between the start and end date & time).

6. Students should be able to see a list of Registraions. GET /registrations

7. SysAdmin should be able to “process” a registration event by calling:
PATCH /registration-events/{id}?processed=true
