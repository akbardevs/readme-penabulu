# How to install the website app

* [VPS]()
**Instalasi menggunakan Virtual Private Server**

a.	Update library OS
```
sudo apt update
sudo apt  -y
```

b.	Instalasi Nginx
```
sudo apt install nginx -y
sudo systemctl enable nginx 
sudo systemctl status nginx 
```

c.	Instalasi MariaDB
```
sudo apt install mariadb-server mariadb-client -y
sudo systemctl start mysql
sudo systemctl status mysql
```
Konfigurasi instalasi MariaDB, memberikan password pada user root, menghapus anonymous user, dll.
```
sudo mysql_secure_installation
:(Enter)
Y
```
Dilanjutkan dengan membuat database dan user tersendiri untuk project Laravel.
```
sudo mysql -u root -p
create database "nama_database"
create user "nama_user_database"@"localhost" identified by password;
grant all privileges on laravel.* to "nama_user_database"@"locahost";
flush privilages;
exit;
```

d.	Instalasi PHP
Instalasi PHP beserta ekstensi yang dibutuhkan
```
sudo apt install php7.2 php7.2-curl php7.2-common php7.2-cli php7.2-mysql php7.2-mbstring php7.2-fpm php7.2-xml php7.2-zip php7.2-bcmath -y
```

Restart dan aktifkan service php-fpm.
```
sudo systemctl restart php7.2.fpm
sudo systemctl enable php7.2.fpm
sudo systemctl status php7.2.fpm
```

e.	Instalasi Composer
```
sudo apt install composer -y
composer -v
```

f.	Konfigurasi Server Block Nginx
Membuat konfigurasi Nginx agar script PHP dapat dieksekusi oleh Nginx dan Laravel dapat diakses melalui subdomain penabulu.
```
cd /etc/nginx/site-available
sudo nano 'subdomain_name'
```
Selanjutnya editor nano akan terbuka, dilanjutkan dengan perintah berikut :
```
server {
         listen 80;
         server_name subdomain_name;

         root /var/www/laravel/public;
         index index.php index.html index.htm;
 
         location / {
                 try_files $uri $uri/ /index.php?$query_string;
         }
 
         location ~ \.php$ {
                 try_files $uri =404;
                 fastcgi_split_path_info ^(.+\.php)(/.+)$;
                 fastcgi_pass unix:/run/php/php7.2-fpm.sock;
                 fastcgi_index index.php;
                 fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                 include fastcgi_params;
         }

        location ~ /\.ht {
                deny all;
        }         

        access_log /var/log/nginx/laravel-access.log;
        error_log /var/log/nginx/laravel-error.log;
}

```

Aktifkan konfigurasi Server Block Nginx
```
sudo ln -s /etc/nginx/sites-available/subdomain_name /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
sudo systemctl status nginx
```

g.	Salin file Laravel ke VPS
* Menyalin file project Laravel kedalam direktori “/var/www/laravel”.
* Ubah hak akses dan kepemilikan file.
```
sudo chmod -R 775 /var/www/laravel
sudo chown -R $USER:www-data /var/www/laravel
```

h.	Menjalankan platform Penabulu
Mengatur database pada file “.env” disesuaikan dengan konfigurasi database lalu Masuk dalam direktori “/var/www/laravel” lalu menjalankan perintah berikut
“php artisan serve”.


**Instalasi menggunakan Shared Hosting(CPANEL)**

a.	Hilangkan “public” dari URL

Dalam shared hosting, menjalankan perintah “php artisan server” tidak memungkinkan, alternatif dari hal tersebut yaitu :

●	Pindahkan file “index.php” dan “.htaccess” yang berada dalam folder “public” keluar folder.

●	Membuka file “index.php” dan merubah baris ke-24 menjadi “require __DIR __. '/ vendor / autoload.php';”, selanjutnya baris ke-38 menjadi “$app = require_once __DIR__.'/bootstrap/app.php';”


b.	Export database 

c.	Mengubah folder project ke dalam bentuk “ZIP”/”rar”.

d.	Membuat database pada CPANEL

Login pada layanan shared hosting membuat database dan user control database pada “phpMyAdmin”


e.	Import database

f.	Upload file

Upload file “zip”/”rar” pada file manager shared hosting dalam folder “public_html”, setelah upload selesai maka file hasil project perlu untuk diekstraksi ke bentuk seperti sebelumnya.


g.	Update file konfigurasi database

Update file “database.php” dalam folder “config”, update dan sesuaikan dengan data user kontrol database.


h.	Keamanan

Menonaktifkan penjelajahan direktori, konfigurasi tersebut dilakukan pada file “.htaccess” dari folder  “public_html”.
```
#disable directory browsing
Option -Indexes

#PROTECT ENV FILE
<Files .env>
order allow,deny
Deny from all
</Files>

#PROTECT ENV FILE
<Files .htaccess>
order allow,deny
Deny from all
</Files>
```
