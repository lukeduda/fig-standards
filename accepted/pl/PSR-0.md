Standardy autoloadingu
====================

> **Deprecated** - Od dnia 2014-10-21 standard PSR-0 jest standardem zdeprecjonowanym. [PSR-4] jest od teraz rekomendowany jako alternatywa.

[PSR-4]: http://www.php-fig.org/psr/psr-4/

Poniższy dokument opisuje obowiązkowe wymagania, które należy stosować w procesie standaryzacji autoloadingu klas.

Wymagania
---------

* W pełni poprawna przestrzeń nazw (namespace) i klasa muszą posiadać następującą strukturę `\<Nazwa Vendora>\(<Namespace>\)*<Nazwa klasy>`
* Każdy namespace musi posiadać namespace bazowy ("Nazwa Vendora").
* Każdy namespace może posiadać tyle namespace’ów dziedziczących ile chce.
* Każdy separator między namespace’ami jest zamieniany na stałą `DIRECTORY_SEPARATOR` podczas ładowania z systemu plików.
* Każdy znak `_` w nazwie klasy jest konwertowany do wartości stałej `DIRECTORY_SEPARATOR`. Znak `_` nie ma żadnego specjalnego znaczenia w nazwie namespace’a.
* W pełni poprawny plik namespace’a i klasy musi kończyć się rozszerzeniem `.php` podcza ładowania z systemu plików.
* Wielkość znaków (duże/małe litery) w nazwie vendora, namespace’a czy klasy nie odgrywa żadnego znaczenia.

Przykłady
--------

* `\Doctrine\Common\IsolatedClassLoader` => `/path/to/project/lib/vendor/Doctrine/Common/IsolatedClassLoader.php`
* `\Symfony\Core\Request` => `/path/to/project/lib/vendor/Symfony/Core/Request.php`
* `\Zend\Acl` => `/path/to/project/lib/vendor/Zend/Acl.php`
* `\Zend\Mail\Message` => `/path/to/project/lib/vendor/Zend/Mail/Message.php`

Znaki podkreślenia w nazwach namespace’ów i klas
-----------------------------------------

* `\namespace\package\Class_Name` => `/path/to/project/lib/vendor/namespace/package/Class/Name.php`
* `\namespace\package_name\Class_Name` => `/path/to/project/lib/vendor/namespace/package_name/Class/Name.php`

Opisane powyżej reguły powinny być najmniej bolesną odpowiedzią na problem standaryzacji autoloadingu klas w PHP. W każdym momencie można wypróbować działanie powyższych standardów poprzez implementację klasy SplClassLoader – będzie ona działać poprawnie już dla projektów opartych o wersję PHP 5.3.

Przykładowa implementacja
----------------------

Poniżej przedstawiono przykładową funkcję do demonstracji jak powinna wyglądać implementacja autloadingu na podstawie powyższych standardów.

```php
<?php

function autoload($className)
{
    $className = ltrim($className, '\\');
    $fileName  = '';
    $namespace = '';
    if ($lastNsPos = strrpos($className, '\\')) {
        $namespace = substr($className, 0, $lastNsPos);
        $className = substr($className, $lastNsPos + 1);
        $fileName  = str_replace('\\', DIRECTORY_SEPARATOR, $namespace) . DIRECTORY_SEPARATOR;
    }
    $fileName .= str_replace('_', DIRECTORY_SEPARATOR, $className) . '.php';

    require $fileName;
}
spl_autoload_register('autoload');
```

Implementacja SplClassLoader
-----------------------------

W poniższym linku znajduje się klasa SplClassLoader implementująca powyższe standardy dotyczące autoloadingu klas. Jest to aktualnie zalecane podejście do procesu ładowania klas dla projektów od wersji 5.3 PHP.

* [http://gist.github.com/221634](http://gist.github.com/221634)

