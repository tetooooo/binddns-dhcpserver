# Centos 8 BIND DNS ve DHCP Server Yapılandırması


İlk etapta Centos 8 Client'imize ***Static IP*** ataması ile başlayacağız. Ardından ***BIND DNS*** kurulumunu yaparak dosya ayarlamalarımızı yapacağız. En sonunda da ***DHCP Server*** kurulumunu yaparak bölümü bitirecez. Dosya düzeltmeleri için ben ***VIM EDITOR*** kullanmayı tercih ettim.


## 1. Centos 8 Static IP Ataması:

<details>
  
  <b><summary> Interface Dosyamızı Manual Yapılandırma: </summary>
  
  $ ip a komutunu kullanarak <i>Network Interface'lerimizi</i> görüntülüyoruz.\
  BURAYA <i>ipacommend.png</i> ekle.\
  Ben Interface olarak <i>enp0s3</i> kullanıyorum. Şimdi /etc/sysconfig/network-script/ifcfg-enp0s3 komutu ile Interface ayarlarımı düzenlicem.\
  BURAYA <i>interfaceayarlari.png</i> ekle.\
  İlk kurulumda <i>BOOTPROTO</i> default olarak dhcp geliyor. Biz onu none ile değiştiriyoruz. Ayrıca <i>IPADDR, PREFIX, GATEWAY, DNS1</i> değişkenlerimizi manual olarak eklememiz lazım.\
  $ nmcli connection down enp0s3; nmcli connection up enp0s3 --> Bu komut yardımı ile interface yeniden başlat yapıyoruz.\
  $ ip a komutu ile tekrardan istediğimiz IP Adresine ayarladı mı diye kontrol ediyoruz.\
  
</detais>

<details>
  
  <b><summary> nmtui Komutu Yardımıyla Yapılandırma: </summary>
  - nmtui --> komutunu çalıştırıyoruz.\
  BURAYA <i>nmtui1.png</i> ekle.\
  Edit a connection giriş yapalım.\
  BURAYA <i>nmtui2.png</i> ekle.\
  Gelen pencerede <i>Edit</i> seçeneğini seçelim.
  BURAYA <i>nmtui3.png</i> ekle.\
  Ardından OK ile burdan çıkış yapalım
  $ sudo nmcli connection down enp1s0 && sudo nmcli connection up enp1s0 --> Komutu ile modem interface yeniden başlat yapıyoruz.\
  $ ip a komutu ile değişiklikler kaydedilmiş mi diye kontrol ediyoruz.
  
</details>




