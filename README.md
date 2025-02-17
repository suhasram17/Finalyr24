# Finalyr24 Eye aspect ratio detection
import cv2
import serial
import time

# Open serial connection
arduino = serial.Serial(port='COM3', baudrate=9600, timeout=1)  # Replace COM3 with your port
time.sleep(2)

# Load Haar cascade for eye detection
eye_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + 'haarcascade_eye.xml')

def detect_closed_eyes(frame):
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    eyes = eye_cascade.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=10, minSize=(30, 30))
    return len(eyes) == 0  # Eyes closed if no eyes are detected

# Start webcam
cap = cv2.VideoCapture(0)

while cap.isOpened():
    ret, frame = cap.read()
    if not ret:
        break
    
    if detect_closed_eyes(frame):
        print("Eyes closed detected")
        arduino.write(b'E')  # Send 'E' to Arduino
       
    cv2.imshow("Eye Detection", frame)
    
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
