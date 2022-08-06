# Intro
Eftersom jeg ofte har oplevet at tekstdata ofte bliver udleveret som enten tei- eller xml-filer, er jeg blevet opmærksom på, at man humanister har brug for et kendskab til, hvordan man trækker data ud af opmærkede filer.

Opmærkede filer er kendetegnet deres særlige datastruktur, der består i at tags omslutter tekstelementer (se f.eks. https://www.w3schools.com/xml/xml_whatis.asp og https://www.w3schools.com/xml/xml_elements.asp). Datastruktureren bliver ofte meget kompleks og jo mere kompleks datastrukturen er jo mere kompleks bliver den kode, der kan trække data ud.

I denne notebook benytter jeg en xml-opmærket fil, som jeg har tilpasset, så den kan anvendes til at få kendskab til, hvordan man trækker data ud af xml uden, at pythonkoden bliver for kompleks.

Den tilpassede fil er bygget ud fra en kompleks tei-opmærket fil, som er produceret i projektet Ludvig Holbergs skrifter: https://www.uib.no/ub/71975/ludvig-holbergs-skrifter.

Filen indeholder Holberg komedien Den Politiske Kandstøber. En komedie opmærket i xml vil ofte være opmærket med tags, der omslutter akter, scener, talere, replikker og så videre.

Jeg har besluttet at bruge python biblioteket BeautifulSoup til at trække data ud (https://beautiful-soup-4.readthedocs.io/en/latest/#quick-start). Biblioteket re (regulære udtryk) vil jeg også bruge. Det er til at bearbejde tekstdata.

# Import af biblioteker
Jeg begynder med at importere bibliokterne.


```python
from bs4 import BeautifulSoup
import re
```

# Indlæsning af xml-filen
I mappen datasets ( https://github.com/KUBDatalab/datasets ) findes en zip-fil, der hedder 'Holbergs comedies'. Zip-filen indeholder en mappe med xml-filer, der indeholder opmærkede Holberg komedier. Download filen, unzip den, og så vil du kunne køre denne notebook selv.

Jeg indlæser filen på denne måde.


```python
with open('C:\\Users\\lakj\\data\\holbergs_comedies\\test1.xml', 'r', encoding='utf-8-sig') as f:
    file = f.read() 

soup = BeautifulSoup(file, 'xml')
```

Xml's datastruktur er nu gemt i vaiablen soup. Lad os undersøge, hvilke tags den indeholder brugt.


```python
tags = [tag.name for tag in soup.find_all(True)]
print (set(tags))
```

    {'date', 'head', 'stage', 'source', 'scene', 'body', 'title', 'speaker', 'titlePage', 'castList', 'role', 'act', 'e_source', 'author', 'front', 'text', 'actor', 'castItem', 'w', 'header', 'performance', 'sp', 'statement', 'p'}
    

Linket til den elektroniske udgave af komedien, som filen filen stammer fra ligger i tagget 'e_source'.


```python
soup.e_source.text
```




    'http://holbergsskrifter.dk/holberg-public/view?docId=skuespill%2FDen_Politiske_Kandstoeber%2FKandst.page;toc.depth=1;brand==start'



# Udtræk tekstdata
Neden for trækker jeg tekst ud af datastrukturen.


```python
date_tag = soup.date # date er et tag
date = date_tag.text # text bruger jeg til at trække den tekst ud, som findes i tagget 
print(date)

title_tag = soup.title
title = title_tag.text
print(title)

author_tag = soup.author
author = author_tag.text
print(author)
```

    1723
    Den Politiske Kandstøber
    Ludvig Holberg
    

Der er flere linjer, der har tagget 'role' Derfor må jeg bruge .find_all('role') til at finde alle rolle-tags.
.find_all() returnerer en liste, som jeg gemmer i 'role_tags'. Når jeg har en liste, kan jeg bruge en list-comprehension til at gennemløbe alle listens 'items' (i). (https://www.w3schools.com/python/python_lists_comprehension.asp).  


```python
role_tags = soup.find_all('role')
roles = [i.text for i in role_tags]
print (roles)
```

    ['Herman von Bremen.', 'Geske hans Hustru.', 'Engelke hans Datter.', 'Antonius hendes Beiler.', 'Henrich Tieneren.', 'Anecke Piigen.']
    

Der er også flere linjer, der har tagget 'actor', så den kan jeg løse på som ovenfor.


```python
actor_tags = soup.find_all('actor')
actors = [i.text for i in actor_tags]
print (actors)
```

    ['M. Ulsø.', 'Mad. Montaigu.', 'Mad. Coffre.', 'M. Piloi.', 'M. Wegner.', 'Mad. Lerke.']
    

W-tagget indeholder ord, der holdes sammen af bindestreger.


```python
hyphen_words_tags  = soup.find_all(['w'])
hyphen_words = [i.text for i in hyphen_words_tags]

print(hyphen_words)
```

    ['Hoved-Personerne', 'Hals-Klud', 'Smørre-Brød', 'u-mælende', 'u-værdig', 'Sviger-Søn', 'Sviger-Søn', 'Stads-Sager', 'Sviger-Søn', 'Staats-Sager', 'Bord-Enden', 'Staats-Sager', 'Chuur-Førster', 'Kreds-Oberst', 'Kreds-Tropperne', 'Staats-Sager', 'Chur-Fyrsten', 'Øster-Rige', 'Øster-Rige', 'Pose-Kiger', 'Søe-Sted', 'Pose-Kiger', 'Søe-Sted', 'Pose-Kiger', 'Brende-Viin', 'Huus-Leje', 'Tamper-Ret', 'Raads-Herrer', 'Raads-Herrer', 'Guld-Smid', 'Guld-Smeddenes', 'Kniv-Smed', 'Handtvercks-Laug', 'Handtverks-Folk', 'Hovet-Lejeren', 'Rihn-Strømmen', 'Rihn-Strømmen', 'Kniv-Smed', 'Hoved-Forseelse', 'Pose-Kiger', 'Kniv-Smed', 'Pose-Kigere', 'Kniv-Smed', 'Raads-Herrers', 'Raads-Herrer', 'Raads-Herrer', 'Bormester-skab', 'Livs-Tid', 'Raads-Herrernes', 'Bormester-skab', 'Kandstøber-Nøcker', 'Jøde-Præst', 'Raads-Herrernes', 'Thee-Bord', 'Kandstøber-Væsen', 'Thee-Bord', 'Caffee-Bord', 'Snustobaks-Daase', 'Skiøde-Hund', 'Naboe-Kone', 'Slagter-Kniven', 'Dricke-Penge', 'u-visse', 'Bonde-Daatter', 'Caffee-Bordet', 'Caffee-Bordet', 'Raads-Herrernes', 'Mad-Spand', 'Liim-Stangen', 'Raad-Stuen', 'Raads-Herrinder', 'Raads-Herinderne', 'Raads-Herinde', 'Raads-Herinde', 'Raads-Herinde', 'Raads-Herinde', 'Raads-Herinde', 'Lencke-Hund', 'Raads-Herinde', 'Raads-Herinde', 'Regierings-Form', 'Raads-Herinder', 'Raads-Herinde', 'Raads-Herinde', 'Raads-Herinde', 'Raads-Herinden', 'Lyck-Ønskning', 'Raads-Herinden', 'Med-Colleginder', 'Raads-Herinden', 'Raads-Herinden', 'Raads-Herinden', 'Raads-Herinde', 'Mands-Person', 'Du-Søstre', 'Raads-Herinden', 'Hoved-Vands-Eeg', 'Smeede-Kielling', 'Brende-Viinen', 'Raads-Herinden', 'Raads-Herinden', 'Bormester-Fruer', 'Raads-Herinden', 'Aar-viis', 'Raads-Herinden', 'Raads-Herinde', 'Raads-Herinden', 'Audientz-Timen', 'Buxe-Lomme', 'Stats-Sager', 'Lyck-Ønskning', 'Lands-Lov', 'Tids-spilde', 'Ud-Tale', 'Ting-Stude', 'Mands-Person', 'Hierte-Blod', 'Liberi-Kiol', 'Østers-Kiellinger', 'Første-grøde', 'Castor-Hatte', 'Tings-Vidne', 'Tings-Vidner', 'Castor-Strømper', 'Stock-Fisk', 'Stue-Bordet', 'Raads-Herrer', 'Raads-Herrer', 'Hals-Been', 'Stads-Cabineter', 'Nach-Tisker', 'Verts-Huus']
    

# Udtræk tekst fra tags med attributter

Et tag kan have en attribut f.eks.:

\<act id="act2"> eller

\<scene id="act2sc1">

Datastrukternen i disse tags kan fremfindes således:


```python
act2 = soup.find(id="act2")
```


```python
act2sc1 = soup.find(id="act2sc1")
```

I hvert akt er der flere linjer, der er tagget 'speaker'. Disse linjer fremfindes med .find_all().


```python
speakers_tags = act2.find_all('speaker')
speakers = [i.text for i in speakers_tags]
print(speakers[0:10])
```

    ['Gert Buntmager.', 'Sivert Pose-Kiger.', 'Frantz Parykmager.', 'Sivert Pose-Kiger.', 'Frantz Parykmager.', 'Sivert.', 'Frantz', 'Herman.', 'Verten.', 'Herman.']
    

I hvert akt er der flere linjer, der har tagget 'stage'. Disse linjer fremfindes på samme vis. 


```python
stage_tags = act2.find_all('stage')
stages = [i.text for i in stage_tags]
print(stages)
```

    ['Collegium Politicum præsenteres i et Wertshuus.', 'I det hand peeger paa Donaustrømmen, støder hand Kruset om med Albuen, saa at Landkortet forderves.', 'De leer allesammen. Ha, ha, ha.', 'Geske. Collegium Politicum. ', 'Hun gier ham Ørefigen.', 'Hand lader som hand vil slaae, men begynder paa nye at tælle til 20.', 'Gieske skielder uden for.', 'Collegium Politicum.', 'De komme i sterck Skielderie og taler en i Munden paa en anden, reiser sig af Stoelene, truer og stoier. Herman slaar imod Bordet og raaber:', 'Paa Vejen disputerer de og stoier om det forrige.']
    

I hvert akt er hvert tekst afsnit opmærket med tagget 'sp'. Koden nedenfor printer de to første afsnit i andet akt. Læg mærke til at elementerne i 'speaker-taggene' er inkluderet i sp-taggene. Det er Gert Buntmager, der siger første replik og Sivert Pose-Kiger, der siger anden replik.  


```python
sp_tags = act2.find_all('sp')
sp = [i.text for i in sp_tags]
print (sp[0:2])
```

    ['\nGert Buntmager.\nDet vil altsammen give sig paa neste Rigsdag; gid jeg var der en Time. Jeg vilde viske Chur-Fyrsten af Mayntz noget i Ørene, som hand skulde tacke mig for. De got Folck veed icke, hvorudi Tysklands Interesse bestaar. Hvor har mand nogen Tid hørt en Keyserlig Residentz Stad som Wien uden Flode eller i det ringeste uden Gallejer? de kunde nock holde en Krigs-Flode til Rigets Forsvar, der gives jo nock Krigssteur og nock Romer-Monathen dertil. See om icke Tyrcken er klogere. Vi kand aldrig lære bedre at føre Krig end af ham. Der er jo Skove nock baade i Øster-Rigeog Prag, dersom mand kun vil bruge dem enten til Skibe eller Master. Hafde vi en Flode i Øster-Rige eller Prag, da lod nock Tyrcken eller Frantzmanden fare at belejre Wien, og vi kunde gaae lige til Constantinopel. Men ingen tencker paa saadant.\n', '\nSivert Pose-Kiger.\nNej aldrig nogen Moors Siæl; vore Forfædre har været langt klogere. Det kommer alt paa Anstalter. Tyskland er icke større nu end det var i gamle Dage, da vi icke alleene forsvarede os berømmelig mod alle vore Naboer, men end og tog ind stoore Stycker af Franckerige, og beleyrede Paris selv baade til Lands og Vands.\n']
    

Replikkerne ligger i p-tags. Replikkerne trækkes ud på denne måde. Koden nedenfor printer de første to.


```python
p_tags = act2.find_all('p')
p = [i.text for i in p_tags]
print (p[0:2])
```

    ['Det vil altsammen give sig paa neste Rigsdag; gid jeg var der en Time. Jeg vilde viske Chur-Fyrsten af Mayntz noget i Ørene, som hand skulde tacke mig for. De got Folck veed icke, hvorudi Tysklands Interesse bestaar. Hvor har mand nogen Tid hørt en Keyserlig Residentz Stad som Wien uden Flode eller i det ringeste uden Gallejer? de kunde nock holde en Krigs-Flode til Rigets Forsvar, der gives jo nock Krigssteur og nock Romer-Monathen dertil. See om icke Tyrcken er klogere. Vi kand aldrig lære bedre at føre Krig end af ham. Der er jo Skove nock baade i Øster-Rigeog Prag, dersom mand kun vil bruge dem enten til Skibe eller Master. Hafde vi en Flode i Øster-Rige eller Prag, da lod nock Tyrcken eller Frantzmanden fare at belejre Wien, og vi kunde gaae lige til Constantinopel. Men ingen tencker paa saadant.', 'Nej aldrig nogen Moors Siæl; vore Forfædre har været langt klogere. Det kommer alt paa Anstalter. Tyskland er icke større nu end det var i gamle Dage, da vi icke alleene forsvarede os berømmelig mod alle vore Naboer, men end og tog ind stoore Stycker af Franckerige, og beleyrede Paris selv baade til Lands og Vands.']
    

Notebooken viser eksempler på, hvordan data kan trækkes ud af xml-filer. Næste trin i en digital analyse kunne være preprocessering for at klargøre teksten til analyse. 


## Bidrag

Alle bidrag til denne notebook er velkommen!


## Tilrettelagt af

Lars Kjær, lakj@kb.dk

## Citation

Lars Kjær, The Digital Scene in Theatre Research, 2022, https://kubdatalab.github.io/the_digital_scene_in_theatre_research/