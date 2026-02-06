
# ARBORETUM 🎗️

`Arboretum 🎗️` - będzie to zestaw narzędzi dla `genealoga` czy `pasjonata historii rodzinnej` ale nie takiego traktującego genealogię jak dobrą zabawę - lecz podchodzącym do tematu w sposób badawczy!

_**Po co kolejny program? - przecież jest ich tak wiele!** - otóż nie! Spośród dostępnych na rynku programów - a długo szukałem / czkeałem, ponad 10 lat - nie istnieje żaden w pełni odpowiadający moim oczekiwaniom. Stąd też głównym adresatem niniejszego rozwiązania, jestem ja sam._

_**Istniejące programy, mają ogromny problem z wydajnością.** Dla przykładu pół miliona osób w `GRAMPS` (napisany w `Python`) daje bazę `sqlite.db` o rozmiarze `3.66 GB` czas otwierania, i manipulacji na danych jest niebotycznie duży. **`Arboretum 🎗️` ma być szybsze, nie tylko przez szybszą technologię użytą, ale także przez zmianę metodyki działań.**_

`Arboretum 🎗️` będzie się składało nie tak jak powszechnie przyjęte - z baz użytkowników - ale dodatkowo trzonem będą bazy systemowe.

Bazy będą maksymalnie zatomizowane a zarazem uporządkowane bardziej niż w modelu GRAMPS.

Brak kosztownych operacji `JOIN` znanych z `SQL`, model bazy jest grafowy! Z Hipergrafami osiągniętymi przez reifikacje Krawędzi - czyli uznanie że Hiperkrawędź też jest Węzłem. (W SurrealDB jest to banalnie proste i wydajne, bo Surreal pozwala przechowywać tablice linków (Array of Record IDs) bezpośrednio w rekordzie.)

Architektura oparta na współdzielonym stanie konfiguracyjnym. Wydajna dzięki plikom konfiguracyjnym w formacie CBOR (Concise Binary Object Representation) lub Bincode (bardzo szybki, specyficzny dla Rusta), zamiast parsowania tekstu (TOML/XML/JSON) w czasie rzeczywistym między procesami. Traktuje plik konfiguracyjny jako "token" przekazywania stanu. Atomowy zapis pliku konfiguracyjnego (tempfile + std::fs::rename) - eliminujący błędy związane z np utratą zasilania..; Singleton na poziomie aplikacji (fslock / named-lock).

