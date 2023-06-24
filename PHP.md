# Przydatne komendy
- Uruchamianie Apache \
`/tmp/$USER/httpd/bin/apachectl start`

- Restart Apache \
`/tmp/$USER/httpd/bin/apachectl restart`

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

# Włączenie rozszerzenia SQLite3 dla PHP

1. Wejdź w plik konfiguracyjny PHP
```bash
cd /tmp/$USER/httpd/lib/php.ini
```

2. Odkomentuj poniższą linijkę \
`extension=sqlite3`

3. Zrestartuj serwer Apache
```bash
/tmp/$USER/httpd/bin/apachectl restart
```

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
3. Aby przetestować czy program działa, należy wejść na adres \
`http://localhost:8080/sprawozdanie_01/`

## Zadanie 3
--------------
Napisz kod obsługi ankiety. Pola ankiety to: imię, nazwisko, wiek, płeć oraz odpowiedź na
pytanie "Czy podoba Ci się PHP?". \
Odpowiedzi ankiety zapisywane są do pliku. Należy sprawdzać
poprawność wprowadzonych danych. Po wysłaniu ankiety wyświetlane są dane statystyczne, tj.
liczba zwolenników i przeciwników PHP.

1. Stwórz plik z rozszerzeniem `.php`, i umieść w nim analogiczny kod:
```php
<?php
function zapisz_ankiete($ankieta) {
    $linia = implode(',', $ankieta) . "\n";
    file_put_contents('ankieta.txt', $linia, FILE_APPEND);
}

function wczytaj_ankiety() {
    $ankiety = array();
    $tresc_pliku = file_get_contents('ankieta.txt');
    $linie = explode("\n", $tresc_pliku);

    foreach ($linie as $linia) {
        $ankieta = explode(',', $linia);
        if (count($ankieta) === 5) {
            $ankiety[] = $ankieta;
        }
    }

    return $ankiety;
}

function wyswietl_statystyki($ankiety) {
    $liczba_zwolennikow = 0;
    $liczba_przeciwnikow = 0;

    foreach ($ankiety as $ankieta) {
        if ($ankieta[4] === 'Tak') {
            $liczba_zwolennikow++;
        } elseif ($ankieta[4] === 'Nie') {
            $liczba_przeciwnikow++;
        }
    }

    echo 'Liczba zwolenników PHP: ' . $liczba_zwolennikow . '<br>';
    echo 'Liczba przeciwników PHP: ' . $liczba_przeciwnikow . '<br>';
}

function sprawdz_poprawnosc_danych($ankieta) {
    if (count($ankieta) !== 5) {
        return false;
    }

    $imie = $ankieta[0];
    $nazwisko = $ankieta[1];
    $wiek = $ankieta[2];
    $plec = $ankieta[3];
    $czy_podoba_sie_php = $ankieta[4];

    if (empty($imie) || empty($nazwisko) || empty($wiek) || empty($plec) || empty($czy_podoba_sie_php)) {
        return false;
    }

    return true;
}

// Obsługa formularza ankiety

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $ankieta = array(
        $_POST['imie'],
        $_POST['nazwisko'],
        $_POST['wiek'],
        $_POST['plec'],
        $_POST['czy_podoba_sie_php']
    );

    if (sprawdz_poprawnosc_danych($ankieta)) {
        zapisz_ankiete($ankieta);
        echo 'Dziękujemy za wypełnienie ankiety!';
    } else {
        echo 'Wprowadzone dane są niepoprawne. Proszę wypełnić formularz ponownie.';
    }
}

// Formularz ankiety

?>

<!DOCTYPE html>
<html>
<head>
    <title>Ankieta</title>
</head>
<body>
    <h1>Ankieta</h1>
    <form method="POST" action="">
        <label>Imię:</label>
        <input type="text" name="imie" required><br>

        <label>Nazwisko:</label>
        <input type="text" name="nazwisko" required><br>

        <label>Wiek:</label>
        <input type="number" name="wiek" required><br>

        <label>Płeć:</label>
        <select name="plec" required>
            <option value="">Wybierz</option>
            <option value="Mężczyzna">Mężczyzna</option>
            <option value="Kobieta">Kobieta</option>
        </select><br>

        <label>Czy podoba Ci się PHP?</label><br>
        <input type="radio" name="czy_podoba_sie_php" value="Tak" required> Tak<br>
        <input type="radio" name="czy_podoba_sie_php" value="Nie" required> Nie<br>

        <input type="submit" value="Wyślij ankietę">
    </form>

    <?php
    
    // Wyświetlanie statystyk
    $ankiety = wczytaj_ankiety();
    wyswietl_statystyki($ankiety);
    ?>
</body>
</html>
```
2. Przetestuj działanie programu, wchodząc na adres `http://localhost:8080/sprawozdanie_01/zadanie_03/formularz.php`

