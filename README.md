import java.io.*;
import java.util.*;

class Room {
    private int roomNumber;
    private String category;
    private double price;
    private boolean available;

    public Room(int roomNumber, String category, double price) {
        this.roomNumber = roomNumber;
        this.category = category;
        this.price = price;
        this.available = true;
    }

    public int getRoomNumber() {
        return roomNumber;
    }

    public String getCategory() {
        return category;
    }

    public double getPrice() {
        return price;
    }

    public boolean isAvailable() {
        return available;
    }

    public void setAvailable(boolean available) {
        this.available = available;
    }

    @Override
    public String toString() {
        return "Room " + roomNumber +
                " | Category: " + category +
                " | Price: Rs." + price +
                " | Available: " +
                (available ? "Yes" : "No");
    }
}

class Reservation {
    private int bookingId;
    private String customerName;
    private int roomNumber;
    private String category;
    private double amount;

    public Reservation(int bookingId,
                       String customerName,
                       int roomNumber,
                       String category,
                       double amount) {

        this.bookingId = bookingId;
        this.customerName = customerName;
        this.roomNumber = roomNumber;
        this.category = category;
        this.amount = amount;
    }

    public int getBookingId() {
        return bookingId;
    }

    public int getRoomNumber() {
        return roomNumber;
    }

    public void displayDetails() {
        System.out.println("\n========== BOOKING DETAILS ==========");
        System.out.println("Booking ID   : " + bookingId);
        System.out.println("Customer     : " + customerName);
        System.out.println("Room Number  : " + roomNumber);
        System.out.println("Room Category: " + category);
        System.out.println("Amount Paid  : Rs." + amount);
        System.out.println("Status       : Confirmed");
        System.out.println("=====================================");
    }

    @Override
    public String toString() {
        return bookingId + "," +
                customerName + "," +
                roomNumber + "," +
                category + "," +
                amount;
    }
}

public class HotelReservationSystem {

    static Scanner scanner = new Scanner(System.in);

    static ArrayList<Room> rooms = new ArrayList<>();
    static ArrayList<Reservation> reservations = new ArrayList<>();

    static int nextBookingId = 1001;

    static final String FILE_NAME = "hotel_bookings.txt";

    public static void main(String[] args) {

        initializeRooms();
        loadBookings();

        int choice;

        do {

            System.out.println("\n====================================");
            System.out.println("       HOTEL RESERVATION SYSTEM");
            System.out.println("====================================");
            System.out.println("1. Search Available Rooms");
            System.out.println("2. Book Room");
            System.out.println("3. Cancel Reservation");
            System.out.println("4. View Booking");
            System.out.println("5. View All Rooms");
            System.out.println("6. Exit");
            System.out.println("====================================");

            System.out.print("Enter your choice: ");
            choice = scanner.nextInt();

            switch (choice) {

                case 1:
                    searchRooms();
                    break;

                case 2:
                    bookRoom();
                    break;

                case 3:
                    cancelReservation();
                    break;

                case 4:
                    viewBooking();
                    break;

                case 5:
                    viewAllRooms();
                    break;

                case 6:
                    System.out.println(
                            "Thank you for using the Hotel Reservation System!"
                    );
                    break;

                default:
                    System.out.println("Invalid choice!");
            }

        } while (choice != 6);

        scanner.close();
    }

    static void initializeRooms() {

        rooms.add(new Room(101, "Standard", 1500));
        rooms.add(new Room(102, "Standard", 1500));

        rooms.add(new Room(201, "Deluxe", 2500));
        rooms.add(new Room(202, "Deluxe", 2500));

        rooms.add(new Room(301, "Suite", 4000));
        rooms.add(new Room(302, "Suite", 4000));
    }

    static void searchRooms() {

        scanner.nextLine();

        System.out.print(
                "Enter category (Standard/Deluxe/Suite): "
        );

        String category = scanner.nextLine();

        boolean found = false;

        System.out.println("\nAvailable Rooms:");

        for (Room room : rooms) {

            if (room.getCategory().equalsIgnoreCase(category)
                    && room.isAvailable()) {

                System.out.println(room);
                found = true;
            }
        }

        if (!found) {
            System.out.println("No rooms available in this category.");
        }
    }

