> 转载自：https://blog.csdn.net/weixin_43849505/article/details/136436575

[TOC]

## 一、M2DGR
该数据集主要针对的是地面机器人，文章正文提到，现在许多机器人在进行定位时，其视角以及移动速度与车或者无人机有着较大的差异，这一差异导致在地面机器人完成SLAM任务时并不能直接套用类似的数据集。针对这一问题该团队设计了这样的一个包含了多传感器、多场景的数据集。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/642566f35c3f4ff5a8b626081b335d82.png" alt="在这里插入图片描述" style="zoom:50%;" />

由于其主要针对的是地面机器人，所以创新点也是围绕着这里进行的。文章使用了一个自己搭建的数据采集机器人，配备了六个朝向四周的鱼眼相机、一个朝向天空的普通相机、一个红外相机、一个事件相机、一个32线激光雷达、IMU以及定位设备。

标定与同步方面。文章使用了MATLAB的标定工具箱对相机的内参进行了标定，鱼眼相机使用了Kannala Brandt model畸变模型机型标定。红外相机使用了不同比热容的标定板进行标定，这一标定方法出现在好多红外相机的标定中，这种方法实际上就是用比热容不同的材料来只做标定板的黑白格，这样在加热标定板的时候，黑白格会因为比热容不同呈现出不同的温度，从而就可以在红外相机中表现出差异，利用这种方法人工寻找黑白格的交界处，就能完成标定。外参标定也是使用了各种工具箱，使用Autoware的工具箱可以标定激光雷达和相机，相机和IMU使用了Kalibr工具箱。传感器之间没有使用硬触发，而是选择系统时间来进行软同步。

由于其针对的是地面机器人，所以设计的录制场景同时包含了室内、室外以及室内外交替三类。室外场景选择了RTK定位来获得真值，室内的真值则是使用动捕系统。对于室内外交替的场景，真值是RTK与动补相结合的，个人推测采集机器人上面安装的朝天的相机就是为了判断是在室内还是室外。

在验证实验部分，文章选择了A-LOAM、LeGo-LOAM、lINS、LIO-SAM、ORBSLAM3、VINS-MONO进行了验证。在一些数据集上表现和真值偏差还是很大的。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/b5b8b15dcbe7405db9dcf8721ec8484a.png" alt="在这里插入图片描述" style="zoom:50%;" />

## 二、UrbanLoco
该数据集针对的是无人驾驶数据集缺少人口密集的城市场景这一问题，现有的数据集比如KITTI、Waymo等数据集，并不是在那种大都市场景下进行的录制，在这类场景下高楼大厦会对GNSS信号产生影响，同时诸如汽车、行人等动态物体也会增多，这对于无人驾驶的定位来说是一个较大的挑战。所以作者提出了这样的一个大规模的城市定位数据集，不仅包含了多样的场景以及动态物体，还使用了多传感器进行录制，为了衡量场景的复杂程度，作者还提出了一个城市化比例的概念。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/dada3ae791884cd7878cc6195cbcb29f.png" alt="在这里插入图片描述" style="zoom:50%;" />

实验采集车使用的是一辆改装过的丰田飞度，在车顶加了一个架子用来放置传感器，使用的传感器包括一个激光雷达、相机、鱼眼相机、IMU以及GNSS设备，录制地点是香港和旧金山的街道。为了获得轨迹真值作者选用的SPAN-CPT来融合IMU和RTK来完成真值。这里作者提到在鱼眼相机仅用在了香港的数据采集，并且这一相机是朝天指向的，这样做是为了去评估卫星的可见程度，从而改善GNSS的定位结果。

标定方面论文没有介绍太多，只写了用Kalibr工具箱来进行相机内参的标定，外参则是使用Autoware工具箱进行的。

验证实验部分，作者选用了视觉、激光两类SLAM框架进行验证，选择了LOAM、NDT、VINS-MONO进行测试。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/d2046a573eff4d55a4255535b3a01a55.png" alt="在这里插入图片描述" style="zoom:50%;" />

## 三、NCLT
该数据集主要针对的是地面机器人的导航，作者使用了一台类似于平衡车的采集机器人，在密歇根大学北校区进行的录制。数据集采用了相同位置不同路径多次录制的方法，在空间与时间上都实现了大规模的录制，提供相机和激光雷达的数据，由于载具是采集机器人，所以提供了室内和室外两类场景，包含了大量的场景变化（动态物体、季节变化、一天内的时间变化）。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/a8dace0a5c1e4f1eb6cb857039e36277.png" alt="在这里插入图片描述" style="zoom:50%;" />

