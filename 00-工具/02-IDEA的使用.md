# 常用快捷键
## Ctrl    
Ctrl + F  -- 在当前文件进行文本查找  

Ctrl + R  -- 在当前文件进行文本替换

Ctrl + Z  -- 撤销 

Ctrl + Y  -- 删除光标所在行或删除选中的行   

Ctrl + D  -- 复制并插入内容  

Ctrl + W  -- 递进式选择代码块  

Ctrl + E  -- 显示最近打开的文件记录列表  

Ctrl + N  -- 根据输入的**类名**查找类文件  

Ctrl + J  -- 插入自定义动态代码模板  

Ctrl + P  -- 方法参数提示显示  

Ctrl + U  -- 前往所在光标方法的父类方法/接口  

Ctrl + B(Ctrl + 左键单击)  -- 进入光标所在的方法/变量的接口或是定义处  

Ctrl + H  -- 显示当前类的层次结构  

Ctrl + `+`  -- 展开代码  

Ctrl + `-`  -- 折叠代码  

Ctrl + /  -- 单行注释  

Ctrl + F1  -- 显示错误信息  

Ctrl + F3  -- 调转到所选中的词的下一个引用位置  

Ctrl + F4  -- 关闭当前文件  

Ctrl + F9  -- 执行Make Project操作  

Ctrl + F11  -- 设置/取消书签  

Ctrl + Space(被占用，建议改成Ctrl + 逗号)  -- 基础代码补全  

Ctrl + Delete  -- 删除光标后一个单词或中文句  

Ctrl + BackSpace  -- 删除光标前一个单词或中文句    

Ctrl + 1,2,3...9  -- 跳转到指定书签  

Ctrl + 左键单击  -- 在打开的文件标题上，弹出该文件路径  

Ctrl + 左方向键  -- 光标跳转到当前单词/中文句的左侧开头位置  

Ctrl + 右方向键  -- 光标跳转到当前单词/中文句的右侧开头位置   

Ctrl + 前方向键  -- 鼠标滚轮向前  

Ctrl + 后方向键  -- 鼠标滚轮向后    
## Alt  
Alt + `  -- 显示版本控制常用操作菜单

Alt + Enter  -- 快速修复选择(导包 导依赖...)  

Alt + insert  -- 代码自动生成(set/get方法  构造方法  toString等)   

Alt + 左右方向键  -- 切换当前已打开的窗口中子视图  

Alt + 前后方向键  -- 跳转到当前文件的前|后一个方法名位置  

Alt + 1,2,3...9  -- 显示对应数值的选项卡  
## Shift  
Shift + F11  -- 弹出书签显示层  

Shift + Tab  -- 取消缩进  

Shift + Enter  -- 开始新一行  

Shift + 左键单击  -- 关闭当前打开的文件  

Shift + 滚动前后轮  -- 当前文件的横向滚动轴滚动 
## Ctrl + Alt  
Ctrl + Alt + L  -- 格式化代码  

Ctrl + Alt + O  -- 优化导入的类  

Ctrl + Alt + T  -- 对选中的代码弹出环绕选项弹出层  

Ctrl + Alt + S  -- 打开IntelliJ IDEA系统设置    

Ctrl + Alt + Enter  -- 在当前行的上一行添加一行  

Ctrl + Alt + 左右方向键  -- 退回|前进到上一个操作的地方  

## Ctrl + Shift  
Ctrl + Shift + F  -- 全文查找  

Ctrl + Shift + R  -- 全文替换  

Ctrl + Shift + J  -- 自动将下一行合并到当前行末尾  

Ctrl + Shift + Z  -- 取消撤销  

Ctrl + Shift + W  -- 递进式取消选择代码块  

Ctrl + Shift + N  -- 通过文件名 定位 | 打开 文件 | 目录[需要在后面多加一个正斜杠]  

Ctrl + Shift + U  -- 对选中的代码进行大/小写轮流转换  

Ctrl + Shift + T  -- 对当前类生成单元测试类  

Ctrl + Shift + C  -- 复制当前文件磁盘路径    

Ctrl + Shift + B  -- 跳转到类型声明处  

Ctrl + Shift + /  -- 多行注释  

Ctrl + Shift + [  -- 选中从光标所在位置到它顶部中括号位置  

Ctrl + Shift + ]  -- 选中从光标所在位置到它底部中括号位置  

Ctrl + Shift + `+`  -- 展开项目所有代码行  

Ctrl + Shift + `-`  -- 折叠项目所有代码行  

Ctrl + Shift + F7  -- 高亮显示所有该选中文本，按Esc高亮消失  

Ctrl + Shift + F12  -- 编辑器最大化  

Ctrl + Shift + Enter  -- 自动结束代码  

Ctrl + Shift + Backspace  -- 退回到上次修改的地方  

Ctrl + Shift + 1,2,3...9  -- 快速添加书签  

Ctrl + Shift + 左右方向键  -- 在代码文件上，光标跳转到当前单词/中文句左/右侧开头位置，同时选中该单词/中文句  

Ctrl + Shift + 排序前后方向键  -- 光标放在方法名上，将方法移动到上/下一个方法前面(调整方法位置)

## Alt + Shift  
Alt + Shift + N  -- 选择/添加task  

Alt + Shift + 左键双击  -- 多选单词/中文句  

Alt + Shift + 前后方向键  -- 将当前行前移/后移一行

## Ctrl + Shift + Alt  
Ctrl + Shift + Alt + V  -- 无格式粘贴

Ctrl + Shift + Alt + S  -- 打开当前项目设置  

## 其他  
F2  -- 跳转到下一个高亮错误或警告位置   

F4  -- 编辑源  

F11  -- 添加书签  

F12  -- 回到前一个工具窗口  

ESC  -- 从工具窗口进入代码文件窗口  

# 清理缓存和索引  
File -- Invalidate Caches / Restart...  

- 一般建议点击 `Invalidate and Restart`，这样会比较干净。    

- 清除索引和缓存会使得 IntelliJ IDEA 的 Local History 丢失，所以如果你项目没有加入到版本控制，而你又需要你项目文件的历史更改记录，那你最好备份下你的 LocalHistory 目录。目录地址在：`C:\Users\当前登录的系统用户名\.IntelliJIdea14\system\LocalHistory`( 建议使用硬盘的全文搜索)  
- 如果你遇到了因为索引、缓存坏了以至于项目打不开，那也建议你可以直接删除 system 目录，一般这样都可以很好地解决你的问题。  
- 如果你 C 盘空间不足的情况下，最好转移下 system 目录。