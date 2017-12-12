# Four GPUs workstation for deep learning 四路GPU 工作站

This is a guide for 4-GPUs workstation for deep learning or GPU computing, I hope this article can help you build a cost-effective computing platform.

这是一篇针对深度学习的4路工作站配置向导，我希望这篇文章能帮助大家组建一台高性价比的计算平台。

This is a link about PCI Express Interface parameters, 这是一篇关于PCIe接口参数的链接: http://timdettmers.com/2017/04/09/which-gpu-for-deep-learning/
As I know, When the CPU integrated PCIe controller, the mainstream GPU with PCIe bus are directly connected to the CPU. So in most cases, How many GPUs we can use mainly depends on these four designs：

据我所知，当CPU集成了PCIe控制器之后，主流的GPU都是直连在CPU上工作。所以在大多数情况下，多显卡由下列4点因素决定：

1) How many PCI Express Lanes that CPU have. CPU有多少条PCIe

2) How many PCI Express Lanes that motherboared chepset have. 主板有多少条PCIe

3) The design of motherboard of PCI Express slots. 主板PCIe插槽的设计

4) The design and model of graphics card. 显卡的设计

### Example 示例:

If we want use z270 motherboard for 4 GTX 1080 Ti, can we? Let us check CPU first.

如果我们想用z270主板来带4片GTX 1080 Ti怎么样？

Z270 chipset motherboard support Intel® Socket 1151 for 7th/6th Generation Core™ i7/Core™ i5/Core™ i3/Pentium®/Celeron® Processors, but all of those CPU have only 16 of PCIe lanes, and PCI Express configurations can be 1x16 or 2x8 or 1x8+2x4, hard to pull 4 GPUs. you can find all intel CPU chipset in https://ark.intel.com

z270主板支持Socket 1151 6/7代i7/i5/i3/奔腾/赛扬处理器，但是这些CPU都仅仅只有16条PCIe，并且仅能配置成1x16 or 2x8 or 1x8+2x4，这样的配置很难匹配4片GPU，可以在 https://ark.intel.com 这个链接中查看关于CPU和芯片组的规格。

How about X99 or X299 motherboard with Intel® Core™ X-series Processors? most of them have 40 of PCIe lanes, and every GPU can use x8 of PCIe lanes. That right but GTX1080 Ti need x16 PCIe lanes to full performance. And both x299/x99 motherboard and X-series are very expensive. 

那么X99或者X299芯片的主板加上X-series系列的cpu 怎么样？大多出这些处理器具有40条PCIe，并且每片GPU可以分配到8条PCIe。这些都是正确的，但是GTX 1080 Ti需要16条PCIe 才能完全发挥性能，并且无论是x299/x99主板或者X-series CPU都非常的贵。

In these circumstances, I think Intel® Xeon® Processor E5 v4 Family and Intel® C612 Chipset motherboard is good choice.

在这样的情况下，我想至强E5 v4系列的cpu和C612芯片主板会是一个很好的选择。

### Benchmark of GPU computing GPU运算的跑分

Here is a benchmark link about each software tool: http://dlbench.comp.hkbu.edu.hk/?v=v7, And based on my own tests Show that features: 

这是一个关于多个框架的跑分连接，根据这个链接及我自己的测试，得到GPU计算以下的特征：

1) CPU does not need to be good. CPU不需要非常强的那种

2) Memory does not need so much. 内存也不需要太大

3) The stronger the GPU the better. GPU越强越好

4) The bigger the GPU memory and bandwidth the better. 内存和内存带宽越大越好

5) GTX1080 and lower than it requires only x8 PCIe Gen3, Only higher than the GTX1080 model needed x16 of PCIe Gen3. GTX1080以及更差的显卡仅仅需要8条PCIe Gen3，只有比GTX1080更高的型号的显卡才需要16条PCIe Gen3

### Configuration list 配置列表

So if you want to use four GTX 1080 or lower GPU 所以如果你需要使用4块GTX 1080 或者更差的GPU: 

| Parts       | Model                            | Number |
| ----------- | -------------------------------- | ------ |
| CPU         | Intel Xeon Processor  E5-2620 v4 | 1      |
| CPU cooler  |                                  | 1      |
| Motherboard | ASRock EPC612D8A                 | 1      |
| RAM         | Samsung DDR4 2133  RDIMM 8GB     | 4      |
| GPU card    | GTX 1080 Founder version         | 4      |
| Power       | Corsair RM1000x 1000W            | 1      |
| Case        | IN WIN 707                       | 1      |

If you want use four GTX 1080 Ti or better GPU 如果你想使用4块GTX 1080 Ti 或者更好的GPU:

| Parts       | Model                           | Number |
| ----------- | ------------------------------- | ------ |
| CPU         | Intel Xeon Processor E5-2603 v4 | 2      |
| CPU cooler  | Cooler Master T400i             | 2      |
| Motherboard | ASRock EP2C612 WS               | 1      |
| RAM         | Samsung DDR4 2133 RDIMM 8GB     | 4      |
| GPU card    | GTX 1080 Ti Founder version     | 4      |
| Power       | SUPER FLOWER LEADEX T 1600W     | 1      |
| Case        | IN WIN 707                      | 1      |

Note 注意 : 

1) ASRock workstation motherboard have full-featured cost-effective, be my best choice. Or you can choice other brand. 华擎的工作站主板性价比最好，是我的首选，或者你也可以选择其他的品牌

2) Dual CPU should note that two GPU need QPI link, so you can only select Xeon E5-2600 v4. 要注意到双路CPU需要QPI连接，所以在双路情况下，你只能选择Xeon E5-2600 v4

3) According to CPU TDP selection cooler.  根据CPU的TDP设计来选择散热器

4) According to all system TDP selection Power unit ( GTX 1080 TDP is 180W etc). 根据整个硬件系统的TDP来选择电源(GTX 1080的设计额定功耗只有180W 等等)

5) Please select Founder version GPU, Last time someone bought 4 Asus ROG STRIX-GTX1080-O8G-11GBPS, but each of GPU take more than 2 slots… … 请选择公版设计的显卡，上次有个朋友买了4片华硕ROG STRIX-GTX1080-O8G-11GBPS，结果这款显卡需要的槽位大于2个... ...

6) Now most GPU require two PCI slots,  so  you need to pay attention to PCIe slot. 目前大多数显卡都需要占用2个PCI槽位，建议注意PCI插槽的位置，尤其在机箱选择上
