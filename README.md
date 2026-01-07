# Deney Sonu Teslimatı

Sistem Programlama ve Veri Yapıları bakış açısıyla veri tabanlarındaki performansı öne çıkaran hususlar nelerdir?

Aşağıda kutucuk (checkbox) ile gösterilen maddelerden en az birini seçtiğiniz açık kaynak kodlu bir VT kaynak kodları üzerinde göstererek açıklayınız. Açıklama bölümüne kısaca metninizi yazıp, kod üzerinde gösterim videonuzun linkini en altta belirtilen kutucuğa yerleştiriniz.

- [X]  Seçtiğiniz konu/konuları bu şekilde işaretleyiniz. **!**
    
---

# 1. Sistem Perspektifi (Operating System, Disk, Input/Output)

### Disk Erişimi

- [ ]  **Blok bazlı disk erişimi** → block_id + offset
- [X]  Rastgele erişim

### VT için Page (Sayfa) Anlamı

- [ ]  VT hangisini kullanır? **Satır/ Sayfa** okuması

---

### Buffer Pool

- [X]  Veritabanları, Sık kullanılan sayfaları bellekte (RAM) kopyalar mı (caching) ?

- [ ]  LRU / CLOCK gibi algoritmaları
- [X]  Diske yapılan I/O nasıl minimize ederler?

# 2. Veri Yapıları Perspektifi

- [ ]  B+ Tree Veri Yapıları VT' lerde nasıl kullanılır?
- [ ]  VT' lerde hangi veri yapıları hangi amaçlarla kullanılır?
- [ ]  Clustered vs Non-Clustered Index Kavramı
- [ ]  InnoDB satırı diskte nasıl durur?
- [ ]  LSM-tree (LevelDB, RocksDB) farkı
- [ ]  PostgreSQL heap + index ayrımı

DB diske yazarken:

- [X]  WAL (Write Ahead Log) İlkesi
- [ ]  Log disk (fsync vs write) sistem çağrıları farkı

---

# Özet Tablo

# Özet Tablo

| Kavram               | Bellek (RAM)                  | Disk / SQL Server            |
|----------------------|-------------------------------|------------------------------|
| Adresleme            | Pointer                       | Page ID + Offset (8 KB)      |
| Erişim Hızı          | O(1)                          | Page I/O                     |
| Erişim Birimi        | Nesne / Pointer               | Page (8 KB)                  |
| Veri Yapısı          | Array / Pointer               | B+ Tree (Index yapısı)       |
| Anahtar Kullanımı    | Yok                           | Clustered / Nonclustered Key |
| Önbellekleme (Cache) | CPU Cache                     | Buffer Pool                  |
| Güncelleme Mekanizması | Doğrudan Bellek             | WAL (Transaction Log)        |


---

# Video [Linki](https://youtu.be/UqeOi23asBk) 


---

# Açıklama

Veri tabanı yönetim sistemlerinde performans, yalnızca yazılan SQL sorgularına bağlı değildir. Aynı zamanda işletim sistemi, disk yapısı, bellek kullanımı ve kullanılan veri yapıları performansı doğrudan etkiler. Disk erişiminin bellek erişimine göre çok daha yavaş olması, veri tabanlarının disk kullanımını en aza indirecek şekilde tasarlanmasını gerekli kılar. SQL Server gibi modern veri tabanları, bu amaçla sayfa tabanlı disk erişimi, bellek önbellekleme (buffer pool), indeksleme ve Write Ahead Log (WAL) gibi mekanizmalar kullanır.

SQL Server’da veriler diskte satır satır değil, sayfa (page) bazlı olarak tutulur. Her bir sayfanın boyutu 8 KB’tır ve bir sorgu çalıştırıldığında SQL Server, ihtiyaç duyulan satır yerine ilgili sayfanın tamamını okur. Bu nedenle SQL Server için temel disk erişim birimi sayfadır. Ayrıca indeksler sayesinde rastgele erişim (random access) mümkün hâle gelir. İndeks kullanılan sorgularda, tüm tabloyu baştan sona okumak yerine yalnızca gerekli olan sayfalara erişilir. Bu durum, disk üzerinde yapılan okuma işlemlerini ciddi şekilde azaltır ve sorgu performansını artırır.

