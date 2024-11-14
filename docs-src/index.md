# Uboot
- **Students:** Joras Oliveira, Eduardo Barros
- **Course:** Engenharia da Computação, Ciencia da Computação
- **Semester:** 9, 6
- **Contact:** jorascco@al.insper.edu.br, eduardosmb@al.insper.edu.br
- **Year:** 2024

## Starting

To follow this tutorial you need:

- **Hardware:** DE10-Standard
- **Memory Card:** At least 8GB
- **Terminal emulator:** Any one that can read serial ports (we recommend picocom)

## Motivation

Expliquem porque quiseram fazer esse tutorial.

----------------------------------------------

## Preparing the Embedded Linux

For this tutorial, we will use a pre-compiled embedded Linux. Please follow the steps below:

1. **Download the ZIP Files**
   - Download the ZIP files located inside the "build" folder.

2. **Insert the Memory Card**
   - Insert your memory card into your computer.

3. **Create Two Partitions**
   - Create two separate partitions on your memory card.

4. **Extract ZIP Contents**
   - Extract the contents of `xyx.zip` to the **first partition**.
   - Extract the contents of `abc.zip` to the **second partition**.

5. **Eject the Memory Card**
   - Safely eject your memory card from the computer.

Now your memory card is prepared with the embedded Linux system.


## Entering in the uboot environment

Turn the FPGA on with the memory card inserted. After that run the picocom:
```bash
sudo picocom /dev/ttyUSB0 -b 115200
```


