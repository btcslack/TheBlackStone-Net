TheBlackStone-Net Handbook
📌 İçindekiler
Giriş

Kurulum

Yapılandırma (config.json)

Çalıştırma

Modüller

5.1 Discovery (Ağ Keşif)

5.2 PortScan (Port Tarama)

5.3 WMICollect (WMI Bilgi)

5.4 SNMPDetect (SNMP Tespiti)

5.5 SolarModule (Solar Panel Verisi)

5.6 CameraDetect (Kamera Tespiti)

5.7 PurpleTeam (Purple-Team Testleri)

5.8 NetMap (Ağ Haritası)

5.9 AnomalyDetect (Anomali Algılama)

5.10 RFDetect (RF/LoRa Tespiti)

5.11 VulnScan (Çift Katmanlı Zafiyet)

5.12 AttackTree (Saldırı Ağacı)

5.13 Compliance (Regülasyon)

5.14 ThreatSim (Tehdit Simülasyonu)

5.15 IoTAnalysis (IoT/Solar Analizi)

5.16 QuantumSafe (Quantum-Safe Testleri)

5.17 Dashboard (Canlı Gösterge)

Raporlama

Örnek Kullanım Akışı

Sürüm Yönetimi & Yenilikler

Sorun Giderme

Gelecek Planları

1. Giriş
TheBlackStone-Net; oteller, kurumlar ve büyük ağ altyapıları için “Uzay Çağı” düzeyinde:

Ağ Envanteri

Donanım & Yazılım Taramaları

Güvenlik & Zafiyet Analizi

Solar, IoT, CCTV, RF Cihaz Tespiti

Purple/Blue/Red Team Senaryoları

AI-Destekli Anomali Algılama

3D & Heatmap Raporlama

PDF/HTML/Excel/CSV Export

… sağlayan modüler bir PowerShell platformudur.

2. Kurulum
İndir

bash
Kopyala
Düzenle
git clone https://github.com/btcslack/TheBlackStone-Net.git
cd TheBlackStone-Net
PowerShell 7.1+ veya PowerShell 5.1 yükleyin.

Gerekiyorsa TLS ve ExecutionPolicy

powershell
Kopyala
Düzenle
Set-ExecutionPolicy Bypass -Scope Process -Force
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
Gerekli modüller otomatik yüklenir.

3. Yapılandırma (config.json)
jsonc
Kopyala
Düzenle
{
  "ScanRanges": ["192.168.1.0/24","10.0.0.0/16"],
  "TCPPorts": [22,80,443,3389],
  "UDPPorts": [161,500],
  "SNMPCommunity": "public",
  "EnableAnomaly":       true,
  "EnableRF":            true,
  "EnableVuln":          true,
  "EnableAttackTree":    true,
  "EnableCompliance":    true,
  "EnableThreatSim":     true,
  "EnableSolarModule":   true,
  "EnableCameraDetect":  true,
  "EnablePurpleTeam":    true,
  "EnableIoT":           true,
  "EnableQuantum":       false,
  "EnableDashboard":     true,
  "Theme": "Dark",         // "Dark" veya "Light"
  "ExportPDF":  true,
  "ExportExcel": false,
  "ExportHTML": true,
  "ExportJSON": true,
  "ExportCSV":  true
}
ScanRanges: CIDR formatlı ağ blokları

Enable…: Her modülü devreye alır

Export…: Hangi formatta çıktı alınacak

4. Çalıştırma
powershell
Kopyala
Düzenle
# Yönetici olarak PowerShell 7+’de:
cd C:\TheBlackStone-Net
.\start.ps1
veya Windows’da çift tıkla start.bat.

5. Modüller
Her modül bir .psm1 dosyasında, Function + Export-ModuleMember ile tanımlıdır.

5.1 Discovery (Ağ Keşif)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\Discovery.psm1
$hosts = Invoke-Discovery -Ranges $config.ScanRanges -Threads 100
Parametreler:

-Ranges (string[])

-Threads (int)

Çıktı: IP adresi listesi

5.2 PortScan (Port Tarama)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\PortScan.psm1
$ports = Invoke-PortScan -Hosts $hosts -TCPPorts $config.TCPPorts -Threads 50
Parametreler:

-Hosts (string[])

-TCPPorts (int[])

-Threads (int)

Çıktı: PSCustomObject dizisi (IPAddress,Port,Service…)

5.3 WMICollect (WMI Bilgi)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\WMICollect.psm1
$wmiData = Get-WMICollect -Hosts $hosts -Credential $creds -Threads 10
Parametreler:

