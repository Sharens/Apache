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
2. Przetestuj działanie skryptu, uruchamiając go `./var.cgi` oraz na adresie `http://localhost:8080/cgi-bin/var.cgi?arg1=foo&arg2=bar`

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
3. Przetestuj skrypt na adresie: `http://localhost:8080/cgi-bin/numbers.cgi?n=30`
