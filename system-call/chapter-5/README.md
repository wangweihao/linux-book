# chapter-5 比进程还小的调度单元——线程

想必经过了前面关于进程的章节的描述，大家对进程的概念有了足够的认识了吧？简单的说，**进程是一个动态的概念，它是静态的可执行文件执行过程的描述，其包含了一个静态程序运行时的状态和其所占据的系统资源的总和。**在这样的描述中，也很容易理解在某一时刻，某个CPU的核心上只运行着某一个进程的既定事实。那CPU的计算能力怎么描述？我们通常用主频等概念来描述CPU的性能。主频也叫时钟频率，单位是兆赫（MHz）或千兆赫（GHz），用来表示CPU的运算、处理数据的速度。这个值的倒数就叫做CPU的时钟周期。通常，主频越高，相应的一个时钟周期就越短，CPU处理数据的速度就越快。1993年的intel Pentium Classic处理器主频仅75~200 MHz，而今天（2015年）的Intel core系列不乏主频3.6GHz的产品。如果一直这样发展下去该有多好，CPU的主频像坐火箭一样提升，我们的程序也会运行的越来越快。但是今天，免费的午餐结束了。CPU的发展遇到了天花板，到了4GHz的时候，由于资源消耗和散热等原因，人类目前没有办法在单个CPU核心上再无止境的提升主频了。除非纳米工艺有了突破性的进展，否则这个天花板是捅不穿的。

怎么办？为了提升性能，所有主流的CPU厂商均采用了类似的方案，那就是多CPU多核心的方案。也就是说，一个CPU芯片上不再只是一个核心，而是嵌入了多个可以独立运算的核心。首先操作系统在CPU多核的管理上就经历了一次升级改进，然后，对计算机程序的设计就有了更新的要求，那就是并行的运算和数据处理。因为CPU是多核心的，如果设计的程序还是只能运行在单CPU核心上，那就没有办法完全利用CPU的性能而造成浪费。不得不说，在这一点上，硬件确实是走到了软件的前面。

说这么多，这和本章的主题线程又有什么关系吗？当然有，线程就是一个可以用来解决一个程序并行处理数据的解决方案。不过必须说明的是，线程并非只用来并行的处理数据，线程的使用有时候也是用于简化程序逻辑等目的。而且，线程并非是只能在多核心CPU上才能使用的，单CPU单核心也可以使用。此时，在任务的调度上虽然要看具体操作系统的安排，但是有一点可以肯定的是，诸多任务必然是在单个CPU核心上处于不断的切换中的。

看起来有点难是吗？不用怕，接下来的例子都很简单易懂，虽然我接下来举的例子还是具体的工程上的场景，但是我希望你能尝试理解和习惯思考这种论述方式。生活化的例子我也能举出来，但是我觉得习惯了具象的东西之后，再面对抽象的问题会更加难以接受。所以我之后的论述都采用较为专业的提法，也希望大家能学着理解和思考。