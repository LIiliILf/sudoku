how to run it 

1. git clone 
2. open "index.html"

# 2023秋软工实践 第二次结对编程作业

| 这个作业属于哪个课程 | [软件工程](https://bbs.csdn.net/forums/fzusdn-0831?typeId=4994744) |
| -------------------- | ------------------------------------------------------------ |
| 这个作业要求在哪里   | [结对作业二要求](https://bbs.csdn.net/topics/617335682)      |
| 个人学号             | 102299110                                                    |
| 结对成员学号         | 102299105                                                    |
| GitHub 仓库地址      | [<GitHub仓库地址>](https://github.com/LIiliILf/sudoku)       |

---



## PSP表格

| 子任务                   | 预估用时 | 实际用时 |
| ------------------------ | -------- | -------- |
| 计划                     | 0.5h     | 0.5h     |
| 估计这个任务需要多少时间 | 0.3h     | 0.3h     |
| 具体设计                 | 1h       | 1.5h     |
| 开发                     | 5h       | 6h       |
| 计算工作量               | 0.5h     | 0.5h     |
| 博客报告、总结           | 1.5h     | 2h       |

## 纸画模型
![纸画](https://img-blog.csdnimg.cn/0f5e228c3d264d0e83e0a3d5d00a598a.jpeg)

##  相关算法

### 数独求解算法

```
const sudokuCreate = (grid) => {
    let unassigned_pos = {
        row: -1,
        col: -1
    }

    if (!findUnassignedPos(grid, unassigned_pos)) return true;

    let number_list = shuffleArray([...CONSTANT.NUMBERS]);

    let row = unassigned_pos.row;
    let col = unassigned_pos.col;

    for (let num of number_list) {
        if (isSafe(grid, row, col, num)) {
            grid[row][col] = num;

            if (sudokuCreate(grid)) {
                return true;
            }

            grid[row][col] = CONSTANT.UNASSIGNED;
        }
    }

    return false;
}
```

 `sudokuCreate(grid)` 函数：

   - 这是一个递归的深度优先搜索算法，它使用递归来尝试在格子中放置数字，并确保生成的数独游戏是合法的。

   - 然后采用了回溯，会不断地尝试填充数字，直到生成一个完整的数独游戏，然后返回 `true`，或者如果无法继续填充则返回 `false`。

     主要的流程如下：

1. **初始化未分配位置：** 函数开始时，`unassigned_pos`对象的`row`和`col`属性都被设置为-1，表示没有找到未分配位置。
2. **查找未分配位置：** 调用`findUnassignedPos(grid, unassigned_pos)`函数，如果找到未分配位置，将其坐标存储在`unassigned_pos`对象中。
3. **尝试放置数字：** 使用`shuffleArray`函数随机打乱数字的尝试顺序。然后对于每个数字，检查是否可以安全地放置在当前位置（使用`isSafe`函数进行检查）。
4. **递归尝试下一个位置：** 如果可以安全地放置数字，将该数字放入网格，并递归调用`sudokuCreate`函数来尝试在下一个位置放置数字。如果在递归调用中找到了解，返回`true`，表示找到了一个有效的解。
5. **回溯：** 如果递归调用中无法找到解，将当前位置的数字重新设为未分配（常量`CONSTANT.UNASSIGNED`），撤销该数字的放置。然后，继续尝试下一个数字。
6. **返回结果：** 如果所有数字都尝试过，且没有找到解，返回`false`，表示无法生成一个有效的数独谜题。

想了一下如何显示答案，还是觉得放在console里面比较好，做不出来再看答案。

### 并发

思路：使用`async`和`await`实现。将`sudokuGen`函数改造为异步函数，然后使用`Promise`和`Promise.all`来并发生成数独。

```
const sudokuGen = async (level) => {
    const generateSudoku = async () => {
        let sudoku = newGrid(CONSTANT.GRID_SIZE);
        let check = sudokuCreate(sudoku);
        if (check) {
            let question = removeCells(sudoku, level);
            return {
                original: sudoku,
                question: question
            };
        }
        return undefined;
    };

    const generatePromises = Array.from({ length: 9 }, () => generateSudoku());

    const sudokus = await Promise.all(generatePromises);
    return sudokus;
};

// 调用方式
const generateNineSudokus = async () => {
    const sudokus = await sudokuGen(level);
    // sudokus 数组包含了9个与原来一样的数独
    console.log(sudokus);
};

// 调用函数
generateNineSudokus();

```

创建了一个内部的异步函数`generateSudoku`，该函数用于生成单个数独。然后，使用`Array.from`创建了包含9个`generateSudoku`函数调用的数组，并将这些调用包装在`Promise`中。使用`Promise.all`来并发执行这些`Promise`，返回一个包含9个数独的数组。最后，在`generateNineSudokus`函数中调用`sudokuGen`。

### 重点难点

数独求解算法：采用深度优先和回溯，这样的复杂度还是挺高的，感受是之前学的理论好像可以用上了。

如何实现判断填入的数独的对错。

在sudoku.js中的**`sudokuCheck(grid)`函数：** 这个函数用于检查填入的数独是否符合数独规则。它接受一个`grid`参数，表示数独的当前状态。检查以下条件：

- 每一行包含了从1到9的不重复数字。

- 每一列包含了从1到9的不重复数字。
- 每个3x3的小格子包含了从1到9的不重复数字。

还有**`isSafe(grid, row, col, value)`函数：** 这个函数用于检查在给定位置（`row`行，`col`列）填入给定的数字`value`是否是安全的。它通过调用`checkCol`、`checkRow`和`checkBox`函数来确保在所在的行、列和3x3的小格子内没有重复的数字。

### 附加分

 - **可以输入数字、游戏界面-选中格子会有对应高亮**

   灰色格子是待填的，可以点击下面的数字填入，然后选择灰色格子会有对应的行列九宫格的提示。

   <img src="https://img-blog.csdnimg.cn/46d25866955d42fa8878121e48b68940.png" alt="fillHover" style="zoom:50%;" />

 - **答案错误的提示** 

  <img src="RE/5d68b4c98b9a4b2d860dc86094c7e0f5.png" alt="errorHint" style="zoom: 50%;" />

## 结对记录

关于结对，先通过共享腾讯文档，分享想法进度，然后制定了代码规范，根据需求讨论最终想要的效果。

## 1、代码规范

**1.1 命名**
统一：对于同一个概念，在程序中用同一种表示方法。比如对于供应商，既可以用supplier，也可以用provider，但是只选定一个使用。
达意：准确的表达出它所代表的意义
简洁：在统一和达意的前提下，用尽量少的标识符。达意>简洁。
驼峰命名：单词之间不使用特殊符号分割，而是通过首字母大写来分割。
尽量用英文单词表示。

**1.2 缩进、空格**
大括号的开始在代码块开始的行尾，闭合在和代码块同一缩进的行首
运算符两边用一个空格隔开

**1.3 其他**
每个if while for等语句，都不要省略大括号{}
注释，单行时用 //, 多行时用 /  *  …  *  / 

## 2、工作照片

 - **通过腾讯会议和共享文档交流**

![onlineMeeting](RE/0e1d0b79cb1543c48ed2a915ed912c16.png)





 - **git提交记录**





 - **线下沟通细节**
   <img src="RE/f619f992981e4380b09ede394e23e806.jpeg" alt="请添加图片描述" style="zoom: 25%;" />

- 调试前端遇到一些问题

  <img src="https://img-blog.csdnimg.cn/05c8437619e8457ab719fd6c54dac5ed.jpeg" alt="调试" style="zoom: 50%;" />

## 总结

![在这里插入图片描述](https://img-blog.csdnimg.cn/2a0e7d2195184688b5ba3eeb4b0ade8a.png)**102299105**

>因为是在第一次的基础上，所以工作量很明显比第一次有少一些，虽然还是很麻烦（苦笑）。第二次结对编程体验，比起第一次感受更好，因为前期代码规范等细节都已沟通好，这一次目标明确的继续完全新任务就好，虽然拖延double有点过分享受国庆。

![在这里插入图片描述](https://img-blog.csdnimg.cn/a47481edf7e74731890bfe0399e874fc.png)  **102299110**

>关于git协作方式，以下可以参考：
>
>	git clone git@github.com:p1/sudoku.git		//p1为成员1的github账号名 
>	git checkout -b p2 //建立成员2的个人分支，名为p2 
>	git push --set-upstream origin p2	//关联远程个人分支 命名一样。 
>	git branch -vv //检查 
>
>然后成员1 可以把这个分支merge到主分支。
>
>体验：相对于第一次会轻松一点，但还是挺花时间的，调试的时候也会遇到各种问题。想做出一个好的产品需要经常优化、打磨。