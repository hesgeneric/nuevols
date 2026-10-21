# Nuevols URL Publisher

Üç adımda kendi dosyalarını oluşturup GitHub'a yükler ve jsDelivr bağlantılarını alırsın.

## 1. Hazırla

`nuevols-generator.html` dosyasını indirip tarayıcıda aç. Hedef adresleri
satır satır gir. **Kod şablonu** alanında kendi içeriğini düzenle; dosya
uzantısını `.js`, `.json` veya `.txt` olarak seç. **Hazırla ve devam et**
düğmesi her hedef için ayrı, rastgele isimli bir dosya oluşturur.

| Kod değişkeni | Yerleştirilen değer |
| --- | --- |
| `{{URL}}` | Doğrulanmış hedef URL, ham metin olarak |
| `{{URL_JSON}}` | Tırnakları ve kaçışlarıyla bir JavaScript/JSON string değeri |

```js
const targetUrl = {{URL_JSON}};
console.log(targetUrl);
```

`{{URL_JSON}}` etrafına ayrıca tırnak koyma. `{{URL}}` ham metindir;
string içinde kullanırsan kaçışları sen yönetirsin. Şablondaki bütün
uygun değişkenler tek geçişte değiştirilir; kodun geri kalanı korunur.

Protokol yazılmazsa `https://` eklenir. Boş satırlar ve tekrarlar atlanır;
bir işlemde en fazla 500 adres hazırlanır. Girilen kod generator içinde
çalıştırılmaz. JSON modunda oluşturulan dosyanın JSON biçimi kontrol edilir.
Dosya uzantısını seçmek kodu başka bir dile çevirmez.

## 2. Yükle

GitHub token'ını ilk kullanımda gir. Repo ayarları gerektiğinde açılan
**GitHub ayarları** bölümündedir. Varsayılanlar:

| Ayar | Değer |
| --- | --- |
| Kullanıcı | hesgeneric |
| Repo | nuevols |
| Branch | Repo varsayılanı otomatik algılanır |
| Klasör | configs |

[Fine-grained GitHub token oluştur](https://github.com/settings/personal-access-tokens/new):

| Ayar | Değer |
| --- | --- |
| Token name | Nuevols Publisher |
| Resource owner | hesgeneric |
| Repository access | Only select repositories → nuevols |
| Repository permissions | Contents → Read and write |

**Generate token** düğmesine basıp değeri araçtaki alana gir. Token bu
tarayıcıda ayrı bir kalıcı kayıtta saklanır; sonraki açılışlarda ve
yüklemelerde korunur. Tarayıcı parola alanını boşaltsa bile kayıtlı token
kullanılır. Token için yazma, değiştirme, odaktan çıkma ve sayfadan ayrılma
anlarında kayıt yapılır.
**Kayıtlı token'ı sil** düğmesi tarayıcıdaki kopyasını temizler.
ChatGPT'nin GitHub bağlantısı bağımsız HTML'ye otomatik yetki sağlamaz.

Dosyaları yüklemeden önce içerik önizlemesini görebilir veya ZIP indirebilirsin.
**GitHub'a yükle** düğmesi yükleme bitince sonuç adımını açar ve ekranı
yumuşakça sonuçların başına kaydırır. Yeşil başarı kutusu bağlantıların
hemen üstünde görünür. Azaltılmış hareket tercihi açıksa kaydırma animasyonsuzdur.

## 3. Sonuçlar

Çıktı adresinden önce baştaki `https://` (veya `http://`) kaldırılır;
kalan adres `encodeURIComponent` ile encode edilir. Ardından önek aynen eklenir:

```text
önek + encodeURIComponent(protokolsüzDosyaAdresi)
```

Öneksiz örnek:

```text
cdn.jsdelivr.net%2Fgh%2Fhesgeneric%2Fnuevols%40COMMIT%2Fconfigs%2Fdosya.js
```

`https://example.com/?file=` önekiyle:

```text
https://example.com/?file=cdn.jsdelivr.net%2Fgh%2Fhesgeneric%2Fnuevols%40COMMIT%2Fconfigs%2Fdosya.js
```

Önek ayrıca encode edilmez; kullanıcı ne yazarsa aynen eklenir. Öneki
değiştirmek sonuçları anında yeniden oluşturur, yüklenen dosyalara dokunmaz.
Sonuç kutusu elle düzenlenebilir; bu düzenlemeler de kaydedilir.
Bağlantılar kopyalanabilir veya TXT olarak indirilebilir.

## Kaydedilen bilgiler

- GitHub token'ı ve repo ayarları.
- Kod şablonu, dosya türü ve hedef URL listesi.
- Sonuç öneki, hazırlanmış dosyaların bilgisi, yükleme sürümü ve sonuç listesi.
- Son açık adım.

Kayıtlar `localStorage` ile aynı tarayıcı ve aynı dosya adresinde saklanır.
Güncel HTML'yi önceki dosyanın üzerine koyup aynı yerden açarsan önceki
kod/repo ayarları da taşınır. Dosyayı başka adrese taşımak veya tarayıcı
verilerini silmek kayıtların erişilememesine yol açabilir. Depolama kapalı
veya doluysa ekranın üstünde **Kaydedilemedi** bilgisi gösterilir.
Token indirilen ZIP'lere veya GitHub'a yüklenen dosyalara eklenmez;
yalnızca GitHub API yetkilendirmesinde ve yerel kayıtta kullanılır.

## Yükleme davranışı

Mevcut repoda dosyalar tek commit ile eklenir ve diğer içerik korunur.
Boş repo önce ilk dosyayla başlatılır. Kalan dosyalar yüklenemezse yalnızca
başarılı olan dosyanın sonucu gösterilir. Branch force-push ile güncellenmez.
Önizleme çıktısı yükleme tamamlandığı anlamına gelmez; başarılı yüklemeden
sonra bağlantılar commit sürümüne sabitlenir. CDN erişimi ayrıca doğrulanmaz.
Ağ kesintisinde yeniden denemeden önce repo geçmişini kontrol et.
Dosyalar herkese açıktır; CDN silinmiş dosyaların kopyasını tutabilir.

Önceki sürümdeki `viewer.html` ve `configs/demo.json` bağımsız örneklerdir;
yeni arayüzün çalışması için gerekmez. Repo GitHub Pages yayınını otomatik açmaz.

## Testler

`node tests/test.cjs` komutu bağımlılıksız davranış testlerini çalıştırır.
Üç adımlı geçiş, verilen örnekle birebir URL encoding, önekin değişmeden
eklenmesi, token/ayar/sonuç geri yükleme, token silme, eski kayıtların
taşınması, depolama hatası, kod değişkenleri ve GitHub API hata senaryoları
kontrol edilir. Parola alanı boşalsa bile token kaydının korunması ve başarı
paneline kaydırma da test edilir. Canlı GitHub API testleri yerine taklit
yanıtlar kullanılır.
