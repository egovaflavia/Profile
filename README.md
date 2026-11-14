# Egova Flavia — Portfolio & Landing Page (`egova.my.id`)

Landing page profil resmi **Egova Flavia (`@egovaflavia`)** yang disusun berdasarkan analisis lebih dari **190+ repositori GitHub** (135+ repositori publik & 60+ sistem informasi privat/enterprise).

Dirancang ringan menggunakan **HTML5, Tailwind CSS, dan Vanilla JavaScript**, siap di-deploy ke **aaPanel (Pure Static + CI/CD WebHook)** maupun **Docker + Nginx Alpine** di **Homeserver (`egova.my.id`)**.

---

## ✨ Fitur Utama

1. **Hero & Interactive Homeserver Terminal (`egova@homeserver:~$`)**
   - Menampilkan identitas, bio *"Agak-agak kiro-kiro"*, afiliasi **Semen Padang Hospital**, dan portal **LokerSumbar.info**.
   - Dilengkapi emulator terminal interaktif (`whoami`, `stack`, `sph`, `repos`, `homeserver`, `contact`, `clear`).
2. **Proyek Unggulan & Sistem Informasi (Gabungan Portfolio Produksi)**
   - **Healthcare & SIMRS**: Medical Check-Up (`mcu.semenpadanghospital.co.id`, MCU Tambang, Re-MCU), Bridging BPJS Kesehatan & Service API JKN Mobile (`Larabridge-SPHXBPJS`), EMR Klinik Utama Pessel, SIMLABOR, Portal Korporat Vue.js, Kapitasi & Piutang.
   - **Web Platform & CMS**: `LokerSumbar.info` (Livewire), `PRO-LOMBA`, `Scolafest`, `filament4cms`.
   - **Enterprise, GIS & SPK**: GIS Dokumentasi Adat, Pelaporan Aset Pemerintah, POS, serta SPK metode MAUT & TOPSIS.
   - **DevOps & AI Tooling**: `DOCKER-SETUP-FOR-LARAVEL`, `WSL-LERD`, Cloudflare Tunnel, `vibes-plug` & OpenClaw AI.
3. **Eksplorasi Repositori Publik (Live Sync GitHub API + Offline Fallback)**
   - Terhubung secara otomatis ke `https://api.github.com/users/egovaflavia/repos` untuk menyinkronkan jumlah repo, followers, dan update terbaru, dilengkapi data fallback terkurasi.
   - Mendukung pencarian instan dan filter kategori (*Laravel & PHP*, *Vue & Frontend*, *Docker, WSL & DevOps*, *AI & Vibe Coding*, *Panduan & Docs*).
4. **Dark & Light Mode**
   - Tema gelap khas terminal/homeserver secara default dengan tombol pengalih ke mode terang.

---

## 🚀 Panduan Arsitektur & Alur Kerja CI/CD di aaPanel (`egova.my.id`)

Karena proyek ini berbasis **Pure Static (HTML + CSS + JS)**, Anda tidak memerlukan PHP-FPM, MySQL, maupun proses build `npm` di server—sehingga jauh lebih ringan dan instan.

### Tahap 1: Pengaturan DNS Domain di Cloudflare
Masuk ke dasbor Cloudflare untuk domain **`egova.my.id`**, buka menu **DNS → Records**, lalu arahkan ke IP Publik Homeserver/VPS Anda:

| Type | Name / Host | Target / IPv4 Address | Proxy Status | TTL |
| :--- | :--- | :--- | :--- | :--- |
| **A** | `@` | `IP_SERVER_ANDA` | Proxied / DNS Only | Auto |
| **A** | `www` | `IP_SERVER_ANDA` | Proxied / DNS Only | Auto |

> **Catatan:** Jika Anda akan menerbitkan SSL **Let's Encrypt** di aaPanel (Tahap 3), matikan sementara Proxy Cloudflare (**DNS Only / Awan Abu-abu**) saat klik *Apply SSL*, lalu nyalakan kembali (**Proxied / Awan Oranye**) dan atur mode **SSL/TLS → Full** setelah sertifikat terbit.

---

### Tahap 2: Persiapan Lingkungan Server di aaPanel
1. Pastikan **Nginx** (v1.24 / v1.26) sudah terpasang dan berjalan di **App Store aaPanel**.
2. Pastikan **AAWaf / WebHook** plugin terpasang di **App Store → Deployment → WebHook** (jika ingin mengaktifkan *Auto-Deploy* setiap kali `git push`).
3. *(Tidak diperlukan instalasi PHP, ekstensi PHP, MySQL, ataupun Node.js untuk proyek statis ini).*

---

### Tahap 3: Pembuatan Situs & Web Server (Nginx) di aaPanel

#### 1. Buat Website Baru
- Buka menu **Website** di sidebar aaPanel → Klik **Add site**:
  - **Domain name:** Masukkan satu per baris:
    ```text
    egova.my.id
    www.egova.my.id
    ```
  - **PHP Version:** Pilih **Pure Static**
  - **Database:** Pilih **Do not create**
  - Klik **Submit**.

#### 2. Pengaturan Site Directory & URL Rewrite
- **Site directory:** Biarkan *Running directory* tetap di `/` (karena `index.html` berada langsung di root repositori, bukan di `/public`).
- **URL Rewrite:** Biarkan kosong / default (tidak memerlukan rewrite `index.php` milik Laravel).

