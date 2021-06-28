# DevOps 101 Training 

## Index
- History 101
- Myths
- Getting the code into the production

## History 101
Tara Harnendez aka Grandma of Continues Integration

Tara 90'larda Netscape Navigator build and relasetakımında çalışıyor. Linux bulunmus ancak henüz yayginlasmis durumda degil. Netscabe browser'ların dağıtımı 2 farklı mac versiyonu (68K ve PPC), iki farklı Windows versiyonu (16 ve 32 bit) ve son olarak 8 farklı Unix versiyonu için hazırlanıyordu. Herhangi bir anda hepsi için başarımı bir build adımı gerçekleştirmeleri neredeyse sıfıra yakın. Ürünler biri iki işletim sisteminde sorunsuz çalışırken diğerlerinde problemler çıkıyor. 

Sonrasında bu durumdan kurtulabilmek için tinderbox'i yazıyorlar. CVS repository'sini dinleyen bir Pearl kodu. Bu sayede her bir comit ile bir change tetikleniyor ve her bir platform icin derleme başlatılıyor. 1990'ların hardware'lerini el verdigince hizli bir sürede başarılı sonuçlar elde etmeye başlamışlar ve ayda bir veya iki temiz derlemeden, her zaman derlenebilir bir kod altyapısını oluşturmayı başarmışlar.

Internet çağı ile birlikte iki senelik acelesiz gelistirme sureleri giderek azalıyordu ve amaç developer'ları verimliliğini olabilidğince arttırmaktı. Netscape boyle bir amaç ile süreçlerini iyileştirmeye çalışan tek şirket de değildi.

Netscap'in yaşağdığı farklı işletim sistemleri için dağıtım problemi Linux'un gelmesi ile birlikte büyük ölçüde çözülmüş oldu. Her ne kadar Linux'un farklı distro'ları da olsa hepsi aynı kernel'i kullanıyor. Bu neden önemli? Çünkü yazılım geliştirme ekipleri zamanlarının büyük bir çoğunluğunu yazılımları farklı platformlarda çalışabilir hale getirmek ile uğraşıyorlardı ancak bu dönüşüm ile birlikte en önemli şey olan yazılımlarına ve ürünlerdeki yeni özelliklere odaklanabilme fırsatı buldular. Bu durum yazılım endüstrisinde inovasyonu arttıran en önemi değişimlerden biridir.

Sonrasında Kohsuke Kawaguchi pearl ile yazilmamis bir urun gelistirdi. Hudson.. Bizler onu Jenkins olarak biliyoruz.

Jenkins'in gelmesiyle birlikte de herkesin tinderbox gibi kendi çözümlerini oluşturmalarına gerek kalmadi. Jenkins'in çok büyük bir community'nin bir parçası ve açık kaynak kodlu olmasının yanı sıra en öne çıkan özelliklerinden birisi plugin ekosistemeidir. Yazılım geliştirme süreçlerinde karışınıza çıkan uygulamalarının hemen hemen hepsi ile kolaylıkla plugin'ler aracılığıyla entegre olabilir. Hızlıca test framework entegrasyonunuzu yapabilir, testlerinizi otomatik koşturabilir, 3rd parti uygulamalar ile entegre olabilirsiniz.

Önceleri tinderbox ile başlayan continues integration yolculuğuna böylece once cont. test sonrasinda cont. delivery eklenmiş oldu.

Şimdilerde ise işletim sistemlerinin ötesinde iş yüklerini buluta taşıyoruz ve bunu - tercihen - konteynerlar aracılığı ile yapıyoruz. Linux'un gelişimi ve sonrasındaki inovasyonun bir diğer ise container dünyası ile birlikte gerçekleşiyor. Konteynerlar sannallaştırmaya kıyasla daha az kaynak tüketen (ligthweight) ve kolay olaşabilir bir tercih olmaya başladı. Konteyner'ların kullanımın artması orkestrasyon anlamında bazı ihtiyçları ortayaçıkarmış oldu. docker-swarm, mesos ve kubernetes. Şimdilerde ise K8 bir standard haline gelmiş durumdadır.

