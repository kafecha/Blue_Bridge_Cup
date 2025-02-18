## Led模块

### led底层

```c
unsigned char ucLed[8] = {0,0,0,0,0,0,0,0};//Led显示数据存放数组
```

```c
void Led_Disp(unsigned char addr, enable)
{
    static unsigned char temp = 0x00;
    static unsigned char temp_old = 0xff;
    if (enable)
        temp |= 0x01 << addr;
    else
        temp &= ~(0x01 << addr);
    if (temp != temp_old)
    {
        P0 = ~temp;
        P2 = P2 & 0x1f | 0x80;
        P2 &= 0x1f;
        temp_old = temp;
    }
}
```

```c
/* 定时器0中断服务函数 */
void Timer0Server() interrupt 1
{ 
    if(++Seg_Pos == 8) Seg_Pos = 0;
	Led_Disp(Seg_Pos,ucLed[Seg_Pos]);
}
```

调用方法

```c
ucLed[x]=y
```

### 互斥点亮

#### 两个互斥点亮

<img src="./led .assets/image-20250218165436642.png" alt="image-20250218165436642" style="zoom:80%;" />

``` 
ucLed[0] = Seg_Disp_Mode;
ucLed[0] = !Seg_Disp_Mode;
```

#### 三个及以上

<img src="./led .assets/image-20250218170005114.png" alt="image-20250218170005114" style="zoom:80%;" />

```c
for(i=0;i<3;i++)
ucLed[1+i] = (i == Seg_Disp_Mode)
```

#### 闪烁

<img src="./led .assets/image-20250218170310312.png" alt="image-20250218170310312" style="zoom: 67%;" />

```c
/* 定时器0中断服务函数 */
void Timer0Server() interrupt 1
{
    if(++Timer_200ms == 200)
    {
        Timer_200ms = 0;
        Led_Star_Flag ^= 1;
    }
}

/* 其他显示函数 */
void Led_Proc()
{
	ucLed[0] = Led_Star_Flag;
}
```