## Zadanie 4
--------------
Napisz krótki test badający stopień znajomości PHP. Po wypełnieniu testu skrypt wystawia
ocenę.

1. Stwórz plik z rozszerzeniem `.php`, i umieść w nim analogiczny kod:
```php
<?php
<?php
function oblicz_wynik($odpowiedzi, $poprawne_odpowiedzi) {
    $liczba_pytan = count($poprawne_odpowiedzi);
    $poprawne = 0;

    for ($i = 0; $i < $liczba_pytan; $i++) {
        if ($odpowiedzi[$i] === $poprawne_odpowiedzi[$i]) {
            $poprawne++;
        }
    }

    return $poprawne / $liczba_pytan * 100;
}

function ocen_test($wynik) {
    if ($wynik >= 90) {
        return '5';
    } elseif ($wynik >= 75) {
        return '4.5';
    } elseif ($wynik >= 65) {
        return '4';
    } elseif ($wynik >= 51) {
        return '3';
    } else {
        return '2';
    }
}

// Pytania, zestawy odpowiedzi i poprawne odpowiedzi
$pytania = array(
    "Pytanie 1: Co oznacza skrót PHP?",
    "Pytanie 2: Jak deklaruje się zmienną w PHP?",
    "Pytanie 3: Jak wyświetlić zawartość zmiennej w PHP?",
    "Pytanie 4: Jaką funkcję używa się do otwarcia pliku w PHP?",
    "Pytanie 5: Jak sprawdzić długość ciągu znaków w PHP?"
);

$zestawy_odpowiedzi = array(
    array("Hypertext Preprocessor", "PHP: Hypertext Preprocessor", "Personal Home Page", "Preprocessed Hypertext Processor"),
    array("$", "var", "int", "string"),
    array("echo", "print", "printf", "print_r"),
    array("fopen", "open_file", "file_open", "readfile"),
    array("strlen", "strlength", "strcount", "count")
);

$poprawne_odpowiedzi = array(
    "Hypertext Preprocessor",
    "$",
    "echo",
    "fopen",
    "strlen"
);

$liczba_pytan = count($pytania);

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $odpowiedzi = array();
    for ($i = 0; $i < $liczba_pytan; $i++) {
        $odpowiedzi[] = $_POST['odpowiedz' . $i];
    }

    $wynik = oblicz_wynik($odpowiedzi, $poprawne_odpowiedzi);
    $ocena = ocen_test($wynik);

    echo 'Twój wynik: ' . $wynik . '%<br>';
    echo 'Ocena: ' . $ocena;
}
?>

<!DOCTYPE html>
<html>
<head>
    <title>Test PHP</title>
</head>
<body>
    <h1>Test PHP</h1>
    <form method="POST" action="">
        <?php for ($i = 0; $i < $liczba_pytan; $i++) { ?>
            <p><?php echo $pytania[$i]; ?></p>
            <?php foreach ($zestawy_odpowiedzi[$i] as $j => $odpowiedz) { ?>
                <input type="radio" name="odpowiedz<?php echo $i; ?>" value="<?php echo $odpowiedz; ?>" <?php if ($j === 0) echo 'required'; ?>> <?php echo $odpowiedz; ?><br>
            <?php } ?>
        <?php } ?>

        <input type="submit" value="Sprawdź odpowiedzi">
    </form>
</body>
</html>
```
2. Przetestuj działanie programu, wchodząc na adres `http://localhost:8080/sprawozdanie_01/zadanie_04/test.php`

