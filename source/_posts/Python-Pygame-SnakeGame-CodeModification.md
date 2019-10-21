---
title: Pygame 贪吃蛇小游戏 代码修改(Pygame SnakeGame Code Modification)
date: 2017-06-07 20:12:57
tags:
- Python
categories:
- Python
---
昨日，一朋友拜托我看一份Python（Pygame）代码（似乎是实验还是课设？）
然后帮忙修改一下，增加点小功能
下载后发现是基于Pygame的SnakeGame，也就是贪吃蛇游戏
代码不长，难度也不是很高（主要是我对Pygame不熟悉）
临时学习了Pygame之后倒也成功修改了代码，下文详讲对代码的分析和修改过程

PS：感兴趣可以下载，琢磨琢磨（发现还是挺有趣的）
[修改前、后 源代码(点击下载)](http://pan.baidu.com/s/1gfwySO7)

【本文仅为学习目的，如有侵权，请联系我删除】
<!-- more -->

# 先解压查看文件
（这里为修改后的文件夹截图，方便对比）如下
{% asset_img 1.png 文件列表 %}
其中

文件名               |作用
---------------------|------------------------------------------------------------
修改前               |
1-16.png             |为原程序自带的图案（用于贪吃蛇Snake本身和需要吃的食物Food）
BGM.ogg              |为原程序自带的背景音乐
face.png             |为原程序自带的开始背景图案
face.psd             |为face.png的PS原工程文件
MyLibrary.py         |为主程序掉调用的自定义lib库代码
SnakeGame.py         |为主程序代码（因此只修改此文件即可）
修改后               |   
17.png               |为修改后新增的障碍物图案
SnakeGame.py         |修改后的主程序代码

# 代码分析
注：
这里只挑比较重要的部分代码（需要修改的、有关的）做分析
需要全部源代码请从上面的链接下载

## MyLibrary.py
基于pygame.sprite.Sprite类，自定义了MySprite和Point类
初始化、定义了一些需要使用的参数和函数

注：
在pygame.sprite模块里面包含了一个名为Sprite类，是pygame本身自带的一个精灵类
Sprite类可以实现加载动态图片（精灵序列图）、更新帧、绘制帧、精灵组等功能
但是这个类的功能比较少，因此MyLibrary.py中新建了MySprite类对其继承
在sprite类的基础上丰富功能，以供SnakeGame.py调用

{% codeblock MyLibrary.py lang:Python %}
# MySprite class extends pygame.sprite.Sprite
class MySprite(pygame.sprite.Sprite):
    def __init__(self):
        pygame.sprite.Sprite.__init__(self) #extend the base Sprite class
        self.master_image = None
        self.frame = 0
        self.old_frame = -1
        self.frame_width = 1
        self.frame_height = 1
        self.first_frame = 0
        self.last_frame = 0
        self.columns = 1
        self.last_time = 0
        self.direction = 0
        self.velocity = Point(0.0,0.0) 
    ……
#Point class
class Point(object):
    def __init__(self, x, y):
        self.__x = x
        self.__y = y
    ……
{% endcodeblock %}

## SnakeGame.py
注：食物Food和贪吃蛇Snake在游戏中的图片为1-16.png中的随机一张或多张组成

### Food类
即随机在地图上出现的可以由贪吃蛇Snake吃掉（增加身体长度）的食物
{% codeblock SnakeGame.py lang:Python %}
class Food(MySprite):
    global img_size,food_image      #全局变量
    def __init__(self):             #初始化
        MySprite.__init__(self)		
        image = create_pic()        #随机获取图像（每个食物在地图上显示为一张图片）
        self.set_image(image)       #设置食物图像
        MySprite.update(self, 0, 30)
        self.X = random.randint(0,23) * img_size  #随机Food食物的X坐标
        self.Y = random.randint(0,17) * img_size  #随机Food食物的Y坐标
{% endcodeblock %}
注：
默认地图大小长为23，宽为17，所以随机数值区间与此挂钩
因为从点“映射”到图像有缩放的比例，即img_size，所以随机出来的数值还需要 * img_size

### SnakeSegment类
每一节贪吃蛇Snake身体的类
{% codeblock SnakeGame.py lang:Python %}
class SnakeSegment(MySprite):
    global img_size,food_image      #全局变量
    def __init__(self):             #初始化
        MySprite.__init__(self)
        image= create_pic()         #随机获取图像（每节蛇身在地图上显示为一张图片）
        self.set_image(image)       #设置蛇身图像
        MySprite.update(self, 0, 30) 
{% endcodeblock %}
注：
整条贪吃蛇Snake是由一节一节的蛇身组成的，每一节蛇身和食物一样都是一张图片
所以Food类和SnakeSegment类定义大部分类似，并且使用的都是同一组图片1-16.png.

### Snake类
贪吃蛇Snake类，初始化为3节蛇身，初始化坐标为（10，9），初始方向向左
{% codeblock SnakeGame.py lang:Python %}
class Snake():
    global img_size,food_image
    def __init__(self):
        self.velocity = Point(-1,0)      #初始化贪吃蛇方向
        self.old_time = 0
        head = SnakeSegment()            #初始化一节蛇身
        head.X = 10*img_size             #初始化位置X坐标
        head.Y = 9*img_size              #初始化位置Y坐标
        self.segments = list()           #初始化蛇身列表
        self.segments.append(head)       #将已经初始化的第一节蛇身加入列表
        self.add_segment()               #再增加一节蛇身
        self.add_segment()               #再增加一节蛇身

    def update(self,ticks):
        global step_time 
        if ticks > self.old_time + step_time:                #根据step_time调整蛇移动速度
            self.old_time = ticks
                                                             #移动蛇的身体部分
            for n in range(len(self.segments)-1, 0, -1):
                self.segments[n].X = self.segments[n-1].X
                self.segments[n].Y = self.segments[n-1].Y
                                                             #移动蛇头
            self.segments[0].X += self.velocity.x * img_size
            self.segments[0].Y += self.velocity.y * img_size

    def draw(self,surface):                                  #根据蛇身在地图上绘制出图像
        for segment in self.segments: 
            surface.blit(segment.image, (segment.X, segment.Y))
    
    def add_segment(self):                                   #增加一节蛇身
        last = len(self.segments)-1
        segment = SnakeSegment()
        start = Point(0,0)
        if self.velocity.x < 0: start.x = img_size
        elif self.velocity.x > 0: start.x = -img_size
        if self.velocity.y < 0: start.y = img_size
        elif self.velocity.y > 0: start.y = -img_size
        segment.X = self.segments[last].X + start.x
        segment.Y = self.segments[last].Y + start.y
        self.segments.append(segment)
{% endcodeblock %}

### 初始化函数game_init()
{% codeblock SnakeGame.py lang:Python %}
#游戏初始化
def game_init():
    global screen, backbuffer, font, timer, snake, food_group, img_size, img_group#全局变量
    font = pygame.font.Font(None, 30)
    timer = pygame.time.Clock()

    #使用备份缓存机制，减少资源的消耗
    backbuffer = pygame.Surface((screen.get_rect().width,screen.get_rect().height))

    #创建蛇体
    snake = Snake()

    #创建食物对象
    food_group = pygame.sprite.Group()
    food = Food()                    #使用Food()类创建food对象（即在地图上随机产生一个食物）
    food_group.add(food)             #创建食物food组（即可以同时存在多个食物）
{% endcodeblock %}

### 加载图片函数load_pic()
{% codeblock SnakeGame.py lang:Python %}
#创建备用图片列表
def load_pic():
    for i in range(1,17):                #循环读取1-17.png
        string = str(i)+".png"
        img = pygame.image.load(string).convert_alpha()
        width,height = img.get_size()    #获取大小
        img = pygame.transform.smoothscale(img,(width//7*5,height//7*5))  #缩放
        img_group.append(img)            #加入图片组，方便调用
        string = ""
{% endcodeblock %}

### 随机返回图片函数create_pic()
{% codeblock SnakeGame.py lang:Python %}
#创建随机头像
def create_pic():
    global img_group              #全局变量，即图片组
    index = random.randint(0,15)  #产生随机数，作为图片组下标
    image = img_group[index]
    return image                  #随机返回图片组中的一张照片
{% endcodeblock %}

### 加载音频函数audio_init()
{% codeblock SnakeGame.py lang:Python %}
#初始化音频功能
def audio_init():
    global bgm
    #初识pygame中的音频模块，程序中只需加载一次
    pygame.mixer.init() 
    #加载音频文件
    bgm = pygame.mixer.Sound("bgm.ogg")
{% endcodeblock %}

### 播放声音函数play_sound()
{% codeblock SnakeGame.py lang:Python %}
#定义播放声音函数
def play_sound(sound):
    channel = pygame.mixer.find_channel(True)
    channel.set_volume(0.5)
    channel.play(sound)
{% endcodeblock %}

### 主程序
{% codeblock SnakeGame.py lang:Python %}
#主程序开始   
pygame.init()               
img_size =40                #默认图片大小
img_group = list()          #初始化图片组
screen_size = 0             #pygame.FULLSCREEN 是否全屏
screen = pygame.display.set_mode((24 * img_size,18 * img_size), screen_size)  #设置屏幕选项
pygame.display.set_caption("嗷大喵贪吃蛇")    #窗口标题
face = pygame.image.load("face.png")          #游戏开始界面
load_pic()                                    #加载图片
game_init()                                   #初始化游戏
game_over = False                             #变量，记录游戏是否结束
last_time = 0
bgm = None
auto_play = False
step_time = 400
waiting =True
audio_init()                                  #加载音频
rePlay = True
play_sound(bgm)
while True:
    timer.tick(30)
    ticks = pygame.time.get_ticks()
    current_time = time.clock()
    for event in pygame.event.get():
        if event.type == QUIT:
            pygame.quit()
            sys.exit()
        elif event.type == MOUSEBUTTONUP:
            if waiting == True:
                waiting = False
                game_over = False
                last_time = 0
                auto_play = False 
                step_time = 400
                game_init()
    keys = pygame.key.get_pressed()
    if keys[K_ESCAPE]:                         #按ESC键退出
        pygame.quit()
        sys.exit()
    elif keys[K_UP] or keys[K_w]:              #按上或W使贪吃蛇的方向为上
        snake.velocity = Point(0,-1)
    elif keys[K_DOWN] or keys[K_s]:            #按下或S使贪吃蛇的方向为下
        snake.velocity = Point(0,1)
    elif keys[K_LEFT] or keys[K_a]:            #按左或A使贪吃蛇的方向为左
        snake.velocity = Point(-1,0)
    elif keys[K_RIGHT] or keys[K_d]:           #按右或D使贪吃蛇的方向为右
        snake.velocity = Point(1,0)
    elif keys[K_f]:                            #按F切换全屏
        if screen_size == pygame.FULLSCREEN:
            screen_size = 0
        else:
            screen_size =pygame.FULLSCREEN
        screen = pygame.display.set_mode((24*img_size,18*img_size),screen_size)
        pygame.display.set_caption("嗷大喵贪吃蛇")
    elif keys[K_PLUS] or keys[K_KP_PLUS]:      #按+加快游戏速度
        step_time -= 10
        if step_time <50:
            step_time=50
    elif keys[K_MINUS] or keys[K_KP_MINUS]:    #按-减慢游戏速度
        step_time += 10
        if step_time >400:
            step_time = 400
    elif keys[K_SPACE]:                        #按空格键自动寻路
        if auto_play:
            auto_play = False
            step_time = 400
        else:
            auto_play = True
            step_time = 100

    if waiting:
        screen.blit(face,(0,0))
    else:
        if not game_over:
            snake.update(ticks)
            food_group.update(ticks)
            
            #检测是否捡起食物，并增加身体长度
            hit_list = pygame.sprite.groupcollide(snake.segments, \
                food_group, False, True)
            if len(hit_list) > 0:
                food_group.add(Food())
                snake.add_segment()

            #检测是否碰到了自己的身体
            for n in range(1, len(snake.segments)):
                if pygame.sprite.collide_rect(snake.segments[0], snake.segments[n]):
                    game_over = True

            #检查屏幕边界
            head_x = snake.segments[0].X//img_size
            head_y = snake.segments[0].Y//img_size
            if head_x < 0 or head_x > 24 or head_y < 0 or head_y > 18:
                game_over = True

            #执行自动寻路代码
            if auto_play: auto_move()
        
        backbuffer.fill((20,50,20)) 
        snake.draw(backbuffer)
        food_group.draw(backbuffer)
        screen.blit(backbuffer, (0,0))

        #左上角输出当前贪吃蛇长度和位置
        if not game_over:
            print_text(font, 0, 0, "Length " + str(len(snake.segments)))
            print_text(font, 0, 20, "Position " + str(snake.segments[0].X//img_size) + \
                       "," + str(snake.segments[0].Y//img_size))
        else:
            print_text(font, 0, 0, "GAME OVER")
            waiting = True
            game_over = False

        #显示自动寻路字样
        if auto_play: 
            print_text(font, 600, 0, "AUTO")

        #循环播放背景音乐 
        if int(current_time)%200 ==0 and rePlay:
            play_sound(bgm)
            rePlay = False
        if int(current_time)%200 == 1:
            rePlay = True

    pygame.display.update() 
{% endcodeblock %}

# 修改代码
## SnakeGame.py

以上即为整个程序的整个流程，看完了这些也就理解了整个程序的调用关系
很明显，要加入的障碍物Wall类，本质上和食物Food类/蛇身SnakeSegment类是一样的
只要稍微修改Food类的代码，即可作为Wall类使用，给程序加入障碍物，代码如下

### 增加 Wall类
{% codeblock SnakeGame.py lang:Python %}
class Wall(MySprite):
    global img_size                 #全局变量
    def __init__(self):             #初始化
        MySprite.__init__(self)
        image = create_wall_pic()   #获取墙图像
        self.set_image(image)       #设置墙图像
        MySprite.update(self, 0, 30)
        self.X = random.randint(0, 23) * img_size  #随机Wall墙的X坐标
        self.Y = random.randint(0, 17) * img_size  #随机Wall墙的Y坐标
{% endcodeblock %}
注:这么做有几个缺点:
- 墙是随机的,不能控制位置
- 墙有可能和食物的位置一样,即游戏无法进行(如果同时只有一个食物的话)

###  增加 create_wall_pic()
{% codeblock SnakeGame.py lang:Python %}
#创建墙头像
def create_wall_pic():
    global img_group
    index = 16
    image = img_group[index]        #返回墙图片
    return image
{% endcodeblock %}

### 增加代码 在game_init()中 初始化墙
{% codeblock SnakeGame.py lang:Python %}
#创建墙对象
wall_group = pygame.sprite.Group()
for i in range(1, wall_num + 1):    #wall_num为全局变量,代表墙的数量
    wall = Wall()                   #初始化一堵墙
    wall_group.add(wall)            #使用墙组wall_group来同时保存多个墙
{% endcodeblock %}

### 增加代码 在主程序中 判断是否撞到墙
{% codeblock SnakeGame.py lang:Python %}
 #检测是否撞到墙，并结束游戏
hit_list = pygame.sprite.groupcollide(snake.segments, wall_group, False, True)
if len(hit_list) > 0:
    game_over = True
{% endcodeblock %}

### 增加代码 在主程序对应位置 刷新墙
{% codeblock SnakeGame.py lang:Python %}
……
wall_group.update(ticks)
……
wall_group.draw(backbuffer)
……
{% endcodeblock %}

# 测试
## 修改前
{% asset_img 2.png %}
## 修改后
{% asset_img 3.png %}
可以看到墙已经在游戏中正常显示，游戏中也能正常的撞墙导致游戏结束
所以到此对该游戏的修改也就结束了………………吗？

不不不，因为我发现代码中还有一些有趣的地方

# 有趣的东西

## 自动寻路？

发现游戏中还有一个“特别”的函数，可以在游戏中按空格Space键让蛇自动寻路（吃食物）
在原版游戏中亲测大部分情况下可以自动寻路（蛇方向和食物方向相反时不可以，原因见下）
### 简单自动寻路函数auto_move()
{% codeblock SnakeGame.py lang:Python %}
#简单自动寻路代码
def auto_move():
    direction = get_current_direction() #获取当前蛇方向
    food_dir = get_food_direction()     #获取当前食物方向
    if food_dir == "left":              #根据蛇方向和食物方向改变蛇的方向
        if direction != "right":
            direction = "left"
    elif food_dir == "right":
        if direction != "left":
            direction = "right"
    elif food_dir == "up":
        if direction != "down":
            direction = "up"
    elif food_dir == "down":
        if direction != "up":
            direction = "down"

    #设置蛇的移动方向
    if direction == "up": snake.velocity = Point(0,-1)
    elif direction == "down": snake.velocity = Point(0,1)
    elif direction == "left": snake.velocity = Point(-1,0)
    elif direction == "right": snake.velocity = Point(1,0)
{% endcodeblock %}
注：
看完了函数，发现真的如注释中所说，是简单自动寻路不是复杂自动寻路
函数也存在一个BUG，在蛇方向和食物方向相反时，不能自动（绕圈）转变蛇方向
所以在这种情况下，蛇不能简单自动寻路


### 获得蛇当前的方向函数get_current_direction()
{% codeblock SnakeGame.py lang:Python %}
#获得蛇当前的方向
def get_current_direction():
    global head_x,head_y
    global img_size
    first_segment_x = snake.segments[1].X//img_size
    first_segment_y = snake.segments[1].Y//img_size
    if head_x-1 == first_segment_x:   return "right"
    elif head_x+1 == first_segment_x: return "left"
    elif head_y-1 == first_segment_y: return "down"
    elif head_y+1 == first_segment_y: return "up"
{% endcodeblock %} 

### 获得食物的方向函数get_food_direction()
{% codeblock SnakeGame.py lang:Python %}
#获得食物的坐标
def get_food_direction():
    global head_x,head_y
    global img_size
    food = Point(0,0)
    for obj in food_group:
        food = Point(obj.X//img_size,obj.Y//img_size)
    if head_x < food.x:       return "right"
    elif head_x > food.x:     return "left"
    elif head_x == food.x:
        if head_y < food.y:   return "down"
        elif head_y > food.y: return "up"
{% endcodeblock %} 

## 代码修改

看完上述代码之后，你会发现在加入墙之后
简单自动寻路基本处于完全无法使用的状态（因为无法判断前进路上是否有墙）

等待更新～



最后更新时间：2017年6月11日 17:11