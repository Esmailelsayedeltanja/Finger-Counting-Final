# Finger-Counting-Final
Finger-Counting Final
This Python code uses the MediaPipe library to detect the number of fingers shown by a hand in a live video stream from a webcam. It displays the number of fingers on the screen in real-time.

Here's a brief description of each part of the code:

```
import cv2
import time
import mediapipe as mp
```
This imports the necessary libraries for the program to work. `cv2` is used to capture and display video, `time` is used to calculate the frames per second (FPS), and `mediapipe` is used to detect the hand landmarks.

```
mp_drawing = mp.solutions.drawing_utils
mp_drawing_styles = mp.solutions.drawing_styles
mp_hands = mp.solutions.hands
```
These are classes from the `mediapipe` library used to draw the landmarks on the image and detect the hand.

```
pTime = 0
confirmation = []
cap = cv2.VideoCapture(0)

with mp_hands.Hands(
    model_complexity=0,
    min_detection_confidence=0.5,
    min_tracking_confidence=0.5) as hands:
```
This initializes the webcam, sets up the `mp_hands.Hands` object to detect hand landmarks, and sets the confidence thresholds for detection and tracking.

```
while cap.isOpened():
    success, image = cap.read()
    if not success:
        print("Ignoring empty camera frame.")
       This is a check to ensure that the video frame is not empty. If the frame is empty, the program continues to the next frame.

```
if cv2.waitKey(1) & 0xFF == ord('q'):
        break
            
    image = cv2.flip(image,1)
```
This checks if the user pressed the 'q' key to exit the program. If the key is pressed, the program exits. The image is flipped horizontally to make the video appear as a mirror image.

```
"---------- FPS : ----------"
    cTime = time.time()
    fps = int(1/(cTime-pTime))
    pTime = cTime # update

    "------- Show FPS : --------"
    cv2.putText(image,f"{fps} FPS",(10,30),cv2.FONT_HERSHEY_COMPLEX,0.8,(0,244,0),2,16)
```
This calculates and displays the frames per second (FPS) of the video stream.

```
image.flags.writeable = False
    image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
    results = hands.process(image)

    # Draw the hand annotations on the image.
    image.flags.writeable = True
    image = cv2.cvtColor(image, cv2.COLOR_RGB2BGR)
```
This converts the color space of the image from BGR to RGB, processes the image to detect the hand landmarks, and converts the image back to BGR.

```
```
    # Initially set finger count to 0 for each cap
    fingerCount = 0

    if results.multi_hand_landmarks:

        for hand_landmarks in results.multi_hand_landmarks:
            # Get hand index to check label (left or right)
            handIndex = results.multi_hand_landmarks.index(hand_landmarks)
            handLabel = results.multi_handedness[handIndex].classification[0].label

            # Set variable to keep landmarks positions (x and y)
            handLandmarks = []

            # Fill list with x and y positions of each landmark
            for landmarks in hand_landmarks.landmark:
                handLandmarks.append([landmarks.x, landmarks.y])

            # Test conditions for each finger
            if handLabel == "Left" and handLandmarks[4][0] > handLandmarks[3][0]:
                fingerCount = fingerCount+1
            elif handLabel == "Right" and handLandmarks[4][0] < handLandmarks[3][0]:
                fingerCount = fingerCount+1

            # Other fingers: TIP y position must be lower than PIP y position, 
            #   as image origin is in the upper left corner.
            if handLandmarks[8][1] < handLandmarks[6][1]:       #Index finger
                fingerCount = fingerCount+1
            if handLandmarks[12][1] < handLandmarks[continued]

[10][1]:     #Middle finger
                fingerCount = fingerCount+1
            if handLandmarks[16][1] < handLandmarks[14][1]:     #Ring finger
                fingerCount = fingerCount+1
            if handLandmarks[20][1] < handLandmarks[18][1]:     #Pinky
                fingerCount = fingerCount+1

            # Draw hand landmarks             
            mp_drawing.draw_landmarks(
                image,
                hand_landmarks,
                mp_hands.HAND_CONNECTIONS,
                mp_drawing_styles.DrawingSpec(color=(237,149,100),thickness=-1,circle_radius=6))

            
            
        ########### CONFIRMATION OF COUNTER###########
        confirmation.append(fingerCount) 

#             print(confirmation)

        if len(confirmation) == 5:

            if confirmation[0] == confirmation[1] and confirmation[1] == confirmation[2] and confirmation[2] == confirmation[3] and confirmation[3] == confirmation[4]:
                fingerCount = confirmation[0]
                print(f"I GOT IT , You Mean number ( {fingerCount} ) !")

            confirmation = []
        ##############################################
```
This section counts the number of fingers shown by the hand by checking the positions of the hand landmarks. It also draws the hand landmarks on the image. The finger count is displayed on the screen.

```
cv2.imshow('MediaPipe Hands', image)
```
This displays the processed image on the screen.

Overall, this program uses the MediaPipe library to detect the number of fingers shown by a hand in real-time using a webcam. It also displays the FPS of the video stream and draws the hand landmarks on the image. The program checks the position of the landmarks to count the number of fingers shown and displays the count on the screen.
