##### Alisa Ahokas

# Django

Seminaarityön aiheena oli tutustua Django-kehysjärjestelmään eli tarkoituksena ei ole luoda maailmaa järisyttävää monimutkaista koodia vaan perehtyä Djangoon ja ymmärtää paremmin sen toimintaa. Django on ilmainen avoimen lähdekoodin Python-pohjainen verkkokehys, joka noudattaa model-template-view -arkkitehtuurimallia. Django luotiin jo vuonna 2003 ja julkaistiin käyttöön vuonna 2005. Django on saanut nimensä kitaristi Django Reinhardtin mukaan.

### Setup ja Djangon asennus

Projektin tekemiseen käytetään VS Codea. VS Code -ohjelmassa tarvitset sekä Python- että Jinja-laajennuksen. Djangon setupia varten tarvitaan myös asennettuna Python3 ja pip.
Versiot voi tarkistaa helposti PowerShellissa: python --version ja pip --version.
Pythonin version tulisi olla python 3.XX ja pipin version pip 19.XX.
Ennen Djangon asentamista luodaan virtuaaliympäristö. Näin projekti on käärittynä omassa ympäristössään helpottaen muiden Django-projektien tekemistä ja konffaamista. Seuraava komento luo kyseisen ”wrapperin” ei virtuaaliympäristöä.

    pip install virtualenvwrapper-win

Seuraavaksi luodaan virtuaaliympäristö:

    mkvirtualenv testenv

Virtuaaliympäristö käynnistyy automaattisesti ja Django voidaan asentaa komennolla.

    pip install django

Tarkista asennuksen jälkeen versio komennolla `django-admin --version`.

Seuraavaksi luodaan hakemisto projektille ` mkdir projects`. Siirrytään hakemistoon komennolla `cd projects`.
Projektien hakemistossa luodaan Django-projekti:

    django-admin startproject techproject

Startproject sanan jälkeen annetaan projektille sen nimi. Tällä tavalla luodaan kaikki pienemmätkin sovellukset, joista yksi iso projekti koostuu. Siirrytään projektin hakemistoon tarkastelemaan, mitä tiedostoja sieltä löytyy.


