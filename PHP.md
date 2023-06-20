# Przydatne komendy
- Uruchamianie Apache \
`/tmp/$USER/httpd/bin/apachectl start`

# Instalacja PHP

Wykonujemy poniższe komendy:

```bash
cd  /tmp/$USER
tar jxvf /nfs/darin/pub/prog/php-8.2.4.tar.bz2
cd php-8.2.4
./configure --prefix=/tmp/$USER/httpd --with-apxs2=/tmp/$USER/httpd/bin/apxs --enable-gd --with-zlib --enable-phpdbg --with-readline
make -j`grep -c ^processor /proc/cpuinfo`
make install
cp php.ini-development /tmp/$USER/httpd/lib/php.ini
cd ..
rm -rf php-8.2.4
strip /tmp/$USER/httpd/bin/*
```

Ponadto należy dopisać do pliku /tmp/$USER/httpd/conf/httpd.conf następującą linię: \
`AddType application/x-httpd-php .php`


# Sprawozdanie #1

## Zadanie 1
--------------
Napisz formularz i obsługujący go kod w PHP realizujący funkcję zgłaszania danych dla potrzeb
rejestracji nowego użytkownika. Kod obsługujący formularz będzie tylko wyświetlał przyjęte dane.

1. Stworzyć nowy plik `.php` (najlepiej w katalogu `htdocs`)
2. Umieścić w nim kod analogiczny do poniższego:

```php
<?php
echo "Hello world\n"; 
phpinfo(); 
?>
```
3. Przetestuj uruchamianie kodu poprzez wpisanie w terminalu: \

```bash
php <nazwa_pliku>.php
```

## Zadanie 2
--------------
Napisz formularz i obsługujący go kod w PHP realizujący funkcję zgłaszania danych dla potrzeb rejestracji nowego użytkownika. 
Kod obsługujący formularz będzie tylko wyświetlał przyjęte dane.

Linki źródłowe:
- [Szkielet programu od prowadzącego](https://artemis.wszib.edu.pl/~darin/ijz/zmienne.txt)
- [Przykład formularza od prowadzącego](https://artemis.wszib.edu.pl/~darin/ijz/zmienne.html)


1. W `htdocs` utwórz nowy katalog, następnie stwórz w nim nowy plik z `.html` z analogicznym kodem:
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Rejestracja nowego użytkownika</title>
</head>
<body>
  <h1>Rejestracja nowego użytkownika</h1>
  <form method="POST" action="formularz.php">
    <label for="name">Imię:</label>
    <input type="text" name="name" id="name" required><br><br>
    <label for="email">Email:</label>
    <input type="email" name="email" id="email" required><br><br>
    <label for="password">Hasło:</label>
    <input type="password" name="password" id="password" required><br><br>
    <input type="submit" value="Zarejestruj">
  </form>
</body>
</html>
```
2. W tym samym katalogu utwórz plik `formularz.php` z analogicznym kodem
```php
<?php
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
  $name = $_POST['name'];
  $email = $_POST['email'];
  $password = $_POST['password'];

  echo "<h1>Przyjęte dane:</h1>";
  echo "<p>Imię: $name</p>";
  echo "<p>Email: $email</p>";
  echo "<p>Hasło: $password</p>";
} else {
  echo "<h1>Błąd: Nieprawidłowy sposób żądania</h1>";
}
?>
```