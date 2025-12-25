<hr>
<h2>html:
offline: true
export_on_save:
html: true</h2>

<p>GNSS：支持GPS/北斗定位</p>
<h2>测试工具</h2>
<p>5G云控天线：
<img src="../../public/img/5G%E5%A4%A9%E7%BA%BF.jpg" alt="alt text"></p>
<p>天线接入方案：
<img src="../../public/img/%E5%A4%A9%E7%BA%BF%E6%8E%A5%E5%85%A5%E6%96%B9%E6%A1%88.png" alt="alt text"></p>
<h2>测试</h2>
<p>进入v2x系统，使用<code>athelper</code>工具，若ignss为locked，GPS功能则正常；在室外空旷情况下，插了天线，一般上电后3分钟将会<code>locked</code>，若长时间没有lock说明有问题</p>
<pre><code># 进入v2x系统, 密码root
ssh root@192.168.62.199
# 使用athelper工具
athelper
# 查询gnss状态
at+ignss
</code></pre>
<p>截图：
<img src="../../screenshots/ignss.png" alt="alt text"></p>

