# AWS VPC (Virtual Private Cloud) Nedir ve Nasıl Çalışır?

AWS VPC, aslında bir nevi kendi özel bulut ağını AWS üzerinde oluşturmanızı sağlayan bir servis. AWS ortamındaki tüm kaynaklar, normalde global olarak herkese açıkken, VPC ile bunları izole edebilir ve sadece belirlediğiniz ağda kullanılmalarını sağlayabilirsiniz. Yani, internetten veya başka ağlardan tamamen yalıtılmış bir ortamda uygulamanızı çalıştırabilirsiniz. Hadi bunu daha detaylı bir şekilde inceleyelim.

## VPC'nin Temel Yapısı

AWS VPC'nin bileşenleri oldukça basit. Bu bileşenler sayesinde, ağınızı istediğiniz şekilde özelleştirebiliyorsunuz. Bu temel bileşenlere gelince:

### 1. **Subnets (Alt Ağlar)**

VPC’nin içinde birkaç farklı alt ağ (subnet) oluşturabilirsiniz. Subnet'ler, VPC’nin içinde daha küçük ağ segmentleri gibi çalışır. Bu sayede, kaynaklarınızı farklı güvenlik seviyelerine sahip ağlarda konumlandırabilirsiniz. VPC'yi kurarken en yaygın kullanılan alt ağ türleri:

- **Private Subnet**: İnternete çıkmak istemediğiniz kaynakları burada tutarsınız. Örneğin, veritabanları veya yalnızca iç hizmetler için ideal.
  
- **Public Subnet**: Bu subnet içinde, internetle direkt bağlantı kurması gereken kaynaklar yer alır. Örneğin, web sunucularınız.

### 2. **Internet Gateway (IGW)**

Eğer VPC'nizdeki bir kaynak (mesela bir EC2 örneği) internete bağlanacaksa, bir **Internet Gateway** (IGW) kullanmanız gerekecek. Bu, ağınızı internetle bağlayan köprüdür. Örnek olarak, public subnet’teki bir EC2 örneği, IGW aracılığıyla dış dünyaya açılır.

### 3. **NAT Gateway (veya NAT Instance)**

Private subnet’teki kaynakların dış dünyaya erişmesi gerekebilir (mesela bir yazılım güncellemesi yapmak için). Ancak, bu kaynakların dışarıya internet erişimi vermek istemediğiniz için, **NAT Gateway** kullanırsınız. NAT Gateway, private subnet’teki kaynakların internete ulaşmasına olanak tanırken, dış dünyadan gelen trafiği engeller.

### 4. **Route Tables (Yönlendirme Tabloları)**

VPC'nizdeki trafiğin nasıl yönlendirileceğini belirten tablolardır. Yani, hangi kaynakların, hangi ağ segmentlerine erişebileceğini bu tablolar ile kontrol edersiniz. Mesela public subnet'teki kaynaklar doğrudan internete gidebilirken, private subnet’teki kaynaklar NAT Gateway üzerinden internete çıkabilir.

### 5. **Security Groups ve NACL'ler (Ağ Erişim Kontrol Listeleri)**

VPC içinde **Security Groups** (SG) ve **NACL**'ler (Network Access Control List) kullanarak kaynaklarınıza gelen ve giden trafiği kontrol edebilirsiniz. En önemli fark, SG'lerin **stateful** (yani bir bağlantı açıldığında, geri gelen trafik de otomatik olarak kabul edilir) olması, NACL’lerin ise **stateless** olmasıdır. Hem SG’ler hem de NACL'ler güvenlik duvarı işlevi görür ve sadece belirli trafiğin geçmesine izin verir.

### 6. **VPC Peering**

Bir VPC'deki kaynaklar, başka bir VPC'deki kaynaklarla iletişim kurmak için **VPC Peering** kullanabilirler. Yani, iki farklı VPC'yi birbiriyle bağlayarak, ağlar arasında güvenli bir iletişim oluşturabilirsiniz. Burada önemli olan, bu bağlantının her iki taraf tarafından kabul edilmesidir.

### 7. **VPN Gateway (VPN GW)**

