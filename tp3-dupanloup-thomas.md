# TP2 - Bash
## Dupanloup - Thomas

### Exercice 1 - Commandes de base

__1. Quels sont les 5 derniers paquets installés sur votre machine ?__

$grep "install " /var/log/dpkg.log

On peut récupérer les 5 dernières lignes qui nous montrent quels paquets ont étés installés.(ex: linux-headers)

__2. Utiliser dpkg et apt pour compter le nombre de paquets installés (ne pas hésiter à consulter le manuel !).
Comment explique-t-on la (petite) différence de comptage ?__

$dpkg -l | grep -c "ii"

__3. Combien de paquets sont disponibles en téléchargement ?__
__4. Créer un alias “maj” qui met à jour le système__
__5. A quoi sert le paquet fortunes ? Installez-le.__
__6. Quels paquets proposent de jouer au sudoku ?__
__7. Lister les derniers paquets installés explicitement avec la commande apt install__


### Exercice 2
