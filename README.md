# Nuevols URL Publisher

Kendi kod şablonunu gir, hedef URL'leri değişkenlere yerleştir, rastgele
isimli dosyaları GitHub'a yükle ve jsDelivr bağlantılarını al.

## Kullanım

1. `nuevols-generator.html` dosyasını indir ve tarayıcıda aç.
2. Hedef URL'leri her satıra bir adres olacak şekilde gir.
3. Dosya türünü seç: `.js`, `.json` veya `.txt`.
4. **Yüklenecek kod şablonu** alanına kendi içeriğini yaz/yapıştır.
5. Hedef URL'nin yerleşeceği konumlara `{{URL}}` veya `{{URL_JSON}}` koy.
6. **Dosyaları hazırla** düğmesine bas. Önizlemeyi incele veya dosya paketini indir.
7. GitHub anahtarını gir; **Bağlantıyı kontrol et → GitHub'a yükle** sırasını uygula.
8. Düzenlenebilir sonuçları kopyala veya TXT indir.

## Kod değişkenleri

| Değişken | Yerine konulan değer |
| --- | --- |
| `{{URL}}` | Doğrulanmış hedef URL, ham metin olarak |
| `{{URL_JSON}}` | Tırnakları ve kaçışları dahil JavaScript/JSON string değeri |

JavaScript örneği:

```js
const targetUrl = {{URL_JSON}};
console.log(targetUrl);
```

`https://example.com/` için oluşan içerik:

```js
const targetUrl = "https://example.com/";
console.log(targetUrl);
```

`{{URL_JSON}}` etrafına ayrıca tırnak koyma. `{{URL}}` ham metindir;
JavaScript string'i içinde kullanırsan kaçışları sen yönetirsin.
Değişkenler şablondaki bütün eşleşmelerde tek geçişte değiştirilir.
Diğer kod içeriği değiştirilmez. URL protokolü yoksa `https://` eklenir;
URL tarayıcının URL kurallarına göre normalize edilir.

JSON örneği:

```json
{
  "targetUrl": {{URL_JSON}}
}
```

Bu örnek, yer tutucu doldurulduktan sonra geçerli JSON olur. JSON modunda
üretilen dosya parse edilerek kontrol edilir. JavaScript ve metin modlarında
sözdizimi kontrolü yapılmaz. Girilen kod generator içinde çalıştırılmaz.
Dosya türü seçimi kodu başka dile çevirmez; yalnızca dosya uzantısını belirler.

## GitHub kurulumu

[Fine-grained anahtar oluşturma sayfasını aç](https://github.com/settings/personal-access-tokens/new).

| Ayar | Değer |
| --- | --- |
| Token name | Nuevols Publisher |
| Resource owner | hesgeneric |
| Expiration | Örneğin 30 gün |
| Repository access | Only select repositories → nuevols |
| Repository permissions | Contents → Read and write |

**Generate token** düğmesine basıp çıkan anahtarı araçtaki parola alanına gir.
Anahtarı sohbet mesajına yazma. Repo varsayılanı `hesgeneric/nuevols`,
klasör `configs`. Branch boşsa repo varsayılanı algılanır.

Anahtar yalnızca GitHub API'ye gönderilir; dosyalara veya localStorage'a
kaydedilmez. Yükleme denemesi bittiğinde parola alanı temizlenir. Kod şablonu
ve diğer ayarlar tarayıcıda kaydedilir; hedef listesi ve sonuçlar kaydedilmez.
ChatGPT'nin GitHub bağlantısı bu bağımsız HTML uygulamasına otomatik yetki sağlamaz.

## Çıktı URL şablonu

| Değişken | Değer |
| --- | --- |
| `{{FILE_URL}}` | jsDelivr dosya bağlantısı |
| `{{FILE_URL_ENCODED}}` | URL parametresi için kodlanmış bağlantı |
| `{{FILENAME}}` | Rastgele dosya adı ve uzantısı |
| `{{PATH}}` | Repo içindeki dosya yolu |

Yalnızca dosya bağlantılarını almak için `{{FILE_URL}}` bırak.
Sonuç kutusu ayrıca elle düzenlenebilir. Şablon değişiklikleri sonuçları
anında yeniden üretir; yayımlanan dosyaları değiştirmez.

## Yükleme davranışı

- En fazla 500 adres; boş satırlar atlanır, tekrarlar kaldırılır.
- Dosya adları kriptografik rastgele sayı üreteciyle oluşturulur.
- Mevcut repoda dosyalar tek commit ile eklenir; mevcut ağaç korunur.
- Boş repo ilk dosyayla başlatılır. Kalan dosyalarda hata olursa yalnızca
  yüklenen dosyanın sonucu gösterilir.
- Branch güncellemesi force-push kullanmaz. Branch koruma kuralları geçerlidir.
- Önizleme, dosyanın yüklendiği anlamına gelmez. Yükleme sonrası URL'ler
  commit hash'i içerir. CDN erişimi uygulama tarafından ayrıca doğrulanmaz.
- Ağ kesilirse sunucudaki işlemin tamamlanıp tamamlanmadığı belirsiz kalabilir;
  yeniden denemeden önce repo geçmişini kontrol et.
- Dosyalar herkese açıktır. jsDelivr kalıcı kopya tutabilir; GitHub'dan silmek
  CDN kopyasının kaldırılmasını garanti etmez.

Önceki sürümden kalan `viewer.html` ve `configs/demo.json` bağımsız JSON
örnekleridir. Yeni JavaScript yayınlama akışının çalışması için gerekmez.

## Doğrulama

`node tests/test.cjs` ile bağımlılıksız testleri çalıştırabilirsin.
Kod şablonu değişkenleri, içeriğin çalıştırılmaması, uzantı seçimi, JSON
kontrolü, URL kodlaması, anahtarın saklanmaması, GitHub API akışı ve yarım
kalan yükleme davranışı taklit yanıtlarla test edilir. Test ayrıca geçici
dizinde ZIP üretir. Görsel tarayıcı testi yapılmamıştır.

Repo otomatik GitHub Pages yayını açmaz. Generator doğrudan dosyadan çalışır.