传感器配置方面，采集机器人配置了一款Velodyne HDL-32E 32线激光雷达，以及一款Pointgrey Ladybug3全景相机，从介绍来看这款全景相机是内部由多个相机拼成的最后根据重叠位置导出了一张全景图。此外该机器人还配备了两款Hokuyo的激光测距仪，一个用来感知长距离范围，另一个用来感知近距离范围。为了记录导航信息，该机器人还配置了IMU、陀螺仪以及定位用的GPS，同时采用了RTK基站来进行辅助定位。作者特别提到，在使用了RTK基站之后，他们的定位效果得到了特别大的提升，所以他们的真值是特别准确的。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/db0c7198574840fd9607737bc4c3ae0c.png" alt="在这里插入图片描述" style="zoom:50%;" />

标定的过程，作者写的很细但是没太看懂，激光雷达和自身的标定采用了一种类SLAM问题的方法来解决，通过最小化一个量来优化出相对关系。激光雷达和全景相机使用的是另一篇文章中的方法。全景相机内参是直接用的厂家信息。其它的参数使用的人工标定。

真值方面，室外必然是使用的RTK，作者为了真值更加准确，使用了激光雷达配准与RTK相结合的方法来提供位姿真值，在室内这类RTK信号不好的位置，作者貌似是记录了RTK的校准值，直接固定这个值加入到点云配准中，以此修正室内情况下的真值。

作者并没有进行验证实验。

## 四、MVSEC
是一个主要针对与事件相机的数据集，事件相机在感知动态物体方面有着得天独厚的优势，这一优势使得其十分适用于应对无人驾驶场景中诸如车辆、行人等动态物体。基于这一点作者提出了首个双目事件相机的同步事件流数据集，带有准确的深度和位姿真值，同时还包括了多种传感器和多种采集设备。

不同于前面几个数据集使用机器人或者实车进行数据采集，这篇文章制作了一个传感器套件，分别将这个套件放置在了无人机、汽车、摩托车上进行录制，甚至还采用了手持录制的方法。提供了丰富的载具类型。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/aae4dfa3d24145a093162b4da24b2c6b.png" alt="在这里插入图片描述" style="zoom:50%;" />

数据集采用ROSBAG的形式，使用了两台DAVIS m346B事件相机，一台视觉惯性传感器（VI-Sensor），一台Velodyne Puck LITE 16线激光雷达以及定位用的GPS设备。为了保证传感器之间的时间同步，作者用了触发信号。这里没太搞明白这个触发，按道理事件相机是不存在按时触发这个东西的，因为事件相机的输出是事件流，只要可视范围内有像素动了，就会直接输出像素位置以及变化信息，所以这个输出是不间断的，这也是为啥事件相机的输出叫事件流而不叫什么事件图之类的。

真值方面，作者选择了使用动捕设备来获取位姿，对于室外场景同时还包括了GPS信息，此外作者还提到使用了激光SLAM算法来计算真值，使用LOAM、Cartographer来完成激光定位，前者计算位姿后者检测回环，作者还将算出来的位置和GPS测量对齐在一块，发现这个偏差不大以此来证明这个方法是可行的。前面提到了这个数据集提供了一个深度真值，这里的真值其实是相机可视范围内部的像素的深度信息，作者选择用LOAM先建图，以此得到一个稠密的3D点云地图，之后将其投影到相机坐标系下，对齐每个像素进而得到深度信息。

标定方面，作者使用了各种工具箱来测量相对位置关系。采用人工标定与工具箱标定相结合的方法。由于这个数据集主要卖点是他们的事件相机，现在已有的开源事件相机数据集不是很多，所以并没有进行对比实验。

## 五、Brno Urban Dataset
这个数据集主要是针对于一个无人驾驶场景下的城市数据集，包括了各种复杂的场景，搭配了比较先进的传感器，使用RTK进行了精确定位，同时还设计了十分完善的时间同步系统。数据集包括了375km的录制，加起来一共有将近十小时。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/2e94e46a4587498f877cca48d6a47ef4.png" alt="在这里插入图片描述" style="zoom:50%;" />

作者选择在车顶布置了一个叫阿特拉斯（ATLAS）的采集平台，实际上就是焊了一个铁架子，这个架子被放置在车顶上，从而实现数据的采集。传感器并没有特别的花样，使用了四台DFK33-GX174相机，一台FLIR Tau2红外相机，两个Velodyne HDL-32e激光雷达，以及常规的GNSS和IMU。其中四台相机两个负责组成双目系统放置在前向位置，剩余两个放在侧边以提升视野范围。激光雷达也采用了稍微倾斜的放置方案，以此来提高感知的效果。加入热成像相机主要是为了区分物体，虽然分辨率相较于RGB相机小了很多，但是胜在其温度上的感知能力，能够比较快速地分辨出来物体信息。

