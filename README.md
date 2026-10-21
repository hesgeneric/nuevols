# Nuevols URL Publisher

Hedef URL'lerden rastgele isimli JSON dosyaları üretir, GitHub'a yükler ve
commit sürümüne sabitlenmiş jsDelivr bağlantıları verir.

## Kullanım

1. `nuevols-generator.html` dosyasını indir ve tarayıcıda aç.
2. Hedef URL'leri satır satır gir, **Dosyaları hazırla** düğmesine bas.
3. JSON paketini indirerek dosyaları yüklemeden inceleyebilirsin.
4. Yükleme için yalnızca bu repo üzerinde `Contents: Read and write` yetkili
   bir [fine-grained GitHub anahtarı](https://github.com/settings/personal-access-tokens/new)
   oluştur ve araçtaki parola alanına gir. Anahtarı sohbet mesajına yazma.
5. **Bağlantıyı kontrol et**, ardından **GitHub'a yükle** düğmesine bas.
6. Sonuçları kopyala veya TXT olarak indir.

Araçtaki repo varsayılanı `hesgeneric/nuevols`, klasör `configs`.
Branch boşsa repo varsayılanı algılanır. Anahtar yalnızca GitHub API'ye
gönderilir; dosyalara veya localStorage'a kaydedilmez. Yükleme denemesi
bittiğinde parola alanı temizlenir. ChatGPT'nin GitHub bağlantısı bu
bağımsız HTML uygulamasına otomatik yetki sağlamaz.

## Çıktı şablonu

Sadece dosya URL'si:

```text
{{FILE_URL}}
```

Kendi sitene yükleyeceğin görüntüleyici için:

```text
https://viewer.example.com/viewer.html?config={{FILE_URL_ENCODED}}
```

`{{FILENAME}}` ve `{{PATH}}` değişkenleri de kullanılabilir. Şablon sonuçlara
anında uygulanır; sonuç kutusu ayrıca elle düzenlenebilir. Bu değişiklikler
yayımlanan dosyaları değiştirmez. Yeniden oluşturma elle düzenlemeyi siler.

## Görüntüleyici

`viewer.html` dosyasını kendi HTTPS sitene yükle. JSON'daki hedef domaini
ve kullanıcı tıklamasıyla açılan bir bağlantı gösterir. `configs/demo.json`
example.com'a giden zararsız bir örnek yapılandırmadır.

```json
{
  "version": 1,
  "targetUrl": "https://example.com/",
  "label": "Continue"
}
```

Bu uygulama kendi veri şemasını kullanır. KRPano/Pannellum enjeksiyon
şablonunun kopyası değildir; olay işleyicisi veya üçüncü taraf sayfa
içeriğini değiştiren kod üretmez. JSON URL'sini `script src` olarak kullanma.

## Yükleme davranışı

- En fazla 500 adres; boş satırlar atlanır, tekrarlar kaldırılır.
- Rastgele dosya adları tarayıcının kriptografik rastgele sayı üreteciyle oluşturulur.
- Mevcut repoda dosyalar tek commit ile eklenir; mevcut ağaç korunur.
- Boş repo ilk dosyayla başlatılır. Kalan dosyalar hata verirse yalnızca
  yüklenen dosyanın sonucu gösterilir.
- Branch güncellemesi force-push kullanmaz. Korunan branch kuralları geçerlidir.
- Önizleme, dosyanın yüklendiği anlamına gelmez. Yükleme sonrası URL'ler
  commit hash'i içerir. CDN erişimi uygulama tarafından ayrıca doğrulanmaz.
- Ağ kesilirse sunucudaki işlemin tamamlanıp tamamlanmadığı belirsiz kalabilir;
  yeniden denemeden önce repo geçmişini kontrol et.
- Dosyalar herkese açıktır. jsDelivr kalıcı kopya tutabilir; GitHub'dan silmek
  CDN kopyasının kaldırılmasını garanti etmez.

## Doğrulama

`node tests/test.cjs` ile bağımlılıksız davranış testlerini çalıştırabilirsin.
JSON üretimi, şablon kodlaması, anahtarın kaydedilmemesi, GitHub API akışı,
başarısız/yarım yükleme ve görüntüleyici davranışı taklit yanıtlarla test edilir.
Test ayrıca geçici dizinde ZIP üretir. Görsel tarayıcı testi yapılmamıştır.

Bu repo otomatik olarak GitHub Pages yayını açmaz. Yerel generator doğrudan
dosyadan çalışır; görüntüleyici için sana ait bir HTTPS yayın adresi gerekir.
