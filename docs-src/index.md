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


### Understanding U-Boot: Why It Matters 

U-Boot (Das U-Boot) is an essential component in the embedded Linux ecosystem, serving as a robust bootloader that bridges the gap between hardware initialization and operating system startup. It is one of the first pieces of software to execute on a system, handling low-level hardware configuration, loading the Linux kernel, and managing boot processes. For developers and engineers, understanding U-Boot is critical because it enables precise control over how embedded systems boot, troubleshoot issues, and adapt to hardware-specific requirements.  

Mastering U-Boot empowers developers to customize the boot process for unique hardware setups, enable advanced features like secure boot, and optimize system performance. It also provides invaluable tools for debugging, testing, and recovering embedded devices in production environments. Whether you're building custom hardware, integrating Linux into an embedded platform, or troubleshooting boot-time issues, a strong grasp of U-Boot commands and functionality is indispensable.

### History of U-Boot

The history of U-Boot begins in the late 1990s with a bootloader called 8xxROM, originally written by Magnus Damm for the PowerPC 8xx architecture. In October 1999, Wolfgang Denk took over the project and moved it to SourceForge.net, renaming it to PPCBoot, as the site did not allow project names starting with numbers. The first publicly released version, 0.4.1, came out on July 19, 2000. PPCBoot was initially focused on PowerPC-based systems, but its architecture support would soon expand.

In 2002, a brief fork of PPCBoot gave rise to ARMBoot, which was quickly merged back into the main project. On October 31, 2002, PPCBoot-2.0.0 was released, marking the last version under the PPCBoot name. The project was renamed to U-Boot (Universal Bootloader) to reflect its growing support for multiple architectures beyond PowerPC, including x86. This change was solidified with the release of U-Boot-0.1.0 in November 2002, which introduced support for the x86 architecture. Over the next couple of years, support for additional architectures was added, including MIPS, Nios II, ColdFire, and MicroBlaze, making U-Boot a versatile tool for various embedded systems.

By 2004, U-Boot had become the bootloader of choice for embedded systems, with the release of U-Boot-1.1.2 supporting over 200 board manufacturers across multiple architectures. The name "Das U-Boot" was adopted, playing on the German film *Das Boot* (The Submarine), adding a bilingual pun. U-Boot is free software, released under the GNU General Public License, and can be built on an x86 PC for various target architectures. It is widely regarded as one of the most flexible, actively developed, and feature-rich open-source bootloaders for embedded Linux systems. As highlighted in *Building Embedded Linux Systems* by Karim Yaghmour, U-Boot is seen as the "richest, most flexible, and most actively developed open-source bootloader available."


??? info 
      if you want to read more on uboot:
      https://en.wikipedia.org/wiki/Das_U-Boot,
      https://source.denx.de/u-boot/u-boot

----------------------------------------------

## Preparing the Embedded Linux

For this tutorial, we will use a pre-compiled embedded Linux. Please follow the steps below:

1. **Insert the Memory Card**
      - Insert your memory card into your computer.

2. **Create Two Partitions**
      - Create two separate partitions on your memory card.

3. **Download the ZIP Files**
      - Download the ZIP files located inside the "build" folder.

4. **Extract ZIP Contents**
      - Extract the contents of `xyx.zip` to the **first partition**.

5. **Eject the Memory Card**
      - Safely eject your memory card from the computer.

Now your memory card is prepared with the embedded Linux system.

## u-boot.src
Inside the smaller partition of SD-card, you will find a u-boot.src file, this is our uboot script, its function is similar to a bash.rc script, as it just executes each command in order, you will notice that every command inside the u-boot.src file is in itself a script, for the most past these scripts simply write something into memory then executes it. 

Open the u-boot.src file in a text editor, and you should see something like this:

```bash
if fatload mmc 0:1 $fpgadata soc_system.rbf; then
    fpga load 0 $fpgadata $filesize;
fi;
run bridge_enable_handoff;
run mmcload;
run mmcboot;
```
??? info 
        You might see some weird characters lile ' ' in the file, this is a issue related to the encoding format of the file itself.


## Entering in the uboot environment

Turn the FPGA on with the memory card inserted. After that run the picocom:
```bash
sudo picocom /dev/ttyUSB0 -b 115200
```

After you start the fpga, there should be a warning on screen telling you to press any button to disable automatic uboot, ***press any button*** so we can force the manual uboot (what we want). In this mode, we can use the printenv command to print all the environemt variables, scripts and commands. 


### Bridge enable handoff
We will start by printing the 'bridge_enable_handoff, a scirpt designed to configure hardware bridges and handoffs (transitions between stages in the hardware setup). It configures hardware bridges that facilitate communication between different components, such as the FPGA, SDRAM, and AXI bus. The "handoff" refers to the seamless transition of control and configuration from the bootloader (e.g., U-Boot) to the operating system or subsequent firmware. This process ensures that all necessary interfaces are properly initialized and operational, avoiding potential communication or hardware access issues as the system progresses through its boot stages. By setting up these bridges during boot, the system guarantees stable and efficient interactions between its components, paving the way for reliable operation.

the output should be:
```bash
bridge_enable_handoff=
    mw $fpgaintf ${fpgaintf_handoff};
    go $fpga2sdram_apply;
    mw $fpga2sdram ${fpga2sdram_handoff};
    mw $axibridge ${axibridge_handoff};
    mw $l3remap ${l3remap_handoff};
```
Let's go command by command, and understand this togheter.

