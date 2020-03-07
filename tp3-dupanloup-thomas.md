# TP3 - Bash
## Dupanloup - Thomas

### Exercice 1 - Commandes de base

__1. Quels sont les 5 derniers paquets installés sur votre machine ?__

```bash
grep "install " /var/log/dpkg.log
```
On peut récupérer les 5 dernières lignes qui nous montrent quels paquets ont étés installés.(ex: linux-headers)

__2. Utiliser dpkg et apt pour compter le nombre de paquets installés (ne pas hésiter à consulter le manuel !).
Comment explique-t-on la (petite) différence de comptage ?__

Par dpkg ```bash -l | grep -c " ii " ``` on obtient 548 packets installés.
Par apt list --installed | wc -l on obtient 549 packets installés mais il faut en enlever un car la premiere ligne affichée par ```bash apt list --installed ``` n'est pas un paquet installé.

__3. Combien de paquets sont disponibles en téléchargement ?__

 On obtient le nombre de paquets disponibles par téléchargement
```bash sudo apt list | wc -l ``` et on obtient 61599 

__4. Créer un alias “maj” qui met à jour le système__
```bash alias mj="sudo apt update && sudo apt dist-upgrade && sudo apt autoremove" ``` permet de faire un alias de mise à jour qui upgrade même le systeme de distribution et supprime les paquets qui ne sont plus utilisés.

__5. A quoi sert le paquet fortunes ? Installez-le.__

On installe le package en utilisant la commande ```bash sudo apt install fortunes -y```
Ce packages est une commande $fortune qui renvoit des messages de "fortune cookie" comme on trouve dans les fameux biscuits.  

__6. Quels paquets proposent de jouer au sudoku ?__
On utilise la commande ```bash apt search sudoku``` pour voir l'ensemble des packages qui contiennet dans leur description le mot sudoku. 

__7. Lister les derniers paquets installés explicitement avec la commande apt install__
On utilise la commande ```bash grep -c "apt install" /var/log/apt/history.log 	```


### Exercice 2

__A partir de quel paquet est installée la commande ls ? Comment obtenir cette information en une seule
commande, pour n’importe quel programme (indice : la réponse est dans le poly de cours 2, dans la liste des
commandes utiles) ? Utilisez la réponse à pour écrire un script appelé origine-commande (sans l’extension
.sh) prenant en argument le nom d’une commande, et indiquant quel paquet l’a installée.__

```bash which ls | xargs dpkg -S | cut -d ":" -f 1 ``` est la commande qui permet de trouver le packet à partir duquel la commande ls a été installée. 

Pour créer un exectuable qui utilise la commande vi et on entre juste la ligne de code 
```bash which $1 | xargs dpkg -S | cut -d ":" -f 1 ```

Il faudra penser à utiliser la commande ```bash chmod u+x origine-commande ``` pour rendre le fichier executable. 

### Exercice 3

__Ecrire une commande qui affiche “INSTALLÉ” ou “NON INSTALLÉ” selon le nom et le statut du package
spécifié dans cette commande.__

Afin de créer un script répondant aux attentes j'ai utilisé le code suivant : 
```bash
#!/bin/bash
test=$(apt list --installed | grep -c $1)
[ "$test" -gt 0 ] && echo "INSTALLED" && exit;
echo "UNINSTALLED";
```

### Exercice 4

__Lister les programmes livrés avec coreutils. A quoi sert la commande ```[``` et comment afficher ce qu’elle
retourne ?__

On utilise le script dpkt -s coreutils pour avoir une description et notamment la liste des commandes contenues. 
La commande ```[``` permet de faire une comparaison : comparer le type d'un fichier ou sa valeur. Elle renvoit un booléen Vrai/Faux.
Pour voir le résultat on peut utiliser la commande : ```bash[ <parametre1> <condition> <parametre2> ] && echo "TRUE" ;```
Si elle renvoit TRUE alors elle est vrai, sinon elle ne renvoit rien. 


### Exercice 5

On instale aptitude avec la commande ```sudo apt install aptitude ```
On lance aptitude avec la commande ```aptitude``` qui nous ouvre l'interface graphique
On utilise ```/``` pour rechercher le paquet emacs 
On l'installe ensuite en le selectionnant avec +


### Exercice 6 

