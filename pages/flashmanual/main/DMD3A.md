<hr>
<h2>html:
offline: true
export_on_save:
html: true</h2>






<p>T形microusb线束：
</p>

<p>V2X刷写操作步骤较多，大致分为：刷写、升级、配置</p>
<p>注意：需提前安装驱动和刷写软件</p>

<ol>
<li><strong>GTL驱动</strong>：此驱动是刷写需要的驱动</li>
</ol>
<p>展开 gTL 文件夹，找到 driver 文件夹（如下图）根据操作系统类型选择运行 SetupX64.exe（64 位系统）或者 SetupX86.exe（32 位系统），根据提示安装</p>

<p>安装完成后确认
windows 徽标键——设置——应用，查看是否有如下程序</p>

<ol>
<li><strong>V2X驱动</strong>：此驱动是连接V2X需要的驱动</li>
</ol>
<p>需要电脑和底板使用T形microusb线束连接（连接靠近SIM卡槽一侧的usb接口），底板上电</p>

<p>1）打开安装包所在文件夹，将 rndismp6.sys 和 usb80236.sys 文件拷贝到电脑的 C:\Windows\System32\drivers 目录下，如果之前文件已经存在请做好备份</p>

<p>2）进入kindle_rndis.inf_amd64文件夹，右键&#34;5-runasadmin_register-CA-cer.cmd&#34;以管理员身份运行</p>

<p>3）将设备通过USB线与PC连接 打开设备管理器查看是否有未安装的驱动提示</p>
<p>4）下图 ，如有黄色感叹号标签的未安装驱动提示，右击更新驱动程序--浏览计算机以查找驱动程序软件--找到kindle_rndis.inf_amd64文件夹选中即可自动安装</p>

<p>如果没有发现未安装驱动测试提示，则该驱动被Windows默认识别为串口 解决方法：板卡正常上电连接 USB 口，在设备管理中注意观察有没有新COM口</p>

<p>右击新出的COM口——更新驱动程序——选中本地RNDIS驱动安装即可</p>

<p>5）确认安装是否完成，安装完成后，USB 连接设备，在设备管理器处会自动识别为如下设备</p>

<p>此时可以通过ssh进入V2X系统</p>
<pre><code># 进入v2x系统, 密码root
ssh root@192.168.62.199
</code></pre>
<p><strong>刷写软件介绍</strong></p>
<p>V2X系统刷写需要用到 gTL 软件，之后的系统升级和配置用的软件是gLMT</p>

<p><strong>系统刷写</strong>    需完成对应驱动的安装</p>
<p>打开gTL_V5.2.3.0_20220330文件夹，双击gTL.exe进入gTL程序</p>
<p>点击Muti-Download选项，进入Muti-Download 界面。点击GTL程序右上角选择 pks 文件，文件位于 gSafety_OBU_DMD3A_V7.4.2.1_ATU_SF_202211041915\4004 文件夹中</p>

<p>勾选十项要刷写的数据，其中除了Ptable 和 A_dtbs没有默认打勾 其他默认打勾</p>

<p>打勾Partions_table后，选择ptable.bin文件（在v7.4.2.1系统包的4004路径下）</p>

<p>打勾A_dtbs后，需要将文件类型改为所有 选择lc1860-DTVLVU4004-01.dtb文件</p>

<p>一共十项数据内容与图中对比</p>
<p>
</p>
<p>连接电脑与底板OTG 串口 靠近sim卡槽的串口，此时设备需要上电</p>
<p>Multi-Dowload 界面点击按键“Start Download（开始下载）”</p>

<p>点击完Start Download 设备快速下电上电，GTL识别到设备，开始烧写固件。
刷写进度 100%后，进度条会变为绿色，显示烧录 Success</p>

<p>拔出USB 线，设备断电重启后即可正常使用</p>
<p><strong>V2X升级</strong>    需完成对应驱动的安装</p>
<p>使用gLMT软件进行系统升级，软件密码为 root</p>
<p>1）将<code>gSafety_OBU_DMD3A_V7.4.3.5_ATU_SF_202304201055-0725修复解码问题更新\4004\update.tar.gz</code>导入到glmt的文件管理里面，版本号填：gSafety_OBU_DMD3A_V7.4.3.5_ATU_SF_202304201055-0725</p>

<p>2）在设备管理页，添加一个设备</p>

<p>转接地址不用填写 如果正常识别到V2X驱动，软件可以识别到刚刚配置的地址，下拉框选择识别到的地址，起一个别名这里以test 为例 点击确定即可 如果显示设备状态显示在线 即代表连接成功</p>

<p>3）升级，将横向滚动条向右滚即可看见升级的进度条，升级时间较长，升级过程中不允许中断，否在系统可能崩溃，升级完毕后，系统会自动启动</p>
<p></p>
<p>4）将 <code>v2x\v2x升级及配置文件</code> 文件夹中的 <code>cfg</code> 文件夹里的内容替换到DMD3A模组中 <code>/system/etc/ncs_4layer/cfg</code> 里面，推荐使用mobax软件或者winscp进行传输，不容易出错</p>
<p>5）将 <code>v2x\v2x升级及配置文件</code> 文件夹中的 <code>cur.cfg</code> 替换到DMD3A模组中 <code>/system/etc</code> 里面</p>
<p>6）重启模组</p>
<pre><code># 在v2x系统中输入以下两条命令
ncs_nr -q
ncs_nr -d
</code></pre>
<p>然后，重新上下电即可完成升级操作</p>
<p><strong>v2x配置</strong></p>
<p>此步骤必须得做，不然无法成功使gps locked、无法通过网络连接v2x</p>
<p>接着升级步骤，重新上电后，仍然使用gLMT软件连接</p>
<p><strong>网关配置</strong></p>
<p>点击右上角参数按钮，进入参数页面配置，增加一个设备网关，按照下图进行配置</p>

<p>这样配置后我们就可以通过i.MX8QM进入到V2X的系统了</p>
<p><strong>gnss配置</strong></p>
<p>依然在参数页面，配置如下四项内容</p>


