


### 1、下载安装git
### 2、下载安装node.js
### 3、安装hexo
1. 利用npm命令即可安装，在任意位置点击鼠标右键，选择git bash
  ![](index_files/423443125.png)

2. 输入命令：npm install -g hexo

### 4、初始化hexo
1. 创建文件夹（我的是在E盘创建的Hexo）

2. 在Hexo文件下，右键运行Git Bash，输入命令：hexo init
  ![](index_files/423545968.png)

3. 在_config.yml,进行基础配置
  ![](index_files/423578765.png)

### 5、本地浏览博客
1. 输入命令： hexo g 然后输入命令：hexo s
  ![](index_files/423652125.png)

2. 在浏览器中输入： localhost：4000 ，就可以进行访问，效果如下：
  ![](index_files/423683609.png)

### 6、写文章
1. 在E:\Hexo\source\_posts文件下，新建.md文件就可以写文章

### 7、部署到github上
1. 登录你的github账号，新建仓库，名为（用户名.github.io）固定写法。
  ![](index_files/423833046.png)

2. 将终端切换到blog目录下，打开配置文件_config.yml配置
```
deploy:
  type: git
  repository: git@github.com:yuansuixin/yuansuixin.github.io.git
  branch: master
```
或者：
![](index_files/424120296.png)

##### 注意：
1. 在配置所有的_config.yml文件时（包括theme中的），在所有的冒号:后边都要加一个空格，否则执行hexo命令会报错，切记 切记
2. 若执行命令`hexo deploy`仍然报错：无法连接git或找不到git，则执行如下命令来安装[hexo-deployer]
  npm install hexo-deployer-git --save
  ![](index_files/424472515.png)
### 8、在浏览器打开http://用户名.github.io

### 9、发布文章
1. 终端cd到blog文件夹下，新建文章：
```
hexo new 'postname'
```
2. 名为postname.md的文件会建在\blog\source\_posts下
3. 文章编辑完成后，终端切换回去，执行以下命令发布：
```
#生成静态页面
hexo g
# 将文章部署到github
hexo d
```



