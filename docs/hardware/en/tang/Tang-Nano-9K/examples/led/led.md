---
title: Light LED
---

> Edit on 2022.04.11

From this essay we can learn the basic usage of Gowin IDE

## Create Project

Create Project：File-->NEW-->FPGA Dsign Project-->OK
![](./../../../../../zh/tang/Tang-Nano/assets/LED-1.png)

Set project name and project path (File name and project path shoule be English)
![](./../../../../../zh/tang/Tang-Nano/assets/LED-2.png)

Choose correct device: 
![Tang_nano_9k_device_choose](./../../../../../zh/tang/Tang-Nano-9K/nano_9k/Tang_nano_9k_Device_choose.png)

## Prepare codes

After creating project, we can start editing codes. 
To creat a new file, we can click where the arrow points to in the picture or use shortcut key Ctrl+N.
Then choose Verilog File in the pop-up window.
![](./../../../../../zh/tang/Tang-Nano/assets/LED-5.png)

Name for file (Suggested using English)
![](./../../../../../zh/tang/Tang-Nano/assets/LED-6.png)

Double click the created file, then edit in right window
![](./../../../../../zh/tang/Tang-Nano/assets/LED-7.png)

- We use light led as an example, copy the following "LED example codes" into the created file or edit the created file by yourself.  

~~~v
module led (
    input sys_clk,          // clk input
    input sys_rst_n,        // reset input
    output reg [5:0] led    // 6 LEDS pin
);

reg [23:0] counter;

always @(posedge sys_clk or negedge sys_rst_n) begin
    if (!sys_rst_n)
        counter <= 24'd0;
    else if (counter < 24'd1349_9999)       // 0.5s delay
        counter <= counter + 1;
    else
        counter <= 24'd0;
end

always @(posedge sys_clk or negedge sys_rst_n) begin
    if (!sys_rst_n)
        led <= 6'b111110;
    else if (counter == 24'd1349_9999)       // 0.5s delay
        led[5:0] <= {led[4:0],led[5]};
    else
        led <= led;
end

endmodule
~~~

After finishing edit the file, it's necessary to tick the `Use DONE as regular IO` in Project->Configuration->Place&Route->Dual-Purpose Pin to avoid error.
![img_configuration](./../../../../../zh/tang/Tang-Nano-9K/nano_9k/LED_Configuration.png)

## Systhesize, constrain, place&route

### Systhesize

After finishing steps above, go to the "Process" interface, systhesize the edited file, which means running "Systhesize". 
![](./../../../../../zh/tang/Tang-Nano-9K/nano_9k/nano_9k_synthsize.png)

If the result is the same as shown below
![](./../../../../../zh/tang/Tang-Nano/assets/LED.png) 

It means that there is no bug in our code, we can continue the next steps. 

If there is some thing wrong, please fix by yourself. 

### Constrain

- Here is no clock constraint involved

To realize function of the code on FPGA, we need bind the ports we define with the chip pins.

Double click the FloorPlanner in the Process interface to set pin constrain(This can be continued if failing systhesize). 

![](./../../assets/examples/led_pjt_2.png)

First time open FloorPlanner it will notice lack ".cst" file, we just choose ok. 
![](./../../../../../zh/tang/Tang-Nano/assets/LED-9.png)

The leds schematic of nano 9k is as shown below:
![](./../../../../../zh/tang/Tang-Nano-9K/nano_9k/LED_Pins.png "nano 9k led pins")

In this GUI interface we have two ways to constrain pins:
- Drag the corresponding port to the pin of chip
- Type the pin number corresponding to the port in IO constraint(This is shown as below)

So we can do the ordered operations in the opened window as what the following picture shows:
![](./../../../../../zh/tang/Tang-Nano-9K/nano_9k/LED_FloorPlanner.png)


### Place&Route

> If it shows error 2017, the solve way can be found ahead 

After finishing Running "Place&Route" in the Process interface window, the result will be as same as below
![](./../../../../../zh/tang/Tang-Nano-9K/nano_9k/LED_Place&Route.png)

## Program

Then connect the board with computer, download firmware.
You can select the device according to the following picture.
![](./../../../../../zh/tang/Tang-Nano-9K/nano_9k/nano_9k_device_scan.png)

We use download to SRAM as an example.
- Configure download mode

![](./../../../../../zh/tang/Tang-Nano-9K/nano_9k/nano_9k_sram_program.png "configure sram download mode")

Download
![](./../../../../../zh/tang/Tang-Nano-9K/nano_9k/nano_9k_sram_download.png "start sram download")

Then the board runs as shown：

![](./../../../../../zh/tang/Tang-Nano-9K/nano_9k/blink.gif)

If you need to store firmware with no power, just choose download to flash mode.

## End

Now the tutorial ends, if you have any suggestions, just leave a message.