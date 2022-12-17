


---
title: "FPGA之差分信号"
date: 2022-11-22T12:15:33+08:00
draft: false
author: "sealong"
categories: "fpga"
tags: ["fpga"]
thumbnail: "images/thumbnail/fpga.png"
headline: 
  enabled: true
  image: "images/headline/fpga.png"
---
差分信号
<!--more-->

<div id="cnblogs_post_body" class="blogpost-body blogpost-body-html">
<p>本文转载自：<span>&nbsp;MYMINIEYE微信公众号</span></p>
<p><strong>1.差分信号简介</strong></p>
<p><strong>1.1差分信号</strong></p>
<p>区别于传统的一根<a href="http://www.hqpcb.com/zhuoluye11/?tid=26&amp;plan=fashaoyou" target="_blank" rel="noopener"><span style="text-decoration: underline">信号线</span></a>一根地线的做法，差分传输在两根线上都传输信号，这两个信号的振幅相同，相位相反，在这两根线上的传输的信号就是差分信号。信号接收端通过比较这两个电压的差值来判断发送端发送的逻辑状态。在电路板上，差分走线必须是等长、等宽、紧密靠近、且在同一层面的两根线。</p>
<center><img src="https://file.elecfans.com/web1/M00/DC/3D/pIYBAGAKXc-AV6hqAAC7NERQULQ499.png" class="medium-zoom-image"></center>
<p><strong>1.2差分信号的抗干扰原理</strong></p>
<p>在实际线路传输中，线路存在干扰，并且同时出现在差分线对上，线路传输干扰同时存在于走线相同的差分对上，可认为一对差分线上所收到的干扰是相同的，但是两根传输线之间的电压差是不变的，因此差分信号有较强的抗干扰能力。</p>
<center><img src="https://file.elecfans.com/web1/M00/DB/BD/o4YBAGAKXg-AED8HAAH6prfs944575.png" class="medium-zoom-image"></center>
<p><strong>2.Xilinx7系列IO实现差分信号</strong></p>
<p><strong>2.1特点</strong></p>
<p>Xilinx7系列<a href="http://www.elecfans.com/tags/fpga/" target="_blank" rel="noopener"><span style="text-decoration: underline">FPGA</span></a>的HR和HPbank，每个bank有50个I/O管脚，每个I/O管脚都可配置成输入、输出。每个bank的首尾管脚只能作为单端I/O，其余48个I/O则可配置成24对差分I/O。在差分信号的实现过程中，管脚分配应选择相应电平标准的bank中除首尾以外的其他48个IO。</p>
<p><strong>2.2实现</strong></p>
<p>Xilinx7系列的差分信号的实现主要通过IBUFDS、OBUFDS、IOBUFDS等原语的调用，在程序中直接进行原语的例化，以IBUFDS和OBUFDS为例：</p>
<p><strong>2.2.1IBUFDS</strong></p>
<p>IBUFDS：用于将差分输入信号转化成标准单端信号，且可加入可选延迟。在IBUFDS原语中，输入信号为I、IB，一个为主，一个为从，二者相位相反。</p>
<center><img src="https://file.elecfans.com/web1/M00/DB/BD/o4YBAGAKXlOAUq3vAAAyNYmOSw0378.png" class="medium-zoom-image"></center>
<p>源码</p>
<center><img src="https://file.elecfans.com/web1/M00/DB/BD/o4YBAGAKXpWAWVdNAAAqhFfxUK4756.png" class="medium-zoom-image"></center>
<p>仿真结果</p>
<center><img src="https://file.elecfans.com/web1/M00/DB/BD/o4YBAGAKXtaADtRdAACym9ojHSc474.png" class="medium-zoom-image"></center>
<p><strong>2.2.2OBUFDS</strong></p>
<p>OBUFDS：将标准单端信号转换成差分信号，输出端口需要直接对应到顶层模块的输出信号，和IBUFDS为一对互逆操作。</p>
<center><img src="https://file.elecfans.com/web1/M00/DC/3D/pIYBAGAKXyCAeDXOAAA0g3v9QxA433.png" class="medium-zoom-image"></center>
<p>源码</p>
<center><img src="https://file.elecfans.com/web1/M00/DB/BD/o4YBAGAKX2GAf861AAA1vFTR8qk322.png" class="medium-zoom-image"></center>
<p>仿真结果</p>
<center><img src="https://file.elecfans.com/web1/M00/DB/BD/o4YBAGAKX5-ADMiXAABPeElHPek083.png" class="medium-zoom-image"></center>
<p><strong>3.GOWIN高云IO实现差分信号</strong></p>
<p><strong>3.1特点</strong></p>
<p>与xilinx7系列相比Gowin高云半导体FPGA产品所有分区都支持差分输入，所有分区支持模拟LVDS差分输出，但需要使用外部<a href="http://www.elecfans.com/yuanqijian/dianzuqi/20171214603273_2.html" target="_blank" rel="noopener"><span style="text-decoration: underline">电阻</span></a>网络，模拟LVDS差分输出使用外部电阻匹配和差分LVCMOS缓存输出实现，特定分区支持真LVDS差分输出和差分输入匹配。GW1N系列FPGA产品在分区1/2/3支持真LVDS差分输出，分区0支持100欧姆输入差分匹配电阻。</p>
<p><strong>3.2实现</strong></p>
<p>GowinFPGA通过TLVDS_IBUF和ELVDS_IBUF（真差分/模拟差分输入缓冲器）、TLVDS_OBUF和ELVDS_OBUF（真差分/模拟差分输出缓冲器）等原语的调用实现差分信号。以TLVDS_IBUF和TLVDS_OBUF为例。</p>
<p><strong>3.2.1TLVDS_IBUF</strong></p>
<p>TLVDS_IBUF：真差分输入缓冲器，其中端口I和IB为差分输入信号，O为输出的单端信号。</p>
<center><img src="https://file.elecfans.com/web1/M00/DB/BD/o4YBAGAKX92AMWqjAAAi2ADdsQI673.png" class="medium-zoom-image"></center>
<p>&nbsp;</p>
<center><img src="https://file.elecfans.com/web1/M00/DC/3D/pIYBAGAKYByAYm3vAABYY_eBZ-E069.png" class="medium-zoom-image"></center>
<p>源码</p>
<center><img src="https://file.elecfans.com/web1/M00/DC/3D/pIYBAGAKYFmAHZm0AAAOC3Ve_uk152.png" class="medium-zoom-image"></center>
<p>仿真结果</p>
<center><img src="https://file.elecfans.com/web1/M00/DB/BD/o4YBAGAKYJeAL-wLAAAO5o38yRA946.png" class="medium-zoom-image"></center>
<p><strong>3.2.2TLVDS_OBUF</strong></p>
<p>TLVDS_OBUF：真差分输出缓冲器，其中端口I为单端输入信号，O和OB为输出的差分信号。</p>
<center><img src="https://file.elecfans.com/web1/M00/DB/BD/o4YBAGAKYNWARqtgAABbzGbLa7o134.png" class="medium-zoom-image"></center>
<p>源码</p>
<center><img src="https://file.elecfans.com/web1/M00/DB/BD/o4YBAGAKYROAFZRHAAAOSSgrnD8607.png" class="medium-zoom-image"></center>
<p>仿真结果</p>
<center><img src="https://file.elecfans.com/web1/M00/DC/3D/pIYBAGAKYVCAGWtiAAASh8XFnGQ351.png" class="medium-zoom-image"></center>
<p><strong>4.差分信号的应用</strong></p>
<p>差分信号的抗干扰能力强，干扰噪声一般会等值、同时的被加载到两根信号线上，噪声对信号的逻辑意义不产生影响。由于差分信号抗干扰能力强、受外界影响小，并且差分信号在传输速率上远胜于单端信号，在信号比较微弱、噪声较高的情况下有明显的优势，因此差分信号在高速数字串行接口中非常常见，比如LVDS、<a href="http://www.hqpcb.com/zhuoluye11/?tid=26&amp;plan=fashaoyou" target="_blank" rel="noopener"><span style="text-decoration: underline">Mi</span></a>ni_LVDS、<a href="http://www.elecfans.com/tags/rs/" target="_blank" rel="noopener"><span style="text-decoration: underline">RS</span></a>DS、PPDS、BLVDS、differen<span style="text-decoration: underline"><a title="TI社区" href="http://bbs.elecfans.com/zhuti_715_1.html" target="_blank" rel="noopener">TI</a></span>alHSTL、SSTL等。除此之外，差分信号能有效抑制电磁干扰（EMI）并且信号时序定位准确。</p>
</div>
