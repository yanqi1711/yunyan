<hr>
<h2>html:
offline: true
export_on_save:
html: true</h2>


<p><strong>Tech</strong>Show</p>

<ol>
<li>
<p>首先连接好线束，用<code>USB数据线</code>连接盒子和电脑USB接口、用<code>FT4222数据线</code>连接盒子和电脑USB接口、用<code>同轴线</code>连接盒子和相机</p>
</li>
<li>
<p>设备管理器中禁用本地相机，只保留SENSING相机，如果线束连接好了，会出现下图两个总线
</p>
</li>
<li>
<p>打开SensingCaptureV2⼯具软件，加载点亮配置（左上角工具中I2C设备需切换到USB）
</p>
</li>
<li>
<p>加载烧写配置
</p>
</li>
<li>
<p>I2C设备切换到MCU
</p>
</li>
<li>
<p>打开Musk3，点击Burn
</p>
</li>
<li>
<p>选择需要烧写的固件，点击Download，烧写完毕会出现Download succeeds

森云提供的出厂版本固件为<code>OX01F10_SR1031_27mclk_96Mpixclk_1280x960_30fps_UYVY_slave_11230904030101.bin</code></p>
</li>
</ol>

<ol>
<li>
<p>打开SensingCaptureV2⼯具软件，加载点亮配置（左上角工具中I2C设备需切换到USB）
</p>
</li>
<li>
<p>加载读取版本配置
</p>
</li>
<li>
<p>I2C设备切换到MCU
</p>
</li>
<li>
<p>左上角工具中点击<code>相机固件升级</code>，选择OV确定后关闭窗口
</p>
</li>
<li>
<p>重新打开<code>相机固件升级</code>窗口，点击读取版本号
</p>
</li>
</ol>


<pre><code>\\10.254.1.251\software\techshow\sensing
</code></pre>





<p>一台可以上网的ubuntu18.04的电脑或虚拟机，我这里是使用的虚拟机</p>
<p>移远有已经构建好环境的docker镜像，这里直接使用移远提供的镜像进行签名操作</p>
<p>Docker安装
Docker 分为免费的 CE（Community Edition）社区版本和 EE（Enterprise Edition）企业级付费版本，所以我们这里选择 docker-ce 进行安装。官方安装文档：https://docs.docker.com/engine/install/ubuntu/</p>
<p>首先安装一些工具：</p>
<pre><code>sudo apt-get install ca-certificates curl gnupg lsb-release
</code></pre>
<p>接着安装官方的 GPG key：</p>
<pre><code>sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
</code></pre>
<p>最后将 Docker 的库添加到 apt 资源列表中：</p>
<pre><code>echo &#34;deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable&#34; | sudo tee /etc/apt/sources.list.d/docker.list &gt; /dev/null
</code></pre>
<p>接着我们更新一次 apt：</p>
<pre><code> sudo apt update
</code></pre>
<p>最后安装 Docker CE 版本：</p>
<pre><code> sudo apt install docker-ce
</code></pre>
<p>最后我们将当前用户添加到 docker 用户组中，不然每次使用 docker 命令都需要 sudo 执行，很麻烦：</p>
<pre><code>sudo usermod -aG docker &lt;用户名&gt;
</code></pre>
<p>创建官方提供的镜像容器</p>
<p>首先，拉取官方镜像</p>
<pre><code>docker pull registry.cn-hangzhou.aliyuncs.com/quectel/ubuntu:secboot_v2
</code></pre>
<p>拉取成功后可以使用<code>docker images</code>查看到该镜像
</p>
<p>然后创建容器，将目前容器的<code>/media</code>目录持久化共享到本地的一个目录，方便后面文件复制粘贴，后续如果操作失误删除了容器，文件也不会丢失</p>
<pre><code># 这里使用本地的 ~/test 目录作为共享目录
docker run -it -v ~/test:/media --name test registry.cn-hangzhou.aliyuncs.com/quectel/ubuntu:secboot_v2
</code></pre>
<p>将签名工具放进容器中</p>
<p>这里直接放在共享目录下
</p>
<p>刚刚创建容器完毕会直接进入到容器系统中，我们直接在容器系统中执行解压操作</p>
<pre><code># 先进入到/media中
cd /media
# 解压
tar -xf ql_sa515m_nork_mro_SecBootTools_Release_v2.4.1.tar.gz
</code></pre>
<blockquote>
<p>如果不小心退出容器了，则执行下面命令可以重新进入</p>
<pre><code>docker start test
docker exec -it test bash
</code></pre>
</blockquote>
<p>镜像签名</p>
<p>我们直接使用生成好的证书就行，即压缩包<code>yuexiang_key.zip</code></p>
<p>首先，将yuexiang_key中的四个文件替换到签名工具中的<code>certs</code>目录下
</p>
<p>然后，获取证书的PK-HASH</p>
<pre><code># 在yuexiang_key文件夹中输入
sha384sum oem_rootca.cer
</code></pre>

