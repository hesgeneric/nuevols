# Nuevols URL Publisher · v4

Kendi kod şablonunu hedef URL'lerle doldur, rastgele isimli dosyaları
GitHub'a yükle ve jsDelivr bağlantılarını al. İşlemler artık tek ekrandadır.

## Kullanım

`nuevols-generator.html` dosyasını indir ve tarayıcıda aç. Üstte **v4 · tek ekran**
yazısını görmelisin. Kod ve GitHub ayarları gerektiğinde açılan bölümlerdedir.

Dört ana düğme aynı satırda, bu sırayla yer alır:

1. **Dosyaları hazırla**
2. **GitHub'a yükle**
3. **Tümünü kopyala**
4. **TXT indir**

Düğme satırı sonuçlara kaydırıldığında da üstte sabit kalır. Yükleme
bittiğinde sonuçlar açılır ve başarı kutusuna yumuşak kaydırma yapılır.
Azaltılmış hareket tercihinde kaydırma animasyonsuzdur.

## Token'ı bir kez kaydet

**GitHub bağlantısı ve ayarları** bölümünü aç, token'ını gir ve
**Token'ı kaydet** düğmesine bas. Kayıt doğrulandığında **Token kayıtlı**
göstergesi görünür ve giriş alanı kapanır. Sonraki yüklemelerde kayıtlı
token kullanılır. Değiştirmek için **Token'ı değiştir**, kaldırmak için
**Kayıtlı token'ı sil** düğmesi vardır.

Token ayrı bir localStorage kaydında saklanır; destekleyen tarayıcılarda
IndexedDB içinde ek bir yedeği tutulur. Ana token kaydı bulunamazsa bu
yedekten geri yüklenir. Parola alanının boşalması kayıtlı token'ı silmez.
Kayıt başarısızsa uygulama bunu açıkça bildirir. Token yalnızca kullanıcı
bilgisayarındaki tarayıcıda ve GitHub API yetkilendirmesinde kullanılır;
oluşturulan dosyalara, ZIP'e veya repoya eklenmez.

Tarayıcı verilerini silmek kayıtları silebilir. Dosya adresi, tarayıcı
profili ve dosya protokolü için depolama davranışı tarayıcıya bağlıdır.
Güncellemeleri önceki HTML'nin üzerine koyup aynı konumdan aç.

[GitHub token oluştur](https://github.com/settings/personal-access-tokens/new):

| Ayar | Değer |
| --- | --- |
| Resource owner | hesgeneric |
| Repository access | Only select repositories → nuevols |
| Repository permissions | Contents → Read and write |

Varsayılan repo `hesgeneric/nuevols`, klasör `configs`.
Branch boşsa repo varsayılanı algılanır.

## Kod şablonu

| Değişken | Yerleştirilen değer |
| --- | --- |
| `{{URL}}` | Hedef adres, ham metin olarak |
| `{{URL_JSON}}` | Tırnakları ve kaçışlarıyla JavaScript/JSON string değeri |

```js
const targetUrl = {{URL_JSON}};
console.log(targetUrl);
```

`{{URL_JSON}}` etrafına ayrıca tırnak koyma. Diğer kod değiştirilmez ve
uygulama içinde çalıştırılmaz. `.js`, `.json` ve `.txt` uzantıları seçilebilir;
JSON seçilirse üretilen dosyanın JSON biçimi kontrol edilir.
Protokolsüz hedeflere `https://` eklenir. Boş satırlar ve tekrarlar atlanır.
Bir işlemde en fazla 500 adres hazırlanır.

## Çıktı

Adresin başındaki `https://` kaldırılır ve kalan kısım encode edilir.
**Sonuç öneki** bunun başına değiştirilmeden eklenir:

```text
önek + encodeURIComponent(protokolsüzDosyaAdresi)
```

```text
cdn.jsdelivr.net%2Fgh%2Fhesgeneric%2Fnuevols%40COMMIT%2Fconfigs%2Fdosya.js
```

Önek, kod, hedef adresler, repo ayarları, hazırlanan dosyalar ve sonuçlar
kaydedilir. Sonuç listesi elle düzenlenebilir. Önek değişirse liste yeniden
oluşturulur; bu, yayımlanan dosyaları değiştirmez.

## Yükleme ve doğrulama

Mevcut repo içeriği korunur; branch güncellemesi force-push kullanmaz.
Boş repo ilk dosyayla başlatılır. Kısmi başarıda yalnızca yüklenmiş
 dosyanın sonucu gösterilir. Ağ kesintisinde yeniden denemeden önce repo
geçmişini kontrol et. Önizleme bağlantıları henüz yüklenmiş dosya değildir;
başarılı yükleme sonrasında bağlantılar commit sürümüne sabitlenir.
CDN erişimi ayrıca doğrulanmaz. Yayımlanan dosyalar herkese açıktır;
CDN silinen dosyaların kopyasını tutabilir.

`node tests/test.cjs` bağımlılıksız davranış testlerini çalıştırır. Tek
satırdaki dört düğme, URL encoding, önek, token kaydı ve bağımsız yedekten
geri yükleme, açık token silme, durum geri yükleme, kaydırma çağrısı ve
GitHub API hata senaryoları test edilir. Tarayıcı depolaması ve API için
taklit nesneler kullanılır. Bu ortamda gerçek tarayıcı testi yapılamamıştır.

Önceki `viewer.html` ve `configs/demo.json` bağımsız örneklerdir; bu akışın
çalışması için gerekmez. Repo otomatik GitHub Pages yayını açmaz.
