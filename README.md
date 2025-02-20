java cDigital Systems Laboratory 4/MSc
ELEE10023/PGEE11117 
School of Engineering, University of Edinburgh 
Course Description
Aim: The course aims to produce students who are capable of developing hardware-software 
digital systems from high level functional specifications and prototyping them on to FPGA 
hardware using a standard hardware description language and software programming language. 
Pre-requisites: Digital Systems Laboratory 3 (ELEE09018) or Digital Systems Laboratory A 
(PGEE10017) or equivalent in other schools and outside institutions (see below). Engineering 
Software 3 or equivalent is advisable but not necessary. 
Co-requisites: Undergraduate students must take Digital System Design 4 (ELEE10007) 
Prohibited Combinations: None 
Visiting Students Pre-requisites: Digital design using Verilog, and embedded system 
programming. 
Keywords: Embedded Digital System Design, Embedded Processor Programming, Verilog, 
Data path and Control Path design, Hardware-Software Co-design 
Default Course Mode of Study: Lab only, 10 weekly 3-hr lab sessions
Default Delivery Period: Semester 2, starting in Week 2. 
Learning Outcomes: 
1. Knowledge and understanding of: 
I. Data paths and Control paths and number of ways of designing them; 
II. Instruction-set based control path design; 
III. Control and data path integration; 
IV. Capture the design of hardware-software digital systems in a standard hardware 
description language; 
2. Intellectual 
I. Ability to use and choose between different techniques for digital system design 
and capture; 
II. Ability to evaluate implementation results (e.g. speed, area, power) and correlate 
them with the corresponding high level design and capture; 
3. Practical 
I. Ability to use a commercial digital system development tool suite to develop 
hardware-software digital systems and prototype them on to FPGA hardware; 
2
Lab Content, What You Are Required To Do
You are required to develop a microprocessor-based system on FPGA with a demo application, 
written in software running on the microprocessor, which controls toy race-cars remotely. 
Students will be split into groups of two students each to tackle this problem. The final system 
will allow a user to simulate controlling cars remotely using a mouse, a VGA screen, and the 
BASYS 3 FPGA board as illustrated in the figure below. 
Figure 1. Proposed System for FPGA-Based Remote Car Control
A user will be able to hover a mouse pointer over a VGA screen with the position of the mouse 
pointer on screen commanding the movement of the remote car as illustrated in the following 
figure. 
3
Figure 2. Car movement (command) depending on mouse pointer position
The FPGA-based system will consist of a simple microprocessor, a VGA interface, and a mouse 
interface, in addition to other peripherals which will be detailed later on this document. An 
Infra-Red (IR) Transmitter interface can be added (As A Bonus) to control a toy car. Each team 
member will first develop one of the following peripherals, individually: VGA interface, or
mouse interface. The whole group will then collaborate to develop the microprocessor at the 
heart of the proposed system with all necessary peripherals, as well as the complete demo 
application. 
Assessment: The lab will be assessed during lab sessions through a number of checkpoints. The 
overall lab mark will be split into individual (60%) and group components (40%) for both 
undergraduate and postgraduate students. All students are encouraged to keep a lab book. 
Verification: To support remote working, a verification environment will be provided for each 
interface. Details will be provided on Learn. 
Assessment: The individual component will consist of two checkpoints: 
1. First individual assessment in Week 5, when every team member will be assessed on the 
particular peripheral interface they developed i.e. mouse driver or VGA interface. Code 
should be uploaded to Learn prior to the timetabled laboratory in Week 5 for assessment. 
This will account for 25% of the overall lab mark. 
2. Second individual assessment in Week 8, when every team member will demonstrate a 
working microprocessor + peripheral demo software application. For instance, the team 
member in charge of mouse driver development will present a demo software 
application running on the microprocessor with the mouse peripheral. Similarly, the 
team member in charge of VGA interface development will present a demo software 
application running on the microprocessor with the VGA interface peripheral. The 
specification of the individual demo software application will be specified later on this 
4
document. This second individual assessment will account for 35% of the overall lab 
mark. Note that while this assessment is individual, it requires prior design of the same 
microprocessor architecture by all team members. Code should be uploaded to Learn 
prior to the timetabled laboratory in Week 8 for assessment. 
3. The final assessment will be a group assessment in Week 11, where the entire team will 
demonstrate the complete demo software application running on the complete 
microprocessor-based system on the BASYS 3 board. There will be an element of peer 
assessment in this final group based assignment where individuals will be asked to 
evaluate the individual performance of colleagues within the group. More details of this 
process will be given nearer the time of assessment. Again all design files should be 
uploaded to Learn prior to the start of the scheduled laboratory. 
Details of the university semester structure can be found on the university web pages. Note that 
the week between weeks 5 and 6 is usually an unnumbered week, Flexible Learning Week. 
The remainder of this document will present the detailed specification of each component of 
the proposed system. The details of the assessment components will also be presented where 
appropriate.
5
1. PS/2 Mouse Driver 
The USB connector on the BASYS 3 board can accommodate a USB mouse. Internally, the 
signals are converted to PS/2-like signals via a microcontroller as discussed on page 7 and 8 of 
the BASYS 3 reference manual. Hence, all we need do is implement a driver for a PS/2 mouse 
as the USB connector could be seen as just a wrapper which has already been implemented. 
The PIC24 drives several signals into the FPGA – two are used to implement a standard PS/2 
interface for communication with a mouse or keyboard (see figure 3 below). 
Figure 3: USB Host signals to PS/2 signal conversion 
PS/2 devices use a two-wire serial bus (clock and data) to communicate with a host device 
Communication is bidirectional and performed in packets of 11-bit words, with each word 
containing a start, stop and odd parity bit. The following describes the PS/2 mouse protocol in 
detail (NB. the PS/2 keyboard protocol can be found in the BASYS 3 user manual if you are 
interested in developing a keyboard interface but this is not required in this lab). 
The mouse device only outputs a clock and data signal when it is moved. Otherwise, the clock 
and data lines remain at logic high (i.e. ‘1’). Open-Collector drivers are usually used to drive 
the two-wire bus between the mouse and host. 
The device can send data to the host only when both data and clock lines are high. Because the 
host is the bus master, the device must check whether the host is sending data before driving 
the bus. The clock line is used for this purpose as a “clear to send” signal; if the host pulls the 
clock line low, the device must not send any data until the clock is released. 
Communication is performed in 11-bit words, where each word consists of a ‘0’ start bit, 
followed by 8 bits of data (LSB first), followed by an odd parity bit (i.e. a bit that is set to ‘1’ if 
the number of 1’s in the 8 bits of data is even, and ‘0’ otherwise), and terminated with a ‘1’ stop 
bit. The odd-parity bit is used for error detection. 
Data sent from a PS/2 device to a host is read on the falling edge of the clock signal, whereas 
data sent from a host to a PS/2 device is read on the rising edge of the clock signal. The 
following figure shows PS/2 signal timing for a device to host communication. Note the timing 
requirements which must be strictly adhered to. The clock frequency, for instance, must lie 
between 10 and 16.7 KHz. 
 
