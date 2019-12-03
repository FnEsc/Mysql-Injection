<div class="toc">
<h1>目录</h1>
<ul>
<li><a href="#一搭建环境--sqli-sql介绍">一、搭建环境 &amp; sqli-sql介绍</a></li>
<li><a href="#二第一部分--page-1-basic-challenges-background-1-基础知识">二、第一部分__page-1 Basic Challenges Background-1 基础知识</a>
<ul>
<li><a href="#系统函数">系统函数</a></li>
<li><a href="#字符串连接函数">字符串连接函数</a></li>
<li><a href="#常用语句">常用语句</a></li>
<li><a href="#一般常识语句">一般常识语句</a></li>
<li><a href="#union操作符">UNION操作符</a></li>
<li><a href="#运算符相关">运算符相关</a></li>
<li><a href="#sql-shell">sql shell</a></li>
</ul>
</li>
<li><a href="#三答题模式-基础知识-less14">三、答题模式-基础知识-Less1~4</a>
<ul>
<li><a href="#less-1">Less-1</a></li>
<li><a href="#less-2">Less-2</a></li>
<li><a href="#less-3">Less-3</a></li>
<li><a href="#less-4">Less-4</a></li>
<li><a href="#less-11">Less-11</a></li>
</ul>
</li>
<li><a href="#四background-2-盲注的讲解">四、Background-2 盲注的讲解</a>
<ul>
<li><a href="#基于布尔sql盲注构造逻辑判断">基于布尔sql盲注——构造逻辑判断</a>
<ul>
<li><a href="#mid函数">mid()函数</a></li>
<li><a href="#substr函数">substr()函数</a></li>
<li><a href="#left函数">Left()函数</a></li>
<li><a href="#ord函数">ord()函数</a></li>
<li><a href="#ascii函数">ascii函数</a></li>
<li><a href="#正则注入">正则注入</a></li>
</ul>
</li>
<li><a href="#思考一个问题limit是限定输出并不是限定正则哦">思考一个问题？limit是限定输出，并不是限定正则哦。</a></li>
<li><a href="#基于报错的sql盲注构造payload让信息通过错误提示回显出来">基于报错的SQL盲注——构造payload让信息通过错误提示回显出来</a></li>
<li><a href="#基于时间的sql盲注延时注入">基于时间的SQL盲注——延时注入</a>
<ul>
<li><a href="#利用sleep延时注入语句">利用sleep()延时注入语句</a></li>
<li><a href="#性测测试延迟注入">性测测试延迟注入</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#五答题模式-盲注的讲解-less-56">五、答题模式-盲注的讲解-Less-5~6</a>
<ul>
<li><a href="#less-5">Less-5</a></li>
<li><a href="#less-6">Less-6</a>
<ul>
<li><a href="#注意单引号和双引号不能混用"><strong>注意：单引号和双引号不能混用！！</strong></a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#六background-3-导入导出相关操作的讲解">六、Background-3 导入导出相关操作的讲解</a>
<ul>
<li><a href="#1-load-file导出文件">1. load_file()导出文件</a></li>
<li><a href="#2-文件导入到数据库">2. 文件导入到数据库</a></li>
<li><a href="#3-导入到文件">3. 导入到文件</a></li>
</ul>
</li>
<li><a href="#七答题模式-background-3-导入导出相关操作的讲解-less-716">七、答题模式-Background-3 导入导出相关操作的讲解-Less-7~16</a>
<ul>
<li><a href="#less-7">Less-7</a></li>
<li><a href="#less-8">Less-8</a></li>
<li><a href="#less-9">Less-9</a></li>
<li><a href="#less-10">Less-10</a></li>
<li><a href="#less-11-1">Less-11</a></li>
<li><a href="#less-12">Less-12</a></li>
<li><a href="#less-13">Less-13</a></li>
<li><a href="#less-14">Less-14</a></li>
<li><a href="#less-15">Less-15</a></li>
<li><a href="#less-16">Less-16</a></li>
</ul>
</li>
<li><a href="#八background-4-增删改函数介绍">八、Background-4 增删改函数介绍</a>
<ul>
<li><a href="#增加-insert">增加 Insert</a></li>
<li><a href="#删除-delete-drop">删除 delete drop</a></li>
<li><a href="#修改">修改</a></li>
</ul>
</li>
<li><a href="#九答题模式-less-17">九、答题模式-Less-17</a>
<ul>
<li><a href="#less-17">Less-17</a>
<ul>
<li><a href="#指引思考为什么我们不在username处进行构造呢">指引思考：为什么我们不在username处进行构造呢？</a>
<ul>
<li><a href="#addslashes">addslashes()</a></li>
<li><a href="#stripsslashes">stripsslashes()</a></li>
<li><a href="#mysql-real-escape-string">mysql_real_escape_string()</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><a href="#十background-5-http头部介绍">十、Background-5 HTTP头部介绍</a></li>
<li><a href="#十一答题模式-less-1822">十一、答题模式-Less-18~22</a>
<ul>
<li><a href="#less-18">Less-18</a></li>
<li><a href="#less-19">Less-19</a></li>
<li><a href="#less-20">Less-20</a></li>
<li><a href="#less-21">Less-21</a></li>
<li><a href="#less-22">Less-22</a></li>
</ul>
</li>
<li><a href="#十二第二部分--page-2-advacned-injection-less-2328a">十二、第二部分__page-2 Advacned injection Less-23~28a</a>
<ul>
<li><a href="#less-23">Less-23</a></li>
<li><a href="#less-24">Less-24</a></li>
<li><a href="#less-25">Less-25</a></li>
<li><a href="#less-25a">Less-25a</a></li>
<li><a href="#less-26">Less-26</a></li>
<li><a href="#less-26a">Less-26a</a></li>
<li><a href="#less-27">Less-27</a></li>
<li><a href="#less-27a">Less-27a</a></li>
<li><a href="#less-28">Less-28</a></li>
<li><a href="#less-28a">Less-28a</a></li>
</ul>
</li>
<li><a href="#十三background-6-服务器两层架构">十三、Background-6 服务器（两层）架构</a></li>
<li><a href="#十四答题模式-less-2931">十四、答题模式-Less-29~31</a>
<ul>
<li><a href="#less-29">Less-29</a></li>
<li><a href="#less-30">Less-30</a></li>
<li><a href="#less-31">Less-31</a></li>
</ul>
</li>
<li><a href="#十五background-7-宽字节注入">十五、Background-7 宽字节注入</a>
<ul>
<li><a href="#首先介绍宽字节注入">首先介绍宽字节注入：</a></li>
</ul>
</li>
<li><a href="#十六答题模式-less-3237">十六、答题模式-Less-32~37</a>
<ul>
<li><a href="#less-32">Less-32</a></li>
<li><a href="#less-33">Less-33</a></li>
<li><a href="#less-34">Less-34</a></li>
<li><a href="#less-35">Less-35</a></li>
<li><a href="#less-36">Less-36</a></li>
<li><a href="#less-37">Less-37</a></li>
</ul>
</li>
<li><a href="#十七第三部分--page-3-stacked-injection-background-8-stacked-injection">十七、第三部分__page-3 Stacked injection Background-8 stacked injection</a>
<ul>
<li><a href="#0x01-原理介绍">0x01 原理介绍</a></li>
<li><a href="#0x02-堆叠注入的局限性">0x02 堆叠注入的局限性</a></li>
<li><a href="#0x03-各个数据库实例介绍">0x03 各个数据库实例介绍</a></li>
</ul>
</li>
<li><a href="#十八答题模式-less-3845">十八、答题模式-Less-38~45</a>
<ul>
<li><a href="#less-38">Less-38</a></li>
<li><a href="#less-39">Less-39</a></li>
<li><a href="#less-40">Less-40</a></li>
<li><a href="#less-41">Less-41</a></li>
<li><a href="#less-42">Less-42</a></li>
<li><a href="#less-43">Less-43</a></li>
<li><a href="#less-44">Less-44</a></li>
<li><a href="#less-45">Less-45</a></li>
</ul>
</li>
<li><a href="#十九background-9-order-by-后的injection">十九、Background-9 order by 后的injection</a></li>
<li><a href="#二十答题模式-less-4653">二十、答题模式-Less-46~53</a>
<ul>
<li><a href="#less-46">Less-46</a></li>
<li><a href="#less-47">Less-47</a></li>
<li><a href="#less-48">Less-48</a></li>
<li><a href="#less-49">Less-49</a></li>
<li><a href="#less-50">Less-50</a></li>
<li><a href="#less-51">Less-51</a></li>
<li><a href="#less-52">Less-52</a></li>
<li><a href="#less-53">Less-53</a></li>
</ul>
</li>
<li><a href="#二十一第四部分page-4-challenges-答题模式less-5464">二十一、第四部分/page-4 Challenges 答题模式Less-54~64</a>
<ul>
<li><a href="#less-54">Less-54</a></li>
<li><a href="#less-55">Less-55</a></li>
<li><a href="#less-56">Less-56</a></li>
<li><a href="#less-57">Less-57</a></li>
<li><a href="#less-58">Less-58</a></li>
<li><a href="#less-59">Less-59</a></li>
<li><a href="#less-60">Less-60</a></li>
<li><a href="#less-61">Less-61</a></li>
<li><a href="#less-62">Less-62</a></li>
<li><a href="#less-63">Less-63</a></li>
<li><a href="#less-64">Less-64</a></li>
<li><a href="#less-65">Less-65</a></li>
</ul>
</li>
<li><a href="#二十二后记">二十二、后记</a></li>
</ul>
</div>
