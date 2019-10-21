# ROSSI Thomas

# TP 7 - Boot, services et processus / Tâches d’administration

## Exercice 1. Personnalisation de GRUB

**1. Commencez par changer l’extension du fichier /etc/default/grub.d/50-curtin-settings.cfg s’il
est présent dans votre environnement (vous pouvez aussi commenter son contenu).**

```
# Curtin disable grub os prober that might find other OS installs.
#GRUB_DISABLE_OS_PROBER=true
#GRUB_TERMINAL=console
```

Fichier commenter

**2. Modifiez le fichier /etc/default/grub pour que le menu de GRUB s’affiche pendant 10 secondes ;
passé ce délai, le premier OS du menu doit être lancé automatiquement**

```
GRUB_TIMEOUT=10
```
J'ai changer le GRUB_TIMEOUT puis update-grub

**3. Lancez la commande update-grub**

FAIT

**4. Redémarrez votre VM pour valider que les changements ont bien été pris en compte**

FAIT et fonctionne

**5. On va augmenter la résolution de GRUB et de notre VM. Cherchez sur Internet le ou les paramètres
à rajouter au fichier grub**

```
GRUB_GFXMODE=1920x1080,1024x768x32
```

**6. On va à présent ajouter un fond d’écran. Il existe un paquet en proposant quelques uns : grub2-splash-images
(après installation, celles-ci sont disponibles dans /usr/share/images/grub).**

```
GRUB_BACKGROUND="/usr/share/images/grub/050817-N-3488C-028.tga"
```

**7. Il est également possible de configurer des thèmes. On en trouve quelques uns dans les dépôts (grub2-themes-*).
Installez-en un**

```
GRUB_THEME="/boot/grub/themes/ubuntu-mate/menu_frames_n.png"
```

**8. Ajoutez une entrée permettant d’arrêter la machine, et une autre permettant de la redémarrer**

```
menuentry Arrêter {
halt
}
menuentry Redémarrer {
reboot
}
```

**9. Configurer GRUB pour que le clavier soit en français**

```
insmod keylayouts
keymap fr
```

## Exercice 2. Noyau

**1. Commencez par installer le paquet build-essential, qui contient tous les outils nécessaires (compilateurs, bibliothèques) à la compilation de programmes en C (entre autres).**

FAIT

**2. Créez un fichier hello.c contenant le code suivant**

FAIT

**3. Créez également un fichier Makefile**

FAIT

**4. Compilez le module à l’aide de la commande make, puis installez-le à l’aide de la commande make
install.**

```
root@ubuntu-server:~# make
make -C /lib/modules/5.0.0-31-generic/build M=/root modules
make[1]: Entering directory '/usr/src/linux-headers-5.0.0-31-generic'
  CC [M]  /root/hello.o
  Building modules, stage 2.
  MODPOST 1 modules
  CC      /root/hello.mod.o
  LD [M]  /root/hello.ko
make[1]: Leaving directory '/usr/src/linux-headers-5.0.0-31-generic'
root@ubuntu-server:~# make install
cp ./hello.ko /lib/modules/5.0.0-31-generic/kernel/drivers/misc
```

**5. Chargez le module ; vérifiez dans le journal du noyau que le message ”La fonction init_module() est
appelée” a bien été inscrit, synonyme que le module a été chargé ; confirmez avec la commande lsmod.**

```
insmod hello.ko
```

```
Oct 21 09:44:57 ubuntu-server systemd[1]: Started Session 4 of user root.
Oct 21 09:45:01 ubuntu-server CRON[1258]: (root) CMD (/usr/bin/screen -S test -X                                                                              stuff 'Bien se rappeler de la r\303\251union'`echo -ne '\015'`)
Oct 21 09:45:01 ubuntu-server CRON[1257]: (CRON) info (No MTA installed, discard                                                                             ing output)
Oct 21 09:45:11 ubuntu-server kernel: [   84.499741] hello: loading out-of-tree                                                                              module taints kernel.
Oct 21 09:45:11 ubuntu-server kernel: [   84.499784] hello: module verification                                                                              failed: signature and/or required key missing - tainting kernel
Oct 21 09:45:11 ubuntu-server kernel: [   84.501028] [Hello world] - La fonction                                                                              init_module() est appelée.
Oct 21 09:46:01 ubuntu-server CRON[1284]: (root) CMD (/usr/bin/screen -S test -X                                                                              stuff 'Bien se rappeler de la r\303\251union'`echo -ne '\015'`)
Oct 21 09:46:01 ubuntu-server CRON[1283]: (CRON) info (No MTA installed, discard                                                                             ing output)
```

Fonctionne

**6. Utilisez la commande modinfo pour obtenir des informations sur le module hello.ko ; vous devriez
notamment voir les informations figurant dans le fichier C**