标定方面，作者提供了一套工具来进行标定，但是这个采集设备的传感器没啥特别的，所以标定也是很常规的那种方案。作者特别介绍了他们的时间同步系统，采用的传感器可以分为两类：能接收到GPS信号的和不能接收到GPS信号的。前者包括了两个激光雷达、IMU和GNSS接收器，所以这三个设备直接通过GNSS信号进行同步；而四个RGB相机和红外相机则依靠GNSS接收器发出的同步信号，根据这个信号输出图像，最后实现一个全局的时间同步。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/f1fbccddc4644bcd9fc5519973c6108a.png" alt="在这里插入图片描述" style="zoom:50%;" />

## 六、SceNDD
一个主打“基于场景”的无人驾驶数据集，这个数据集传感器配置并不出众，包括一个Ouster OS-1 64线激光雷达，六个FLIR Grasshopper-3 工业摄像头，GPS和IMU。其中激光雷达居中，剩下六个相机分别放在了前后和车顶的四个角。工业摄像头的配置特别高，能提供2048×2048分辨率最高90FPS的数据。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/a3bdf2cf985743e895b9f2ab9d82f7b3.png" alt="在这里插入图片描述" style="zoom:50%;" />

作者在介绍数据集的时候七七八八讲了一大堆，简单来说就是GPS进行了矫正、点云进行了目标检测、相机暂时没用上。最后提到他们所谓的基于场景，个人理解就是他们在点云数据的基础上，通过目标检测等任务，将当前场景的车辆、建筑等内容，建模成了场景，这些场景包括了具体的驾驶行为，比如说有车交汇的时候采取的移动策略等等，所以这个数据集其实针对的，更多是一些场景下，车辆会采用什么方法进行怎样的移动，并不是传统的建图数据集之类的。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/57810758512d4994adf9a5c5652f971a.png" alt="在这里插入图片描述" style="zoom:50%;" />

## 七、DSEC
一个双目事件相机数据集，在这个数据集的论文发表的时候，还是2021年，那时候只有MVSEC这一个事件相机数据集，而且其分辨率较低，DSEC在当时也算是开创性的。这个数据集是一个大规模的双目事件相机数据集，实现了准确的事件与深度信息的对齐，并且包括了不同的光照条件。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/375e4df5801f4765a932acafdeacfa84.png" alt="在这里插入图片描述" style="zoom:50%;" />

传感器配置方面，作者也是在车顶做了一个改装顶，搭配了两个Prophesee Gen3.1事件相机，两个FLIR Blackfly USB3相机，一个Velodyne VLP-16激光雷达以及常用的GNSS和IMU。其中事件相机具有640×480的分辨率，前向放置并保证视差为60cm。里侧同时放置了两个RGB相机，具有1440×1080的高分辨率，并保证视差为51cm。相机设置了自动曝光，频率为20Hz。为了降低噪声，事件相机在白天时设置了较大的敏感度，夜间则相反。

数据集一共录制了4T的原始数据，囊括了城市、郊区的黑夜和白天时间段。

该数据集同样使用了以GPS为核心的时间同步策略，GNSS设备在收到信号时会向其他设备发送信号，最终实现近似20Hz的频率。标定的过程依旧是老样子，区别在于他们标定事件相机的方法，从正文叙述来看，他们采用的是根据事件流重建RGB图像的方法，之后标定这个图像来计算外参。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/1577e1ecb4e841febe8f9f75a8f5ffd6.png" alt="在这里插入图片描述" style="zoom:50%;" />

真值方面，作者貌似是使用算法计算的位姿。重点在于，作者对事件相机进行了对齐操作，利用激光雷达的数据，作者进行了建图，并将构建的地图投影到2d上，以此获得事件相机每个位置的深度信息。所以对这个数据集来说，主要的点在于双目事件以及事件对应位置的像素深度。

由于事件相机当时还没有开源的算法，所以算法并没有进行定位的实验。

## 八、KAIST
这个数据集是韩国科学技术院的一篇顶刊论文，针对的是不同光照情况下的图像数据。作者使用了很复杂的方法，让红外图像和RGB图像进行了对齐，提供了完全对齐的红外图和RGB图像。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/f7c27aedf97a41dcb8559df2e6b9abdb.png" alt="在这里插入图片描述" style="zoom:50%;" />

作者使用有格子的快拆板组装了一个顶棚，放在了一辆SUV的上面，快拆板内部的网格减轻了标定的困难。传感器方面，作者使用了两个PointGrey Flea3相机，一个FLIP A655Sc红外相机，一个Velodyne32线激光雷达以及IMU和定位装置。由于主要卖点是红外相机，作者使用的这个红外相机的规格貌似特别厉害，属于是夜视仪级别的红外相机，他们还在顶棚里面放了一个滤波器，以此来减小红外相机接收到的噪声。