# Sprawozdanie #2

## Zadanie 1
--------------
Stworzyć bazę danych oraz tabele student. Pola tabeli to: id, imie, nazwisko, nr albumu, zdjęcie.
Napisać i przetestować funkcję w PHP, która wyświetli listę studentów (jako tabelka w html).

1. Utwórz skrypt `db.php`, który będzie zawierał skrypt tworzący bazę danych, analogicznie do poniższego kodu
```php
<?php
// Utworzenie bazy danych SQLite
$db = new SQLite3('students.db');

// Utworzenie tabeli "student" z polami
$query = 'CREATE TABLE IF NOT EXISTS student (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    imie TEXT NOT NULL,
    nazwisko TEXT NOT NULL,
    nr_albumu TEXT NOT NULL,
    zdjecie TEXT
)';
$db->exec($query);
$db->close();
?>
```
2. Wykonaj inserty przykładowych danych do bazy
```bash
sqlite3 students.db

INSERT INTO student (imie, nazwisko, nr_albumu, zdjecie) VALUES ('Jan', 'Kowalski', '123456', 'jan.jpg');
INSERT INTO student (imie, nazwisko, nr_albumu, zdjecie) VALUES ('Anna', 'Nowak', '987654', 'anna.jpg');
INSERT INTO student (imie, nazwisko, nr_albumu, zdjecie) VALUES ('Piotr', 'Wojcik', '456789', 'piotr.jpg');
```
3. Stwórz skrypt `students_list.php`, wyświetlający zawartość bazy w HTML
```php
<?php
function wyswietlListeStudentow() {
    // Połączenie z bazą danych SQLite
    $db = new SQLite3('students.db');

    // Pobranie danych studentów z tabeli
    $query = 'SELECT * FROM student';
    $result = $db->query($query);

    // Wyświetlenie tabelki z danymi studentów
    echo '<table>
        <thead>
            <tr>
                <th>ID</th>
                <th>Imie</th>
                <th>Nazwisko</th>
                <th>Nr albumu</th>
                <th>Zdjecie</th>
            </tr>
        </thead>
        <tbody>';

    while ($row = $result->fetchArray(SQLITE3_ASSOC)) {
        echo '<tr>
            <td>' . $row['id'] . '</td>
            <td>' . $row['imie'] . '</td>
            <td>' . $row['nazwisko'] . '</td>
            <td>' . $row['nr_albumu'] . '</td>
            <td>' . $row['zdjecie'] . '</td>
        </tr>';
    }

    echo '</tbody>
    </table>';

    // Zamkniecie połączenia z bazą danych
    $db->close();
}

// Wywołanie funkcji do wyświetlenia listy studentów
wyswietlListeStudentow();
?>
```
4. Uruchom pierwszy skrypt otwierając strone o adresie  \
`http://localhost:8080/sprawozdanie_02/db.php`

5. Przetestuj działanie skryptu wyświetlającego tabelę w HTMLu, wchodząc na adres \
`http://localhost:8080/sprawozdanie_02/students_list.php`

## Zadanie 2
--------------
Dodać do bazy tabele pomocnicze oceny (zawierająca oceny) i passwd (zawierająca hasła). Pola
tabel pomocniczych - według własnego uznania.

1. Uruchomić w terminalu sqlite3 z odpowiednią bazą danych
```bash
sqlite3 students.db
```
2. W sqlite3 wykonać poniższe zapytania SQL
```sql
CREATE TABLE IF NOT EXISTS oceny (
    id INT AUTO_INCREMENT PRIMARY KEY,
    student_id INT,
    przedmiot VARCHAR(50),
    ocena INT,
    FOREIGN KEY (student_id) REFERENCES student(id)
);

CREATE TABLE IF NOT EXISTS passwd (
    id INT AUTO_INCREMENT PRIMARY KEY,
    student_id INT,
    haslo VARCHAR(255),
    FOREIGN KEY (student_id) REFERENCES student(id)
);
```

