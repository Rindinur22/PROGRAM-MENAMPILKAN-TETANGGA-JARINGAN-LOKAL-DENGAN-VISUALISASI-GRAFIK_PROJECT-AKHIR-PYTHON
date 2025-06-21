# Import library matplotlib untuk membuat grafik visualisasi
import matplotlib.pyplot as plt
#RINDI NUR WULANDARI/23030013
#PROJECT AKHIR PEMROGRAMAN SISTEM
# Import subprocess untuk menjalankan perintah sistem seperti 'ping' dan 'arp'
import subprocess
# Import re (regular expression) untuk mengambil IP dan MAC dari output teks
import re
# Fungsi untuk melakukan pemindaian jaringan lokal
def scan_jaringan():
    print("🔍 Memindai jaringan, mohon tunggu...\n")

    # Melakukan ping ke IP dari 192.168.1.1 hingga 192.168.1.9
    # Tujuannya agar perangkat tersebut masuk ke dalam ARP table
    for i in range(1, 10):  # Bisa disesuaikan rentangnya
        ip = f"192.168.1.{i}"
        # Ping 1x dengan timeout 200ms, hasilnya tidak ditampilkan
        subprocess.run(f"ping -n 1 -w 200 {ip}", stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)

    # Mengambil data ARP table dari sistem menggunakan perintah 'arp -a'
    hasil = subprocess.check_output("arp -a", shell=True).decode()

    # Menggunakan regex untuk mengekstrak pasangan alamat IP dan MAC
    # Format hasil ARP: IP    MAC
    data = re.findall(r"(\d+\.\d+\.\d+\.\d+)\s+([\w-]{17})", hasil)

    # Mengembalikan daftar perangkat berupa tuple (IP, MAC)
    return data

# Fungsi untuk menampilkan hasil pemindaian ke dalam bentuk grafik batang horizontal
def tampilkan(data):
    # Mengambil daftar IP dan MAC dari hasil pemindaian
    ip_list = [ip for ip, mac in data]
    mac_list = [mac for ip, mac in data]

    # Membuat figure grafik berukuran 8x5 inci
    plt.figure(figsize=(8, 5))

    # Menampilkan grafik batang horizontal untuk setiap IP
    plt.barh(ip_list, range(len(ip_list)), color='skyblue')

    # Menambahkan label teks MAC Address di samping masing-masing batang
    for i, mac in enumerate(mac_list):
        plt.text(0.5, i, f"MAC: {mac}", va='center', ha='left', fontsize=9)

    # Label sumbu X
    plt.xlabel('Alamat IP')

    # Judul grafik
    plt.title('Perangkat di Jaringan (ARP Table)')

    # Menampilkan IP di sumbu Y
    plt.yticks(range(len(ip_list)), ip_list)

    # Merapikan layout agar tidak terpotong
    plt.tight_layout()

    # Menampilkan grafik ke layar
    plt.show()

# Memanggil fungsi untuk memindai jaringan dan menampilkan hasilnya
devices = scan_jaringan()
tampilkan(devices)
