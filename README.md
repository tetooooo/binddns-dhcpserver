# Centos 8 BIND DNS ve DHCP Server Yapılandırması


İlk etapta Centos 8 Client'imize ***Static IP*** ataması ile başlayacağız. Ardından ***BIND DNS*** kurulumunu yaparak dosya ayarlamalarımızı yapacağız. En sonunda da ***DHCP Server*** kurulumunu yaparak bölümü bitirecez. Dosya düzeltmeleri için ben ***VIM EDITOR*** kullanmayı tercih ettim.


## 1. Centos 8 Static IP Ataması:

<details>
  
  <b><summary> İnterface Dosyamızı Manual Değiştirme: </summary>
  
  ip a komutunu kullanarak <i>Network Interface'lerimizi</i> görüntülüyoruz.\
  BURAYA <b>ipacommend.png</b> ekle.\
  Ben Interface olarak <i>enp0s3</i> kullanıyorum. Şimdi /etc/sysconfig/network-script/ifcfg-enp0s3 komutu ile Interface ayarlarımı düzenlicem.\
  BURAYA <b>interfaceayarlari</b> ekle.\
  İlk kurulumda <b>BOOTPROTO</b> default olarak dhcp geliyor. Biz onu none ile değiştiriyoruz. Ayrıca <b>IPADDR, PREFIX, GATEWAY, DNS1</b> değişkenlerimizi manual olarak eklememiz lazım.
  
</detais>




