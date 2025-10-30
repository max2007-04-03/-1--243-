Miles, [Oct 30, 2025 at 12:37]
import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.time.LocalDate;
import java.util.*;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;
import java.util.Optional;

// ============== ІНТЕРФЕЙСИ ==============

interface Searchable {
    boolean matches(String criteria);
}

interface Rentable {
    boolean rent(String borrowerName);
    boolean returnItem();
    boolean isAvailable();
}

interface Displayable {
    String[] toTableRow();
}

// ============== БАЗОВІ КЛАСИ ==============
abstract class LibraryItem implements Searchable, Rentable, Displayable {
    protected String internalId;
    protected String itemTitle;
    protected int publicationYear;
    protected boolean isAvailable;
    protected String currentBorrower;
    protected LocalDate dateOfRent;

    public LibraryItem(String id, String title, int year) {
        this.internalId = id;
        this.itemTitle = title;
        this.publicationYear = year;
        this.isAvailable = true;
    }

    @Override
    public boolean rent(String borrowerName) {
        if (!this.isAvailable) {
            return false;
        }
        this.isAvailable = false;
        this.currentBorrower = borrowerName;
        this.dateOfRent = LocalDate.now();
        return true;
    }

    @Override
    public boolean returnItem() {
        if (this.isAvailable) {
            return false;
        }
        this.isAvailable = true;
        this.currentBorrower = null;
        this.dateOfRent = null;
        return true;
    }

    @Override
    public boolean isAvailable() {
        return this.isAvailable;
    }

    @Override
    public boolean matches(String criteria) {
        String lowerCriteria = criteria.toLowerCase();
        return internalId.toLowerCase().contains(lowerCriteria) ||
                itemTitle.toLowerCase().contains(lowerCriteria);
    }

    public abstract String getType();

    public String getId() { return internalId; }
    public String getTitle() { return itemTitle; }
    public int getYear() { return publicationYear; }
    public String getRentedBy() { return currentBorrower; }
}

class Book extends LibraryItem {
    private String bookAuthor;
    private int pageCount;

    public Book(String id, String title, int year, String author, int pages) {
        super(id, title, year);
        this.bookAuthor = author;
        this.pageCount = pages;
    }

    @Override
    public String getType() {
        return "Книга";
    }

    @Override
    public String[] toTableRow() {
        return new String[]{
                internalId,
                getType(),
                itemTitle,
                String.valueOf(publicationYear),
                "Автор: " + bookAuthor + ", Сторінок: " + pageCount,
                isAvailable ? "Доступна" : "Орендована",
                currentBorrower != null ? currentBorrower : "-"
        };
    }

    @Override
    public boolean matches(String criteria) {
        return super.matches(criteria) || bookAuthor.toLowerCase().contains(criteria.toLowerCase());
    }

    public String getAuthor() {
        return bookAuthor;
    }

    public int getPages() {
        return pageCount;
    }
}

class Magazine extends LibraryItem {
    private int issueNum;
    private String publisherName;

    public Magazine(String id, String title, int year, int issueNumber, String publisher) {
        super(id, title, year);
        this.issueNum = issueNumber;
        this.publisherName = publisher;
    }

    @Override
    public String getType() {
        return "Журнал";
    }

    @Override
    public String[] toTableRow() {
        return new String[]{
                internalId,
                getType(),
                itemTitle,
                String.valueOf(publicationYear),
                "Випуск: " + issueNum + ", Видавець: " + publisherName,
                isAvailable ? "Доступний" : "Орендований",
                currentBorrower != null ? currentBorrower : "-"
        };
    }