6
Figure 3. PS/2 Device to Host Signal Timing
The following figure shows PS/2 signal timing for a host to device communication. The host 
brings the clock line low first, for at least 100µs. It then brings the data line low and releases 
the clock line. The host then waits for the PS/2 device to bring the clock line low. After that, it 
sets or resets the data line with the first data bit, and waits for the device to bring clock line 
high. It then waits for the device to bring the clock line low before it sets/rests the data line 
with the second data bit. This process is repeated until all eight data bits are sent as well as the 
odd-parity bit. Next, the host releases the data line, and waits for the device to bring the data 
line low, and then the clock line low. Finally, the host waits for the device to release data and 
clock lines. 
Figure 4. Host to PS/2 Device Signal Timing
Now that we have seen the low level PS/2 protocol, let us look at the high level host-mouse 
communication. At power-up, a typical host-mouse communication consists of the following 
steps: 
1) The host sends a Reset Command (consisting of byte “FF”) to the mouse, 
2) The mouse responds with an Acknowledgement byte “FA”, 
3) The mouse then goes through a self-test process and sends “AA” when this is passed. Then 
a mouse ID byte “00” is sent to the host, after which the host knows that the mouse is 
functioning well and ready to transmit data, 
4) The host sends byte “F4” to instruct the mouse to “Start Transmitting” its position 
information, 
5) The mouse acknowledges the “Start Transmitting” command by sending byte “FA” back 
to the host**, 
6) After this, the mouse starts transmitting its position information in the form of 3 bytes at a 
sample rate that can be set by the host (the default is 100Hz) 
7
**Note however that in the Basys3 FPGA board, probably due to the USB to PS/2 
conversion, F4 instead of FA is returned, and parity test fails. Hence in state 8 of 
MasterStateSM module, the acknowledgement code have been changed to F4, and parity 
check skipped. 
Thus, each data transmission from the mouse to the host after initialisation consists of 33 bits, 
where bits 1 (first bit), 12, and 23 are ‘0’ start bits; bits 10, 21, and 32 are Odd-Parity bits; and 
bits 11, 22, and 33 are ‘1’ stop bits. The three-byte data fields contain status and movement data 
as shown in the figure below. 
Byte 1: Status Byte 
Byte 2: X Direction Byte 
Byte 3: Y Direction Byte 
Figure 5. Mouse Data Format
The mouse reports a relative coordinate system whereby a move to the right generates a positive 
number in the X Direction Byte field, and a move to the left generates a negative number in this 
field. Similarly, a move upwards generates a positive number in the Y Direction Byte field, and 
a move downwards generates a negative number. Note that the X and Y Direction Bytes 
represent the magnitude of the rate of mouse movement, the larger the number the faster the 
mouse is moving. Bits XS and YS in the Status Byte are the sign bits, whereby a ‘1’ indicates a 
negative number, whereas XV and YV bits are movement overflow indicators, whereby a ‘1’ 
means overflow has occurred. The L and R fields in the Status Byte indicate that the left and 
right button have been pressed, respectively (‘1’ indicates the button has been pressed). 
What you are required to do
You are required to design an FPGA PS/2 mouse interface and implement it on the BASYS 3 
board. The clock line is physically connected to pin “C17” of the Artix7 FPGA chip, and the 
data line is physically connected to pin “B17” of the chip. 
Note that in connecting these pins in the XDC file, pull up need to be set true by using the 
additional comment of the form: set_property PULLUP true [get_ports PS2_CLK] after the PS/2 
clock and PS/2 Data ports. 
The FPGA mouse interface can be built from three modules: a “Transmitter” module, a 
“Receiver” module and a “State Machine” module to control the FPGA-Mouse communication, 
as shown in Figure 6. 
L R 0 1 XS
YS XV YV
X1 X2 X3 X4 X5 X6 X7 X8
Y1 Y2 Y3 Y4 Y5 Y6 Y7 Y8
8
Figure 6. Mouse Interface: Simplified Block Diagram
The following shows Verilog code fragments for the “Receiver” module: 
module MouseReceiver( 
 //Standard Inputs 
input RESET, 
input CLK, 
//Mouse IO - CLK 
input CLK_MOUSE_IN, 
//Mouse IO - DATA 
input DATA_MOUSE_IN, 
//Control 
input READ_ENABLE, 
output [7:0] BYTE_READ, 
 output [1:0] BYTE_ERROR_CODE, 
output BYTE_READY 
 ); 
