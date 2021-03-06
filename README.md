# self-driving-ride-on-car
Making a kids ride on car semi autonomous.

# Motivation
I've been taking the [Self-Driving Cars Specialization](https://www.coursera.org/specializations/self-driving-cars) on Coursera and wanted to do a project on that. Fortunately, my kids got a ride-on car from the grandparents for their birthday. Needless to say, I was more excited about it than my kids :)

Since my kids are only 2 and 3, they cannot drive safely on their own, especially on the sidewalks where there are fences, and other obstacles. Fortunately, it comes with a remote that lets parents control it. But the remote has challenges of its own. You need two hands to drive the car, one for longitudinal motion and another for lateral. The front wheels don't auto center either after turning, so driving in a straight line is a challenge because you are pushing the left or right button to center it, and you end up driving in a zigzag. Then there is the problem of spurious left/right motion due to uneven driving surface even on paved sidewalks. So you are constantly monitoring the car, and minor distractions (like checking to see if my shoelace got undone) has led to driving on neighbors lawn or small brushes with the fence. It feels like flying an airplane without wing/nose levelers

Goals for the project are:
#### Sidewalk driving
* Drive in a straight line
* Follow the curves and turn 
* Stop or reverse back into sidewalk if the car is on the street by any chance
* Avoid crashing into fence, fire-hydrants
* Avoid driving over the occasional dog poop
* Wait for people/bike/pets to pass in a safe area

#### Verbal warnings
* Provide verbal warnings when approaching obstacles or driving on the lawn. So kids learn its not okay and learn to maneuver out of those situations

#### Follow me mode
Sometimes, one or both kids want to get out and walk or be carried, so a hands-free follow me mode is required.

#### Go home
* This would be awesome to have because it requires crossing the street. Not sure if the sensors I've would be enough to detect ongoing traffic from all directions at a residential neighbor intersection

# Hardware
* [12V 2-Seater Licensed Land Rover Ride-On w/ Parent Remote Control](https://bestchoiceproducts.com/products/12v-2-seater-licensed-land-rover-ride-on-w-parent-remote-control)
* [Intel RealSense D435i depth camera](https://www.intelrealsense.com/depth-camera-d435i/)
* [NVIDIA Jetson Nano](https://developer.nvidia.com/embedded/jetson-nano-developer-kit)

# Making the computer drive
The first step is to get a computer to control the car. Fortunately, the car had a "drive-by-wire" system for parent's remote control. So I didn't need to fiddle with the mechanical parts. The three options I found were

1) Use relays to control the three motors. (1 for steering and 2 for longitudinal motion). But due to my fear of electrical fires and my daughters' safety, I didn't pursue this option

2) The next option was to record the radio signals emitted by the remote and replay them using Software Defined Radio. Even though I would've loved to do some SDR, the 2.4 GHz SDR receiver/transmitter cost was a bit too expensive for me.

3) I was able to find a replacement remote for the car for about $15 on Amazon and decided to control it using raspberry pi.

## Interfacing the remote with a Raspberry Pi
My initial plan of just soldering some wires on the remote control board didn't work out as it was using capacitance sensors. But there was a 16 pin Integrated Circut which didn't have any markings. After some google search it looked to be something like [this](http://www.farnell.com/datasheets/2140385.pdf?_ga=2.146026876.959354315.1593116380-481204244.1593116380&_gac=1.217729636.1593116380.CjwKCAjwltH3BRB6EiwAhj0IUFVMlb6TjKmd8uZE4ZQnzn8N3dOhjkd53IEtFgnTHEZf2MvdYNDB6RoCUGsQAvD_BwE), so I set out to find the ground by measuring the voltage across each pin and discovered it to be pin 2. I noticed about seven 4.7 kOhm resistors, so I set out connecting each pin on the remote to the ground using the 4.7 kOhm resistor and eventually find all six pins needed to control the car.
The six pins are for the following functions Forward, Reverse, Left, Right, Speed, Lock (emergency break/cut off)
![Capacitive touch sensor controller pinout diagram](capacitive_touch_sensor_controller_diagram.png)

### Wiring remote to Raspberry Pi
I built six circuits on [Adafruit Perma-Proto boards](https://www.adafruit.com/product/571?gclid=CjwKCAjwltH3BRB6EiwAhj0IULoPkd2ZcyuDW_MTVG1tCEflQ7JtR55zu2QEbKyM4xGwm5b_yYa4yhoCq0IQAvD_BwE) following [Output circuits using NPN transistor](https://elinux.org/RPi_GPIO_Interface_Circuits#Using_an_NPN_transistor). I used [PN2222 transistors](https://www.adafruit.com/product/756) from adafruit and 1K resistors I had lying around at home.
![Raspberry Pi + remote + circuit for interfacing GPIO with touch controller](rpi_touch_controller_transistor_circuit.JPG)
> I could have made the circuits smaller, but I prefer to have everything modular so testing is easier and I can swap out components when needed. Debugging circuts and solder issues are not fun and I had a few shorts that took a while to identify

# Telemetry from the car
The next step is to get feedback from the car for steering position and driving mode.


## Steering position sensing
I've seen almost all self-driving cars use radians to measure steering angle, but a single-axis linear coordinate system is better for this car due to the steering construction. I plan on assigning -10 to the left, 0 to center, and +10 for right, as shown below. 

![Steering coordinates](steering-single-axis-diagram.png)

Some options are
1. Use a potentiometer, most likely linear to measure resistance and assign a value on the X-axis for 
2. Use hall effect sensors with magnets mounted on the steering rod
3. Use ToF sensors and measure the distance 

Details Comming Soon

## Velocity and longitudinal motion sensing
To get longitudinal motion, I think the best bet is to use hall effect sensors and magnets on the wheel to identify speed and direction

Details Comming Soon


# Software
Details Comming Soon





