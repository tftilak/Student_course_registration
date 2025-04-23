import java.util.ArrayList;
import java.util.Scanner;

public class Main {
        public static void main(String[] args) {
                // Create courses and students arrays
                ArrayList<Course> courses = new ArrayList<>();
                ArrayList<Student> students = new ArrayList<>();

                // Add some sample courses
                courses.add(new Course("CS101", "Introduction to Programming", "Learn basic programming", 30, "Mon/Wed 10:00-11:30"));
                courses.add(new Course("MATH101", "Calculus I", "Basic calculus concepts", 25, "Tue/Thu 9:00-10:30"));
                courses.add(new Course("ENG101", "English Composition", "Writing skills", 35, "Fri 13:00-16:00"));

                // Add some sample students
                students.add(new Student("101", "Tilak"));
                students.add(new Student("102", "Jatin"));
                students.add(new Student("103", "Vishal"));

                // Create scanner for user input
                Scanner scanner = new Scanner(System.in);
                boolean exit = false;

                // Main menu loop
                while (!exit) {
                        System.out.println("\n===== STUDENT COURSE REGISTRATION SYSTEM =====");
                        System.out.println("1. List All Courses");
                        System.out.println("2. Register Student for Course");
                        System.out.println("3. Drop Course");
                        System.out.println("4. View Student Courses");
                        System.out.println("5. Exit");
                        System.out.print("Enter your choice: ");

                        int choice = scanner.nextInt();
                        scanner.nextLine(); // Clear buffer

                        switch (choice) {
                                case 1:
                                        listAllCourses(courses);
                                        break;
                                case 2:
                                        registerStudentForCourse(students, courses, scanner);
                                        break;
                                case 3:
                                        dropCourse(students, courses, scanner);
                                        break;
                                case 4:
                                        viewStudentCourses(students, scanner);
                                        break;
                                case 5:
                                        exit = true;
                                        System.out.println("Thank you for using the Course Registration System!");
                                        break;
                                default:
                                        System.out.println("Invalid option. Please try again.");
                        }
                }

                scanner.close();
        }

        // Method to list all available courses
        private static void listAllCourses(ArrayList<Course> courses) {
                System.out.println("\n===== AVAILABLE COURSES =====");
                System.out.println("CODE | TITLE | DESCRIPTION | AVAILABLE/CAPACITY | SCHEDULE");
                for (Course course : courses) {
                        System.out.println(course);
                }
        }

        // Method to register a student for a course
        private static void registerStudentForCourse(ArrayList<Student> students, ArrayList<Course> courses, Scanner scanner) {
                System.out.print("Enter student ID: ");
                String studentId = scanner.nextLine();

                Student student = findStudent(students, studentId);
                if (student == null) {
                        System.out.println("Student not found!");
                        return;
                }

                System.out.print("Enter course code: ");
                String courseCode = scanner.nextLine();

                Course course = findCourse(courses, courseCode);
                if (course == null) {
                        System.out.println("Course not found!");
                        return;
                }

                if (course.getAvailableSlots() <= 0) {
                        System.out.println("Course is full!");
                        return;
                }

                if (student.isRegisteredFor(course)) {
                        System.out.println("Student is already registered for this course!");
                        return;
                }

                student.registerCourse(course);
                course.decreaseAvailableSlots();
                System.out.println("Successfully registered " + student.getName() + " for " + course.getTitle());
        }

        // Method to drop a course
        private static void dropCourse(ArrayList<Student> students, ArrayList<Course> courses, Scanner scanner) {
                System.out.print("Enter student ID: ");
                String studentId = scanner.nextLine();

                Student student = findStudent(students, studentId);
                if (student == null) {
                        System.out.println("Student not found!");
                        return;
                }

                System.out.print("Enter course code to drop: ");
                String courseCode = scanner.nextLine();

                Course course = findCourse(courses, courseCode);
                if (course == null) {
                        System.out.println("Course not found!");
                        return;
                }

                if (student.dropCourse(course)) {
                        course.increaseAvailableSlots();
                        System.out.println("Course dropped successfully!");
                } else {
                        System.out.println("Student is not registered for this course!");
                }
        }

        // Method to view a student's registered courses
        private static void viewStudentCourses(ArrayList<Student> students, Scanner scanner) {
                System.out.print("Enter student ID: ");
                String studentId = scanner.nextLine();

                Student student = findStudent(students, studentId);
                if (student == null) {
                        System.out.println("Student not found!");
                        return;
                }

                student.listRegisteredCourses();
        }

        // Helper method to find a student by ID
        private static Student findStudent(ArrayList<Student> students, String id) {
                for (Student student : students) {
                        if (student.getId().equals(id)) {
                                return student;
                        }
                }
                return null;
        }

        // Helper method to find a course by code
        private static Course findCourse(ArrayList<Course> courses, String code) {
                for (Course course : courses) {
                        if (course.getCode().equals(code)) {
                                return course;
                        }
                }
                return null;
        }
}

// Course class to store course information
class Course {
        private String code;
        private String title;
        private String description;
        private int capacity;
        private int availableSlots;
        private String schedule;

        public Course(String code, String title, String description, int capacity, String schedule) {
                this.code = code;
                this.title = title;
                this.description = description;
                this.capacity = capacity;
                this.availableSlots = capacity;
                this.schedule = schedule;
        }

        public String getCode() {
                return code;
        }

        public String getTitle() {
                return title;
        }

        public int getAvailableSlots() {
                return availableSlots;
        }

        public void decreaseAvailableSlots() {
                if (availableSlots > 0) {
                        availableSlots--;
                }
        }

        public void increaseAvailableSlots() {
                if (availableSlots < capacity) {
                        availableSlots++;
                }
        }

        @Override
        public String toString() {
                return code + " | " + title + " | " + description + " | "
                        + availableSlots + "/" + capacity + " | " + schedule;
        }
}

// Student class to store student information and registered courses
class Student {
        private String id;
        private String name;
        private ArrayList<Course> registeredCourses;

        public Student(String id, String name) {
                this.id = id;
                this.name = name;
                this.registeredCourses = new ArrayList<>();
        }

        public String getId() {
                return id;
        }

        public String getName() {
                return name;
        }

        public void registerCourse(Course course) {
                registeredCourses.add(course);
        }

        public boolean dropCourse(Course course) {
                for (int i = 0; i < registeredCourses.size(); i++) {
                        if (registeredCourses.get(i).getCode().equals(course.getCode())) {
                                registeredCourses.remove(i);
                                return true;
                        }
                }
                return false;
        }

        public boolean isRegisteredFor(Course course) {
                for (Course c : registeredCourses) {
                        if (c.getCode().equals(course.getCode())) {
                                return true;
                        }
                }
                return false;
        }

        public void listRegisteredCourses() {
                System.out.println("\n===== COURSES REGISTERED BY " + name + " =====");
                if (registeredCourses.isEmpty()) {
                        System.out.println("No courses registered.");
                } else {
                        for (Course course : registeredCourses) {
                                System.out.println(course);
                        }
                }
        }
}