```
root@ubuntu-server:/lib/modules/5.0.0-31-generic/kernel/drivers/misc# modinfo hello.ko
filename:       /lib/modules/5.0.0-31-generic/kernel/drivers/misc/hello.ko
version:        Version 1.00
description:    Module hello world
author:         John Doe
license:        GPL
srcversion:     4398A2271F215E3A6F58078
depends:
retpoline:      Y
name:           hello
vermagic:       5.0.0-31-generic SMP mod_unload
```

**7. Déchargez le module ; vérifiez dans le journal du noyau que le message ”La fonction cleanup_module()
est appelée” a bien été inscrit, synonyme que le module a été déchargé ; confirmez avec la commande
lsmod.**

```
root@ubuntu-server:/lib/modules/5.0.0-31-generic/kernel/drivers/misc# rmmod hello
root@ubuntu-server:/lib/modules/5.0.0-31-generic/kernel/drivers/misc# lsmod | grep hello
root@ubuntu-server:/lib/modules/5.0.0-31-generic/kernel/drivers/misc#
```

**8. Pour que le module soit chargé automatiquement au démarrage du système, il faut l’inscrire dans le
fichier /etc/modules. Essayez, et vérifiez avec la commande lsmod après redémarrage de la machine.**

```

```

## Exercice 3. Interception de signaux

**1. Commencez par écrire un script qui recopie dans un fichier tmp.txt chaque ligne saisie au clavier par
l’utilisateur**

```
while (true); do
read text
echo $text >> tmp.txt
done
```

**2. Lancez votre script et appuyez sur CTRL+Z. Que se passe-t-il ? Comment faire pour que le script poursuive son exécution ?**

Le script se met en arrière plan

Pour reprendre l'execution écrire bg puis fg [id]

**3. Toujours pendant l’exécution du script, appuyez sur CTRL+C. Que se passe-t-il ?**

CTRL+C permet d'arreter le script

**4. Modifiez votre script pour redéfinir les actions à effectuer quand le script reçoit les signaux SIGTSTP
(= CTRL+Z) et SIGINT (= CTRL+C) : dans le premier cas, il doit afficher ”Impossible de me placer en
arrière-plan”, et dans le second cas, il doit afficher ”OK, je fais un peu de ménage avant” avant de
supprimer le fichier temporaire et terminer le script.**

```
trap ctrl_c INT
trap ctrl_z SIGTSTP

function ctrl_c() {
        echo "OK, je fais juste un peu de ménage avant"
        rm tmp.txt
        exit
}

function ctrl_z() {
        echo "impossible de me placer en arrière-plan !"
}
```

**5. Testez le nouveau comportement de votre script en utilisant d’une part les raccourcis clavier, d’autre
part la commande kill**



**6. Relancez votre script et faites immédiatement un CTRL+C : vous obtenez un message d’erreur vous
indiquant que le fichier tmp.txt n’existe pas. A l’aide de la commande interne test, corrigez votre
script pour que ce message n’apparaisse plus.**

```
trap ctrl_c INT
trap ctrl_z SIGTSTP

function ctrl_c() {
        echo "OK, je fais juste un peu de ménage avant"
        if test -f tmp.txt; then
                rm tmp.txt
        fi
        exit
}

function ctrl_z() {
        echo "impossible de me placer en arrière-plan !"
}
```

# Exercice 4 - Surveillance de l’activité du système

**1. Dans tty1, lancez la commande htop, puis tapez la commande w dans tty2. Qu’affiche cette commande ?**

La commande w affiche la liste des commandes en cours et les utilisateurs

**2. Comment afficher l’historique des dernières connexions à la machine ?**

last

**3. Quelle commande permet d’obtenir la version du noyau ?**

```
uname -r
```

**4. Comment récupérer toutes les informations sur le processeur, au format JSON ?**

```
lshw -class Processor -json > lshwProc.txt
```

**5. Comment obtenir la liste des derniers démarrages de la machine avec la commande journalctl ?
Comment afficher tout ce qu’il s’est passé sur la machine lors de l’avant-dernier boot ?**

```
journalctl -b -1
```

**6. Comment obtenir la liste des derniers démarrages de la machine avec la commande journalctl ?**

```
journalctl --list-boots
```

**7. Faites en sortes que lors d’une connexion à la machine, les utilisateurs soient prévenus par un message
à l’écran d’une maintenance le 26 mars à minuit.**

Écrire dans /etc/motd

**8. Ecrivez un script bash qui permet de calculer le k-ième nombre de Fibonacci : Fk = Fk−1 + Fk−2,
avec F0 = F1 = 1. Lancez le calcul de F100 puis lancez la commande tload depuis un autre terminal
virtuel. Que constatez-vous ? Interrompez ensuite le calcul avec CTRL+C et observez la conséquence sur
l’affichage de tload.**
