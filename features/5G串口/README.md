<hr>
<h2>html:
offline: true
export_on_save:
html: true</h2>

<h2>测试工具</h2>
<p>M12转TTL-&gt;USB串口线束：
</p>
<p>测试软件：<a href="https://www.putty.org/" rel="nofollow">PuTTY</a></p>
<h2>测试</h2>
<p>要求：通过车载以太网口可以正常访问移远5G系统</p>
<p>车载以太网口位置：
<img src="../../public/img/%E8%BD%A6%E8%BD%BD%E4%BB%A5%E5%A4%AA%E7%BD%91%E5%8F%A3.png" alt="alt text"></p>
<ol>
<li>
<p>使用测试线束将电脑端USB接口与控制器端车载以太网口连接起来，电脑端的设备管理器上会出现一个端口，记录下来：
</p>
</li>
<li>
<p>使用putty连接这一个COM端口，配置如下，其中COM选择设备管理器中出现的：
</p>
</li>
<li>
<p>上电后，会出现5G开机打印信息，待系统启动完毕，依次输入用户名和密码，用户名<code>root</code>，密码<code>oelinux123</code>

如图正常进入系统，说明该项功能正常</p>
</li>
</ol>

