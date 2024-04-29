# Windows Server Failover Cluster MSSQL-AlwaysOn Kurulumu
<div><span style="text-decoration: underline;"><strong>&nbsp;</strong></span><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ1.png"><span style="text-decoration: underline;"><strong>
<br>
</strong></span></div>
<div><br>
</div>
<div>MS SQL Always On mimarisi gereği minimum iki sunucudan oluşmaktadır. Windows Server işletim sistemi ailesi rölelerinden olan failover cluster üzerine kurgulanmaktadır. Always on kurulumu için aşağıdakilere ihtiyacınız olacaktır.
</div>
<ul>
    <li>2 adet Windows Server 2016 yada 2019 kurulu sunucu </li>
    <li>Windows Server işletim sistemleri üzerinde Failover Cluster role kurulumu </li>
    <li>DNS suffix yapılandırması </li>
    <li>Minimum 4 adet IP adresi: 1 IP birinci sunucunuz için, 1 IP ikinci sunucunuz için, 1 IP Failover cluster için, 1 IP Always On için ihtiyacınız olacaktır.
    </li>
    <li>2 sunucu içinde MS SQL Enterprise Editon kurulumu </li>
</ul>
<div>İlk olarak yapılandırma işlemlerine kurulmuş olan Windows Server işletim sistemli sunucularımızın DNS suffix yapılandırması ile başlayınız. Kurulum ve yapılandırma süreçlerinde iki sunucu ile eş zamanlı çalışma yapmamız gereken durumlar olduğu için iki
sunucuyu aynı anda göstereceğim. Sol taraf da bulunan ekran 1. Sunucuya sağ tarafta bulunan ekran 2. Sunucuya aittir. Control Panel\All Control Panel Items\System penceresinde sol bölümde bulunan menüden "Advanced system settings" tıklayınız.
<br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ2.png">
<br>
</div>
<div><br>
</div>
<div>"System Properties" penceresinde "Computer Name" sekmesinde "Change" butonuna basınız.
<br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ3.png">
<br>
</div>
<div><br>
</div>
<div>"Computer name / domain changes" penceresinde "more" butonuna basınız. "DNS Suffix and netbios computer name" penceresinde "Primary DNS suffix of this computer" bölümüne failover clusterimiz için bir domain belirliyoruz. Bunun için önerim domain.local
şeklinde bir tanımlama yapmanızdır. Talimat kapsamında ben "test.local" olarak tanımlamamı yaparak devam edeceğim.</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ4.png"><br>
</div>
<div><br>
</div>
<div>İşlemler sonrası DNS suffix etkin olması için sistemin yeninden başlatılması gerekmektedir. Sisteminiz yeninden başladıktan sonra IPv6'nın kapalı olduğunu kontrol ediniz. Not: Always On SQL yapısının bağlantı sağlayacağı uygulamanın IPv6 iletişim kurmadığından
emin olunuz. IPv6 kapatmamızın sebebi failover cluster kurulumundan sonra tanımladığımız DNS suffix sonrası oluşan FQDN lerin dns çözümlemelerini IPv4 üzerinden yapacağımız için. Uygulama IPv6 kullanıyorsa FQDNlere A kaydı tanımlarken IPv6 girilmesi gerekmektedir.
Talimat kapsamında IPv6 kapalı işlem yapılmıştır. Windows Server işletim sistemimizde failover cluster kurulumunu hızlıca tamamlamak için
<br>
</div>
<div><br>
</div>
<div>PowerShell açınız ve aşağıdaki komutu çalıştırınız. Install-WindowsFeature Failover-Clustering -IncludeManagementTools
<br>
</div>
<div><br>
</div>
<div>
<div class="reCodeBlock" style="border: 1px solid #7f9db9; overflow-y: auto;">
<div style="background-color: #ffffff;"><span style="margin-left: 0px !important;"><code style="color: #000000;">Install-WindowsFeature Failover-Clustering -IncludeManagementTools</code></span></div>
</div>
<br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ5.png"><br>
</div>
<div><br>
</div>
<div>PowerShell üzerinde failover cluster kurulumunuz tamamlanmıştır. <br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ6.png">
<br>
</div>
<div><br>
</div>
<div>Kurulum sonrası failover clusteriniz public IP üzerinden DNS A kaydı ekleyerek çalıştırabilirsiniz. Clsuter güvenliği için önerilen local IP adresi ile çalışması ve public erişiminin doğrudan olmamasıdır. Bu tip durumlarda DNS tanımlamaları için Windows
Host dosyasını kullanabilirsiniz. <br>
</div>
<div><br>
</div>
<div>FQDN adresleri: <br>
</div>
<div><br>
</div>
<div>Server1: FC-Ozan.test.local <br>
</div>
<div>Server2: FC-Ozan2.test.local <br>
</div>
<div>Cluster FQDN: cluster.test.local <br>
</div>
<div>Availability Group (AG) FQDN: ag.test.lcoal <br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ7.png"><br>
</div>
<div>Faiover cluster kurulumumuz tamamladıktan sonra yapılandırmamız gerekiyor. Yapılandırma sırasında bağlantı sorunu yaşamamak için host dosyasına belirlediğimiz FQDN adreslerini ver IP adreslerini giriyoruz. Birinci sunucumuza tanımlama yaparken ikinici
sunucumuzun FQDN adresini ve IP adresini giriyoruz. İkinci sunucumuza tanımlama yaparken birinci sunucmuzun FQDN adresini ve IP adresini giriyoruz.
<br>
</div>
<div><br>
</div>
<div>Her iki sunucumuza AG ve cluster FQDN adresleri ve IP adreslerini giriyoruz.
<br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ8.png">
<br>
</div>
<div><br>
</div>
<div>Failover Cluster Manager çalıştırınız. Failover cluster kurulumu işlemlerini yapınızda bulunan birinci sunucuda yapmanız yeterlidir.
<br>
</div>
<div><br>
</div>
<div><strong>Bu bölümdeki işlemler sadece birinci sunucuda yapılmıştır. </strong>
<br>
</div>
<div><br>
</div>
<div>"Failover Cluster Manager" penceresinde sağ bölümde bulunan menüden "Create Cluster" basınız. Create Cluster Wizard penceresinde Before you begin adımını next butonuna basarak geçiniz. Select Servers bölümünden FQDN adresleri ile birinci ve ikinci sunucularınızı
ekleyiniz. <br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ9.png">
<br>
</div>
<div><br>
</div>
<div>Validation warning adımında "yes" opsiyonunu seçerek Next butonuna basınız. <br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ10.png">
<br>
</div>
<div><br>
</div>
<div>Validation ekranında next butonuna basarak devam ediniz. <br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ11.png">
<br>
</div>
<div><br>
</div>
<div>Validating işlemi bittikten sonra finish butonuna basınız.</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ12.png">
<br>
</div>
<div><br>
</div>
<div>Clsuter kurulumunun "Access point for administering the cluster" adımında failover clusterimiz için isim ve IP tanımlamasını yapınız.
<br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ13.png">
<br>
</div>
<div><br>
</div>
<div>Next butonuna basınız.Confirmation ekranında ayarlarınızı kontrol edin ve next butonuna tekrar basınız. "Creating New Cluster" işleminiz başlayacaktır.</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ14.png">
<br>
</div>
<div><br>
</div>
<div>Summary ekranında işlem özetinizi görüntülüyorsunuz.</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ15.png">
<br>
</div>
<div><br>
</div>
<div>Failover clusterimizda bulunan 2. Node etkin duruma getirmek için ikinci sunucumuzda işlemlere başlıyoruz.
<br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ16.png">
<br>
</div>
<div><br>
</div>
<div>Failover Cluster Manager da sağ bölümde bulunan Connect to cluster butonuna basınız. Cluster FQDN adresini yazınız ok butonuna basınız
<br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ17.png">
<br>
</div>
<div><br>
</div>
<div>Kısa süre içerisinde cluster ile bağlantısı tamamlanacaktır.</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ18.png">
<br>
</div>
<div><br>
</div>
<div>Cluster kurulum işlemimiz tamamlanmıştır.</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ19.png">
<br>
</div>
<div><br>
</div>
<div>Bu adımdan itibaren işlemleri her iki cluster bağlı sunucunuz için yapmanız gerekmektedir. MS SQL Always On yapılandırması öncesi MS SQL Enterprise Edition kurulumunu yapmanız gerekmektedir. Kurulum için kurmanız gereken standart SQL kurulumunda olduğu
gibi "Datebase Engine Services" ve "Full-Text and Semantic Extractions for Search" kurmanız yeterlidir. Yönetim içinde SQL Server manager yüklemeniz gerekmektedir. AlwaysOn yapılandırmamıza SQL Server Configuration Manager üzerinde ayarlarımızı yapılandırarak
başlıyoruz. Sol taraftan SQL Server Services seçiniz. Sağ bölümde bulunan SQL Server (MSSQLSERVER) sağ tıklayınız. AlwaysOn High Availability sekmesine geliniz. Windows Failover Cluster isminizi giriniz ve Enable duruma getiriniz.
<br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ20.png">
<br>
</div>
<div><br>
</div>
<div>SQL Server (MSSQLSERVER) Properties penceresinde Log On sekmesini tıklayınız. "This Account" opsiyonunu seçiniz. Accountu Administrator olarak düzenleyin. ".\administrator" tanımlamanızı yapmanız gerekmektedir.</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ21.png">
<br>
</div>
<div><br>
</div>
<div>Microsoft SQL Server Management Studio açınız. MS SQL server üzerinde databasenizi oluşturunuz yada aktif bir database ise bir kopyasını taşıyınız. Bu işlemi her iki sunucu içinde gerçekleştiriniz.</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ22.png">
<br>
</div>
<div><br>
</div>
<div>Bu adımdan itibaren işlemleri sadece 1 nolu sunucuda yapmanız gerekmektedir. "AlwaysOn High Availability" dizine geliniz. Mouse sağ tuşu ile tıklayınız. Açılan menüden "New Availability Group Wizard" tıklayınız.</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ23.png">
<br>
</div>
<div><br>
</div>
<div>"New Availability Group" penceresinde "Specify Options" adımında "Availability Group Name" belirleyiniz. "Cluster Type" Windows Server Failover Cluster seçiniz. "Database Level Health Detection" check box seçiniz.</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ24.png">
<br>
</div>
<div><br>
</div>
<div>"Select Databases" adımında oluşturduğunuz yada taşıdığınız veri tabanını seçiniz.
<br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ25.png"><br>
</div>
<div><br>
</div>
<div>"Specify Replicas" adımında "Add replica…" butonuna basarak iki nolu sunucunuzda ki instance ekleyiniz.
<br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ26.png">
<br>
</div>
<div><br>
</div>
<div>"Connect to server" penceresi açılacaktır. İki nolu SQL server bağlanmanızı isteyecektir. 2 nolu sunucu için erişim bilgilerini giriniz. "Connect" butonuna basınız.
<br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ27.png">
<br>
</div>
<div><br>
</div>
<div>"Specify Replicas" adımında iki nolu sunucumuz listeye eklenmiştir. <br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ28.png">
<br>
</div>
<div><br>
</div>
<div>"Specify Replicas" adımında "listener" sekmesine tıklayınız. Bu bölümde AlwaysOn Availability Group için DNS tanımlaması yaparak Listener DNS kaydı oluşturacağız. Talimatın başında da belirttiğim AG olarak tanımlama yapacağım. Port ve Static IP adresi
tanımlamasını yapınız. Next butonuna basınız. <br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ29.png">
<br>
</div>
<div><br>
</div>
<div>"Select Data Synchronization" admında iki seçenek ile ilerleyebilirsiniz. Eğer ikinci sunucu üzerinde veritabanını kopyalamadıysanız birinci seçenek "Automatic seeding" ile işleme devam edebilirsiniz. Eğer veritabanını iki sunucu üzerine de kopyaladıysanız
3 seçenek olan "join Only" seçeneği ile devam edebilirsiniz. Bir başka opsiyonumuz 2. Seçenek ile database ve log dosyasını bu adımda replicasyon işlemini gerçekleştirebilirsiniz. Önerim veri tabanını iki sunucu üzerine kopyalayıp Join only olarak ilerlemenizdir.</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ30.png">
<br>
</div>
<div><br>
</div>
<div>"Validation" adımında işlemlerin bitmesini bekleyiniz ve next butonuna basınız.
<br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ31.png">
<br>
</div>
<div><br>
</div>
<div>"Progress" ekranında işlemlerin tamamlanmasını bekleyiniz. <br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ32.png">
<br>
</div>
<div><br>
</div>
<div>"Results" adımında wizard başarılı bir şekilde tamamlandıktan sonra close ile pencereyi kapatabilirsiniz.
<br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ33.png">
<br>
</div>
<div><br>
</div>
<div>AlwaysOn Availability Group kurulumu ve yapılandırma işlemimiz tamamlanmıştır.
<br>
</div>
<div><br>
</div>
<div><img alt="" src="https://www.emreozanmemis.com/wp-content/uploads/2019/11/110519_1150_WindowsServ34.png"></div>
