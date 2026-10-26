# Proposal Form (Bitirme Projesi Başvuru Formu)

> **Status:** draft · **Date:** 2026-10-26
>
> Source text for the official form. The filled form is `01-proposal-form.docx` (not committed: it holds the student number and the advisor's details). The title is in Turkish and English; the body uses Turkish sentences with English technical terms (engine, order book, latency, benchmark, …), as the field actually talks.

## Proje Başlığı (Title of the Project)

Düşük Gecikmeli ve Deterministik Emir Eşleştirme Motoru

A Low-Latency, Deterministic Order Matching Engine

## Grup Üyeleri

In the `.docx` only.

## Verildiği Tarih

16.09.2026

## Danışman

In the `.docx` only.

## Projenin Amacı

Bir borsa, aynı anda çok sayıda kullanıcıdan alım ve satım emirleri (order) alır. Borsanın merkezinde matching engine adı verilen bir program bulunur. Bu program, henüz eşleşemeyen order'ları order book'ta tutar; bir alıcının fiyatı bir satıcının fiyatıyla buluştuğunda bir trade oluşturur. Borsaların çoğu price-time priority kuralını kullanır: önce en iyi fiyat eşleşir, aynı fiyattaki order'lar arasında ise önce gelen önce işlem görür [1].

Bu projenin amacı, MatchCore adı verilen böyle bir matching engine'i sıfırdan geliştirmek ve onun nasıl hızlı ve güvenilir hale getirilebileceğini incelemektir. Engine'in üç temel hedefi vardır: deterministic olması (aynı order listesi her zaman birebir aynı trade'leri üretir), low latency (order başına işlem süresi mikrosaniyeler mertebesinde) ve bir crash sonrasında hiçbir trade'i kaybetmeden veya değiştirmeden kaldığı yerden devam edebilmesi.

Bu proje, bilgisayar mühendisliğinin temel konularını bir araya getirir; her hedef bir konuya karşılık gelir. Order book'u tutmak bir data structure problemidir: engine en iyi fiyatı hızlıca bulmalı ve bir queue'nun ortasındaki bir order'ı hızlıca cancel edebilmelidir. Hız bir performance problemidir: memory layout, memory allocation ve latency'nin doğru ölçülmesi önemlidir. Crash recovery ise bir systems problemidir; her input'un bir event log'a yazılıp gerektiğinde replay edilmesiyle çözülür. Aynı yaklaşım LMAX borsasında da kullanılmaktadır [2].

Proje tam bir borsa değildir: kullanıcı hesapları, gerçek para veya gerçek bir piyasa bağlantısı yoktur ve yalnızca synthetic (üretilmiş) test verisi kullanılır. Projenin ana çıktıları, çalışan bir matching engine ve order book için farklı data structure seçeneklerinin benchmark'lara dayalı bir karşılaştırması olacaktır.

## Yöntem ve Metodlar

Projede "simplest version first" yaklaşımı izlenir. İlk versiyon, doğru çalışan en basit engine'dir; sonraki her versiyon tek bir iyileştirme ekler ve bir önceki versiyonla karşılaştırılarak ölçülür.

1. **Basic engine:** tek bir symbol; limit, market ve cancel order'ları; price-time priority; fiyatların integer olarak tutulması; single-threaded çalışma. Unit test'ler, order book'un her zaman valid kaldığını kontrol eder.
2. **Benchmarking:** synthetic order akışları üreten bir workload generator (örneğin çok sayıda cancel, agresif market order'lar, dar veya geniş fiyat aralıkları) ve throughput ile latency percentile'larını (p50, p99, p99.9) raporlayan bir benchmark programı.
3. **Data structure comparison:** price level'lar ve order lookup için daha hızlı data structure'ların denenmesi (örneğin balanced tree ile price-indexed array, hash map ile direct index) ve aynı workload'lar altında ilk versiyonla karşılaştırılması. Açık kaynaklı C++ engine'i Liquibook [3] referans olarak kullanılacaktır.
4. **Crash recovery:** her input bir event log'a yazılır, engine belirli aralıklarla snapshot alır ve bir crash'ten sonra log'u replay ederek birebir aynı state'e döner [2].
5. **Dashboard:** order book'u ve latency değerlerini gösteren, read-only bir web sayfası. Engine'i yavaşlatmaması için ayrı bir process olarak çalışır.

Zaman kalırsa, engine'in ikinci bir makineye basit bir replication'ı opsiyonel olarak eklenecektir.

**Yazılım:** engine için C++20 ve CMake; unit test'ler için GoogleTest; benchmark'lar için Google Benchmark ve HdrHistogram; dashboard server'ı için Go; bir web frontend (framework henüz belirlenmedi); version control için Git ve GitHub.

**Donanım:** kişisel laptop (Apple M2, 8 core, 16 GB RAM). Özel bir donanım gerekmemektedir.

## Belirlenen İş Zaman Çizelgesi

| Dönem | Zaman | Yapılacak iş |
| --- | --- | --- |
| Güz 2026 (BIL 497) | Ekim | Borsalar ve order book'lar üzerine araştırma, ilk design, proje önerisi |
| Güz 2026 (BIL 497) | Kasım | Unit test'leriyle basic engine |
| Güz 2026 (BIL 497) | Aralık | Workload generator, benchmark'lar ve ilk ölçümler; ara rapor |
| Bahar 2027 (BIL 498) | Şubat – Mart | Data structure comparison |
| Bahar 2027 (BIL 498) | Mart – Nisan | Crash recovery: event log, snapshot, replay |
| Bahar 2027 (BIL 498) | Nisan | Read-only web dashboard |
| Bahar 2027 (BIL 498) | Mayıs | Opsiyonel replication (zaman kalırsa), tez ve final sunumu |

## Referanslar

1. Gould, M. D., Porter, M. A., Williams, S., McDonald, M., Fenn, D. J. and Howison, S. D. (2013). Limit order books. *Quantitative Finance*, 13(11), 1709–1742.
2. Fowler, M. (2011). The LMAX Architecture. https://martinfowler.com/articles/lmax.html (erişim tarihi: 26.10.2026).
3. Liquibook: açık kaynaklı C++ order matching engine. GitHub deposu, https://github.com/enewhuis/liquibook (erişim tarihi: 26.10.2026).