    static void bookRoom() {

        scanner.nextLine();

        System.out.print("Enter customer name: ");
        String customerName = scanner.nextLine();

        System.out.print(
                "Enter room category (Standard/Deluxe/Suite): "
        );

        String category = scanner.nextLine();

        Room selectedRoom = null;

        for (Room room : rooms) {

            if (room.getCategory().equalsIgnoreCase(category)
                    && room.isAvailable()) {

                selectedRoom = room;
                break;
            }
        }

        if (selectedRoom == null) {

            System.out.println(
                    "Sorry, no room is available in this category."
            );

            return;
        }

        System.out.println("\nRoom Found:");
        System.out.println(selectedRoom);

        System.out.print(
                "Do you want to book this room? (yes/no): "
        );

        String confirmation = scanner.nextLine();

        if (!confirmation.equalsIgnoreCase("yes")) {

            System.out.println("Booking cancelled.");
            return;
        }

        double amount = selectedRoom.getPrice();

        System.out.println("\nTotal Amount: Rs." + amount);

        System.out.print(
                "Simulate payment? (yes/no): "
        );

        String payment = scanner.nextLine();

        if (!payment.equalsIgnoreCase("yes")) {

            System.out.println(
                    "Payment unsuccessful. Booking cancelled."
            );

            return;
        }

        selectedRoom.setAvailable(false);

        Reservation reservation =
                new Reservation(
                        nextBookingId,
                        customerName,
                        selectedRoom.getRoomNumber(),
                        selectedRoom.getCategory(),
                        amount
                );

        reservations.add(reservation);

        saveBookings();

        System.out.println(
                "\nPayment successful!"
        );

        System.out.println(
                "Room booked successfully."
        );

        System.out.println(
                "Your Booking ID is: "
                        + nextBookingId
        );

        reservation.displayDetails();

        nextBookingId++;
    }

    static void cancelReservation() {

        System.out.print(
                "Enter Booking ID to cancel: "
        );

        int bookingId = scanner.nextInt();

        Reservation foundReservation = null;

        for (Reservation reservation : reservations) {

            if (reservation.getBookingId() == bookingId) {

                foundReservation = reservation;
                break;
            }
        }

        if (foundReservation == null) {

            System.out.println(
                    "Booking not found."
            );

            return;
        }

        for (Room room : rooms) {

            if (room.getRoomNumber()
                    == foundReservation.getRoomNumber()) {

                room.setAvailable(true);
                break;
            }
        }

        reservations.remove(foundReservation);

        saveBookings();

        System.out.println(
                "Reservation cancelled successfully."
        );

        System.out.println(
                "Payment refund simulated successfully."
        );
    }

    static void viewBooking() {

        System.out.print(
                "Enter Booking ID: "
        );

        int bookingId = scanner.nextInt();

        for (Reservation reservation : reservations) {

            if (reservation.getBookingId() == bookingId) {

                reservation.displayDetails();
                return;
            }
        }

        System.out.println(
                "Booking not found."
        );
    }

    static void viewAllRooms() {

        System.out.println("\n========== ALL ROOMS ==========");

        for (Room room : rooms) {
            System.out.println(room);
        }

        System.out.println("===============================");
    }

    static void saveBookings() {

        try {

            FileWriter writer =
                    new FileWriter(FILE_NAME);

            for (Reservation reservation : reservations) {

                writer.write(
                        reservation.toString()
                                + "\n"
                );
            }

            writer.close();

        } catch (IOException e) {

            System.out.println(
                    "Error saving bookings: "
                            + e.getMessage()
            );
        }
    }

    static void loadBookings() {

        File file = new File(FILE_NAME);

        if (!file.exists()) {
            return;
        }

        try {

            BufferedReader reader =
                    new BufferedReader(
                            new FileReader(file)
                    );

            String line;

            while ((line = reader.readLine()) != null) {

                String[] data =
                        line.split(",");

                if (data.length == 5) {

                    int bookingId =
                            Integer.parseInt(data[0]);

                    String customerName =
                            data[1];

                    int roomNumber =
                            Integer.parseInt(data[2]);

                    String category =
                            data[3];

                    double amount =
                            Double.parseDouble(data[4]);

                    Reservation reservation =
                            new Reservation(
                                    bookingId,
                                    customerName,
                                    roomNumber,
                                    category,
                                    amount
                            );

                    reservations.add(reservation);

                    for (Room room : rooms) {

                        if (room.getRoomNumber()
                                == roomNumber) {

                            room.setAvailable(false);
                            break;
                        }
                    }

                    if (bookingId >= nextBookingId) {
                        nextBookingId = bookingId + 1;
                    }
                }
            }

            reader.close();

        } catch (Exception e) {

            System.out.println(
                    "Error loading bookings: "
                            + e.getMessage()
            );
        }
    }
}
