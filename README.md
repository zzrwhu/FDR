# Face Detection and Recognition

Update : 2018 - 05 -15

## Prerequests

* 第三方程序

  * MySQL Server

* 第三方库

  * cv2(python-opencv)
  * dlib
  * numpy
  * face_recognition
  * PyQt5
  * mysqlclient
  * wordcloud
  * jieba
  * weibo
  * requests

## 原理说明

将数据库中的图片通过已经训练好的神经网络,计算出每张图片中人脸的128个特征值的向量.不同人的图片分别保存在不同的文件夹下,经过转化的特征向量保存在数据库中.

在实时的人脸检测中,程序只需一次性将数据库中所有向量一次性载入内存,保存成一个(ID:向量组)字典.对于获取到的图像,通过face detection检测出人脸位置,并同样得到该未知人脸的128维向量,将该向量与库中已有向量进行相似度计算(这里采用欧式距离).设置距离阈值,若所有距离均大于阈值,则认为是陌生人,否则给出距离最小的向量对应的姓名.

## 优点

* 所需数据量很小,测试时每个人一张照片也能进行识别,十张即可达到很高的准确率.
* 有具体数值度量相似度.
* 可将陌生人实时动态地加入数据库,只需现场采集照片,并一次性计算出特征向量,保存在数据库中即可.
* 若识别错误,可以动态进行修正,只需现场采集照片,更新数据库即可.同理也可以动态删除.

## 数据库结构

运行程序之前, 您的MySQL数据库必须满足制定的结构. 其初始化可以通过项目中的init.sql脚本完成, 具体结构为:

* DATABASE FDR
  * TABLE Persons
    * person_ID
    * name
    * last_meet_time
  * TABLE Vectors
    * vector_ID
    * person_ID
    * vector
  * TABLE Meets
    * meet_ID
    * meet_time
    * meet_place
    * person_ID

## GUI 使用说明

**本次更新提供了图形化界面供用户使用**, 使用说明如下:

* 界面左侧为实时检测识别窗口, 右侧为管理面板.
* 管理面板中,上方为检测识别结果显示区域, 中间为录入新数据区域, 下方为控制按钮.
* 进入GUI后, 先点击Start按钮开启摄像头, 并进行实时识别, 也可以点击Stop按钮暂停, Exit按钮则退出GUI.
* 实时检测识别的过程中, 检测到的最匹配人脸的库中照片, 姓名, 及距离, 会显示在右上方.
* 若要录入新的数据, 请在文本框输入姓名(英文), 并点击Enter New Data按钮. 程序会自动根据实时检测窗口的录像进行截图录入, 并将录入头像显示在文本框上方.

**需要注意的问题** :

* GUI暂时只能一次录入一张图片, 若要一次录入多张图片并且多次点击Enter New Data即可.
* 录入新数据并不要求名字原来不存在, 也可以向原来已有的人的库中加入新数据.
* 录入新数据后, 需要退出GUI, 重新启动才能利用新数据进行识别.

## 后续要做的扩展

* 将通过knn等机器学习分类器进一步提高准确率.

## 样例

样例见 example.py, 其中包括命令行和GUI启动.
