# Proposal Form (Bitirme Projesi Başvuru Formu)

> **Status:** draft · **Date:** 2026-10-26
>
> Source text for the official form. The filled form is `01-proposal-form.docx` (not committed: it holds the student number and the advisor's details). The title is in Turkish and English; the body is mainly Turkish, with English technical terms where they are the common usage (often in parentheses after the Turkish word).

## Proje Başlığı (Title of the Project)

Düşük Gecikmeli ve Deterministik Emir Eşleştirme Motoru

A Low-Latency, Deterministic Order Matching Engine

## Grup Üyeleri

In the `.docx` only.

## Verildiği Tarih

26.10.2026

## Danışman

In the `.docx` only.

## Projenin Amacı

Bir borsa, aynı anda çok sayıda kullanıcıdan alım ve satım emirleri alır. Borsanın merkezinde eşleştirme motoru (matching engine) adı verilen bir program bulunur. Bu program, henüz işlem göremeyen emirleri emir defterinde (order book) tutar; bir alıcının fiyatı bir satıcının fiyatıyla buluştuğunda bir işlem (trade) oluşturur. Borsaların çoğu fiyat-zaman önceliği (price-time priority) kuralını kullanır: önce en iyi fiyat eşleşir, aynı fiyattaki emirler arasında ise önce gelen önce işlem görür [1].

Bu projenin amacı, MatchCore adı verilen böyle bir eşleştirme motorunu sıfırdan geliştirmek ve onun nasıl hızlı ve güvenilir hale getirilebileceğini incelemektir. Motorun üç temel hedefi vardır: deterministik olması (aynı emir listesi her zaman birebir aynı işlemleri üretir), hızlı olması (emir başına işlem süresi mikrosaniyeler mertebesinde) ve bir çökme (crash) sonrasında hiçbir işlemi kaybetmeden veya değiştirmeden kaldığı yerden devam edebilmesi.

Bu proje, bilgisayar mühendisliğinin temel konularını bir araya getirir; her hedef bir konuya karşılık gelir. Emir defterini tutmak bir veri yapıları problemidir: motor en iyi fiyatı hızlıca bulmalı ve bir kuyruğun ortasındaki bir emri hızlıca iptal edebilmelidir. Hız bir performans problemidir: bellek düzeni, bellek ayırma (memory allocation) ve gecikmenin (latency) doğru ölçülmesi önemlidir. Çökme sonrası kurtarma ise bir sistem problemidir; her girdinin kaydedilip yeniden oynatılmasıyla (replay) çözülür. Aynı yaklaşım LMAX borsasında da kullanılmaktadır [2].

Proje tam bir borsa değildir: kullanıcı hesapları, gerçek para veya gerçek bir piyasa bağlantısı yoktur ve yalnızca sentetik (üretilmiş) test verisi kullanılır. Projenin ana çıktıları, çalışan bir eşleştirme motoru ve emir defteri için farklı veri yapısı seçeneklerinin ölçümlere dayalı bir karşılaştırması olacaktır.

## Yöntem ve Metodlar

Projede "önce en basit hali" yaklaşımı izlenir. İlk sürüm, doğru çalışan en basit motordur; sonraki her sürüm tek bir iyileştirme ekler ve bir önceki sürümle karşılaştırılarak ölçülür.

1. **Temel motor:** tek bir hisse senedi (symbol); limit, market ve iptal (cancel) emirleri; fiyat-zaman önceliği; fiyatların tam sayı olarak tutulması; tek iş parçacıklı (single-threaded) çalışma. Birim testleri (unit test), emir defterinin her zaman geçerli kaldığını kontrol eder.
2. **Ölçüm:** sentetik emir akışları üreten bir üreteç (örneğin çok sayıda iptal, agresif market emirleri, dar veya geniş fiyat aralıkları) ve throughput ile gecikme yüzdeliklerini (p50, p99, p99.9) raporlayan bir benchmark programı.
3. **Veri yapısı karşılaştırması:** fiyat seviyeleri ve emir arama için daha hızlı veri yapılarının denenmesi (örneğin dengeli ağaç ile fiyat indeksli dizi, hash map ile doğrudan indeks) ve aynı iş yükleri altında ilk sürümle karşılaştırılması. Açık kaynaklı C++ motoru Liquibook [3] referans olarak kullanılacaktır.
4. **Çökme sonrası kurtarma:** her girdi bir olay günlüğüne (event log) yazılır, motor belirli aralıklarla anlık görüntü (snapshot) alır ve bir çökmeden sonra günlüğü yeniden oynatarak birebir aynı duruma döner [2].
5. **Dashboard:** emir defterini ve gecikme değerlerini gösteren, salt okunur bir web sayfası. Motoru yavaşlatmaması için ayrı bir program olarak çalışır.

Zaman kalırsa, motorun ikinci bir makineye basit bir şekilde kopyalanması (replication) isteğe bağlı bir ek olarak yapılacaktır.

**Yazılım:** motor için C++20 ve CMake; birim testleri için GoogleTest; ölçümler için Google Benchmark ve HdrHistogram; dashboard sunucusu için Go; bir web arayüzü (framework henüz belirlenmedi); sürüm kontrolü için Git ve GitHub.

**Donanım:** kişisel dizüstü bilgisayar (Apple M2, 8 çekirdek, 16 GB RAM). Özel bir donanım gerekmemektedir.

## Belirlenen İş Zaman Çizelgesi

| Dönem | Zaman | Yapılacak iş |
| --- | --- | --- |
| Güz 2026 (BIL 497) | Ekim | Borsalar ve emir defterleri üzerine araştırma, ilk tasarım, proje önerisi |
| Güz 2026 (BIL 497) | Kasım | Birim testleriyle temel motor |
| Güz 2026 (BIL 497) | Aralık | Emir üreteci, benchmark'lar ve ilk ölçümler; ara rapor |
| Bahar 2027 (BIL 498) | Şubat – Mart | Veri yapısı karşılaştırması |
| Bahar 2027 (BIL 498) | Mart – Nisan | Çökme sonrası kurtarma: event log, snapshot, replay |
| Bahar 2027 (BIL 498) | Nisan | Salt okunur web dashboard |
| Bahar 2027 (BIL 498) | Mayıs | İsteğe bağlı replication (zaman kalırsa), tez ve final sunumu |

## Referanslar

1. Gould, M. D., Porter, M. A., Williams, S., McDonald, M., Fenn, D. J. and Howison, S. D. (2013). Limit order books. *Quantitative Finance*, 13(11), 1709–1742.
2. Fowler, M. (2011). The LMAX Architecture. https://martinfowler.com/articles/lmax.html (erişim tarihi: 26.10.2026).
3. Liquibook: açık kaynaklı C++ emir eşleştirme motoru. GitHub deposu, https://github.com/enewhuis/liquibook (erişim tarihi: 26.10.2026).
