
# === Buat folder project ===
mkdir -p ~/mawari-node-manager
cd ~/mawari-node-manager

# === Buat script utama (mawari.sh) ===
nano mawari.sh
# (#!/bin/bash

# Mawari Guardian Node Manager (Multi-Container + Auto Update + Rename)
# by Jackxyz72 :)

# Variabel default
IMAGE="us-east4-docker.pkg.dev/mawarinetwork-dev/mwr-net-d-car-uses4-public-docker-registry-e62e/mawari-node:latest"
CACHE_DIR="$HOME/mawari"

# Fungsi: instal Docker
install_docker() {
    echo "🔧 Menginstal Docker..."
    sudo apt update && sudo apt upgrade -y
    sudo apt install -y docker.io
    sudo systemctl enable docker
    sudo systemctl start docker
    echo "✅ Docker terinstal."
}

# Fungsi: jalankan node baru
start_node() {
    read -p "Masukkan nama container: " NODE_NAME
    read -p "Masukkan alamat wallet (0x...): " OWNER_ADDRESS
    mkdir -p $CACHE_DIR/$NODE_NAME
    echo "🚀 Menjalankan Mawari Node ($NODE_NAME)..."
    docker run -d --name $NODE_NAME --pull always \
        -v $CACHE_DIR/$NODE_NAME:/app/cache \
        -e OWNERS_ALLOWLIST=$OWNER_ADDRESS \
        $IMAGE
    echo "✅ Node $NODE_NAME dijalankan."
}

# Fungsi: hentikan node
stop_node() {
    echo "📦 Daftar container:"
    docker ps -a --format "table {{.Names}}\t{{.Status}}"
    echo ""
    read -p "Masukkan nama container yang mau dihentikan: " NODE_NAME
    docker stop $NODE_NAME && docker rm $NODE_NAME
    echo "✅ Node $NODE_NAME dihentikan & dihapus."
}

# Fungsi: cek status semua node
status_node() {
    docker ps -a --format "table {{.Names}}\t{{.Status}}\t{{.Image}}"
}

# Fungsi: lihat log node
logs_node() {
    echo "📦 Daftar container:"
    docker ps -a --format "table {{.Names}}\t{{.Status}}"
    echo ""
    read -p "Masukkan nama container untuk lihat log: " NODE_NAME
    docker logs -f $NODE_NAME
}

# Fungsi: auto update node
update_node() {
    echo "📦 Daftar container:"
    docker ps -a --format "table {{.Names}}\t{{.Status}}"
    echo ""
    read -p "Masukkan nama container untuk update: " NODE_NAME
    echo "🔄 Pull image terbaru..."
    docker pull $IMAGE
    echo "🛑 Stop $NODE_NAME..."
    docker stop $NODE_NAME
    echo "🚀 Jalankan ulang $NODE_NAME dengan image terbaru..."
    docker rm $NODE_NAME
    docker run -d --name $NODE_NAME \
        -v $CACHE_DIR/$NODE_NAME:/app/cache \
        -e OWNERS_ALLOWLIST=$OWNER_ADDRESS \
        $IMAGE
    echo "✅ Node $NODE_NAME berhasil diperbarui."
}

# Fungsi: rename container
rename_node() {
    echo "📦 Daftar container:"
    docker ps -a --format "table {{.Names}}\t{{.Status}}"
    echo ""
    read -p "Masukkan nama container lama: " OLD_NAME
    read -p "Masukkan nama container baru: " NEW_NAME
    echo "🛑 Menghentikan container $OLD_NAME..."
    docker stop $OLD_NAME
    echo "✏️ Rename $OLD_NAME → $NEW_NAME..."
    docker rename $OLD_NAME $NEW_NAME
    echo "🚀 Menjalankan kembali $NEW_NAME..."
    docker start $NEW_NAME
    echo "✅ Container berhasil di-rename!"
}

# Menu interaktif
while true; do
    clear
    echo "======================================="
    echo "   Mawari Guardian Node Multi-Manager  "
    echo "======================================="
    echo "1) Instal Docker"
    echo "2) Jalankan Node Baru"
    echo "3) Hentikan Node"
    echo "4) Cek Status Semua Node"
    echo "5) Lihat Log Node"
    echo "6) Auto Update Node"
    echo "7) Rename Container"
    echo "8) Keluar"
    echo "======================================="
    read -p "Pilih menu [1-8]: " choice

    case $choice in
        1) install_docker ;;
        2) start_node ;;
        3) stop_node ;;
        4) status_node ;;
        5) logs_node ;;
        6) update_node ;;
        7) rename_node ;;
        8) echo "👋 Keluar..."; exit 0 ;;
        *) echo "❌ Pilihan tidak valid!";;
    esac

    echo ""
    read -p "Tekan [Enter] untuk kembali ke menu..."
done
chmod +x mawari.sh

# === Buat README.md ===
cat > README.md << 'EOF'
# Mawari Guardian Node Manager

[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Made with Bash](https://img.shields.io/badge/Made%20with-Bash-blue.svg)](https://www.gnu.org/software/bash/)

Script Bash interaktif untuk mengelola **Mawari Guardian Node** di Linux.  
Mendukung multi-container, auto update, dan rename container dengan menu yang mudah digunakan.

---

## ✨ Fitur
- 🚀 Instalasi Docker otomatis  
- 📦 Jalankan banyak node (multi-container)  
- ⏹️ Hentikan & hapus node  
- 🔍 Cek status semua node  
- 📜 Lihat log node  
- 🔄 Auto update ke image terbaru  
- ✏️ Rename container tanpa repot  

---

## 📦 Instalasi

```bash
git clone https://github.com/Jackxyz72/Mawari-Guardian-Node-Manager-Multi-Container-Auto-Update-Rename-by-Jackxyz-.git
cd mawari-node-manager
chmod +x mawari.sh