Certains logiciels ne figurent pas dans les dépôts officiels. C’est le cas par exemple de la version ”officielle”
de Java depuis qu’elle est développée par Oracle. Dans ces cas, on peut parfois se tourner vers un ”dépôt
personnel” ou PPA.

__1. Installer la version Oracle de Java (avec l’ajout des PPA)__

Pour cela on ajoute le repository : ```sudo add-apt-repository ppa:linuxuprising/java```
On fait ensuite la mise à jour de notre librairie : ```sudo apt update```
On installe ensuite la version Oracle de Java : ```sudo apt install oracle-java13-installer```
On noterra que l'on installe la version 13 et non 11 comme demandé dans l'énoncé

__2. Vérifiez qu’un nouveau fichier a été créé dans /etc/apt/sources.list.d. Que contient-il ?__
Un fichier a bien été créé il est nommé : linuxuprising-ubuntu-java-eoan.list 
Il contient l'adresse source du repository ajouté. 

### Exercice 7. Création de dépôt personalisé

### Création d’un paquet Debian avec dpkg-deb

1. Placement dans le dossier scripts :
```cd ~/scripts```

Création du sous-dossier **origine-commande**, d'un autre sous-dossier **DEBIAN** ainsi que l'arborescence **usr/local/bin** :  
```mkdir -p origine-commande/DEBIAN```  
```mkdir -p usr/local/bin```  

2. Placement dans le bon dossier :
```cd origine-commande/DEBIAN```
Création du fichier **control**:  
```
nano control 
```
il contient: 
```
Package: origine-commande 
Version: 0.1 #numéro de version
Maintainer: DUPANLOUP-THOMAS 
Architecture: all 
Description: Cherche l'origine d'une commande
Section: utils 
Priority: optional 
```  
3. Construction du paquet que nous avons créé :
```
cd $HOME
dpkg-deb --build origine-commande
```

### Création du dépôt personnel avec reprepro

1. Nous créons la racine de notre dépôt : **repo-cpe**  
```
mkdir repo-cpe
```
2. Nous y ajoutons ensuite les sous-dossiers **conf ** et **packages **:  
```
cd repo-cpe 
mkdir conf
mkdir packages
```
3. Dans le dossier **conf** nous créons un fichier **distributions** :  
```
cd conf 
nano distributions 
```
il contient:
```
Origin: DUPANLOUP-THOMAS
Label: repo-cpe
// Suite: stable 
Codename: cosmic 
Architectures: i386 amd64 
Components: universe  
Description: Une description du dépôt 
```
4. Nous devons d'abord installer le paquet **reprepro** puis nous génèrerons l'arborescence du dépôt avec la commande ```reprepro -b . export ```

5. Nous copions le paquet **origine-commande.deb** dans la racine et dans le dossier **packages** avec la commande :  
```
cp ~/script/origine-commande.deb ./packages
cp ~/script/origine-commande.deb 
```
Ensuite nous éxecutons la commande suivante pour inscrire notre paquet dans le dépôt :  
```
reprepro -b . includedeb cosmic origine-commande.deb
```
6.On crée un nouveau fichier **repo-cpe.list** dans le fichier **/etc/apt/sources.list.d** :   
``` sudo nano /etc/apt/sources.list.d/repo-cpe.list``` et on ajoute la configuration du dépôt
```
deb file:/home/DUPANLOUP-THOMAS/repo-cpe cosmic multiverse
```
7. Nous lancons la commande ``` sudo apt update```et nous pouvons remarquer que notre dépôt ne peut être pris en compte puisque non signé.

### Signature du dépôt avec GPG

1. Génération d'une nouvelle paire de clé : 
``` 
gpg --gen-key
Real name: DUPANLOUP-THOMAS
Email address: shana.dupanloup@cpe.fr
passphrase : **********
```
2. Avec nano nous rajoutons ```SignWith: yes``` au fichier **distributions**

3. Nous ajoutons ensuite la clé à notre dépôt en éxecutant la commande:  
```reprepro --ask-passphrase -b . export```

4. Ajout de notre clé publique à notre dépôt avec la commande :  
```gpg --export -a "auteur" > public.key```

5. Ajout de cette clé à la liste des clés fiables connues de apt :  
```sudo apt-key add public.key```


 ### Exercice 8