## Zadanie 3
--------------
Napisać i przetestować funkcję w PHP, która służy do dodawania studentów do listy.

1. Stwórz plik `dodajStudenta.php`, z analogicznym kodem
```php
<?php
function dodajStudenta($imie, $nazwisko, $nr_albumu, $zdjecie) {
    // Połączenie z bazą danych SQLite
    $db = new SQLite3('students.db');

    // Przygotowanie zapytania INSERT
    $query = "INSERT INTO student (imie, nazwisko, nr_albumu, zdjecie) VALUES (:imie, :nazwisko, :nr_albumu, :zdjecie)";

    // Utworzenie przygotowanego zapytania
    $statement = $db->prepare($query);

    // Przypisanie wartości parametrów
    $statement->bindValue(':imie', $imie, SQLITE3_TEXT);
    $statement->bindValue(':nazwisko', $nazwisko, SQLITE3_TEXT);
    $statement->bindValue(':nr_albumu', $nr_albumu, SQLITE3_TEXT);
    $statement->bindValue(':zdjecie', $zdjecie, SQLITE3_TEXT);

    // Wykonanie zapytania INSERT
    $result = $statement->execute();

    // Zamknięcie połączenia z bazą danych
    $db->close();

    // Zwrócenie wyniku operacji (true/false)
    return $result;
}

// Przykład użycia funkcji
if (dodajStudenta('Jan', 'Kowalski', '123456', 'jan.jpg')) {
    echo 'Dodano studenta do listy.';
} else {
    echo 'Wystąpił błąd podczas dodawania studenta.';
}
?>
```

2. Stwórz plik `formularz_dodawania.php`, z analogicznym kodem
```php
<?php
require_once 'dodajStudenta.php';
?>

<!DOCTYPE html>
<html>
<head>
    <title>Dodawanie studenta</title>
</head>
<body>
    <h1>Dodawanie studenta</h1>

    <form method="post" action="">
        <label>Imię:</label>
        <input type="text" name="imie" required><br>

        <label>Nazwisko:</label>
        <input type="text" name="nazwisko" required><br>

        <label>Nr albumu:</label>
        <input type="text" name="nr_albumu" required><br>

        <label>Zdjęcie:</label>
        <input type="text" name="zdjecie" required><br>

        <input type="submit" name="submit" value="Dodaj">
    </form>

    <?php
    if (isset($_POST['submit'])) {
        $imie = $_POST['imie'];
        $nazwisko = $_POST['nazwisko'];
        $nr_albumu = $_POST['nr_albumu'];
        $zdjecie = $_POST['zdjecie'];

        if (dodajStudenta($imie, $nazwisko, $nr_albumu, $zdjecie)) {
            echo '<p>Student został dodany do listy.</p>';
        } else {
            echo '<p>Wystąpił błąd podczas dodawania studenta.</p>';
        }
    }
    ?>

</body>
</html>
```
3. Przetestuj działanie skryptów, otwierając adres \
`http://localhost:8080/sprawozdanie_02/formularz_dodawania.php`

## Zadanie 4
--------------
Napisać i przetestować funkcję w PHP do usuwania studentów. Selekcja studentów za pomocą
"checkbox-ów".

