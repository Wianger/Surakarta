# 第一阶段报告
***
## 第一阶段规则判断实现报告
*报告人* __水稻试验田::仇嘉惟__
*****
规则判断的实现，主要在于判断吃子步骤的合法性，其他的情况通常简单。
下面主要报告如何判断吃子的合法与否。
__关键在于:__
1.__棋子在什么吃子棋线上__
2.__怎样模拟所有的走法__
3.__棋子位于交点时的处理__
下面我来分别报告：
***
  任何一个在能够吃子的棋子，其必在两条吃子棋线上（两条棋线可能重合，当重合时，就构成了交点情况）。分别是距棋盘边缘
___min(board_size_-1-mypiece.GetPosition().x,mypiece.GetPosition().x)__
的X棋线和距棋盘边缘
___min(board_size_-1-mypiece.GetPosition().y,mypiece.GetPosition().y)__
的y棋线（当有距离为0，即该棋线是棋盘边缘时，这条棋线就不是吃子棋线），我们分别模拟棋子在这两条棋线上的移动，若在移动过程中无阻碍地碰到了目标棋子，那么就是一次可吃的步骤。
  ***
  在模拟棋子在某条吃子棋线上的移动时，我们先判断我方棋子和对方棋子是否均在该吃子棋线上，若不是如此，则二者不能互吃，该次模拟返回false。若均在该棋线上，则开始模拟移动。我们将吃子棋线对应的四条直线顺序储存在一个（vector）line中，并通过函数Judgeroad和Judgeindex来确定棋子本身在此line中的位置。通过Liftmypiece来做到在此line中将自己的位置变更为NONE，以此实现自己不会撞到自己。然后，用一个指针指向line中的我方棋子的位置，通过
```cpp
index++;
if(index==(int)board_size_){
circle++;
index=0;
roadnow=(roadnow+1)%4;
}
```
和
```cpp
index--;
if(index==-1){
circle++;
index=(int)board_size_-1;
roadnow=(roadnow+3)%4;
}
```
来将四条直线连接成环，并将指针顺着环移动和逆着环移动。由此，可以充分模拟棋子的移动，若有一种移动做到了无阻碍地碰到了对方棋子且相遇时已经经过一次直线间连接处（circle>=1），则该次吃子有效，该次模拟可以返回true，在返回前通过Dropmypiece来保证模拟间不相互影响。
  ***
  若某棋子位于交点，则其在一次模拟中的在line中的位置不唯一，有两种情况。因此，我通过
  ___JudgeroadCross、JudgeindexCross___
  和
  ___Judgeroad、Judgeindex___
  两套函数实现优先根据x判断位置和优先根据y判断位置两种情况。并在一次模拟中四种情况（优先y，优先y）（优先y，优先x）（优先x，优先y）（优先x，优先x）分别模拟，来实现对交点情况的充分考虑。
***
综上，便充分模拟了棋子的移动并可据此判断吃子步骤的合法与否。
***
写在后面的话：
* 一开始以为boardsize!=6时也是全棋盘只有两条吃子棋线，按照直觉写了两套函数分别模拟“内棋线”和“外棋线”。后来测试没通过才知道一共有boardsize/2-1条棋线。然后就改成了x棋线和y棋线，并通过增加参数来将两套函数归于一套，使代码减少了约200行。
* 代码初期还是两套函数时，一套基本是另一套复制粘贴并改名字而来。这时我犯了一个很蠢的错误，改名字没改充分。这导致之后出现了匪夷所思的输出。我不断调试并在运行中输出信息，浪费了大量时间，最终找到了那个愚蠢的bug。由此观之，代码量越少，越一能生万物，正确性就越高，debug也会更轻松。
***
## 第一阶段ai报告
*报告人* **水稻试验田::朱文彬** **王韵雅**
我们初步只写了个简单的贪心，大致思路如下：
得到我方棋子的集合（记作from）以及对方棋子和空位的集合（记作to），构建从from到to的映射(由此产生一个move)，对这个move进行估值，主要从棋子站位的价值，以及能不能吃子两个方面来估值，由此得到一个较合理的走步；
**遇到的问题就是，我们期待使用哈希表来储存各棋子的合法走步映射，但编译总报错，期待后续完善ai的时候再解决。**
随机方面，我们在遇到相同估值的时候生成一个随机数，如果是偶数就更新，以此达到效果。
总之，这个ai进步空间还很大(