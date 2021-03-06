# 自动对焦（Auto Focus，AF）

# 原理
1. 感受：相机的光电传感器接受物体反射的光
    + 分类
        - 机身对被摄体发射红外线、超声波或激光，然后接受反射回波，进行测距，然后对于镜头对焦进行干预。
            - 优点：可以用于细线条物体和动体，也可以在低反差，弱光下对焦。
        - 光学系统接受被摄体自身的反光以进行检测
            - 优点：不需发射系统，耗能小。可以在逆光下或透过玻璃对焦
            - 策略：
                1. 相位检测对焦（Phase Detection Auto Focus，PDAF）
                2. 对比度检测对焦（Contrast Detection Auto Focus，CDAF）
2. 处理：根据相机内部芯片的计算与处理
3. 对焦：最后由电动对焦装置对焦

##　相位检测对焦（Phase Detection Auto Focus，PDAF）
将进入镜头的光线投射到相位检测传感器上，传感器对镜头径向两方的光线进行对比。
相位对焦结构在单反相机上的实现通常需要**分光镜**以分离光束，通常是位于半透明的主反光镜（Reflex Mirror）后的一片副反光镜（Sub Mirror）将光线投射到位于相机机身底部的AF传感器上
##　对比度检测对焦（Contrast Detection Auto Focus，CDAF）
通过在图像中对焦主体对比度信息的调整过程中，检出最高对比度所在，即为判定合焦。
- 优点:对比度对焦通常并不需要分光设备，所以布置与操作更加灵活
![概览](../../../imgs/AF图示.jpg)