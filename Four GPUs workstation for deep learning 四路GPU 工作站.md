This is a guide for 4-GPUs workstation for deep learning or GPU computing, I hope this article can help you build a cost-effective computing platform.

This is a link about PCI Express Interface parameters: https://www.trentonsystems.com/industry-applications/pci-express-interface

As I know, When the CPU integrated PCIe controller, the mainstream GPU with PCIe bus are directly connected to the CPU. So in most cases, How many GPUs we can use mainly depends on these four designs：

1) How many PCI Express Lanes that CPU have.

2) How many PCI Express Lanes that motherboared chepset have.

3) The design of motherboard of PCI Express slots.

4) The design and model of graphics card.

### Example:

If we want use Z270 motherboard for 4 GTX 1080 Ti, can we? Let us check CPU first.

Z270 chipset motherboard support Intel® Socket 1151 for 7th/6th Generation Core™ i7/Core™ i5/Core™ i3/Pentium®/Celeron® Processors, but all of those CPU have only 16 of PCIe lanes, and PCI Express configurations can be 1x16 or 2x8 or 1x8+2x4, hard to pull 4 GPUs. you can find all intel CPU chipset in https://ark.intel.com

How about X99 or X299 motherboard with Intel® Core™ X-series Processors? most of them have 40 of PCIe lanes, and every GPU can use x8 of PCIe lanes. That right but GTX1080 Ti need x16 PCIe lanes to full performance. And both x299 motherboard and X-series CPU are very expensive. 

In these circumstances, I think Intel® Xeon® Processor E5 v4 Family and Intel® C612 Chipset motherboard is good choice.

### Benchmark of GPU computing

Here is a benchmark link about each software tool: http://dlbench.comp.hkbu.edu.hk/?v=v7, And based on my own tests Show that features: 

1) CPU does not need to be good

2) Memory does not need so much

3) The stronger the GPU the better

4) The bigger the GPU memory the better

5) GTX1080 and lower than it requires only x8 PCIe Gen3, Only higher than the GTX1080 model needed x16 of PCIe Gen3

### Configuration#1

So if you want to use four GTX 1080: 

| Parts       | Model                            | Number |
| ----------- | -------------------------------- | ------ |
| CPU         | Intel Xeon Processor  E5-2620 v4 | 1      |
| CPU cooler  |                                  | 1      |
| Motherboard | ASRock EPC612D8A                 | 1      |
| RAM         | Samsung DDR4 2133  RDIMM 8GB     | 4      |
| GPU card    | GTX 1080 Founder version         | 4      |
| Power       | Corsair RM1000x 1000W            | 1      |
| Case        | IN WIN 707                       | 1      |

If you want use four GTX 1080 Ti or better GPU:

| Parts       | Model                           | Number |
| ----------- | ------------------------------- | ------ |
| CPU         | Intel Xeon Processor E5-2603 v4 | 2      |
| CPU cooler  | Cooler Master T400i             | 2      |
| Motherboard | ASRock EP2C612 WS               | 1      |
| RAM         | Samsung DDR4 2133 RDIMM 8GB     | 4      |
| GPU card    | GTX 1080 Founder version        | 4      |
| Power       | SUPER FLOWER LEADEX T 1600W     | 1      |
| Case        | IN WIN 707                      | 1      |

PS: 

1) ASRock workstation motherboard have full-featured cost-effective, be my best choice;

2) Dual CPU should note that two GPU need QPI link, so you can only select Xeon E5-2600 v4

3) According to CPU TDP selection cooler 

4) According to all system TDP selection Power unit ( GTX 1080 TDP is 180W etc)

5) Please select Founder version GPU, Last time someone bought 4 ROG STRIX-GTX1080-O8G-GAMING, but each of GPU take more than 2 slots… … 

6) Now most GPU require two PCI slots,  so  you need to pay attention to PCIe slot

![1511170524605](/Users/zichun.wan/Downloads/1511170524605.jpg)
