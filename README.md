# greedysnake
A game based on javascript    

## 一 项目介绍   

该项目是一个基于Javascript，采用面向对象的思想设计和实现的贪吃蛇小游戏。它的玩法是通过吃苹果得分，一直吃到没食物为止。    

### 贪吃蛇有三个关键的参与对象：

1.蛇   
2.食物    
3.地图    
地图是一个 m * n 的矩阵（二维数组），用于标记食物和蛇的位置。    

蛇是一串坐标索引链表；
食物是一个指向舞台坐标的索引值。

### 蛇的活动
蛇的活动有三种，如下：

移动    
吃食    
碰撞    

蛇在移动时，内部发生了什么变化？    

蛇链表在一次移动过程中做了两件事：**向表头插入一个新节点，同时剔除表尾一个旧节点， 如果吃到食物，则不剔除表尾节点。**   
考虑到蛇的移动是一个高频率的动作，如果采用数组作为链表性能较低，所以蛇链表必须是真正的链表结构。    
```形成链表关系
snakeHead.last = null;
snakeHead.next = snakeBody1;

snakeBody1.last = snakeHead;
snakeBody1.next = snakeBody2;

snakeBody2.last = snakeBody1;
snakeBody2.next = null;   
 ```
在蛇移动时，创建一个新的蛇头，并更新链表关系    
```
move: function(format) { //format用于决定要不要删除最后一个方块
    //创建新身体
    var newBody = new Square(this.head.x / sw, this.head.y / sh, 'snakeBody');
    //更新链表的关系
    newBody.next = this.head.next;
    newBody.next.last = newBody;
    newBody.last = null;

    this.head.remove();
    newBody.create();

    // 创建一个新蛇头
    var newHead = new Square(this.head.x / sw + this.direction.x, this.head.y / sh + this.direction.y, 'snakeHead');

    //更新链表关系
    newHead.next = newBody;
    newHead.last = null;
    newBody.last = newHead;

    newHead.viewContent.style.transform = 'rotate(' + this.direction.rotate + 'deg)';
    newHead.create();

    //蛇身上每一个方块的坐标更新
    this.pos.unshift([this.head.x / sw + this.direction.x, this.head.y / sh + this.direction.y]);
    // this.pos.splice(0, 0, [this.head.x / sw + this.direction.x, this.head.y / sh + this.direction.y]);
    this.head = newHead;

    if (!format) { //如果format的值为false,表示需要删除
        this.tail.remove();
        this.tail = this.tail.last;

        this.pos.pop();
    }
```

### 随机投食
采用Math.random()函数创建随机数，但同时要考虑到食物不能出现在蛇身上。   
```
var include = true; //循环跳出的条件，true表示食物的坐标在蛇身上（需要继续循环），false表示食物的坐标不在蛇身上
    while (include) {
        x = Math.round(Math.random() * (td - 1));
        y = Math.round(Math.random() * (tr - 1));

        snake.pos.forEach(function(value) {
            if (x != value[0] && y != value[1]) {
                include = false;
            }
        });

    }
```
