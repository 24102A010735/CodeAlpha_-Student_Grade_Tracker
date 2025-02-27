import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.ArrayList;

class Student {
    String name;
    double grade;

    Student(String name, double grade) {
        this.name = name;
        this.grade = grade;
    }
}

public class StudentGradeTrackerGUI {
    private ArrayList<Student> students = new ArrayList<>();
    private DefaultTableModel tableModel;
    private JTable table;

    public StudentGradeTrackerGUI() {

        JFrame frame = new JFrame("Student Grade Tracker");
        frame.setSize(500, 400);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setLayout(new BorderLayout());

   
        tableModel = new DefaultTableModel(new Object[]{"Name", "Grade"}, 0);
        table = new JTable(tableModel);
        JScrollPane scrollPane = new JScrollPane(table);

        JPanel bottomPanel = new JPanel(new FlowLayout());

        JButton addButton = new JButton("Add Student");
        JButton calculateButton = new JButton("Calculate Stats");

        bottomPanel.add(addButton);
        bottomPanel.add(calculateButton);


        frame.add(scrollPane, BorderLayout.CENTER);
        frame.add(bottomPanel, BorderLayout.SOUTH);


        addButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                openAddStudentDialog();
            }
        });

        calculateButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                showStatisticsWindow();
            }
        });

        frame.setVisible(true);
    }

    private void openAddStudentDialog() {
        JDialog addStudentDialog = new JDialog();
        addStudentDialog.setTitle("Add Student");
        addStudentDialog.setSize(300, 200);
        addStudentDialog.setLayout(new BorderLayout());

        JPanel inputPanel = new JPanel(new GridLayout(2, 2, 5, 5));
        JTextField nameField = new JTextField();
        JTextField gradeField = new JTextField();

        inputPanel.add(new JLabel("Enter Name:"));
        inputPanel.add(nameField);
        inputPanel.add(new JLabel("Enter Grade:"));
        inputPanel.add(gradeField);

        JButton saveButton = new JButton("Save");

       
        JPanel buttonPanel = new JPanel();
        buttonPanel.add(saveButton);

        addStudentDialog.add(inputPanel, BorderLayout.CENTER);
        addStudentDialog.add(buttonPanel, BorderLayout.SOUTH);

        saveButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String name = nameField.getText();
                String gradeText = gradeField.getText();

                try {
                    if (name.isEmpty() || gradeText.isEmpty()) {
                        throw new IllegalArgumentException("Fields cannot be empty!");
                    }

                    double grade = Double.parseDouble(gradeText);

                    if (grade < 0 || grade > 100) {
                        throw new IllegalArgumentException("Grade must be between 0 and 100!");
                    }

                    students.add(new Student(name, grade));
                    tableModel.addRow(new Object[]{name, grade});
                    addStudentDialog.dispose();
                } catch (NumberFormatException ex) {
                    JOptionPane.showMessageDialog(addStudentDialog, "Enter a valid numeric grade!", "Error", JOptionPane.ERROR_MESSAGE);
                } catch (IllegalArgumentException ex) {
                    JOptionPane.showMessageDialog(addStudentDialog, ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
                }
            }
        });

        addStudentDialog.setLocationRelativeTo(null);
        addStudentDialog.setVisible(true);
    }

    private void showStatisticsWindow() {
        if (students.isEmpty()) {
            JOptionPane.showMessageDialog(null, "No data available to calculate statistics!", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }

        double sum = 0, highest = Double.MIN_VALUE, lowest = Double.MAX_VALUE;
        String highestStudent = "", lowestStudent = "";

        for (Student student : students) {
            sum += student.grade;
            if (student.grade > highest) {
                highest = student.grade;
                highestStudent = student.name;
            }
            if (student.grade < lowest) {
                lowest = student.grade;
                lowestStudent = student.name;
            }
        }

        double average = sum / students.size();

        
        JDialog statsWindow = new JDialog();
        statsWindow.setTitle("Statistics");
        statsWindow.setSize(300, 200);
        statsWindow.setLayout(new GridLayout(4, 1));

        JLabel avgLabel = new JLabel("Average Score: " + String.format("%.2f", average), SwingConstants.CENTER);
        JLabel highestLabel = new JLabel("Highest: " + highestStudent + " (" + String.format("%.2f", highest) + ")", SwingConstants.CENTER);
        JLabel lowestLabel = new JLabel("Lowest: " + lowestStudent + " (" + String.format("%.2f", lowest) + ")", SwingConstants.CENTER);

        JButton closeButton = new JButton("Close");
        closeButton.addActionListener(e -> statsWindow.dispose());

        statsWindow.add(avgLabel);
        statsWindow.add(highestLabel);
        statsWindow.add(lowestLabel);
        statsWindow.add(closeButton);

        statsWindow.setLocationRelativeTo(null);
        statsWindow.setVisible(true);
    }

    public static void main(String[] args) {
        new StudentGradeTrackerGUI();
    }
}

        addStudentDialog.setLocationRelativeTo(null
