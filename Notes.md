# Sprawozdanie #1

## Zadanie 1
-------------- 
Proszę utworzyć w głównym katalogu dokumentów serwera (htdocs)
podkatalog "katalog1", a w nim plik "start.txt", a następnie wyłączyć dla tego
katalogu automatyczne tworzenie indeksu i ustawić domyślne ładowanie pliku
"start txt". Następnie proszę ograniczyć dostęp do katalogu "katalog1" tylko
dla localhosta.

1. Stworzenie katalogu `katalog1` w folderze `htdocs` za pomocą komendy `mkdir`
2. Stworzenie pliku .txt za pomocą komendy `touch`
3. Włączamy `.htaccess` dodając w pliku `/tmp/$USER/httpd/conf/httpd.conf` następującą linijkę:
```
DocumentRoot "/tmp/lenovo/httpd/htdocs"
<Directory "/tmp/lenovo/httpd/htdocs">
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>
```
4. W katalog1 stworzenie pliku `.htaccess`
5. Aby wyłączyć automatyczne tworzenie indeksu należy w pliku `.htaccess` dodać linijkę: `Options -Indexes`
6. Aby ustawić domyślne ładowanie pliku start.txt należy w pliku `.htaccess` dodać linijkę: `DirectoryIndex start.txt`
7. Aby umożliwić dostęp do katalogu tylko localhostowi należy dopisać linijkę: 
```
Order Deny,Allow
Deny from all
Allow from 127.0.0.1
```

Finalnie plik `.htaccess` powinien wyglądać tak:
```
Options -Indexes

DirectoryIndex start.txt

Order Deny,Allow
Deny from all
Allow from 127.0.0.1
```

## Zadanie 2 
--------------
Proszę ustawić dla podkatalogu "/katalog2" przekierowanie stałe (kod 301) na
adres `www.kernel.org`.

1. Stworzyć katalog2 za pomocą komendy `mkdir katalog2`
2. Dodac w niej plik `.htaccess`
3. W `.htaccess` dodać następującą linijkę:
```
Redirect 301 /katalog2 http://www.kernel.org
```

## Zadanie 3
--------------
Proszę włączyć obsługę .htaccess w pliku konfiguracyjnym serwera. Ponadto
proszę utworzyć podkatalog "katalog3" w głównym katalogu dokumentów
serwera (htdocs), a w nim plik index.html. Proszę za pomocą programu
htpasswd wygenerować plik zawierający użytowników i hasła i wprowadzić
dla katalogu "katalog3" dostęp tylko dla użytkowników zdefiniowanych w tym
pliku. Na koniec proszę w pliku .htaccess ustawić przekierowanie dla kodu 404
na adres `www.wszib.edu.pl`.

1. Aby wygenerować plik zawierający użytkowników i hasła, należy użyć programu htpasswd. W tym celu należy wpisać w terminalu lub wierszu poleceń:
```
htpasswd -c /sciezka/do/pliku/.htpasswd nazwa_uzytkownika
```

2. Następnie zostaniemy poproszeni o wpisanie hasła dla użytkownika. Możemy dodać kolejnych użytkowników do pliku .htpasswd, wpisując:

```
htpasswd /sciezka/do/pliku/.htpasswd nazwa_uzytkownika
```

3. Aby ograniczyć dostęp do katalogu "katalog3" tylko dla użytkowników zdefiniowanych w pliku .htpasswd, należy dodać poniższe instrukcje do pliku .htaccess znajdującego się w katalogu "katalog3":
```
AuthType Basic
AuthName "Restricted Content"
AuthUserFile /sciezka/do/pliku/.htpasswd
Require valid-user
```
4. Aby ustawić przekierowanie dla kodu 404 na adres www.wszib.edu.pl, należy dodać do pliku .htaccess w katalogu "katalog3" następującą instrukcję:

```
ErrorDocument 404 http://www.wszib.edu.pl
```

### Wyjaśnienie:
- Instrukcja "AuthType Basic" określa, że autentykacja będzie odbywała się przy użyciu nazwy użytkownika i hasła.
- "AuthName" to nazwa wyświetlana na formularzu logowania.
- "AuthUserFile" to ścieżka do pliku .htpasswd.
- "Require valid-user" oznacza, że dostęp do katalogu wymaga podania prawidłowej nazwy użytkownika i hasła.
- Instrukcja "ErrorDocument" umożliwia ustawienie niestandardowej strony błędu dla kodu 404 (Not Found).


