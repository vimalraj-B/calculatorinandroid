# EX.NO:5-Develop a program to create a simple calculator using android studio.
### AIM : 
To design and develop a Stylish Scientific Calculator Android Application using Android Studio with Java and XML. The application performs basic arithmetic operations and scientific calculations such as trigonometric functions, logarithms, square roots, powers, and factorials through a user-friendly graphical interface.
### Developed By : VIMALRAJ B
### Register Number : 212224230304 
### EQUIPMENTS REQUIRED : 
Android Studio
### PROCEDURE :
1. Open Android Studio and create a new project.
2. Select Empty Views Activity and choose Java as the programming language.
3. Set the application name as Scientific Calculator.
4. Open the activity_main.xml file and design the calculator interface using LinearLayout and GridLayout.
5. Customize the user interface with a dark background, rounded buttons, attractive colors, and a stylish display.
6. Create different button styles for numbers, scientific functions, operators, and the equal button.
7. Open MainActivity.java and initialize the TextView and Button components.
8. Implement the onButtonClick() method to handle user interactions.
9. Write Java methods to perform arithmetic and scientific calculations.
10. Implement an expression parser to evaluate mathematical expressions.
11. Connect all calculator buttons to the Java click-handling method.
12. Run the application using an Android emulator or a physical Android device.
13. Test the calculator with different mathematical expressions and verify the results.

