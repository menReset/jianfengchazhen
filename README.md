# jianfengchazhen
一个canvas小游戏
javaScript代码部分
var canvas=document.querySelector("#canvas");
var ctx=canvas.getContext("2d");
var centerX = 400;//大圆X轴坐标
var centerY = 200;//大圆Y轴坐标
var bigRadius = 50;// 大圆半径
var smallRadius = 10;//小球半径
var line = 100;//线条的长度
var balls = [];//定义一个用于存储转动小球数组 =  {'angle':120,numStr:''}
var waitBalls = [];//定义一个用于存储等待小球数组
//游戏模拟数据
var level;//定义游戏关卡
//重置关卡
var re=document.querySelector("#reset");
re.onclick=function(){
    window.location.href = "index.html#"+0;
};
if(parseInt(window.location.href.split("#")[1])){
    level = parseInt(window.location.href.split("#")[1]);
    console.log(level);
}else{
    level = 0;
}
var levelArr=[          //设置关卡  ballNum转动小球  waitNum等待小球  speed速度
    {"ballNum":3, "waitNum":5, "speed":1},
    {"ballNum":4, "waitNum":8, "speed":1},
    {"ballNum":5, "waitNum":5, "speed":1},
    {"ballNum":6, "waitNum":8, "speed":1},
    {"ballNum":7, "waitNum":10, "speed":1},
    {"ballNum":8, "waitNum":12, "speed":1},
    {"ballNum":9, "waitNum":15, "speed":1},
    {"ballNum":10, "waitNum":18, "speed":1},
    {"ballNum":5, "waitNum":8, "speed":2},
    {"ballNum":6, "waitNum":10, "speed":2},
    {"ballNum":7, "waitNum":12, "speed":2},
    {"ballNum":6, "waitNum":8, "speed":3},
    {"ballNum":7, "waitNum":10, "speed":3},
    {"ballNum":8, "waitNum":12, "speed":3},
    {"ballNum":9, "waitNum":14, "speed":3}
];
var ballNum = levelArr[level].ballNum;//定义转动小球数量
var waitNum = levelArr[level].waitNum;//定义等待小球数量
var speed=levelArr[level].speed;//定义旋转速度  用以控制游戏难度
//定义全局的画笔样式
ctx.textAlign="center";//水平居中
ctx.textBaseline="middle";//垂直居中

function drawBig(){
    //绘制大圆
    ctx.beginPath();
    ctx.arc(centerX,centerY,bigRadius,0,Math.PI*2);
    ctx.closePath();
    ctx.fillStyle="#000";
    ctx.fill();
    //绘制关卡数
    ctx.fillStyle="#fff";
    ctx.font = "50px 微软雅黑";
    ctx.fillText(level+1,centerX,centerY);
}

//小球转动数据
    for(var i=0;i<ballNum;i++){
        var angle=360/ballNum*i;
        balls.push({
            angle:angle,
            numStr:''
        })
    }

//整体旋转
function drawBalls(){
    ctx.save();
    ctx.clearRect(200,20,400,350);
    drawBig();
    ctx.translate(centerX,centerY);//设置圆心位置
    //绘制转动小球
    balls.forEach(function(item){//通过循环遍历绘制小球,item 为数组每次遍历的元素
        ctx.save();//保存
        //旋转
        ctx.rotate(Math.PI/180*item.angle);
        item.angle+=speed;//增加角度   用以控制游戏难度
        if(item.angle>=360)item.angle=item.angle-360;//角度恢复
        //绘制小球线条
        ctx.beginPath();
        ctx.moveTo(0,-bigRadius);
        ctx.lineTo(0,-(bigRadius+line));
        ctx.stroke();
        ctx.closePath();
        //绘制小球
        ctx.beginPath();
        ctx.fillStyle="#000";
        ctx.arc(0,-(bigRadius+line),smallRadius,0,Math.PI*2);
        ctx.fill();
        ctx.closePath();
        //绘制数字
        if(item.numStr!=''){
            ctx.fillStyle="#fff";
            ctx.font="12px 微软雅黑";
            ctx.fillText(item.numStr,0,-(bigRadius+line));
        }
        ctx.restore();//恢复
    });
    ctx.restore();
    window.requestAnimationFrame(drawBalls);
}
drawBalls();

//小球储存信息
    for(var i=waitNum;i>0;i--){
        waitBalls.push({
            angle:'',
            numStr:i
        })
    }

//绘制等待小球
function drawWaitBalls(){
    //清空等待小球区域位置
    ctx.clearRect(390,350,20,450);
    //等待的小球
    waitBalls.forEach(function(item,index){//通过循环遍历绘制小球,item 为数组每次遍历的元素 index是索引值
        ctx.save();//保存
        //位移
        ctx.translate(0,30*index);
        //绘制等待小球
        ctx.beginPath();
        ctx.fillStyle="#000";
        ctx.arc(centerX,centerY+200,smallRadius,0,Math.PI*2);
        ctx.fill();
        ctx.closePath();
        ctx.fillStyle="#fff";
        ctx.font = "12px 微软雅黑";
        ctx.fillText(item.numStr,centerX,centerY+200);
        ctx.restore();
    });
}
drawWaitBalls();

//canvas 点击事件
var gameOver=false;
canvas.onclick=function(){
    //删除等待小球，并且把删除的小球插入到转动小球的末尾处
    var obj = waitBalls.shift();
    //更改小球的角度
    obj.angle=180;
    //通过循环判断小球是否被撞
    for(var i=0;i<balls.length;i++){
        //console.log(balls[i].angle);
        if(balls[i].angle>=172.4&&balls[i].angle<=187.6){
            gameOver=true;
            break;
        }
    }
    //如果等待小球撞到旋转小球，游戏就结束
    if(gameOver){
        alert('闯关失败');
        //重置当前关卡
        window.location.href = "index.html#"+level;
        gameOver=false;
    }else if(waitBalls.length==0){
        alert('成功过关');
        level++;
        if(level>=levelArr.length)alert('通关');
        window.location.href = "index.html#"+level;
    }
    //将删除的小球插入
    balls.push(obj);
    //重新绘制等待小球区域
    drawWaitBalls();
};
