## session 

我们都知道 session 是为了直接用 cookies 处理会话的诸多问题，所以先说 cookie 做会话的问题。

* cookie 太笨重了，因为可以说他是明文的（用某种可逆的算法加密也一样），拿来发来发去的浪费流量。虽然在真实环境中用 session 看起来也是很笨重的一坨。
* cookie 是明文的，而且可以给客户端随意修改发送，安全问题。细说就是个大大大 project 了。佩服那些搞安全的。

session 就是解决这两个问题的方案。`session 的原理就是将原本发给用户的 cookie 保存起来（一般是内存中）并给他打一个编号。形成一个一一对应的信息表。然后将编号发给用户，用户下次发请求来的时候带上这个编号，服务端在用编号的获取身份信息。`

因为编号比整个信息小，所以解决第一个问题。因为是保存在服务端的，客户端只有一个编号，客户端改了编号发给服务端，错非你获得了别人的编号（CSRF），不然服务端只会当找不到对应信息处理，这就解决了第二个问题。

## session 与证书

理论上我们只要获得了他人的 session 我们就能登录上他的账号，如果站点安全防护性低的话。那么如果站点生成 session id 的方法太过简单的话。只要写个简单的服务器，就能暴力破解出其有效的 session_id。所以这个生成算法很讲究，但是复杂的算法，需要成本，不可能每个引用都自己搞一个。还好我们有数学家，这个高级理论的提供者。他们提供了带参数的哈希算法。同一个值用配合不同的参数使用哈希算法出来的结果完全不同。而且算法非常复杂不能通过简单的列举计算出参数。所以是相对安全的。这样降低了成本也提高了安全性。

koa-session 的 signed 属性。它的逻辑是使用一种带密钥的哈希算法加密 session-key: session-value 形成字符串，并通过cookie发送给客户端。作用是为了防止用户私自改动 cookie 来尝试发送请求。因为你改动了 session-key 的value，没有密钥你算不出签名。加大了攻击的成本。
