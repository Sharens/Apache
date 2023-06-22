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
    <title>Formularz rejestracyjny</title>
</head>
<body>
    <h2>Formularz rejestracyjny</h2>
    <form action="formularz.php" method="post">
        <label for="username">Nazwa uzytkownika:</label>
        <input type="text" id="username" name="username" required><br><br>

        <label for="email">Adres email:</label>
        <input type="email" id="email" name="email" required><br><br>

        <label for="password">Haslo:</label>
        <input type="password" id="password" name="password" required><br><br>

        <input type="submit" value="Zarejestruj">
    </form>
</body>
</html>
```
2. W tym samym katalogu utwórz plik `formularz.php` z analogicznym kodem
```php
<!DOCTYPE html>
<html>
<head>
    <title>Rejestracja - Potwierdzenie danych</title>
</head>
<body>
    <h2>Potwierdzenie danych rejestracyjnych</h2>

    <?php
    if ($_SERVER["REQUEST_METHOD"] == "POST") {
        $username = $_POST["username"];
        $email = $_POST["email"];
        $password = $_POST["password"];

        echo "<p>Nazwa uzytkownika: $username</p>";
        echo "<p>Adres email: $email</p>";
        echo "<p>Haslo: $password</p>";
    }
    ?>
</body>
</html>
?>
```