### Fusing Trajectories and RS Images to Recognize the Fine Structure of Overpass

本页面为`融合遥感影像与车辆轨迹数据的立交桥精细结构识别`项目主页。

项目可见[Github仓库-TZHSW](https://github.com/TZHSW/roadtest)或[Github仓库-Andytonglove](https://github.com/Andytonglove/roadtest)，感谢支持。😃

### 简介与计划

>近年来，随着智慧城市、三维实景中国建设的逐步推进，城市路网交通信息的精确获取与更新也变得愈发重要。然而，过去传统的道路信息提取研究往往局限于单一类型的数据源，对于道路结构的推断方法也存在着一定的局限性，在处理以立交桥为代表的这类具有复杂拓扑结构的立体交叉道口区域时效果欠佳。

本研究项目即是希望旨在创新性地采用多源融合的方式，同时使用遥感影像与轨迹数据，通过构建深度学习网络来识别立交桥这类复杂的交通区域。我们希望能通过研究基于深度学习的多元融合的立交桥精细结构识别提取，为智慧城市路网信息系统与三维实景中国建设中复杂交通道口这一疑难问题贡献新的解决方案，提升城市路网构建的全面性、精确性与可扩展性。

本项目首先了解了过往的各类研究，随后针对本项目数据进行了项目数据源的获取与预处理过程，紧接着研究了神经网络进行路网信息提取的原理、特点以及目前前瞻性的模型与技术，然后针对本项目组所使用的神经网络的设计、模型、结构与创新点需求进行了设计与测试实践。


### 数据源

本项目所使用的数据源主要包括遥感影像与车辆轨迹数据，其中遥感影像数据来源于华为提供，车辆轨迹数据同样来源于华为提供的对应的的轨迹数据服务。

此处略。组织方式如下：
``` bash
    ├── data_name
    │   ├── line
    │   │   ├── line_data……
    │   ├── polygon
    │   │   ├── polygon_data……
    │   ├── raster
    │   │   ├── data.tfw
    │   │   ├── data.tif
```


### 项目计划

1. 初步实验：   
    - 原始计划：
    采用`U-Net`/`D-LinkNet`模型进行路网信息提取，使用`CGAN`模式进行训练，使用`Dice Loss`作为损失函数，使用`Adam`作为优化器，使用`Tensorboard`进行可视化。

    - 整体架构：![overall](https://raw.githubusercontent.com/Andytonglove/roadtest/master/images/overall.png)

    - 初步尝试：![first](https://raw.githubusercontent.com/Andytonglove/roadtest/master/images/first.png)


2. 神经网络设计
    - 总体结构：参考了了`U-Net`与`D-LinkNet`的结构，同时在以`CGAN`模式为基础进行了改进，使得网络能够更好地提取路网信息。
    ![all-structure](https://raw.githubusercontent.com/Andytonglove/roadtest/master/images/all-structure.png)


    - 生成器：
    ![discriminator](https://raw.githubusercontent.com/Andytonglove/roadtest/master/images/discriminator.png)

    - 判别器：
    ![Generator](https://raw.githubusercontent.com/Andytonglove/roadtest/master/images/Generator.png)
    结构设计旨在使得判别器能够更好地判断生成器生成的图像与真实图像的差异性，从而使得生成器能够更好地学习到路网信息；可以有效解决生成图像存在`道路断裂`时的判别错误的问题。

    - 损失函数：
    ![fx](https://raw.githubusercontent.com/Andytonglove/roadtest/master/images/fx.png)


3. 轨迹数据处理：
    - 轨迹数据处理：借助folium将对应轨迹数据转换为图像数据，以便于神经网络进行处理。同时进行初步聚类展示在Web上，方便观察与调试。
    ![traj-vl-web](https://raw.githubusercontent.com/Andytonglove/roadtest/master/traj_process/traj-vl-web.png)
    → 进行DBSCAN聚类后
    ![traj-vl-web-color](https://raw.githubusercontent.com/Andytonglove/roadtest/master/traj_process/traj-vl-web-color.png)
    → 叠加TIFF影像显示
    ![traj-vl-web-tif](https://raw.githubusercontent.com/Andytonglove/roadtest/master/traj_process/traj-vl-web-tif.png)

    - 使用库：
        - [transbigdata](https://github.com/ni1o1/transbigdata) + geopandas
        - [folium](https://github.com/python-visualization/folium)
        - [KMeans](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html)


4. 遥感影像处理：
    - 遥感影像处理：对于神经网络提取路网信息的研究，遥感影像数据的处理与预处理是非常重要的一环，这里采用ArcGIS与原始数据对照进行初步shp与其他格式处理。

    - 进行标注：使用ArcGIS进行标注，将标注的shp文件转换为tif文件，以便于神经网络进行处理。
        > 1、首先构建道路边线标注，标注立交桥涉及的主要道路，使用双线标注（包括匝道）；2、建立拓扑文件，选择边线要素没有悬挂点的规则，验证拓扑消除悬挂点；3、使用要素转面工具；4、使用编辑工具栏中的联合工具来合并面，主要区分道路的主次和行驶方向，保留完整的路面；5、为面要素集添加新字段type，为每条道路填写type；6、使用面转栅格工具，将值字段设为type，像元大小调整为底图的像元大小；7、为nodata部分赋值为0；8、导出栅格影像；


5. 发展展望：
    - [Sat2Graph: Road Graph Extraction through Graph-Tensor Encoding](https://github.com/songtaohe/Sat2Graph)

    - Fusing Taxi Trajectories and RS Images to Build Road Map via DCNN

    - [D-LinkNet: Linknet with pretrained encoder and dilated convolution for high resolution satellite imagery road extraction](https://github.com/zlckanata/DeepGlobe-Road-Extraction-Challenge)

    - 逐步从单源数据融合到多源数据融合，从而使得路网信息提取更加精确；逐步从普通道路提取转向立交桥结构针对性提取。


### 参考
- 陆川伟, 孙群, 陈冰,等. 车辆轨迹数据的道路学习提取法[J]. 测绘学报, 2020, 49(6):11.

- ZHOU L，ZHANG C，WU M．D-LinkNet: Linknet with pretrained encoder and dilated convolution for high resolution satellite imagery road extraction［A］． IEEE / CVF Conference on Computer Vision & Pattern Recognition Workshops［C］． Salt Lake City: IEEE，2018:182－186．

- Yali Li,Longgang Xiang,Caili Zhang,Huayi Wu. Fusing Taxi Trajectories and RS Images to Build Road Map via DCNN.[J]. IEEE Access,2019,7:

- He S , Bastani F , Jagwani S , et al. Sat2Graph: Road Graph Extraction through Graph-Tensor Encoding[J]. 2020.

- …… 


### 其他
- 本项目供学习交流使用。