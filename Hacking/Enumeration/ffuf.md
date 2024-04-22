
Il doit obligatoirement y avoir au minimum une wordlist `-w` et un url `-u` avec le keyword `FUZZ` quelque part dans la commande.

(utiliser la version 1.5 pour un output single line)
`go install github.com/ffuf/ffuf@v1.5.0`
## Paramètres communs

- `-c` : colorisation de l'output
- `-<f|m>c` : filtre ou match les code de status
- `-<f|m>s` : filtre ou match la taille de la réponse
- `-<f|m>r` : filtre ou match en donnant une regex
- `-H` : header specifique
- `-b` : cookies 
	- exemple : `-b "cookie1=FUZZ; cookie2=test"`
- `-d` : data dans un formulaire post par exemple
- `-e` : fuzz la wordlist avec des extensions 
	- exemple : `-e .php,.html,.js,.txt`
- `-recursion` : fuzz de manière récursive les dossiers trouvés 
	- exemple : `-recursion -recursion-depth 3`
- `-X POST` : permet de fuzz dans une requete POST
	- exemple : `-X POST -d "param1=FUZZ&param2=test"`

cf `ffuf --help` pour en savoir plus

#### Utilisation classique

```bash
ffuf -c -w w.txt -u "http://exemple.com/FUZZ"
```

#### Fuzzing DNS

```bash
ffuf -c -w w.txt -u "http://exemple.com/" -H "Host: http://FUZZ.exemple.htb/"
```

#### Enumeration d'utilisateurs

```bash
ffuf -c -w w.txt -u "http://exemple.com/login" -X POST -d "username=FUZZ&password=a" -fr "Invalid username"
```
"Invalid username" étant le message d'erreur envoyé par le server