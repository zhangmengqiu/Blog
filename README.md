#### title: 搭建这个博客
#### author: Tao Feng
#### tags: Article Archive
#### date: 2016-12-04 22:03:00

### 简单介绍

这个博客是基于[hexo](https://hexo.io)的博客框架，使用了一个腾讯前端开发的大牛[Litten](https://litten.me)开源的hexo主题[yilia](https://github.com/litten/hexo-theme-yilia)，详细的安装过程分别在hexo和yilia的首页上有详细的介绍，可以自行参考。hexo使用的是[Node.js](https://nodejs.org/en/)，是使用JavaScript编写的服务端框架，打开它的首页就能看到相应系统的安装包，另外还需要有[npm](https://www.npmjs.com)，是Node.js的软件仓库，也是包管理工具，用它安装Node.js的程序很方便。npm的安装也可以参考hexo在线文档提供的指导。

### 安装过程

首先需要安装的是[homebrew](http://brew.sh)，然后用brew安装npm，再到Node.js主页下载OSX的安装包，我是用的OSX，所以只介绍OSX上的安装方式。

```bash
#安装homebrew
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

#安装nmp和git
brew update -y
brew install nmp git -y
```

下载了Node.js安装包并安装好之后，就可以开始安装hexo了。

```bash
#安装hexo-cli
npm install hexo-cli -g
#一般将博客根目录放用户主目录下，所以回到主目录
cd ~
#初始化博客根目录，这个会下载github上的hexo博客框架
hexo init Myblog
#进入根目录
cd Myblog
#安装hexo博客
nmp install
#现在就可以运行博客的服务了,默认URL为http://localhost:4000
hexo server
```
到现在你已经成功安装了hexo，但是默认的demo模板并不很好看，所以要换一个好看些的。那么有很多其他高大上的模板，每个模板都有一个个性化的配置文件，为了了解，我们先看下博客根目录下的目录结构。
可以自己用brew命令安装tree工具，然后使用tree命令展示Myblog的目录结构。

	Myblog/
        _config.yml
        package.json
        db.json
        node_modules/
        source/
        themes/
        scaffolds/
       
我们需要关注的是_config.yml和thems/文件夹，前者是一个全局的配置，指定了使用哪一个主题等信息，后者是具体的主题存放的目录，我们要下载的yilia主题就是要放在themes文件夹下。
具体的命令是：
```bash
#进入博客根目录，如果已经是就忽略本命令
cd ~/Myblog
#从git下载[pull]
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```
相应的在yilia的文件夹下也有一个_config.yml文件，是对yilia主题具体的配置，相关配置参考[_config.yml.yilia](_config.yml.yilia)或者yilia的github的[README.md](https://github.com/litten/hexo-theme-yilia)。相应的全局的_config.yml也要配置，涉及到theme,deploy,etc相关信息，到yilia上拿一个模板改改就好。

写博客用什么工具呢，在github上也有一个针对hexo开发的网页版带预览的Markdown格式文件编辑工具，叫[hexo-admin](https://github.com/jaredly/hexo-admin),虽然用着有些小bugs，但是寥胜于无吧。安装也很简单：
```bash
cd ~/Myblog
npm install --save hexo-admin

#运行服务器
hexo server -d
```
安装完，运行服务器后，默认URL在http://localhost:4000/admin ，看着就会用。

### 部署

详细的部署方法可以参考[hexo的官方文档](https://hexo.io/docs/deployment.html),常用的是使用rsync和git方式，一般用git就好，配置需要在Myblog下的_config.yml中修改。格式如下：
```_config.yml
deploy:
     type: git
     repository: git@github.com:osgee/osgee.github.io.git
     branch: master
```
需要注册github账号，然后新建一个repository，名字是[username].github.io，其中[username]为你注册的用户名。然后进入repository的页面，点击【设置】按钮，最下面有个[Delploy keys], 里面可以添加你的ssh公钥，一般这样查看你的ssh公钥：
```bash
cat .ssh/id_rsa.pub
```
这里的id_rsa.pub的前缀是根据公认的取名规则。具体的文件名还要看当时生成密钥对的导出名。如果没有产生过rsa密钥对，就如下方式产生：
```bash
#如果~目录下没有.ssh/，就要生成.ssh目录，如果有就跳过本命令
cd ~
mkdir .ssh
#检查目录的访问权限，正确的应为drwx------，用数字表示即为700
ls -la ~|grep .ssh
#如果不是drwx------，需要修改访问权限
sudo chmod 700 -r .ssh 
#生成ssh握手密钥对
ssh-keygen -t rsa -b 2048 -P "" -f ~/.ssh/id_rsa
```
至此，在~/.ssh目录下已经生成了id_rsa和id_rsa.pub两个文件，前者是私钥，后者是公钥，注意，我们需要添加到github上的是公钥。顾名思义，公钥就是要给其他人的密钥，所以给github提交的就是你的公钥，一个公钥只有一个私钥才能对应解密，所以私钥可以用来验证你的身份。

用hexo-admin写完第一个博客，各个配置都改好后，现在可以开始部署了。部署也分两步，第一步，产生静态的网页文件，第二步，将文件复制到服务器，这两个步骤都是由hexo自动完成的，我们需要做的就是配置好文件后，使用命令部署：
```bash
#生成静态网页文件
cd ~/Myblog
hexo generate
#复制文件到服务器
hexo deploy
```
现在，就可以访问https://[username].github.io 查看博客的效果了。

PS：可能有很多细节的地方没有写上来，而且很多细节写上来反而让读者困惑，所以自己实际部署时还是要多上网搜搜相关的指导，不过都以官网文档为主。有遇到不能解决的问题也可以通过邮件的形式联系我，希望能够帮助你搭建一个高大上的个人博客。
			