<p>然后，将签名工具中 common/sectools/config/sdx55/sdx55_fuseblower_USER.xml 文件中的
root_cert_hash 值替换成生成的 PK-HASH 值
</p>
<p>然后，将需要签名的文件放进签名工具 firmwares 目录中</p>
<p>一个没有签过名的固件有下面这些文件需要签名</p>


<p>最后，进行签名</p>
<pre><code># 先进入到工具目录下
cd /media/ql_sa515m_nork_mro_SecBootTools_Release_v2.4.1
# 执行脚本进行签名
./signImage.sh
</code></pre>


<p>生成好的文件在签名工具的 output/SignImg 目录中，我将对应文件都替换回去</p>


<p>至此，固件签名已经完成了</p>
<p>生成 sec.elf 文件</p>
<p>这个是开启secboot需要的文件，在签名工具中执行</p>
<pre><code># 这里默认你已经按照上面操作替换了PK-HASH了
./genSecdat.sh
</code></pre>

<p>sec.elf 文件已经生成过了，如果证书不变的话可以继续使用</p>


<pre><code>\\10.254.1.251\software\techshow\sign-img
</code></pre>





<p>需使用Ubuntu16.04系统的电脑/虚拟机</p>
<p>预装环境</p>
<pre><code>apt-get install build-essential bc curl ca-certificates fakeroot gnupg2 libssl-dev lsb-release libelf-dev bison flex
</code></pre>
<p>安装交叉编译器</p>
<ol>
<li>
<p>将fsl-imx-xwayland-glibc-x86_64-meta-toolchain-aarch64-toolchain-4.14-sumo.sh拷贝到/opt</p>
</li>
<li>
<p>安装交叉编译器</p>
</li>
</ol>
<pre><code>cd /opt
./fsl-imx-xwayland-glibc-x86_64-meta-toolchain-aarch64-toolchain-4.14-sumo.sh
</code></pre>
<p>上面步骤需要管理员身份</p>
<p>导入环境变量</p>
<pre><code>source /opt/fsl-imx-xwayland/4.14-sumo/environment-setup-aarch64-poky-linux
</code></pre>
<p>解压内核</p>
<pre><code>tar -jxvf imx_4.14.98_2.0.0_ga.tar.bz2
</code></pre>
<p>编译内核</p>
<ol>
<li>设备环境变量</li>
<li>unset LDFLAGS</li>
<li>unset PKG_CONFIG_PATH</li>
<li>make defconfig</li>
<li>make -j12</li>
</ol>
<p>生成内核目标文件所在目录</p>
<ol>
<li>
<p>内核目标文件：<code>imx_4.14.98_2.0.0_ga/arch/arm64/boot/Image</code></p>
</li>
<li>
<p>设备树目标文件：<code>imx_4.14.98_2.0.0_ga/arch/arm64/boot/dts/freescale/fsl-imx8qm-mek-jdi-wuxga-lvds0-panel.dtb</code></p>
</li>
</ol>


<pre><code>\\10.254.1.251\software\IMX8加森云相机\OS驱动文档
</code></pre>

<pre><code>\\10.254.1.251\software\IMX8加森云相机\OS驱动文档\OS系统及驱动源码+基础烧写版本\驱动及系统源码
</code></pre>





<p>软件准备
Ubuntu系统18.04、Linux_for_Tegra_4.4工具包、system.img镜像</p>
<p>硬件连接配置</p>
<ul>
<li>J1烧写模式：用杜邦线将烧写模式配置接口中的7脚与2脚短接</li>
<li>J2烧写模式：用杜邦线将烧写模式配置接口中的8脚与2脚短接
</li>
</ul>
<p>正确连接后，控制器上电后，在本地ubuntu系统中输入命令<code>lsusb</code>，若显示NVidia Corp的设备即表示成功进入烧写模式</p>
<p>镜像准备</p>
<p>需将想要刷入的镜像放到工具包中的<code>bootloader</code>目录下</p>

<p>我们系统版本管理员手中有4个镜像，即101、102、103、104</p>
<p>为了方便刷写操作可以使用链接文件，刷写镜像的时候，将<code>bootloader</code>下的<code>system.img</code>链接文件链接到对应镜像即可，这样有效节约了系统的存储空间</p>
<pre><code># 在bootloader目录执行，system1.img是101的镜像，需替换成你需要镜像
ln -sf ~/Desktop/system1.img system.img
</code></pre>
<p>刷写</p>
<p>进入工具包文件夹，根据自己本机目录修改，然后运行刷机命令</p>
<pre><code>sudo ./flash.sh -r jetson-xavier mmcblk0p1
</code></pre>



<pre><code>\\10.254.1.251\software\techshow\flash-xavier
</code></pre>




<p>| 脚本 | 解释说明 | 备注 |
| :--: | :--: | :--: |
| mount_b.sh | xavier存储挂载脚本 | 运行后需重启系统 |
| automatic_spot_check.sh | 自动驾驶控制器自动化测试脚本 |  |</p>


<pre><code>\\10.254.1.251\software\techshow\script
</code></pre>