/* Fill in the code */
endmodule
Receiver
Transmitter
Master 
State 
Machine Byte_To_Send
CLK_MOUSE_IN DATA_MOUSE_IN
CLK_MOUSE_IN / 
CLK_MOUSE_OUT_EN
DATA_MOUSE_IN / 
DATA_MOUSE_OUT/
DATA_MOUSE_OUT_EN
PS2 Clock Line
PS2 Data Line
SEND_BYTE
BYTE_SENT
BYTE_SENT
Byte_Read
READ_ENABLE
9
The following shows Verilog code fragments for the “Transmitter” module: 
module MouseTransmitter( 
//Standard Inputs 
input RESET, 
input CLK, 
//Mouse IO - CLK 
input CLK_MOUSE_IN, 
output CLK_MOUSE_OUT_EN, // Allows for the control of the Clock line 
//Mouse IO - DATA 
input DATA_MOUSE_IN, output DATA_MOUSE_OUT, 
output DATA_MOUSE_OUT_EN, 
//Control 
input SEND_BYTE, 
input [7:0] BYTE_TO_SEND, 
output BYTE_SENT 
 ); 
/* Fill代 写PGEE11117、Python/Java
代做程序编程语言 in the code */
endmodule
10
The following shows Verilog code fragments for the Master State Machine module:
module MouseMasterSM( 
input CLK, 
input RESET, 
//Transmitter Control 
output SEND_BYTE, 
output [7:0] BYTE_TO_SEND, 
input BYTE_SENT, 
//Receiver Control 
output READ_ENABLE, 
input [7:0] BYTE_READ, 
input [1:0] 
BYTE_ERROR_CODE, input 
BYTE_READY, 
//Data Registers 
output [7:0] MOUSE_DX, 
output [7:0] MOUSE_DY, 
output [7:0] MOUSE_STATUS, 
output SEND_INTERRUPT 
 ); 
