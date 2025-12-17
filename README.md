# suricata-ids-ips

1. Install Suricata 
Jalankan sebagai IDS

3. Deteksi: 
Port scanning (Nmap) 
SSH brute force 

4. Analisa log (eve.json)

🔹 STEP 1 – Install Suricata (Ubuntu Server)
> sudo apt update && sudo apt install suricata -y  
> suricata --version  
  
🔹 STEP 2 – Tentukan Interface Network  
> ip addr  
> ens33 -> catat interface  
  
🔹 STEP 3 – Konfigurasi Suricata Interface  
> sudo nano /etc/suricata/suricata.yaml  

Cari dan sesuaikan bagian interface:
> af-packet:
>   - interface: ens33

🔹 STEP 4 – Download Rule IDS (Emerging Threats)
> sudo suricata-update 
Cek rule terpasang:
> ls /var/lib/suricata/rules/

🔹 STEP 5 – Jalankan Suricata (IDS Mode) 
> sudo systemctl stop suricata
> sudo suricata -c /etc/suricata/suricata.yaml -i ens33

Kalau tidak ada error → Suricata aktif ✅ 

🔹 STEP 6 – Simulasi Serangan (Dari Kali Linux) 
Dari Kali : 
> nmap -sS <IP_UBUNTU> 

6.2 SSH Brute Force (Simulasi) 
Dari Kali: 
> hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://<IP_UBUNTU> 

🔹 STEP 7 – Analisa Alert Suricata 
> sudo tail -f /var/log/suricata/eve.json
contoh alert:
> "alert": {
>   "signature": "ET SCAN Nmap Scripting Engine User-Agent Detected",
>   "severity": 2
> }

🔹 STEP 8 – Filter Alert Penting 
Port Scan: 
> jq 'select(.alert.signature | contains("Nmap"))' /var/log/suricata/eve.json 

SSH Bruteforce: 
> jq 'select(.alert.signature | contains("SSH"))' /var/log/suricata/eve.json 

6️⃣ Re-check alert setelah scan 
> jq 'select(.event_type=="alert") | .alert.signature' /var/log/suricata/eve.json | sort | uniq 
atau:  
 
> jq 'select(.event_type=="alert" and (.alert.signature | test("SCAN|Nmap"; "i")))' /var/log/suricata/eve.json 


