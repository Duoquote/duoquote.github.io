title: Web Scraping Nasıl Yapılır?
author: Güven Değirmenci
date: 2020-08-09 16:46:42
tags:
---
Web Scraping, her ne kadar basit gibi gözükse de, korkunç birçok tarafı vardır. Birbirinden farklı birçok websitesi, birçok farklı teknoloji var. Bu paylaşımda elimden geldiğince Web Scraping konusunu sizlere aktarmaya çalışacağım.

# Web Scraping nedir?

Bunun zaten ne olduğunu biliyorsanız sizi alt kısımlara alabilirim :). Web Scraping, belirlenen web sayfasından veri almaktır. Tabii ki sadece veriyi almak değil, bu veriyi derlemek, işe yarar hale getirmek de bir parçasıdır.


# Hangi programlama dili kullanılmalı?
Bu konuda seçenekleriniz oldukça geniş. Benim tercihim python dilinden yana. Özellikle `jupyter notebook` kullanarak çok seri bir şekilde yazdığınız kodu test ederek ilerleyebilirsiniz. Python dışında aşağı yukarı her dil ile veri toplayabilirsiniz. `Selenium` diye bir sistem var. Bu sistemin de veri almada kullanılabileceğini düşünürsek alternatif olarak kullanabileceğiniz diller arasında;
- Node.JS
- Java (Selenium)
- JavaScript (Chrome Kullanarak)
- PHP
- C
- C#
- Bash (Evet bash kullanarak bile yapabilirsiniz)

> Bunlar sadece birkaç örnek, mantıken internetten veri alabildiğiniz her dil veya sistem ile web scraping yapabilirsiniz.

# Bir website'den veri almayı deneyelim;

Bu örnek için ben python kullanacağım, sisteminizde python ve pip kurulu olduğunu varsayaraktan konsol açıp `requests` modülünü kurmanız gerekmektedir.
```bash
pip install requests
```

## Veri alma

İlk olarak requests modülünü projemize dahil edelim.

```python
import requests
```

Basit bir `GET` isteği göndermek için `requests` modülünden `get` fonksiyonunu kullanabiliriz. Örnekte ben kendi siteme bir `GET` isteği yolluyorum.

```python
data = requests.get("https://guvendegirmenci.com")
```
Şu ana kadar veriyi aldık iyi güzel de aldığımız veriyi nasıl görüntüleyeceğiz? Aldığımız veriyi `text`, yani yazı formatında görüntüleyebiliriz:

```python
print(data.text)
```
Ya da byte olarak görüntüleyebiliriz:

```python
print(data.content)
```
Ya da aldığınız veri `JSON` formatında bir veri ise python `dictionary` formatında alabilirsiniz. Örnekte kendi websitemde dil değiştirme özelliği için kullandığım JSON verisini kullanıyorum:
```python
data = requests.get("https://guvendegirmenci.com/data.json")

print(data.json())
```

> Dictionary verisini düzenli görüntülemek için `pprint` modülünü kullanabilirsiniz, örnek:
>```python
from pprint import pprint

pprint(data.json())
```

Kodumuzun son hali aşağıdaki gibi bir şey olacaktır:

```python
import requests
from pprint import pprint

data = requests.get("https://guvendegirmenci.com")

# Text verisi görüntüle
print(data.text)

# Byte verisi görüntüle
print(data.content)

# Sitemdeki JSON verisini almak için başka bir GET isteği yolla.
data = requests.get("https://guvendegirmenci.com/data.json")

# JSON verisi görüntüle
pprint(data.json())
```

Programımızın çıktısı aşağıdaki gibi olacaktır, çıktı biraz uzun olduğu için ... ile kısalttım:
```python
<!DOCTYPE html><html lang="TR"><head><title>Güven Değirmenci</title><meta http...
b'<!DOCTYPE html><html lang="TR"><head><title>G\xc3\xbcven De\xc4\x9firmenci</...
{'EN': {'data': {'labels': {'about': 'about',
                            'blog': 'blog',
                            'contact': 'contact',
                            'discord': 'discord',
                            'links': 'links',...
```

## Veri bulma

İyi güzel veriyi aldık da, bu veri karman çorman, burdan bir şekilde veri elde etmemiz lazım. Bunun için kullanabileceğimiz birkaç metod mevcut.

### Metod 1 - BeautifulSoup

En kolay ve belki de en verimli yöntem, `BeautifulSoup` modülünü kullanarak aradığınızı bulmayı kolaylaştırabilirsiniz. Kullanımı eğer programlamaya yeni başlayan biriyseniz zor gelebilir ancak kodlamaya devam edin! Yapmaya çalıştığınız şeye her zaman ulaşmaya çalışın, pes etmeyin! `BeautifulSoup` python ile birlikte gelmemekte, bu yüzden pip ile kurmamız gerekiyor;
```bash
pip install bs4
```

