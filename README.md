Bu repo, üniversite seviyesinde yazılım mühendisliği öğrencileri için kapsamlı bir MySQL kılavuzu içermektedir. Temel kavramlardan ileri seviye konulara kadar her düzeyde bilgi içeren bu kılavuz, MySQL veritabanı yönetim sistemini derinlemesine anlamanıza yardımcı olacaktır.

## İçindekiler

1. [Giriş](#giriş)
2. [Temel SQL Komutları](#temel-sql-komutları)
3. [Karar Yapıları (Control Statements)](#karar-yapıları-control-statements)
4. [Döngü Yapıları (Loops)](#döngü-yapıları-loops)
5. [Tetikleyiciler (Triggers)](#tetikleyiciler-triggers)
6. [Stored Procedures ve Functions](#stored-procedures-ve-functions)
7. [Cursors Kullanımı](#cursors-kullanımı)
8. [Performans ve Güvenlik](#performans-ve-güvenlik)

## Giriş

MySQL, dünyanın en popüler açık kaynaklı ilişkisel veritabanı yönetim sistemidir. Oracle Corporation tarafından geliştirilen ve desteklenen MySQL, web uygulamalarından kurumsal yazılımlara kadar geniş bir yelpazede kullanılmaktadır.

### MySQL'in Tanımı

MySQL, yapılandırılmış verilerinizi depolamak, yönetmek ve sorgulamak için kullanılan güçlü bir ilişkisel veritabanı yönetim sistemidir. "My" kısmı geliştiricisinin kızının adından gelirken, "SQL" ise Structured Query Language (Yapılandırılmış Sorgu Dili) ifadesinin kısaltmasıdır.

### Kullanım Alanları

MySQL, çeşitli alanlarda yaygın olarak kullanılmaktadır:

- **Web Uygulamaları**: WordPress, Joomla, Drupal gibi içerik yönetim sistemleri
- **E-ticaret Platformları**: Ürün katalogları, kullanıcı verileri, sipariş takibi
- **Mobil Uygulamalar**: Kullanıcı verileri ve içerik depolama
- **Kurumsal Uygulamalar**: İş süreçleri, raporlama ve analiz
- **Veri Analizi ve Raporlama**: İstatistiksel veri depolama ve analiz işlemleri

### Avantajları

MySQL'in başlıca avantajları şunlardır:

1. **Açık Kaynak Kodlu**: Ücretsiz olarak kullanılabilir ve geniş bir geliştirici topluluğu tarafından desteklenir.
2. **Hız ve Performans**: Optimizasyon özellikleri sayesinde yüksek hız ve performans sunar.
3. **Güvenilirlik**: Veri güvenliği ve tutarlılığı konusunda güçlü mekanizmalar içerir.
4. **Ölçeklenebilirlik**: Küçük projelerden büyük kurumsal sistemlere kadar her ölçekte kullanılabilir.
5. **Çapraz Platform Desteği**: Windows, Linux, macOS gibi çeşitli işletim sistemlerinde çalışabilir.
6. **Geniş Dil Desteği**: PHP, Python, Java, C#, Node.js gibi birçok programlama dili ile entegre çalışabilir.

```sql name=mysql_surum_kontrol.sql
-- MySQL sürüm bilgisini öğrenmek için
SELECT VERSION();

-- Mevcut veritabanlarını listelemek için
SHOW DATABASES;
```

## Temel SQL Komutları

Bu bölümde, MySQL'de sıkça kullanılan temel SQL komutlarını inceleyeceğiz.

### Veritabanı İşlemleri

#### Veritabanı Oluşturma

```sql name=veritabani_islemleri.sql
-- Veritabanı oluşturma
CREATE DATABASE universite;

-- Belirlenen karakter seti ile veritabanı oluşturma
CREATE DATABASE e_ticaret CHARACTER SET utf8mb4 COLLATE utf8mb4_turkish_ci;

-- Veritabanı kullanımı
USE universite;
```

#### Veritabanı Silme

```sql name=veritabani_silme.sql
-- Veritabanı silme
DROP DATABASE IF EXISTS test_db;
```

### Tablo İşlemleri

#### Tablo Oluşturma

```sql name=tablo_olusturma.sql
-- Öğrenci tablosu oluşturma
CREATE TABLE ogrenci (
    ogrenci_id INT AUTO_INCREMENT PRIMARY KEY,
    ad VARCHAR(50) NOT NULL,
    soyad VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    dogum_tarihi DATE,
    kayit_tarihi TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    aktif BOOLEAN DEFAULT TRUE
);

-- Ders tablosu oluşturma
CREATE TABLE ders (
    ders_id INT AUTO_INCREMENT PRIMARY KEY,
    ders_adi VARCHAR(100) NOT NULL,
    kredi INT DEFAULT 3,
    aciklama TEXT
);

-- İlişkili tablo oluşturma (öğrenci-ders kayıtları)
CREATE TABLE kayit (
    kayit_id INT AUTO_INCREMENT PRIMARY KEY,
    ogrenci_id INT,
    ders_id INT,
    donem VARCHAR(20),
    not_ortalamasi DECIMAL(3,2),
    FOREIGN KEY (ogrenci_id) REFERENCES ogrenci(ogrenci_id),
    FOREIGN KEY (ders_id) REFERENCES ders(ders_id)
);
```

#### Tablo Yapısını Değiştirme

```sql name=tablo_yapisi_degistirme.sql
-- Tabloya yeni kolon ekleme
ALTER TABLE ogrenci ADD bolum_id INT;

-- Kolonu değiştirme
ALTER TABLE ogrenci MODIFY email VARCHAR(150);

-- Kolon adını değiştirme
ALTER TABLE ders CHANGE ders_adi kurs_adi VARCHAR(100);

-- Kolon silme
ALTER TABLE ogrenci DROP COLUMN aktif;
```

#### Tablo Silme

```sql name=tablo_silme.sql
-- Tablo silme
DROP TABLE IF EXISTS kayit;
```

### Veri İşlemleri

#### Veri Ekleme (INSERT)

```sql name=veri_ekleme.sql
-- Tek satır veri ekleme
INSERT INTO ogrenci (ad, soyad, email, dogum_tarihi)
VALUES ('Ahmet', 'Yılmaz', 'ahmet.yilmaz@example.com', '2000-05-15');

-- Çoklu satır veri ekleme
INSERT INTO ders (ders_adi, kredi, aciklama)
VALUES 
    ('Veritabanı Yönetimi', 4, 'Veritabanı tasarımı ve SQL dili'),
    ('Programlama Temelleri', 3, 'Algoritma ve temel programlama'),
    ('Web Programlama', 3, 'HTML, CSS ve JavaScript');
```

#### Veri Güncelleme (UPDATE)

```sql name=veri_guncelleme.sql
-- Tek bir kaydı güncelleme
UPDATE ogrenci SET email = 'ahmet.yilmaz.new@example.com' WHERE ogrenci_id = 1;

-- Birden fazla alanı güncelleme
UPDATE ders SET kredi = 5, aciklama = 'Gelişmiş veritabanı yönetimi ve SQL' 
WHERE ders_adi = 'Veritabanı Yönetimi';

-- Koşullu güncelleme
UPDATE kayit SET not_ortalamasi = not_ortalamasi + 0.5
WHERE not_ortalamasi < 2.0 AND donem = '2024-Bahar';
```

#### Veri Silme (DELETE)

```sql name=veri_silme.sql
-- Belirli kayıtları silme
DELETE FROM ogrenci WHERE dogum_tarihi < '1990-01-01';

-- Tüm verileri silme
DELETE FROM kayit;

-- Tablo içeriğini hızlı silme (TRUNCATE)
TRUNCATE TABLE ders;
```

### Veri Sorgulama (SELECT)

#### Temel Sorgular

```sql name=temel_sorgular.sql
-- Tüm sütunları seçme
SELECT * FROM ogrenci;

-- Belirli sütunları seçme
SELECT ad, soyad, email FROM ogrenci;

-- Koşullu sorgulama
SELECT * FROM ogrenci WHERE dogum_tarihi > '2000-01-01';

-- Sıralama işlemleri
SELECT * FROM ogrenci ORDER BY soyad ASC, ad DESC;

-- Limit ve Offset kullanımı
SELECT * FROM ogrenci ORDER BY kayit_tarihi DESC LIMIT 10 OFFSET 20;
```

#### İlişkisel Sorgular (JOIN)

```sql name=iliskisel_sorgular.sql
-- INNER JOIN: Her iki tabloda da eşleşen kayıtları getirir
SELECT o.ad, o.soyad, d.ders_adi, k.not_ortalamasi
FROM ogrenci o
INNER JOIN kayit k ON o.ogrenci_id = k.ogrenci_id
INNER JOIN ders d ON k.ders_id = d.ders_id
WHERE k.donem = '2024-Bahar';

-- LEFT JOIN: Sol tablodaki tüm kayıtları ve sağ tablodaki eşleşen kayıtları getirir
SELECT o.ad, o.soyad, COUNT(k.kayit_id) as ders_sayisi
FROM ogrenci o
LEFT JOIN kayit k ON o.ogrenci_id = k.ogrenci_id
GROUP BY o.ogrenci_id;

-- RIGHT JOIN: Sağ tablodaki tüm kayıtları ve sol tablodaki eşleşen kayıtları getirir
SELECT d.ders_adi, COUNT(k.ogrenci_id) as ogrenci_sayisi
FROM kayit k
RIGHT JOIN ders d ON k.ders_id = d.ders_id
GROUP BY d.ders_id;
```

#### Gruplama ve Hesaplama

```sql name=gruplama_ve_hesaplama.sql
-- Gruplama işlemleri
SELECT donem, COUNT(*) as kayit_sayisi
FROM kayit
GROUP BY donem;

-- Hesaplama fonksiyonları
SELECT 
    AVG(not_ortalamasi) as ortalama_not,
    MAX(not_ortalamasi) as en_yuksek_not,
    MIN(not_ortalamasi) as en_dusuk_not,
    COUNT(*) as ogrenci_sayisi
FROM kayit
WHERE ders_id = 1;

-- HAVING kullanımı (gruplandırma sonuçlarını filtreleme)
SELECT ders_id, AVG(not_ortalamasi) as ortalama_not
FROM kayit
GROUP BY ders_id
HAVING ortalama_not > 2.5;
```

## Karar Yapıları (Control Statements)

MySQL'de koşullu işlemler yapmak için çeşitli karar yapıları kullanılabilir. Bu yapılar, stored procedure, function, trigger gibi programlanabilir bileşenlerde kullanılır.

### IF-THEN-ELSE İfadeleri

MySQL'de IF-THEN-ELSE yapısı, belirli koşullara göre farklı işlemler yapmayı sağlar.

```sql name=if_then_else.sql
DELIMITER //

CREATE PROCEDURE HarfNotuHesapla(IN puan DECIMAL(5,2), OUT harf_notu VARCHAR(2))
BEGIN
    IF puan >= 90 THEN
        SET harf_notu = 'AA';
    ELSEIF puan >= 85 THEN
        SET harf_notu = 'BA';
    ELSEIF puan >= 80 THEN
        SET harf_notu = 'BB';
    ELSEIF puan >= 75 THEN
        SET harf_notu = 'CB';
    ELSEIF puan >= 70 THEN
        SET harf_notu = 'CC';
    ELSEIF puan >= 65 THEN
        SET harf_notu = 'DC';
    ELSEIF puan >= 60 THEN
        SET harf_notu = 'DD';
    ELSEIF puan >= 50 THEN
        SET harf_notu = 'FD';
    ELSE
        SET harf_notu = 'FF';
    END IF;
END//

DELIMITER ;

-- Kullanım örneği
SET @sonuc = '';
CALL HarfNotuHesapla(87.5, @sonuc);
SELECT @sonuc AS 'Harf Notu';
```

### CASE İfadeleri

CASE ifadesi, SQL sorgularında veya programlanabilir nesnelerde koşullu mantık uygulamak için kullanılır.

```sql name=case_ifadeleri.sql
-- Sorgu içinde CASE kullanımı
SELECT 
    ogrenci_id,
    ad,
    soyad,
    CASE 
        WHEN dogum_tarihi >= '2000-01-01' THEN 'Z Kuşağı'
        WHEN dogum_tarihi >= '1980-01-01' THEN 'Y Kuşağı'
        WHEN dogum_tarihi >= '1965-01-01' THEN 'X Kuşağı'
        ELSE 'Baby Boomer'
    END AS kusak
FROM ogrenci;

-- Stored Procedure içinde CASE kullanımı
DELIMITER //

CREATE PROCEDURE OgrenciDurumunuGuncelle(IN p_ogrenci_id INT)
BEGIN
    DECLARE v_ortalama DECIMAL(3,2);
    
    -- Öğrencinin not ortalamasını al
    SELECT AVG(not_ortalamasi) INTO v_ortalama 
    FROM kayit 
    WHERE ogrenci_id = p_ogrenci_id;
    
    -- CASE ile durum güncelleme
    CASE 
        WHEN v_ortalama >= 3.50 THEN
            UPDATE ogrenci SET durum = 'Yüksek Onur' WHERE ogrenci_id = p_ogrenci_id;
        WHEN v_ortalama >= 3.00 THEN
            UPDATE ogrenci SET durum = 'Onur' WHERE ogrenci_id = p_ogrenci_id;
        WHEN v_ortalama >= 2.00 THEN
            UPDATE ogrenci SET durum = 'Normal' WHERE ogrenci_id = p_ogrenci_id;
        ELSE
            UPDATE ogrenci SET durum = 'Probation' WHERE ogrenci_id = p_ogrenci_id;
    END CASE;
END//

DELIMITER ;
```

### Gerçek Dünya Senaryosu: Sipariş Durumu Kontrol Sistemi

Aşağıdaki örnek, bir e-ticaret sistemi için sipariş durumunu kontrol eden ve gerekli işlemleri yapan bir stored procedure'ü göstermektedir.

```sql name=siparis_durumu_kontrolu.sql
DELIMITER //

CREATE PROCEDURE SiparisDurumuGuncelle(IN p_siparis_id INT, IN p_yeni_durum VARCHAR(50))
BEGIN
    DECLARE v_eski_durum VARCHAR(50);
    DECLARE v_musteri_id INT;
    DECLARE v_siparis_tutari DECIMAL(10,2);
    
    -- Sipariş bilgilerini al
    SELECT durum, musteri_id, toplam_tutar INTO v_eski_durum, v_musteri_id, v_siparis_tutari
    FROM siparisler
    WHERE siparis_id = p_siparis_id;
    
    -- Durum güncellemesi
    IF p_yeni_durum = v_eski_durum THEN
        -- Zaten aynı durumda, güncellemeye gerek yok
        SELECT CONCAT('Sipariş zaten ', v_eski_durum, ' durumunda.') AS mesaj;
    ELSE
        -- Durum geçişlerini kontrol et
        CASE p_yeni_durum
            WHEN 'Onaylandı' THEN
                IF v_eski_durum = 'Beklemede' THEN
                    UPDATE siparisler SET durum = p_yeni_durum, onay_tarihi = NOW() WHERE siparis_id = p_siparis_id;
                    -- Stok kontrolü yap
                    CALL StokKontrolEt(p_siparis_id);
                    SELECT 'Sipariş onaylandı ve stok kontrolü yapıldı.' AS mesaj;
                ELSE
                    SELECT 'Geçersiz durum geçişi! Sadece beklemedeki siparişler onaylanabilir.' AS mesaj;
                END IF;
                
            WHEN 'Kargoya Verildi' THEN
                IF v_eski_durum = 'Onaylandı' THEN
                    UPDATE siparisler SET durum = p_yeni_durum, kargo_tarihi = NOW() WHERE siparis_id = p_siparis_id;
                    -- Kargo takip numarası oluştur
                    CALL KargoTakipNoOlustur(p_siparis_id);
                    SELECT 'Sipariş kargoya verildi ve takip numarası oluşturuldu.' AS mesaj;
                ELSE
                    SELECT 'Geçersiz durum geçişi! Sadece onaylanmış siparişler kargoya verilebilir.' AS mesaj;
                END IF;
                
            WHEN 'Tamamlandı' THEN
                IF v_eski_durum = 'Kargoya Verildi' THEN
                    UPDATE siparisler SET durum = p_yeni_durum, tamamlanma_tarihi = NOW() WHERE siparis_id = p_siparis_id;
                    -- Müşteri puanını güncelle
                    IF v_siparis_tutari > 1000 THEN
                        UPDATE musteriler SET puan = puan + 100 WHERE musteri_id = v_musteri_id;
                    ELSEIF v_siparis_tutari > 500 THEN
                        UPDATE musteriler SET puan = puan + 50 WHERE musteri_id = v_musteri_id;
                    ELSE
                        UPDATE musteriler SET puan = puan + 10 WHERE musteri_id = v_musteri_id;
                    END IF;
                    SELECT 'Sipariş tamamlandı ve müşteri puanları güncellendi.' AS mesaj;
                ELSE
                    SELECT 'Geçersiz durum geçişi! Sadece kargoya verilen siparişler tamamlanabilir.' AS mesaj;
                END IF;
                
            WHEN 'İptal Edildi' THEN
                IF v_eski_durum IN ('Beklemede', 'Onaylandı') THEN
                    UPDATE siparisler SET durum = p_yeni_durum, iptal_tarihi = NOW() WHERE siparis_id = p_siparis_id;
                    -- Stok iadesi yap
                    IF v_eski_durum = 'Onaylandı' THEN
                        CALL StokIadesiYap(p_siparis_id);
                    END IF;
                    SELECT 'Sipariş iptal edildi.' AS mesaj;
                ELSE
                    SELECT 'Geçersiz durum geçişi! Sadece beklemede veya onaylanmış siparişler iptal edilebilir.' AS mesaj;
                END IF;
                
            ELSE
                SELECT 'Geçersiz sipariş durumu! Kabul edilen durumlar: Onaylandı, Kargoya Verildi, Tamamlandı, İptal Edildi' AS mesaj;
        END CASE;
    END IF;
END//

DELIMITER ;

-- Kullanım örneği
CALL SiparisDurumuGuncelle(1001, 'Onaylandı');
```

## Döngü Yapıları (Loops)

MySQL'de stored procedure ve function içinde kullanabileceğiniz üç temel döngü yapısı vardır: WHILE, REPEAT ve LOOP. Bu yapılar, tekrarlayan işlemleri gerçekleştirmek için kullanılır.

### WHILE Döngüsü

WHILE döngüsü, belirtilen koşul doğru olduğu sürece içindeki işlemleri tekrarlar.

```sql name=while_dongusu.sql
DELIMITER //

CREATE PROCEDURE FaktoriyelHesapla(IN n INT, OUT sonuc BIGINT)
BEGIN
    DECLARE i INT DEFAULT 1;
    SET sonuc = 1;
    
    WHILE i <= n DO
        SET sonuc = sonuc * i;
        SET i = i + 1;
    END WHILE;
END//

DELIMITER ;

-- Kullanım örneği
SET @faktoriyel = 0;
CALL FaktoriyelHesapla(5, @faktoriyel);
SELECT @faktoriyel AS '5 Faktöriyel';
```

### REPEAT Döngüsü

REPEAT döngüsü, belirtilen koşul sağlanana kadar içindeki işlemleri en az bir kez gerçekleştirir.

```sql name=repeat_dongusu.sql
DELIMITER //

CREATE PROCEDURE FibonacciDizisiOlustur(IN n INT)
BEGIN
    DECLARE i INT DEFAULT 2;
    DECLARE fib1 INT DEFAULT 0;
    DECLARE fib2 INT DEFAULT 1;
    DECLARE fib_next INT;
    
    CREATE TEMPORARY TABLE IF NOT EXISTS fibonacci_temp (
        sira INT PRIMARY KEY,
        deger BIGINT
    );
    
    -- İlk iki Fibonacci sayısını ekle
    INSERT INTO fibonacci_temp VALUES (1, fib1);
    INSERT INTO fibonacci_temp VALUES (2, fib2);
    
    -- Diğer Fibonacci sayılarını hesapla
    REPEAT
        SET fib_next = fib1 + fib2;
        INSERT INTO fibonacci_temp VALUES (i+1, fib_next);
        
        SET fib1 = fib2;
        SET fib2 = fib_next;
        SET i = i + 1;
    UNTIL i >= n END REPEAT;
    
    -- Sonuçları göster
    SELECT * FROM fibonacci_temp;
    
    -- Geçici tabloyu temizle
    DROP TEMPORARY TABLE fibonacci_temp;
END//

DELIMITER ;

-- Kullanım örneği
CALL FibonacciDizisiOlustur(10);
```

### LOOP Döngüsü

LOOP döngüsü, içinde LEAVE komutu kullanılana kadar sürekli olarak çalışır. Bu, sonsuz döngü oluşturur ve genellikle bir koşullu LEAVE ifadesi ile kontrol edilir.

```sql name=loop_dongusu.sql
DELIMITER //

CREATE PROCEDURE AsalSayilariListele(IN baslangic INT, IN bitis INT)
BEGIN
    DECLARE sayi INT DEFAULT baslangic;
    DECLARE asal_mi BOOLEAN;
    DECLARE bolen INT;
    
    CREATE TEMPORARY TABLE IF NOT EXISTS asal_sayilar (
        sayi INT PRIMARY KEY
    );
    
    etiket: LOOP
        IF sayi > bitis THEN
            LEAVE etiket;
        END IF;
        
        -- 1 ve 2 için özel durum kontrolü
        IF sayi = 1 THEN
            SET sayi = sayi + 1;
            ITERATE etiket;
        ELSEIF sayi = 2 THEN
            INSERT INTO asal_sayilar VALUES (sayi);
            SET sayi = sayi + 1;
            ITERATE etiket;
        END IF;
        
        -- Çift sayıları atla (2 hariç)
        IF sayi % 2 = 0 THEN
            SET sayi = sayi + 1;
            ITERATE etiket;
        END IF;
        
        -- Asal sayı kontrolü
        SET asal_mi = TRUE;
        SET bolen = 3;
        
        ic_loop: LOOP
            IF bolen * bolen > sayi THEN
                LEAVE ic_loop;
            END IF;
            
            IF sayi % bolen = 0 THEN
                SET asal_mi = FALSE;
                LEAVE ic_loop;
            END IF;
            
            SET bolen = bolen + 2;
        END LOOP ic_loop;
        
        IF asal_mi THEN
            INSERT INTO asal_sayilar VALUES (sayi);
        END IF;
        
        SET sayi = sayi + 1;
    END LOOP etiket;
    
    -- Sonuçları göster
    SELECT * FROM asal_sayilar ORDER BY sayi;
    
    -- Geçici tabloyu temizle
    DROP TEMPORARY TABLE asal_sayilar;
END//

DELIMITER ;

-- Kullanım örneği
CALL AsalSayilariListele(1, 100);
```

### Pratik Uygulama: Toplu Veri İşleme

Aşağıdaki örnek, bir veritabanındaki büyük miktarda veriyi parti parti (batch) işleyen bir stored procedure'ü göstermektedir. Bu tür işlemler, performans optimizasyonu için önemlidir.

```sql name=toplu_veri_isleme.sql
DELIMITER //

CREATE PROCEDURE EskiVerileriArsivle(
    IN p_tablo_adi VARCHAR(100),
    IN p_tarih_kolonu VARCHAR(100),
    IN p_limit_tarihi DATE,
    IN p_parti_boyutu INT
)
BEGIN
    DECLARE v_bitti BOOLEAN DEFAULT FALSE;
    DECLARE v_toplam_kayit INT DEFAULT 0;
    DECLARE v_islem_sayisi INT DEFAULT 0;
    DECLARE v_baslangic_zamani DATETIME;
    DECLARE v_hedef_tablo VARCHAR(100);
    
    SET v_baslangic_zamani = NOW();
    SET v_hedef_tablo = CONCAT(p_tablo_adi, '_arsiv');
    
    -- Hedef arsiv tablosu kontrolü
    SET @sql = CONCAT('CREATE TABLE IF NOT EXISTS ', v_hedef_tablo, ' LIKE ', p_tablo_adi);
    PREPARE stmt FROM @sql;
    EXECUTE stmt;
    DEALLOCATE PREPARE stmt;
    
    -- Toplam işlenecek kayıt sayısını kontrol et
    SET @count_sql = CONCAT('SELECT COUNT(*) INTO @kayit_sayisi FROM ', p_tablo_adi, 
                           ' WHERE ', p_tarih_kolonu, ' < ''', p_limit_tarihi, '''');
    PREPARE stmt FROM @count_sql;
    EXECUTE stmt;
    DEALLOCATE PREPARE stmt;
    
    SELECT @kayit_sayisi INTO v_toplam_kayit;
    
    IF v_toplam_kayit = 0 THEN
        SELECT 'Arşivlenecek kayıt bulunamadı.' AS mesaj;
        LEAVE main_proc;
    END IF;
    
    SELECT CONCAT('Toplam ', v_toplam_kayit, ' kayıt arşivlenecek.') AS mesaj;
    
    main_proc: BEGIN
        parti_loop: LOOP
            -- Parti boyutunda veri taşı
            SET @insert_sql = CONCAT('INSERT INTO ', v_hedef_tablo, 
                                    ' SELECT * FROM ', p_tablo_adi, 
                                    ' WHERE ', p_tarih_kolonu, ' < ''', p_limit_tarihi, '''',
                                    ' LIMIT ', p_parti_boyutu);
            PREPARE stmt FROM @insert_sql;
            EXECUTE stmt;
            DEALLOCATE PREPARE stmt;
            
            SET @affected_rows = ROW_COUNT();
            SET v_islem_sayisi = v_islem_sayisi + @affected_rows;
            
            -- İşlenen kayıtları sil
            IF @affected_rows > 0 THEN
                SET @delete_sql = CONCAT('DELETE FROM ', p_tablo_adi, 
                                        ' WHERE ', p_tarih_kolonu, ' < ''', p_limit_tarihi, '''',
                                        ' LIMIT ', p_parti_boyutu);
                PREPARE stmt FROM @delete_sql;
                EXECUTE stmt;
                DEALLOCATE PREPARE stmt;
                
                SELECT CONCAT('İşlenen: ', v_islem_sayisi, '/', v_toplam_kayit, 
                             ' (', ROUND(v_islem_sayisi/v_toplam_kayit*100, 2), '%)') AS ilerleme;
                
                -- Veritabanına işlemleri commit etme şansı vermek için kısa bekleme
                DO SLEEP(0.1);
            ELSE
                SET v_bitti = TRUE;
            END IF;
            
            IF v_bitti OR v_islem_sayisi >= v_toplam_kayit THEN
                LEAVE parti_loop;
            END IF;
        END LOOP parti_loop;
    END main_proc;
    
    SELECT CONCAT('Arşivleme tamamlandı. ', v_islem_sayisi, ' kayıt arşivlendi. ',
                 'Toplam süre: ', TIMESTAMPDIFF(SECOND, v_baslangic_zamani, NOW()), ' saniye.') AS sonuc;
END//

DELIMITER ;

-- Kullanım örneği
CALL EskiVerileriArsivle('siparisler', 'siparis_tarihi', '2023-01-01', 1000);
```

## Tetikleyiciler (Triggers)

Tetikleyiciler (Triggers), belirli bir tabloda INSERT, UPDATE veya DELETE işlemi gerçekleştiğinde otomatik olarak çalışan SQL kodlarıdır. Veri bütünlüğünü korumak, ilişkili tabloları güncellemek, log tutmak gibi amaçlarla kullanılırlar.

### INSERT Trigger

INSERT işlemi gerçekleştiğinde çalışan tetikleyici örneği:

```sql name=insert_trigger.sql
DELIMITER //

-- Yeni bir ürün eklendiğinde stok hareketi oluşturan tetikleyici
CREATE TRIGGER trg_urun_eklendi 
AFTER INSERT ON urunler
FOR EACH ROW
BEGIN
    -- Stok hareketi tablosuna kayıt ekle
    INSERT INTO stok_hareketleri (
        urun_id, 
        hareket_turu, 
        miktar, 
        birim_fiyat, 
        toplam_tutar, 
        aciklama, 
        islem_tarihi
    ) 
    VALUES (
        NEW.urun_id, 
        'BAŞLANGIÇ', 
        NEW.stok_miktari, 
        NEW.birim_fiyat, 
        NEW.stok_miktari * NEW.birim_fiyat, 
        'Yeni ürün kaydı', 
        NOW()
    );
    
    -- Kategori istatistiklerini güncelle
    UPDATE kategoriler 
    SET urun_sayisi = urun_sayisi + 1 
    WHERE kategori_id = NEW.kategori_id;
END//

DELIMITER ;
```

### UPDATE Trigger

UPDATE işlemi gerçekleştiğinde çalışan tetikleyici örneği:

```sql name=update_trigger.sql
DELIMITER //

-- Öğrenci not ortalaması güncellendiğinde durum kontrolü yapan tetikleyici
CREATE TRIGGER trg_not_ortalamasi_guncellendi
BEFORE UPDATE ON ogrenci
FOR EACH ROW
BEGIN
    DECLARE v_mezuniyet_durumu VARCHAR(50);
    
    -- Not ortalaması değiştiyse durum kontrolü yap
    IF NEW.not_ortalamasi != OLD.not_ortalamasi THEN
        IF NEW.not_ortalamasi >= 2.00 THEN
            SET v_mezuniyet_durumu = 'Mezuniyet Şartlarını Sağlıyor';
        ELSE
            SET v_mezuniyet_durumu = 'Mezuniyet Şartlarını Sağlamıyor';
        END IF;
        
        SET NEW.mezuniyet_durumu = v_mezuniyet_durumu;
        SET NEW.son_guncelleme = NOW();
        
        -- Log tablosuna kayıt ekle
        INSERT INTO ogrenci_not_degisiklikleri (
            ogrenci_id, 
            eski_ortalama, 
            yeni_ortalama, 
            degisiklik_tarihi, 
            degisiklik_yapan
        ) 
        VALUES (
            NEW.ogrenci_id, 
            OLD.not_ortalamasi, 
            NEW.not_ortalamasi, 
            NOW(), 
            CURRENT_USER()
        );
    END IF;
END//

DELIMITER ;
```

### DELETE Trigger

DELETE işlemi gerçekleştiğinde çalışan tetikleyici örneği:

```sql name=delete_trigger.sql
DELIMITER //

-- Ürün silindiğinde ilişkili kayıtları kontrol eden tetikleyici
CREATE TRIGGER trg_urun_silindi
BEFORE DELETE ON urunler
FOR EACH ROW
BEGIN
    DECLARE v_siparis_sayisi INT;
    
    -- Ürüne ait sipariş var mı kontrol et
    SELECT COUNT(*) INTO v_siparis_sayisi 
    FROM siparis_detaylari 
    WHERE urun_id = OLD.urun_id;
    
    -- Ürüne ait sipariş varsa silme işlemini iptal et
    IF v_siparis_sayisi > 0 THEN
        SIGNAL SQLSTATE '45000' 
        SET MESSAGE_TEXT = 'Bu ürün siparişlerde kullanıldığı için silinemez. Önce ürünü pasife çekin.';
    END IF;
    
    -- Ürün silindi log kaydı oluştur
    INSERT INTO silinen_urunler (
        urun_id, 
        urun_adi, 
        kategori_id, 
        birim_fiyat, 
        stok_miktari, 
        silme_tarihi, 
        silen_kullanici
    ) 
    VALUES (
        OLD.urun_id, 
        OLD.urun_adi, 
        OLD.kategori_id, 
        OLD.birim_fiyat, 
        OLD.stok_miktari, 
        NOW(), 
        CURRENT_USER()
    );
    
    -- Kategori istatistiklerini güncelle
    UPDATE kategoriler 
    SET urun_sayisi = urun_sayisi - 1 
    WHERE kategori_id = OLD.kategori_id;
END//

DELIMITER ;
```

### İş Akışı Otomasyonu: Stok Yönetim Sistemi

Aşağıdaki örnekte, bir stok yönetim sisteminde tetikleyiciler kullanarak otomatik iş akışları oluşturulmuştur:

```sql name=stok_yonetim_sistemi.sql
DELIMITER //

-- Ürün tablosu için tetikleyiciler
-- 1. Stok miktarı güncellendiğinde stok hareketi oluştur
CREATE TRIGGER trg_stok_guncellendi
AFTER UPDATE ON urunler
FOR EACH ROW
BEGIN
    DECLARE v_hareket_turu VARCHAR(20);
    DECLARE v_miktar INT;
    DECLARE v_aciklama VARCHAR(255);
    
    -- Stok miktarı değiştiyse işlem yap
    IF NEW.stok_miktari != OLD.stok_miktari THEN
        IF NEW.stok_miktari > OLD.stok_miktari THEN
            SET v_hareket_turu = 'GİRİŞ';
            SET v_miktar = NEW.stok_miktari - OLD.stok_miktari;
            SET v_aciklama = 'Stok girişi';
        ELSE
            SET v_hareket_turu = 'ÇIKIŞ';
            SET v_miktar = OLD.stok_miktari - NEW.stok_miktari;
            SET v_aciklama = 'Stok çıkışı';
        END IF;
        
        -- Stok hareketi kaydet
        INSERT INTO stok_hareketleri (
            urun_id, 
            hareket_turu, 
            miktar, 
            birim_fiyat, 
            toplam_tutar, 
            aciklama, 
            islem_tarihi
        ) 
        VALUES (
            NEW.urun_id, 
            v_hareket_turu, 
            v_miktar, 
            NEW.birim_fiyat, 
            v_miktar * NEW.birim_fiyat, 
            v_aciklama, 
            NOW()
        );
        
        -- Kritik stok seviyesi kontrolü
        IF NEW.stok_miktari <= NEW.kritik_stok_seviyesi AND OLD.stok_miktari > OLD.kritik_stok_seviyesi THEN
            -- Stok uyarı tablosuna ekle
            INSERT INTO stok_uyarilari (
                urun_id, 
                mevcut_stok, 
                kritik_stok, 
                uyari_mesaji, 
                uyari_tarihi, 
                durum
            ) 
            VALUES (
                NEW.urun_id, 
                NEW.stok_miktari, 
                NEW.kritik_stok_seviyesi, 
                CONCAT('Kritik stok seviyesinin altına düştü: ', NEW.urun_adi), 
                NOW(), 
                'AÇIK'
            );
        END IF;
    END IF;
    
    -- Fiyat değiştiyse fiyat geçmişi kaydet
    IF NEW.birim_fiyat != OLD.birim_fiyat THEN
        INSERT INTO fiyat_degisim_gecmisi (
            urun_id, 
            eski_fiyat, 
            yeni_fiyat, 
            degisim_tarihi, 
            degisim_orani
        ) 
        VALUES (
            NEW.urun_id, 
            OLD.birim_fiyat, 
            NEW.birim_fiyat, 
            NOW(), 
            ROUND(((NEW.birim_fiyat - OLD.birim_fiyat) / OLD.birim_fiyat) * 100, 2)
        );
    END IF;
END//

-- 2. Sipariş detayı eklendiğinde stok miktarını azalt
CREATE TRIGGER trg_siparis_detayi_eklendi
AFTER INSERT ON siparis_detaylari
FOR EACH ROW
BEGIN
    DECLARE v_mevcut_stok INT;
    
    -- Mevcut stok miktarını kontrol et
    SELECT stok_miktari INTO v_mevcut_stok 
    FROM urunler 
    WHERE urun_id = NEW.urun_id;
    
    -- Stok yetersizse hata ver
    IF v_mevcut_stok < NEW.miktar THEN
        SIGNAL SQLSTATE '45000' 
        SET MESSAGE_TEXT = 'Yetersiz stok miktarı!';
    ELSE
        -- Stok miktarını güncelle
        UPDATE urunler 
        SET stok_miktari = stok_miktari - NEW.miktar 
        WHERE urun_id = NEW.urun_id;
        
        -- Ürün satış istatistiklerini güncelle
        INSERT INTO urun_satis_istatistikleri (
            urun_id, 
            siparis_id, 
            satis_miktari, 
            birim_fiyat, 
            toplam_tutar, 
            satis_tarihi
        ) 
        VALUES (
            NEW.urun_id, 
            NEW.siparis_id, 
            NEW.miktar, 
            NEW.birim_fiyat, 
            NEW.toplam_tutar, 
            NOW()
        ) 
        ON DUPLICATE KEY UPDATE 
            satis_miktari = satis_miktari + NEW.miktar,
            toplam_tutar = toplam_tutar + NEW.toplam_tutar;
    END IF;
END//

-- 3. Sipariş detayı silindiğinde stok miktarını geri ekle
CREATE TRIGGER trg_siparis_detayi_silindi
AFTER DELETE ON siparis_detaylari
FOR EACH ROW
BEGIN
    -- Siparişin durumunu kontrol et
    DECLARE v_siparis_durumu VARCHAR(50);
    
    SELECT durum INTO v_siparis_durumu 
    FROM siparisler 
    WHERE siparis_id = OLD.siparis_id;
    
    -- Sadece tamamlanmamış siparişlerde stok iadesi yap
    IF v_siparis_durumu NOT IN ('Tamamlandı', 'Teslim Edildi') THEN
        -- Stok miktarını geri artır
        UPDATE urunler 
        SET stok_miktari = stok_miktari + OLD.miktar 
        WHERE urun_id = OLD.urun_id;
        
        -- Stok iade hareketi oluştur
        INSERT INTO stok_hareketleri (
            urun_id, 
            hareket_turu, 
            miktar, 
            birim_fiyat, 
            toplam_tutar, 
            aciklama, 
            islem_tarihi
        ) 
        VALUES (
            OLD.urun_id, 
            'İADE', 
            OLD.miktar, 
            OLD.birim_fiyat, 
            OLD.toplam_tutar, 
            CONCAT('Sipariş iptali nedeniyle stok iadesi (Sipariş ID: ', OLD.siparis_id, ')'), 
            NOW()
        );
    END IF;
END//

DELIMITER ;
```

## Stored Procedures ve Functions

MySQL'de Stored Procedures ve Functions, veritabanı içinde saklanan ve çalıştırılabilen SQL kod bloklarıdır. Bu yapılar, karmaşık işlemleri daha modüler hale getirmenize, performansı artırmanıza ve güvenliği sağlamanıza yardımcı olur.

### Stored Procedures

Stored Procedure, bir veya birden fazla SQL ifadesini içeren ve sunucuda saklanan bir veritabanı nesnesidir. Parametreler alabilir, değişkenler kullanabilir ve sonuç kümelerini döndürebilir.

#### Temel Stored Procedure Örneği

```sql name=temel_stored_procedure.sql
DELIMITER //

-- Parametre almayan basit bir stored procedure
CREATE PROCEDURE sp_KritikStokSeviyesindekiUrunler()
BEGIN
    SELECT 
        urun_id,
        urun_adi,
        stok_miktari,
        kritik_stok_seviyesi,
        CASE 
            WHEN stok_miktari = 0 THEN 'Stok Yok'
            WHEN stok_miktari <= kritik_stok_seviyesi * 0.5 THEN 'Çok Kritik'
            ELSE 'Kritik'
        END AS durum
    FROM 
        urunler
    WHERE 
        stok_miktari <= kritik_stok_seviyesi
    ORDER BY 
        stok_miktari ASC;
END//

DELIMITER ;

-- Kullanım örneği
CALL sp_KritikStokSeviyesindekiUrunler();
```

#### Parametreli Stored Procedure

```sql name=parametreli_stored_procedure.sql
DELIMITER //

-- Çeşitli parametre türlerini içeren stored procedure
CREATE PROCEDURE sp_UrunFiyatGuncelle(
    IN p_urun_id INT,                  -- Giriş parametresi
    IN p_yeni_fiyat DECIMAL(10,2),     -- Giriş parametresi
    OUT p_eski_fiyat DECIMAL(10,2),    -- Çıkış parametresi
    OUT p_degisim_yuzdesi DECIMAL(5,2) -- Çıkış parametresi
)
BEGIN
    DECLARE v_mevcut_fiyat DECIMAL(10,2);
    
    -- Mevcut fiyatı al
    SELECT birim_fiyat INTO v_mevcut_fiyat
    FROM urunler
    WHERE urun_id = p_urun_id;
    
    -- Eski fiyatı çıkış parametresine ata
    SET p_eski_fiyat = v_mevcut_fiyat;
    
    -- Değişim yüzdesini hesapla
    SET p_degisim_yuzdesi = ROUND(((p_yeni_fiyat - v_mevcut_fiyat) / v_mevcut_fiyat) * 100, 2);
    
    -- Fiyatı güncelle
    UPDATE urunler
    SET 
        birim_fiyat = p_yeni_fiyat,
        son_guncelleme_tarihi = NOW()
    WHERE 
        urun_id = p_urun_id;
    
    -- Fiyat değişim geçmişini kaydet
    INSERT INTO fiyat_degisim_gecmisi (
        urun_id,
        eski_fiyat,
        yeni_fiyat,
        degisim_tarihi,
        degisim_orani
    )
    VALUES (
        p_urun_id,
        p_eski_fiyat,
        p_yeni_fiyat,
        NOW(),
        p_degisim_yuzdesi
    );
    
    -- Sonuç mesajı
    SELECT 
        CONCAT('Ürün fiyatı güncellendi: ', p_eski_fiyat, ' TL -> ', p_yeni_fiyat, ' TL (Değişim: %', p_degisim_yuzdesi, ')') AS mesaj;
END//

DELIMITER ;

-- Kullanım örneği
SET @eski_fiyat = 0;
SET @degisim_yuzdesi = 0;
CALL sp_UrunFiyatGuncelle(101, 149.99, @eski_fiyat, @degisim_yuzdesi);
SELECT @eski_fiyat, @degisim_yuzdesi;
```

#### Hata Yönetimi ile Stored Procedure

```sql name=hata_yonetimi_stored_procedure.sql
DELIMITER //

-- Hata yönetimi içeren stored procedure
CREATE PROCEDURE sp_MusteriSiparisBilgileriniGetir(
    IN p_musteri_id INT
)
BEGIN
    DECLARE v_musteri_var INT DEFAULT 0;
    DECLARE v_siparis_var INT DEFAULT 0;
    
    -- Hata durumunu yakalamak için CONTINUE HANDLER
    DECLARE CONTINUE HANDLER FOR SQLEXCEPTION
    BEGIN
        -- Hata mesajı
        SELECT 'Bir hata oluştu. İşlem iptal edildi.' AS hata_mesaji;
        
        -- Açık olan cursors varsa kapat
        CLOSE IF @@CURSOR_COUNT > 0;
        
        -- Açık olan transaction varsa rollback yap
        ROLLBACK;
    END;
    
    -- Müşteri var mı kontrol et
    SELECT COUNT(*) INTO v_musteri_var 
    FROM musteriler 
    WHERE musteri_id = p_musteri_id;
    
    IF v_musteri_var = 0 THEN
        SIGNAL SQLSTATE '45000' 
        SET MESSAGE_TEXT = 'Belirtilen müşteri bulunamadı.';
    END IF;
    
    -- Siparişleri var mı kontrol et
    SELECT COUNT(*) INTO v_siparis_var 
    FROM siparisler 
    WHERE musteri_id = p_musteri_id;
    
    -- Müşteri bilgilerini getir
    SELECT 
        m.musteri_id,
        m.ad,
        m.soyad,
        m.email,
        m.telefon,
        m.kayit_tarihi,
        COUNT(s.siparis_id) AS toplam_siparis_sayisi,
        SUM(s.toplam_tutar) AS toplam_harcama
    FROM 
        musteriler m
    LEFT JOIN 
        siparisler s ON m.musteri_id = s.musteri_id
    WHERE 
        m.musteri_id = p_musteri_id
    GROUP BY 
        m.musteri_id;
    
    -- Müşterinin siparişleri varsa sipariş detaylarını getir
    IF v_siparis_var > 0 THEN
        -- Son 5 siparişi göster
        SELECT 
            s.siparis_id,
            s.siparis_tarihi,
            s.durum,
            s.toplam_tutar,
            s.odeme_yontemi,
            COUNT(sd.urun_id) AS toplam_urun_cesidi,
            SUM(sd.miktar) AS toplam_urun_adedi
        FROM 
            siparisler s
        LEFT JOIN 
            siparis_detaylari sd ON s.siparis_id = sd.siparis_id
        WHERE 
            s.musteri_id = p_musteri_id
        GROUP BY 
            s.siparis_id
        ORDER BY 
            s.siparis_tarihi DESC
        LIMIT 5;
        
        -- Müşterinin en çok aldığı ürünleri göster
        SELECT 
            u.urun_id,
            u.urun_adi,
            SUM(sd.miktar) AS toplam_satin_alinan,
            COUNT(DISTINCT s.siparis_id) AS siparis_sayisi,
            MAX(s.siparis_tarihi) AS son_satin_alma_tarihi
        FROM 
            urunler u
        JOIN 
            siparis_detaylari sd ON u.urun_id = sd.urun_id
        JOIN 
            siparisler s ON sd.siparis_id = s.siparis_id
        WHERE 
            s.musteri_id = p_musteri_id
        GROUP BY 
            u.urun_id
        ORDER BY 
            toplam_satin_alinan DESC
        LIMIT 5;
    ELSE
        SELECT 'Müşteriye ait sipariş bulunamadı.' AS bilgi;
    END IF;
END//

DELIMITER ;

-- Kullanım örneği
CALL sp_MusteriSiparisBilgileriniGetir(1001);
```

### Functions

MySQL Functions, belirli bir işlemi gerçekleştiren ve geriye tek bir değer döndüren veritabanı nesneleridir. SQL sorgularında doğrudan kullanılabilirler.

#### Temel Function Örneği

```sql name=temel_function.sql
DELIMITER //

-- Vergi dahil fiyat hesaplayan function
CREATE FUNCTION fn_VergiDahilFiyatHesapla(
    p_fiyat DECIMAL(10,2),
    p_vergi_orani DECIMAL(5,2)
) 
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    DECLARE v_sonuc DECIMAL(10,2);
    
    SET v_sonuc = p_fiyat + (p_fiyat * p_vergi_orani / 100);
    
    RETURN v_sonuc;
END//

DELIMITER ;

-- Kullanım örneği
SELECT 
    urun_id,
    urun_adi,
    birim_fiyat AS vergisiz_fiyat,
    fn_VergiDahilFiyatHesapla(birim_fiyat, 18) AS vergili_fiyat
FROM 
    urunler
LIMIT 10;
```

#### Gelişmiş Function Örneği

```sql name=gelismis_function.sql
DELIMITER //

-- Müşteri puanına göre indirim oranı hesaplayan function
CREATE FUNCTION fn_IndirimOraniHesapla(
    p_musteri_id INT,
    p_sepet_tutari DECIMAL(10,2)
) 
RETURNS DECIMAL(5,2)
READS SQL DATA
BEGIN
    DECLARE v_musteri_puan INT;
    DECLARE v_musteri_seviye VARCHAR(20);
    DECLARE v_indirim_orani DECIMAL(5,2) DEFAULT 0;
    
    -- Müşteri puanını al
    SELECT puan INTO v_musteri_puan
    FROM musteriler
    WHERE musteri_id = p_musteri_id;
    
    -- Müşteri seviyesini belirle
    IF v_musteri_puan >= 1000 THEN
        SET v_musteri_seviye = 'PLATINUM';
    ELSEIF v_musteri_puan >= 500 THEN
        SET v_musteri_seviye = 'GOLD';
    ELSEIF v_musteri_puan >= 100 THEN
        SET v_musteri_seviye = 'SILVER';
    ELSE
        SET v_musteri_seviye = 'BRONZE';
    END IF;
    
    -- Seviyeye göre temel indirim oranını belirle
    CASE v_musteri_seviye
        WHEN 'PLATINUM' THEN SET v_indirim_orani = 10;
        WHEN 'GOLD' THEN SET v_indirim_orani = 7;
        WHEN 'SILVER' THEN SET v_indirim_orani = 5;
        WHEN 'BRONZE' THEN SET v_indirim_orani = 2;
    END CASE;
    
    -- Sepet tutarına göre ek indirim
    IF p_sepet_tutari >= 5000 THEN
        SET v_indirim_orani = v_indirim_orani + 5;
    ELSEIF p_sepet_tutari >= 2500 THEN
        SET v_indirim_orani = v_indirim_orani + 3;
    ELSEIF p_sepet_tutari >= 1000 THEN
        SET v_indirim_orani = v_indirim_orani + 1;
    END IF;
    
    -- Maksimum indirim sınırı
    IF v_indirim_orani > 15 THEN
        SET v_indirim_orani = 15;
    END IF;
    
    RETURN v_indirim_orani;
END//

DELIMITER ;

-- Kullanım örneği
SELECT 
    m.musteri_id,
    CONCAT(m.ad, ' ', m.soyad) AS musteri_adi,
    m.puan,
    s.siparis_id,
    s.toplam_tutar,
    fn_IndirimOraniHesapla(m.musteri_id, s.toplam_tutar) AS indirim_orani,
    ROUND(s.toplam_tutar * (1 - fn_IndirimOraniHesapla(m.musteri_id, s.toplam_tutar) / 100), 2) AS indirimli_tutar
FROM 
    musteriler m
JOIN 
    siparisler s ON m.musteri_id = s.musteri_id
ORDER BY 
    s.siparis_tarihi DESC
LIMIT 10;
```

### Tarih ve String İşlemleri için Utility Functions

```sql name=utility_functions.sql
DELIMITER //

-- Metni belirli bir uzunluğa kısaltan function
CREATE FUNCTION fn_MetniKisalt(
    p_metin TEXT,
    p_max_uzunluk INT
) 
RETURNS VARCHAR(255)
DETERMINISTIC
BEGIN
    DECLARE v_sonuc VARCHAR(255);
    
    IF LENGTH(p_metin) <= p_max_uzunluk THEN
        SET v_sonuc = p_metin;
    ELSE
        SET v_sonuc = CONCAT(LEFT(p_metin, p_max_uzunluk - 3), '...');
    END IF;
    
    RETURN v_sonuc;
END//

-- Tarih formatını değiştiren function
CREATE FUNCTION fn_TarihFormati(
    p_tarih DATETIME,
    p_format VARCHAR(20)
) 
RETURNS VARCHAR(50)
DETERMINISTIC
BEGIN
    DECLARE v_sonuc VARCHAR(50);
    
    CASE p_format
        WHEN 'KISA' THEN
            SET v_sonuc = DATE_FORMAT(p_tarih, '%d.%m.%Y');
        WHEN 'UZUN' THEN
            SET v_sonuc = DATE_FORMAT(p_tarih, '%d %M %Y');
        WHEN 'TAM' THEN
            SET v_sonuc = DATE_FORMAT(p_tarih, '%d.%m.%Y %H:%i:%s');
        WHEN 'GUN' THEN
            SET v_sonuc = DATE_FORMAT(p_tarih, '%W');
        ELSE
            SET v_sonuc = DATE_FORMAT(p_tarih, '%Y-%m-%d');
    END CASE;
    
    RETURN v_sonuc;
END//

-- Tarih ve Saat arasında kalan süreyi hesaplayan function
CREATE FUNCTION fn_SureHesapla(
    p_baslangic DATETIME,
    p_bitis DATETIME
) 
RETURNS VARCHAR(100)
DETERMINISTIC
BEGIN
    DECLARE v_fark_saniye INT;
    DECLARE v_gun INT;
    DECLARE v_saat INT;
    DECLARE v_dakika INT;
    DECLARE v_saniye INT;
    DECLARE v_sonuc VARCHAR(100);
    
    -- İki tarih arasındaki farkı saniye cinsinden hesapla
    SET v_fark_saniye = TIMESTAMPDIFF(SECOND, p_baslangic, p_bitis);
    
    -- Negatif farkları pozitife çevir
    IF v_fark_saniye < 0 THEN
        SET v_fark_saniye = -v_fark_saniye;
    END IF;
    
    -- Gün, saat, dakika ve saniye hesapla
    SET v_gun = FLOOR(v_fark_saniye / 86400);
    SET v_fark_saniye = v_fark_saniye % 86400;
    
    SET v_saat = FLOOR(v_fark_saniye / 3600);
    SET v_fark_saniye = v_fark_saniye % 3600;
    
    SET v_dakika = FLOOR(v_fark_saniye / 60);
    SET v_saniye = v_fark_saniye % 60;
    
    -- Sonuç formatını hazırla
    SET v_sonuc = '';
    
    IF v_gun > 0 THEN
        SET v_sonuc = CONCAT(v_sonuc, v_gun, ' gün ');
    END IF;
    
    IF v_saat > 0 OR v_gun > 0 THEN
        SET v_sonuc = CONCAT(v_sonuc, v_saat, ' saat ');
    END IF;
    
    IF v_dakika > 0 OR v_saat > 0 OR v_gun > 0 THEN
        SET v_sonuc = CONCAT(v_sonuc, v_dakika, ' dakika ');
    END IF;
    
    SET v_sonuc = CONCAT(v_sonuc, v_saniye, ' saniye');
    
    RETURN v_sonuc;
END//

DELIMITER ;

-- Kullanım örnekleri
SELECT fn_MetniKisalt('Bu çok uzun bir açıklama metni ve kısaltılması gerekiyor.', 20) AS kisaltilmis_metin;

SELECT 
    siparis_id,
    fn_TarihFormati(siparis_tarihi, 'KISA') AS kisa_tarih,
    fn_TarihFormati(siparis_tarihi, 'UZUN') AS uzun_tarih,
    fn_TarihFormati(siparis_tarihi, 'TAM') AS tam_tarih_saat,
    fn_TarihFormati(siparis_tarihi, 'GUN') AS siparis_gunu
FROM 
    siparisler
LIMIT 5;

SELECT
    siparis_id,
    siparis_tarihi AS siparis_zamani,
    teslim_tarihi AS teslim_zamani,
    fn_SureHesapla(siparis_tarihi, teslim_tarihi) AS teslimat_suresi
FROM
    siparisler
WHERE 
    sql name=utility_functions.sql
    teslim_tarihi IS NOT NULL
ORDER BY 
    teslimat_suresi DESC
LIMIT 5;
```

## Cursors Kullanımı

Cursor (İmleç), MySQL'de satır satır veri işlemenizi sağlayan bir veritabanı nesnesidir. Özellikle SELECT sorgusu sonucunda dönen her bir satır için farklı işlemler yapmak istediğinizde kullanışlıdır.

### Temel Cursor Örneği

```sql name=temel_cursor.sql
DELIMITER //

CREATE PROCEDURE sp_UrunStokRaporuOlustur()
BEGIN
    DECLARE v_urun_id INT;
    DECLARE v_urun_adi VARCHAR(100);
    DECLARE v_stok_miktari INT;
    DECLARE v_kategori_adi VARCHAR(50);
    DECLARE v_bitti INT DEFAULT 0;
    
    -- Rapor için geçici tablo oluştur
    CREATE TEMPORARY TABLE IF NOT EXISTS tmp_stok_raporu (
        urun_id INT,
        urun_adi VARCHAR(100),
        kategori_adi VARCHAR(50),
        stok_miktari INT,
        stok_durumu VARCHAR(20),
        guncelleme_tarihi DATETIME
    );
    
    -- Cursor tanımlama
    DECLARE urun_cursor CURSOR FOR 
        SELECT u.urun_id, u.urun_adi, u.stok_miktari, k.kategori_adi
        FROM urunler u
        JOIN kategoriler k ON u.kategori_id = k.kategori_id
        ORDER BY k.kategori_adi, u.urun_adi;
    
    -- Cursor sonuna gelindiğinde işaretleyecek handler
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET v_bitti = 1;
    
    -- Geçici tabloyu temizle
    TRUNCATE TABLE tmp_stok_raporu;
    
    -- Cursor'ı aç
    OPEN urun_cursor;
    
    -- Satırları oku
    read_loop: LOOP
        -- Bir sonraki satırı al
        FETCH urun_cursor INTO v_urun_id, v_urun_adi, v_stok_miktari, v_kategori_adi;
        
        -- Cursor sonu kontrolü
        IF v_bitti = 1 THEN
            LEAVE read_loop;
        END IF;
        
        -- Stok durumunu belirle
        INSERT INTO tmp_stok_raporu
        VALUES (
            v_urun_id,
            v_urun_adi,
            v_kategori_adi,
            v_stok_miktari,
            CASE 
                WHEN v_stok_miktari = 0 THEN 'Stok Yok'
                WHEN v_stok_miktari <= 10 THEN 'Kritik'
                WHEN v_stok_miktari <= 25 THEN 'Az'
                WHEN v_stok_miktari <= 50 THEN 'Normal'
                ELSE 'Yeterli'
            END,
            NOW()
        );
    END LOOP;
    
    -- Cursor'ı kapat
    CLOSE urun_cursor;
    
    -- Raporu göster
    SELECT * FROM tmp_stok_raporu ORDER BY kategori_adi, urun_adi;
    
    -- Geçici tabloyu temizle
    DROP TEMPORARY TABLE IF EXISTS tmp_stok_raporu;
END//

DELIMITER ;

-- Kullanım örneği
CALL sp_UrunStokRaporuOlustur();
```

### Dinamik SQL ile Cursor Kullanımı

```sql name=dinamik_sql_cursor.sql
DELIMITER //

CREATE PROCEDURE sp_KategoriUrunRaporuOlustur(
    IN p_min_stok INT,
    IN p_max_stok INT
)
BEGIN
    DECLARE v_kategori_id INT;
    DECLARE v_kategori_adi VARCHAR(50);
    DECLARE v_bitti INT DEFAULT 0;
    DECLARE v_sql VARCHAR(1000);
    
    -- Kategori cursor'ı
    DECLARE kategori_cursor CURSOR FOR 
        SELECT kategori_id, kategori_adi 
        FROM kategoriler 
        ORDER BY kategori_adi;
    
    -- Handler tanımı
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET v_bitti = 1;
    
    -- Raporlama başlangıcı
    SELECT CONCAT('Stok Raporu (', p_min_stok, ' - ', p_max_stok, ' arası)') AS 'Rapor Başlığı';
    SELECT CONCAT('Oluşturma Tarihi: ', NOW()) AS 'Oluşturma Bilgisi';
    
    -- Kategori cursor'ını aç
    OPEN kategori_cursor;
    
    -- Kategorileri oku
    kategori_loop: LOOP
        -- Bir sonraki kategoriyi al
        FETCH kategori_cursor INTO v_kategori_id, v_kategori_adi;
        
        -- Cursor sonu kontrolü
        IF v_bitti = 1 THEN
            LEAVE kategori_loop;
        END IF;
        
        -- Kategori başlığını göster
        SELECT CONCAT('Kategori: ', v_kategori_adi) AS 'Kategori Bilgisi';
        
        -- Dinamik SQL oluştur ve çalıştır
        SET v_sql = CONCAT('
            SELECT 
                u.urun_id,
                u.urun_adi,
                u.stok_miktari,
                u.birim_fiyat,
                u.stok_miktari * u.birim_fiyat AS stok_degeri
            FROM 
                urunler u
            WHERE 
                u.kategori_id = ', v_kategori_id, '
                AND u.stok_miktari BETWEEN ', p_min_stok, ' AND ', p_max_stok, '
            ORDER BY 
                u.stok_miktari DESC
        ');
        
        PREPARE stmt FROM v_sql;
        EXECUTE stmt;
        DEALLOCATE PREPARE stmt;
        
    END LOOP;
    
    -- Kategori cursor'ını kapat
    CLOSE kategori_cursor;
    
    -- Rapor özetini göster
    SELECT 
        COUNT(*) AS toplam_urun_sayisi,
        SUM(stok_miktari) AS toplam_stok_adedi,
        ROUND(AVG(stok_miktari), 2) AS ortalama_stok_miktari,
        SUM(stok_miktari * birim_fiyat) AS toplam_stok_degeri
    FROM 
        urunler
    WHERE 
        stok_miktari BETWEEN p_min_stok AND p_max_stok;
END//

DELIMITER ;

-- Kullanım örneği
CALL sp_KategoriUrunRaporuOlustur(10, 50);
```

### Cursors ile Satır Bazlı Veri İşleme

Aşağıdaki örnek, bir veritabanındaki siparişlerin durumunu güncelleyen ve bu işlemleri gerçekleştiren bir cursor kullanımını göstermektedir:

```sql name=satir_bazli_veri_isleme.sql
DELIMITER //

CREATE PROCEDURE sp_SiparisDurumlariniGuncelle(
    IN p_baslangic_tarihi DATE,
    IN p_bitis_tarihi DATE
)
BEGIN
    DECLARE v_siparis_id INT;
    DECLARE v_musteri_id INT;
    DECLARE v_siparis_tarihi DATETIME;
    DECLARE v_mevcut_durum VARCHAR(50);
    DECLARE v_teslimat_tarihi DATETIME;
    DECLARE v_toplam_tutar DECIMAL(10,2);
    DECLARE v_yeni_durum VARCHAR(50);
    DECLARE v_bildirim_mesaji VARCHAR(255);
    DECLARE v_bitti INT DEFAULT 0;
    DECLARE v_islem_sayisi INT DEFAULT 0;
    DECLARE v_guncellenen_sayisi INT DEFAULT 0;
    
    -- Cursor tanımı
    DECLARE siparis_cursor CURSOR FOR 
        SELECT 
            siparis_id, 
            musteri_id,
            siparis_tarihi, 
            durum, 
            teslim_tarihi, 
            toplam_tutar
        FROM 
            siparisler
        WHERE 
            siparis_tarihi BETWEEN p_baslangic_tarihi AND p_bitis_tarihi
        ORDER BY 
            siparis_tarihi;
    
    -- Handler tanımı
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET v_bitti = 1;
    
    -- Bildirim tablosu
    CREATE TEMPORARY TABLE IF NOT EXISTS tmp_siparis_bildirimleri (
        siparis_id INT,
        eski_durum VARCHAR(50),
        yeni_durum VARCHAR(50),
        guncelleme_tarihi DATETIME,
        bildirim_mesaji VARCHAR(255)
    );
    
    -- Transaction başlat
    START TRANSACTION;
    
    -- Cursor'ı aç
    OPEN siparis_cursor;
    
    -- Siparişleri oku ve güncelle
    siparis_loop: LOOP
        -- Bir sonraki siparişi al
        FETCH siparis_cursor INTO v_siparis_id, v_musteri_id, v_siparis_tarihi, v_mevcut_durum, v_teslimat_tarihi, v_toplam_tutar;
        
        -- Cursor sonu kontrolü
        IF v_bitti = 1 THEN
            LEAVE siparis_loop;
        END IF;
        
        SET v_islem_sayisi = v_islem_sayisi + 1;
        SET v_yeni_durum = v_mevcut_durum; -- Varsayılan olarak mevcut durumu koru
        SET v_bildirim_mesaji = NULL;
        
        -- Sipariş durumunu güncelleme koşulları
        CASE v_mevcut_durum
            WHEN 'Beklemede' THEN
                -- 3 günden eski bekleyen siparişleri iptal et
                IF DATEDIFF(NOW(), v_siparis_tarihi) > 3 THEN
                    SET v_yeni_durum = 'İptal Edildi';
                    SET v_bildirim_mesaji = 'Sipariş 3 gün içinde onaylanmadığı için otomatik iptal edildi.';
                END IF;
                
            WHEN 'Onaylandı' THEN
                -- 3 günden eski onaylanmış siparişleri kargoya ver
                IF DATEDIFF(NOW(), v_siparis_tarihi) > 3 THEN
                    SET v_yeni_durum = 'Kargoya Verildi';
                    SET v_bildirim_mesaji = 'Sipariş kargoya verildi.';
                END IF;
                
            WHEN 'Kargoya Verildi' THEN
                -- 7 günden eski kargodaki siparişleri teslim edildi olarak işaretle
                IF DATEDIFF(NOW(), v_siparis_tarihi) > 7 THEN
                    SET v_yeni_durum = 'Teslim Edildi';
                    SET v_teslimat_tarihi = NOW();
                    SET v_bildirim_mesaji = 'Sipariş teslim edildi.';
                END IF;
                
            WHEN 'Teslim Edildi' THEN
                -- 14 günden eski teslim edilmiş siparişleri tamamlandı olarak işaretle
                IF DATEDIFF(NOW(), v_teslimat_tarihi) > 14 THEN
                    SET v_yeni_durum = 'Tamamlandı';
                    SET v_bildirim_mesaji = 'Sipariş tamamlandı.';
                    
                    -- Sadece tamamlanan siparişler için müşteri puanı güncelleme
                    UPDATE musteriler 
                    SET puan = puan + FLOOR(v_toplam_tutar / 100) 
                    WHERE musteri_id = v_musteri_id;
                END IF;
        END CASE;
        
        -- Durum değiştiyse güncelle
        IF v_mevcut_durum != v_yeni_durum THEN
            -- Siparişi güncelle
            UPDATE siparisler 
            SET 
                durum = v_yeni_durum,
                teslim_tarihi = CASE WHEN v_yeni_durum = 'Teslim Edildi' THEN NOW() ELSE teslim_tarihi END,
                son_guncelleme = NOW()
            WHERE 
                siparis_id = v_siparis_id;
            
            -- Bildirim tablosuna ekle
            INSERT INTO tmp_siparis_bildirimleri (
                siparis_id,
                eski_durum,
                yeni_durum,
                guncelleme_tarihi,
                bildirim_mesaji
            ) VALUES (
                v_siparis_id,
                v_mevcut_durum,
                v_yeni_durum,
                NOW(),
                v_bildirim_mesaji
            );
            
            -- Bildirim oluştur
            INSERT INTO musteri_bildirimleri (
                musteri_id,
                bildirim_turu,
                bildirim_basligi,
                bildirim_mesaji,
                olusturma_tarihi,
                okundu
            ) VALUES (
                v_musteri_id,
                'Sipariş Durum Değişikliği',
                CONCAT('Sipariş #', v_siparis_id, ' durumu güncellendi'),
                v_bildirim_mesaji,
                NOW(),
                0
            );
            
            SET v_guncellenen_sayisi = v_guncellenen_sayisi + 1;
        END IF;
        
    END LOOP;
    
    -- Cursor'ı kapat
    CLOSE siparis_cursor;
    
    -- Transaction'ı tamamla
    COMMIT;
    
    -- Bildirim özetini göster
    SELECT 
        CONCAT('Toplam İşlenen: ', v_islem_sayisi, ', Güncellenen: ', v_guncellenen_sayisi) AS 'Özet';
    
    -- Güncellenen siparişlerin detaylarını göster
    SELECT * FROM tmp_siparis_bildirimleri;
    
    -- Geçici tabloyu temizle
    DROP TEMPORARY TABLE IF EXISTS tmp_siparis_bildirimleri;
END//

DELIMITER ;

-- Kullanım örneği
CALL sp_SiparisDurumlariniGuncelle('2025-01-01', '2025-03-01');
```

### Cursors ile Yapılan İşlemlerin Optimizasyonu

Cursor'lar, satır satır işlem yaptıkları için genellikle toplu (batch) işlemlere göre daha yavaştırlar. Ancak gerekli olduğu durumlarda, aşağıdaki optimizasyon teknikleri kullanılabilir:

```sql name=cursor_optimizasyon.sql
DELIMITER //

CREATE PROCEDURE sp_StokRaporuOptimize(
    IN p_parti_boyutu INT
)
BEGIN
    DECLARE v_son_id INT DEFAULT 0;
    DECLARE v_max_id INT;
    DECLARE v_islem_sayisi INT DEFAULT 0;
    
    -- Maksimum ürün ID'sini al
    SELECT MAX(urun_id) INTO v_max_id FROM urunler;
    
    -- Rapor tablosu
    CREATE TEMPORARY TABLE IF NOT EXISTS tmp_stok_raporu (
        urun_id INT PRIMARY KEY,
        urun_adi VARCHAR(100),
        kategori_adi VARCHAR(50),
        tedarikci_adi VARCHAR(100),
        stok_miktari INT,
        birim_fiyat DECIMAL(10,2),
        stok_degeri DECIMAL(15,2),
        son_guncelleme DATETIME
    );
    
    -- Partition (parti) boyutlarında işleme
    WHILE v_son_id < v_max_id DO
        -- Bir sonraki parti için ID aralığını belirle
        INSERT INTO tmp_stok_raporu
        SELECT 
            u.urun_id,
            u.urun_adi,
            k.kategori_adi,
            t.tedarikci_adi,
            u.stok_miktari,
            u.birim_fiyat,
            u.stok_miktari * u.birim_fiyat AS stok_degeri,
            NOW()
        FROM 
            urunler u
        JOIN 
            kategoriler k ON u.kategori_id = k.kategori_id
        JOIN 
            tedarikciler t ON u.tedarikci_id = t.tedarikci_id
        WHERE 
            u.urun_id > v_son_id
            AND u.urun_id <= v_son_id + p_parti_boyutu;
        
        -- İşlenen kayıt sayısını güncelle
        SET v_islem_sayisi = v_islem_sayisi + ROW_COUNT();
        
        -- Bir sonraki parti için son ID'yi güncelle
        SET v_son_id = v_son_id + p_parti_boyutu;
        
        -- Ara durumu göster
        SELECT CONCAT('İşlenen: ', v_islem_sayisi, ' kayıt. Son ID: ', v_son_id) AS durum;
        
        -- Sistemin diğer işlemlere zaman ayırabilmesi için kısa bekleme
        DO SLEEP(0.01);
    END WHILE;
    
    -- Sonuç raporunu göster
    SELECT
        kategori_adi,
        COUNT(*) AS urun_sayisi,
        SUM(stok_miktari) AS toplam_stok,
        SUM(stok_degeri) AS toplam_deger
    FROM
        tmp_stok_raporu
    GROUP BY
        kategori_adi
    ORDER BY
        toplam_deger DESC;
    
    -- Stok miktarına göre en yüksek değere sahip 10 ürün
    SELECT
        urun_id,
        urun_adi,
        kategori_adi,
        tedarikci_adi,
        stok_miktari,
        birim_fiyat,
        stok_degeri
    FROM
        tmp_stok_raporu
    ORDER BY
        stok_degeri DESC
    LIMIT 10;
    
    -- Geçici tabloyu temizle
    DROP TEMPORARY TABLE IF EXISTS tmp_stok_raporu;
END//

DELIMITER ;

-- Kullanım örneği
CALL sp_StokRaporuOptimize(1000);
```

## Performans ve Güvenlik

Bu bölümde, MySQL veritabanlarında performans optimizasyonu ve güvenlik en iyi uygulamalarını inceleyeceğiz.

### Performans Optimizasyonu

#### İndeksleme Stratejileri

İndeksler, veritabanı sorgularının hızını önemli ölçüde artırabilir. Aşağıda, etkili indeksleme stratejileri için bir örnek bulunmaktadır:

```sql name=indeksleme_stratejileri.sql
-- Temel indeks oluşturma
ALTER TABLE siparisler ADD INDEX idx_siparis_tarihi (siparis_tarihi);

-- Bileşik indeks oluşturma (birden fazla kolon)
ALTER TABLE siparis_detaylari ADD INDEX idx_siparis_urun (siparis_id, urun_id);

-- Tam metin indeksi oluşturma
ALTER TABLE urunler ADD FULLTEXT INDEX idx_urun_arama (urun_adi, aciklama);

-- Benzersiz indeks oluşturma
ALTER TABLE musteriler ADD UNIQUE INDEX idx_email (email);

-- İndeks kaldırma
ALTER TABLE siparisler DROP INDEX idx_siparis_tarihi;

-- İndeksleri görüntüleme
SHOW INDEX FROM siparisler;

-- İndeksleri açıklama (sorgu planını görüntüleme)
EXPLAIN SELECT * FROM siparisler WHERE siparis_tarihi BETWEEN '2025-01-01' AND '2025-03-01';
```

#### Sorgu Optimizasyonu

Sorgu optimizasyonu, veritabanı performansını artırmak için çok önemlidir. İşte bazı optimizasyon teknikleri:

```sql name=sorgu_optimizasyonu.sql
-- Kötü sorgu örneği
SELECT * FROM urunler WHERE YEAR(uretim_tarihi) = 2024;

-- İyi sorgu örneği (indeks kullanımına uygun)
SELECT * FROM urunler WHERE uretim_tarihi BETWEEN '2024-01-01' AND '2024-12-31';

-- Gereksiz DISTINCT kullanımından kaçınma
-- Kötü:
SELECT DISTINCT musteri_id FROM siparisler WHERE siparis_tarihi > '2025-01-01';
-- İyi:
SELECT musteri_id FROM siparisler WHERE siparis_tarihi > '2025-01-01' GROUP BY musteri_id;

-- JOIN optimizasyonu
-- Kötü:
SELECT o.ad, o.soyad, d.ders_adi
FROM ogrenci o, ders d, kayit k
WHERE o.ogrenci_id = k.ogrenci_id AND d.ders_id = k.ders_id;
-- İyi:
SELECT o.ad, o.soyad, d.ders_adi
FROM kayit k
JOIN ogrenci o ON k.ogrenci_id = o.ogrenci_id
JOIN ders d ON k.ders_id = d.ders_id;

-- Alt sorguları optimize etme
-- Kötü:
SELECT * FROM urunler 
WHERE kategori_id IN (SELECT kategori_id FROM kategoriler WHERE kategori_adi LIKE '%Elektronik%');
-- İyi:
SELECT u.* 
FROM urunler u
JOIN kategoriler k ON u.kategori_id = k.kategori_id
WHERE k.kategori_adi LIKE '%Elektronik%';

-- COUNT(*) optimizasyonu
-- Kötü:
SELECT COUNT(*) FROM buyuk_tablo;
-- İyi (eğer PRIMARY KEY veya NOT NULL bir sütun varsa):
SELECT COUNT(id) FROM buyuk_tablo;
```

#### Veritabanı Yapılandırma Optimizasyonu

MySQL sunucu yapılandırması, performansı önemli ölçüde etkileyebilir. İşte bazı önemli parametreler:

```sql name=veritabani_yapilandirma.sql
-- Mevcut sistem değişkenlerini görüntüleme
SHOW VARIABLES LIKE 'innodb_buffer_pool_size';
SHOW VARIABLES LIKE 'max_connections';
SHOW VARIABLES LIKE 'query_cache_size';

-- Geçici oturum değişkenlerini ayarlama
SET SESSION sort_buffer_size = 2 * 1024 * 1024; -- 2MB

-- Performans şemaları (MySQL 5.7 ve üzeri)
SELECT * FROM performance_schema.events_statements_summary_by_digest
ORDER BY sum_timer_wait DESC LIMIT 10;

-- Yavaş sorgu günlüğünü etkinleştirme
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1; -- 1 saniyeden uzun süren sorgular
SET GLOBAL slow_query_log_file = '/var/log/mysql/mysql-slow.log';

-- Tablo istatistiklerini güncelleme
ANALYZE TABLE siparisler;

-- Tablo optimizasyonu
OPTIMIZE TABLE siparisler;
```

### Güvenlik Önlemleri

#### Kullanıcı Yönetimi ve İzinler

MySQL'de güvenlik için sağlam bir kullanıcı yönetimi ve izin sistemi kurmak çok önemlidir:

```sql name=kullanici_yonetimi.sql
-- Yeni kullanıcı oluşturma
CREATE USER 'uygulama_kullanici'@'localhost' IDENTIFIED BY 'GuvenliParola123!';

-- Kullanıcıya belirli izinler verme
GRANT SELECT, INSERT, UPDATE ON e_ticaret.* TO 'uygulama_kullanici'@'localhost';

-- Salt okunur kullanıcı oluşturma
CREATE USER 'raporlama_kullanici'@'%' IDENTIFIED BY 'SadeceOku456!';
GRANT SELECT ON e_ticaret.* TO 'raporlama_kullanici'@'%';

-- Yönetici kullanıcısı oluşturma
CREATE USER 'admin_kullanici'@'localhost' IDENTIFIED BY 'YoneticiParola789!';
GRANT ALL PRIVILEGES ON e_ticaret.* TO 'admin_kullanici'@'localhost';

-- Belirli bir tabloya özel izinler
GRANT SELECT, INSERT ON e_ticaret.siparisler TO 'siparis_kullanici'@'localhost';

-- İzinleri geri alma
REVOKE DELETE ON e_ticaret.* FROM 'uygulama_kullanici'@'localhost';

-- Kullanıcı izinlerini görüntüleme
SHOW GRANTS FOR 'uygulama_kullanici'@'localhost';

-- Kullanıcı silme
DROP USER 'test_kullanici'@'localhost';

-- Tüm kullanıcıları listeleme
SELECT User, Host FROM mysql.user;

-- İzinleri yenileme
FLUSH PRIVILEGES;
```

#### Veri Şifreleme

Hassas verileri korumak için şifreleme teknikleri kullanabilirsiniz:

```sql name=veri_sifreleme.sql
-- MySQL'de AES şifreleme kullanımı
-- Şifreleme anahtarı (gerçek uygulamada güvenli bir yerde saklanmalıdır)
SET @sifreleme_anahtari = 'gizli_anahtar_123';

-- Veri şifreleme
UPDATE musteriler
SET kredi_karti_no = AES_ENCRYPT(kredi_karti_no, @sifreleme_anahtari)
WHERE kredi_karti_no IS NOT NULL;

-- Şifreli veriyi çözme
SELECT 
    musteri_id,
    ad,
    soyad,
    CAST(AES_DECRYPT(kredi_karti_no, @sifreleme_anahtari) AS CHAR) AS kredi_karti_no
FROM 
    musteriler
WHERE 
    kredi_karti_no IS NOT NULL;

-- Şifreli veri için fonksiyon oluşturma
DELIMITER //

CREATE FUNCTION fn_SifreliVeriCoz(
    p_sifrelenmis_veri VARBINARY(255)
) 
RETURNS VARCHAR(255)
DETERMINISTIC
BEGIN
    -- Gerçek uygulamada anahtar daha güvenli bir yerden alınmalıdır
    DECLARE v_anahtar VARCHAR(100) DEFAULT 'gizli_anahtar_123';
    
    IF p_sifrelenmis_veri IS NULL THEN
        RETURN NULL;
    ELSE
        RETURN CAST(AES_DECRYPT(p_sifrelenmis_veri, v_anahtar) AS CHAR);
    END IF;
END//

DELIMITER ;

-- Kullanım örneği
SELECT 
    musteri_id,
    ad,
    soyad,
    fn_SifreliVeriCoz(kredi_karti_no) AS kredi_karti_no
FROM 
    musteriler
LIMIT 10;
```

#### SQL Injection Önleme

SQL Injection, en yaygın ve tehlikeli veritabanı güvenlik açıklarından biridir. İşte önlem almak için bazı teknikler:

```sql name=sql_injection_onleme.sql
-- Kötü uygulama (SQL Injection'a açık)
-- Bu PHP kod örneği:
-- $sorgu = "SELECT * FROM kullanicilar WHERE kullanici_adi = '" . $_POST['kullanici_adi'] . "' AND sifre = '" . $_POST['sifre'] . "'";

-- İyi uygulama (Prepared Statements kullanımı)
PREPARE stmt FROM 'SELECT * FROM kullanicilar WHERE kullanici_adi = ? AND sifre = ?';
SET @kullanici = 'ahmet';
SET @sifre = 'gizli123';
EXECUTE stmt USING @kullanici, @sifre;
DEALLOCATE PREPARE stmt;

-- Kullanıcı girişini doğrulamak için stored procedure
DELIMITER //

CREATE PROCEDURE sp_KullaniciGirisKontrol(
    IN p_kullanici_adi VARCHAR(50),
    IN p_sifre VARCHAR(255)
)
BEGIN
    DECLARE v_kullanici_id INT;
    DECLARE v_hash_sifre VARCHAR(255);
    
    -- Kullanıcı adını kontrol et
    SELECT kullanici_id, sifre_hash INTO v_kullanici_id, v_hash_sifre
    FROM kullanicilar
    WHERE kullanici_adi = p_kullanici_adi;
    
    -- Kullanıcı bulunamadıysa veya şifre eşleşmiyorsa
    IF v_kullanici_id IS NULL THEN
        SELECT 0 AS basarili, 'Kullanıcı adı bulunamadı' AS mesaj;
    ELSEIF NOT v_hash_sifre = p_sifre THEN
        -- Gerçek uygulamada şifre hash karşılaştırması yapılmalıdır
        SELECT 0 AS basarili, 'Şifre yanlış' AS mesaj;
        
        -- Başarısız giriş denemesini kaydet
        INSERT INTO kullanici_giris_log (kullanici_id, durum, ip_adresi, tarih)
        VALUES (v_kullanici_id, 'BASARISIZ', '127.0.0.1', NOW());
    ELSE
        SELECT 1 AS basarili, 'Giriş başarılı' AS mesaj, v_kullanici_id AS kullanici_id;
        
        -- Başarılı girişi kaydet
        INSERT INTO kullanici_giris_log (kullanici_id, durum, ip_adresi, tarih)
        VALUES (v_kullanici_id, 'BASARILI', '127.0.0.1', NOW());
        
        -- Son giriş tarihini güncelle
        UPDATE kullanicilar SET son_giris_tarihi = NOW() WHERE kullanici_id = v_kullanici_id;
    END IF;
END//

DELIMITER ;

-- Kullanım örneği
CALL sp_KullaniciGirisKontrol('ahmet', 'gizli123');
```

### Yedekleme ve Geri Yükleme Stratejileri

Veritabanınızı korumak için düzenli yedekleme almak çok önemlidir:

```sql name=yedekleme_stratejileri.sql
-- MySQL Dump ile yedekleme (komut satırı)
-- mysqldump -u root -p e_ticaret > e_ticaret_backup_20250322.sql

-- Belirli tabloları yedekleme
-- mysqldump -u root -p e_ticaret siparisler siparis_detaylari > siparisler_backup_20250322.sql

-- Sadece şema yedekleme (veri olmadan)
-- mysqldump -u root -p --no-data e_ticaret > e_ticaret_schema_20250322.sql

-- Veritabanını geri yükleme
-- mysql -u root -p e_ticaret < e_ticaret_backup_20250322.sql

-- MySQL içinden yedekleme yapmak için
SELECT 'Veritabanı yedeğini almak için MySQL sunucusuna erişimi olan bir komut satırından mysqldump komutunu çalıştırın.' AS 'Yedekleme Bilgisi';

-- Yedekleme için bir stored procedure
DELIMITER //

CREATE PROCEDURE sp_TablolariYedekle(
    IN p_hedef_dizin VARCHAR(255)
)
BEGIN
    DECLARE v_tablo_adi VARCHAR(100);
    DECLARE v_dosya_adi VARCHAR(255);
    DECLARE v_komut VARCHAR(1000);
    DECLARE v_bitti INT DEFAULT 0;
    
    -- Tablo cursor'ı
    DECLARE tablo_cursor CURSOR FOR 
        SELECT table_name 
        FROM information_schema.tables 
        WHERE table_schema = DATABASE();
    
    -- Handler tanımı
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET v_bitti = 1;
    
    -- Cursor'ı aç
    OPEN tablo_cursor;
    
    -- Tabloları yedekle
    tablo_loop: LOOP
        -- Bir sonraki tabloyu al
        FETCH tablo_cursor INTO v_tablo_adi;
        
        -- Cursor sonu kontrolü
        IF v_bitti = 1 THEN
            LEAVE tablo_loop;
        END IF;
        
        -- Dosya adını oluştur
        SET v_dosya_adi = CONCAT(p_hedef_dizin, '/', v_tablo_adi, '_', DATE_FORMAT(NOW(), '%Y%m%d_%H%i%s'), '.sql');
        
        -- Yedekleme komutunu oluştur (gerçekte bu komutu doğrudan çalıştıramayız, sadece gösteriyoruz)
        SET v_komut = CONCAT('mysqldump -u root -p --single-transaction ', DATABASE(), ' ', v_tablo_adi, ' > ', v_dosya_adi);
        
        -- Komutu göster
        SELECT CONCAT('Tablo yedeği: ', v_tablo_adi, ', Komut: ', v_komut) AS 'Yedekleme İşlemi';
    END LOOP;
    
    -- Cursor'ı kapat
    CLOSE tablo_cursor;
    
    SELECT 'Yedekleme işlemi tamamlandı. Gerçek yedeklemeyi komut satırından çalıştırmalısınız.' AS 'Bilgi';
END//

DELIMITER ;

-- Kullanım örneği (sadece komutları gösterir, gerçek yedek almaz)
CALL sp_TablolariYedekle('/tmp/backups');
```

Bu kapsamlı MySQL rehberinin sonuna geldik. Veritabanı yönetimi, optimizasyon ve güvenlik konularında edindiğiniz bilgiler, sizlere yazılım mühendisliği kariyerinizde büyük fayda sağlayacaktır. Konuları daha derinlemesine keşfetmek için MySQL'in resmi dokümantasyonunu incelemenizi ve pratik uygulamalar geliştirmenizi öneririz.