![calc_app_dir](https://user-images.githubusercontent.com/112398757/236718878-a3af00f4-f100-4ad9-afa8-9f7ce10a4037.JPG)



Django-sovelluksen mukana tulee aina useampi tiedosto ja yksi erittäin tärkeä on manage.py. Sen avulla sovellus aina pyöräytetään käyntiin ja on projektin hallintaa varten. Settings.py-tiedostossa on sovelluksen sisäisiin asetuksiin liittyviä tärkeitä kohtia.

Middleware pureutuu tietoturvaan ja installed_apps listassa tulee olla listattuna projektiin tehdyt sovellukset, jotta ne pyörivät. Secret_key on käyttöönottoa varten. Debugin oletusarvona on ”True” ja se tulee vaihtaa arvoon ”False” tuotantoasennuksen yhteydessä.

Urls.py -tiedostoon lisätään polut, jotta navigointi onnistuu selaimessa ja sovelluksen sisällä eli se käsittelee URL-polkuja. Wsgi.py on käyttöönottoa varten. Django tarjoaa oletuksena sqlite-tietokannan.

Projektin ollessa luotu virtuaaliympäristössä voidaan se käynnistää komennolla:

    python manage.py runserver

Seuraava ilmoitus näkyy käynnistyksen jälkeen: "starting development server at http://127.0.0.1:8000/" Projektin IP-osoite on 127.0.0.1 eli localhost ja portti 8000.

Painamalla linkkiä päätyy selaimessa osoitteeseen 127.0.0.1:8000 ja näet Djangon aloitussivun.


![start_page_default](https://user-images.githubusercontent.com/112398757/236722371-c2be29d3-1da7-4d36-9aff-2a56dbc8a4d7.JPG)


Virtuaaliympäristöön pääsee aina komennolla `workon testenv`, jos se deaktivoituu eli `workon <envin nimi>`


### Calculator-sovellus

Seuraavaksi luodaan uusi sovellus. Sovellukset ovat kuin pieniä moduuleja Django-projektin sisällä. Jokainen luotu sovellus on omaan tarkoitukseensa luotu. Projekti on verkkosivusto, joka sisältää pieniä sovelluksia, kuten sisäänkirjautumisen tai laskimen tai erilaisia kohteita. Kyseinen rakenne helpottaa verkkosivun ylläpitoa ja päivittämistä.

Luodaan uusi sovellus virtuaaliympäristössä komennolla `python manage.py startapp calculator`.

Claculator-sovelluksessa on models.py ja tests.py (testejä varten), ja views.py käsittelee pyyntöjä. Tehdään tiedosto nimeltä urls.py myös uuteen sovellukseen, joka on sisällöltään samanlainen kuin ensimmäisen projektin urls.py-tiedosto.

```
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='home')
]
```

Views.py-tiedostoon luodaan funktio home(), jota kutsutaan calculator-sovelluksen urls.py-tiedoston polussa path('', ...).

```
from django.http import HttpResponse

def home(request):
    return HttpResponse("<h1>Hello user!</h1>");
```

Siirrytään projektin urls.py-tiedostoon ja lisätään tämä calculator-sovelluksen urls.py urlpatterns:iin.

```
from django.urls import path, include

urlpatterns=[
    path('', include('calculator.urls')),
    path('admin/', admin.site.urls),
]
```

Useamman urls.py-tiedoston käyttäminen helpottaa ison sivuston hallintaa, koska se pilkkoo url-polkuja pienemmiksi hallittavammiksi kokonaisuuksiksi, jotka sisällytetään ”pää” urls.py-tiedostoon.

Staattisen HTML-sivun pohjana voidaan käyttää template-pohjaa. Projektista löytyy settings.py-tiedosto, jossa on templates-osa, joka hallinnoi projektissa käytettäviä templateja. Määritetään templates-osaan polku 'DIRS': [os.path.join(BASE_DIR, 'templates')] eli määritetään perushakemisto ja kansio, jossa on kaikki projektissa käytettävät ja sinne luotavat HTML-templatet.
Tehdään uusi pohjasivu (base.html) templates-kansioon Jinjan avulla. Tämä on yhteinen pohja kaikille html-sivuille calculator-sovelluksessa. 

```
<body bgcolor="alisa">
  {% block content %}
  {% endblock %}
</body>
```

Nyt meillä on lohko, johon muista HTML-sivuista tuleva sisältö sijoitetaan.

Seuraavaksi muutetaan views.py-tiedoston HttpResponse palauttamaan HTML-sivun.

```
def home(request):
    return render(request, 'home.html')
```

Tehdään HTML-sivu dynaamiseksi muuttamalla views.py-tiedostoa. Sijoitetaan renderöitävän HTML-sivun jälkeen sanakirja, jolle annetaan avaimeksi ”name” ja arvoksi ”Alisa”. Näin välittyy käyttäjän nimi sivulle pelkän sanan ”user” sijaan.

```
def home(request):
  return render(request, 'home.html', {'name': 'Alisa'})
```

Home.html-tiedostossa muutetaan sana ”user” muotoon {{name}}, jotta sanakirjasta saadaan sen arvo ja sivusta tulee dynaamisempi.

```
<h1>Hello {{name}}</h1>
```

Home.html-tiedostossa pitää muistaa lisätä {% extends 'base.html' %} eli tieto siitä, että se käyttää base.html-tiedostoa.

Tehdään sivu nimeltä result.html, joka renderöi laskimen tuloksen. Luodaan se templates-kansioon.

Käytä result.html-tiedostossa base.html-tiedostoa samalla tavalla kuin home.html-tiedostossa.

```
{% extends 'base.html' %}

{% block content %}

  Result: {{result}}

{% endblock %}
```

Lisätään polku "add" calculator-sovelluksen urls.py-tiedostoon.

```
urlpatterns = [
  path('',views.home, name='home'),
  path('add',views.add, name='add')
]
```

Nyt täytyy lisätä funktio views.py-tiedostoon, jotta laskin toimii eli syötetyt arvot lasketaan yhteen ja palautetaan renderöity result.html-sivu.

```
def add(request):
  value1 = int(request.POST['number1'])
  value2 = int(request.POST['number2'])
  res = value1 + value2

return render(request, "result.html",{'result': res})
```

![form_method_post_adding_csrfToken](https://user-images.githubusercontent.com/112398757/236722566-85ceb928-c2ed-4f60-acd3-9142b98c7bc3.JPG)


Djangon middlewareen kuuluu esimerkiksi CsrfViewMiddleware, jota voidaan käyttää käyttäjälle turvallisemman koodin luomiseksi. Tämä yhdessä post-menetelmän kanssa tarkoittaa, että käyttäjä ei lähetä erilaisia tietoja urlissa.


![settings_middleware_csrfView](https://user-images.githubusercontent.com/112398757/236722431-f8614f77-084c-4938-8f7f-280c4adf0f50.JPG)



MVT = Model View Template. Model linkittyy dataan, Template on normaalia HTML:ää, CSS:ää, JS:ää, DTL (Django Template Language) ja View yhdistää kaksi edellä mainittua kommunikoiden samalla urls.py-tiedostojen kanssa. View ilmentää liiketoimintamallia.
Springin MVC -mallissa eli model, view, controller, missä controller vastaa Djangon view’tä.


![django](https://user-images.githubusercontent.com/112398757/236718504-53aac8ce-ab57-4979-befb-533808847db9.JPG)



### Travello-sovellus

Tehdään seuraava sovellus, kuten aikaisemminkin ja käytetään siinä tällä kertaa valmista mallipohjaa.
Travello-sovellukseen tehdään assets-kansio, johon Django kerää kaikki staattiset tiedostot komennolla `python manage.py collectstatic`. Tämä auttaa erilaisten tyylikirjastojen ja kuvien käyttämisessä.

Seuraavaksi ladataan tietokanta PostgreSQL ja tietokannan hallintapaneeli pgAdmin4.
Muutoksia settings.py-tiedostoon, jotta voidaan yhdistää projekti ladattuun tietokantaan. Seuraavaksi tulee vielä asentaa psycopg2 `pip install psycopg2`. Ajuri auttaa PostgreSQL-tietokantaa toimimaan yhdessä Pythonin kanssa.

Djangon dokumentaatiosta tulee selvittää oikeat tyypit, jotta models.py-tiedostoon tehdystä luokasta saadaan luotua toimiva tietokantataulu tietokantaan. Settings.py- ja models.py-tiedoston ollessa kunnossa ajetaan komento `python manage.py makemigrations`. Tämä luo migraation.

Ennen makemigrations-komentoa tarkista sovelluksen nimi sen apps.py -tiedostosta ja lisää se settings.py -tiedostoon (INSTALLED_APPS).

Seuraavaksi sinun täytyy asentaa Pillow käyttäen komentoa `pip install Pillow`. Kuvien käyttämiseen ja lataamiseen Djangolla tarvitaan tämä kyseinen kirjasto.

Ajetaan komento:
    python manage.py makemigrations

Nyt olemme luoneet migrations-kansioon tiedoston, mutta itse tietokantataulua ei vielä ole.

Suoritetaan komento `python manage.py sqlmigrate travello 0001`, jossa travello on sovelluksen nimi ja 0001 on migraatiotiedoston nimi/numero. Nyt SQL-koodi on suoritettu ja ajetaan vielä komento `python manage.py migrate`, jotta saadaan synkronoitua luotu taulu tietokantaan.

Tarkistetaan tilanne pgAdmin4:lla. Se näyttää luodun Destination-taulun ja kyseisen taulun omaavan vaadittavat sarakkeet, jotka täsmäävät destination-luokalla. ORM:n (Object Relational Mapper) avulla saimme luokasta tehtyä tietokantataulun.


### Admin-paneeli

Admin-paneeliin pääset osoitteessa localhost:8000/admin, jonka Django tarjoaa automaattisesti. Ensin luodaan superuser admin-paneelia varten komennolla:
    python manage.py createsuperuser

Anna käyttäjänimi, sähköposti ja salasana superuserille. Selaimessa kohteiden tietojen lisääminen onnistuu, kun travello-sovelluksen damin.py-tiedostoon tehdään pieni muutos. Selaimessa matkakohteiden tietojen lisääminen onnistuu nyt ongelmitta ja siinä kysytään samat tiedot kuin tietokantaan on asetettu. Kuvat lisätään siis käyttäjien toimesta ja settings.py-tiedostoon tulee tehdä taas muutoksia, 

Nyt voimme lisätä kohteita, joten admin.py-tiedostoon täytyy lisätä tarvittavat tiedot. Selaimessa voit lisätä kohteita antamalla vastaavat tiedot kuin luokassa. Kuvat lisätään käyttäjien toimesta, joten joudut tekemään muutoksia settings.py-tiedostoon ja luodaan uusi mediakansion, josta lopulta kuvat haetaan.
Selaimen kautta lisätyt kohteet näkyvät myös tietokannassa, jota voi tarkastella pgAdmin4 avulla.

> Kohteen lisäys selaimessa

![Adding_destination_all_fields](https://user-images.githubusercontent.com/112398757/236721246-af5a6d4c-8b4f-4af0-a3ca-ae479ca22416.JPG)


> pgAdmin

![added_destinations_pgAdmin4](https://user-images.githubusercontent.com/112398757/236721321-72f7d13b-ea2b-448d-911b-3ab099f0ae67.JPG)

> Lisätyt kohteet selaimessa

![destinations_wPictures_browser](https://user-images.githubusercontent.com/112398757/236721621-a3762e01-5f6b-49b7-a490-cb91b0e8d81e.JPG)


### Accounts-sovellus

Seuraavaksi luodaan accounts-sovellus, jolla hallitaan rekisteröitymistä, kirjautumista ja uloskirjautumista. Urls.py-tiedostoon accounts-sovelluksessa täytyy luoda polut, ja urls.py (techproject)-tiedostoon täytyy sisällyttää accounts-sovelluksen urls-tiedosto. Muutetaan index.html-tiedostossa valikon sisältöä, jotta siinä on rekisteröityminen ja kirjautuminen. Molemmat löytyvät accounts-sovelluksen alla. Uudet käyttäjät rekisteröityvät lomakkeen kautta ja näin ne lisätään myös tietokantaan.


### Lopuksi

Django tarjoaa huomattavan paljon ominaisuuksia, joilla on mahdollista tehdä kohtuullisen helpostikin isoja websovelluksia. Tätä helpottaa projektin sisällä olevat pienet moduulit eli sovellukset, joista muodostuu lopulta yksi iso projekti, mutta hallitun kokoisissa palasissa. Pienet palaset helpottavat laajentamista sekä ylläpitoa huomattavasti. Suurin muistaminen on ehdottomasti siinä, että uusia url-polkuja ei saa unohtaa sisällyttää ”päätiedostoon” sekä laittaa polut oikein sovelluksen omaan urls.py-tiedostoon.

Admin-paneeli tarjoaa paljon muitakin toimintoja, jotka eivät tässä ilmenneet. Aikaisemmin olin käyttänyt kotitehtävässä sqliteä, joka tulee Djangon mukana automaattisesti, joten oli mielekästä kokeilla PostgreSQL:n käyttöä ja sen yhdistämistä Django-projektiin.

Mutkia toki oli matkassa, mutta onneksi Stack Overflow auttoi, kun hätä oli suurin. Importit olivat kunnossa ja asennukset, mutta pitikin osata laittaa VS Coden alalaidasta virtualenvironment käyttöön. Tämän keksimisessä meni kyllä pieni hetki.


![vsCode_in_venv](https://user-images.githubusercontent.com/112398757/236719484-3be45344-3eb5-4aba-a7ba-51f00ae76a30.JPG)


### [Video](https://haagahelia-my.sharepoint.com/:v:/r/personal/bgu159_myy_haaga-helia_fi/Documents/Recording-20230508_003808.webm?csf=1&web=1&e=hCssbO)

### Lähteet

- [Django](https://www.djangoproject.com/). Luettu 05.05.2023.
- [Mosh Hamedani - Python Django Tutorial for Beginners ](https://www.youtube.com/watch?v=rHux0gMZ3Eg). Katsottu 05.05.2023.
- [Navin Reddy - Django Tutorial for Beginners](https://www.youtube.com/watch?v=OTmQOjsl0eg). Katsottu 06.05.2023.
- [Wikipedia - Django (web framework)](https://en.wikipedia.org/wiki/Django_(web_framework)). Luettu 08.05.2023.