## Zadanie 4
----
Proszę utworzyć w katalogu htdocs dwa pliki startowe - w języku polskim i
angielskim, a następnie tak skonfigurować serwer tak, aby wyświetlana była
strona w preferowanym języku przeglądarki.
Aby skonfigurować serwer tak, aby wyświetlał stronę w preferowanym języku przeglądarki, należy wykonać następujące kroki:

1. W katalogu htdocs należy utworzyć dwa pliki startowe - jeden w języku polskim, a drugi w języku angielskim. Pliki powinny nazywać się index-pl.html oraz index-en.html.
2. Następnie należy dodać poniższe linie do pliku .htaccess znajdującego się w katalogu htdocs:
```
RewriteEngine On

# Sprawdzanie preferowanego języka przeglądarki
RewriteCond %{HTTP:Accept-Language} ^pl [NC]
RewriteRule ^$ /index-pl.html [L,R=301]

# Domyślnie wyświetlana strona w języku angielskim
RewriteRule ^$ /index-en.html [L,R=301]
```
3. Zapisz plik `.htaccess` i przeładuj stronę w przeglądarce.
<br>
<br>

# Sprawozdanie #2
## Zadanie 1
---
Napisać skrypt CGI wypisujący argumenty z linii poleceń.

### Uruchamianie CGI
1. Dodać linijkę (odkomentować) `LoadModule cgid_module modules/mod_cgid.so` w pliku konfiguracyjnym serwera Apache `httpd.conf`.

2. Odkomentuj dyrektywę `AddHandler cgi-script .cgi .pl`, która określi, jakie typy plików będą traktowane jako skrypty CGI w pliku konfiguracyjnym serwera Apache `httpd.conf`.

3. Odkomentuj dyrektywę `<Directory "/tmp/$USER/httpd/cgi-bin">`  w pliku konfiguracyjnym serwera Apache `httpd.conf` oraz dodaj w niej fragment kodu:
```
<Directory "/tmp/lenovo/httpd/cgi-bin">
    AllowOverride None
    Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
    Require all granted
</Directory>
```
### Testowanie skryptu
1. Stworzyć skrypt w formacie `.cgi` i dodać analogiczny kod:
```
#!/usr/bin/perl -wT 

use CGI;

# Utwórz nowy obiekt CGI
my $cgi = CGI->new();

# Pobierz argumenty z linii poleceń z parametrów CGI
my @args = $cgi->param();

# Wypisz argumenty
print $cgi->header('text/html');
print "<html><body>\n";
foreach my $arg (@args) {
  print "$arg: " . $cgi->param($arg) . "<br>\n";
}
print "</body></html>\n";
``` 
2. Zmienić uprawnienia na wyświetlanie pliku za pomocą komendy `chmod 755 var.cgi`
3. Przetestuj działanie skryptu, uruchamiając go `./var.cgi` oraz na adresie `http://localhost:8080/cgi-bin/var.cgi?arg1=foo&arg2=bar`

## Zadanie 2
---
Napisać skrypt CGI wypisujący argumenty z linii poleceń.
Napisać skrypt CGI, który wyświetla wszystkie liczby całkowite od 1 do N. N jest
przekazywane do skryptu poprzez linię adresu.

1. Wykonaj konfigurację CGI z powyższego zadania
2. Stwórz skrypt `numbers.cgi`, analogicznie do tego przykladu:
```
#!/usr/bin/perl -wT 

use CGI;

# Utwórz nowy obiekt CGI
my $cgi = CGI->new();

# Pobierz wartość parametru "n" z linii adresu
my $n = $cgi->param('n');

# Wypisz liczby od 1 do n
print $cgi->header('text/html');
print "<html><body>\n";
for (my $i = 1; $i <= $n; $i++) {
  print "$i<br>\n";
}
print "</body></html>\n";
```
3. Zmień uprawnienia na wyświetlanie pliku za pomocą komendy `chmod 755 numbers.cgi`
4. Przetestuj skrypt na adresie: `http://localhost:8080/cgi-bin/numbers.cgi?n=30`

## Zadanie 3
---
Napisać skrypt CGI, który wyświetla tablice znaków wraz z ich kodami ASCII.

