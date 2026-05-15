import java.sql.*;
import java.util.Scanner;

class Student {
    int id;
    String name;
    int age;
    String course;

    Student(int id, String name, int age, String course) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.course = course;
    }
}

class DatabaseManager {
    private static final String URL = "jdbc:mysql://localhost:3306/studentdb";
    private static final String USER = "root";
    private static final String PASSWORD = "password";

    public Connection connect() throws SQLException {
        return DriverManager.getConnection(URL, USER, PASSWORD);
    }

    public void addStudent(Student student) {
        String query = "INSERT INTO students VALUES (?, ?, ?, ?)";
        try (Connection conn = connect(); PreparedStatement stmt = conn.prepareStatement(query)) {
            stmt.setInt(1, student.id);
            stmt.setString(2, student.name);
            stmt.setInt(3, student.age);
            stmt.setString(4, student.course);
            stmt.executeUpdate();
            System.out.println("Student added successfully!");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void viewStudents() {
        String query = "SELECT * FROM students";
        try (Connection conn = connect(); Statement stmt = conn.createStatement(); ResultSet rs = stmt.executeQuery(query)) {
            while (rs.next()) {
                System.out.println(rs.getInt("id") + " | " + rs.getString("name") + " | " + rs.getInt("age") + " | " + rs.getString("course"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void deleteStudent(int id) {
        String query = "DELETE FROM students WHERE id = ?";
        try (Connection conn = connect(); PreparedStatement stmt = conn.prepareStatement(query)) {
            stmt.setInt(1, id);
            stmt.executeUpdate();
            System.out.println("Student deleted successfully!");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

public class StudentManagementSystem {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        DatabaseManager db = new DatabaseManager();

        while (true) {
            System.out.println("\n1. Add Student\n2. View Students\n3. Delete Student\n4. Exit");
            int choice = sc.nextInt();

            switch (choice) {
                case 1:
                    System.out.print("Enter ID: ");
                    int id = sc.nextInt();
                    sc.nextLine();
                    System.out.print("Enter Name: ");
                    String name = sc.nextLine();
                    System.out.print("Enter Age: ");
                    int age = sc.nextInt();
                    sc.nextLine();
                    System.out.print("Enter Course: ");
                    String course = sc.nextLine();
                    db.addStudent(new Student(id, name, age, course));
                    break;
                case 2:
                    db.viewStudents();
                    break;
                case 3:
                    System.out.print("Enter ID to delete: ");
                    int deleteId = sc.nextInt();
                    db.deleteStudent(deleteId);
                    break;
                case 4:
                    System.exit(0);
                default:
                    System.out.println("Invalid choice!");
            }
        }
    }
}
