# AWS VPC Mimarisi Açıklaması

Bu dosya, oluşturduğum AWS VPC mimarisini açıklamaktadır.

## 1. VPC Genel Yapısı

- **VPC CIDR:** `10.10.0.0/16`
- **Availability Zones:** 3 farklı bölgeye yayılmış subnetler içeriyor.
- **Subnet Dağılımı:**
  - **Availability Zone A**
    - Public Subnet A: `10.10.10.0/24`
    - Private Subnet A: `10.10.11.0/24`
  - **Availability Zone B**
    - Public Subnet B: `10.10.20.0/24`
    - Private Subnet B: `10.10.21.0/24`
  - **Availability Zone C**
    - Public Subnet C: `10.10.30.0/24`
    - Private Subnet C: `10.10.31.0/24`

## 2. Public ve Private Subnet Ayrımı

- **Public subnetler**, internete çıkabilmesi için **Internet Gateway (IGW)** ile ilişkilendirilmiştir.
- **Private subnetler**, internete çıkabilmesi için **NAT Gateway** üzerinden yönlendirilmiştir.

## 3. Route Table ve Trafik Yönlendirme

### 3.1 Public Subnet Route Table
- `0.0.0.0/0` → **Internet Gateway (IGW)**

### 3.2 Private Subnet Route Table
- `0.0.0.0/0` → **NAT Gateway** (IGW yerine)
- `On-Premises CIDR` → **VPN Gateway** (Varsa)

## 4. Güvenlik Yapılandırmaları

### 4.1 Security Groups (SG)
- Public subnet içindeki kaynaklar için güvenlik grupları şu şekilde yapılandırılmıştır:
  - **Public Subnet İçin:**
    - HTTP (`80`), HTTPS (`443`), SSH (`22`) yalnızca belirli IP'lere açılmıştır.
  - **Private Subnet İçin:**
    - Sadece iç iletişim için izin verilmiştir.

### 4.2 Network ACL (NACL)
- Public subnetler için gelen/giden trafiğe uygun kurallar tanımlanmıştır.
- Private subnetler için dış erişim tamamen kısıtlanmıştır.

## 5. AWS Endpoint Kullanımı
- AWS servislerine erişim için **S3, DynamoDB gibi hizmetlere özel VPC Endpoint** kullanılmıştır.
- Böylece Private Subnet içindeki kaynaklar internete çıkmadan AWS hizmetlerine erişebilir.

## 6. Bağlantılar ve Güvenlik
- **Internet Gateway (IGW):** Public subnetlerin internete çıkmasını sağlar.
- **VPN Gateway (VPN-GW):** On-premises bağlantı sağlar.
- **NAT Gateway:** Private subnetler için internet erişimi sunar.
- **VPC Endpoint:** AWS hizmetlerine özel erişim sağlar.

---

Geri bildirimlerinizi beklerim!
