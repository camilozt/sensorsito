#!/usr/bin/python

# Import required Python libraries
import uinput
import time               # library for time reading time
import RPi.GPIO as GPIO   # library to control Rpi GPIOs
pintgpio=12
events = (uinput.KEY_UP, uinput.KEY_DOWN, uinput.KEY_LEFT, uinput.KEY_RIGHT, uinput.KEY_LEFTCTRL)

device = uinput.Device(events)
right = False

while True:
def main():
  # We will be using the BCM GPIO numbering
  GPIO.setmode(GPIO.BOARD)

  # Select which GPIOs you will use
  GPIO_TRIGGER = 16
  GPIO_ECHO    = 18

  # Set TRIGGER to OUTPUT mode
  GPIO.setup(GPIO_TRIGGER,GPIO.OUT)
  # Set ECHO to INPUT mode
  GPIO.setup(GPIO_ECHO,GPIO.IN)

  # Set TRIGGER to LOW
  GPIO.output(GPIO_TRIGGER, False)

  # Let the sensor settle for a while
  time.sleep(0.5)

  # Send 10 microsecond pulse to TRIGGER
  GPIO.output(GPIO_TRIGGER, True) # set TRIGGER to HIGH
  time.sleep(0.00001) # wait 10 microseconds
  GPIO.output(GPIO_TRIGGER, False) # set TRIGGER back to LOW

  # Create variable start and give it current time
  start = time.time()
  # Refresh start value until the ECHO goes HIGH = until the wave is send
  while GPIO.input(GPIO_ECHO)==0:
    start = time.time()
  # Assign the actual time to stop variable until the ECHO goes back from HIGH to LOW
  while GPIO.input(GPIO_ECHO)==1:
    stop = time.time()

  # Calculate the time it took the wave to travel there and back
  measuredTime = stop - start
  # Calculate the travel distance by multiplying the measured time by speed of sound
  distanceBothWays = measuredTime * 33112 # cm/s in 20 degrees Celsius
  # Divide the distance by 2 to get the actual distance from sensor to obstacle
  distance = distanceBothWays / 2
  # Print the distance
  print("Distance : {0:5.1f}cm".format(distance))
  if (not right) and (distance<=35):  # Right button pressed
    right = True
    device.emit(uinput.KEY_L, 1) # Press Right key
  if right and distance>=35:  # Right button released
    right = False
    device.emit(uinput.KEY_L, 0) # Release Right key
  time.sleep(.04)
  # Reset GPIO settings
  GPIO.cleanup()

# Run the main function when the script is executed
if __name__ == "__main__":
    main()
