
## Paramètres communs

`-Pn` : Ignore le host discovery (ping initial)
`-A` : Aggressive scan : équivalent de `-sC -sV -O --traceroute`
`-oN` : output to file
`-T<0-5>` : Timing allant de 0 à 5. 
	0 = très lent mais discret. (paranoid)
	5 = très rapide mais très bruyant (insane)

## Workflow classique :
#### Scan rapide :
```bash
nmap -F <IP>
```
Permet de rapidement trouver quelques ports pour commencer a creuser.
#### Scan complet de tous les ports :
```bash
nmap -p- <IP>
```
pour éviter de rater un port ouvert après avoir utilisé le scan rapide.

#### Detection d'OS + basic scripts :
```bash
nmap -sC -sV <IP> -p<ports séparés dune virgule>
```

