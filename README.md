package jdbc;
import java.sql.*;
import java.util.Scanner;

public class HotelManagement {

    public static void main(String[] args) {

        String url = "jdbc:mysql://localhost:3306/hotel_db";
        String user = "root";      // Your MySQL username
        String pass = "@shree101"; // Your MySQL password

        Scanner sc = new Scanner(System.in);

        try {
            // Load and register MySQL driver
            Class.forName("com.mysql.cj.jdbc.Driver");

            // Establish database connection
            Connection con = DriverManager.getConnection(url, user, pass);
            System.out.println("✅ Database connected successfully!");

            while (true) {
                System.out.println("\n=== Hotel Management Menu ===");
                System.out.println("1. Add Room");
                System.out.println("2. View Rooms");
                System.out.println("3. Update Room");
                System.out.println("4. Delete Room");
                System.out.println("5. Exit");
                System.out.print("Enter your choice: ");
                int choice = sc.nextInt();

                switch (choice) {

                    case 1:
                        // ➕ ADD Room
                        System.out.print("Enter Room ID: ");
                        int roomId = sc.nextInt();
                        sc.nextLine();
                        System.out.print("Enter Room Type (e.g., Deluxe, Standard): ");
                        String roomType = sc.nextLine();
                        System.out.print("Enter Price per Night: ");
                        double price = sc.nextDouble();
                        System.out.print("Is Room Available? (yes/no): ");
                        String available = sc.next();

                        String insertQuery = "INSERT INTO rooms VALUES (?, ?, ?, ?)";
                        PreparedStatement psInsert = con.prepareStatement(insertQuery);
                        psInsert.setInt(1, roomId);
                        psInsert.setString(2, roomType);
                        psInsert.setDouble(3, price);
                        psInsert.setString(4, available);

                        int rowsInserted = psInsert.executeUpdate();
                        System.out.println(rowsInserted + " record inserted successfully!");
                        psInsert.close();
                        break;

                    case 2:
                        // 📋 VIEW Rooms
                        String selectQuery = "SELECT * FROM rooms";
                        Statement stmt = con.createStatement();
                        ResultSet rs = stmt.executeQuery(selectQuery);

                        System.out.println("\nRoomID\tType\tPrice\tAvailable");
                        System.out.println("-----------------------------------");
                        while (rs.next()) {
                            System.out.println(
                                rs.getInt("room_id") + "\t" +
                                rs.getString("room_type") + "\t" +
                                rs.getDouble("price_per_night") + "\t" +
                                rs.getString("availability")
                            );
                        }
                        rs.close();
                        stmt.close();
                        break;

                    case 3:
                        // ✏ UPDATE Room
                        System.out.print("Enter Room ID to update: ");
                        int updateId = sc.nextInt();
                        sc.nextLine();
                        System.out.print("Enter new Room Type: ");
                        String newType = sc.nextLine();
                        System.out.print("Enter new Price per Night: ");
                        double newPrice = sc.nextDouble();
                        System.out.print("Is Room Available? (yes/no): ");
                        String newAvail = sc.next();

                        String updateQuery = "UPDATE rooms SET room_type=?, price_per_night=?, availability=? WHERE room_id=?";
                        PreparedStatement psUpdate = con.prepareStatement(updateQuery);
                        psUpdate.setString(1, newType);
                        psUpdate.setDouble(2, newPrice);
                        psUpdate.setString(3, newAvail);
                        psUpdate.setInt(4, updateId);

                        int rowsUpdated = psUpdate.executeUpdate();
                        System.out.println(rowsUpdated + " record updated successfully!");
                        psUpdate.close();
                        break;

                    case 4:
                        // ❌ DELETE Room
                        System.out.print("Enter Room ID to delete: ");
                        int deleteId = sc.nextInt();

                        String deleteQuery = "DELETE FROM rooms WHERE room_id=?";
                        PreparedStatement psDelete = con.prepareStatement(deleteQuery);
                        psDelete.setInt(1, deleteId);

                        int rowsDeleted = psDelete.executeUpdate();
                        System.out.println(rowsDeleted + " record deleted successfully!");
                        psDelete.close();
                        break;

                    case 5:
                        // 🚪 EXIT
                        System.out.println("Exiting program... Goodbye!");
                        con.close();
                        sc.close();
                        System.exit(0);

                    default:
                        System.out.println("❌ Invalid choice! Try again.");
                }
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
