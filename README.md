# MySQL Temel ve İleri Seviye Konular Üzerine Detaylı Bir Kılavuz

## Giriş
### MySQL Nedir?
MySQL, açık kaynak kodlu bir ilişkisel veritabanı yönetim sistemidir (RDBMS). Sunucu tabanlı ve çok kullanıcılı bir yapıya sahiptir. MySQL, özellikle web tabanlı uygulamalarda yaygın bir şekilde kullanılır ve yüksek performans ile güvenilirlik sunar.

### Kullanım Alanları
- Web uygulamaları
- Veri analizi
- E-ticaret platformları
- İçerik yönetim sistemleri (CMS)

### Avantajları
- Açık kaynak kodlu ve ücretsizdir.
- Yüksek performans ve ölçeklenebilirlik sağlar.
- Geniş topluluk ve destek ağı mevcuttur.
- Çeşitli programlama dilleri ile uyumludur.

## Temel SQL Komutları
### Veritabanı ve Tablo Oluşturma

```sql
-- Veritabanı oluşturma
CREATE DATABASE universite;

-- Veritabanını kullanma
USE universite;

-- Tablo oluşturma
CREATE TABLE ogrenciler (
    id INT AUTO_INCREMENT PRIMARY KEY,
    isim VARCHAR(50),
    soyisim VARCHAR(50),
    dogum_tarihi DATE
);
```

### Veri Ekleme

```sql
-- Veri ekleme
INSERT INTO ogrenciler (isim, soyisim, dogum_tarihi) VALUES ('Ali', 'Veli', '2000-01-01');
```

### Veri Güncelleme

```sql
-- Veri güncelleme
UPDATE ogrenciler SET soyisim = 'Yılmaz' WHERE id = 1;
```

### Veri Silme

```sql
-- Veri silme
DELETE FROM ogrenciler WHERE id = 1;
```

### Veri Sorgulama

```sql
-- Veri sorgulama
SELECT * FROM ogrenciler;
```

## Karar Yapıları (Control Statements)
### IF-THEN-ELSE ve CASE İfadeleri

```sql
-- IF-THEN-ELSE kullanımı
DELIMITER //
CREATE PROCEDURE ogrenciKontrol(IN ogrenci_id INT)
BEGIN
    IF (SELECT COUNT(*) FROM ogrenciler WHERE id = ogrenci_id) > 0 THEN
        SELECT 'Öğrenci mevcut.' AS mesaj;
    ELSE
        SELECT 'Öğrenci bulunamadı.' AS mesaj;
    END IF;
END //
DELIMITER ;

-- CASE kullanımı
SELECT isim, soyisim,
    CASE
        WHEN dogum_tarihi < '2000-01-01' THEN '2000 öncesi'
        ELSE '2000 sonrası'
    END AS donem
FROM ogrenciler;
```

### Gerçek Dünya Senaryoları
Öğrencilerin mezuniyet durumlarını belirlemek için IF-THEN-ELSE ve CASE ifadeleri kullanılabilir. Örneğin, bir öğrencinin mezun olup olmadığını kontrol etmek için aşağıdaki prosedür yazılabilir:

```sql
DELIMITER //
CREATE PROCEDURE mezuniyetKontrol(IN ogrenci_id INT)
BEGIN
    DECLARE kredi INT;
    SET kredi = (SELECT toplam_kredi FROM ogrenciler WHERE id = ogrenci_id);

    IF kredi >= 120 THEN
        SELECT 'Öğrenci mezun olmuştur.' AS mesaj;
    ELSE
        SELECT 'Öğrenci mezun olamamıştır.' AS mesaj;
    END IF;
END //
DELIMITER ;
```

## Döngü Yapıları (Loops)
### WHILE, REPEAT ve LOOP Kullanımı

```sql
-- WHILE döngüsü
DELIMITER //
CREATE PROCEDURE ogrenciListele()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE ogrenci_id INT DEFAULT 1;

    WHILE done = 0 DO
        SELECT isim, soyisim FROM ogrenciler WHERE id = ogrenci_id;
        SET ogrenci_id = ogrenci_id + 1;

        IF ogrenci_id > (SELECT COUNT(*) FROM ogrenciler) THEN
            SET done = 1;
        END IF;
    END WHILE;
END //
DELIMITER ;

-- REPEAT döngüsü
DELIMITER //
CREATE PROCEDURE ogrenciYazdir()
BEGIN
    DECLARE ogrenci_id INT DEFAULT 1;
    DECLARE toplam INT;

    SET toplam = (SELECT COUNT(*) FROM ogrenciler);

    REPEAT
        SELECT isim, soyisim FROM ogrenciler WHERE id = ogrenci_id;
        SET ogrenci_id = ogrenci_id + 1;
    UNTIL ogrenci_id > toplam
    END REPEAT;
END //
DELIMITER ;

-- LOOP döngüsü
DELIMITER //
CREATE PROCEDURE ogrenciDolas()
BEGIN
    DECLARE ogrenci_id INT DEFAULT 1;

    dolas: LOOP
        IF ogrenci_id > (SELECT COUNT(*) FROM ogrenciler) THEN
            LEAVE dolas;
        END IF;

        SELECT isim, soyisim FROM ogrenciler WHERE id = ogrenci_id;
        SET ogrenci_id = ogrenci_id + 1;
    END LOOP dolas;
END //
DELIMITER ;
```

### Pratik Uygulamalar ve Performans Değerlendirmesi
Döngü yapıları, özellikle büyük veri setlerinde iteratif işlemler yapmak için kullanışlıdır. Ancak, performans açısından dikkatli olunmalıdır. Döngüler, büyük veri setlerinde uzun süre çalışabilir ve sunucu kaynaklarını tüketebilir. Bu nedenle, döngü içindeki işlemler optimize edilmeli ve gerektiğinde indeksler kullanılmalıdır.

