# Git

1. 安装git：[Git for Windows](https://gitforwindows.org/)

2. 配置命令

   ```nginx
   git config --global user.name "Tony" 
   
   git config --global user.email "tony@gmail.com"
   ```

   查看是否配置成功，

   ```nginx
   git config --global user.name 
   
   git config --global user.email 
   ```

   

3. 建立代码仓库：

   给Demo项目建立代码仓库，进入Demo目录，执行<code>git init</code>

4. 仓库创建完成后，会在Demo项目的根目录下生成一个隐藏的.git目录，这个 目录就是用来记录本地所有的Git操作的

   >  如果你想要删除本地仓库，只需要删除这个目录就行了。

5. 提交本地代码

   1. 添加要提交的代码

      ```nginx
      git add build.gradle
      ```

      

   2. 提交代码

   3. ```nginx
      git commit -m "First commit."
      ```

      -m参数加上提交的描述信息，没有描述信息的提 交被认为是不合法的

   4. 的

6. 忽略某些文件不提交

   如，修改app/.gitignore文件内容

   ```groovy
   /build
   /src/test
   /src/androidTest
   
   ```

   

7. 查看修改内容

   `git status`

8. 查看文件内变动的内容

   `git diff`

9. 撤销未提交的修改,只能撤销还没有执行过add命令的文件

   ```kotlin
   git checkout app/src/main/java/com/example/playvideotest/MainActivity.kt
   ```

   已经执行过add命令的，通过reset命令

   ```kotlin
   git reset HEAD app/src/main/java/com/example/playvideotest/MainActivity.kt
   ```

10. 查看历史提交记录

    

    `git log`

    查看具体某一条记录，通过执行id

    

    `git log e09eeec7fb75fdafafe01753fbc7c0cd94c86003`

    ![image-20221122104512958](C:\Users\dpzxka\AppData\Roaming\Typora\typora-user-images\image-20221122104512958.png)

    查看近几次提交

    `git log -l`

11. 
