Analyse générale:

q1
Ce fichier est un fichier de configuration pour pare feu via des regles ip table. 

q2
Le script lance le firewall daemon (active le service) avec pour base un script firewall.sh
Puis le script défini les variables utilisées tels que les ports utilisés
Il défini ensuite les regles iptable

ensuite, des fonctions sont définies permettant d'arreter le firewall, clear les regles du firewall, de redemarrer ces regles et les tester

q3:structure de controle itérative dans des tableaux

q4: Cela va ouvrir plusieurs ports avec la command iptables

q5:
/sbin/iptables -A INPUT -p tcp --dport ${PORT} -j ACCEPT
 -A pour append, -p permet d'indiquer le protocole, --dport avec la variable port doit permettre d'indiquer sur quel port agir mais j'ai pas trouvé ou elle est défini, -j pour selectionner la cible.

q6:  /sbin/iptables -A INPUT -p udp --dport ${PORT} -j ACCEPT
meme chose pour les options

q7: si la variable network_mgmt est vide le script va accepter les entrée par le port ssh indiqué en variable plutot que toutes les entrées du réseau indiqué par l'ip du réseau de la variable network_mgmt
/sbin/iptables -A INPUT -p tcp --dport ${SSH_PORT}  -j ACCEPT
voila la commande qui va s'appliquer dans ce cas

q8: si network_mgmt n'est pas vide, le pare feu accepte toutes les entrées venant du réseau indiqué en variable.
/sbin/iptables -A INPUT -p tcp --src ${NETWORK_MGMT} --dport ${SSH_PORT} -j ACCEPT
C'est celle ci qui s'applique

q9: la commande permet d'accepter les requetes du protocole icmp, comme les pings

q10: Elle permet d'accepter les requetes vers l'interface lo

q11: INPUT DROP bloque les requetes qui n'ont pas été permises par les regles précédentes.

q12: permet de garder les logs des input

q13: la commande permet de faire passer l'output vers l'interface lo

q14: la commande de permet de match l'etat d'entrée et de sortie

q15: autorise l'utilisation du protocole icmp, genre les pings

q16: cette ligne autorise la connexion a security.debian.org via le port 80

q17: cette partie concerne les sorties vers l'exterieur du reseau

q18: input c'est les entrée, output les sorties

q19: iptables -A OUTPUT -j LOG   autorise la generation de logs

/sbin/iptables -A OUTPUT -j REJECT: bloque les requetes non autorisé en amont en envoyant un message d'erreur a l'utilisateur

/sbin/iptables -P OUTPUT DROP: bloque les requetes non autorisé en amont silencieusement

q20: Cette regle permet la tenue de log sur le systeme.

q21:echo 0 > /proc/sys/net/ipv4/ip_forward

echo 0 > /proc/sys/net/ipv4/conf/all/send_redirects       Ces 3 lignes désactivent des fonctions qui pourraient etre nécessaires au fonctionnement du réseau.

echo 0 > /proc/sys/net/ipv4/conf/all/accept_source_route


q22:

fw_stop () {  # arrete le pare feu

/sbin/iptables -F         #flush les regles iptables

/sbin/iptables -t nat -F    #flush la table nat

/sbin/iptables -t mangle -F  #flush la table mangle

/sbin/iptables -P INPUT DROP  # bloque tous les inputs

/sbin/iptables -P FORWARD DROP  #bloque tous les forwarding

/sbin/iptables -P OUTPUT ACCEPT  #laisse passer les output


q23

fw_clear () {  #redemarre les regles mais sans arreter le pare feu

/sbin/iptables -F    #flush les regles iptables

/sbin/iptables -t nat -F  # flush la table nat

/sbin/iptables -t mangle -F   #flush la table mangle

/sbin/iptables -P INPUT ACCEPT #autorise les input

/sbin/iptables -P FORWARD ACCEPT  #autorise le forwarding

/sbin/iptables -P OUTPUT ACCEPT  #autorise les output
}

q24:

fw_test () {  #

fw_save   #fonction qui fait un backup des regles iptables

fw_restart  # fonction qui utilise la fonction fw_stop puis relance la fonction fw_start

sleep 30  # fais patienter 30s avant la suite

fw_restore # fonction qui restore le backup 
}