1. Stwórz plik `usuwanie_studentow.php`, i umieść w nim analogiczny kod
```php
<?php
function usunStudentow($studentIds) {
    // Połączenie z bazą danych SQLite
    $db = new SQLite3('students.db');

    // Przygotowanie zapytania DELETE
    $query = "DELETE FROM student WHERE id IN (".implode(',', $studentIds).")";

    // Wykonanie zapytania DELETE
    $result = $db->exec($query);

    // Zamknięcie połączenia z bazą danych
    $db->close();

    // Zwrócenie wyniku operacji (true/false)
    return $result;
}

// Przykład użycia funkcji
if (isset($_POST['submit'])) {
    $selectedStudents = $_POST['students'];

    if (usunStudentow($selectedStudents)) {
        echo 'Usunięto zaznaczonych studentów z listy.';
    } else {
        echo 'Wystąpił błąd podczas usuwania studentów.';
    }
}

// Wyświetlanie listy studentów z checkbox-ami
function wyswietlListeStudentow() {
    // Połączenie z bazą danych SQLite
    $db = new SQLite3('students.db');

    // Pobranie wszystkich studentów z bazy danych
    $query = "SELECT * FROM student";
    $result = $db->query($query);

    // Wyświetlanie tabelki z listą studentów
    echo '<form method="post" action="">';
    echo '<table>';
    echo '<tr><th>ID</th><th>Imię</th><th>Nazwisko</th><th>Nr albumu</th><th>Zdjęcie</th><th>Usuń</th></tr>';

    while ($row = $result->fetchArray()) {
        $id = $row['id'];
        $imie = $row['imie'];
        $nazwisko = $row['nazwisko'];
        $nr_albumu = $row['nr_albumu'];
        $zdjecie = $row['zdjecie'];

        echo '<tr>';
        echo '<td>'.$id.'</td>';
        echo '<td>'.$imie.'</td>';
        echo '<td>'.$nazwisko.'</td>';
        echo '<td>'.$nr_albumu.'</td>';
        echo '<td>'.$zdjecie.'</td>';
        echo '<td><input type="checkbox" name="students[]" value="'.$id.'"></td>';
        echo '</tr>';
    }

    echo '</table>';
    echo '<input type="submit" name="submit" value="Usuń zaznaczonych">';
    echo '</form>';

    // Zamknięcie połączenia z bazą danych
    $db->close();
}

// Wywołanie funkcji do wyświetlenia listy studentów
wyswietlListeStudentow();
?>
```
2. Przetestuj działanie skryptu, poprzez wejście na adres `http://localhost:8080/sprawozdanie_02/usuwanie_studentow.php`

## Zadanie 5
--------------
Napisać funkcję w PHP do zmiany/ustawiania hasła.

1. Stwórz plik `nowe_haslo_form.php`, z analogicznym kodem

```php
<?php
require_once 'funkcje_hasla.php';
?>

<!DOCTYPE html>
<html>
<head>
    <title>Zmiana / usuwanie hasła</title>
</head>
<body>
    <h1>Zmiana / usuwanie hasła</h1>

    <form action="zmiana_hasla.php" method="post">
        <label for="uzytkownikId">ID użytkownika:</label>
        <input type="number" name="uzytkownikId" required>

        <br><br>

        <label for="noweHaslo">Nowe hasło:</label>
        <input type="password" name="noweHaslo">

        <br><br>

        <input type="submit" name="zmienHaslo" value="Zmień hasło">
        <input type="submit" name="usunHaslo" value="Usuń hasło">
    </form>
</body>
</html>
```