/* Fill in the code */
endmodule
11
Finally, the above three blocks should be connected in a “Mouse Transceiver” module as 
suggested in the code fragments below: 
Module MouseTransceiver( 
//Standard Inputs 
input RESET, 
input CLK, 
//IO - Mouse side 
inout CLK_MOUSE, 
inout DATA_MOUSE, 
 // Mouse data information 
 output [3:0] MouseStatus, 
output [7:0] MouseX, 
output [7:0] MouseY ); 
// X, Y Limits of Mouse Position e.g. VGA Screen with 160 x 120 resolution 
 parameter [7:0] MouseLimitX = 160; parameter [7:0] 
MouseLimitY = 120; 
/* Fill in the code */
endmodule
12
Note how we deal with bidirectional ports (inout’s) needed here as the Data line and the 
Clock line are both written to and read from. In general, to make use of an inout port 
“Port_Inout_Example”, you need to create an internal wire (“Port_Inout_Example_In”) with 
the value of the “Port_Inout_Example” port assigned to it. This can be used internally as an 
input to user logic when the port is in input mode. To write data to the port, we create an enable 
signal “Port_Inout_Example_Enable” which when set causes the output port to take the value 
of an internal signal “Port_Inout_Example_Out” from user logic. Otherwise, the inout port is 
set to high impedance. The following show Verilog code snippets to the above effect. 
wire Port_Inout_Example_In; 
assign Port_Inout_Example_In = Port_Inout_Example; 
// Use Port_Inout_Example_In as an input to your logic 
…. …. 
// Internal signal Port_Inout_Example_Out can be assigned to the inout port if an enable 
// signal (Port_Inout_Example_Enable) is set 
assign Port_Inout_Example = ( Port_Inout_Example_Enable ? Port_Inout_Example_Out : 1’bZ);
Assessment 
You will need to complete the mouse interface code based on the above, synthesise and verify 
your code with test benches, generate an FPGA bitstream and test on the BASYS 3 board. The 
team member in charge of this peripheral development will be tested in Week 5. This individual 
assessment will account for 25% of your overall lab mark. During the test, you will need to 
demonstrate a functioning mouse interface on the BASYS 3 board through plugging a USB 
mouse to the board, and showing the various mouse register values (Status Byte, X Direction 
Byte, and Y Direction Byte) displayed on the LEDs and seven segment displays of the BASYS 
3 board as the mouse is moved around. You will be supplied with the Verilog description of a 
seven segment display interface (SevenSeg). A specification of the latter can also be found in 
the Digital System Laboratory 3/A material. During the assessment, you will need to be able to 
take the assessor through your code answering any possible query about your design choices, 
coding style, etc. Your code will have been uploaded to Learn prior to the start of the laboratory 
session. 
13
2. VGA Interface
In order to display video onto a monitor screen, we will make use of the VGA port available on 
the BASYS 3 board. The latter uses 14 signals (carried by 14 FPGA pins, see Figure on page 
11 of the BASYS 3 Reference Manual) with 12-bit colour and two synchronisation signals (HS 
– Horizontal Sync, and VS – Vertical Sync). HS and VS allow the monitor to align the incoming 
colour signals such that individual pixels on the screen are coloured correctly. Colours are 
derived by a combination of the three primary colours: red, blue and green. Four bits are used 
for each colour (given 16 possible levels of each colour). 
However, it is possible to use only 8 of the 12-bit colours, especially for compatibility with an 
8 bit bus processor architecture. In this configuration, three bits are used for Red and Green 
(giving 8 possible levels of each colour) and two bits are used for Blue (giving four possible 
levels only, as the human eye is less sensitive to blue than it is to red and green). You could 
either chose to implement an 8-bit colour (connecting only the 3 LSBs of Red and Green, and 
2 LSBs of Blue), or implement the full 12 bits colour but you will have to send the colour 
information twice on an 8-bit bus. In this description, we target the 8-bit configuration. 
You are required to develop an FPGA-based VGA driver which generates the necessary VGA 
signals (8 bit colour information and 2 synchronisation signals) to a VGA monitor using a frame 
buffer that holds frame pixel values. Pages 11- 14 of the manual give details of the VGA 
standard, providing much of the specification for this module - read it carefully. The following 
will take you through a suggested design of the VGA driver. 
What you are required to do
VGA provides a resolution of 640 x 480 pixels (horizontal x vertical). A VGA driver could be 
built from two modules as shown in the following figure: 
- “Frame_Buffer”: A dual ported memory module which can be written to by an outside 
module e.g. a microprocessor, and read from by the “VGA_Sig_Gen” module (see 
Figure 7 below) for VGA display 
- “VGA_Sig_Gen”: VGA signal generator module, which reads consecutive pixel colours 
in raster pattern, from the “Frame_Buffer” module, and outputs the VGA signals through 
the proper FPGA pins 
14
Figure 7. VGA Interface
Unfortunately, the FPGA chip on the BASYS 3 board does not have enough memory resources 
to store a full VGA frame with 8-bit pixels (i.e. 640 x 480 x 8 bits memory). In fact, few FPGA 
chips on the market can hold complete frames on-chip. Instead, off-chip memory (e.g. SDRAM) 
is often used to implement the frame buffer. The BASYS 3 board, however, does not have such 
external memory resource, which means that FPGA logic must be used to hold frame buffer 
data. To solve this problem, we can use a reduced colour resolution as well as a reduced pixel 
resolution to minimise memory storage requirements. In our suggested design, we will reduce 
the screen resolution by 4 in each direction i.e. each 4x4 pixel region on the screen would be 
represented by one single data element on the frame buffer, hence the above wordlength of the 
X and Y addresses in Figure 7 (i.e. 8 and 7 bits respectively instead of 10 and 9 bits for the 640 
x 480 VGA resolution). We also reduce the colour resolution from 256 possible colours to just 
two colours (foreground and background) hence 1 bit data is sufficient for the colour 
information. A dual ported 256 x 128 1-bit memory should then be large enough to hold the 
whole frame buffer, which could be easily stored on the Artix-7 chip. Note that in the proposed 
code below, the foreground and background colours can be set by an external module e.g. a 
microprocessor, through input Config_Colour[15:0] (see Figure 7 above) which represents 2 x 
8 bits of data for two possible 8-bit colours. 
VGA_Sig_Gen
Frame_Buffer
8 HS VS
7 8
VGA_Colour
Addr_X Addr_Y Pixel_Data_Out
Config_Colours[15:0]
Pixel_Data_In Addr_X 8 7 Addr_Y
Write_Enable
15
The following shows possible Verilog code for the Frame_Buffer module: 
module Frame_Buffer( /// 
Port A - Read/Write 
input A_CLK, 
input [14:0] A_ADDR, // 8 + 7 bits = 15 bits hence [14:0] 
input A_DATA_IN, // Pixel Data In 
output reg A_DATA_OUT, 
input A_WE, // Write Enable 
//Port B - Read Only 
input B_CLK, 
input [14:0] B_ADDR, // Pixel Data Out 
output reg B_DATA 
 ); 
 // A 256 x 128 1-bit memory to hold frame data 
 //The LSBs of the address correspond to the X axis, and the MSBs to the Y axis 