### PROGRAM : 
### MainActivity.java
```java
package com.example.exp_05;

import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    TextView display;
    String expression = "";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        display = findViewById(R.id.display);
    }

    public void onButtonClick(View view) {

        Button button = (Button) view;
        String value = button.getText().toString();

        try {
            switch (value) {

                case "C":
                    expression = "";
                    break;

                case "DEL":
                    if (!expression.isEmpty()) {
                        expression = expression.substring(
                                0, expression.length() - 1);
                    }
                    break;

                case "=":
                    double result = calculate(expression);
                    expression = formatResult(result);
                    break;

                case "sin":
                case "cos":
                case "tan":
                case "log":
                case "ln":
                    expression += value + "(";
                    break;

                case "√":
                    expression += "sqrt(";
                    break;

                case "x²":
                    expression += "^2";
                    break;

                case "π":
                    expression += String.valueOf(Math.PI);
                    break;

                case "e":
                    expression += String.valueOf(Math.E);
                    break;

                case "x!":
                    expression += "!";
                    break;

                case "×":
                    expression += "*";
                    break;

                case "÷":
                    expression += "/";
                    break;

                case "−":
                    expression += "-";
                    break;

                default:
                    expression += value;
                    break;
            }

            display.setText(
                    expression.isEmpty() ? "0" : expression);

        } catch (Exception e) {
            Toast.makeText(this, "Invalid Expression",
                    Toast.LENGTH_SHORT).show();
        }
    }

    private String formatResult(double result) {

        if (Double.isNaN(result) || Double.isInfinite(result)) {
            return "Error";
        }

        if (result == (long) result) {
            return String.valueOf((long) result);
        }

        return String.valueOf(result);
    }

    private double calculate(String exp) {

        if (exp.isEmpty()) {
            return 0;
        }

        return new ExpressionParser(exp).parse();
    }

    // Expression Parser

    class ExpressionParser {

        String input;
        int pos = -1;
        int ch;

        ExpressionParser(String input) {
            this.input = input;
        }

        void nextChar() {
            ch = (++pos < input.length()) ?
                    input.charAt(pos) : -1;
        }

        boolean eat(int charToEat) {

            while (ch == ' ') nextChar();

            if (ch == charToEat) {
                nextChar();
                return true;
            }

            return false;
        }

        double parse() {

            nextChar();

            double x = parseExpression();

            if (pos < input.length()) {
                throw new RuntimeException("Invalid Expression");
            }

            return x;
        }

        double parseExpression() {

            double x = parseTerm();

            while (true) {

                if (eat('+')) {
                    x += parseTerm();
                }

                else if (eat('-')) {
                    x -= parseTerm();
                }

                else {
                    return x;
                }
            }
        }

        double parseTerm() {

            double x = parseFactor();

            while (true) {

                if (eat('*')) {
                    x *= parseFactor();
                }

                else if (eat('/')) {

                    double divisor = parseFactor();

                    if (divisor == 0) {
                        throw new ArithmeticException();
                    }

                    x /= divisor;
                }

                else {
                    return x;
                }
            }
        }

        double parseFactor() {

            if (eat('+')) return parseFactor();

            if (eat('-')) return -parseFactor();

            double x;
            int startPos = pos;

            if (eat('(')) {

                x = parseExpression();

                if (!eat(')')) {
                    throw new RuntimeException("Missing bracket");
                }

            }

            else if ((ch >= '0' && ch <= '9') || ch == '.') {

                while ((ch >= '0' && ch <= '9') || ch == '.') {
                    nextChar();
                }

                x = Double.parseDouble(
                        input.substring(startPos, pos));
            }

            else if (ch >= 'a' && ch <= 'z') {

                while (ch >= 'a' && ch <= 'z') {
                    nextChar();
                }

                String function = input.substring(startPos, pos);

                x = parseFactor();

                switch (function) {

                    case "sin":
                        x = Math.sin(Math.toRadians(x));
                        break;

                    case "cos":
                        x = Math.cos(Math.toRadians(x));
                        break;

                    case "tan":
                        x = Math.tan(Math.toRadians(x));
                        break;

                    case "sqrt":
                        x = Math.sqrt(x);
                        break;

                    case "log":
                        x = Math.log10(x);
                        break;

                    case "ln":
                        x = Math.log(x);
                        break;

                    default:
                        throw new RuntimeException("Unknown function");
                }
            }

            else {
                throw new RuntimeException("Invalid Input");
            }

            if (eat('^')) {
                x = Math.pow(x, parseFactor());
            }

            if (eat('!')) {

                if (x < 0 || x != Math.floor(x) || x > 170) {
                    throw new RuntimeException("Invalid factorial");
                }

                double fact = 1;

                for (int i = 1; i <= (int) x; i++) {
                    fact *= i;
                }

                x = fact;
            }

            return x;
        }
    }
}

```
### activity_main.xml
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="12dp"
    android:background="#0B0E17">

    <!-- Header -->

    <TextView
        android:layout_width="match_parent"
        android:layout_height="45dp"
        android:text="SCIENTIFIC CALCULATOR"
        android:textColor="#A78BFA"
        android:textSize="18sp"
        android:textStyle="bold"
        android:gravity="center"/>

    <!-- Display -->

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="2"
        android:orientation="vertical"
        android:gravity="bottom"
        android:padding="20dp"
        android:background="#171D2C">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="SCIENTIFIC MODE"
            android:textColor="#8B90A5"
            android:textSize="12sp"
            android:gravity="end"/>

        <TextView
            android:id="@+id/display"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="0"
            android:textColor="#FFFFFF"
            android:textSize="38sp"
            android:gravity="end"
            android:paddingTop="20dp"
            android:maxLines="2"
            android:ellipsize="start"/>

    </LinearLayout>

    <!-- Calculator Buttons -->

    <GridLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="5"
        android:columnCount="4"
        android:rowCount="8"
        android:layout_marginTop="12dp">

        <!-- Scientific Functions -->

        <Button
            android:text="sin"
            style="@style/ScientificButton"/>

        <Button
            android:text="cos"
            style="@style/ScientificButton"/>

        <Button
            android:text="tan"
            style="@style/ScientificButton"/>

        <Button
            android:text="DEL"
            style="@style/OperatorButton"/>

        <Button
            android:text="log"
            style="@style/ScientificButton"/>

        <Button
            android:text="ln"
            style="@style/ScientificButton"/>

        <Button
            android:text="√"
            style="@style/ScientificButton"/>

        <Button
            android:text="x²"
            style="@style/ScientificButton"/>

        <Button
            android:text="("
            style="@style/ScientificButton"/>

        <Button
            android:text=")"
            style="@style/ScientificButton"/>

        <Button
            android:text="^"
            style="@style/ScientificButton"/>

        <Button
            android:text="÷"
            style="@style/OperatorButton"/>

        <!-- Number Buttons -->

        <Button
            android:text="7"
            style="@style/NumberButton"/>

        <Button
            android:text="8"
            style="@style/NumberButton"/>

        <Button
            android:text="9"
            style="@style/NumberButton"/>

        <Button
            android:text="×"
            style="@style/OperatorButton"/>

        <Button
            android:text="4"
            style="@style/NumberButton"/>

        <Button
            android:text="5"
            style="@style/NumberButton"/>

        <Button
            android:text="6"
            style="@style/NumberButton"/>

        <Button
            android:text="−"
            style="@style/OperatorButton"/>

        <Button
            android:text="1"
            style="@style/NumberButton"/>

        <Button
            android:text="2"
            style="@style/NumberButton"/>

        <Button
            android:text="3"
            style="@style/NumberButton"/>

        <Button
            android:text="+"
            style="@style/OperatorButton"/>

        <Button
            android:text="0"
            style="@style/NumberButton"/>

        <Button
            android:text="."
            style="@style/NumberButton"/>

        <Button
            android:text="π"
            style="@style/ScientificButton"/>

        <Button
            android:text="="
            style="@style/EqualButton"/>

        <!-- Bottom Buttons -->

        <Button
            android:text="e"
            style="@style/ScientificButton"/>

        <Button
            android:text="x!"
            style="@style/ScientificButton"/>

        <Button
            android:text="C"
            style="@style/ClearButton"/>

    </GridLayout>

