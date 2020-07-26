# Centos 8 BIND DNS ve DHCP Server Yapılandırması

İlk etapta Centos 8 Client'imize ***Static IP*** ataması ile başlayacağız. Ardından ***BIND DNS*** kurulumunu yaparak dosya ayarlamalarımızı yapacağız. En sonunda da ***DHCP Server*** kurulumunu yaparak bölümü bitirecez. Dosya düzeltmeleri için ben ***VIM EDITOR*** kullanmayı tercih ettim.


### 1. Centos 8 Static IP Ataması:

<details>
  
  <b><summary> Interface Dosyamızı Manual Yapılandırma: </summary>
  
  $ ip a komutunu kullanarak <i>Network Interface'lerimizi</i> görüntülüyoruz.\
  BURAYA <i>ipacommend.png</i> ekle.\
  Ben Interface olarak <i>enp0s3</i> kullanıyorum. Şimdi /etc/sysconfig/network-script/ifcfg-enp0s3 komutu ile Interface ayarlarımı düzenlicem.\
  BURAYA <i>interfaceayarlari.png</i> ekle.\
  İlk kurulumda <i>BOOTPROTO</i> default olarak dhcp geliyor. Biz onu none ile değiştiriyoruz. Ayrıca <i>IPADDR, PREFIX, GATEWAY, DNS1</i> değişkenlerimizi manual olarak eklememiz lazım.\
  $ nmcli connection down enp0s3; nmcli connection up enp0s3 --> Bu komut yardımı ile interface yeniden başlat yapıyoruz.\
  $ ip a komutu ile tekrardan istediğimiz IP Adresine ayarladı mı diye kontrol ediyoruz.
  
</details>

<details>
  
  <b><summary> nmtui Komutu Yardımıyla Yapılandırma: </summary>
  - nmtui --> komutunu çalıştırıyoruz.\
  BURAYA <i>nmtui1.png</i> ekle.\
  Edit a connection giriş yapalım.\
  BURAYA <i>nmtui2.png</i> ekle.\
  Gelen pencerede <i>Edit</i> seçeneğini seçelim.\
  BURAYA <i>nmtui3.png</i> ekle.\
  Ardından OK ile burdan çıkış yapalım.\
  $ sudo nmcli connection down enp1s0 && sudo nmcli connection up enp1s0 --> Komutu ile modem interface yeniden başlat yapıyoruz.\
  $ ip a komutu ile değişiklikler kaydedilmiş mi diye kontrol ediyoruz.
  
</details>

### 2. BIND DNS Kurulumu ve Yapılandırması:
$ dnf -y install bind bind-utils --> Bind paketlerimizi sisteme yüklüyoruz.\
<b>BIND</b> default olarak <b>/etc/named.conf</b> dosyasını kullanıyor.\
$ vim /etc/named.conf --> Bind dosyasını düzenlicez.\
BURAYA <i>nameconf1.png</i> ekle.\
BURAYA <i>nameconf2.png</i> ekle.

zone “.” tanımını değiştirmememiz gerekiyor. Eğer NS tanımlı serverımız eğer olur da çalışmaz ise zone “.” tanımlı alan adına başvuracak.

zone “srv.world” tanımı burada bize srv.world adında alan adı oluşturcağımızı gösterir. type master; ile birincil isim sunucu olduğunu belirtiyor. file “srv.world.lan”; ise alan adımız için oluşturacağımız forward ve reverse zone dosyalarımız default olarak named.conf dosyası içerisinde /var/named dosyası olarak ayarlıdır. Birazdan da göreceğiniz gibi zone dosyalarını /var/named dosyası içinde yapılandırcaz. file dosyasının ismini dilediğiniz gibi yapabilirsiniz buradaki örnekte “srv.world.len” olarak tanımlandığı için bu sadece bir örnek.

srv.world adlı alan adımızı 10.0.0.30 ip adresinde açacağımızı düşünelim. zone “0.0.10.in-addr.arpa” tanımını sonundaki 8 byte'ı silersek 10.0.0 olarak kalıyor. Ve bu reverse zone tanımı olacağı için aklınızda tersten yazacağız diye aklınızda kalabilir 0.0.10. file dosyasının ismini dilediğiniz gibi yapabilirsiniz buradaki örnekte “0.0.10.db” olarak tanımlandığı için bu sadece bir örnek. İsterseniz “reverse.srv.world” diye de tanımlayabilirdik.

BURAYA <i>forwardzone1.png</i> ekle.\
BURAYA <i>forwardzone2.png</i> ekle.\
BURAYA <i>reversezone1.png</i> ekle.\
BURAYA <i>reversezone2.png</i> ekle.

IN --> İnternet tanımı olduğunu gösterir.\
TTL --> Time-to-Live (Yaşam Süresi).\
MX --> Mail Exchanger temsil eder.\
SOA --> Start of Authority, alan adı ile ilgili genel tanımları temsil eder.\
NS --> Name Server, alan adını tanımlar.\
A --> Alan adının hangi IP Adresine karşılık geldiğini temsil eder.
PTR --> Pointer, IP Adresinin hangi domaini temsil ettiğini temsil eder.

Terminalden bind aktif etmemiz lazım.

$ systemctl enable --now named\
BURAYA <i>enablebind1.png</i> ekle.\
$ firewall-cmd --add-service=dns --permanent\
BURAYA <i>enablebind2.png</i> ekle.\
$ firewall-cmd --reload\
BURAYA <i>enablebind3.png</i> ekle.\
$ nmcli connection modify enp0s3 ipv4.dns 192.168.2.220\
BURAYA <i>enablebind4.png</i> ekle.\
$ nmcli connection down enp0s3; nmcli connection up enp0s3\
BURAYA <i>interfacereset.png</i> ekle.


