/* Fill in the code */
endmodule
16
The following shows Verilog code fragments for the VGA_Sig_Gen module: 
module VGA_Sig_Gen( 
input CLK, 
//Colour Configuration Interface 
input [15:0] CONFIG_COLOURS, 
// Frame Buffer (Dual Port memory) Interface 
output DPR_CLK, 
output [14:0] VGA_ADDR, 
input VGA_DATA, 
//VGA Port Interface 
output reg VGA_HS, 
output reg VGA_VS, 
output [7:0] VGA_COLOUR 
 ); 
//change the clock to 25MHz to drive the VGA display 
/* 
Define VGA signal parameters e.g. Horizontal and Vertical display time, pulse widths, front and back porch 
widths etc. 
*/ 
 // Use the following signal parameters 
parameter HTs = 800; // Total Horizontal Sync Pulse Time 
parameter HTpw = 96; // Horizontal Pulse Width Time 
parameter HTDisp = 640; // Horizontal Display Time 
parameter Hbp = 48; // Horizontal Back Porch Time 
 parameter Hfp = 16; // Horizontal Front Porch Time 
parameter VTs = 521; // Total Vertical Sync Pulse Time 
parameter VTpw = 2; // Vertical Pulse Width Time 
parameter VTDisp = 480; // Vertical Display Time 
parameter Vbp = 29; // Vertical Back Porch Time 
parameter Vfp = 10; // Vertical Front Porch Time 
/* Fill in the code */
endmodule
Note that the VGA standard was designed for CRT monitors, and hence it is important to be 
aware of the timing of the original system, even if we are using a digital display. It should also 
be noted that in addition to the HS and VS timing definitions within the reference manual, the 
8-bit colour signal needs to be set to 8'h00 (zero) whilst not during the display time. 
You will need to complete your code based on the above, synthesise and verify your code with 
test benches, generate an FPGA bitstream and test on the BASYS 3 board. Test the above driver 
block by packaging it within a top level module that generates frame buffer and colour 
configuration data, and outputs the VGA signals to the proper FPGA pins. Display the following 
chequered image on the VGA display, changing colours every one second. 
17
Figure 8. Chequered image to be displayed on the VGA screen
Assessment
The team member in charge of this peripheral development will be tested on Week 5. This 
individual assessment will account for 25% of your overall lab mark. During the test, you will 
need to demonstrate a functioning VGA interface on the BASYS 3 board through plugging a 
VGA monitor to the BASYS 3 board, and showing the above chequered image, changing 
colours every one second. You will need to take the assessor through your code answering any 
possible query about your design choices, coding style, etc. Your code will have been uploaded 
to Learn prior to the start of the laboratory session. 
1 VGA Buffer Element, 
equivalent to a 4x4 VGA 
Pixel Area on screen
18
3. Microprocessor-based System for Remote Car Control
The following figure gives a block diagram of the overall FPGA-based system for remote car 
control. At its heart is a microprocessor which will be the master of an 8-bit bus to which various 
peripherals (slaves) will be connected. 
Figure 9. Microprocessor-based system block architecture
The following presents the memory mapping of the peripherals on the microprocessor’s data 
address bus. 
Peripheral Base Address High Address
IR Transmitter 0x90 0x90 
Mouse 0xA0 0xA2 
VGA 0xB0 0xB2 
LEDs 0xC0 0xC0 
SevenSeg 0xD0 0xD1 
Timer 0xF0 0xF3 
Table 1. Peripheral Memory Address Mapping
Note that the proposed microprocessor has a Harvard architecture which means that the 
instruction memory and data memory are distinct with separate address and data busses for each 
of them. Parallel accesses to both instructions and program data can thus be made, resulting in 
higher performance compared to Von-Neumann processors where instructions and data are 
stored on the same memory. 
19
In our architecture, the data memory consists of 128 bytes and is mapped to the 
microprocessor’s address space as follow: 
Data Memory Base Address High Address
0x00 0x7F 
Table 2. Data Memory Address Mapping
Note that in advanced microprocessor systems, data memory is given its own high speed local 
bus, with a separate peripheral bus for relatively slower devices. In our case, data memory 
shares the same bus as the peripherals since high performance is not a major concern for us in 
this lab. 
The following presents a suggested Verilog design of the data memory: 
module RAM( 
 //standard signals 
input CLK, 
//BUS signals 
inout [7:0] BUS_DATA, 
input [7:0] BUS_ADDR, 
input BUS_WE 
 ); 
