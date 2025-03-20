import java.util.*;

class User {
    enum Role { ADMIN, USER }
    private String username;
    private Role role;

    public User(String username, Role role) {
        this.username = username;
        this.role = role;
    }

    public String getUsername() { return username; }
    public Role getRole() { return role; }
}

class Book {
    enum Type { BOOK, MOVIE }
    private String title;
    private String author;
    private String serialNo;
    private Type type;
    private boolean available;

    public Book(String title, String author, String serialNo, Type type) {
        this.title = title;
        this.author = author;
        this.serialNo = serialNo;
        this.type = type;
        this.available = true;
    }

    public String getTitle() { return title; }
    public String getAuthor() { return author; }
    public String getSerialNo() { return serialNo; }
    public Type getType() { return type; }
    public boolean isAvailable() { return available; }
    public void setAvailable(boolean available) { this.available = available; }
}

class Membership {
    private User user;
    private Date startDate;
    private int durationMonths;

    public Membership(User user, int durationMonths) {
        this.user = user;
        this.startDate = new Date();
        this.durationMonths = durationMonths;
    }

    public Date getEndDate() {
        Calendar calendar = Calendar.getInstance();
        calendar.setTime(startDate);
        calendar.add(Calendar.MONTH, durationMonths);
        return calendar.getTime();
    }
}

class BookIssue {
    private User user;
    private Book book;
    private Date issueDate;
    private Date returnDate;

    public BookIssue(User user, Book book) {
        this.user = user;
        this.book = book;
        this.issueDate = new Date();
        Calendar calendar = Calendar.getInstance();
        calendar.setTime(issueDate);
        calendar.add(Calendar.DAY_OF_MONTH, 15);
        this.returnDate = calendar.getTime();
        book.setAvailable(false);
    }

    public Date getReturnDate() { return returnDate; }
}

class BookReturn {
    private BookIssue bookIssue;
    private Date returnDate;
    private boolean finePaid;

    public BookReturn(BookIssue bookIssue, Date returnDate) {
        this.bookIssue = bookIssue;
        this.returnDate = returnDate;
        this.finePaid = false;
    }

    public int calculateFine() {
        long diff = returnDate.getTime() - bookIssue.getReturnDate().getTime();
        int daysLate = (int) (diff / (1000 * 60 * 60 * 24));
        return daysLate > 0 ? daysLate * 5 : 0;
    }
}

public class Main {
    public static void main(String[] args) {
        User admin = new User("Alice", User.Role.ADMIN);
        User user = new User("Bob", User.Role.USER);

        Book book1 = new Book("Java Programming", "John Doe", "12345", Book.Type.BOOK);
        Book book2 = new Book("Data Structures", "Jane Smith", "67890", Book.Type.BOOK);

        Membership membership = new Membership(user, 6);
        System.out.println("Membership End Date: " + membership.getEndDate());

        BookIssue issue = new BookIssue(user, book1);
        System.out.println("Book Issued: " + book1.getTitle() + " Return By: " + issue.getReturnDate());

        Calendar returnCalendar = Calendar.getInstance();
        returnCalendar.setTime(issue.getReturnDate());
        returnCalendar.add(Calendar.DAY_OF_MONTH, 5); // Late return by 5 days
        Date actualReturnDate = returnCalendar.getTime();

        BookReturn bookReturn = new BookReturn(issue, actualReturnDate);
        int fine = bookReturn.calculateFine();
        System.out.println("Fine to be paid: " + fine);
    }
}