-Hosts (string[])

-Credential (PSCredential)

-Threads (int)

Çıktı: PSCustomObject detaylı sistem bilgisi

5.4 SNMPDetect (SNMP Tespiti)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\SNMPDetect.psm1
$snmpData = Get-SNMPDetect -Hosts $hosts -Community $config.SNMPCommunity
Parametreler:

-Hosts (string[])

-Community (string)

Çıktı: SNMP cihaz bilgileri

5.5 SolarModule (Solar Panel Verisi)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\SolarModule.psm1
$solarData = Get-SolarModule -Hosts $hosts
Inverter firmware, production stats

5.6 CameraDetect (Kamera Tespiti)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\CameraDetect.psm1
$camData = Get-CameraDetect -Hosts $hosts
Hikvision / Tiandy detection via default ports

5.7 PurpleTeam (Purple-Team Testleri)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\PurpleTeam.psm1
$purple = Invoke-PurpleTeam -Hosts $hosts -TCPPorts $config.TCPPorts
Default creds, SNMP brute, SMB checks

5.8 NetMap (Ağ Haritası)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\NetMap.psm1
New-NetworkMap -Hosts $hosts -Links $ports -Theme $config.Theme
SVG/WebGL 3D network map

5.9 AnomalyDetect (Anomali Algılama)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\AnomalyDetect.psm1
Invoke-AnomalyDetect -Hosts $hosts
Light AI ile baseline öğrenme, bandwidth spike uyarısı

5.10 RFDetect (RF/LoRa Tespiti)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\RFDetect.psm1
Invoke-RFDetect -Ranges $config.ScanRanges
LoRa/RF cihaz sweep

5.11 VulnScan (Çift Katmanlı Zafiyet)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\VulnScan.psm1
Invoke-VulnScan -OpenPorts $ports -Threads 20
Nmap+CVEDB match, hızlı zafiyet raporu

5.12 AttackTree (Saldırı Ağacı)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\AttackTree.psm1
Invoke-AttackTree -AllData $allData
AI tabanlı attack-tree grafik

5.13 Compliance (Regülasyon)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\Compliance.psm1
Invoke-Compliance -AllData $allData
GDPR/KVKK/HIPAA kontrolleri

5.14 ThreatSim (Tehdit Simülasyonu)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\ThreatSim.psm1
Invoke-ThreatSim -Hosts $hosts
Fake brute-force, log test

5.15 IoTAnalysis (IoT/Solar Analizi)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\IoTAnalysis.psm1
Invoke-IoTAnalysis -Hosts $hosts
IoT firmware & config audit

5.16 QuantumSafe (Quantum-Safe Testleri)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\QuantumSafe.psm1
Invoke-QuantumSafe -Hosts $hosts
RSA vs. quantum risk

5.17 Dashboard (Canlı Gösterge)
powershell
Kopyala
Düzenle
Import-Module .\src\modules\Dashboard.psm1
Invoke-Dashboard -AllData $allData -Theme $config.Theme
Live charts, heatmap, WebSocket destekli

6. Raporlama
HTML5 grafikleri (Chart.js, Three.js, WebGL 3D)

PDF ve Excel export

JSON/CSV veri saklama

Çıktılar output/YYYYMMDD_HHMMSS/ klasöründe.

7. Örnek Kullanım Akışı
powershell
Kopyala
Düzenle
# 1. Kurulum ve config.json ayarla
# 2. start.ps1 çalıştır
# 3. Konsolda:
#    Discovery → PortScan → WMICollect → … → Dashboard
# 4. Browser’da `http://localhost:8080` aç
# 5. output klasöründeki HTML/PDF raporu incele
8. Sürüm Yönetimi & Yenilikler
v1.0: Modüler iskelet / temel modüller

v1.1: AnomaliDetect, RFDetect

v1.2: VulnScan, AttackTree

v1.3: Compliance, ThreatSim

v1.4: IoTAnalysis, QuantumSafe, Dashboard

9. Sorun Giderme
Modül Bulunamadı: Import-Module .\src\modules\<Modül>.psm1

ExecutionPolicy: Set-ExecutionPolicy Bypass -Scope Process

Port Çakışmaları: config.json’de port listelerini güncelleyin

10. Gelecek Planları
ML-tabanlı Anomali Analizinin derinleşmesi

Zero-Trust Simülasyon eklentisi

ChatGPT API ile dinamik rapor Q&A