作者选择了在同一位置分别在白天和黑夜五个时间段进行录制的策略，提供了高质量的图像数据。GPS信息为十分丰富的，一共提供了19项不同的内容。激光雷达主要是为了计算深度，通过重建并投影，以此来生成深度图。不仅有位姿等信息，作者还提供了物体信息的标注，用于目标检测等任务。

标定方面没有什么太多可介绍的，有意思的是一种标定红外相机的方法，作者提出了一种“基于线的标定板”，具体来说就是在张正友标定板上，用铜线来把所有的线给走一遍，这样在使用红外相机拍摄时，铜线的位置就会特别明显，从而更好地提取角点进行标定。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/8c8205e47c7f433ba30d422831aaf33a.png" alt="在这里插入图片描述" style="zoom:50%;" />

## 九、Weather and Light Level Classification for Autonomous Driving:Dataset, Baseline and Active Learning
一篇2021年ITSC的文章，主要针对的是无人驾驶场景下天气、光照的分类，作者提出了一个天气预测的模型并附带了一个数据集。由于模型是主要的工作，所以数据集并没有介绍太多。作者才用了一个前向放置的工业摄像头，录制了1024×1083分辨率的RCCC格式的视频，该格式能够提供更高标准的图像数据。采集包括了不同天气状况下白天、黄昏以及夜晚的图像信息，用于训练他们的主动学习模型。

## 十、LaFiDa
一个室内外场景的建图数据集，作者设计了一个头戴设备，包括了两个VRmagic VrmC-12鱼眼相机、一个Hokuyo UTM-39LX-EW激光测距仪。其中激光测距仪前向放置并向下倾斜，主要针对地面进行采集，通过USB3.0与工控机连接。鱼眼相机则布置在两侧，通过硬触发实现时间同步。真值的获取则是依赖于动捕系统。数据的录制在作者搭建的环境下进行，并不是那种大范围的录制，提供的数据片段也不多。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/53a5397146ab4864b783955e4ff60bd5.png" alt="在这里插入图片描述" style="zoom:50%;" />

## 十一、OpenLORIS-Scene
一个针对于服务机器人的数据集，因为服务机器人的使用场景是大部分长期相同同时伴随有小部分变化的，所以作者特地针对于这一点做了补充，包括了视角、物体、光照、动态物体和传感器退化的场景。数据集使用了地面机器人进行采集，搭载了一个RealSense D435j深度相机一个RealSense T256双目鱼眼相机以及IMU，其中IMU与图像之间使用硬触发来保证时间同步。同时，数据集还提供了轮速计信息。

真值方面，作者使用了OptiTrack动捕系统和一些激光雷达，负责建图和轨迹的真值。

作者进行了实验验证，选取了ORBSLAM2、DS-SLAM、VINS-MONO、InfiniTAM和ElasticFusion验证轨迹的准确性。

## 十二、PanoraMIS
一个全景相机数据集，提供了大视野范围的图像，包括室内外、人造与天然场景，并进行了准确的时间同步。一个很特别的地方在于，作者采用的采集设备加入了一个倒置的器件，通过反射来进行图像的采集。

采集过程配备了双目相机，分别在工业机械臂、轮式机器人以及无人机上进行了采集。数据包括了八个片段，四个室内四个室外。同样没有进行实验验证。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/994b8a51a0f74d3ab7d0ba4d97ff1852.png" alt="在这里插入图片描述" style="zoom:50%;" />

## 十三、SubT-MRS
一个针对于天气变化的数据集，包括了多种的天气情况，同时作者提出了一个衡量算法鲁棒性的指标，倡导更多的研究人员不要卷精度，而是对算法的稳定性进行研究。采集设备包括了四个Leopard鱼眼相机、一个Velodyne十六线激光雷达、一个FLIR Boson红外相机以及IMU。时间同步方面作者使用了一个PPS设备，IMU、激光雷达和红外相机使用CPU时钟进行时间同步，四个鱼眼相机则通过FPGA板载进行同步。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/bf31b2bbfd6441978d466a2cbc39db30.png" alt="在这里插入图片描述" style="zoom:50%;" />

作者在视觉退化、几何退化以及混合退化场景下进行了录制，甚至还包括了多机系统。数据集使用FARO Focus 3D S120 3D扫描仪进行地图真值的建立，用建好的图进行位姿估计以此作为轨迹的真值。

有关作者提出的鲁棒性指标，个人感觉更像是一个百分比制的RMSE，其实就是统计了偏差小于阈值的位置的比例。

<img src="https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/746015278cb3463a963354f485ff13e5.png" alt="在这里插入图片描述" style="zoom:50%;" />

## 思维导图

![在这里插入图片描述](https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/5461633c0a264a10a6f922e1579712f3.png)