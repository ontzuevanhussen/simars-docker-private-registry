## SIMARS DOCKER DENGAN PRIVATE REGISTRY
SIMARS merupakan Sistem Informasi Manajemen Administrasi Rumah Sakit. Aplikasi ini dibuat dalam versi Docker (Hanya Aplikasi Web, server database dibuat terpisah dengan server Host).

### STEP 1: Install Docker dan Docker Compose<br>
Link install docker dan docker compose:
```
https://forum.simars.id/t/install-docker-dan-docker-compose-di-ubuntu/537
```

### STEP 2: Install Portainer: <br>
```
docker run -d -p 8000:8000 -p 9443:9443 --name=portainer-ce --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

### STEP 3: Clone SIMARS Docker melalui link github dibawah ini:
```
git clone https://github.com/ontzuevanhussen/simars-docker-localhost.git
```

### STEP 4: Masuk ke direktori simars docker yang telah di unduh: <br>
```
cd simars-docker-localhost
```

### STEP 5: Buat network baru dengan mode driver mode bridge: <br>
```
sudo docker network create proxy
```

### STEP 6: Jalankan Docker Compose: <br>
```
docker compose up -d
```

### STEP 7: Setelah file config.php di folder SIMARS sudah disesuaikan dengan server database, akses ke aplikasi SIMARS di alamat:
```
localhost:8080/simrs
```

***
#### Struktur folder SIMARS Docker:
```
simars-docker
  ├── simrs
  ├── Dockerfile
  ├── docker-compose.yml
  ├── cups
      ├── Dockerfile
      ├── cupsd.conf
      ├── printers.conf
      ├── smb.conf
```

#### Berikut isi dari file docker-compose.yml yang sudah didapatkan dari github sebelumnya:
```
services:
    php-httpd:
        build:
            context: .
        container_name: simars_app
        restart: always
        ports:
            - 8080:80
        environment:
            - TZ=Asia/Jakarta
        volumes:
            # sesuaikan dengan direktori di server hocker host
            - /home/xen:/var/www/html
        networks:
            - proxy
    cups:
        build:
            context: ./cups
        container_name: simars_cups
        restart: always
        ports:
            - 631:631
        devices:
            - /dev/bus/usb:/dev/bus/usb
        environment:
            - CUPSADMIN=print
            - CUPSPASSWORD=print
            - TZ=Asia/Jakarta
        volumes:
            - cups_data:/etc/cups
        networks:
            - proxy

networks:
  proxy:
    external: true

volumes:
    cups_data:
```