2. Stwórz plik `zmiana_hasla.php` z analogicznym skryptem umożliwiającym zmiane haseł:
```php
<?php
// Definiowanie wymaganych funkcji
function zmienHaslo($studentId, $noweHaslo) {
    // Połączenie z bazą danych SQLite
    $db = new SQLite3('students.db');

    // Sprawdzenie, czy student istnieje w bazie danych
    $query = "SELECT * FROM student WHERE id = $studentId";
    $result = $db->query($query);

    if ($result->fetchArray()) {
        // Zaszyfrowanie nowego hasła
        $zaszyfrowaneHaslo = password_hash($noweHaslo, PASSWORD_DEFAULT);

        // Aktualizacja hasła studenta w tabeli passwd
        $query = "UPDATE passwd SET haslo = '$zaszyfrowaneHaslo' WHERE student_id = $studentId";
        $db->exec($query);

        // Zamknięcie połączenia z bazą danych
        $db->close();

        return true;
    } else {
        // Zamknięcie połączenia z bazą danych
        $db->close();

        return false;
    }
}

function usunHaslo($studentId) {
    // Połączenie z bazą danych SQLite
    $db = new SQLite3('students.db');

    // Sprawdzenie, czy student istnieje w bazie danych
    $query = "SELECT * FROM student WHERE id = $studentId";
    $result = $db->query($query);

    if ($result->fetchArray()) {
        // Usunięcie hasła studenta z tabeli passwd
        $query = "DELETE FROM passwd WHERE student_id = $studentId";
        $db->exec($query);

        // Zamknięcie połączenia z bazą danych
        $db->close();

        return true;
    } else {
        // Zamknięcie połączenia z bazą danych
        $db->close();

        return false;
    }
}
?>

<?php
if (isset($_POST['zmienHaslo'])) {
    // Sprawdzenie czy został przesłany formularz zmiany hasła
    $uzytkownikId = $_POST['uzytkownikId'];
    $noweHaslo = $_POST['noweHaslo'];

    if (zmienHaslo($uzytkownikId, $noweHaslo)) {
        echo 'Hasło zostało zmienione dla użytkownika o ID '.$uzytkownikId.'.';
    } else {
        echo 'Użytkownik o podanym ID nie istnieje.';
    }
} elseif (isset($_POST['usunHaslo'])) {
    // Sprawdzenie czy został przesłany formularz usunięcia hasła
    $uzytkownikId = $_POST['uzytkownikId'];

    if (usunHaslo($uzytkownikId)) {
        echo 'Hasło zostało usunięte dla użytkownika o ID '.$uzytkownikId.'.';
    } else {
        echo 'Użytkownik o podanym ID nie istnieje.';
    }
}
?>
```
3. Przetestuj działanie programu, wchodząc pod adres analogicznie do ścieżki pliku `http://localhost:8080/sprawozdanie_02/nowe_haslo_form.php`

## Zadanie 6
--------------
Napisać program w PHP do sprawdzania loginu i hasła. Jeśli są poprawne zwraca id
zalogowanego studenta.

1. Stwórz plik `login_check.php`, i umieść w nim analogiczny kod
```php
<?php
// Sprawdzenie, czy dane logowania zostały przesłane
if (isset($_POST['nr_albumu']) && isset($_POST['haslo'])) {
    // Pobranie danych logowania z formularza
    $nr_albumu = $_POST['nr_albumu'];
    $haslo = $_POST['haslo'];

    // Połączenie z bazą danych
    $db = new SQLite3('students.db');

    // Zapytanie SQL w celu weryfikacji danych logowania
    $query = "SELECT s.id FROM student s 
              INNER JOIN passwd p ON s.id = p.student_id 
              WHERE s.nr_albumu = :nr_albumu AND p.haslo = :haslo";

    // Przygotowanie i wykonanie zapytania SQL z użyciem zabezpieczeń przed SQL Injection
    $stmt = $db->prepare($query);
    $stmt->bindValue(':nr_albumu', $nr_albumu, SQLITE3_TEXT);
    $stmt->bindValue(':haslo', $haslo, SQLITE3_TEXT);
    $result = $stmt->execute();

    // Sprawdzenie, czy dane logowania są poprawne
    if ($row = $result->fetchArray(SQLITE3_ASSOC)) {
        $studentId = $row['id'];
        echo "Zalogowano studenta o ID: " . $studentId;
    } else {
        echo "Niepoprawne dane logowania.";
    }

    // Zamknięcie połączenia z bazą danych
    $db->close();
}
?>

<!DOCTYPE html>
<html>
<head>
    <title>Formularz logowania</title>
</head>
<body>
    <h2>Formularz logowania</h2>
    <form action="<?php echo $_SERVER['PHP_SELF']; ?>" method="post">
        <label for="nr_albumu">Nr albumu:</label>
        <input type="text" id="nr_albumu" name="nr_albumu" required>
        <br>
        <label for="haslo">Hasło:</label>
        <input type="password" id="haslo" name="haslo" required>
        <br>
        <input type="submit" value="Zaloguj">
    </form>
</body>
</html>
```
2. Przetestuj działanie programu, wchodząć na odpowiedni adres dostosowany do lokalizacji pliku. Przykładowo: `http://localhost:8080/sprawozdanie_02/login_check.php`