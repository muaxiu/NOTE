# # 用Python简单处理图片

## 一、图片的打开、显示和保存

```
from PIL import Image
import matplotlib.pyplot as plt
img=Image.open('d:/dog.png')
img.show()#这种图片显示方式是调用操作系统自带的图片浏览器来打开图片，下面的是让程序绘制图片：
plt.figure("dog")
plt.imshow(img)
plt.show()
plt.axis('off')#figure默认是带axis的，如果没有需要，我们可以关掉
print img.size  #图片的尺寸
print img.mode  #图片的模式
print img.format  #图片的格式
img.save('d:/dog.jpg')#不仅能保存图片，还能转换格式
```
推荐使用这种方法，它使用一个matplotlib的库来绘制图片进行显示。matplotlib是一个专业绘图的库，相当于matlab中的plot,可以设置多个figure,设置figure的标题，甚至可以使用subplot在一个figure中显示多张图片。

##二、图像通道


```
from PIL import Image
import matplotlib.pyplot as plt
img=Image.open('d:/ex.jpg')  #打开图像
gray=img.convert('L')   #转换成灰度
#plt.imshow(gray,cmap='gray')
r,g,b=img.split()   #分离三通道
pic=Image.merge('RGB',(r,g,b)) #合并三通道
plt.figure("beauty")
plt.subplot(2,3,1), plt.title('origin')
plt.imshow(img),plt.axis('off')
plt.subplot(2,3,2), plt.title('gray')
plt.imshow(gray,cmap='gray'),plt.axis('off')
plt.subplot(2,3,3), plt.title('merge')
plt.imshow(pic),plt.axis('off')
plt.subplot(2,3,4), plt.title('r')
plt.imshow(r,cmap='gray'),plt.axis('off')
plt.subplot(2,3,5), plt.title('g')
plt.imshow(g,cmap='gray'),plt.axis('off')
plt.subplot(2,3,6), plt.title('b')
plt.imshow(b,cmap='gray'),plt.axis('off')
plt.show()
```

使用函数convert()来进行转换，它是图像实例对象的一个方法，接受一个 mode 参数，用以指定一种色彩模式，mode 的取值可以是如下几种：

· 1 (1-bit pixels, black and white, stored with one pixel per byte)
· L (8-bit pixels, black and white)
· P (8-bit pixels, mapped to any other mode using a colour palette)
· RGB (3x8-bit pixels, true colour)
· RGBA (4x8-bit pixels, true colour with transparency mask)
· CMYK (4x8-bit pixels, colour separation)
· YCbCr (3x8-bit pixels, colour video format)
· I (32-bit signed integer pixels)
· F (32-bit floating point pixels)
![](/assets/140867-20160103140351917-83215038.png)


```
from PIL import Image
import matplotlib.pyplot as plt
img=Image.open('d:/ex.jpg')  #打开图像
plt.figure("beauty")
plt.subplot(1,2,1), plt.title('origin')
plt.imshow(img),plt.axis('off')

box=(80,100,260,300)
roi=img.crop(box)
plt.subplot(1,2,2), plt.title('roi')
plt.imshow(roi),plt.axis('off')
plt.show()
```

从原图片中裁剪感兴趣区域（roi),裁剪区域由4-tuple决定，该tuple中信息为(left, upper, right, lower)。 Pillow左边系统的原点（0，0）为图片的左上角。坐标中的数字单位为像素点。
用plot绘制显示出图片后，将鼠标移动到图片上，会在右下角出现当前点的坐标，以及像素值。

几何变换


```
#图像的缩放和旋转
dst = img.resize((128, 128))
dst = img.rotate(45) # 顺时针角度表示
#转换图像
dst = im.transpose(Image.FLIP_LEFT_RIGHT) #左右互换
dst = im.transpose(Image.FLIP_TOP_BOTTOM) #上下互换
dst = im.transpose(Image.ROTATE_90)  #顺时针旋转
#transpose()和rotate()没有性能差别。
```

##三、图像中的像素访问


```
from PIL import Image
import numpy as np
import matplotlib.pyplot as plt
#只要是在python中进行数字图像处理，我们都需要导入以上包
img=np.array(Image.open('d:/lena.jpg'))  
#打开图像并转化为数字矩阵
plt.figure("dog")
plt.imshow(img)
plt.axis('off')
plt.show()
#查看图片信息
print img.shape  
print img.dtype 
print img.size 
print type(img)
```
如果是RGB图片，那么转换为array之后，就变成了一个rows*cols*channels的三维矩阵,因此，我们可以使用img[i,j,k]来访问像素值。
例1：打开图片，并随机添加一些椒盐噪声