1. Wykonaj konfigurację CGI z pierwszego zadania
2. Stwórz skrypt `ascii.cgi`, analogicznie do tego przykladu:
```
#!/usr/bin/perl -wT

use CGI;

# Utwórz nowy obiekt CGI
my $cgi = CGI->new();

# Tablica znaków i ich kody ASCII
my @chars = ('A'..'Z', 'a'..'z', '0'..'9', '+', '/');
my %ascii_codes = map { $_ => ord($_) } @chars;

# Wypisz tablicę znaków i ich kody ASCII
print $cgi->header('text/html');
print "<html><body>\n";
foreach my $char (@chars) {
  my $code = $ascii_codes{$char};
  print "$char: $code<br>\n";
}
print "</body></html>\n";
```
### Wyjaśnienie: 
> Ten skrypt definiuje tablicę znaków i ich kody ASCII za pomocą map() i hashu %ascii_codes. Następnie wykorzystuje pętlę foreach do wyświetlenia każdego znaku i jego kodu ASCII w formacie HTML.

3. Zmień uprawnienia na pliku `chmod 755 ascii.cgi`
4. Przetestuj skrypt na adresie `http://localhost:8080/cgi-bin/ascii.cgi`

## Zadanie 4
---
Napisać skrypt CGI, który wyświetla kalendarz danego miesiąca podanego w linii adresu.
Dni miesiąca umieścić w odpowiedniej tabelki.

1. Wykonaj konfigurację CGI z pierwszego zadania
2. Stwórz skrypt `calendar.cgi`, analogicznie do tego przykladu:
```
#!/usr/bin/perl -wT 

use CGI;
use Time::Local;

# Utwórz nowy obiekt CGI
my $cgi = CGI->new();

# Pobierz wartości parametrów "month" i "year" z linii adresu
my $month = $cgi->param('month');
my $year = $cgi->param('year');

# Utwórz obiekt Time::Local dla pierwszego dnia danego miesiąca
my $time = timelocal(0, 0, 0, 1, $month - 1, $year - 1900);
my ($sec, $min, $hour, $mday, $wday) = localtime($time);

# Ustal dzień tygodnia pierwszego dnia danego miesiąca (0 - niedziela, 1 - poniedziałek, itd.)
$wday = ($wday + 6) % 7;

# Pobierz ilość dni w danym miesiącu
my $last_day = (localtime(timelocal(0, 0, 0, 1, $month, $year - 1900) - 86400))[3];

# Wygeneruj kalendarz
print $cgi->header('text/html');
print "<html><body>\n";
print "<h1>Kalendarz dla $month/$year</h1>\n";
print "<table border=\"1\">\n";
print "<tr>\n";
print "<th>Nd</th><th>Pn</th><th>Wt</th><th>Sr</th><th>Cz</th><th>Pt</th><th>So</th>\n";
print "</tr>\n";
for (my $row = 0; $row < 6; $row++) {
  print "<tr>\n";
  for (my $col = 0; $col < 7; $col++) {
    if (($row == 0 && $col < $wday) || ($mday > $last_day)) {
      print "<td></td>\n";
    } else {
      print "<td>$mday</td>\n";
      $mday++;
    }
  }
  print "</tr>\n";
}
print "</table>\n";
print "</body></html>\n";
```

### Wyjaśnienie
> - `use CGI;` - zaimportowanie modułu CGI, który umożliwia tworzenie skryptów CGI w Perl.
> - `use Time::Local;` - zaimportowanie modułu Time::Local, który zawiera funkcje do pracy z czasem.
> - `my $cgi = CGI->new();` - utworzenie nowego obiektu CGI.
> - `my $month = $cgi->param('month');` - pobranie wartości parametru "month" z linii adresu.
> - `my $year = $cgi->param('year');` - pobranie wartości parametru "year" z linii adresu.
> - `my $time = timelocal(0, 0, 0, 1, $month - 1, $year - 1900);` - utworzenie obiektu `Time::Local` dla pierwszego dnia danego miesiąca. Funkcja `timelocal()` zwraca ilość sekund od 1 stycznia 1970 do danego momentu w czasie, czyli do pierwszego dnia podanego miesiąca.
> - `my ($sec, $min, $hour, $mday, $wday) = localtime($time);` - pobranie informacji o czasie z wykorzystaniem funkcji `localtime()`. Zwrócone wartości to: sekundy, minuty, godziny, dzień miesiąca i dzień tygodnia (0 - niedziela, 1 - poniedziałek, itd.).
> - `$wday = ($wday + 6) % 7;` - ustalenie dnia tygodnia pierwszego dnia danego miesiąca. Dodanie 6 do wartości $wday zapewnia, że niedziela zostanie zmapowana na 0, ponieważ reszta z dzielenia przez 7 jest równa 0.
> - `my $last_day = (localtime(timelocal(0, 0, 0, 1, $month, $year - 1900) - 86400))[3];` - obliczenie ilości dni w danym miesiącu. Funkcja localtime() zwraca informacje o czasie w postaci tablicy, a `[3]` oznacza dzień miesiąca.
> - `print $cgi->header('text/html');` - wygenerowanie nagłówka HTTP.
> - `print "<html><body>\n";` - otwarcie znacznika <html> i <body>.
> - `print "<h1>Kalendarz dla $month/$year</h1>\n";` - wygenerowanie nagłówka z informacją o wyświetlanym miesiącu.
> - `print "<table border=\"1\">\n";` - otwarcie znacznika <table> z atrybutem `border="1"`.
> - `print "<th>Nd</th><th>Pn</th><th>Wt</th><th>Sr</th><th>Cz</th><th>Pt</th><th>So</th>\n";` - wygenerowanie nagłówków kolumn tabeli.

