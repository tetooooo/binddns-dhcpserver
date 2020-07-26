# Centos 8 BIND DNS ve DHCP Server Yapılandırması

İlk etapta Centos 8 Client'imize ***Static IP*** ataması ile başlayacağız. Ardından ***BIND DNS*** kurulumunu yaparak dosya ayarlamalarımızı yapacağız. En sonunda da ***DHCP Server*** kurulumunu yaparak bölümü bitirecez. Dosya düzeltmeleri için ben ***VIM EDITOR*** kullanmayı tercih ettim.


### 1. Centos 8 Static IP Ataması:
---

<details>
  
  <b><summary> Interface Dosyamızı Manual Yapılandırma: </summary></b>
  
  $ <b>ip a</b> komutunu kullanarak <b><i>Network Interface'lerimizi</i></b> görüntülüyoruz.\
  ![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/ipacommand.png) \
  Ben Interface olarak <b><i>enp0s3</i></b> kullanıyorum. Şimdi <b><i>/etc/sysconfig/network-script/ifcfg-enp0s3</b></i> komutu ile Interface ayarlarımı düzenlicem.\
  ![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/interfaceayarlari.png)\
  İlk kurulumda <b><i>BOOTPROTO</b></i> default olarak dhcp geliyor. Biz onu none ile değiştiriyoruz. Ayrıca <b><i>IPADDR, PREFIX, GATEWAY, DNS1</b></i> değişkenlerimizi manual olarak eklememiz lazım.\
  $ <b>nmcli connection down enp0s3; nmcli connection up enp0s3</b> --> Bu komut yardımı ile interface yeniden başlat yapıyoruz.\
  $ <b>ip a</b> komutu ile tekrardan istediğimiz IP Adresine ayarladı mı diye kontrol ediyoruz.
  
</details>

<details>
  
  <b><summary> nmtui Komutu Yardımıyla Yapılandırma: </summary></b>
  $ <b>nmtui</b>\
  ![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/nmtui1.png)\
  <b><i>Edit a connection</b></i> giriş yapalım.\
  ![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/nmtui2.png)\
  Gelen pencerede <b><i>Edit</b></i> seçeneğini seçelim.\
  ![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/nmtui3.png)\
  Ardından OK ile burdan çıkış yapalım.\
  $ <b><i>sudo nmcli connection down enp1s0 && sudo nmcli connection up enp1s0</b></i> --> Komutu ile modem interface yeniden başlat yapıyoruz.\
  $ <b>ip a</b> komutu ile değişiklikler kaydedilmiş mi diye kontrol ediyoruz.
  
</details>

### 2. BIND DNS Kurulumu ve Yapılandırması:
---
$ <b>dnf -y install bind bind-utils</b> --> Bind paketlerimizi sisteme yüklüyoruz.\
<b><i>BIND</b></i> default olarak <b><i>/etc/named.conf</b></i> dosyasını kullanıyor.\
$ <b><i>vim /etc/named.conf</b></i> --> Bind dosyasını düzenlicez.\
![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/namedconf1.png)\
![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/nameconf2.png)\

zone “.” tanımını değiştirmememiz gerekiyor. Eğer NS tanımlı serverımız eğer olur da çalışmaz ise zone “.” tanımlı alan adına başvuracak.

<b><i>zone “example.com”</b></i> tanımı burada bize <b>example.com</b> adında alan adı oluşturcağımızı gösterir. <b>type master;</b> ile birincil isim sunucu olduğunu belirtiyor. <b>file “example.com.lan”;</b> ise alan adımız için oluşturacağımız forward ve reverse zone dosyalarımız default olarak named.conf dosyası içerisinde <b><i>/var/named</b></i> klasörü olarak ayarlıdır. Birazdan da göreceğiniz gibi zone dosyalarını /var/named klasörü içinde yapılandırcaz. file dosyasının ismini dilediğiniz gibi yapabilirsiniz buradaki örnekte <b>“example.com.len”</b> olarak tanımlandığı için bu sadece bir örnek.

example.com adlı alan adımızı 192.168.2.220 IP Adresinde açacağımızı düşünelim. <b>zone “2.168.192.in-addr.arpa”</b> tanımını sonundaki 8 byte'ı silersek 192.168.2 olarak kalıyor. Ve bu reverse zone tanımı olacağı için aklınızda tersten yazacağız diye aklınızda kalabilir 2.168.192 file dosyasının ismini dilediğiniz gibi yapabilirsiniz buradaki örnekte <b>“2.168.192.db”</b> olarak tanımlandığı için bu sadece bir örnek. İsterseniz “reverse.example.com” diye de tanımlayabilirdik.

![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/forwardzone1.png)\
![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/forwardzone2.png)\
![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/reversezone1.png)\
![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/reversezone2.png)\

<b>IN --> İnternet tanımı olduğunu gösterir.\
TTL --> Time-to-Live (Yaşam Süresi).\
MX --> Mail Exchanger temsil eder.\
SOA --> Start of Authority, alan adı ile ilgili genel tanımları temsil eder.\
NS --> Name Server, alan adını tanımlar.\
A --> Alan adının hangi IP Adresine karşılık geldiğini temsil eder.
PTR --> Pointer, IP Adresinin hangi domaini temsil ettiğini temsil eder.</b>

Terminalden bind aktif etmemiz lazım.

$ <b>systemctl enable --now named\
![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/enablebind1.png)

Arka planda Firewall çalışıyor ise:
$ firewall-cmd --add-service=dns --permanent\
![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/enablebind2.png)\
$ firewall-cmd --reload\
![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/enablebind3.png)\

$ nmcli connection modify enp0s3 ipv4.dns 192.168.2.220\
![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/enablebind4.png)\
$ nmcli connection down enp0s3; nmcli connection up enp0s3</b>\
![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/interfacereset.png)

![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/dig1.png)\
![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/dig2.png)

<b>dig</b> komutu sayesinde bize domaine bağlı olan kayıtları görebiliyoruz.

### 3. DHCP Server Kurulumu ve Yapılandırması:
---
$ <b>dnf -y install dhcp-server </b> --> DHCP Server kurulumunu yapıyoruz.\
![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/dhcpserverkurulumu.png)\
$ <b>vim /etc/dhcp/dhcpd.conf</b> --> DHCP Server dosyamızın düzenlemesini yapmamız lazım.\
![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/dhcp2.png)\
![](https://github.com/tetooooo/binddns-dhcpserver/blob/master/images/dhcp3.png)

$ <b>systemctl enable --now dhcpd</b> --> Sistemimize dhcp sunucumuzu aktif ediyoruz.

Arka planda Firewall çalışıyor ise:\
<b>$ firewall-cmd --add-service=dhcp --permanent</br> 
$ firewall-cmd --reload</b>

<b><i>NOT: </b></i>Modem arayüzünden DHCP kapalı tutmanız lazım.