> BeautifulSoup'u kurarken `pip install beautifulsoup` yazarak kurmayınız.

Önceki yaptıklarımızdan biraz referans alarak, birkaç modülü hemen projemize dahil edelim:

```python
import requests
from pprint import pprint
from bs4 import BeautifulSoup
```

Yine basit bir `GET` isteği gönderelim:

```python
data = requests.get("https://guvendegirmenci.com")
```

Şimdi onu `BeautifulSoup`'a aktaralım:

```python
sayfa = BeautifulSoup(data.text, "html")
```

> Dikkat edin, `data` değil de `data.text`'i kullandım, data bir istek objesidir; data.text ise istekte gelmiş olan string verisini içerir.

> `data.text` yerine `data.content` de kullanılabilir. `data.text` string, `data.content` byte olarak veriyi içerir. Beautifulsoup bu iki tipi de kabul eder.


Sayfada arama yapmak için birçok yöntemi kullanabilirsiniz. Kod olarak son halini size örnekleriyle birlikte vereyim;

```python
import requests
from bs4 import BeautifulSoup

data = requests.get("https://guvendegirmenci.com")

sayfa = BeautifulSoup(data.text, "html")

# <title> tagına sahip olan ilk elementini bul.
print(sayfa.find("title"))

# <p> tagına sahip olan ilk elementi bul.
print(sayfa.find("p"))

# <p> tagına sahip olan tüm elementleri bul. (element listesi verir)
print(sayfa.findAll("p"))

# Herhangi bir class'a sahip olmayan <a> elementlerini bul.
print(sayfa.findAll("a", {"class": False}))

# Herhangi bir class'a sahip olan <a> elementlerini bul.
print(sayfa.findAll("a", {"class": True}))

# <li> tagına sahip olup "nav-item" class'ına sahip olan elementleri bul.
print(sayfa.findAll("li", {"class": "nav-item"}))

# <a> tagına sahip olup "nav-link" class'ına sahip olan elementleri bul.
print(sayfa.findAll("a", {"class": "nav-link"}))

# <a> tagına sahip olup "href" değeri "https://stackoverflow.com/story/duoquote"
# olan elementleri bul.
print(sayfa.find("a", {"href": "https://stackoverflow.com/story/duoquote"}))
```
Örnekte yazdığım `find` ve `findAll` metodları gayet klasiktir. Bunların dışında SoupSieve denen library sayesinde BeautifulSoup bizlere javascriptteki `querySelector` komutunda kullandığımız syntax'ı kullanmamızı sağlıyor. Burada detaya pek giremedim, sonuçta kendisi bir web standardıdır. Daha detaylı bilgi için [buraya](https://facelessuser.github.io/soupsieve/) göz atabilirsiniz.

#### Bir adım ileri, RegEx örneği.
Yazılım dünyasında en sevdiğim şey, RegEx. RegEx, uzun adıyla Regular Expression, yazdığınız kurallar ile yazı içerisinde o kurallara uyan kısımları aramanıza yarar. RegEx ile bir element arayacak olsaydık;

```python
import requests, re
from bs4 import BeautifulSoup

data = requests.get("https://guvendegirmenci.com")

sayfa = BeautifulSoup(data.text, "html")

# <a> tagına sahip olup "href" değeri r".*stackoverflow\.com.*" RegEx ile eşleşen
# elementi bul.
print(sayfa.find("a", {"href": re.compile(r".*stackoverflow\.com.*")}))
```
Burada yazdığım RegEx kuralını parçalara ayıralım. Korkutucu gözükebilir ancak temelinde çok basit prensipleri var.

`r".*stackoverflow\.com.*"` ilk olarak tırnak ifadelerini es geçebiliriz, python'da fonksiyona verdiğimiz bir yazı olarak hayal edin bunu, asıl regex değerimiz;

`.*stackoverflow\.com.*`

Şimdi RegEx'te;
`.` (nokta), ___herhangi bir karakter___ anlamına gelmekte.
`*` (yıldız), ___hiç ya da sonsuz sayıda___ demek. Bu ikisi birleşince, `.*` ___herhangi bir karakterden hiç ya da sonsuz sayıda___ gibi bir ifade ortaya çıkıyor. Yani bunu bir yazı ile karşılaştıracak olsaydık, yazı bomboş olsaydı veya sonsuz karakterden oluşsaydı her daim eşleşecekti. Şimdi bunun yanına `stackoverflow.com` eklediğimizde, bu `stackoverflow.com` yazısının başlangıcında sonsuz sayıda karakter olabilir, olmaya da bilir demek. `.*` kısmını sonuna eklediğimizde ise, başında veya sonunda herhangi bir sayıda karakter olabilir olmaya da bilir anlamına geliyor. Yani bu duruma göre, __asd__**_stackoverflow.com_**__zxc__ yazısı da eşlenirdi __stackoverflow.com__ yazısı da.