## Tetikleyiciler (Triggers)
### INSERT, UPDATE ve DELETE İşlemleri İçin Trigger Yazımı

```sql
-- INSERT trigger
DELIMITER //
CREATE TRIGGER ogrenciEklemeTrig AFTER INSERT ON ogrenciler
FOR EACH ROW
BEGIN
    INSERT INTO log (islem, tarih) VALUES ('Yeni öğrenci eklendi', NOW());
END //
DELIMITER ;

-- UPDATE trigger
DELIMITER //
CREATE TRIGGER ogrenciGuncellemeTrig AFTER UPDATE ON ogrenciler
FOR EACH ROW
BEGIN
    INSERT INTO log (islem, tarih) VALUES ('Öğrenci bilgileri güncellendi', NOW());
END //
DELIMITER ;

-- DELETE trigger
DELIMITER //
CREATE TRIGGER ogrenciSilmeTrig AFTER DELETE ON ogrenciler
FOR EACH ROW
BEGIN
    INSERT INTO log (islem, tarih) VALUES ('Öğrenci silindi', NOW());
END //
DELIMITER ;
```

### İş Akışı Otomasyonu İçin Örnekler
Tetikleyiciler, veritabanı işlemlerini otomatik olarak izlemek ve belirli olaylar gerçekleştiğinde belirli eylemleri tetiklemek için kullanılır. Örneğin, bir öğrenci kaydedildiğinde otomatik olarak bir log kaydı oluşturulabilir. Bu, veri bütünlüğünü korumak ve iş akışlarını otomatikleştirmek için ideal bir yöntemdir.

## Stored Procedures ve Functions
### Parametreli Stored Procedure Yazımı

```sql
DELIMITER //
CREATE PROCEDURE ogrenciEkle(IN isim VARCHAR(50), IN soyisim VARCHAR(50), IN dogum_tarihi DATE)
BEGIN
    INSERT INTO ogrenciler (isim, soyisim, dogum_tarihi) VALUES (isim, soyisim, dogum_tarihi);
END //
DELIMITER ;
```

### Kullanıcı Tanımlı Fonksiyonlar İle Hesaplamalar

```sql
DELIMITER //
CREATE FUNCTION yasHesapla(dogum_tarihi DATE) RETURNS INT
BEGIN
    DECLARE yas INT;
    SET yas = TIMESTAMPDIFF(YEAR, dogum_tarihi, CURDATE());
    RETURN yas;
END //
DELIMITER ;
```

## Cursors Kullanımı
### Satır Bazlı Veri İşleme

```sql
DELIMITER //
CREATE PROCEDURE ogrenciBilgileri()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE ogrenci_isim VARCHAR(50);
    DECLARE ogrenci_soyisim VARCHAR(50);
    DECLARE cur CURSOR FOR SELECT isim, soyisim FROM ogrenciler;

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN cur;

    ogrenci_loop: LOOP
        FETCH cur INTO ogrenci_isim, ogrenci_soyisim;
        IF done = 1 THEN
            LEAVE ogrenci_loop;
        END IF;
        SELECT ogrenci_isim, ogrenci_soyisim;
    END LOOP ogrenci_loop;

    CLOSE cur;
END //
DELIMITER ;
```

### Cursors İle Yapılan İşlemlerin Optimizasyonu
Cursors, büyük veri setlerinde satır bazlı işlemler yapmak için kullanışlıdır. Ancak, performans açısından dikkat edilmesi gereken bazı noktalar vardır. Cursors ile yapılan işlemler, bellek tüketimini artırabilir ve işlem süresini uzatabilir. Bu nedenle, mümkün olduğunca SQL sorgularını optimize etmek ve gerekli durumlarda indeksler kullanmak önemlidir.

## Kod Açıklamaları ve Uygulama Senaryoları
### Her SQL Komutu İçin Açıklamalar
SQL komutlarının her biri için ayrıntılı açıklamalar ve örnekler sunulacaktır. Bu sayede, öğrenciler ve geliştiriciler, her komutun nasıl çalıştığını ve hangi senaryolarda kullanılabileceğini anlayabileceklerdir.

### Gerçek Hayatta Karşılaşılan Problemleri Çözecek Pratik Örnekler
Öğrencilerin ve geliştiricilerin karşılaşabileceği gerçek dünya senaryolarına yönelik pratik örnekler sunulacaktır. Bu örnekler, SQL komutlarının ve yapıların nasıl uygulanabileceğini gösterecektir.

## Veritabanı Tasarımında Performans ve Güvenlik
Veritabanı tasarımında performans ve güvenlik konuları da göz önünde bulundurulmalıdır. İyi tasarlanmış bir veritabanı, veri işleme sürelerini kısaltır ve sunucu kaynaklarını verimli kullanır. Ayrıca, güvenlik önlemleri alınarak veri bütünlüğü ve gizliliği sağlanmalıdır.

### Performans
- İndekslerin kullanımı
- Normalizasyon ve denormalizasyon
- Sorgu optimizasyonu

### Güvenlik
- Kullanıcı yetkilendirme
- Veri şifreleme
- Güvenli veri erişimi

Bu kılavuz, MySQL konusunda kapsamlı bir bilgi sunmayı amaçlamaktadır. Hem temel hem de ileri seviye konulara yer verilerek, yazılım mühendisliği öğrencileri ve MySQL öğrenmek isteyen geliştiriciler için faydalı bir kaynak oluşturulmuştur.
