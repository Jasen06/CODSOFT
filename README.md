https://chatgpt.com/canvas/shared/67b64c39ce0c81919583f27cc63d99b2


public class SList {
    private Node head;

    // Add a new item at the end
    public void add(ProductItem item) {
        Node newNode = new Node(item);
        if (head == null) {
            head = newNode;
        } else {
            Node temp = head;
            while (temp.next != null) {
                temp = temp.next;
            }
            temp.next = newNode;
        }
    }

    // Get previous node before a given node
    public Node prev(Node givenNode) {
        if (head == null || head == givenNode) return null;
        Node temp = head;
        while (temp.next != null && temp.next != givenNode) {
            temp = temp.next;
        }
        return temp.next == givenNode ? temp : null;
    }

    // Replace an item in the list
    public ProductItem replace(Node givenNode, ProductItem newItem) {
        if (givenNode == null) return null;
        ProductItem oldItem = givenNode.item;
        givenNode.item = newItem;
        return oldItem;
    }

    // Insert a new node after a given node
    public Node insertAfter(Node givenNode, ProductItem itemToAdd) {
        if (givenNode == null) return null;
        Node newNode = new Node(itemToAdd);
        newNode.next = givenNode.next;
        givenNode.next = newNode;
        return newNode;
    }

    // Insert a new node before a given node
    public Node insertBefore(Node givenNode, ProductItem itemToAdd) {
        if (head == null || givenNode == null) return null;
        if (head == givenNode) {
            Node newNode = new Node(itemToAdd);
            newNode.next = head;
            head = newNode;
            return newNode;
        }
        Node prevNode = prev(givenNode);
        if (prevNode == null) return null;
        return insertAfter(prevNode, itemToAdd);
    }

    // Remove a node from the list
    public ProductItem remove(Node nodeToRemove) {
        if (head == null || nodeToRemove == null) return null;
        if (head == nodeToRemove) {
            ProductItem removedItem = head.item;
            head = head.next;
            return removedItem;
        }
        Node prevNode = prev(nodeToRemove);
        if (prevNode != null) {
            ProductItem removedItem = nodeToRemove.item;
            prevNode.next = nodeToRemove.next;
            return removedItem;
        }
        return null;
    }

    // Search for an item in the list
    public Node search(String itemName) {
        Node temp = head;
        while (temp != null) {
            if (temp.item.getName().equalsIgnoreCase(itemName)) return temp;
            temp = temp.next;
        }
        return null;
    }

    // Convert the list to a string
    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        Node temp = head;
        while (temp != null) {
            sb.append(temp.item).append("\n");
            temp = temp.next;
        }
        return sb.toString();
    }
}






import java.io.*;
import java.util.Scanner;

public class Main {
    private static final String FILE_NAME = "products.dat";

    public static void writeProductItemToFile(ProductItem item) {
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(FILE_NAME, true))) {
            oos.writeObject(item);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static SList readProductItemsFromFile() {
        SList list = new SList();
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(FILE_NAME))) {
            while (true) {
                try {
                    ProductItem item = (ProductItem) ois.readObject();
                    list.add(item);
                } catch (EOFException e) {
                    break;
                }
            }
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
        return list;
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        SList shoppingList = readProductItemsFromFile();

        while (true) {
            System.out.println("\n1. Add Item\n2. Display List\n3. Search Item\n4. Remove Item\n5. Exit");
            System.out.print("Choose an option: ");
            int choice = scanner.nextInt();
            scanner.nextLine();

            switch (choice) {
                case 1 -> {
                    System.out.print("Enter product name: ");
                    String name = scanner.nextLine();
                    System.out.print("Enter product price: ");
                    double price = scanner.nextDouble();
                    ProductItem newItem = new ProductItem(name, price);
                    shoppingList.add(newItem);
                    writeProductItemToFile(newItem);
                }
                case 2 -> System.out.println("\nShopping List:\n" + shoppingList);
                case 3 -> {
                    System.out.print("Enter product name to search: ");
                    String name = scanner.nextLine();
                    Node result = shoppingList.search(name);
                    System.out.println(result != null ? "Found: " + result.item : "Item not found.");
                }
                case 4 -> {
                    System.out.print("Enter product name to remove: ");
                    String name = scanner.nextLine();
                    Node node = shoppingList.search(name);
                    if (node != null) {
                        shoppingList.remove(node);
                        System.out.println("Item removed.");
                    } else {
                        System.out.println("Item not found.");
                    }
                }
                case 5 -> {
                    System.out.println("Exiting...");
                    System.exit(0);
                }
                default -> System.out.println("Invalid choice. Try again.");
            }
        }
    }
}
