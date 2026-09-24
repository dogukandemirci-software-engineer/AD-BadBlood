# 🛡️ Active Directory & BadBlood Cyber Security Lab Environment

Bu proje, siber güvenlik araştırmaları, sızma testleri (pentest), BloodHound analizi ve MAVİ Takım (Blue Team) tespit senaryolarını gerçekleştirmek amacıyla otomatikleştirilmiş bir **Active Directory Lab Ortamı** kurulumunu kapsar.

---

## 📐 Mimari ve Senaryo Özeti

### 🎯 Senaryo: "lab.local" Kurumsal Ağ Simülasyonu
Senaryo gereği, **`lab.local`** adında yeni bir kurumsal domain yapısı oluşturulmuştur. Gerçekçi bir kurumsal ağ yapısını simüle etmek için Active Directory yapısı boş bırakılmamış, **BadBlood** aracı kullanılarak binlerce rastgele kullanıcı, grup, bilgisayar, Access Control List (ACL) ve güvensiz yetkilendirme (Organizational Unit / Delegation) verisi ile doldurulmuştur.

```
[ Domain Controller (lab.local) ] 
       │
       ├──> Windows Server 2019 (AD DS & DNS Server)
       ├──> Kerberos / NTLM Kimlik Doğrulama
       └──> BadBlood Mock Data (500+ Kullanıcı, OU, Nesne ve Zayıf Yetkiler)
```

---

## 🛠️ Adım Adım Yapılan İşlemler

### Adım 1: Active Directory Domain Services (AD DS) Kurulumu
Görseldeki PowerShell çıktısında görüldüğü üzere, otomatikleştirilmiş betik yardımıyla `lab.local` domain forest'ı oluşturulmuştur.

* **Komut:** `Install-ADDSForest -DomainName "lab.local" ...`
* **Yapılan İşlemler:**
  1. Ortam ve girdi doğrulaması tamamlandı.
  2. Kerberos ilkeleri güvenliği sağlandı.
  3. `lab.local` domain controller rolüne yükseltildi (Promoting forest).
  4. DNS zone kayıtları ve Active Directory varsayılan veritabanı (NTDS.dit) yapılandırıldı.

---

### Adım 2: BadBlood ile Domain’i Domain Verileriyle Doldurma
AD DS kurulumu tamamlandıktan sonra, test senaryolarını gerçekçi kılmak amacıyla ortama **BadBlood** aracı entegre edilmiştir.

> **BadBlood Nedir?**
> Active Directory ortamlarında BloodHound gibi araçlarla analiz yapılabilecek karmaşık, zayıf konfigürasyonlara ve ilişkilere sahip binlerce rastgele nesne (User, Group, Computer, OU, ACL) oluşturan bir simülasyon aracıdır.

**BadBlood Tarafından Oluşturulan Nesneler:**
* **Organizational Units (OU):** Rastgele yetki hiyerarşisine sahip OU mimarisi.
* **Kullanıcılar & Gruplar:** Yüzlerce rastgele kullanıcı hesabı ve içi içe geçmiş (nested) gruplar.
* **Görünmeyen Zayıflıklar:** Rastgele dağıtılmış `GenericAll`, `WriteDACL`, `ForceChangePassword` gibi suiistimale açık ACL (Access Control List) izinleri.

---

## 🚀 Örnek Test ve Saldırı Senaryoları (Laboratuvarda Yapılabilecekler)

Bu lab ortamı tamamlandığında aşağıdaki testler gerçekleştirilebilir:

### 1. Kırmızı Takım (Red Team) Senaryoları
* **Reconnaissance & BloodHound:** `SharpHound` ile domain verilerini toplayıp Domain Admin'e giden en kısa yetki yükseltme (Privilege Escalation) yollarını bulma.
* **Kerberoasting & AS-REP Roasting:** Ortama eklenen güvensiz kullanıcı hesapları üzerinden bilet yakalama ve parola kırma saldırıları.
* **ACL Exploitation:** BadBlood tarafından rastgele atanan zayıf ACL yetkilerini kullanarak yetki yükseltme.

### 2. Mavi Takım (Blue Team) Senaryoları
* **SIEM / Sysmon Log Analizi:** Ortamda gerçekleşen şüpheli Kerberos bilet isteklerini ve yetki değişimlerini izleme.
* **AD Hardening:** BloodHound çıktısına göre tespit edilen kritik yetki yollarını ve zayıf ACL yapılandırmalarını temizleme.

---

## ⚠️ Dikkat Edilmesi Gereken Hususlar & Uyarılardan Dersler

Kurulum sırasında PowerShell üzerinde alınan uyarılar ve anlamları:

1. **Statik IP Uyarısı:**
   * *Açıklama:* Domain Controller için statik IP atanması önerilir. Dinamik IP değişirse DNS ve kimlik doğrulama hizmetleri aksayabilir.
2. **DNS Delegation Uyarısı:**
   * *Açıklama:* Üst seviye bir DNS sunucusu bulunmadığı için yetkilendirme (delegation) oluşturulamadı. Isolated lab ortamları için bu durum normaldir.
3. **Allow cryptography algorithms compatible with Windows NT 4.0:**
   * *Açıklama:* Windows Server 2019 varsayılan zayıf kripto uyumluluk uyarısıdır. Gerekirse GPO ile sıkılaştırılabilir.

---

## 📝 Sonuç

Kurulum başarıyla tamamlanmış ve `lab.local` Active Directory yapısı hem kırmızı hem mavi takım çalışmaları için hazır hale getirilmiştir.