> [![Rust](https://img.shields.io/badge/Rust-1.93.0%20%7C%20edition%202024-dca282.svg?logo=rust&logoColor=white)](https://rust-lang.org/) - główny język.
>
> (jeszcze nie cały) stos technologiczny w Rust (Pure Rust),:
>
> [![SurrealDB](https://img.shields.io/badge/SurrealDB%20-2.6%20(embedded:%20kv--surrealkv)-ff00a0.svg?logo=surrealdb&logoColor=white)](https://surrealdb.com/) - baza danych
> > SurrealKV dla pojedynczej bazy ma następującą strukturę plików
> > 
> > ```plaintext
> > ├──┬ 📂 clog/
> > │  └── 📄 00000000000000000000.clog
> > └──┬ 📂 manifest/
> >    └── 📄 00000000000000000000.manifest
> >  ```
> > 
> > nie mamy tu jak w SQLite plików z rozszerzeniem `*.db`
>
> [![Slint](https://img.shields.io/badge/Slint-1.15.0-4c4cff.svg?logo=slint&logoColor=white)](https://slint.dev/) - interfejs graficzny
>
> inquire "0.9.3", colored "3.1.1", clap "4.5.16" - interfejs terminala/konsoli
>
> tokio "1.49.0" - obsługa asynchroniczności
>
> serde "1.0.218", serde_json "1.0", uuid "1.10.0" - serializacja i identyfikacja
>
> thiserror "2.0.1", anyhow "1.0", tracing "0.1", tracing-subscriber "0.3" - śledzenie błędów
>
> chrono, jiff - manipulacja formatem wyświetlanego czasu.
>
> strsim, phonetic,tantivy - Przetwarzanie Tekstu i Wyszukiwanie
>
> nom, winnow - parsowanie tagów
>
> tarpc, interprocess - Komunikacja między binarkami
>
> petgraph - manipulacja na grafach
>
> geo, geozero - geolokalizacja
>
> directories, fs2, fslock, notify - Zarządzanie Plikami i Konfiguracją
>
> ring, argon2 - Bezpieczeństwo i Kryptografia (Opcjonalne)
>
> rfd - specyficzne narzędzia dla SLINT, dialog wyboru plików.
>
> tempfile, named-lock, 

Narazie jest pisany na Windowsie 64bitowym w wersji 11 na i7, ale docelowo będę chciał kąpilować wersje na starsze windowsy też, linux, macos, ios, android.

Projekt od początku powstaje przy odpowiednich standardach:

* DRY (Don't Repeat Yourself).
* SRP (Single Responsibility Principle).
* SoC (Separation of Concerns).
* Fail Fast (Szybka Porażka).
* Composition over Inheritance.
* The Boy Scout Rule.
* KISS (Keep It Simple, Stupid).

Maksymalna reużywalność i idiomatyczne nazwy funkcji, struktur, plików, folderów, modułów - to podstawa.

---

## **[A]** Paradygmat wprowadzania danych w wiodących programach

..

### **[A.1]** MyHeritage

Jestem zadowolonym posiadaczem abonamentu `PremiumPlus` wraz z `abonamentem kompletnym` korzystam z MyHeritage od 2008 roku, i powstanie tego programu `Arboretum` nie wpłynie na zaprzestanie! będzie on służył do gromadzenia i zarządzania i porządkowania, natomiast MyHeritage pozostanie do publikacji i poszukiwań osób zajmujących się tymi samymi danymi `SmartMatch`.

Skupmy się teraz na sposobie wprowadzania danych, mamy tu tylko rekordy typu osoba, i wszystko drzemie w nich, gdy szukamy to możemy szukać tylko w obrębie tego co jest wpisane w oknie `imiona` i `nazwisko` to determinuje dodawania róznych niepotrzebnych infromaci w tych oknach tylko poto aby móc po tym `tagu` coś znaleźć. I tak dla przykładu:

* w imieniu mojego taty mam `✅ ✉️ ⧭ Jerzy Maciej Stanisław Józef, ⛿[PL-12: Tarnów]` a w nazwisku mam `h.Sas, Cisowski (z Niemirowa), ♂I‐Y250574 ♀T1a1b‐a`, i tak w mojej konwencji pozwala mi to wyszukać inne osoby pasujące do tagów, nie tylko innych Jerzych i innych Cisowskich ale (tu gdzyby to była kobieta mielibyśmy jeszcze `nazwisko panieńskie` zapomocą kótego również moglibyśmy szukać):
  * `✅` symbol oznacza że ta osoba z inną osobą ma potwierdzone dopasowanie genetyczne
  * `✉️` symbol oznacza że mam kontakt z taką osobą
  * `⧭` symbol oznacza że jest to mój przodek
  * `Jerzy Maciej Stanisław Józef` to standardowe imiona, można szukać pojedynczo lub zbiorczo
  * `⛿[PL-12: Tarnów]` ten ciąg pozwala wyszukać mi osoby pochodzące z małopolski `⛿[PL-12:` a lbo strikte związane z Tarnowem `⛿[PL-12: Tarnów]`
  * `h.Sas` to pozwala mi znaleźć osoby z herbem Sas
  * `Cisowski` to standardowe nazwisko, można i po nim szukać ale trzeba pamiętać że `Cisowska` dla systemu to inne nazwisko
  * `(z Niemirowa)` to pozwala mi znaleźć rody z protoplastą w Niemirowie
  * `♂I‐Y250574` to pozwala znaleźć mi osoby z tą y-chromosomalną haplogrupą
  * `♀T1a1b‐a` to pozwala znaleźć mi osoby z tą mitochondrialną haplogrupą
* kolejne okienka w formularzu to `Tytuł`, `Przydomek` cokolwiek tu wpiszemy, nie będziemy mogli po tym szukać
* potem jest data urodzenia i śmierci obie z miejscem, oraz przyczyna śmierci i miejsce pogrzebu, a następnie małżeństwa z datami i miejscami - to też są izolowane dane których nie da się wyszukać.

Po wybraniu opcji `Edytuj więcej szczegółów` mamy wiecej opcji, których też nie da się wyszukać:

* SEKCJA `Info podstawowe`: możemy tu dodać `Imie Religijne`, `Poprzednie imię`, `Przezwisko`, `Nazwa po (imiennik)` - gdzie wskazujemy inną pojedynczą istniejącą w bazie osobę.
* SEKCJA `Rodzina` możemy tu dodać informacje czy to jest `biologiczne` dziecko czy inny rodzaj a także `świadków` z ślubów w formie krókiego tekstu.
* SEKCJA `Biografia` możemy tu napisać coś i dołączyć obrazki, ale uwaga nie mamy mozliwości przeczytać tego z poziomu aplikacji mobilnej, to biografie są nazwane `przypis` i możemy ich utówrzyć dużo koło siebie.
* SEKCJA `Informacje kontaktowe` tu nie będę się rozwodził, mammy rubryki: adresów, mailów, telefonów, i profilów w sieci.
* SEKCJA `Praca` i SEKCJA `Edukacja`: tu możemy dodać prace i edukacje, wypełniając dla każdego elementu rubryczki `Zatrudnienie/Edukacja`, `Miejsce`, `Przypis` i `Od` `Do` (daty).
* SEKCJA `Ulubione` tu możemy rodzielając przecinkami podać: `Zainteresowania`, `Aktywności`, `Muzyka`, `Filmy`, `Programy Telewizyjne`, `Książki`, `Sporty`, `Restauracje`, `Kuchnie świata`, `Ludzie i gwiazdy`, `Krótkie wakacje`, `Cytaty` - nie będę komentował tej sekcji - jest absurdalna.
* SEKCJA `Informacje osobiste` tu mamy: `Religia`, `Narodowość`, `Język`, `Polglądy polityczne`, `Wzrost`, `Waga`, `Kolor włosów`, `Kolor oczu`, `Sprawność fizyczna`
* SEKCJA `Źródło cytatu` to jedna z tych sekcji nad któą najbardziej ubolewam, możemy tu wszakże podać źródła, ale zarządzanie nimi jest w zasadzie żadne!możemy dodać podobnie jak w `Biografi`, `Edukacji`, `Pracy` wiele `odnośników do źródeł / Cytatów / Źródeł cytatu` (różnie nazywanych  - tyczących się tego samego elementu); w każdym jednym wskazujemy `źródło` lub tworzymy nowe (opiszę poniżej), następnie mamy `Tekst cytatu` w którym możemy napisać dłużą informacje tak jak w `Biografi`, możemy podać `Srtona/URL`, `Potwierdzenie` (stopień ufności), oraz `data`
  * podsekcją jest opcja dodawania źródła w której mamy takie róbryczki: `Tytuł źródła`, `Skrót`, `Autor`, `Wydawca`, `Agencja`, `Opis`, `dołączanie: zdjęć, plików, i dokumentów źródła`
* SEKCJA `Wszystkie fakty` mamy tu wylistowane nasze fakty wcześniejsze takie jak `Narodziny`, `ślub`, `adresy`, `prace`, `edukacje` dodatkowo są też `liczba znanych dzieci` i możemy tu dodać dowolny inny fakt, niektóre fakty posiadają spersonalizowane róbryczki względem nich, ale ogólnie do każdego faktu (poza wyjątkami) możemy dodać: `data`, `miejsce`, `krótki opis`, `przypis` (w formie takiej jak w biografi, możemy dodać ich wiele), `Cytaty` (w takiej formie jak w sekcji `cytaty`, tez możemy dodać ich wiele), oraz ostatni element `zdjęcia` możemy tez dodać ich wiele.
Dodatkowo z poziomu wyświetlenia profilu osoby jak i z poziomu zdjęć, możemy przypisać do osoby wiele zdjęć, na których możemy zaznaczyć ramkę z twarzą.
Każde pojedyncze zdjęcie może mieć podstawowo `nazwę`, `date` i `miejsce` dodatkowo jak już wspomniałem, pozaznaczane ramki twarzy przypisane do osób, można też dodać `słowa kluczowe` i `uwagi`, a także przypisać do albumów (co jest starsznie czasochłonne).
W profilu osoby mamy też opcje związane z DNA autosomalnym, i Podobieństwami automatycznymi.

Oczywiście MyHeritage posiada funkcję przeszukiwania, ale nie możemy w niej zaznaczyć że interesują nas tylko nasze drzewa, szukamy po całym dwu miliardowym zbiorze `https://www.myheritage.pl/research/category-5000/family-trees` tu możemy szukać po `imieniu`, `nazwisku`, `dacie urodzenia i miejscu`, `dacie śmierci i miejscu`, `dacie małżeństwa i miejscu` a także po po innych wydażeniach: `miejscu zamieszkania (data i miejsce)`,`służba wojskowa (data i miejsce)`,`imigracja (data i miejsce)`,`lub dowolne wydarzenie bez nazwy (data i miejsce)`, dodatkowo możemy uszczegółowić i podać: `Ojciec (imie i nazwisko)`, `Matka (imie i nazwisko)`, `Dziecko (imie i nazwisko)`, `Małżonek (imie i nazwisko)`, `Brat/Siostra (imie i nazwisko)`, możemy też dodać `słowo kluczowe` które działa dziwnie, oraz `płeć`.

### **[A.2]** FamilySearch

Z FamilySerach rónież korzystam od niezliczonych czasów conajmniej od 2014 a może i wcześnie, jest on dużo bardziej czasochłonny, ale ma wiele żeczy lepiej przemyślanych niż MyHeritage, a innych poprostu nie ma lub działają gorzej. Podstawową wadą tego serwisu, jest to że każdy może edytować wszystko i bardzo często czas jaki poświęcimy idzie do kosza, bo ktoś coś zmieni w sposób niezgodny z rzeczywistością.

* W sekcji `kluczowe informacje` mamy : `imię i nazwisko`, `płeć`, `Narodziny`, `Chrzest`, `Śmierć`, `Pochówek` każda z tych informacji posiada niezależne wskazanie daty ostatniej edycji oraz informacje kto tej edycji dokonał (co jest bardz przydatne! w sekcji `Ostatnie zmiany` możemy przejżeć historię wszystkich zmian, od początku od stworzenia profilu, co również jest cenne!). I tak dla każdego mamy widzimy listę źródeł które to potwierdzają (samo dodawanie źródła jest w innym miejscu), oczywiście wyświetla nam się informacja jeśli zmieniamy istniejący profil, kto i kiedy dokonał zmiany, i możemy wyświetlić wszystkie poprzednie zmiany, a także możemy podać uzasadnie:
  * `imię i nazwisko` : tu możemy podać `język`, `tytuł`, `imiona`, `nazwisko`, `sufiks`
  * `płeć` to wiadomo 3 opcje `mężczyzna`, `kobieta`, `nieznana`
  * `Narodziny`, `Chrzest`, `Śmierć` `Pochówek`: w każdym podajemy `datę` i `miejsce`
* W sekcji `Członkowie rodziny` edytujemy relacje z rodzicami, z małżonkami i dziećmi, co kluczowe możemy wygodnie dodać wielu rodziców, nap adopcyjnych czy przybranych itp.
  * KOICJA: W podsekcji małżeństwo (zarówno własne jak i małżeństwo rodziców), możemy do każdej z dwojga osób wchodzących w koicję podać uzasadnienie, mamy też datę dodania każdej z dwóch osób i informację kto to dodał,
    * do każdego małżeństwa^(czyli komórki rodziny/koicji) możemy dodać wydarzenia 5 typów `Małżeństwo`, `Mieszkali razem`, `Rozwód`, `Małżeństwo według prawa zwyczajowego`, `Unieważnienie` - każde z nich ma następujące pola `Data`, `Miejsce` i `Uzasadnienie` a także jak w sekcji `kluczowe informacje` widzimy listę źródeł które to potwierdzają (samo dodawanie źródła jest w innym miejscu), a takze mamy info kto i kiedy edytował, oraz możlowość podglądnięcia wszystkich zmian.
    * cudowną rzeczą któej MyHeritage nie posiada, to możemy dodać przez kolejną podsekcję `Fakty` niestety tu również jest ograniczenie i jedynym faktem jaki możemy dodać to `Brak dzieci` z uzasadnieniem i tak samo jak wszedzie źródłami i historią zman.
    * kolejna podsekcja to `Notatki` tu możemy dodać swobodny tekst do 10000 znaków, z tytułem w osobnym polu,
    * i ostatnia podsekcja to `Źródła` tu mamy 3 opcje `Dodaj nowe źródło`, `Dodaj nowe źródło wspomnienia`, `Dołącz ze Schowka ze źródłami` to opiszę w sekcji źródeł by nie powielać.
    * a takze możemy zobaczyć historię wszystkich zmian.
  * FILIACJA: Druga podsekcja to edytowanie/dodawanie szczegółów na temat relacji dziecka z rodzicami, i to dotyczy zarówno relacji z rodzicami (wstępni) jak i relacji z dzieckiem (zstępni); i tu przy każdym z rodziców możemy podać relacje i jej uzasadnienie, z relacji mamy wybór: `Biologiczna`, `Aopcyjna`, `Zastępczy`, `Kuratela`, `Przybrana`, natomiast przy dziecku, podobnie jak w KOICJI, mamy datę dodania/ostatniej zmiany i info kto dodał, a także pole `uzasadnienia`. Tu tak samo mozemy dodać `Źródła`, `Notatki` i zobaczyć historię wszystkich zmian.
* kolejna sekcja to `Inne relacje` ona również jest kluczowa i brakuje jej w MyHeritage, ale jest ograniczona tylko do kilku relacji: `Domostwo`, `Krewny`,`Nauka zawodu`, `Niewola`, `Praca`, `Rodzic chrzestny`, `Sąsiad`, każda taka relacja w podsekcji ma podony zestaw informacji do uzupełnienia jak w podsekcji KOICJA czyli: przy każdej z dwojga osób mamy ostatnią zmianę i przez kogo; a także uzasadnie; możemy dodać specyficzne wydarzenia z tytułem `opis relacji` a następnie tak samo jak w poprzednich sekcjach (data,miejsce, uzasadnienie, historia zmian, lista śródeł potwierdzającyh); a także możemy dodać notatki, źródła, i zobaczyć historię zmian jak wszędzie.
* kolejna sekcja to `Inne informacje`
  * `Alternatywne imie i nazwisko` mamy tu do wyboru takie typy jak `Znany też jako`, `Imie i nazwisko przy urodzeniu`, `Imie i nazwisko po zawarciu małżeństwa`, `Przydomek` - a cały formularz jest identyczny jak w podsekcji `imię i nazwisko` w sekcji `kluczowe informacje`
  * `Wydarzenia` mamy tu do wyboru takie typy jak : `Przynależność`, `Bar micwa`, `Bat micwa`, `Kremacja`, `Imigracja`, `Służba wojskowa`, `Naturalizacja`, `Tytuł szlachecki`, `Zawód`, `Przynależność religijna`, `Miejsce zamieszkania`, `Martwo urodzone`, i najważniejsza pozycja `Wvdarzenie zdefiniowane` - czyli dowolne. W wiekszości wydażeń podajmy `Opis`, `Datę`, `Miejsce`, `Uzasadnienie` (to jak i każde inne w innym miejscu, może mieć 2000 znaków), a w wydarzeniu dowolnym dodatkowo `tytuł wydarzenia` dodatkowo jak wszędzie mamy listę źródeł, informacjie kto i kiedy zmienił, oraz możność zobaczenia wszystkich zmian.
  * `Fakty` mamy tu do wyboru takie typy jak: `Nazwa kasty`, `Nazwa klanu`, `Dokument tożsamości`, `Pochodzenie narodowe`, `Brak relacji pary`, `Brak dzieci`, `Opis fizyczny`, `Rasa`, `Plemię`, i najważniejsza pozycja  `Zdefiniowany fakt` - czyli dowolny. W wiekszości faktów podajmy `Opis` i `Uzasadnienie` , a w fakcie dowolnym dodatkowo `tytuł faktu` dodatkowo jak wszędzie mamy listę źródeł, informacjie kto i kiedy zmienił, oraz możność zobaczenia wszystkich zmian.
* kolejna sekcja to `Krótka historia życia` możemy tu opisać w notatce maks 10000 znaków krótką historię, dodatkowo mamy też pole uzasadnienia, a także możność zobaczenia histori zmian.
* kolejna sekcja to `Notatka` możemy tu opisać w notatce maks 10000 znaków cokolwiek, dodatkowo mamy też pole uzasadnienia, każdej notatce nadajemy też tytuł, a także możność zobaczenia histori zmian, oraz kto edytował i kiedy.
* kolejna sekcja to `Ostatnie zmiany` pozwala nam prześledzić wszystkie zmiany w obrębie profilu.

Na osobnej karcie mamy zarządzanie materiałami źródłowymi oraz Wspomnieniami (czyli miedzy innymi fotografiami). Na pojedynczej fotografii, możemy oznaczyć osoby, dodać plik audio z opowieścią o zdjeciu, mamy inforamcje kto zamieścił i kiedy, możemy dodać etykiety tematów na zdjęciu, dodać do albumy, opisać historię dotyczącą zdjęcia (dołączone zdjęcia(max 10), Tytuł histori, treść, miejsce, data); Album może zawierać tytuł i opic do 4000 znaków;

Natomiast na karcie źródła, `w dodawaniu nowego` podajemy datę, tytuł, rodzaj `url strony internetowej` albo `wspomnienie`, następnie mamy treść cytatu, uwagi, uzasadnienie, i etykiety czego to dotyczy: Imie i nazwisko, Płeć, Narodziny, Chrzest, Śmierć, Pochówek.; DOdatkowo mamy przechowywalnie źródeł `https://www.familysearch.org/pl/tree/sources/sourceBox` z której możemy istniejące źródło, dołaczyć do profilu. w każdym źródle mamy też uzasadnienie i historie edycji.

### **[A.3]** Gramps

opisane tu :

* `https://www.cisowscy.com/gene/grampsxml`
* `https://www.cisowscy.com/gene/gramps`

### **[A.4+]** Programy, Strony, Formaty;  (które mają interesujące funkcje)

> Przy koncepcjonowaniu naszego projektu `Arboretum`, pewne cechy i rozwiązania odwzorujemy z:
>
> * [GRAMPS (v: 6.0.6) [Python, SQLite]](https://github.com/gramps-project/gramps),
> * [GeneWeb (v: 7.1.0-beta2 [OCaml])](https://github.com/geneweb/geneweb),
> * [GOV GENEALOGY.NET](https://gov.genealogy.net/item/show/KOPINOJO84WS),
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
