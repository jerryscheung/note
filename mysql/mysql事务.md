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

<p>脏读(Dirty Reads)：一个事务读取到了另一个事务未提交的数据。<b>一个事务读取到了另一个事务未提交
的数据，如果另一个事务回滚，那么就不合符一致性要求</b></p>
<img src="https://github.com/jerryscheung/note/blob/master/img/mysql/mysql%E5%B9%BB%E8%AF%BB.png" target="_blank"/>

<p>不可重复读(Non-Repeatable Reads)：一个事务在读取某些数据后的某个时间，再次读取该数据，却
发现其读出的数据已经发生了改变，或某些记录已经被删除了。这种现象叫做“不可重复读”。
<b>一个事务内部的相同查询语句在不同时刻读出的结果不一致，不符合隔离性</b></p>
<img src="https://github.com/jerryscheung/note/blob/master/img/mysql/mysql%E4%B8%8D%E5%8F%AF%E9%87%8D%E5%A4%8D%E8%AF%BB.png" target="_blank"/>

<p>幻读(Phantom Reads)一个事务按相同的查询条件重新读取以前检索过的数据，却发现其它事务插入了满足其查询条件的新数据，这种现象称为“幻读”。
<b>一个事务读取到了另一个事务提交的新增数据，不合格隔离性</b></p>


<h2>事务隔离级别</h2>
<a href="https://www.contrib.andrew.cmu.edu/~shadow/sql/sql1992.txt" target="_blank">SQL1992规范</a>


<h2>mysql锁</h2>



<table border="0" width="50%" height="50%"  align="left" cellspacing="0" >
  <tr>
    <th>隔离级别</th>
    <th>脏读(Dirty Read)</th>
    <th>不可重复读(NonRepeatable Read)</th>
    <th>幻读(Phantom Read)</th>
  </tr>
  <tr>
    <td>读未提交(Read-Uncommitted)</td>
    <td>Y</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td>读已提交(Read-Committed)</td>
    <td>N</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td>可重复读(Repeatable-Read)</td>
    <td text-align="center">N</td>
    <td>N</td>
    <td>Y</td>
  </tr>
  <tr>
    <td>可串行化(Serializable)</td>
    <td>N</td>
    <td>N</td>
    <td>N</td>
  </tr>
</table>

<p>数据库的事务隔离级别越严格，并发带来的副作用越小，但付出的代价越更高，因为事务隔离实质上
就是使事务在一定程度上“串行化”进行，这显然与“并发”是矛盾的。同时，不同的应用对读一致性和事务
隔离程度的要求也是不同的，比如许多应用对“不可重复读”和“幻读”并不敏感，可能更关心数据并发访问
的能力。<b>MySQL默认的事务隔离级别是“可重复读”，Spring的隔离级别基于MySQL</b></p>
<br>

<h2>mysql 锁</h2>
<h3>表锁</h3>
<p>每次操作锁住整张表。开销小，加锁快(因为找到表直接加锁)；不会出现死锁；锁的粒度大，发生锁冲突概率最高，
并发度最低。</p>

<h3>行锁</h3>
<p>每次操作锁住一行数据。开销大，加锁慢(因为要找出该条记录)；会出现死锁；锁的粒度小，发生锁冲突的概率最低，
并发度最高。</p>

<h3>总结</h3>
<p>MyISAM在执行查询语句SLELECT前，会自动给涉及的所有表加读锁，在执行UPDATE，INSERT，DELETE操作会自动给涉及的表加锁。</p>
<p>InnoDB在执行查询语句SELECT时，因为有MVCC机制不会加锁。但是UPDATE,INSERT,DELETE操作会加行锁。</p>
<p><b>读锁会阻塞写，但是不会阻塞读。而写锁则会把读和写都阻塞。可串行化，开启事务，针对该行查询都会加锁。</b></p>

<h3>间隙锁</h3>
<p>间隙锁，锁的就是两个值之间的空隙。MySQL默认级别是Repeatable-Read，间隙锁在某些情况下可以解决幻读问题。</p>
<p><b>什么为间隙锁？</b><br>id为(4,8),(8,15),(15,正无穷)，这三个区间，在事务1中执行
UPDATE user SET name = "jerrys" WHERE id > 4 AND id < 15;  其它事务没法在这个范围所包含的所有记录以及行
记录所在间隙里插入或修改任何数据，即id在(4,15)区间都无法修改数据。<b>间隙锁是在可重复读隔离级别才会生效</b></p>
