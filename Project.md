## 目录

1. [项目规范](#project)

<a name="project"></a>
## 项目规范

1. <font color=799042>【参考】</font>一个项目有以下三个基本的项目环境：本地环境(local),测试开发环境(dev),线上生产环境(prod)

1. <font color=febd2c>【推荐】</font>项目使用软件是，优先选择流行稳定的版本。

        1) PHP 版本优先考虑 PHP 7。
        2) 数据库软件 应该 优先选择 MySQL，因为其使用率最高。选择版本时 应该 优先考虑 MySQL 5.7。
        3) 其他软件 应该 优先选择 流行 稳定 版本。

1. <font color=bc0008>【强制】</font>线上环境使用的软件绝不使用 Beta 或者其他不稳定发行版。

1. <font color=799042>【参考】</font>本地环境(local)统一使用域名 .test 作为后缀；统一使用 IP 192.168.10.10 作为 hosts 绑定地址，如：192.168.10.10 project-name.test

1. <font color=bc0008>【强制】</font>测试开发环境(dev)除了域名等其他独立应用配置以外，环境跟 prod 保持高度一致性。

1. <font color=799042>【参考】</font>测试开发环境(dev)可以的话，与 prod 使用同台机器。

1. <font color=bc0008>【强制】</font>线上生产环境(prod)出于安全考虑，线上环境只开放以下端口：

        80 HTTP 
        443 HTTPS
        22 SSH

1. <font color=bc0008>【强制】</font>仅开发环境中使用的扩展包，安装时使用 --dev 参数。避免生产环境无用的负载。

	``` php
	composer require laracasts/generators --dev
	```

1. <font color=bc0008>【强制】</font>本地 .env 里添加变量时同步到 .env.example 中，以免影响其他项目参与者的工作。

1. <font color=bc0008>【强制】</font>配置信息通过 config() 来读取，所有的 .env 配置信息通过 config() 来读取，绝不在配置文件以外的范围使用 env()。

	<font color=977919>说明:</font>
	
		1) 定义分明，config() 是配置信息，env() 只是用来区分不同环境；
		2) 统一放置于 config 中还可以利用框架的 配置信息缓存功能 来提高运行效率；
		3) 代码健壮性， config() 在 env() 之上多出来一个抽象层，会使代码更加健壮，更加灵活。

    <font color=29965b>正例:</font>
    ``` php
    //.env 文件：
    CDN_DOMAIN=cdn.domain.com

	//config/config.php 文件：
	'cdn_domain' => env('CDN_DOMAIN', null),

	//获取config：
	config('cdn_domain');
    ```

    <font color=fa4113>反例:</font>
	
		1) 硬代码，直接写死。
		2) 写死在 config/app.php 文件中。
		3) 存储于 .env 文件中，使用 env() 方法直接读取。

1. <font color=bc0008>【强制】</font>每一个项目都包含一个 readme.md 文件，readme 里书写这个项目的简单信息。作用主要有两个，一个是团队新成员可从此文件中快速获悉项目大致情况，另一个是部署项目时可以作为参考。

1. <font color=bc0008>【强制】</font>文档页面排版遵循 [中文文案排版指北](https://github.com/sparanoid/chinese-copywriting-guidelines) ，在此基础上：
中文文档请使用全角标点符号；必须 遵循 Markdown 语法，勿让代码显示错乱；原文中的双引号（” “）请代换成中文的引号（『』符号）。所有的 「加亮」、「加粗」和「链接」都需要在左右保持一个空格。

1. <font color=799042>【参考】</font>readme.md 文档包含以下内容：

	1.  「项目概述」- 介绍说明项目的一些情况，类似于简单的产品说明，简单的功能描述，项目相关链接等，500 字以内；
	1. 「运行环境」- 运行环境说明，系统要求等信息；
	1. 「开发环境部署 / 安装」- 一步一步引导说明，保证项目新成员能最快速的，没有歧义的部署好开发环境；
	1. 「服务器架构说明」- 最好能有服务器架构图，从用户浏览器请求开始，包括后端缓存服务使用等都描述清楚（主要体现为软件的使用），配合「运行环境」区块内容，可作为线上环境部署的依据；
	1. 「代码上线」- 介绍代码上线流程，需要执行哪些步骤；
	1. 「扩展包说明」- 表格列出所有使用的扩展包，还有在哪些业务逻辑或者用例中使用了此扩展包；
	1. 「自定义命令列表」- 以表格形式罗列出所有自定义的命令，说明用途，指出调用场景；
	1. 「队列列表」- 以表格形式罗列出项目所有队列接口，说明用途，指出调用场景。

1. <font color=febd2c>【推荐】</font>工具的统一，是为了方便工作流的统一，还有工具使用经验的传承。团队里的成员，经常需要互相使用对方电脑来讨论问题、查看某段代码、Debug 某个功能，工具统一起来后，你会发现，虽然是别人的电脑，工具使用起来是熟悉的，用起来就跟自己的电脑一样顺手，自然的工作效率就会提高。

	- 系统：MacOS or Windows
	- 开发环境：Docker
	- 代码仓库工具：Git
	- 编辑器：Vscode or PHPStorm 
	- 编辑器代码格式化：EditorConfigPHP 
	- 代码风格矫正器：PHP-CS-Fixer 
	- 命令行工具：iTerm2 or Xshell 
	- 浏览器：Chrome 
	- 数据库工具：Navicat Premiun
	- 设计工具：PhotoShop
	- 设计图管理工具：蓝湖