```
from PIL import Image
import numpy as np
import matplotlib.pyplot as plt
img=np.array(Image.open('d:/ex.jpg'))

#随机生成5000个椒盐
rows,cols,dims=img.shape
for i in range(5000):
    x=np.random.randint(0,rows)
    y=np.random.randint(0,cols)
    img[x,y,:]=255
    
plt.figure("beauty")
plt.imshow(img)
plt.axis('off')
plt.show()

```

例2：将lena图像二值化，像素值大于128的变为1，否则变为0

```
from PIL import Image
import numpy as np
import matplotlib.pyplot as plt
img=np.array(Image.open('d:/pic/lena.jpg').convert('L'))

rows,cols=img.shape
for i in range(rows):
    for j in range(cols):
        if (img[i,j]<=128):
            img[i,j]=0
        else:
            img[i,j]=1
            
plt.figure("lena")
plt.imshow(img,cmap='gray')
plt.axis('off')
plt.show()

```

如果要对多个像素点进行操作，可以使用数组切片方式访问。切片方式返回的是以指定间隔下标访问 该数组的像素值。下面是有关灰度图像的一些例子：


```
img[i,:] = im[j,:] # 将第 j 行的数值赋值给第 i 行
img[:,i] = 100 # 将第 i 列的所有数值设为 100
img[:100,:50].sum() # 计算前 100 行、前 50 列所有数值的和
img[50:100,50:100] # 50~100 行，50~100 列（不包括第 100 行和第 100 列）
img[i].mean() # 第 i 行所有数值的平均值
img[:,-1] # 最后一列
img[-2,:] (or im[-2]) # 倒数第二行
```

##四、图像直方图


```
vec=np.arange(15)#生成一维数组[0-14]
print vec
mat= vec.reshape(3,5)#变成一个3*5二维矩阵
print mat

```

mat显示为
[[ 0  1  2  3  4]
 [ 5  6  7  8  9]
 [10 11 12 13 14]]
返过来，知道一个二维矩阵，要变成一个一维数组，就不能用reshape了，只能用flatten. 我们来看两者的区别

```
a1=mat.reshape(1,-1)  #-1表示为任意，让系统自动计算
print a1
a2=mat.flatten()
print a2
```

a1: [[ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14]]
a2: [ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14]
对图像求直方图，就需要先把图像矩阵进行flatten操作，使之变为一维数组，然后再进行统计。

###灰度直方图

```
from PIL import Image
import numpy as np
import matplotlib.pyplot as plt
img=np.array(Image.open('d:/pic/lena.jpg').convert('L'))

plt.figure("lena")
arr=img.flatten()
n, bins, patches = plt.hist(arr, bins=256, normed=1, facecolor='green', alpha=0.75)  
plt.show()

```

hist的参数非常多，常用就这五个，只有第一个是必须的，后面四个可选
arr: 需要计算直方图的一维数组
bins: 直方图的柱数，可选项，默认为10
normed: 是否将得到的直方图向量归一化。默认为0
facecolor: 直方图颜色
alpha: 透明度

返回值 ：
n: 直方图向量，是否归一化由参数设定
bins: 返回各个bin的区间范围
patches: 返回每个bin里面包含的数据，是一个list

###彩色图片直方图


```
from PIL import Image
import numpy as np
import matplotlib.pyplot as plt
src=Image.open('d:/ex.jpg')
r,g,b=src.split()

plt.figure("lena")
ar=np.array(r).flatten()
plt.hist(ar, bins=256, normed=1,facecolor='r',edgecolor='r',hold=1)
ag=np.array(g).flatten()
plt.hist(ag, bins=256, normed=1, facecolor='g',edgecolor='g',hold=1)
ab=np.array(b).flatten()
plt.hist(ab, bins=256, normed=1, facecolor='b',edgecolor='b')
plt.show()
```

![](/assets/140867-20160103172422776-1019770553.png)


##五、添加水印


```
#文字水印
from PIL import Image, ImageDraw,ImageFont
im = Image.open("d:/pic/lena.jpg").convert('RGBA')
txt=Image.new('RGBA', im.size, (0,0,0,0))
fnt=ImageFont.truetype("c:/Windows/fonts/Tahoma.ttf", 20)
d=ImageDraw.Draw(txt)
d.text((txt.size[0]-80,txt.size[1]-30), "cnBlogs",font=fnt, fill=(255,255,255,255))
out=Image.alpha_composite(im, txt)
out.show()
```


```
#添加小图片水印
from PIL import Image
im = Image.open("d:/pic/lena.jpg")
mark=Image.open("d:/logo_small.gif")
layer=Image.new('RGBA', im.size, (0,0,0,0))
layer.paste(mark, (im.size[0]-150,im.size[1]-60))
out=Image.composite(layer,im,layer)
out.show()

```
