3. Zmień uprawnienia na pliku `chmod 755 calendar.cgi`
4. Przetestuj skrypt na adresie `http://localhost:8080/cgi-bin/calendar.cgi?month=4&year=2023`


## Zadanie 5
---
Napisać skrypt CGI, który wyświetla liczbę odwiedzin

1. Wykonaj konfigurację CGI z pierwszego zadania
2. Stwórz skrypt `counter.cgi`, analogicznie do tego przykladu:
```
#!/usr/bin/perl -wT 

use strict;
use warnings;
use CGI qw(:standard);

# Pobierz wartość z pliku licznika
my $file = "counter.txt";
open(my $fh, '<', $file) or die "Nie można otworzyć pliku '$file': $!";
my $count = <$fh>;
close($fh);

# Zwiększ wartość licznika
$count++;
open($fh, '>', $file) or die "Nie można otworzyć pliku '$file': $!";
print $fh $count;
close($fh);

# Wyświetl stronę z licznikiem
print header();
print start_html();
print "<h1>Liczba odwiedzin: $count</h1>";
print end_html();
```
### Wyjaśnienie
> - W linijkach 3-4 dodane są dyrektywy `"use strict"` i `"use warnings"`, które wymuszają na skrypcie restrykcyjne podejście do deklarowania zmiennych i wykrywają ewentualne problemy związane z niepoprawnym użyciem funkcji, zmiennych itp.
> - W linijce 6 zaimportowano moduł CGI oraz jego funkcje standardowe przy pomocy `qw(:standard)`.
> - W linijkach 9-11 otwierany jest plik `"counter.txt"` i pobierana jest z niego wartość licznika, która zostaje zapisana do zmiennej `$count`.
> - W linijkach 14-16 wartość $count zostaje zwiększona o 1 i zapisana do pliku `"counter.txt"`.
> - W linijkach 19-21 wykorzystując funkcje standardowe modułu CGI, wyświetlana jest strona z wartością licznika z pliku `"counter.txt"`. Najpierw wyświetlany jest nagłówek strony (header), następnie jej początek (start_html), treść strony, a na końcu jej koniec (end_html).

3. Zmień uprawnienia na pliku `chmod 755 counter.cgi`
4. Przetestuj skrypt na adresie `http://localhost:8080/cgi-bin/counter.cgi`

## Zadanie 6
---
Napisać skrypt CGI, który wyświetla zawartość pliku, którego nazwa przekazywana jest
poprzez linie adresu.

1. Wykonaj konfigurację CGI z pierwszego zadania
2. Stwórz skrypt `display_file.cgi`, analogicznie do tego przykladu:
```
#!/usr/bin/perl -wT 

use strict;
use warnings;
use CGI qw(:standard);

# Pobierz nazwę pliku z linii adresu
my $filename = param('filename');

# Otwórz plik i odczytaj jego zawartość
open(my $fh, '<', $filename) or die "Nie można otworzyć pliku '$filename': $!";
my $content = do { local $/; <$fh> };
close($fh);

# Wyświetl zawartość pliku
print header();
print start_html();
print "<h1>Zawartosc pliku $filename</h1>";
print "<pre>$content</pre>";
print end_html();
```

3. Stwórz plik tekstowy z losową zawartością, nazwij go `plik.txt`
4. Zmień uprawnienia dla pliku korzystając z komeny `chmod 755 display_file.cgi`
5. Przetestuj skrypt, wchodząc na stronę: `http://localhost:8080/cgi-bin/display_file.cgi?filename=plik.txt`