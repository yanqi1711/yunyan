<hr>
<h2>html:
offline: true
export_on_save:
html: true</h2>

<p>TC397做为MCU，是控制器电源lifetime的管理者</p>
<h2>测试</h2>
<p>5G云控控制器支持电压<code>+12V~36V</code>，正常情况下使用12V供电最好</p>
<p>控制器上电后，测试各个模块都正常供电，即imx8qm系统启动、5G系统启动并能够被8qm检测到、DMD3A模块启动并能被8qm检测到，则说明MCU正常</p>

