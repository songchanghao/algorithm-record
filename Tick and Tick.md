#### Tick and Tick

​	时钟的三个指针每一秒都在转动，每天都要见面很多次。最后，他们厌倦了这个，每个针都想远离其他两个。如果一个指针与其他任何一个指针至少有D度的距离，那么它就是快乐的。你要计算一天中有多少时间所有的指针都是快乐的。输入D是一个在0到120之间的实数，输出一天中所有指针都快乐的时间百分比，精确到小数点后3位。

**示例 1:**

```
输入: 0
输出: 100.000 
```

**示例 2:**

```
输入: 120
输出: 0.000 
```

**示例 3:**

```
输入: 90
输出: 6.251
```

​		最初的想法是三个for循环去遍历秒针、分针和时针，然后每次指针都要走....嗯？走多少？因为时间是连续的，但是如果普通for循环很明显是离散的，那么就需要对角度进行计算，一个表盘是360°，秒针的速度是360/60°/s，分针速度是360/(60×60)°/s，时针速度是360/(60×60×12)°/s，那么该如何模拟三个不同速度的指针？

<img src="C:\Users\Dell\Desktop\algorithm\algorithm-record\微信图片_20200425231558.png" alt="微信图片_20200425231558" style="zoom: 50%;" />

<img src="C:\Users\Dell\Desktop\algorithm\algorithm-record\微信图片_20200425231723.png" alt="微信图片_20200425231723" style="zoom:50%;" />

<img src="C:\Users\Dell\Desktop\algorithm\algorithm-record\微信图片_20200425231811.png" alt="微信图片_20200425231811" style="zoom: 50%;" />

​	参考了如下解法：

​	1、三个指针走的角速度秒针速度S = 6°/s，分针速度M = (1/10)°/s，时针速度H = (1/120)°/s

​	2、三个指针相对速度差秒时相差S_H = (719/120)°/s，秒分相差S_M = (59/10)°/s，分时相差M_H = (120/11)°/s

​	3、相差1°需要的时间秒时相差SH = (120/719)s/°，秒分相差SM = (10/59)s/°，分时相差MH = (120/11)s/°

​	4、相差360°需要的时间秒时相差tSH = 43200.0/719，秒分相差tSM = 3600.0/59，分时相差tMH = 43200.0/11

​	5、三者之间都需要最少相差D°才能满足条件，先求出三指针两两之间第一次满足条件的时间和第一次不满足条件的时间。在对两两指针之间满足条件的开始时间和结束时间进行遍历(三重循环)，把所有满足条件的时间累加起来就是所求满足条件的总时间。最后结果为：满足条件的总时间/43200*100。

​	6、设t为三个指针都满足条件的总时间，则t满足以下条件：

​	[相差N°的时间 + k圈 < t < 1圈 - 相差N°(相差360°-N°的时间) + k圈]

​	N×SH + k1×tSH < t < tSH - N×SH +k1×tSH

​	N×SM + k2×tSM < t < tSM - N×SM + k2×tSM

​	N×MH + k3×tMH < t < tMH - N×MH + k3×tMH

```c++
#include<iostream>
#include<stdio.h>
#include<algorithm>
using namespace std;

double max(double a,double b,double c){
    double temp = (a>b)?a:b;
    return (temp>c)?temp:c;
}

double min(double a,double b,double c){
    double temp = (a<b)?a:b;
    return (temp<c)?temp:c;
}

int main(){
    int n;
    //秒针、时针、分针角速度 秒针速度S = 6°/s，分针速度M = (1/10)°/s，时针速度H = (1/120)°/s
//    double v_s = 360.0/60;
//    double v_m = 360.0/60/60;
//    double v_h = 360/12/60/60;
    //任意两针的相对速度 秒时相差S_H = (719/120)°/s，秒分相差S_M = (59/10)°/s，分时相差M_H = (120/11)°/s
//    double v_hm = v_m - v_h;
//    double v_ms = v_s - v_m;                                                          
//    double v_hs = v_s - v_h;
    const double v_hs = 719.0/120, v_ms = 59.0/10, v_hm = 11.0/120;
    while(cin>>n){
        if(n==-1) break;
        //第一次满足条件的时间（开始时间）
        double start_hm = n / v_hm;
        double start_ms = n / v_ms;
        double start_hs = n / v_hs;
        //第一次不满足条件的时间（结束时间）
        double end_hm = (360 - n) / v_hm;
        double end_ms = (360 - n) / v_ms;
        double end_hs = (360 - n) / v_hs;
        //相差一度所需时间 秒时相差SH = (120/719)s/度，秒分相差SM = (10/59)s/度，分时相差MH = (120/11)s/度
        //相差360度所需时间 秒时相差tSH = 43200.0/719，秒分相差tSM = 3600.0/59，分时相差tMH = 43200.0/11
        const double T_hm=43200.0/11,T_hs=43200.0/719,T_ms=3600.0/59;
        double sum = 0;
        for(double b3 = start_hs,e3 = end_hs;e3 <= 43200.000001;b3 += T_hs,e3 += T_hs){
            for(double b2 = start_hm,e2 = end_hm;e2 <= 43200.000001;b2 += T_hm,e2 += T_hm){
                if(e2 < b3) continue; //去掉交集
                if(b2 > e3) break;
                for(double b1 = start_ms,e1 = end_ms;e1 <= 43200.000001;b1 += T_ms,e1 += T_ms){
                    if(e1 < b2 || e1 < b3) continue; //去掉交集
                    if(b1 > e2 || b1 > e3) break;
                    double Begin = max(b1,b2,b3);  //开始时间取最大，以满足全部要求
                    double End = min(e1,e2,e3);    //结束时间取最小，以满足全部要求
                    sum += (End-Begin);
                }
            }
        }
        printf("%.3lf\n",sum/432);
    }
    return 0;
}
```

