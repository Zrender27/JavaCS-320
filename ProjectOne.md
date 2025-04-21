# JavaCS-320
import java.util.Objects;

public class Contact {
    private final String contactId;
    private String firstName;
    private String lastName;
    private String phoneNumber;
    private String address;

    public Contact(String contactId, String firstName, String lastName, String phoneNumber, String address) {
        if (contactId == null || contactId.length() > 10) {
            throw new IllegalArgumentException("Invalid contact ID");
        }
        if (firstName == null || firstName.length() > 10) {
            throw new IllegalArgumentException("Invalid first name");
        }
        if (lastName == null || lastName.length() > 10) {
            throw new IllegalArgumentException("Invalid last name");
        }
        if (phoneNumber == null || phoneNumber.length() != 10 || !phoneNumber.matches("\\d+")) {
            throw new IllegalArgumentException("Invalid phone number");
        }
        if (address == null || address.length() > 30) {
            throw new IllegalArgumentException("Invalid address");
        }

        this.contactId = contactId;
        this.firstName = firstName;
        this.lastName = lastName;
        this.phoneNumber = phoneNumber;
        this.address = address;
    }

    public String getContactId() {
        return contactId;
    }

    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        if (firstName == null || firstName.length() > 10) {
            throw new IllegalArgumentException("Invalid first name");
        }
        this.firstName = firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        if (lastName == null || lastName.length() > 10) {
            throw new IllegalArgumentException("Invalid last name");
        }
        this.lastName = lastName;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        if (phoneNumber == null || phoneNumber.length() != 10 || !phoneNumber.matches("\\d+")) {
            throw new IllegalArgumentException("Invalid phone number");
        }
        this.phoneNumber = phoneNumber;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        if (address == null || address.length() > 30) {
            throw new IllegalArgumentException("Invalid address");
        }
        this.address = address;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Contact contact = (Contact) o;
        return contactId.equals(contact.contactId);
    }

    @Override
    public int hashCode() {
        return Objects.hash(contactId);
    }
}

//ContactService.java
import java.util.HashMap;
import java.util.Map;

public class ContactService {
    private final Map<String, Contact> contacts = new HashMap<>();

    public void addContact(Contact contact) {
        if (contacts.containsKey(contact.getContactId())) {
            throw new IllegalArgumentException("Contact ID already exists");
        }
        contacts.put(contact.getContactId(), contact);
    }

    public void deleteContact(String contactId) {
        if (!contacts.containsKey(contactId)) {
            throw new IllegalArgumentException("Contact ID not found");
        }
        contacts.remove(contactId);
    }

    public void updateContact(String contactId, String firstName, String lastName, String phone, String address) {
        Contact contact = contacts.get(contactId);
        if (contact == null) {
            throw new IllegalArgumentException("Contact ID not found");
        }
        if (firstName != null) {
            contact.setFirstName(firstName);
        }
        if (lastName != null) {
            contact.setLastName(lastName);
        }
        if (phone != null) {
            contact.setPhone(phone);
        }
        if (address != null) {
            contact.setAddress(address);
        }
    }

