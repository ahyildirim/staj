## DATABASE VE WEB SERVER SANAL MAKİNELERİNİ KURMA

- Öncelikle kullanmak istediğiniz hypervisor uygulamasını kurun. Ben bu uygulamada halihazırda kullandığım Oracle VM VirtualBox uygulamasını kullanacağım ve bunun üzerinden anlatacağım.
- Hypervisor kurulumunu yaptıktan sonra eğer host bilgisayarınızın BIOS ayarlarında Intel Virtualization Technology özelliği açık değil ise bu ayarı host bilgisayarınızın sanallaştırılabilmesi için bu özelliği aktifleştirmeniz gerekmektedir.
- Bu link üzerinden [Ubuntu Server 22.04](https://ubuntu.com/download/server) ISO dosyasını indirin.
- Oracle VM VirtualBox uygulamasını açın.
- Yeni sekmesine tıklayın, ardından Ad kısmına sanal makinenizin adını girin. (Örneğin database, server).
- Klasör kısmından sanal makinenizin nerede saklanacağını seçin.
- ISO Kalıbı kısmından indirdiğimiz ubuntu-24.04.2-live-server-amd64.iso dosyasını seçin ve ileri diyin
- Kullanıcı adı ve parola kısmına tercihinize bağlı bir kullanıcı adı ve parola seçin fakat bu kullanıcı adını ve parolayı unutmayın. İleri seçeneğine tıklayın.
- Önünüze gelen sekmede sanal makinenizin host makinenizden ne kadar kaynak kullanacağını seçeceksiniz. Host makinenizin gücüne bağlı olarak minimal bir seçim yapın. (Zaten arayüz vb. donanım gerektiren işlemlerle uğraşmayacağımız için minimal bir seçim en iyisi olacaktır.)
- İleri dedikten sonra sanal makinenizin host makinenizin diskinden kaç GB alan kullanacağını seçeceksiniz. Burada 20GB gibi bir değer gayet yeterli olacaktır. İleri seçeneğine tıklayın, ardından bitir seçeneğine tıklayın.
- Şimdi sanal makineniz otomatik olarak açılacaktır. Her iki sanal makine kurulumu için bu adımları takip edin.
- Sanal makinenizin ekranı geldikten sonra dil seçeneği seçin. Ben İngilizce seçiyorum.
- Ardından klavye düzeninizi seçeceksiniz. İsterseniz şuanda aktif kullandığınız klavyeyi Identify Keyboard seçeneğine tıklayıp basmanızı istediği tuşa basarak otomatik olarak klavye dilinizi tanıtabilirsiniz, isterseniz de yukarıdan manuel olarak seçebilirsiniz. Türkçe düzen klavye seçiyorum. Done seçeneğine tıklayın.
- Bizden minimal bir kurulum istendiği için Ubuntu Server(minimized) seçeneğine tıklayıp done seçeneğine basıyoruz.
- Extra bir proxy vb. bir adrese ihtiyaç duymayacağımız için 2 kere done seçeneğine basıp devam ediyoruz. Ardından Ubuntu testini bitirdikten sonra bir kez daha done basıyoruz.
- Disk bölümlemeleri veya LVM Disk Şifrelemesi gibi kısımlar bizden istenmediği için direkt olarak Use Entire Disk seçeneğine basıp done basıyoruz.
- Tekrardan done basıp continue basıyoruz.
- Bu gelen ekranda sanal makinemiz için ana bir kullanıcı belirleyip şifre belirlenmemiz isteniyor. Tercihinize bağlı bir isim ve şifre seçin. Name kısmına ahmethakan, Server Name kısmına server, username kısmına ahmet yazıp şifre belirleyip done basıyorum. **(Unuttuğunuz takdirde sanal makine açılmayacağı için username ve password kısmını kesinlikle bir yere kaydetmenizi öneriyorum.)**
- Ubuntu Pro kısmında Skip for now seçeneğine tıklayıp continue basıyoruz.
- OpenSSH bize kesinlikle lazım olacağı için install OpenSSH seçeneğine tıklayıp done basıyoruz.
- Ekstra bir paket istemediğim için hiçbir seçeneği seçmeden done basıp sanal makinemin kurulumunu bekliyoruz.
- Reboot now seçeneğine tıklayıp makinenin tekrar başlatılmasını bekliyoruz.
- Makine açıldıktan sonra bizden login ve password isteyecektir. Önceki adımda username ve password kısmında belirlediğimiz bilgileri giriyoruz. Artık sanal makinemiz kurulmuş bulunmakta. Şimdi ise her sanal makine için yapmamız gerekenlere bakalım.

## WEB SERVER SANAL MAKİNESİ ADIMLARI

- İlk olarak .yaml, .conf, .html gibi dosyaları düzenleyebilmek için Nano Text Editor kuralım.
- `sudo apt update` komutu ile paketleri yükleyeceğimiz servisi güncelleyelim `sudo apt install nano -y` yazın. Sizden şifre isteyecektir, buraya sanal makinenize girmek için belirlediğiniz şifreyi girin ve kurulumu tamamlamasını bekleyin.
- Ardından sanal makinemiz her yeniden başlatıldığında IP'si değiştiği için ve bu da sitelere erişmemize engel olduğu için bunu düzeltelim. (**BU ADIMI HER İKİ SANAL MAKİNE İÇİN DE UYGULAMANIZ GEREKMEKTEDİR. DATABASE SANAL MAKİNESİNİN ADIMLARINI ANLATTIĞIM ZAMAN BU KISMI BİR DAHA ANLATMAYACAĞIM**)
- Sanal makineyi kapatın, Oracle VM VirtualBox uygulamasına gelip sanal makinenize sağ tıklayın ve ayarlara basın.
- Ağ kısmına gelip Şuna Takılı: kısmını Köprü Bağdaştırıcısı seçeneğini seçip tamama basın.
- Sanal makineyi açın. Ardından `ip a` komutunu girerek 192.168.1.x/24 satırında sanal makinenizin IP'sini ve Mask'ını göreceksiniz. O kısmın biraz yukarısında 2: şeklinde bir kısım olacak, burada yazan isime dikkat edin. Bende (eğer VirtualBox kullandıysanız büyük ihtimal sizde de) enp0s3 yazıyor.
- Ardından `sudo nano /etc/netplan/01-netcfg.yaml` komutunu girin ve önünüze gelen ekranda aşağıda verilen kod bloğunu yazın. Burada verilen yol netplan konfigürasyonları için belirlenen yoldur. Netplan ise Linux dağıtımlarında DNS, IP gibi ağ ayarlarının yapıldığı araçtır. (# yorum satırıdır ve açıklamalar için vardır yazmanıza gerek yok.)

```
     network:
      version: 2                     # Netplan konfigürasyon versiyonu
      renderer: networkd             # Ağ yöneticisi → systemd-networkd kullanıyoruz
      ethernets:
        enp0s3:                      # Ağ arabirimi (interface) adı → ip a yazdığınızda 2: olan kısım
          addresses:
            - 192.168.1.50/24        # Sabit IP adresi (Hangi ip adresini istiyorsanız onu yazın)
          gateway4: 192.168.1.1      # Varsayılan ağ geçidi (Modem IP'si)
          nameservers:
            addresses: [8.8.8.8, 1.1.1.1]  # DNS sunucuları (Google & Cloudflare DNS)
```

- CTRL+O, Enter basın ve kaydedin. Ardından CTRL+X basarak çıkın.
- `sudo netplan apply` yazarak netplan konfigürasyonlarını aktifleştirin.
- Sanal makinede kullanici isimli bir kullanıcı oluşturmak için `sudo adduser kullanici` komutunu girin. Çıkan ekranda şifre bir şifre belirleyin, şifrenizi doğrulayın. Ardından çıkan diğer kısımları boş bırakabilirsiniz.
- Host makinenizden kullanici adlı kullanıcıya bağlanabilmek için host makinenizden `ssh keygen` yazarak SSH anahtarınızı alın. `ssh-copy-id kullanici@<Sanal Makine IP>` komutunu kullanarak SSH anahtarını uzak sunucuya kopyalayın. Bu sayede SSH ile bağlanırken şifre girmemize gerek kalmayacak. Sanal Makine IP kısmına bir önceki adımda belirlediğiniz statik ip'yi girin. `ssh-copy-id kullanici@192.168.1.50` gibi. Çıkan ekranda oluşturduğunuz kullanıcı için belirlediğiniz şifreyi girin. Ardından `ssh kullanici@192.168.1.50` komutunu girerek bağlantı sağlayıp sağlayamadığınızı test edin.
- SSH üzerinden root girişi ve parola doğrulamasını kapatmak için `sudo nano /etc/ssh/sshd_config` komutunu girin. Çıkan ekranda `PermitRootLogin` ve `PasswordAuthentication` satırlarını bulup ikisini de **no** yapın, CTRL+O, enter ile kaydedip CTRL+X ile çıkın. Ardından `sudo systemctl restart ssh` ile SSH servisini yeniden başlatın ki ayarlar geçerli olsun.
- Güvenlik duvarının gerekli olmayan herhangi bir isteği kabul etmeyecek şekilde ayarlamak için öncelikle UFW (Uncomplicated Firewall) servisini kurmamız gerekiyor.
- Bunun için `sudo apt install ufw -y` komutunu girin.
- Ardından `sudo ufw default deny incoming` komutu ile gelen tüm bağlantıları engelleyin. `sudo ufw default allow outgoing` komutu ile giden bağlantılara izin verin. `sudo ufw allow OpenSSH` komutu ile SSH bağlantılarının isteklerine izin verin ki SSH ile bağlanırken sorun yaşamayın. `sudo ufw enable` komutu ile güvenlik duvarını aktif edin.
- Nginx web sunucusu ve gerekli bazı eklentileri kurmak için `sudo apt install nginx php-fpm php-mysql unzip curl` komutunu girerek Nginx (Nginx web sunucusu) php-fpm (PHP'nin hızlı çalışması için FastCGI Process Manager), php-mysql (PHP'nin MySQL veri tabanı ile iletişim kurması için), unzip (sıkıştırılmış .zip dosyalarını çıkartabilmek için gerekli), curl (Web'den dosya indirmek için gerekli) paketlerini kurun.
- Ardından `sudo systemctl enable nginx` komutu ile Nginx servisini etkinleştirin.
- php-fpm servisini etkinleştirmek için önce `systemctl list-units --type=service | grep fpm` komutu ile php-fpm servisinin sürümünü kontrol edin. Yüksek ihtimalle php8.x-fpm.service gibi bir yazı göreceksiniz. Ardından `sudo systemctl php8.x-fpm` komutu ile php-fpm servisini etkinleştirin.
- Nginx kurulumu tamamlandıktan sonra güvenlik duvarının Ngnix'in HTTP(80) ve HTTPS(443) portlarının isteklerine izin vermek için `sudo ufw allow 'Nginx Full'` komutunu girin.
- WordPress kurulumu için öncelikle Nginx sitelerinin genelde tutulduğu dizine `cd /var/www` komutu ile geçiş yapın. Ardından `sudo wget https://wordpress.org/latest.zip` komutu ile internetten WordPress'in son sürümünü latest.zip dosyası adı altında inidirin. `sudo unzip latest.zip` komutu ile .zip dosyasını çıkartın. Çıkartılan dosyayı `sudo mv wordpress bugday` komutu ile bugday olarak yeniden adlandırın. `sudo chown -R www-data:www-data bugday` komutu ile bugday dosyasının sahipliğini Nginx veya Apache web sunucularının sistem kullanıcısı olan www-data adlı kullanıcıya devredin ki Ngnix veya Apache bu dosyalara erişebilsin.
- Şimdi ise Nginx web sunucusunu başlatabilmemiz için bir Nginx konfigürasyon dosyası yazmamız gerekiyor. Önce default konfigürasyon dosyası olan dosyayı `sudo rm /etc/sites-enabled/default` komutu ile silelim. Ardından `sudo nano /etc/nginx/sites-available/bugday` komutu ile nginx'in sites-available kısmındaki yerde bugday isimli bir dosya oluşturuyoruz. İçini aşağıdaki şekilde doldurun.

```
    - server {
        listen 80 default_server; #HTTP portunu dinlemek için
        server_name bugday.org buğday.org; #WordPress'in hangi domainlerde aktif olacağı
        root /var/www/bugday; #WordPress'in bulunduğu dizin(indirdiğimiz)

        index index.php index.html index.htm; #Varsayılan olarak açılacak dosyalar(sırayla index.php, index.html, index.htm)

        location / {
            try_files $uri $uri/ /index.php?$args;
        } # bugday.org/ornek gibi ana ve alt dizine gelen istekleri kontrol ederiz. Gelen isteğe karşılık dosya sunucuda varsa o dosyayı göster, eğer gelen bir istek klasörse ve dizin varsa o dizini göster, Dosya/dizin yoksa isteği parametrelerle birlikte index.php'ye yönlendir
        #  WordPress gibi dinamik sitelerde, URL’ler “güzel URL” yapısında olur (örneğin: /yazilar/başlık), ama fiziksel olarak dosya yoktur. Bu yapı sayesinde tüm istekler index.php’ye düşer, PHP içerik dinamik olarak cevap verir.

        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/run/php/php8.3-fpm.sock;  # Versiyona dikkat
        } #.php uzantılı dosya geldiğinde çalışır. PHP dosyalarının nasıl çalışacağını belirleyen ön ayar dosyasını include eder ve PHP dosyasının hangi araç ile çalıştıralacağını belirler

        location ~ /\.ht {
            deny all;
        } #.ht uzantısı ile gelen tüm dosyaların isteğini tamamen engeller çünkü Nginx, Apache gibi .htaccess vb. dosyalarını kullanmaz fakat bu dosyaların içinde hassas bilgiler olabilir. Bu yüzden bu dosyaların erişim isteği engellenir.
    }

```

> Burada listen 80 satırının yanına default_server eklememin altında şöyle bir sebep yatıyor: Normalde bugday.org doğru siteye(WordPress'in olduğu siteye) yönleniyor fakat buğday.org Nginx'in default sitesine yönleniyor. Bunu düzeltmek için ilk başta /etc/nginx/sites-enabled/ kısmından default'u silmeyi denedim. İlk başta çalışmıştı fakat 2025ozgur.com sitesini eklediğimde bu sefer buğday.org 2025ozgur.com'a yönleniyordu. Bunun önüne geçmek için bugday.org sitesini default_server olarak tanımladım ve bu sayede buğday.org WordPress'in olduğu siteye yönlenmeye başladı.

- Ardından bu oluşturduğumuz dosyanın sembolik linkini `sudo ln -s /etc/nginx/sites-available/bugday /etc/nginx/sites-enabled/` kodu ile Nginx'in siteleri aktifleştirdiği dizinde oluşturuyoruz. Sonrasında `sudo nginx -t` ile yazdığımız konfigürasyonda herhangi bir hata olup olmadığını kontrol edip `sudo systemctl reload nginx` komutu ile Nginx servisini yeniden yüklüyoruz.
- Host makinenizde terminali açıp sudo nano /etc/hosts klasörüne giriyoruz ve aşağıdaki satırı ekleyin.

```
192.168.1.50 bugday.org buğday.org
```

- 192.168.1.50 olan kısma kendi sanal makinenizin IP adresini verin. Bu sayede tarayıcıda bugday.org domaini sizin sanal makinenizin IP adresi üzerinden Nginx ile iletişim kuracak ve kurduğunuz site açılacaktır.
- Ardından host makinenizde tarayıcıyı açıp bugday.org adresini girin. Karşınıza WordPress kurulum ekranı gelecektir. Dil seçip devama basın. WordPress kurulumuna devam edebilmemiz için bir database kurmamız gerekiyor, bizden istendiği üzere database için ayrı bir sanal makine kurmamız ve onun da ayarlarını yapmamız gerekiyor. Şimdi database sanal makinesinin kurulumuna geçelim

# DATABASE SANAL MAKİNESİ ADIMLARI
- Sanal makinede kullanici isimli bir kullanıcı oluşturmak için `sudo adduser kullanici` komutunu girin. Çıkan ekranda şifre bir şifre belirleyin, şifrenizi doğrulayın. Ardından çıkan diğer kısımları boş bırakabilirsiniz.
- Host makinenizden kullanici adlı kullanıcıya bağlanabilmek için host makinenizden `ssh keygen` yazarak SSH anahtarınızı alın. `ssh-copy-id kullanici@<Sanal Makine IP>` komutunu kullanarak SSH anahtarını uzak sunucuya kopyalayın. 
> Bu kısımda uzak sunucunun kimliğinin değiştiğine dair bir hata alabilirsiniz. Bunu düzeltmek için `ssh-keygen -f "/home/username/.ssh/known_hosts" -R "<Sanal Makine IP>"` komutu ile eski anahtarı known_hosts dosyasından silebilirsiniz.

- Bu sayede SSH ile bağlanırken şifre girmemize gerek kalmayacak. Çıkan ekranda oluşturduğunuz kullanıcı için belirlediğiniz şifreyi girin. Ardından `ssh kullanici@192.168.1.78` komutunu girerek bağlantı sağlayıp sağlayamadığınızı test edin.
- SSH üzerinden root girişi ve parola doğrulamasını kapatmak için `sudo nano /etc/ssh/sshd_config` komutunu girin. Çıkan ekranda `PermitRootLogin` ve `PasswordAuthentication` satırlarını bulup ikisini de **no** yapın, CTRL+O, enter ile kaydedip CTRL+X ile çıkın. Ardından `sudo systemctl restart ssh` ile SSH servisini yeniden başlatın ki ayarlar geçerli olsun.
- Güvenlik duvarının gerekli olmayan herhangi bir isteği kabul etmeyecek şekilde ayarlamak için öncelikle UFW (Uncomplicated Firewall) servisini kurmamız gerekiyor.
- Bunun için `sudo apt install ufw -y` komutunu girin.
- Ardından `sudo ufw default deny incoming` komutu ile gelen tüm bağlantıları engelleyin. `sudo ufw default allow outgoing` komutu ile giden bağlantılara izin verin. `sudo ufw allow OpenSSH` komutu ile SSH bağlantılarının isteklerine izin verin ki SSH ile bağlanırken sorun yaşamayın. `sudo ufw allow from <WEB_IP> to any port 3306 proto tcp` komutu ile Web sunucusundan gelen SSH ve Mysql bağlantılarına izin verin. WEB_IP yazan kısma Web Server sanal makinesinin ip adresini girin. `sudo ufw enable` komutu ile güvenlik duvarını aktif edin.
- MariaDB database sunucusunu kurmak için `sudo apt install mariadb-server -y` komutunu girin.
- Kurulduktan sonra `sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf` komutu ile MariaDB'nin konfigürasyon ayarlarına girelim. Burada `bind-address = 0.0.0.0` kısmını bu şekilde güncelleyerek bu database'e tüm ip adreslerinin ve farklı makinelerin erişebileceğini belirtiriz. Bu güvenliksiz bir davranış gibi gözükebilir fakat UFW ayarlarında biz database erişimimizi ``sudo ufw allow from <WEB_IP> to any port 3306 proto tcp`` satırı ile sadece Web Server Ip'si ile kısıtlamıştık. 
- `sudo mysql` komutu ile MySQL'e root yetkisi ile girin. `CREATE DATABASE wordpress_db;` komutu ile wordpress_db adlı bir database oluşturun. `CREATE USER 'wordpress_user'@'<WEB_IP>' IDENTIFIED BY 'guclu_sifre';` komutu ile bir database kullanıcısı oluşturun ve kullancıyı sadece Web Server makinesinin IP'si ile ulaşılacak şekilde belirleyin. Ardından guclu_sifre kısmına güvenliği yüksek bir şifre girin.  `GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wordpress_user'@'<WEB_IP>';` komutu ile oluşturduğumuz kullanıcıya oluşturduğumuz wordpress_db database'i üstündeki tüm yetkileri verin. `FLUSH PRIVILEGES;` komutu ile yetki değişikliklerini geçerli kılıp `EXIT` komutu ile database'den çıkın.
- `sudo systemctl enable mariadb` komutu ile MariaDB sunucusunu devreye sokup `sudo systemctl restart mariadb` komutu ile yeniden başlatın.
# WORDPRESS SİTE KURULUMU
- Web sunucusu adımlarında yarım bıraktığımız WordPress kurulumuna devam edelim.
- bugday.org sitesini tarayıcımızda açtıktan sonra WordPress'de dil seçip ileri diyin.
- Hadi başlayaylım seçeneğine tıkladıktan sonra karşında çıkan ekranı aşağıdaki şekilde doldurun

| SORU | YAZILACAK ŞEY |
| ------- | ------- |
| Veritabanı Adı    | Oluşturduğumuz veritabanının adı (Bu belgeye göre wordpress_db)    |
Kullanıcı Adı | Oluşturulan kullanıcının adı (Bu belgeye göre wordpress_user)
Parola | Belirlenen güçlü şifre (Bu belgeye göre guclu_sifre)
Veritabanı Sunucusu | Database sanal makinesinin ip'si
Tablo ön eki | Bir şey girmeye veya değiştirmeye gerek yok.

- Kurulumu başlat seçeneğine tıklayın.
- Burada çıkan Site başlığına istediğinizi girin, kullanıcı adı ve parola kısmına siteye giriş yapmak için kullanmak istediğiniz şifreyi ve parolayı girin. E-posta adresine bizden herhangi bir e-posta işlemleri istenmediği için gerçek olmayan bir e-posta adresi de girebilirsiniz. Doldurduktan sonra WordPress'i kur seçeneğine tıklayın. Sonrasında gelen ekrana belirlediğiniz kullanıcı adı ve şifreyi girip giriş yapına basın.
- Site açıldıktan sonra üstten Yeni -> Yazı seçeneğine tıklayın. Burada bir başlık ve bir yazı yazın, ardından sol üstten + simgesine basıp Ortam sekmesinin altından bir görsel yükleyip sağ üstten yayınla tuşuna basın. 
- Yazıyı yayınladıktan sonra sol üstte sitenizin ismine tıklayın ve açılan ekranda sol taraftan Ayarlar -> Kalıcı bağlantılar kısmına tıklayın. Buradan özel yapıyı seçip aşağıdaki seçeneklerden sırasıyla %year%, %monthnum%, %day% %postname% seçeneklerini seçin.Özel yapı kısmı şu şekilde görünmeli:
```
http://bugday.org/%year%/%monthnum%/%day%/%postname%
```
- Ardından aşağıdaki değişikleri kaydet seçeneğine basarak seçenekleri kaydedin.
# 2025OZGUR.COM SİTESİNİN KURULUMU
- Bu site herhangi bir WordPress tarzı bir şey kullanmadığı için öncelikle elimizle /var/www dosyasının içine `sudo mkdir /var/www/2025ozgur` isimli bir dosya açıyoruz. Ardından bu dosyanın içinde `sudo nano /var/www/2025ozgur/index.html` isimli bir html dosyası açıyoruz. Bu html dosyasını aşağıdaki şekilde dolduruyoruz.
```
<!DOCTYPE html>
<html lang="tr">
<body>

<div id="output"></div>
<meta charset="UTF-8">
<script>
  let message = "Kullanıcılarımın kişisel verilerini toplamayacağım.<br>";
  let output = document.getElementById("output");

  for (let i = 0; i < 100; i++) {
    output.innerHTML += message;
  }
</script>

</body>
</html>

```
- Sonrasında `sudo nano /etc/nginx/sites-available/2025ozgur` komutu ile bugday dosyasında yaptığımız gibi bir Nginx konfigürasyon dosyası hazırlayacağız.
```
server {
    listen 80;
    server_name 2025ozgur.com www.2025ozgur.com;
    root /var/www/2025ozgur;

    index index.html;

    location /yonetim {
        auth_basic "Yönetim Alanı";
        auth_basic_user_file /etc/nginx/.htpasswd;
    } #yonetim dosyasına erişim için parola girilmesi gerektiğini belirtir.
}
```
- Burada yonetim adlı istek geldiği zaman bizden kullanıcı adı ve şifre girilmesini ayarlamamız gerekiyor. Bu yüzden Basic Auth ayarı yapmamız gerekiyor. Bunun için `sudo apt install apache2-utils -y` komutu ile HTTP Server'ın şifrelenebilmesi için gereken paketi kuruyoruz. Ardından `sudo htpasswd -c /etc/nginx/.htpasswd ad.soyad` komutu ile kullanıcı adı ad.soyad(mailde istenen) olan bir dosya oluşturuyoruz. Bu komutu yazdıktan sonra parola girme ekranı gelecek, buraya da "parola"(mailde istenen) yazıp gönderiyoruz.
- Bu sayede artık 2025ozgur.com/yonetim adresine gittiğimizde bizden bir kullanıcı adı ve parola isteyecek. Doğru girdiğimizde büyük ihtimalle 404 not found hatası verecektir çünkü yonetim sayfası için bir html belirlemedik. Bunun için `sudo mkdir /var/www/2025ozgur./yonetim` komutu ile yonetim için bir dosya oluşturup `sudo nano /var/www/2025ozgur/yonetim/index.html` komutunu yazıp bu html dosyasının içine "Giriş başarılı! Yönetim paneline hoşgeldiniz." gibi bir şey yazarsak bu siteyi açtığımızda önümüze verdiğimiz yazı yazan site açılacaktır. `sudo nginx -t` ve `sudo systemctl reload nginx` komutları ile yaptığımız işlemleri aktifleştirmeyi unutmayın.
- Ardından host makineniz içinde sudo nano /etc/hosts klasörüne bugday.org'u eklediğimiz satırın altına aynı IP adresinden(Web Server sanal makinesi IP'si) www.2025ozgur.com 2025ozgur.com domainleri tanımlayın ki tarayıcıdan bu domainler aracılığı ile sitelere ulaşabilin.
- Web sunucusunun dosyadaki işlemleri minimum yetki ile yapması için aşğaıdaki komutları uygulayabiliriz
```
sudo chown -R www-data:www-data /var/www
sudo find /var/www -type d -exec chmod 755 {} \;
sudo find /var/www -type f -exec chmod 644 {} \;
```
- `sudo chown -R www-data:www-data /var/www` komutu ile www klasörünün(web sitelerinin bulunduğu klasör) sahipliğini Nginx web sunucusunun sistem kullanıcısı olan www:data kullanıcısına veririz. Bu sayede gereksiz root yetkisi almadan Web sunucumuz bu dosyada okuma yazma yapabilir.
- `sudo find /var/www -type d -exec chmod 755 {} \;` komutu ile www klasörünün içindeki tüm dizin(directory) tiplerini seçer ve izinlerini dizin sahibi için 7(read-write-execute), dizindeki gruplar için 5(read-execute), diğer tüm kullanıcılar için 5(read-execute) izinleri verilir. Dizinlerin içine girebilmek için execute(çalıştırma) yetkisi vermek zorunlu olduğu için tüm herkes için execute izini mutlaka verilmelidir.
- `sudo find /var/www -type f -exec chmod 644 {} \;` komutu ile www klasörünün içindeki tüm dosya(file) tiplerini seçer ve izinlerini klasör sahibi için 6(read-write), klasördeki gruplar için 4(read), diğer herkes için 4(read) izinleri verilir. Root dışında kimse yazma yetkisine sahip olmadığı için site dosyalarının değiştirilmesi engellenir ve güvenlik sağlanır.
- Son olarak
```
sudo systemctl is-enabled nginx #Web Server Sanal Makinesi için
sudo systemctl is-enabled php8.3-fpm #Web Server Sanal Makinesi için
sudo systemctl is-enabled mariadb #Database Sanal Makinesi için

```
komutları uygulanarak sistemlerin başlangıçta aktif olup olmadığının kontrolleri yapılır.

> DİPNOT: Halihazırda 42 Yazılım okulunda şuanda Inception adlı docker-compose ve WordPress kullanarak bir sanal makine üzerinden site, sunucu oluşturma projesi ile uğraştığım için benim adıma gerçekten yararlı ve bana çok değerli bilgiler katan bir ön değerlendirme aşaması oldu. Umarım hazırladığım belge anlaşılır ve açıklayıcı olmuştur. Eğer gramer, hitap vb. hatalarım olduysa şimdiden özür dilerim. Okuduğunuz için teşekkür ederim.
