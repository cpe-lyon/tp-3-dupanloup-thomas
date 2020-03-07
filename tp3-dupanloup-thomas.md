# TP2 - Bash
## Dupanloup - Thomas

### Exercice 1 - Commandes de base

__1. Quels sont les 5 derniers paquets installés sur votre machine ?__

$grep "install " /var/log/dpkg.log

On peut récupérer les 5 dernières lignes qui nous montrent quels paquets ont étés installés.(ex: linux-headers)

__2. Utiliser dpkg et apt pour compter le nombre de paquets installés (ne pas hésiter à consulter le manuel !).
Comment explique-t-on la (petite) différence de comptage ?__

Par dpkg -l | grep -c " ii " on obtient 548 packets installés.
Par apt list --installed | wc -l on obtient 549 packets installés mais il faut en enlever un car la premiere ligne affichée par apt list --installed n'est pas un paquet installé.

__3. Combien de paquets sont disponibles en téléchargement ?__

 On obtient le nombre de paquets disponibles par téléchargement
 sudo apt list | wc -l et on obtient 61599 

__4. Créer un alias “maj” qui met à jour le système__
alias mj="sudo apt update && sudo apt dist-upgrade && sudo apt autoremove" permet de faire un alias de mise à jour qui upgrade même le systeme de distribution et supprime les paquets qui ne sont plus utilisés.
__5. A quoi sert le paquet fortunes ? Installez-le.__

On installe le package en utilisant la commande sudo apt install fortunes -y
Ce packages est une commande $fortune qui renvoit des messages de "fortune cookie" comme on trouve dans les fameux biscuits.  

__6. Quels paquets proposent de jouer au sudoku ?__
On utilise la commande apt search sudoku pour voir l'ensemble des packages qui contiennet dans leur description le mot sudoku. 

__7. Lister les derniers paquets installés explicitement avec la commande apt install__
On utilise la commande $grep -c "apt install" /var/log/apt/history.log 	


### Exercice 2

__A partir de quel paquet est installée la commande ls ? Comment obtenir cette information en une seule
commande, pour n’importe quel programme (indice : la réponse est dans le poly de cours 2, dans la liste des
commandes utiles) ? Utilisez la réponse à pour écrire un script appelé origine-commande (sans l’extension
.sh) prenant en argument le nom d’une commande, et indiquant quel paquet l’a installée.__

which ls | xargs dpkg -S | cut -d ":" -f 1 est la commande qui permet de trouver le packet à partir duquel la commande ls a été installée. 

Pour créer un exectuable qui utilise la commande vi et on entre juste la ligne de code 
which $1 | xargs dpkg -S | cut -d ":" -f 1 

Il faudra penser à utiliser la commande chmod u+x origine-commande pour rendre le fichier executable. 

### Exercice 3

__Ecrire une commande qui affiche “INSTALLÉ” ou “NON INSTALLÉ” selon le nom et le statut du package
spécifié dans cette commande.__

Afin de créer un script répondant aux attentes j'ai utilisé le code suivant : 

#!/bin/bash
test=$(apt list --installed | grep -c $1)
[ "$test" -gt 0 ] && echo "INSTALLED" && exit;
echo "UNINSTALLED";

### Exercice 4

__Lister les programmes livrés avec coreutils. A quoi sert la commande ’[’ et comment afficher ce qu’elle
retourne ?__

On utilise le script dpkt -s coreutils pour avoir une description et notamment la liste des commandes contenues. 
La commande [ permet de faire une comparaison : comparer le type d'un fichier ou sa valeur. Elle renvoit un booléen Vrai/Faux.
Pour voir le résultat on peut utiliser la commande : [ <parametre1> <condition> <parametre2> ] && echo "TRUE" ;
Si elle renvoit TRUE alors elle est vrai, sinon elle ne renvoit rien.


### Exercice 5

__Installez le paquet emacs à l’aide de la version graphique d’aptitude.__

On instale aptitude avec la commande sudo apt install aptitude
On lance aptitude avec la commande $aptitude qui nous ouvre l'interface graphique
On utilise "/" pour rechercher le paquet emacs 
On l'installe ensuite en le selectionnant avec +

## Exercice 6

__Certains logiciels ne figurent pas dans les dépôts officiels. C’est le cas par exemple de la version ”officielle”
de Java depuis qu’elle est développée par Oracle. Dans ces cas, on peut parfois se tourner vers un ”dépôt
personnel” ou PPA.__

__1. Installer la version Oracle de Java (avec l’ajout des PPA)__
Pour cela on utilise les 3 commandes différentes