    public Contact getContact(String contactId) {
        return contacts.get(contactId);
    }
}
//ContactTest.java
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class ContactTest {

    @Test
    void testContactConstructorValidInput() {
        Contact contact = new Contact("12345", "Stacy", "Corn", "1234567890", "135 Elk Avenue");
        assertEquals("12345", contact.getContactId());
        assertEquals("Stacy", contact.getFirstName());
        assertEquals("Corn", contact.getLastName());
        assertEquals("1234567890", contact.getPhone());
        assertEquals("135 Elk Avenue", contact.getAddress());
    }

    @Test
    void testContactConstructorInvalidId() {
        assertThrows(IllegalArgumentException.class, () -> new Contact(null, "Stacy", "Corn", "1234567890", "135 Elk Avenue"));
        assertThrows(IllegalArgumentException.class, () -> new Contact("12345678901", "Stacy", "Corn", "1234567890", "135 Elk Avenue"));
    }

    @Test
    void testContactConstructorInvalidFields() {
        assertThrows(IllegalArgumentException.class, () -> new Contact("12345", null, "Corn", "1234567890", "135 Elk Avenue"));
        assertThrows(IllegalArgumentException.class, () -> new Contact("12345", "Stacy", null, "1234567890", "135 Elk Avenue"));
        assertThrows(IllegalArgumentException.class, () -> new Contact("12345", "Stacy", "Corn", null, "135 Elk Avenue"));
        assertThrows(IllegalArgumentException.class, () -> new Contact("12345", "Stacy", "Corn", "12345", "135 Elk Avenue"));
        assertThrows(IllegalArgumentException.class, () -> new Contact("12345", "Stacy", "Corn", "1234567890", null));
        assertThrows(IllegalArgumentException.class, () -> new Contact("12345", "Stacy", "Corn", "1234567890", "This address is way too long and should fail."));
    }

    @Test
    void testSettersValidInput() {
        Contact contact = new Contact("12345", "Stacy", "Corn", "1234567890", "135 Elk Avenue");
        contact.setFirstName("Jamie");
        contact.setLastName("Smith");
        contact.setPhone("0987654321");
        contact.setAddress("654 Coffee Court");
        
        assertEquals("Jamie", contact.getFirstName());
        assertEquals("Smith", contact.getLastName());
        assertEquals("0987654321", contact.getPhone());
        assertEquals("654 Coffee Court", contact.getAddress());
    }

    @Test
    void testSettersInvalidInput() {
        Contact contact = new Contact("12345", "Stacy", "Corn", "1234567890", "135 Elk Avenue");
        assertThrows(IllegalArgumentException.class, () -> contact.setFirstName(null));
        assertThrows(IllegalArgumentException.class, () -> contact.setLastName(null));
        assertThrows(IllegalArgumentException.class, () -> contact.setPhone("12345"));
        assertThrows(IllegalArgumentException.class, () -> contact.setAddress(null));
        assertThrows(IllegalArgumentException.class, () -> contact.setAddress("This address is way too long and should fail."));
    }
}
//ContactService.java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

import java.util.NoSuchElementException;

class ContactServiceTest {

    @Test
    void testAddContactValid() {
        ContactService contactService = new ContactService();
        Contact contact = new Contact("12345", "Stacy", "Corn", "1234567890", "135 Elk Avenue");
        
        contactService.addContact(contact);
        assertEquals(contact, contactService.getContact("12345"));
    }

    @Test
    void testAddContactDuplicateId() {
        ContactService contactService = new ContactService();
        Contact contact1 = new Contact("12345", "Stacy", "Corn", "1234567890", "135 Elk Avenue");
        Contact contact2 = new Contact("12345", "Jamie", "Smith", "0987654321", "654 Coffee Court");
        
        contactService.addContact(contact1);
        assertThrows(IllegalArgumentException.class, () -> contactService.addContact(contact2));
    }

    @Test
    void testDeleteContactValid() {
        ContactService contactService = new ContactService();
        Contact contact = new Contact("12345", "Stacy", "Corn", "1234567890", "135 Elk Avenue");
        
        contactService.addContact(contact);
        contactService.deleteContact("12345");
        assertNull(contactService.getContact("12345"));
    }

    @Test
    void testDeleteContactInvalidId() {
        ContactService contactService = new ContactService();
        assertThrows(IllegalArgumentException.class, () -> contactService.deleteContact("nonexistent"));
    }

    @Test
    void testUpdateContactValid() {
        ContactService contactService = new ContactService();
        Contact contact = new Contact("12345", "Stacy", "Corn", "1234567890", "135 Elk Avenue");
        
        contactService.addContact(contact);
        contactService.updateContact("12345", "Jamie", "Smith", "0987654321", "654 Coffee Court");
        
        Contact updatedContact = contactService.getContact("12345");
        assertEquals("Jamie", updatedContact.getFirstName());
        assertEquals("Smith", updatedContact.getLastName());
        assertEquals("0987654321", updatedContact.getPhone());
        assertEquals("654 Coffee Court", updatedContact.getAddress());
    }

    @Test
    void testUpdateContactInvalidId() {
        ContactService contactService = new ContactService();
        assertThrows(IllegalArgumentException.class, () -> contactService.updateContact("nonexistent", "Jamie", "Smith", "0987654321", "654 Coffee Court"));
    }
}