## Myths
### DevOps is Only for Startups
DevOps uygulamaları; Google, Amazon, Netflix ve Etsy gibi Internet'in “unicorn” şirketleri öncülük ederken, bu kuruluşların her biri, tarihlerinin bir noktasında, daha geleneksel organizasyonlar ile ilişkili sorunlar nedeniyle işsiz kalma riskinı yaşadı. felaketle sonuçlanan başarısızlığa meyilli son derece tehlikeli kod sürümleri, rekabeti yenmek için özellikleri yeterince hızlı yayınlayamama, uyumluluk endişeleri, ölçekleme yetersizliği, Geliştirme ve Operasyon ekipleri arasındaki yüksek düzeyde güvensizlik ve benzeri.

Ancak bu kuruluşların her biri, DevOps ile ilişkilendirdiğimiz harika sonuçları yaratmak için mimarilerini, teknik uygulamalarını ve kültürlerini dönüştürmeyi başardı.

### DevOps replaces Agile
DevOps ilkeleri ve uygulamaları Agile methodoloji ile uyumludur. DevOps'un 2001'de başlayan Agile yolculuğunun mantıklı bir devamı olduğunu gözlemlenebilir. Agile methodoloji, müşterilerine sürekli olarak yüksek kaliteli kod sunan küçük ekiplere odaklanması nedeniyle DevOps'un etkin bir etkinleştiricisi olarak hizmet eder.

Her itersyon sonunda çalışmamızı "potansiyel olarak gönderilebilir kod" hedefinin ötesinde yönetmeye devam edersek birçok DevOps uygulaması ortaya çıkar. Bunu kodun sürekli yaygınlatırılabilir durumda olması, developer'ların günlük olarak kod değişikliği göndermesi ve yeni özellikleri prod benzeri ortamlarda gösterebilmeyi eklenebilir

### DevOps is Incompatible with Information Security and Compliance
Geleneksel kontrollerin olmaması (ör. görev ayrımı, değişiklik onay süreçleri, proje sonunda manuel güvenlik incelemeleri) bilgi güvenliği ve uyum uzmanlarını korkutabilir. Ancak bu, DevOps kuruluşlarının etkili kontrollere sahip olmadığı anlamına gelmez. Güvenlik ve uyumluluk faaliyetleri yalnızca proje sonunda gerçekleştirilmek yerine, kontroller yazılım geliştirme yaşam döngüsündeki günlük çalışmanın her aşamasına entegre edilerek daha iyi kalite, güvenlik ve uyumluluk sonuçları elde edilir.

### DevOps Means Eliminating IT Operations, or “NoOps
Birçoğu DevOps'u IT Operasyonları işlevinin tamamen ortadan kaldırılması olarak yanlış yorumluyor. Ancak, bu nadiren olur. IT Operasyonlarının doğası değişebilse de, her zamanki gibi önemini koruyor. IT Operasyonları, yazılım yaşam döngüsünün başlarında geliştirici ekipler ile çalışmaya başlar ve bu etkileşim uygulama canlıya alındıktan çok sonrasında da deveam eder.

IT Operasyonları gelen istek ve çağrılar için manuel çalışma yerine, ortamlar oluşturan, kodu test eden ve dağıtan, üretim telemetrisini izleyen ve görüntüleyen API'ler ve self servis platformlar aracılığıyla geliştirici üretkenliği sağlar. Bunu yaparak, IT Operasyonları, ürünün geliştiricilerin IT hizmetlerini üretimde güvenli, hızlı ve güvenli bir şekilde test etmek, dağıtmak ve çalıştırmak için kullandıkları platform ve altyapıları geliştiren ekiplere dönüşür

### DevOps is Just “Infrastructure as Code” or Automation
Bu eğitimde gösterilen DevOps kalıplarının çoğu otomasyon gerektirirken, DevOps ayrıca kültürel normlar ve BT değer akışı boyunca paylaşılan hedeflere ulaşılmasına izin veren bir mimari gerektirir. Bu sadece otomasyonun çok ötesine geçer. Bir teknoloji yöneticisi ve DevOps'un en eski tarihçilerinden biri olan Christopher Little'ın yazdığı gibi, "DevOps otomasyonla ilgili değildir, tıpkı astronominin teleskoplarla ilgili olmadığı gibi."

## Getting the code into the production
Müşterilerimizin/ Patronların iş tanımımızda temelde bizden yapmamızı beklediği tek bir şey var ve organizasyonun neresinde çalıştığınızın (operasyon veya development) bir önemi yoktur. Misyonumuz, kodu üretime almaktır. Operasyon ekipleri bunun onların işi olmadığını düşünebilir. Şöyle bir senaryoda tahmin edin ne olur; eğer kod üretime geçmezse şu anda sattığımız her ne ise onu satamayız ya da bir sosyal medya şirketiysek insanların kimliğini çalamayız :) Bu nedenle kodun üretime alınması çok önemlidir. İşe yarayabileceğimiz en önemli şey bu.

