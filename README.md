# Centos 8 BIND DNS ve DHCP Server Yapılandırması

İlk etapta Centos 8 Client'imize ***Static IP*** ataması ile başlayacağız. Ardından ***BIND DNS*** kurulumunu yaparak dosya ayarlamalarımızı yapacağız. En sonunda da ***DHCP Server*** kurulumunu yaparak bölümü bitirecez. Dosya düzeltmeleri için ben ***VIM EDITOR*** kullanmayı tercih ettim.


### 1. Centos 8 Static IP Ataması:
---

<details>
  
  <b><summary> Interface Dosyamızı Manual Yapılandırma: </summary></b>
  
  $ <b>ip a</b> komutunu kullanarak <b><i>Network Interface'lerimizi</i></b> görüntülüyoruz.\
  ![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/ipacommand.png) \
  Ben Interface olarak <b><i>enp0s3</i></b> kullanıyorum. Şimdi <b><i>/etc/sysconfig/network-script/ifcfg-enp0s3</b></i> komutu ile Interface ayarlarımı düzenlicem.\
  BURAYA <i>interfaceayarlari.png</i> ekle.\
  İlk kurulumda <b><i>BOOTPROTO</b></i> default olarak dhcp geliyor. Biz onu none ile değiştiriyoruz. Ayrıca <b><i>IPADDR, PREFIX, GATEWAY, DNS1</b></i> değişkenlerimizi manual olarak eklememiz lazım.\
  $ <b>nmcli connection down enp0s3; nmcli connection up enp0s3</b> --> Bu komut yardımı ile interface yeniden başlat yapıyoruz.\
  $ <b>ip a</b> komutu ile tekrardan istediğimiz IP Adresine ayarladı mı diye kontrol ediyoruz.
  
</details>

<details>
  
  <b><summary> nmtui Komutu Yardımıyla Yapılandırma: </summary></b>
  $ <b>nmtui</b>\
  BURAYA <i>nmtui1.png</i> ekle.\
  <b><i>Edit a connection</b></i> giriş yapalım.\
  BURAYA <i>nmtui2.png</i> ekle.\
  Gelen pencerede <b><i>Edit</b></i> seçeneğini seçelim.\
  BURAYA <i>nmtui3.png</i> ekle.\
  Ardından OK ile burdan çıkış yapalım.\
  $ <b><i>sudo nmcli connection down enp1s0 && sudo nmcli connection up enp1s0</b></i> --> Komutu ile modem interface yeniden başlat yapıyoruz.\
  $ <b>ip a</b> komutu ile değişiklikler kaydedilmiş mi diye kontrol ediyoruz.
  
</details>

### 2. BIND DNS Kurulumu ve Yapılandırması:
---
$ <b>dnf -y install bind bind-utils</b> --> Bind paketlerimizi sisteme yüklüyoruz.\
<b><i>BIND</b></i> default olarak <b><i>/etc/named.conf</b></i> dosyasını kullanıyor.\
$ <b><i>vim /etc/named.conf</b></i> --> Bind dosyasını düzenlicez.\
BURAYA <i>nameconf1.png</i> ekle.\
BURAYA <i>nameconf2.png</i> ekle.

zone “.” tanımını değiştirmememiz gerekiyor. Eğer NS tanımlı serverımız eğer olur da çalışmaz ise zone “.” tanımlı alan adına başvuracak.

<b><i>zone “example.com”</b></i> tanımı burada bize <b>example.com</b> adında alan adı oluşturcağımızı gösterir. <b>type master;</b> ile birincil isim sunucu olduğunu belirtiyor. <b>file “example.com.lan”;</b> ise alan adımız için oluşturacağımız forward ve reverse zone dosyalarımız default olarak named.conf dosyası içerisinde <b><i>/var/named</b></i> klasörü olarak ayarlıdır. Birazdan da göreceğiniz gibi zone dosyalarını /var/named klasörü içinde yapılandırcaz. file dosyasının ismini dilediğiniz gibi yapabilirsiniz buradaki örnekte <b>“example.com.len”</b> olarak tanımlandığı için bu sadece bir örnek.

example.com adlı alan adımızı 192.168.2.220 IP Adresinde açacağımızı düşünelim. <b>zone “2.168.192.in-addr.arpa”</b> tanımını sonundaki 8 byte'ı silersek 192.168.2 olarak kalıyor. Ve bu reverse zone tanımı olacağı için aklınızda tersten yazacağız diye aklınızda kalabilir 2.168.192 file dosyasının ismini dilediğiniz gibi yapabilirsiniz buradaki örnekte <b>“2.168.192.db”</b> olarak tanımlandığı için bu sadece bir örnek. İsterseniz “reverse.example.com” diye de tanımlayabilirdik.

BURAYA <i>forwardzone1.png</i> ekle.\
BURAYA <i>forwardzone2.png</i> ekle.\
BURAYA <i>reversezone1.png</i> ekle.\
BURAYA <i>reversezone2.png</i> ekle.

<b>IN --> İnternet tanımı olduğunu gösterir.\
TTL --> Time-to-Live (Yaşam Süresi).\
MX --> Mail Exchanger temsil eder.\
SOA --> Start of Authority, alan adı ile ilgili genel tanımları temsil eder.\
NS --> Name Server, alan adını tanımlar.\
A --> Alan adının hangi IP Adresine karşılık geldiğini temsil eder.
PTR --> Pointer, IP Adresinin hangi domaini temsil ettiğini temsil eder.</b>

Terminalden bind aktif etmemiz lazım.

$ <b>systemctl enable --now named\
BURAYA <i>enablebind1.png</i> ekle.

Arka planda Firewall çalışıyor ise:
$ firewall-cmd --add-service=dns --permanent\
BURAYA <i>enablebind2.png</i> ekle.
$ firewall-cmd --reload\
BURAYA <i>enablebind3.png</i> ekle.

$ nmcli connection modify enp0s3 ipv4.dns 192.168.2.220\
BURAYA <i>enablebind4.png</i> ekle.\
$ nmcli connection down enp0s3; nmcli connection up enp0s3</b>\
BURAYA <i>interfacereset.png</i> ekle.

BURAYA <i>dig1.png</i> ekle.\
BURAYA <i>dig2.png</i> ekle.

<b>dig</b> komutu sayesinde bize domaine bağlı olan kayıtları görebiliyoruz.

### 3. DHCP Server Kurulumu ve Yapılandırması:
---
$ <b>dnf -y install dhcp-server </b> --> DHCP Server kurulumunu yapıyoruz.\
BURAYA <i>dhcpserverkurulumu.png</i> ekle.\
$ <b>vim /etc/dhcp/dhcpd.conf</b> --> DHCP Server dosyamızın düzenlemesini yapmamız lazım.\
BURAYA <i>dhcp1.png</i>\
BURAYA <i>dhcp2.png</i>

$ <b>systemctl enable --now dhcpd</b> --> Sistemimize dhcp sunucumuzu aktif ediyoruz.

Arka planda Firewall çalışıyor ise:\
<b>$ firewall-cmd --add-service=dhcp --permanent</br> 
$ firewall-cmd --reload</b>
