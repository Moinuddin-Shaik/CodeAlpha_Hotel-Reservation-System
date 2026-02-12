# Hotel-Reservation-System
This project is a Java-based Hotel Reservation System developed as part of the CodeAlpha internship program. It enables streamlined hotel room booking and management through a simple, interactive console-based interface.

🏨 Features
Add and manage hotel room bookings

View reservation details by customer

Check availability and pricing

Cancel or modify existing bookings

Efficient data storage using arrays or ArrayLists

🛠 Technologies Used
Java

Object-Oriented Programming (OOP)

Console Input/Output

Array and List-based data handling

📂 Project Structure
HotelReservationSystem.java: Contains core logic and user menu

Modular design for scalability and easy maintenance

🎯 Objective
To simulate a basic hotel booking platform using Java fundamentals, enhancing understanding of data structures, user input handling, and logical flow control in real-world scenarios.

🔖 Status
Completed as part of the CodeAlpha Internship - June Batch

Below is the Complete Code for the Project
import java.io.*;
import java.util.*;

public class HotelSystem {

    static class Room {
        int roomNumber;
        String category;
        boolean isAvailable;

        public Room(int roomNumber, String category, boolean isAvailable) {
            this.roomNumber = roomNumber;
            this.category = category;
            this.isAvailable = isAvailable;
        }

        public String toFileString() {
            return roomNumber + "," + category + "," + isAvailable;
        }

        public static Room fromFileString(String line) {
            String[] parts = line.split(",");
            return new Room(Integer.parseInt(parts[0]), parts[1], Boolean.parseBoolean(parts[2]));
        }
    }

    static class Booking {
        String customerName;
        int roomNumber;
        String paymentStatus;

        public Booking(String customerName, int roomNumber, String paymentStatus) {
            this.customerName = customerName;
            this.roomNumber = roomNumber;
            this.paymentStatus = paymentStatus;
        }

        public String toFileString() {
            return customerName + "," + roomNumber + "," + paymentStatus;
        }

        public static Booking fromFileString(String line) {
            String[] parts = line.split(",");
            return new Booking(parts[0], Integer.parseInt(parts[1]), parts[2]);
        }
    }

    static List<Room> rooms = new ArrayList<>();
    static List<Booking> bookings = new ArrayList<>();
    static final String ROOMS_FILE = "rooms.txt";
    static final String BOOKINGS_FILE = "bookings.txt";

    public static void loadRooms() throws IOException {
        rooms.clear();
        File file = new File(ROOMS_FILE);
        if (!file.exists()) return;
        BufferedReader br = new BufferedReader(new FileReader(file));
        String line;
        while ((line = br.readLine()) != null) {
            rooms.add(Room.fromFileString(line));
        }
        br.close();
    }

    public static void saveRooms() throws IOException {
        BufferedWriter bw = new BufferedWriter(new FileWriter(ROOMS_FILE));
        for (Room r : rooms) {
            bw.write(r.toFileString());
            bw.newLine();
        }
        bw.close();
    }

    public static void loadBookings() throws IOException {
        bookings.clear();
        File file = new File(BOOKINGS_FILE);
        if (!file.exists()) return;
        BufferedReader br = new BufferedReader(new FileReader(file));
        String line;
        while ((line = br.readLine()) != null) {
            bookings.add(Booking.fromFileString(line));
        }
        br.close();
    }

    public static void saveBookings() throws IOException {
        BufferedWriter bw = new BufferedWriter(new FileWriter(BOOKINGS_FILE));
        for (Booking b : bookings) {
            bw.write(b.toFileString());
            bw.newLine();
        }
        bw.close();
    }

    public static void searchRooms(String category) {
        System.out.println("Available " + category + " rooms:");
        boolean found = false;
        for (Room r : rooms) {
            if (r.category.equalsIgnoreCase(category) && r.isAvailable) {
                System.out.println("Room No: " + r.roomNumber);
                found = true;
            }
        }
        if (!found) {
            System.out.println("No available rooms in this category.");
        }
    }

    public static void bookRoom(String customer, int roomNo) throws IOException {
        for (Room r : rooms) {
            if (r.roomNumber == roomNo && r.isAvailable) {
                r.isAvailable = false;
                bookings.add(new Booking(customer, roomNo, "Paid"));
                saveRooms();
                saveBookings();
                System.out.println("✅ Room " + roomNo + " booked successfully by " + customer);
                return;
            }
        }
        System.out.println("❌ Room not available or doesn't exist.");
    }

    public static void cancelBooking(int roomNo) throws IOException {
        Iterator<Booking> it = bookings.iterator();
        while (it.hasNext()) {
            Booking b = it.next();
            if (b.roomNumber == roomNo) {
                it.remove();
                for (Room r : rooms) {
                    if (r.roomNumber == roomNo) {
                        r.isAvailable = true;
                    }
                }
                saveRooms();
                saveBookings();
                System.out.println("✅ Booking for room " + roomNo + " cancelled.");
                return;
            }
        }
        System.out.println("❌ No booking found for room " + roomNo);
    }

    public static void viewBookings() {
        System.out.println("📋 Current Bookings:");
        if (bookings.isEmpty()) {
            System.out.println("No bookings yet.");
            return;
        }
        for (Booking b : bookings) {
            System.out.println("Customer: " + b.customerName + ", Room: " + b.roomNumber + ", Payment: " + b.paymentStatus);
        }
    }

    public static void main(String[] args) throws IOException {
        Scanner sc = new Scanner(System.in);

        // First time setup: create rooms file if missing
        File roomsFile = new File(ROOMS_FILE);
        if (!roomsFile.exists()) {
            BufferedWriter bw = new BufferedWriter(new FileWriter(roomsFile));
            bw.write("101,Standard,true\n102,Standard,true\n201,Deluxe,true\n202,Deluxe,true\n301,Suite,true\n302,Suite,true");
            bw.close();
        }

        loadRooms();
        loadBookings();

        while (true) {
            System.out.println("\n====== Hotel Reservation System ======");
            System.out.println("1. Search Room");
            System.out.println("2. Book Room");
            System.out.println("3. Cancel Booking");
            System.out.println("4. View Bookings");
            System.out.println("5. Exit");
            System.out.print("Enter your choice: ");
            int choice = sc.nextInt();
            sc.nextLine(); // consume newline

            switch (choice) {
                case 1:
                    System.out.print("Enter category (Standard / Deluxe / Suite): ");
                    String category = sc.nextLine();
                    searchRooms(category);
                    break;
                case 2:
                    System.out.print("Enter your name: ");
                    String name = sc.nextLine();
                    System.out.print("Enter room number to book: ");
                    int roomNo = sc.nextInt();
                    bookRoom(name, roomNo);
                    break;
                case 3:
                    System.out.print("Enter room number to cancel: ");
                    int cancelNo = sc.nextInt();
                    cancelBooking(cancelNo);
                    break;
                case 4:
                    viewBookings();
                    break;
                case 5:
                    System.out.println("Thank you! Exiting...");
                    return;
                default:
                    System.out.println("Invalid choice. Try again.");
            }
        }
    }
}
OUTPUT:
<img width="647" height="408" alt="image" src="https://github.com/user-attachments/assets/cc70d04a-8333-441e-ba0b-6bf402c5c89e" />

