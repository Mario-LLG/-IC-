## Iset.m

[电流卡配置](https://ww2.mathworks.cn/help/daq/examples/generate-signals-on-ni-devices-that-output-current.html)

```matlab
function data3 = iset(gbest,time)  %gbest 是迭代得到的全局最优值
Ioutput = daq.creatSession('ni')
Ioutput.addAnalogOutputChannel('Dev3','14:31','Current')
Ioutput2 = daq.creatSession('ni')
Ioutput2.addAnalogOutputChannel('Dev4','14:31','Current')
I1 = gbest/1000;
Ioutput.outputsingleScan(I1(1:16))
Ioutput2.outputsinglescan(I1(17:32))  %NI端口输出
```

>Iset函数是将pso算法得到的一次全局最优值通过NI电流卡输出
>
>![image-20190621112130046](http://ww1.sinaimg.cn/large/006tNc79ly1g48m1ylrv8j30ue08p40b.jpg)

![image-20190621112109691](http://ww3.sinaimg.cn/large/006tNc79ly1g48m1nthcuj30na0anmyv.jpg)

##plotfiled.m

```matlab
function data3 = plotfiled(gbest,time)
Ioutput = daq.creatSession('ni')
Ioutput.addAnalogOutputChannel('Dev3','14:31','Current')
Ioutput2 = daq.creatSession('ni')
Ioutput2.addAnalogOutputChannel('Dev3','14:31','Current')
I1 = gbest/1000;
Ioutput.outputsingleScan(I1(1:16));
Ioutput2.outputsingleScan(I2(17:32));

siz1 = 320;
siz2 = 256;
yy = ((0:siz1)-siz1/2)*30;
xx = ((0:siz2)-siz2/2)*30;

yy=asin(yy*75/100/(10*1000))/pi*180;
xx=asin(xx*75/100/(10*1000))/pi*180;

data0=yprime(time,2);
data0(181,153)=round((data0(180,153)+data0(182,153)+data0(181,152)+data0(181,154))/4);
data0(139,148)=round((data0(140,148)+data0(138,148)+data0(139,147)+data0(139,149))/4);
data0(175,98)=round((data0(175,97)+data0(175,99)+data0(174,98)+data0(176,98))/4);
data0(215,127)=round((data0(215,128)+data0(215,126)+data0(216,127)+data0(214,127))/4);
data0(320,:)=data0(319,:);

data=double(data0);

t1=find(abs(yy)<=10);
data1=data0(t1,:);
mesh(data1);
data3=data1;
```



### profile.m

```matlab
%data=yprime(1,1);  main to plot what??
data0=data;
data0(181,153)=round((data0(180,153)+data0(182,153)+data0(181,152)+data0(181,154))/4);
data0(139,148)=round((data0(140,148)+data0(138,148)+data0(139,147)+data0(139,149))/4);
data0(175,98)=round((data0(175,97)+data0(175,99)+data0(174,98)+data0(176,98))/4);
data0(215,127)=round((data0(215,128)+data0(215,126)+data0(216,127)+data0(214,127))/4);
data0(320,:)=data0(319,:);

data=double(data0);

focusLength=10;%mm
[siz1,siz2]=size(data);
yy=((0:siz1-1)-siz1/2)*30;%um
xx=((0:siz2-1)-siz2/2)*30;%um
    
yy=asin(yy*75/100/(focusLength*1000))/pi*180;
xx=asin(xx*75/100/(focusLength*1000))/pi*180;

% t1=find(abs(yy)<=10);
% data1=data(t1,:)a;
% [t1,t2]=max(data1);
% [t3,peakPositionWavelength]=max(t1);

y=data(92,:);
x=data(:,127);

x1=x-min(x);
y1=y-min(y);
y1=y1/max(y1);
x1=x1/max(x1);
plot(xx,y1);hold on;grid on;
plot(yy,x1);grid on;
```

- 做了个什么运算

![1561098320484](C:\Users\L\AppData\Roaming\Typora\typora-user-images\1561098320484.png)

### PSOoptimization2DAC.m

```matlab
function PSoptimization

global dataBackground;
global position;
global angle1;
global angle2;
global angle3;
global xx;
global yy;
global peakPositionWavelength;  %%??波峰波长??
global currentChannel;

global instr;
instr=[];

position=1.0;%degree
numOfLoop=150;

angle1=0.15;%degree
angle2=2.0;%degree
angle3=10;%degree

if (~isempty(imaqfind))
    stop(imaqfind);
    delete(imaqfind);
end
if ~isempty(instrfind)
    fclose(instrfind);
    delete(instrfind);
end

initialCurrent=1.0;%mA
outputCurrent('PXI1Slot4/ao23',0.0,0.02,initialCurrent/1000);%the first one 1 mA;

currentChannel=[16,17,18,19,20,21,22];
currentOutput(ones(size(currentChannel))*initialCurrent);

vi=videoinput('ni',1);
triggerConfig(vi,'manual'); %%设置触发为人为模式
vi.FramesPerTrigger=1;   %每次触发为1帧图像  %%set(vid,'FramesPerTrigger',100);%每次触发为100帧图像
vi.TriggerRepeat=inf;   %Keep executing the trigger every time the trigger condition is met until the stop function is called or an error occurs.
start(vi);

t1=0;

% instr0=gpib('agilent',7,20);
% fopen(instr0);

% cmd=[':output off'];
% fprintf(instr0,cmd);

pause(1);
trigger(vi);  
while vi.FramesAvailable~=1
    t1=t1+1;
end
dataBackground0=getdata(vi);
dataBackground=double(dataBackground0);

dataBackground=dataBackground*0;

% cmd=[':output on'];
% fprintf(instr0,cmd);

pause(1);

    
trigger(vi);

while vi.FramesAvailable~=1
    t1=t1+1;
end

data0=getdata(vi);
data=double(data0);
data=data-dataBackground;

%%%%%%%%%%%%%%%%%%%%%%%%%%????????????????
focusLength=10;%mm
[siz1,siz2]=size(data);  %siz1 is line; siz2 is row
yy=((0:siz1-1)-siz1/2)*25;%um  symmetrization
xx=((0:siz2-1)-siz2/2)*25;%um
    
yy=asin(yy*75/100/(focusLength*1000))/pi*180;  %度＝弧度×180°/pi
xx=asin(xx*75/100/(focusLength*1000))/pi*180;
    
t1=find(abs(yy)<=10);   %%放回序列yy小于10的下表值
t0=t1-1;
data1=data(t1,:);       %%提取t1行的值
yy1=yy(t1);
[t1,t2]=max(data1);
[t3,peakPositionWavelength]=max(t1);



%----------------------------PSO algorithm---------------------------------

N=7;  %7个通道
M=7;  %粒子值
xmin=1.0;%mA
xmax=20;%mA
vmax=(xmax-xmin)/4;

x=zeros(M,N);
v=zeros(M,N);

x=initiatex(M,N,xmin,xmax);
v=initiatev(M,N,vmax);

% numOfLoop=100;

omega=(0:1/(numOfLoop-1):1)*(0.4-0.9)+0.9;  %%其实不用这样，通过乘以0.99 就可以将w逐步减小
c1=2.0;
c2=2.0;


pbestScore=fitness(x,vi);
[gbestScore,t1]=min(pbestScore);
pbest=x;
gbest=x(t1,:);

gbestScoreRecorded=zeros(1,numOfLoop);
flag=1;
for di=1:numOfLoop
    score=fitness(x,vi);
    for dj=1:M
        if pbestScore(dj)>=score(dj)
            pbest(dj,:)=x(dj,:);
            pbestScore(dj)=score(dj);
        end
        if gbestScore>=score(dj);
            gbest=x(dj,:);
            gbestScore=score(dj);
        end
    end
    v=omega(di)*v+c1*rand(M,N).*(pbest-x)+c2*rand(M,N).*(ones(M,1)*gbest-x);
    t1=find(v<-vmax);
    v(t1)=-vmax;
    t1=find(v>vmax);
    v(t1)=vmax;
    x=x+v;
    t1=find(x<xmin);
    x(t1)=xmin;
    t1=find(x>xmax);
    x(t1)=xmax;
    gbestScoreRecorded(di)=gbestScore;
    [di,gbestScore]
    if di==50 & gbestScore>0.3
        flag=0;
        break;
    end
end

figure;plot(gbestScoreRecorded,'b:.');
if flag==1
    filename=['result\gbest_',int2str(position),'.mat'];
    save(filename,'gbest');
    re=gbest
end



%---------------------------------------------
function v=initiatev(M,N,vmax)

t1=rand(M,N);
v=(t1-0.5)/0.5*vmax;


%----------------------------------------------------
function x=initiatex(M,N,xmin,xmax)

x=zeros(M,N);
t1=rand(M,N);
x=t1*(xmax-xmin)+xmin;
% x(1,:)=[4.4078    5.8546    9.2694    6.7907    1.3154   13.6941   13.5196];
% x(2,:)=[ 6.8429   13.7751   20.0000   20.0000   14.3875   12.8719   12.7171];


    
%-----------------------------------------------------
function score=fitness(X,vi)

global dataBackground;
global position;
global angle1;
global angle2;
global angle3;
global yy;
global xx;
global peakPositionWavelength;

lX=size(X,1);   % szdim = size(A,dim)
score=zeros(lX,1);
t1=0;
num=10;
for di=1:lX
    X1=X(di,:);
    currentOutput(X1);
    pause(0.05)

    trigger(vi);
    while vi.FramesAvailable~=1
        t1=t1+1;
    end
    data0=getdata(vi);
    data=double(data0);
    data=data-dataBackground;
   
    t5=find(abs(yy-position)<angle1);
    t6=max(abs(data(t5,peakPositionWavelength+(-num:num))));
    t7=find(abs(yy-position)>angle2 & abs(yy-position)<angle3);
    t8=max(abs(data(t7,peakPositionWavelength+(-num:num))));
    score(di)=max(t8)/max(t6);
    
end

%---------------------------------------------------------
function currentOutput(I)

global currentChannel;
I=I/1000;%A
lI=length(I);

for di=1:lI
    channelName=['PXI1Slot4/ao',int2str(currentChannel(di))];
    flag=outputCurrent(channelName,0.0,0.02,I(di)); 
    if flag~='succeed'
        error(['Current output error for channel ',int2str(currentChannel(di))]);
    end       
end


%----------------------------------------------------------
function SMConfigI(gpibAddress)

global instr;

l1=length(gpibAddress);
for di=1:l1
    instr=[instr,gpib('agilent',7,gpibAddress(di))];
    fopen(instr(di));
    cmd=[':sour:func curr'];
    fprintf(instr(di),cmd);
    cmd=[':sour:curr:mode fix'];
    fprintf(instr(di),cmd);
    cmd=[':sour:curr:rang:auto 1'];
    fprintf(instr(di),cmd);
    complianceVoltage=3;%V
    cmd=[':sens:volt:prot ',num2str(complianceVoltage,'%.1f')];
    fprintf(instr(di),cmd);

    cmd=[':sens:volt:rang:auto 1'];
    fprintf(instr(di),cmd);
    cmd=[':conf:volt'];
    fprintf(instr(di),cmd);

    cmd=[':form:elem volt'];
    fprintf(instr(di),cmd);
end

```

[imaqfind 配置](<https://ww2.mathworks.cn/help/imaq/imaqfind.html?searchHighlight=imaqfind&s_tid=doc_srchtitle>)  [instrfind 配置](<https://ww2.mathworks.cn/help/matlab/ref/instrfind.html?searchHighlight=instrfind&s_tid=doc_srchtitle>)

![1561098979603](C:\Users\L\AppData\Roaming\Typora\typora-user-images\1561098979603.png)

![1561100425970](C:\Users\L\AppData\Roaming\Typora\typora-user-images\1561100425970.png)



ADN8810: 转换公式
$$
I_{O U T}=\frac{\operatorname{Code}}{1,000} \times \frac{1}{R_{S N}} \times\left(\frac{R_{S N}}{15 k \Omega}+0.1\right)
$$

$$
I_{O U T}=\frac{\operatorname{Code}}{1,000} \times 0.25627
$$
![1567059945490](C:\Users\L\AppData\Roaming\Typora\typora-user-images\1567059945490.png)![1567060006936](C:\Users\L\AppData\Roaming\Typora\typora-user-images\1567060006936.png)

![1567056589127](C:\Users\L\AppData\Roaming\Typora\typora-user-images\1567056589127.png)