</LinearLayout>

```
### strings.xml
```xml
<resources>
    <string name="app_name">exp_05</string>

    <style name="BaseCalcButton">
        <item name="android:layout_width">0dp</item>
        <item name="android:layout_height">0dp</item>
        <item name="android:layout_columnWeight">1</item>
        <item name="android:layout_rowWeight">1</item>
        <item name="android:layout_margin">4dp</item>
        <item name="android:textSize">17sp</item>
        <item name="android:textStyle">bold</item>
        <item name="android:textAllCaps">false</item>
        <item name="android:insetTop">0dp</item>
        <item name="android:insetBottom">0dp</item>
        <item name="android:insetLeft">0dp</item>
        <item name="android:insetRight">0dp</item>
        <item name="android:onClick">onButtonClick</item>
    </style>

    <style name="NumberButton" parent="BaseCalcButton">
        <item name="android:backgroundTint">#202638</item>
        <item name="android:textColor">#FFFFFF</item>
    </style>

    <style name="ScientificButton" parent="BaseCalcButton">
        <item name="android:backgroundTint">#30264F</item>
        <item name="android:textColor">#C4B5FD</item>
        <item name="android:textSize">15sp</item>
    </style>

    <style name="OperatorButton" parent="BaseCalcButton">
        <item name="android:backgroundTint">#49301F</item>
        <item name="android:textColor">#FFAD72</item>
    </style>

    <style name="EqualButton" parent="BaseCalcButton">
        <item name="android:backgroundTint">#FF8C42</item>
        <item name="android:textColor">#FFFFFF</item>
        <item name="android:textSize">23sp</item>
    </style>

    <style name="ClearButton" parent="BaseCalcButton">
        <item name="android:backgroundTint">#512B3A</item>
        <item name="android:textColor">#FF6B81</item>
    </style>

</resources>
```
### OUTPUT : 



<img width="1780" height="1020" alt="Screenshot 2026-11-27 092604" src="https://github.com/user-attachments/assets/0371e941-d344-4b26-8e5c-f82ede54059e" />

<img width="1775" height="1018" alt="Screenshot 2026-11-27 092638" src="https://github.com/user-attachments/assets/3d49fbde-3040-4b01-afe7-c7abd32c1518" />

<img width="433" height="892" alt="Screenshot 2026-11-27 093640" src="https://github.com/user-attachments/assets/76791897-f841-4598-b5bf-b79e11f7af7e" />

<img width="437" height="887" alt="Screenshot 2026-11-27 093717" src="https://github.com/user-attachments/assets/b828963b-b176-4b58-9725-1bd4090d5569" />


### RESULT :
Thus, the Stylish Scientific Calculator Android Application was successfully designed and developed using Java and XML in Android Studio. The application performs basic arithmetic and scientific operations with an attractive user interface and provides accurate results for valid mathematical expressions.