Disk erişiminin maliyetli olması nedeniyle SQL Server, sık kullanılan veri sayfalarını bellekte tutmak için Buffer Pool mekanizmasını kullanır. Diskten okunan her veri sayfası öncelikle Buffer Pool’a alınır. Aynı sayfaya daha sonra tekrar ihtiyaç duyulursa, veri diskten tekrar okunmaz; doğrudan bellekten erişilir. Bu sayede disk I/O işlemleri minimum seviyeye indirilmiş olur. SQL Server’da bellek yönetimi otomatik olarak gerçekleştirilir ve Lazy Writer ile Checkpoint mekanizmaları sayesinde bellekteki sayfalar kontrollü bir şekilde diske yazılır. Bu yaklaşım, hem sistem kararlılığını hem de performansı korumayı amaçlar.

Veri yapıları açısından bakıldığında, SQL Server indeksleri B+ Tree veri yapısını temel alır. B+ Tree yapısı, dengeli bir ağaç yapısı olduğu için arama işlemlerini hızlı bir şekilde gerçekleştirebilir. SQL Server’da clustered index, tablonun fiziksel sıralamasını belirler ve veriler doğrudan bu indeksin yaprak düğümlerinde tutulur. Non-clustered index ise verinin kendisini değil, veriye ulaşmak için gerekli olan anahtar bilgilerini içerir. Bu yapı sayesinde SQL Server, indeksli sorgularda “Index Seek” işlemi yaparak yalnızca ilgili sayfalara rastgele erişim sağlar ve gereksiz disk okuma işlemlerinden kaçınır.

Veri tabanlarında veri güvenliğini sağlamak için kullanılan bir diğer önemli mekanizma Write Ahead Log (WAL) ilkesidir. SQL Server’da bu ilke, transaction log dosyaları üzerinden uygulanır. Bir veri sayfasında değişiklik yapılmadan önce, yapılan değişiklik mutlaka transaction log dosyasına yazılır. Bu işlem tamamlandıktan sonra veri sayfaları diske aktarılır. Sistem beklenmedik bir şekilde kapanırsa, SQL Server transaction log’u kullanarak işlemleri geri alabilir veya tamamlayabilir. Bu sayede veri kaybı önlenir ve veri tabanının tutarlılığı korunur.

Sonuç olarak SQL Server, disk erişimini sayfa bazlı yaparak, Buffer Pool ile bellek önbelleklemesi kullanarak, B+ Tree tabanlı indeksler ile rastgele erişimi destekleyerek ve WAL ilkesi ile veri güvenliğini sağlayarak yüksek performans elde etmeyi amaçlar. Bu mekanizmalar birlikte çalışarak disk I/O maliyetini azaltır ve veri tabanı sistemlerinin hem hızlı hem de güvenilir olmasını sağlar.

## VT Üzerinde Gösterilen Kaynak Kodları

1. Microsoft SQL Server – Indexes (Clustered / Non-Clustered) [[Linki](https://learn.microsoft.com/en-us/sql/relational-databases/indexes/indexes)]
2. Microsoft SQL Server – Buffer Pool ve Bellek Yönetimi [[Linki](https://learn.microsoft.com/en-us/sql/relational-databases/performance/performance-center-for-database-engine-and-azure-sql-database)] 
3. Microsoft SQL Server – Transaction Log ve WAL (Write Ahead Log) [[Linki](https://learn.microsoft.com/en-us/sql/relational-databases/logs/the-transaction-log-sql-server)]
4. Database System Concepts – Silberschatz, Korth, Sudarshan [[Linki](https://youtu.be/UqeOi23asBk)] 
5. YouTube – Database Internals (Eğitsel Video)
