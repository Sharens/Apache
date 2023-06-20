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