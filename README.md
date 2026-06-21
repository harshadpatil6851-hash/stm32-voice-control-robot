# stm32-voice-control-robot
Voice-controlled robot built with STM32 Blue Pill and HC-05 Bluetooth module.

📖 Table of Contents
Overview
Hardware Components
Circuit Diagram
How It Works
Challenges & Solutions


Overview
    This project is a voice-controlled robot built using the STM32 Blue Pill microcontroller. 
    The robot receives voice commands from a smartphone via an HC-05 Bluetooth module and performs movements such as forward, backward, left, and right turns. 
    The project demonstrates the integration of wireless communication, motor control, and embedded system programming.


Hardware Components
    Component                                  Quantity                              Purpose
    STM32 Blue Pill (STM32F103C8T6)	              1	                            Main microcontroller
    HC-05 Bluetooth Module	                      1	                            Wireless voice command receiver
    L298N Motor Driver	                          1	                            Drives the DC motors
    DC Motors (with wheels)	                      2	                            Provide movement to the robot
    Battery Pack (7.4V LiPo or 9V)	              1	                            Power source for motors
    Jumper Wires	                             Several	                        Circuit connections
    Chassis	                                      1	                            Robot body structure


Circuit Diagram
  Pin Connections
    STM32 Blue Pill Pin	                         Connected To                            Description
    PB0	                                           L298N IN1	                        Left motor direction
    PB1	                                           L298N IN2	                        Left motor direction
    PB10	                                         L298N IN3	                        Right motor direction
    PB11	                                         L298N IN4	                        Right motor direction
    PA9 (TX)	                                     HC-05 RX	                          Bluetooth transmit
    PA10 (RX)	                                     HC-05 TX	                          Bluetooth receive
    3.3V	                                         HC-05 VCC	                        Bluetooth power
    GND	                                           HC-05 GND	                        Common ground

How It Works
    Command	                                      Action
      'F'	                                    Move Forward for 2 seconds
      'B'	                                    Move Backward for 2 seconds
      'L'	                                    Turn Left for 250 ms
      'R'	                                    Turn Right for 250 ms
     Others	                                  Stop all motors


Voice-to-Command Flow

1. User speaks a command (e.g., "Forward") into a smartphone app.
2. The speech recognition app (e.g., "Arduino Bluetooth Controller" or "AMR Voice") converts the speech into a character ('F', 'B', 'L', or 'R').
3. The app sends the character via Bluetooth to the HC-05 module.
4. The STM32 Blue Pill receives the character on Serial1 (UART).
5. The microcontroller executes the corresponding motor control function.
6. The robot moves for a fixed duration and then stops.


Code Logic Overview

1. Initialization: Sets motor pins as outputs, initializes UART communication for both USB debugging (Serial) and Bluetooth (Serial1), and ensures motors are stopped initially.
2. Main Loop: Continuously checks Serial1 for incoming Bluetooth data.
3. Command Handling: When data arrives, it reads the character and uses a switch statement to call the appropriate movement function.
4. Motor Control: Functions (forward(), backward(), etc.) set the four motor driver pins (IN1–IN4) to specific HIGH/LOW combinations to achieve the desired motion.
5. Timed Movement: After each movement, a delay() keeps the robot moving for a set time, then stopMotors() is called.


Challenges & Solutions

1.Bluetooth Interference and Noise
Problem: Bluetooth modules sometimes send random or corrupted characters during pairing or when the signal is weak. This can cause unexpected movements.

Solution:
1. Validate incoming data: Only process commands that are exactly 'F', 'B', 'L', or 'R'. Use the default case in the switch statement to ignore or stop on invalid data.
2. Software debouncing: Add a small delay after reading to filter out noise.
3. CRC or checksum: For more robust systems, send commands with a checksum or a start/end marker (e.g., <F> and >).

2. UART Conflicts with USB
Problem: The STM32 Blue Pill shares Serial with the USB port. If you try to use Serial for Bluetooth and USB simultaneously, they can interfere.

Solution:
Use Serial (USART1 on PA9/PA10) exclusively for USB debugging.
Use Serial1 (USART2 or USART3) exclusively for Bluetooth.
This ensures both communication channels work independently without conflicts.