Daha detaylı olarak incelemek veya kurcalamak isterseniz, bahsettiğim RegEx ifadesini [burada](https://regex101.com/r/xyqUVq/2) inceleyebilirsiniz.

RegEx'in Web Scraping alanındaki değeri çok büyük, istediğiniz her şeyi RegEx ile bulabilmek mümkün, sınırı yok. Biraz idrak etmesi zor bir sistem, ancak bir kere öğrendiğinizde, normal şartlarda ne kadar zor bulunabilecek bir şey varsa, bunu RegEx ile ne kadar kolay bulabileceğinizi farkedebiliyorsunuz.

### Metod 2 - LXML
Bu yine bir python örneği ancak başka bir modül kullanarak, bu modül pek güzel gelmeyebilir ancak performans söz konusu ise oldukça iyidir. LXML'i Windows'ta kurmak sıkıntı yaratabilir bu yüzden tercih etmeyebilirsiniz, benim önerim Windows'ta [Subsystem for Linux](https://www.windowscentral.com/install-windows-subsystem-linux-windows-10) özelliğini açıp Microsoft Mağaza'dan Ubuntu veya Debian kurmanız, kodu orada çalıştırmanızdır.

Requests ve gereken lxml parçasını import edelim;
```python
import requests
from lxml import etree
```

Veriyi alalım;
```python
data = requests.get("https://guvendegirmenci.com")
```

Sayfayı lxml'e aktaralım;
```python
sayfa = etree.HTML(data.content)
```

LXML'i şahsen genel olarak `xpath` denen arama özelliği yüzünden kullanırım, xpath'e alıştıktan sonra BeautifulSoup ile herhangi bir şey aratmak işkence gibi geliyor, xpath BeautifulSoup'a göre biraz karmaşıktır, bir arama dilidir diyebiliriz. Xpath ile alakalı genel örneklere [bu linkten](https://devhints.io/xpath) ulaşabilirsiniz.

Xpath ile sayfada aradıklarınızı kolayca bu şekilde bulabilirsiniz;
```python
# Sayfadaki tüm <div> taglerini bul.
sayfa.xpath("//div")

# Sayfada class değeri "row"'a eşit olan tüm <div> elementlerini bul.
# Dikkat edin "row" yazısı içeren vs. demedim, direk "row" yazan yerler,
# yani "row " veya "row element" gibi değerler ile eşleşmez.
sayfa.xpath("//div[@class='row']")

# Elementlerin "href" veya "class" gibi attribute dediğimiz yerlerdeki
# verileri `.get` metodunu kullanarak istediğimiz değeri alabiliriz.
print(sayfa.xpath("//div[@class='row']").get("class"))

# Sayfada class değeri "row" içeren tüm div elementlerini bul.
sayfa.xpath("//div[contains(@class, 'row')]")

# Class yerine farklı değerler de kullanabiliriz, örneğin;
sayfa.xpath("//a[contains(@href, '#')]")

# Class değeri olarak "post-data" içeren div elementlerini bul,
# bir alt elementi <h1> olan <h1> elementlerini seç.
h1Elementleri = sayfa.xpath("//div[contains(@class, 'post-data')]/h1")

# Üstteki örneğin aynısı ancak farklı bir yaklaşım.
divElementleri = sayfa.xpath("//div[contains(@class, 'post-data')]")

for div in divElems:
    # "./" kullandık, çünkü aslında bu element üstünde xpath fonksiyonunu
    # çalıştırsak bile yazdığımız xpath yönergeleri tüm web sayfası için
    # geçerlidir, şu anda bulunduğu elementin alt elementini aradığımız için
    # "./" kullanarak o elementten devam etmesini sağlayabiliriz.
    div.xpath("./h1")

    # `.text` kullanarak elementtin içine yazılı olan veriyi alabiliriz.
    # Bunu yaparken yine [0] ile bulunan ilk elementi seçmeyi unutmayın.
    print(div.xpath("./h1")[0].text)
```

LXML kısmı biraz kısa olmuş olabilir ancak o konuya tam anlamıyla dalacak olsaydık büyük ihtimalle bu blog postundan daha uzun bir post paylaşmam gerekirdi. Bu tabii ki yapmayacağım demek değil :).