**VPN Gateway**, şirket içi ağınız ile AWS VPC'niz arasında güvenli bir bağlantı oluşturmanıza olanak tanır. Bu, **site-to-site VPN** olarak bilinen bağlantıyı sağlar. Böylece, kendi veri merkeziniz ile AWS arasında güvenli bir tünel kurarak, iki ağ arasında veri iletişimi gerçekleştirebilirsiniz. VPN Gateway, IPsec VPN tünelleri üzerinden veriyi şifreler, böylece verinizin güvenliğini sağlar.

---

## VPC Best Practices 

AWS VPC’yi kurarken ve kullanırken dikkat etmeniz gereken bazı şeyler:

### 1. **CIDR Bloklarını Dikkatli Seçin**

VPC’yi kurarken kullanacağınız **CIDR blokları** önemlidir. Yani, hangi IP aralıklarını kullanacağınıza karar vermelisiniz. Örneğin, `10.0.0.0/16` gibi geniş bir IP aralığı, subnet'lerinizin büyümesini sağlar. Ancak çok büyük bir aralık seçmek de ilerleyen zamanlarda bazı zorluklara yol açabilir. Bu yüzden ihtiyacınız olan kadar geniş bir aralık seçmek en iyisi.

### 2. **İnternet Erişimini İyi Yönetin**

VPC'nizdeki public subnet’lerdeki kaynakları internete açmak için **IGW** kullanıyorsunuz, ancak private subnet’teki kaynaklar için **NAT Gateway** kullanmanız çok önemli. NAT Gateway olmadan private subnet'lerdeki kaynaklar dış dünyaya erişemez. Bu tür detaylar ağınızı daha güvenli ve verimli hale getirecektir.

### 3. **Güvenlik Gruplarını Özelleştirin**

Güvenlik grupları, VPC'nizin en önemli güvenlik bileşenidir. En iyi uygulama, yalnızca gereken trafiğe izin vererek minimal güvenlik grubu kuralları oluşturmak. Her servis için özel güvenlik grupları oluşturmak, ağınızı daha güvenli tutar. Örneğin, web sunucuları için farklı, veritabanları için farklı güvenlik grupları kullanın.

### 4. **VPC Peering'i Kullanmak**

VPC Peering, iki VPC arasında ağ bağlantısı kurmanıza olanak tanır. Ama burada dikkat edilmesi gereken nokta, peering ilişkilerinin her iki taraf için de geçerli olmasıdır. Ayrıca, VPC peering ilişkisi kurduğunuzda trafiğin yönlendirilmesini doğru yapmanız gerekir.

---

## VPC Nerelerde Kullanılır?

AWS VPC, özellikle aşağıdaki kullanım senaryoları için idealdir:

- **Web Uygulamaları Barındırma**: Web sunucularını ve veritabanlarını VPC içinde farklı subnet’lere yerleştirerek, güvenli ve ölçeklenebilir bir altyapı oluşturabilirsiniz.
- **Private Cloud Yapıları**: Kendi özel bulut altyapınızı oluşturmak isterseniz, AWS VPC bunun için mükemmel bir çözüm sunar.
- **Hybrid Cloud Çözümleri**: Şirket içi veri merkeziniz ile AWS’deki altyapınızı güvenli bir şekilde bağlamak için VPN Gateway kullanabilirsiniz.
- **Yüksek Güvenlik Gereksinimleri**: VPC içindeki her şeyi kontrol edebileceğiniz için, çok sıkı güvenlik önlemleri almak isteyenler için birebir.

---

## Yani

AWS VPC, ihtiyacınıza göre son derece özelleştirilebilen bir ağ altyapısı sunar. Güvenlik, performans ve esneklik açısından güçlü araçlar sunarak, bulut ortamınızda sorunsuz bir ağ yapısı kurmanıza yardımcı olur. Doğru yapılandırmalarla, AWS üzerindeki altyapınız güvenli, yüksek performanslı ve ölçeklenebilir hale gelir.

VPC'yi etkin kullanarak, AWS bulutunu istediğiniz gibi kontrol edebilir ve ağınızı gerektiği gibi izole edebiliriz.

---

**Bu konuyu Özgür Öztürk hocamdan dinledim. Bu süreçte bana katkılarından dolayı kendisine teşekkür ediyorum.**
