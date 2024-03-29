# SSO 单点登录

SSO 是在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统。（登录微博，再访问新浪邮箱📮不用再登录）

# 需要解决的问题

* 不同的站点浏览器端如何获取相同的token。或者最后再统一为同一个。
* 注销token之后如何让全部站点同步删除。

## 大体思路

一个方案就是将原本分布在不同站点的session统一为一个。并将于session相关的逻辑抽象（如登录安全监测，黑名单等逻辑）出来成为实现成一个服务器。至于客户端的session共享可以使用 cookies 的domain属性存储在同一个二级域名上。

## 不同域的cookies共享

根据上面的方案，我们不同域不能共享 cookies 。那么这个网站也需要登录。而且登录得到的session 可能是不同的备份不好管理。为了实现共享。需要考虑两种情况，第一，有其他网站登录，如何获取。第二，大家都没有登录，如何将登录后的 cookies 共享给其他网站。

只有同域才能让浏览器去获取 cookies 。所以可行的思路就是让网站重定向到同域名的某个站点中如 login.xxx.com 。并用脚本让浏览器向服务器发送一个情况，那么这个HTTP请求中的 cookies 就有有token。站点再往上面的 session 服务发送token验证等逻辑。当login.xxx.com 收到验证合格的信息。就向客户端发送302重定向会不同域名的站点，并将在cookie中存放token。（完成）

第二中情况。如果没有token，到login.xxx.com也登录失败，那么全部网站都没有登录。这就需要在不同域的网站中进行操作。让用户填写登录信息，并将登录信息发送给session后端验证账户是否可登陆。如果可以那么将token保存在本地。并将token通过带密钥的方式加密，往同域名站点发送请求。请求的做用是将token保留在通域名下。在重定向为原来的站点。

（完）
