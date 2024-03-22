# Flutter Whisper.cpp

Flutter Whisper.cpp, [OpenAI'nin Whisper](https://openai.com/research/whisper) ASR modelini kullanarak çevrimdışı/cihaz üzerinde hızlı ve doğru otomatik konuşma tanıma (ASR) sağlayan bir uygulamadır. Bu uygulama, [ggerganov'un Whisper.cpp](https://github.com/ggerganov/whisper.cpp) üzerine kurulmuş olup, Flutter ile Rust'ı FFI aracılığıyla bağlamak için [flutter_rust_bridge](https://github.com/fzyzcjy/flutter_rust_bridge) ve Rust C bağlarını Whisper.cpp'ye bağlamak için [whisper-rs](https://github.com/tazz4843/whisper-rs) kullanmaktadır. Ayrıca, iOS'ta .m4a kaydetmek için [Record](https://github.com/llfbandit/record) Dart kütüphanesinden yararlanılmaktadır.

[Flutter ile Rust Kullanarak Uygulamalarınızı Nasıl Güçlendirebileceğinize Dair Konuşmamı Fluttercon 2023'te İzleyin](https://www.droidcon.com/2023/08/07/supercharging-your-flutter-apps-with-rust/)

Flutter sürümü 3.16.0 ve Rust sürümü 1.74.0 ile test edilmiştir.

### Çalıştığı Platformlar
- [x] iOS (17.0.3 ve XCode 15.0)
- [ ] Android
- [x] Mac OS (Sonoma 14.0 ve XCode 15.0)
- [ ] Linux
- [ ] Windows

## Video
Aşağıdaki örnek, bir iPhone 12'de sesi işlemek için < 1 saniye sürdü.

![Demo Video](https://user-images.githubusercontent.com/20296911/229925629-9f4e9fa0-6165-4d96-b61b-a04f8105a1f6.MOV)

## Amaç ve Faydalar
Bu uygulamanın amacı, Flutter'da OpenAI'nin Whisper ASR modelini kullanarak otomatik konuşma tanıma gerçekleştirmek için bir örnek sağlamaktır. Bu yaklaşımın faydaları şunlardır:

- Whisper ASR modelinin yüksek performanslı çıkarımı.
- Konuşmayı kaydetme ve dönüştürme için kullanıcı dostu arayüz.

## Kurulum
Yüklenmesi gerekenler:
- Flutter
- Rust

### iOS için Kurulum Adımları
Uygulama şimdilik sadece iOS ile kurulmuş durumdadır ve iPhone 12 ile 2022 M1 çipi olan bir iPad Air'de test edilmiştir. Kurulum için aşağıdaki adımları izleyin:

> **Not:** ffmpeg kütüphanesi kullanıldığından şu anda simülatörde çalışmıyor. Bu yüzden gerçek bir iOS cihazı kullanmanız önerilir.

#### Modeli Değiştirmeden Çalıştırma
1. Depoyu yerel makinenize klonlayın.
2. Gerekli bağımlılıkları ve kütüphaneleri kurun, yani ana dizinde `flutter pub get` ve `./rs_whisper_gpt` dizininde `cargo build` çalıştırın.
3. Modelin `/rs_whisper_gpt/ggml-base.en.bin` konumuna eklenmiş olduğundan emin olun. [Modelleri Yükleme/İndirme için Whisper.cpp'ye Bakın](https://github.com/ggerganov/whisper.cpp/tree/master/models#readme).
4. `flutter run -d {cihaz}` komutunu çalıştırın.


### Modeli Değiştirme
1. Yeni bir model indirin (örneğin ggml-tiny.en.bin) [Modelleri Yüklemek/İndirmek için Whisper.cpp'yi Görün](https://github.com/ggerganov/whisper.cpp/tree/master/models#readme)
2. Bu dosyaya XCode'da bir referans ekleyin, Runner/Runner dizininde olduğundan emin olun (Rust kodunda arama için önemli, ya da Rust kodundaki yolu bu dosyayı referans gösterecek şekilde değiştirin)
3. `./rs_whisper_gpt/src/api.rs` içindeki Rust kodunu, modelin adını referans alacak şekilde güncelleyin
4. Aşağıda açıklandığı gibi yeni bağlantıları oluşturmak üzere flutter_rust_bridge_codegen komutunu çalıştırın.

### Dil Değiştirme

Modelin dilini değiştirebilirsiniz, yapmanız gereken tek şey, api çağrısında dili, kullanmak istediğiniz dilin koduyla aynı olarak ayarlamaktır. Ayrıca, doğru modeli kullandığınızdan emin olmanız gerekir, yani ggml-tiny.bin yerine ggml-base.en.bin. Daha fazla detay için [OpenAI Whisper](https://github.com/openai/whisper#available-models-and-languages) sayfasına bakın.

## Flutter Rust Bridge ile Rust Bağlantılarını Düzenleme

Aşağıdaki komutu çalıştırmanız gerekecek
flutter_rust_bridge_codegen --rust-input rs_whisper_gpt/src/api.rs --dart-output lib/bridge_generated.dart -c ios/Runner/bridge_generated.h -e macos/Runner/

less
Copy code
Daha fazla bilgi için [flutter_rust_bridge Kullanıcı Kılavuzuna](https://cjycode.com/flutter_rust_bridge/) göz atın

## iOS'ta Optimizasyonlar

Whisper.cpp'deki diğer örneklerde olduğu gibi, uygulama -O3 -DNDEBUG ekleyerek Diğer C Bayraklarına performans için optimize edilmiştir. Ancak, üretim veya gerçek dünya senaryoları için önerilmez.
