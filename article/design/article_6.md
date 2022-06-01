> 专注于PHP、MySQL、Linux和前端开发，感兴趣的感谢点个关注哟！！！文章整理在[GitHub](https://github.com/7small7),[Gitee](https://gitee.com/bruce_qiq)主要包含的技术有PHP、Redis、MySQL、JavaScript、HTML&CSS、Linux、Java、Golang、Linux和工具资源等相关理论知识、面试题和实战内容。

@author:[7small7](https://github.com/7small7)。

@source:[公众号-菜鸟成长学习笔记](/site/)。

@project:[微信小程序 程序员面试题大全](/site/)。


## 抢红包方案设计

在一个抢红包的营销活动中，有100万人参与，但是红包金额只有100个。你该如何设计这一的系统?下面是大致的一个需求。

1. 每一个用户只能参与一次，参与过的用户无法点击红包。

2. 红包金额是固定的，并且每一个红包的金额是随机的。

3. 每一个红包的金额都是有上限和下限的。

4. 用户抽中红包之后，立即自动提现到对应账号。(可以是微信、银行卡、支付宝等账号)。

5. 如果红包金额已被抢完，没有抢的用户仍然可以参与。