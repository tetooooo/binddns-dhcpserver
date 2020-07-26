# Centos 8 BIND DNS ve DHCP Server Yapılandırması


İlk etapta Centos 8 Client'imize ***Static IP*** ataması ile başlayacağız. Ardından ***BIND DNS*** kurulumunu yaparak dosya ayarlamalarımızı yapacağız. En sonunda da ***DHCP Server*** kurulumunu yaparak bölümü bitirecez. Dosya düzeltmeleri için ben ***VIM EDITOR*** kullanmayı tercih ettim.


## 1. Centos 8 Static IP Ataması:

<details>
  
  <b><summary> İnterface Dosyamızı Manual Değiştirme: </summary>
  
  -ip a komutunu kullanarak <i>Network Interface'lerimizi</i> görüntülüyoruz.\
  BURAYA <i>ipacommend.png</i> ekle.\
  Ben Interface olarak <i>enp0s3</i> kullanıyorum. Şimdi /etc/sysconfig/network-script/ifcfg-enp0s3 komutu ile Interface ayarlarımı düzenlicem.\
  BURAYA <i>interfaceayarlari</i> ekle.\
  İlk kurulumda <i>BOOTPROTO</i> default olarak dhcp geliyor. Biz onu none ile değiştiriyoruz. Ayrıca <i>IPADDR, PREFIX, GATEWAY, DNS1</i> değişkenlerimizi manual olarak eklememiz lazım.\
  -nmcli connection down enp0s3; nmcli connection up enp0s3 --> Bu komut yardımı ile interface yeniden başlat yapıyoruz.\
  -ip a komutu ile tekrardan istediğimiz IP Adresine ayarladı mı diye kontrol ediyoruz.
  
</detais>




