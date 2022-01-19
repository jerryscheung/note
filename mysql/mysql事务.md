<h1> mysql事务 </h1>

<a href="">email jerryscheung@163.com</a><br>
<br>
<p><a href="#目录">目录</a></p>
<ul><li><a href="#事务的四大特性">事务的四大特性</a></li></ul><br>


<h2> 什么是事务？</h2>
<p>事务是数据库管理系统执行过程中的一个逻辑单位，它能够保证每个操作单元的原子性
一致性，隔离性，持久性。</p>


<br>

<h2 id="事务的四大特性"> 事务的四大特性 </h2>
<p> 原子性(Atomicity)：事务是一个不可分割的工作单元，事务中的操作要么都发生，要么都不发生。<b>undolog解决</b></p>
<p> 隔离性(Isolation)：事务的隔离性是指一个事务的执行不能被其它事务干扰。<b> MVCC解决</b></p>
<p> 持久性(Durability)：导致对数据库表变更的操作，一旦事务完成提交，它就是永久性的。<b>redolog | double write buffer解决</b></p>
<p> 一致性(Consistency)：数据库的完整性约束没有被破坏，事务执行前后都合法状态。</p>

<h2> 并发事务带来的问题</h2>
<p> 更新丢失(Lost Update)或者脏写：当多个事务选择同一行(而且数据都是一致的)进行对数据变更的操作，由于每个事务都
不知道其它事务的存在，就会发生丢失更新问题，<b>最后的更新覆盖了由其它事务所做的更新</b>。比如 对一列数据10，一个事务对其
更改减5，提交完，另一个事务也对其更改减3，提交完。10-5=5，10-3=7，写入数据库导致数据丢失。</p>

<p>脏读(Dirty Reads)：一个事务读取到了另一个事务未提交的数据</p>
<img src="https://github.com/jerryscheung/note/blob/master/img/mysql/mysql%E5%B9%BB%E8%AF%BB.png" target="_blank"/>

<p>不可重复读(Non-Repeatable Reads)：一个事务在读取某些数据后的某个时间，再次读取该数据，却
发现其读出的数据已经发生了改变，或某些记录已经被删除了。这种现象叫做“不可重复读”。</p>
<img src="https://github.com/jerryscheung/note/blob/master/img/mysql/mysql%E4%B8%8D%E5%8F%AF%E9%87%8D%E5%A4%8D%E8%AF%BB.png" target="_blank"/>


