# 下载不同版本
创建的github项目，如果想要项目中使用，支持composer require就需要发布到下面平台
https://packagist.org/packages/

删除依赖
composer remove zhouzeken/wxsdk


引入不同版本符号意义
波浪号版本范围 (~) ：composer require zhouzeken/wxsdk "~1.2.5"【相当于>=1.2.5 < 1.3.0小版本控制】
入符号版本范围 (^) ：composer require zhouzeken/wxsdk "^1.2.5"【相当于>=1.2.5 < 2.0.0大版本控制】
固定版本：composer require zhouzeken/wxsdk "1.2.5"
固定某大版本：composer require zhouzeken/wxsdk "1.2.*"



# 安装 thinkphp 最新的发行版 到 tp 目录
composer create-project topthink/think tp

# 安装 thinkphp 6.0.* 开发版到 tp 目录
composer create-project topthink/think tp 6.0.*

# 安装 thinkphp 5.1.* 发行版到 tp 目录
composer create-project topthink/think tp 5.1.*

 
# 安装 laravel 最新的发行版 到 blog 目录
composer create-project --prefer-dist laravel/laravel blog

# 安装 laravel 6.x 版本到 到 blog 目录
composer create-project --prefer-dist laravel/laravel blog "6.*"
