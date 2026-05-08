#  Akıllı Asansör Park Stratejisi (Q-Learning ile Stratejik Konumlanma)

##  Projenin Amacı ve Özeti
Genel olarak asansör projeleri sadece yolcu taşımaya odaklanırken, bu projede günün farklı saatlerindeki (sabah girişi,öğle arası ve akşam çıkışı) trafik yoğunluğunu öğrenerek asansörün en uygun katta beklemesi hedeflendi.

**Açıklama:** Çok katlı bir binada asansörün, çağrı gelmediği boş zamanlarda hangi katta beklemesi gerektiğini (park durumu) öğrenmesi hedeflenmiştir. Temel amaç, bir sonraki rastgele çağrıya ulaşım süresini minimize ederek hem enerji hem de zaman tasarrufu sağlamaktır.

##  Pekiştirmeli Öğrenme (RL) Ortam Dinamikleri
Projenin matematiksel modeli bir Markov Karar Süreci (MDP) olarak şu şekilde tanımlanmıştır:

* **State ($s$):** [Günün Saati (Sabah, Öğle, Akşam), Asansörün Mevcut Katı]. Toplam 30 olası durum (3 zaman x 10 kat) mevcuttur.
* **Action ($a$):** [Katta Bekle, Alt Kata İn, Üst Kata Çık].
* **Reward ($r$):** (Yeni Gelen Çağrıya Ulaşma Süresi). Asansör doğru katı tahmin ederse tam isabet ödülü alır, edemezse çağrının geldiği kata olan uzaklığı kadar negatif ceza yer. Her hareket (inme/çıkma) küçük bir enerji maliyetidir.

##  Projenin Mantığı
Bu projede ajana hangi saatte nerede beklemesi gerektiği söylenmemiştir. Ajan, binadaki çağrı istatistiklerini reinforcement learning yoluyla kendisi keşfeder. 
* **Sabahları:** İnsanlar binaya girdiği için çağrıların büyük kısmı zemin kattan gelir.
* **Öğlenleri:** Yemek molası nedeniyle çağrılar binanın her yerine rastgele ve eşit dağılır.
* **Akşamları:** Mesai bitimi olduğu için çağrılar üst katlarda yoğunlaşır.
Q-Learning algoritması, bu gizli örüntüleri (hidden patterns) çözerek her zaman dilimi için en optimum bekleme noktasını otomatik olarak Q-Tablosuna işler.

---

##  Eğitim Süreci ve Öğrenme Eğrisi

![Öğrenme Eğrisi](ogrenme_egrisi.gif?v=1)

### Eğrisinin Yorumlanması:
Yukarıdaki grafik, Q-Learning ajanının 10.000 epoch boyunca aldığı toplam ödüllerin (reward) gelişimini göstermektedir.
* **Başlangıç (Rastgele):** Eğitimin ilk aşamalarında ajan tamamen rastgele (`epsilon = 1.0`) hareket ettiği için sürekli yanlış katlarda bekler ve yüksek cezalar (eksi puanlar) alır.
* **Tırmanış ve Keşif:** Epochlar ilerledikçe ajan hangi saatte hangi katta beklemenin daha mantıklı olduğunu fark eder ve eğri istikrarlı bir şekilde yukarı doğru ivmelenir.
* **Ustalık :** Eğitimin sonlarına doğru ajan sistemi tamamen çözer. Çağrıların nereden geleceğini büyük oranda doğru tahmin edip optimum noktalara park etmeye başlar ve ödül grafiği tepe noktasında stabilize olur.

---

##  Asansör Simülasyonu

![Asansör Simülasyonu](akilli_asansor.gif?v=1)

### Simülasyonun Yorumlanması:
Eğitilmiş model 3 farklı zaman diliminde test edildiğinde, ajanın öğrendiği istatistiksel stratejiler şunlardır:

1. **Sabah Senaryosu:** Ajan güne başlar başlamaz doğrudan zemin kata inerek beklemede kalır. Gelen çağrılar da büyük oranda alt katlardan olduğu için minimum mesafeyle yolculara ulaşır.
2. **Öğle Senaryosu:** Çağrıların hangi kattan geleceğinin tamamen belirsiz olduğu bu saatte, ajan ortalama mesafeyi ve riski minimize etmek için binanın tam ortasına (4. veya 5. Kata) gidip beklemede kalır. Bu durum, ajanınn olasılık dağılımını (probability distribution) iyi öğrendiğinin ispatıdır.
3. **Akşam Senaryosu:** Ajan mesai bitiş saatini algıladığı an, çağrıların üst katlardan geleceğini tahmin ederek derhal yukarı katlara çıkar ve orada beklemeye başlar. Çıkan yolculara anında yanıt verir.

**Sonuç:** Standart asansörler çağrı gelmeden hareket etmezken, geliştirdiğimiz model çağrıları önceden tahmin ederek yolcuları kapıda hazır bekler ve zaman kaybını ortadan kaldırır.
