http://web.jobbole.com/90595/

ession与cookie的区别：
1，session 在服务器端，cookie 在客户端（浏览器）
2，session 存在在服务器的一个文件里（默认），不是内存
3，session 的运行依赖 session id，而 session id 是存在 cookie 中的，也就是说，如果 浏览器禁用了 cookie ，同时 session 也会失效（当然也可以在 url 中传递）
4，session 可以放在 文件，数据库，或内存中都可以。
5，用户验证这种场合一般会用 session
因此，维持一个会话的核心就是客户端的唯一标识，即 session id