#### 3. Pasang SSL (HTTPS Gratis Let's Encrypt)
- Klik nama situs `egova.my.id` → Buka tab **SSL** → Pilih tab **Let's Encrypt**.
- Centang kedua domain (`egova.my.id` dan `www.egova.my.id`).
- Klik **Apply**.
- Setelah sertifikat terbit, aktifkan saklar **Force HTTPS**.

---

### Tahap 4: Otentikasi SSH Deploy Key (GitHub ke Server aaPanel)
*(Lewati pembuatan key baru jika di server sudah pernah Anda konfigurasi `~/.ssh/id_ed25519_deploy`, cukup daftarkan public key-nya ke repo `Profile` jika repo bersifat Private).*

#### 1. Buat SSH Key Khusus Deploy di Terminal Server
Buka **Terminal** aaPanel (atau via SSH):
```bash
ssh-keygen -t ed25519 -C "deploy-egova-profile" -f ~/.ssh/id_ed25519_profile -N ""
```

#### 2. Daftarkan Public Key ke Repositori GitHub `egovaflavia/Profile`
Tampilkan isi kunci publik:
```bash
cat ~/.ssh/id_ed25519_profile.pub
```
- Buka GitHub: **`https://github.com/egovaflavia/Profile/settings/keys`** (**Settings → Deploy keys → Add deploy key**).
- **Title:** `aaPanel Homeserver egova.my.id`
- **Key:** Tempelkan seluruh hasil output di atas.
- Klik **Add key** (biarkan *Allow write access* tidak dicentang).

#### 3. Konfigurasi SSH Client & User `www` di Server
```bash
cat << 'EOF' >> ~/.ssh/config

Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_profile
    IdentitiesOnly yes
    StrictHostKeyChecking no
EOF
chmod 600 ~/.ssh/config

# Salin akses kunci untuk user sistem www (agar WebHook aaPanel bisa git pull)
mkdir -p /etc/ssh/deploy_keys /home/www/.ssh /www/.ssh
cp ~/.ssh/id_ed25519_profile /etc/ssh/deploy_keys/id_ed25519_profile
chown -R www:www /etc/ssh/deploy_keys /home/www/.ssh /www/.ssh
chmod 600 /etc/ssh/deploy_keys/id_ed25519_profile
cp ~/.ssh/config /home/www/.ssh/config 2>/dev/null || true
cp ~/.ssh/config /www/.ssh/config 2>/dev/null || true
git config --system --add safe.directory '*'
```

#### 4. Uji Koneksi SSH ke GitHub
```bash
ssh -T git@github.com
```
Pastikan muncul: `Hi egovaflavia/Profile! You've successfully authenticated...`

---

### Tahap 5: Tarik Kode Pertama Kali & Integrasi WebHook CI/CD

#### 1. Clone Repositori ke Folder `/www/wwwroot/egova.my.id`
Jalankan perintah ini di Terminal aaPanel:
```bash
cd /www/wwwroot/egova.my.id
rm -rf index.html 404.html .htaccess
git clone git@github.com:egovaflavia/Profile.git .
chown -R www:www /www/wwwroot/egova.my.id
```
*(Catatan: Karena repo `egovaflavia/Profile` berstatus Public, Anda juga bisa memakai URL HTTPS `https://github.com/egovaflavia/Profile.git` jika tidak memakai SSH Deploy Key).*

#### 2. Buat Script Auto-Deploy di Plugin WebHook aaPanel (Opsional CI/CD)
Agar setiap kali Anda melakukan `git push` dari laptop ke GitHub maka `egova.my.id` otomatis ter-update:
1. Buka **App Store → WebHook → Add Hook**:
   - **Title:** `Deploy egova.my.id`
   - **Execution script:**
     ```bash
     #!/bin/bash
     echo ""
     date --date='0 days ago' "+%Y-%m-%d %H:%M:%S"
     echo "Start deployment egova.my.id..."

     WEB_PATH="/www/wwwroot/egova.my.id"
     cd $WEB_PATH
     git fetch --all
     git reset --hard origin/main
     git pull origin main
     chown -R www:www $WEB_PATH
     chmod -R 755 $WEB_PATH

     echo "Deployment finished successfully!"
     ```
   - Klik **Submit**.
2. Klik **View Key** pada daftar WebHook aaPanel, lalu salin **Webhook URL**-nya.
3. Buka GitHub: **`https://github.com/egovaflavia/Profile/settings/hooks`** (**Settings → Webhooks → Add webhook**):
   - **Payload URL:** Tempelkan URL WebHook aaPanel tadi.
   - **Content type:** `application/json`
   - **SSL verification:** Pilih *Disable (not recommended)* jika panel aaPanel belum memakai domain ber-SSL valid.
   - Klik **Add webhook**.

---

## 📁 Struktur Direktori

```text
.
├── index.html              # Halaman utama landing page egova.my.id
├── assets/
│   ├── css/
│   │   └── style.css       # Custom styling, glassmorphism & dark/light theme
│   └── js/
│       └── app.js          # Data proyek, GitHub API sync, filter & terminal emulator
├── Dockerfile              # Image ringan berbasis nginx:stable-alpine (opsional)
├── docker-compose.yml      # Konfigurasi container untuk Docker (opsional)
├── nginx.conf              # Konfigurasi Nginx (gzip, cache, security headers, /healthz)
└── README.md               # Dokumentasi CI/CD & deployment aaPanel
```