1. **`mw $fpgaintf ${fpgaintf_handoff};`**
   - **`mw`**: This stands for "memory write," which is a U-Boot command used to write a value into a memory location.
   - **`$fpgaintf`**: This is a variable representing the address of the FPGA interface (this could be a hardware register or memory-mapped IO address).
   - **`${fpgaintf_handoff}`**: This is another variable representing the value to be written at the `$fpgaintf` address. The value likely contains configuration data for the FPGA interface, which will be passed from one part of the system to another as part of the "handoff" process.

   In this command, the FPGA interface is being configured by writing the value stored in `${fpgaintf_handoff}` to the address `$fpgaintf`.

2. **`go $fpga2sdram_apply;`**
   - **`go`**: This is a U-Boot command that is used to jump to an address in memory and start executing code from that address. It is commonly used to jump to a part of memory where code is stored (such as firmware or an application).
   - **`$fpga2sdram_apply`**: This variable likely holds the address of code that is responsible for applying the FPGA-to-SDRAM configuration. The `go` command tells U-Boot to jump to this address and begin executing the code there.

   This command initiates the FPGA-to-SDRAM application process, possibly activating or applying the configuration that was set up earlier.

3. **`mw $fpga2sdram ${fpga2sdram_handoff};`**
   - **`mw`**: This is again the memory write command.
   - **`$fpga2sdram`**: This variable points to the address where the FPGA-to-SDRAM configuration should be applied.
   - **`${fpga2sdram_handoff}`**: This variable holds the configuration data to be written to the `$fpga2sdram` address. This likely configures how the FPGA communicates with SDRAM.

   This command writes the value stored in `${fpga2sdram_handoff}` to the address `$fpga2sdram`, configuring the bridge between the FPGA and SDRAM as part of the handoff.

4. **`mw $axibridge ${axibridge_handoff};`**
   - **`mw`**: Writes a value to memory.
   - **`$axibridge`**: This variable refers to the address of the AXI bridge (a hardware component that allows communication between different parts of the system, such as between processors or memory).
   - **`${axibridge_handoff}`**: This contains the value that should be written to the `$axibridge` address. This value likely configures or enables the AXI bridge.

   This command configures the AXI bridge by writing the value stored in `${axibridge_handoff}` to the `$axibridge` address.

5. **`mw $l3remap ${l3remap_handoff};`**
   - **`mw`**: Again, this is the memory write command.
   - **`$l3remap`**: This variable likely points to the address of a component responsible for remapping memory in the system's L3 cache or memory controller.
   - **`${l3remap_handoff}`**: This variable holds the configuration value for the L3 remap process, possibly adjusting memory mappings for better access performance.

   This command configures the L3 remapping by writing the value stored in `${l3remap_handoff}` to the `$l3remap` address.

### Overall Explanation

This sequence is configuring various hardware bridges and interfaces, such as the FPGA interface, FPGA-to-SDRAM bridge, AXI bridge, and L3 memory remapping. The handoff values (`${fpgaintf_handoff}`, `${fpga2sdram_handoff}`, etc.) are being written to specific hardware addresses (`$fpgaintf`, `$fpga2sdram`, `$axibridge`, `$l3remap`) to prepare the system for a specific stage of operation. The `go` command executes code that applies some of the configurations, making the system ready to transition into the next phase, likely involving further hardware initialization or the booting of an operating system.

This process ensures the system is properly configured before jumping into the main system operation, such as loading the kernel or initializing other devices.



## Recursos Markdown

Vocês podem usar tudo que já sabem de markdown mais alguns recursos:

!!! note 
    Bloco de destaque de texto, pode ser:
    
    - note, example, warning, info, tip, danger
    
!!! example "Faça assim"
    É possível editar o título desses blocos
    
    !!! warning
        Isso também é possível de ser feito, mas
        use com parcimonia.
    
??? info 
    Também da para esconder o texto, usar para coisas
    muito grandes, ou exemplos de códigos.
    
    ```txt
    ...
    
    
    
    
    
    
    
    
    
    
    
    oi!
    ```
    
- **Esse é um texto em destaque**
- ==Pode fazer isso também==

Usar emojis da lista:

:two_hearts: - https://github.com/caiyongji/emoji-list


```c
// da para colocar códigos
 void main (void) {}
```

É legal usar abas para coisas desse tipo:
    
=== "C"

    ``` c
    #include <stdio.h>

    int main(void) {
      printf("Hello world!\n");
      return 0;
    }
    ```

=== "C++"

    ``` c++
    #include <iostream>

    int main(void) {
      std::cout << "Hello world!" << std::endl;
      return 0;
    }
    ```

Inserir vídeo:

-  Abra o youtube :arrow_right: clique com botão direito no vídeo :arrow_right: copia código de incorporação:

<iframe width="630" height="450" src="https://www.youtube.com/embed/UIGsSLCoIhM" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

!!! tip
    Eu ajusto o tamanho do vídeo `width`/`height` para não ficar gigante na página
    
Imagens você insere como em plain markdown, mas tem a vantagem de poder mudar as dimensões com o marcador `{width=...}`
    
![](icon-elementos.png)

![](icon-elementos.png){width=200}