parameter RAMBaseAddr = 0; 
parameter RAMAddrWidth = 7; // 128 x 8-bits memory 
//Tristate 
wire [7:0] BufferedBusData; 
reg [7:0] Out; 
reg RAMBusWE; 
//Only place data on the bus if the processor is NOT writing, and it is addressing this memory assign 
BUS_DATA = (RAMBusWE) ? Out : 8'hZZ; assign BufferedBusData = BUS_DATA; 
//Memory 
reg [7:0] Mem [2**RAMAddrWidth-1:0]; 
// Initialise the memory for data preloading, initialising variables, and declaring constants
initial $readmemh("Complete_Demo_RAM.txt", Mem); 
//single port ram always@(posedge CLK) 
begin 
// Brute-force RAM address decoding. Think of a simpler way... 
if((BUS_ADDR >= RAMBaseAddr)  (BUS_ADDR  reports current timer value 
//BaseAddr + 1 -> Address of a timer interrupt interval register, 100 ms by default 
//BaseAddr + 2 -> Resets the timer, restart counting from zero 
//BaseAddr + 3 -> Address of an interrupt Enable register, allows the microprocessor to disable 
 // the timer 
//This module will raise an interrupt flag when the designated time is up. It will 
//automatically set the time of the next interrupt to th         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
