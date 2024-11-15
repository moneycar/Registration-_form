import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.sql.*;

public class RegistrationForm {
    // connection variables
    private static final String DB_URL = "jdbc:mysql://localhost:3306/registration_db";
    private static final String DB_USER = "root";
    private static final String DB_PASSWORD = "password";

    public static void main(String[] args) {
        //  JFrame
        JFrame frame = new JFrame("Registration Form");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(800, 400);
        frame.setLayout(new BorderLayout());

        // Header
        JLabel header = new JLabel("Registration Form", JLabel.CENTER);
        header.setFont(new Font("Arial", Font.BOLD, 24));
        frame.add(header, BorderLayout.NORTH);

        // Left panel 
        JPanel formPanel = new JPanel();
        formPanel.setLayout(new GridLayout(7, 2, 10, 10));
        frame.add(formPanel, BorderLayout.CENTER);

        // Form 
        JLabel nameLabel = new JLabel("Name:");
        JTextField nameField = new JTextField();

        JLabel mobileLabel = new JLabel("Mobile:");
        JTextField mobileField = new JTextField();

        JLabel genderLabel = new JLabel("Gender:");
        JPanel genderPanel = new JPanel(new FlowLayout(FlowLayout.LEFT));
        JRadioButton maleButton = new JRadioButton("Male");
        JRadioButton femaleButton = new JRadioButton("Female");
        ButtonGroup genderGroup = new ButtonGroup();
        genderGroup.add(maleButton);
        genderGroup.add(femaleButton);
        genderPanel.add(maleButton);
        genderPanel.add(femaleButton);

        JLabel dobLabel = new JLabel("DOB:");
        JTextField dobField = new JTextField();

        JLabel addressLabel = new JLabel("Address:");
        JTextArea addressArea = new JTextArea(3, 20);
        JScrollPane addressScrollPane = new JScrollPane(addressArea);

        JLabel contactLabel = new JLabel("Contact:");
        JTextField contactField = new JTextField();

        // Buttons
        JButton registerButton = new JButton("Register");
        JButton exitButton = new JButton("Exit");

        formPanel.add(nameLabel);
        formPanel.add(nameField);
        formPanel.add(mobileLabel);
        formPanel.add(mobileField);
        formPanel.add(genderLabel);
        formPanel.add(genderPanel);
        formPanel.add(dobLabel);
        formPanel.add(dobField);
        formPanel.add(addressLabel);
        formPanel.add(addressScrollPane);
        formPanel.add(contactLabel);
        formPanel.add(contactField);
        formPanel.add(registerButton);
        formPanel.add(exitButton);

        // Right panel
        JPanel displayPanel = new JPanel();
        displayPanel.setLayout(new BorderLayout());
        JTextArea displayArea = new JTextArea();
        displayArea.setEditable(false);
        displayPanel.add(new JScrollPane(displayArea), BorderLayout.CENTER);
        frame.add(displayPanel, BorderLayout.EAST);

        // Action listeners
        registerButton.addActionListener(e -> {
            String name = nameField.getText();
            String mobile = mobileField.getText();
            String gender = maleButton.isSelected() ? "Male" : (femaleButton.isSelected() ? "Female" : "");
            String dob = dobField.getText();
            String address = addressArea.getText();
            String contact = contactField.getText();

            if (name.isEmpty() || mobile.isEmpty() || gender.isEmpty() || dob.isEmpty() || address.isEmpty() || contact.isEmpty()) {
                JOptionPane.showMessageDialog(frame, "Please fill all fields!", "Error", JOptionPane.ERROR_MESSAGE);
                return;
            }

            // Save to database
            try (Connection conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASSWORD);
                 PreparedStatement stmt = conn.prepareStatement(
                         "INSERT INTO users (name, mobile, gender, dob, address, contact) VALUES (?, ?, ?, ?, ?, ?)")) {
                stmt.setString(1, name);
                stmt.setString(2, mobile);
                stmt.setString(3, gender);
                stmt.setDate(4, java.sql.Date.valueOf(dob));
                stmt.setString(5, address);
                stmt.setString(6, contact);
                stmt.executeUpdate();

                JOptionPane.showMessageDialog(frame, "Registration Successful!");
                displayArea.append("Name: " + name + "\nMobile: " + mobile + "\nGender: " + gender + "\nDOB: " + dob +
                        "\nAddress: " + address + "\nContact: " + contact + "\n\n");
            } catch (Exception ex) {
                ex.printStackTrace();
                JOptionPane.showMessageDialog(frame, "Error: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        });

        exitButton.addActionListener(e -> frame.dispose());

        // Show  frame
        frame.setVisible(true);
    }
}
 CREATE DATABASE registration_db;
USE registration_db;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    mobile VARCHAR(15),
    gender VARCHAR(10),
    dob DATE,
    address TEXT,
    contact VARCHAR(100)
);
