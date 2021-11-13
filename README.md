# Jarkom-Modul-3-D11-2021

Anggota kelompok:
- 05111940000067 - Fika Nur Aini
- 05111940000095 - Fuad Elhasan Irfani
- 05111940000203 - Fidhia Ainun Khofifah
---

1. EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server

    a. EnniesLobby sudah dikonfigurasi sebagai DNS server pada modul 2
    
    b. Jipangu sebagai DHCP server
    - Jalankan ```apt-get update```
    - Install DHCP server dengan ```apt-get install isc-dhcp-server```
    
    c. Water7 sebagai Proxy Server
    - Jalankan ```apt-get update```
    - Install Squid dengan ```apt-get install squid```

2. Foosha sebagai DHCP Relay
    - Jalankan ```apt-get update```
    - Install DHCP Relay dengan ```apt-get install isc-dhcp-relay```
    - Edit konfigurasi ```/etc/default/isc-dhcp-relay``` seperti berikut. Pada servers diisikan alamat DHCP server (Jipangu)
    
    ![image](https://user-images.githubusercontent.com/73324192/141602999-253bd63d-aac1-4e5f-a5be-cbac005a8812.png)

3. Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server. Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169

    a. Semua client menggunakan konfigurasi IP dari DHCP Server
    - Pada semua client (yang melalui switch 1 dan switch 3), edit ```/etc/network/interfaces``` menjadi seperti berikut
    
    ![image](https://user-images.githubusercontent.com/73324192/141603162-4c82d8b1-70e2-4013-8aef-fc1ea84ddb5e.png)
    
    b. Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169
    - Pada Jipangu, edit konfigurasi ```/etc/dhcp/dhcpd.conf``` tambahkan konfigurasi berikut
    
    ![image](https://user-images.githubusercontent.com/73324192/141603114-9e27a163-4797-4387-a5d3-19e4c3c94285.png)
    - Restart dhcp server, dhcp relay (jika perlu), dan restart node client
    - Client akan mendapat IP dari DHCP server
    
    ![image](https://user-images.githubusercontent.com/73324192/141603201-77270bdd-00d7-4c7a-a7d7-a82b163c2c24.png)

4. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50
    - Pada Jipangu, edit konfigurasi ```/etc/dhcp/dhcpd.conf``` tambahkan konfigurasi berikut
    
    ![image](https://user-images.githubusercontent.com/73324192/141603256-58de4d42-1bf5-4a9f-8677-f519b6cf15f3.png)
    - Restart dhcp server, dhcp relay (jika perlu), dan restart node client
    - Client akan mendapat IP dari DHCP server
    
    ![image](https://user-images.githubusercontent.com/73324192/141603340-f7a06226-5f14-44bf-8170-44e1387cf459.png)

5. Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut
    - Di Jipangu, pada konfigurasi ```/etc/dhcp/dhcpd.conf``` pastikan ```option domain-name-servers``` berisi DNS dari EniesLobby
    
    ![image](https://user-images.githubusercontent.com/73324192/141603415-03b0d5a6-e1f3-4a00-87d6-9507face69b5.png)
    - Agar dapat terhubung dengan internet, konfigurasikan DNS forwarder pada EniesLobby. Di EniesLobby edit ```/etc/bind/named.conf.options``` pada ```forwarders``` isikan DNS dari NAT. Comment ```dnssec-validation auto;``` dan tambahkan ```allow-query{any;};```
    
    ![image](https://user-images.githubusercontent.com/73324192/141603909-26f32d8a-61d8-4dd3-9005-db2410085c3b.png)
    - Restart service bind
    - Sekarang client bisa mengakses internet melalui DNS EniesLobby
    
    ![image](https://user-images.githubusercontent.com/73324192/141603692-9bb2fcc8-844c-410a-a45b-1b766e712470.png)

6. Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit.
    - Di Jipangu, pada konfigurasi ```/etc/dhcp/dhcpd.conf``` isikan ```default-lease-time``` dan ```max-lease-time``` sesuai soal (dalam detik).
    
    ![image](https://user-images.githubusercontent.com/73324192/141603766-b8b723ec-98f1-4138-9a8e-eeb372b59267.png)

7. Menjadikan Skypie dengan alamat IP yang tetap dengan IP [prefix IP].3.69
    - Cek hwaddress Skypie dengan ```ip a``` dan lihat pada ```eth0``` bagian ```link/ether```
    
    ![image](https://user-images.githubusercontent.com/73324192/141604066-9500cf57-52e7-4872-b0c2-14dbbf989849.png)
    - Di Jipangu, pada konfigurasi ```/etc/dhcp/dhcpd.conf``` tambahkan konfigurasi berikut. Hardware ethernet berisi hwaddress Skypie.
    
    ![image](https://user-images.githubusercontent.com/73324192/141603804-3bbf959b-0714-49ed-81d9-e209b8545e00.png)
    - Di Skypie, tambahkan konfigurasi hwaddress pada ```/etc/network/interfaces```
    
    ![image](https://user-images.githubusercontent.com/73324192/141604198-dd3bf564-db69-4c5f-aefb-97adade3756d.png)
    - Restart DHCP server dan node Skypie
    - Sekarang Skypie memiliki alamat tetap
    
    ![image](https://user-images.githubusercontent.com/73324192/141603867-5009286a-0cff-419f-874e-5470338fc042.png)
    
8. Loguetown digunakan sebagai client Proxy agar transaksi jual beli dapat terjamin keamanannya, juga untuk mencegah kebocoran data transaksi.Pada Loguetown, proxy harus bisa diakses dengan nama jualbelikapal.yyy.com dengan port yang digunakan adalah 5000.
    - Install squid pada Water7.
    - Tambahkan konfigurasi berikut pada ```/etc/squid/squid.conf```.
    
    ![image](https://user-images.githubusercontent.com/90237196/141643390-26346e87-900f-4c91-8a7d-a91254cc38b6.png)
    
    - Restart squid.
    - Aktifkan proxy pada Loguetown, kemudian cek apakah konfigurasi telah berhasil dengan ```env | grep -i proxy```.
    - Akses http://jualbelikapald11.com pada Loguetown.
    
    ![image](https://user-images.githubusercontent.com/90237196/141643418-a599330a-3dcd-419f-adf2-b60010b352ae.png)  

9. Agar transaksi jual beli lebih aman dan pengguna website ada dua orang, proxy dipasang autentikasi user proxy dengan enkripsi MD5 dengan dua username, yaitu luffybelikapalyyy dengan password luffy_yyy dan zorobelikapalyyy dengan password zoro_yyy 
    - Buat username dan password di Water7.
      ```
      httpasswd -c /etc/squid/passwd luffybelikapald11
      password: luffy_d11
      
      httpasswd -c /etc/squid/passwd zorobelikapald11
      password: zoro_d11
      ```
    - Tambahkan konfigurasi berikut pada ```/etc/squid/squid.conf```.
    
    ![image](https://user-images.githubusercontent.com/90237196/141643751-1d5cdb4c-2971-4b5b-acc7-7f05997b532a.png)
    
    - Akses http://jualbelikapald11.com pada Loguetown.

10. Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00)
    - Tambahkan konfigurasi berikut pada file ```/etc/squid/squid.conf```.
    
    ![image](https://user-images.githubusercontent.com/90237196/141644719-0bc0dd91-70a5-4266-b5fd-b87f7820d484.png)
    
    - Tambahkan konfigurasi berikut pada file ```/etc/squid/acl.conf```.
    
    ![image](https://user-images.githubusercontent.com/90237196/141646928-6ab503d2-5210-4fa8-882b-cb501c7a46a0.png)
    
    - Akses http://jualbelikapald11.com dengan command ```lynx jualbelikapald11.com```
    
    ![image](https://user-images.githubusercontent.com/90237196/141645575-ae870d1b-62a3-4b94-8fde-119f6aede96c.png) 

11. Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju super.franky.yyy.com dengan website yang sama pada soal shift modul 2. Web server super.franky.yyy.com berada pada node Skypie
    - Tambahkan konfigurasi berikut pada ```/etc/squid/squid.conf```.

    ![image](https://user-images.githubusercontent.com/90237196/141647256-7e9ae56c-e581-4608-8ed3-4a74b4db55cf.png)

    - Restart squid, kemudian akses google.com pada client.
    
    ![image](https://user-images.githubusercontent.com/90237196/141647283-dd3c4c1e-1774-423c-8348-10f70deba6a4.png)

    - Maka, google.com akan terarahkan pada super.franky.d11.com

    ![image](https://user-images.githubusercontent.com/90237196/141647298-4fd18de5-ae13-46e8-aeaf-1e8fce68e274.png)

12. 
