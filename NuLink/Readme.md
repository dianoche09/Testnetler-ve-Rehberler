# NuLink Testnet Katılım Rehberi

![NuLinklogo](https://user-images.githubusercontent.com/107190154/190390683-f45a307d-73a1-476b-8216-f24f6ddff618.png)

### Minimum Gereksinimler

|      CPU        |   RAM    |  Disk    | 
|-----------------|----------|----------|
|2-4 CPU|   4GB    | 30GB    |

**Öncelikle bize lazım olan 9151 portumuzu açalım.**
```
sudo su
sudo ufw allow 9151
```
## Linux sistem güncellemesi yapıyoruz.
```
sudo apt-get update && apt-get upgrade -y
```

## Kütüphane kurulumunu yapıyoruz
```
sudo apt-get -y install libssl-dev && apt-get -y install cmake build-essential git wget jq make gcc
```

Bize gerekli olan dosyayı indiriyoruz ve ayarları yapıp bir worker hesabı oluşturuyoruz.
```
wget https://gethstore.blob.core.windows.net/builds/geth-linux-amd64-1.10.24-972007a5.tar.gz

tar -xvzf geth-linux-amd64-1.10.24-972007a5.tar.gz

cd geth-linux-amd64-1.10.24-972007a5/

./geth account new --keystore ./keystore
```
Şifre oluşturuyoruz. Bu kısımda şifreyi yazdıktan sonra bir kez daha aynı şifreyi yazıyorsunuz ve şifre oluşturuluyor.
> Çıktı şöyle görünecek burada size bir key verecek, onu not etmeyi unutmayın. 
> 
![nununu](https://user-images.githubusercontent.com/107190154/190369550-1ef68ab9-33d3-49ec-954e-0dd4b50173fe.png)

### Şimdi devam edelim, kuruluma
```
cd /root
```
>Docker'ı Yüklüyoruz.
```
sudo apt install docker.io
```
>Docker'ı başlatalım.
```
sudo systemctl enable --now docker
```
NuLink İmage dosyasını çekelim.
>İndirme işlemi yapacağı için biraz bekleyelim
```
docker pull nulink/nulink:latest
```
### nuLink adında bir dosya oluşturuyoruz
```
cd /root
mkdir nulink
```

**Burada `Path of the secret key file` yazan kısımda gördüğünüz kısmı alıp cp den sonraki kısma yapıştırıyorsunuz ve sonuna `/root/nulink` ekliyorsunuz.**
```
 cp burayapathofthesecretkeyfileyazın /root/nulink
```
![nunaa](https://user-images.githubusercontent.com/107190154/190372480-43c054fc-433d-47b7-bbb0-b53fca52da3f.png)

Örneğin şöyle; 
```
 cp /root/geth-linux-amd64-1.10.23-d901d853/keystore/UTC--2022-09-13T01-14-32.465358210Z--8b1819341bec211a45a2186c4d0030681ccce0ee /root/nulink
 ```
 **Bu Komutu yazmayı unutursanız hata alırsınız.**
 ```
 chmod -R 777 /root/nulink
```

### Değişkenleri ayarlayacağız
> Burada şifre olarak bazı karakterleri (en az 8 karakter) seçebilirsiniz.
```
export NULINK_KEYSTORE_PASSWORD=şifreniz

export NULINK_OPERATOR_ETH_PASSWORD=şifreniz
```

### Bu adım, NuLink çalışan node yapılandırmasını depolar ve yalnızca bir kez çalıştırılması gerekir.
> İlgili yerleri düzenledikten sonra komutu tek seferde girelim.
```
docker run -it --rm \
-p 9151:9151 \
-v /root/nulink:/code \
-v /root/nulink:/home/circleci/.local/share/nulink \
-e NULINK_KEYSTORE_PASSWORD \
nulink/nulink nulink ursula init \
--signer keystore:///code/ `Path of the secret key file` \
--eth-provider https://data-seed-prebsc-2-s2.binance.org:8545  \
--network horus \
--payment-provider https://data-seed-prebsc-2-s2.binance.org:8545 \
--payment-network bsc_testnet \
--operator-address adresiniz \
--max-gas-price 100
```
**signer keystore:, `///code/.......` bu kısma az evvel yukarıda da kullandığımız `Path of the secret key file` kısmında yazan yeri kopyalayacağız ancak `UTC` yazan yerden itibaren kopyalayacağız.**

Şöyle olacak;

Örneğin;--signer keystore:///code/UTC--2022-09-13T01-14-32.465358210Z--8b18193XXXXXXXXXXXXXXXXXXXXXXXXXe\

**`operator-address` yazan yere az evvel worker hesabı oluşturduğumuzda karşımıza çıkan `public address'i` yazıyoruz.**
**Örneğin; 0x8b18193XXXXXXXXXXXXXXXXXXXXXXXXXe**
>Sarı ile işaretli yerdeki adres `operator-address` olan yere yazacağız.
![nununu](https://user-images.githubusercontent.com/107190154/190402707-09fb815c-2021-42af-ad5b-a13ec90dbc60.png)

![gitnulink](https://user-images.githubusercontent.com/107190154/190388655-5c68865f-cfda-4dde-885f-56bf72b6d2f8.png)

### Örnek Çıktı;

**y/N kısmına y yazıp enter diyoruz.**

**Ardından 8 karakterli bir şifre oluşturuyoruz. Bu şifreleri bir yere not edin kaybolmasın.** 

**Şimdi karşımıza kelimelerimiz çıkacak, mavi renktedirler. Mutlaka bir yer yedekleyin. Tekrar kelimeleri görmen şansınız yok.**

**Ardından yine y/N sorusune y yazıp enter yapalım.**

**Açılan sayfaya az önce gelen kelimelerimizi yapıştıralım ve enter diyelim.**

**Arından Public Key ve keystore dosya dizinin yolunu gösteren bir çıktı alacağız.** 
```
# step 1
 Detected IPv4 address (8.219.186.125) - Is this the public-facing address of Ursula? [y/N]: y
 
 Please provide a password to lock Operator keys.
 Do not forget this password, and ideally store it using a password manager.
 
 # step 2
 Enter nulink keystore password (8 character minimum): xxxxxx
 Repeat for confirmation: xxxxxx
 
 Backup your seed words, you will not be able to view them again.
 
 xxxxxxxxxxxxxxxxxxxxxxxx
 
 # step 3
 Have you backed up your seed phrase? [y/N]: y
 
 # step 4
 Confirm seed words: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
 
Public Key:   02bb2067d21a677ce928967c0ece79a9
Path to Keystore: /home/circleci/.local/share/nulink/keystore

- You can share your public key with anyone. Others need it to interact with you.
- Never share secret keys with anyone! 
- Backup your keystore! Character keys are required to interact with the protocol!
- Remember your password! Without the password, it's impossible to decrypt the key!

Generated configuration file at default filepath /home/circleci/.local/share/nulink/ursula.json

* Review configuration  -> nulink ursula config
* Start working         -> nulink ursula run
```

![finalnu](https://user-images.githubusercontent.com/107190154/190388608-029e9da9-d664-4a0c-9c85-a149e32bfd7f.png)

### Aşağıdaki komutla node'u başlatıyoruz
> Direkt Girebilirsiniz. Size bir tx verecektir, tx verdiyse işlem başarılıdır.
```
docker run --restart on-failure -d \
--name ursula \
-p 9151:9151 \
-v /root/nulink:/code \
-v /root/nulink:/home/circleci/.local/share/nulink \
-e NULINK_KEYSTORE_PASSWORD \
-e NULINK_OPERATOR_ETH_PASSWORD \
nulink/nulink nulink ursula run --no-block-until-ready
```
![txnuu](https://user-images.githubusercontent.com/107190154/190388569-c7cc262a-b3c5-4003-9b00-bb828bf6d4fd.png)

### Kontrol sağlayalım
```
docker logs -f ursula
```
![image](https://user-images.githubusercontent.com/107190154/190395106-60cea495-64fc-47fa-b078-03248ae71e47.png)

### Artık Node'umuz çalışıyor. Ancak işlemler daha bitmedi. Birkaç adım daha var.

Cüzdanımızı metamask'a kurulum esnasında aldığımız kelimelerle import edelim ve siteye şu gidelim; https://test-staking.nulink.org/faucet
Metamaskta BSC test ağına geçelim. (Siteye bağlanmaya çalıştığınızda kendi geçiş yapıyor yapmazsa şuradan bsc [test](https://academy.binance.com/tr/articles/connecting-metamask-to-binance-smart-chain) ağını ekleyebilirsin.

Ardından BSC test ağına test token alalım ve bsc test token geldikten sonra nlk token da alalım faucetten.

![image](https://user-images.githubusercontent.com/107190154/190427147-1447808c-0518-4880-8467-ce5da67b5e0e.png)

Şimdi `Staking` kısmına gelelim. Şimdi aldığımız nlk tokenleri stake edeceğiz.

![image](https://user-images.githubusercontent.com/107190154/190427972-b6543a26-662e-4833-b48d-16132ec17a45.png)

Nlk tokenleri stake edelim.

![image](https://user-images.githubusercontent.com/107190154/190428750-73b8c80b-891e-4db7-bbff-f3d0a9f8c946.png)

`Confirm` diyelim.

![image](https://user-images.githubusercontent.com/107190154/190429119-fa098247-ca9d-421e-8168-76529b3cdeec.png)

Tokenlerimizi stake ettikten sonra şimdi `bond` işlemi yapacağız. `Bond owner` butonuna basalım.

![image](https://user-images.githubusercontent.com/107190154/190430550-6c5daa38-6601-47b9-8bcd-e6935b387093.png)

Şimdi karşımıza gelen ekranda doldurmamız gereken yerler var.

Worker adres az önce node kurarken oluşan public adresimiz, worker adres kısmına onu yazıyoruz.

Node Url kısmına da şu şekilde yazacağız. **https://sunucuip:9151**
> **örneğin: https://123.45.678:9151**

Sonra confirm butonuna basıp cüzdanımıza gelen işlemi onaylayalım.

Not: Sitede node online olup sonradan offline gözükürse endişelenmeyin, siteden kaynaklı bir durum.

İşlemler bu kadardı, yapılması gereken her şeyi yaptık. Hepinize kolay gelsin.

**Herhangi bir sorun ya da hata alırsanız bana telegramdan ya da discorddan ulabilirsiniz.** 

[Nulink Türkiye Telegram Kanalı](https://t.me/NuLink_Turkey)
[Nulink Discord Kanalı](https://discord.gg/wGvjRWtw)

### [Resmi Doküman için](https://docs.nulink.org/products/testnet)
### [Feedback Formu](https://forms.gle/EeSxZBZToB74scru7)