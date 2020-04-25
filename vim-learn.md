### ctrl + c === Es 
### 改键位
× 进入家目录。
× 进入.vim文件夹，如果没有则创建.vim文件夹。
× 创建vimrc文件。
× noremap 更改键位命令。
> eg: `noremap a b` 用a替换b。当你按a时，vim会认为你按了b。
#### 更改常用键
  * vim原先 h j k l 分别对于 左下上右 可以通过 norempa更改
  * norempa i k
  * noremap k j
  * noremap j h
  * noremap 
#### 命令快捷键
  * map S :w<CR>  # 用大写S替换:w回车(<CR>),快速保存。
  * map s <nop> # 小写s，不执行指令。
  * map Q :q<CR> #大写Q，替换:q回车，快速退出。
  * map R :source $HOME/.vim/vimrc, 重新加载vimrc配置文件当前文件。
#### 编辑命令
  <operation> <motion>
  * a 删除：d3l-d是删除操作 3是删除字符数 l是删除的方向,dd是剪切整行，p是粘贴
  * G 文尾行。
  * o 新建一行 大写前，小写后
  * y 是复制
  * p 是粘贴 大写前,小写后。
  * c 是 change 修该,cw修改一个词，ciw--(change in word) 在单词中修改。ci""--在引号中修改。di""
  * x 是删除。
  * w 下一个单词，b上一个单词。 
  * u 是撤销操作。
  * J 合并两行。 
  * f 查找。可以跟 d y结合使用。
  * /字符串全文查找。:noh取消高亮。
#### 插件
github上的 vim-plug是插件管理软件。




#### 可视化模式
    可视化模式有三种。
	* v-line ,按shit+v.选完行后可以输入:normal A.png(所选中的行后面都加.png) 指令进行按行批量操作。:normal Kstring。K是在前面添加
