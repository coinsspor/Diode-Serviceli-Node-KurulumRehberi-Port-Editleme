# 🌐 DIODE NODE KURULUM REHBERI (SNAP'SIZ, PORT EDİTLEME ILE)

## 1. GEREKLI SISTEM PAKETLERINI KUR
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git curl unzip make build-essential autoconf automake libssl-dev libreadline-dev libncurses5-dev libwxgtk3.0-gtk3-dev libgl1-mesa-dev libglu1-mesa-dev libpng-dev libssh-dev unixodbc-dev xsltproc fop libxml2-utils openjdk-11-jdk
```

## 2. ASDF YONETICISINI KUR
```bash
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.13.1
echo '. "$HOME/.asdf/asdf.sh"' >> ~/.bashrc
echo '. "$HOME/.asdf/completions/asdf.bash"' >> ~/.bashrc
echo '. "$HOME/.asdf/asdf.sh"' >> ~/.profile
echo '. "$HOME/.asdf/completions/asdf.bash"' >> ~/.profile
source ~/.bashrc
source ~/.profile
```

## 3. ASDF'YE ERLANG VE ELIXIR EKLE
```bash
asdf plugin-add erlang
asdf plugin-add elixir
```

## 4. ERLANG 25.3.2.8 KUR VE GLOBAL AYARLA
```bash
asdf install erlang 25.3.2.8
asdf global erlang 25.3.2.8
```
Kontrol etmek icin:
```bash
erl
```
Cikmak icin: `Ctrl+C` sonra `a` sonra `Enter`

## 5. ELIXIR 1.15.7 (ref) KUR VE GLOBAL AYARLA
```bash
asdf install elixir ref:v1.15.7
asdf global elixir ref-v1.15.7
```
✅ Ardından .tool-versions dosyasını oluştur:
```bash
echo -e "erlang 25.3.2.8\nelixir 1.15.7" > ~/.tool-versions
```
✅ Ortam değişkenlerini güncelle:
```bash
asdf reshim
source ~/.bashrc
source ~/.profile
```
Kontrol etmek icin:
```bash
elixir --version
mix --version
```
Dogru cikti:
```
Elixir 1.15.7 (compiled with Erlang/OTP 25)
Mix 1.15.7 (compiled with Elixir 1.15.7)
```

## 6. `.tool-versions` DOSYASINI SIL
```bash
rm ~/.tool-versions
```
**Neden?**
`ref:v1.15.7` yazili oldugu icin sorun cikmasin diye siliyoruz.
`asdf global` zaten ortam secimini yapti.

## 7. DIODE NODE KAYNAGINI CEK VE BAGIMLILIKLARI YUKLE
```bash
cd ~
git clone https://github.com/diodechain/diode_node.git
cd diode_node
mix deps.get
```
Eger `libsecp256k1` bagimliligi derleme hatasi verirse:
```bash
mix deps.compile libsecp256k1 --force
```

## 8. SYSTEMD SERVIS DOSYASINI OLUSTUR (PORT ENVIRONMENT ILE)
```bash
sudo nano /etc/systemd/system/diode-node.service
```
Icerik tam bu sekilde olsun çakışan portları sisteminize göre ayarlayın
```ini
[Unit]
Description=Diode Node Service
After=network.target

[Service]
User=root
WorkingDirectory=/root/diode_node
Environment=RPC_PORT=8545
Environment=RPCS_PORT=8443
Environment=EDGE2_PORT=41046,41045,993,1723,10000
Environment=PEER2_PORT=51055
Environment="PATH=/root/.asdf/installs/elixir/1.15.7/bin:/root/.asdf/installs/erlang/25.3.2.8/bin:/root/.asdf/shims:/root/.asdf/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
ExecStart=/root/.asdf/shims/mix run --no-halt
Restart=always
RestartSec=5
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target

```

## 9. SERVISI YUKLE, ETKINLESTIR VE BASLAT
```bash
sudo systemctl daemon-reload
sudo systemctl enable diode-node
sudo systemctl start diode-node
```

Servis durumu kontrol:
```bash
sudo systemctl status diode-node
```

Log takibi yapmak icin:
```bash
journalctl -u diode-node -f
```

---

# ✨ Kurulum Tamam! ✨
- Diode node aktif calisacak.
- Loglardan baglantilari gorebilirsin.
- Sistem her reboot'ta otomatik baslatir.

# 📍 EK BILGILER:
- Sorun olursa: `mix deps.get`, `mix deps.compile libsecp256k1 --force`, `systemctl restart diode-node`
- Cok buyuk bir hata olmazsa artik "libsecp256k1" derleme hatasi tekrarlamaz.

register yapacağınız adresi ilk etapta loglarda göremesseniz   bir kaç dakika sonra   
node yeniden başlatın 
```bash
sudo systemctl restart diode-node
```

Servis durumu kontrol:
```bash
sudo systemctl status diode-node
```

Log takibi yapmak icin:
```bash
journalctl -u diode-node -f
```
stausten ya da loglardan resimdeki gibi localhost yazan sizin uygulmadan register edeceğiniz adres
```bash
register node NODEID NODEADI
```

![image](https://github.com/user-attachments/assets/88924821-14a6-4dc0-be33-4043f8059cbb)


