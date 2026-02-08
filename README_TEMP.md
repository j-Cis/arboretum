

opisane tu :

* `https://www.cisowscy.com/gene/grampsxml`
* `https://www.cisowscy.com/gene/gramps`

### **[A.4+]** Programy, Strony, Formaty;  (które mają interesujące funkcje)

> Przy koncepcjonowaniu naszego projektu `Arboretum`, pewne cechy i rozwiązania odwzorujemy z:
>
> * [GRAMPS (v: 6.0.6) [Python, SQLite]](https://github.com/gramps-project/gramps),
> * [GeneWeb (v: 7.1.0-beta2 [OCaml])](https://github.com/geneweb/geneweb),
> * ,
> * [MyHeritage Family Tree Builder](https://www.myheritage.pl/family-tree-builder),
> * [My Family Tree (v: 16) [.NET 10, SQLite]](https://chronoplexsoftware.com/myfamilytree/),
> * GedCom 5.5.1
> * GedCom 7.0.0
> * GrampsXML 1.7.2
>
> ---
>
> * [Ages! (v: 2.2.0) [Delphi / C++]](https://www.daubnet.com/en/ages),
> * [Ahnenblatt (v: 4)](https://www.ahnenblatt.com/),
> * [progeny: Charting Companion / Family Tree Charts](https://progenygenealogy.com/products/family-tree-charts/),
> * [GenoPro](https://genopro.com/pl/),
> * [RootsMagic (v: 10) [C++, SQLite]](https://www.rootsmagic.com/download/rootsmagic-10),
> * [plSOFT: Drzewo Genealogiczne](https://www.plsoft.pl/produkt/drzewo-genealogiczne/),
> * [The Complete Genealogy Products](http://www.tcgr.bufton.org/),
>
> ---
>
> * [FTAnalyzer](https://github.com/ShammyLevva/FTAnalyzer),
> * [gedcom-svg-tree](https://github.com/ameros/gedcom-svg-tree),
>
> ---
>
> * [WikiTree](https://www.wikitree.com/),
> * [Rodovid](https://en.rodovid.org/wk/Main_Page),
> * [FamilySearch](https://www.familysearch.org/pl/tree/),
> * [Geneteka](https://geneteka.genealodzy.pl/index.php),
> * GeneaNet,
> * MyHeritage,
> * Ancestry,
> * Geni,
>
> ---
>

## **[B]** Zmiana paradygmatu wprowadzania danych

..

### **[B.1]** Problem powielania

Największym niepotrzebnym zbiorem jest powielanie tych samych imion i nazwisk, co też przy powielaniu może powodować głupie błędy typu literówki.
Zamierzam zmienić to w taki sposób że, sercem naszego `Arboretum` będzie spora lista dołączonych baz sytemowych np.:

* `Antropomastykon` będize zawierał wszystkie imiona wraz z ich IPA, i fleksją (deklinacją); podobnie też nazwiska (tu wiadomo lista nie ma szans być pełna), herby, przydomki (tu wiadomo lista nie ma szans być pełna), patrynonimy, matrynonimy; być może wydziele Antropomastykon na sekcje tematyczne by ułatwić jego rozwój;
* `Ojkonomastykon` zbiór wszelkich nazw miejscowości zamieszkałych, wraz z lokalizacjami GPS, i alternatywnymi nazwami i IPA, oraz informacją kiedy powstało, kiedy miastem było a kiedy wsią, itp
* `Choronomastykon` zbiór wszelkich krain, powiatwów, województw, gmin, krajów, państw, cyrkółów itp wraz z datami istnienia, i zależnościmi przenależności; coś na wzór `https://gov.genealogy.net/item/show/KOPINOJO84WS`
* `Pragmatomastykon` zbiór wszystkich zawodów i funkcji również w wielu językach
* i inne
  
te bazy systemowe będą wykorzystywane w wielu miejscach, należy (Uzytkownik w bazie uzytkowej, wybiera z bazy systemowej tylko to co potrzebuje i umieszcza w bazie uzytkowej. , dzieki temu niezaleznie od wersji bazy sytemowej będzie można przenieść na inne stanowisko, bazę użytkową która będzie kompletna, poza zewnetrznym zbioram: foldererm z plikami (fotografie, skany, materialy audio notatek, materialy video wspomnien np, materiały tekstowe (opisy, dokumenty, notatki itp))):

* np gdy będziemy indeksować metrykę czy inne źródło (o mechanice tego napiszę jeszcze dokładniej poźniej/poniżej), będziemy pisać początek słowa, albo litery których jesteśmy pewni (wiadomo wyraz może być w róznej deklinacji ale to nie problem bo mamy to!, wyraz moze być też z błedem, ale spokojnie mamy IPA, znajdzie wyrazy o podobnym brzmieniu), wiec wpisujemy literki, a system popowieada nam słowa z istniejących baz systemowych - to niesamowicie ułatwi indeksację.
* drugie zastosowanie, w profilu osoby, nie wpisujemy imion, dołączamy do naszej bazy zdefiniowane słowa z baz systemowych, których chemy użyć - dzieje się to niemal automatycznie, chcąc dodać imię do osoby które nie jest jeszcze w systemie, poprostu się dołącza, następnie w profilu osoby nie przechowywujemy imienia, tylko odniesienie do niego, dzięki temu nie mamy 10 tysięcy razy napiasnego `Stanisław Jan` tylko 2 odniesienia-relacje do imienia `Stanisław` jako pierwsze i kolejne w tej sekwencji `Jan`, i tak samo z nazwiskiem, herbem, miejscem zamieszkania czy uroddzenia czy innego wydarzenia i zawodem itp.

#### **[B.2]** Źródła ⚡⚡⚡⚡⚡

Nie spotkałem narzędzia genealogicznego które miało by przemyślany ten model. O ile nawet dodawanie źródeł jest możliwe, o tyle zarządzanie nimi już nie, jak i zarządzanie czym kolwiek bez źródła. System FamilySearch jest całkiem niezły bo mamy w nim element podpinania źródeł pod konkretne dane - problematczne jest że u nich źródło może być linkiem lub wspomnieniem, to odcina dzięsiątki setek typów innych.

---


## **[C]** Główne koncepcje technologiczne w bukiecie apek `Arboretum`

Nasza struktura plików

```plaintext
PS A:\GIT-RUST\l007-db-slint\ARBORETUM-02> cargo plot-fs-tree -s 'ext-name' -l '.' -p '**/*, !.git/, !target/, !release/, !.idea/' 
------------------------------------------------
Lokalizacja: .
Wzorce:      **/*, !.git/, !target/, !release/, !.idea/
------------------------------------------------
├── 🔒 Cargo.lock
├── ⚙️  Cargo.toml
├── ⚙️  .gitignore
├── 📖 Untitled-1.md
├── 📖 all_files.md
├── 📖 info.md
├── 📄 Makefile.toml
├──┬ 📂 apps/
│  ├──┬ 📂 config-access/
│  │  ├── ⚙️  Cargo.toml
│  │  ├── 🦀 build.rs
│  │  ├──┬ 📂 src/
│  │  │  └── 🦀 main.rs
│  │  └──┬ 📂 ui/
│  │     └── 📄 window.slint
│  └──┬ 📂 config-assets/
│     ├── ⚙️  Cargo.toml
│     ├── 🦀 build.rs
│     ├──┬ 📂 src/
│     │  └── 🦀 main.rs
│     └──┬ 📂 ui/
│        └── 📄 window.slint
└──┬ 📂 crates/

------------------------------------------------
PS A:\GIT-RUST\l007-db-slint\ARBORETUM-02>
```

> Ułatwienie uzyskiwania struktury
>
> ```powershell
> PS> cargo plot-fs-tree -s 'ext-name' -l '.' -p '**/*, !.git/, !target/, !release/, !.idea/'
> ```

### **[C.1]** BINARKI

Binarki mają być odpowiedzialne tylko za interakcje, cała logika ma być w bibliotekach. Każda binarka będzie tak na dobrą sprawę złożona z 3 oddzielnych binarek:

* wersja 🅰️(bez Slint) w której wszystko możemy wykonać z poziomu CLI / komend w terminalu czy interfejsu konsolowego.
* wersja 🅱️(z Slint) w której wszystko możemy wyklikać z poziomu GUI / interfejsu graficznego
* wersja 🆎(z Slint) w której mamy możliwość wewnątrz okna odpalenia konsoli/terminala, i dla wygody niektóre rzeczy wykonywać z konsoli, aby nie klikać za dużo, jest to wersja zawierająca pełnoprawne obie 🅰️ i 🅱️.

### **[C.2]**  Zarządzanie zbiorami

Podobnie jak w `Gramps` czy `FamilyTreeBuilder`, u nas w `Arboretum` będzie trochę inaczej gdyż osobny program konfiguracyjny będziemy mieli - osobna binarka `config-access` w niej tak samo jak w `Gramps` wskażemy folder w którym przechowywać będziemy nasze bazy, dodaatkowo wskażemy wolder w którym będziemy zapisywać automatyczne kopie bazy "dump", dodatkowo czego w `Gramps` nie było gdyż posiada on inną logikę, będziemy w osobnej binarce `config-assets` wskazywać folder z bazami systemowymi, i tam też nimi zarządzać. Jako że wiadomo że nie można otworzyć już otwrtej bazy ani mieć do niej dostępu z innego punktu, nasze programy będą potrzebować do komunikacji między sobą pliku konfiguracyjnego, jeszcze nie zdecydowałem w jakim formacie, czy toml, czy xml, czy inny..może binarny? grunt by nie spowalniał całośći.

---

**Teraz poszczególnie omówię te 2 binarki związane z zarządzaniem naszym `Aboretum`:**

#### **[C.2.a]**  `config-assets` Zarządzanie kolekcją baz danych stanowiących trzon systemowy programu, a także zarządzanie nimi

ciąg dalszy opisu nastąpi...

---

#### **[C.2.b]** `config-access` Zarządzanie kolekcją baz danych i samymi pojedynczymi bazami stanowiącymi nasze projekty

ciąg dalszy opisu nastąpi...

....
...ciąg dalszy nastąpi

---