### Consistency
Kodu üretime alabilmek için deployment yapmamız gerekiyor. Ve ben **tutarlı** olarak deploy yapabilmek istiyorum istiyorum. Deployment için ne gerekiyorsa onu yapmak istiyorum, yazılımın gerçekten deploy olması gereken yere gitmesini ve bunu tekrar ve tekrar yapabilmek istiyorum.

### Reliability
Ve sonra bu süreçte **güvenilir** olmak istiyorum. Yazılımın güvenilir bir kopyasının rastgele bir sunucuya gitmesini ve orada biri bir dosyayı değiştirdiğinde yazlımım artık çalışmıyor, bunun olmasını istemiyorum. Dağıtım yaptığımız yerin aslında oldukça dayanıklı olduğundan emin olmak istiyorum.

### Confidence
Ve son olarak, yaptığımız her şeyin işe yaradığına dair **güven** istiyorum çünkü eğer emin değilsek onu değiştireceğiz, bu da muhtemelen güvenilir olmayacağı ve güvenilir değilse tutarlı olmayacağımız anlamına geliyor.

### The Framework
Yazılım dünyasında yaşanan en büyük problemlerden birsi tarikat gibi davranıyor olmamız. Lider firmalar ne yapıyorsa sen de onu yap.
Örneğin, google bir fikir ortaya atıyor ve biz de bir gün google kadar başarılı ve büyük olmak istediğimiz için google ne yapıyorsa onu yapmaya çalışıyoruz.
izleme, monitoring, big data... Google'da çok zeki insanların çalıştığını düşünürsek neden onların yaptığını kopyalamıyoruz. Netflix gibi distributed olma, amazon gibi deploy yapma vb.

- Deployment'ı nelerin oluşturduğunu iyi anlayın? Hangi bileşenleri, teknolojileri deploy ediyoruz? Siebel? Pega? JEE war paketleri?
- Nereye deploy ettiğinizi iyi anlayın. On-prem bir çözüm üzerine mi çalışıyor oraya deploy edin, cloud'da mı çalışııyor oraya deploy edin. Ancak deploy ederken neler yapmanız gerekiyor bunun doğru ve iyi anlaşılması önemli çünkü weblogic üzreine deploy etmek için geçerli operasyonel gereksinimler Google Cloud GKE ile veya Azure'daki bir serviste farklılaşabilir
- Ne zaman ve ne sıklıkla deployment yapmanız gerekiğini anlayın. HelloWorld diye bir şirket olduğunu ve hello-world app'ini yazdıklarını var sayalı. Sadece ekrana hello world yazan bir uygulama. Bunun günde kaç kere deploy edilmesi gerekir :) Başka bir örneği alalım. Bakanlıklardan biri için bir yazılım geliştirdiğimizi düşünelim. Uygulamalarımızı regülasyonlar değiştiğinde değiştirebiliyoruz ve onun dışında pek dokunamıyoruz. Çünkü çok kritik destek hizmeti sağladığı için herhangi bir downtime yaşanmaması gerekiyor. 
- Anlaşılması gereken bir diğer konu ise neden deployment yapıyoruz? Her küçük değişikliğin hemen canlıya alınması gerekir mi? Toplu olarak yapamaz mıyız? 
- Framework'teki son parça ise yapılan devreye alımın doğru çalıştığını nasıl bileceğiz? Bazılarınız gözlerimizi kapatıp çalışmasını umuyor olabiliriz - Siebel ekibi mesela :D -. Twitter'dan veya ekşi sözlükten müşteri şikayeti var mı yok mu kontrol edebiliriz. Şaka bir yana uygulamanın başarılı ayağa kalkmasının yanı sıra belli başlı senaryolar kontrol ediliyor olabilir ancak genelde sabah sahadan/ son kullanıcıdan gelen şikayetler ile gerçekten bir problem olup olmadığını görüyoruz. Bunu değiştiriyor olmamız gerekli. Problem anında başkaları söylemeden bizim bir problem olup olmadığını bilebilecek yetkinlikte olmamız gerekiyor.
