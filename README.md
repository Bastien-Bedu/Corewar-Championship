# Corewar-Championship
projet epitech

ceci est une aide pour comprendre le sujet du corewar championship

Le sujet n'étant pas tres explicite, je vais réaliser une petite aide

mais je vous conseille de relire ce tuto plusieurs fois, la plupart des notions sont plutot complexes et expliquées en plusieurs parties disparates...

commencons par le commencement, comment dire "live" ?

regardons ce que le zork fait:

	l2: sti r1,%:live,%0x1 ;=> il stock ce qui est contenu dans le registre 1 à l'adresse du flag "live" + 1
		and r1,%0,r1 ;=> cela permet de set la carry à 1, ce qui est necessaire pour zjmp
	live: live %1 ; il dit live, avec le bon nom de joueur, puisque %1 aura été remplacé (voir plus loin)
		zjmp %:live ; il retourne a l'adresse du flag live


pour bien comprendre, il faut savoir que le nom de joueur est contenu dans le registre 1 au début de la partie,

un registre est plus ou moins comme une variable, il contient 4 octets, donc lorsqu'on le stock dans la memoire il va écraser les 4 octets à partir de l'adresse indiquée (ici à partir de live + 1, donc les 4 octets suivant la fonction... autrement dit il ecrasera le 1er argument, voila pourquoi beaucoup de personne disent que le parametre de live n'est pas important).

live prend 5 places en mémoire, le premier octet est là pour la fonction live, tandis que les 4 suivants correspondent à l'argument  qui lui est passé en paramètre.

maintenant que l'on sait pourquoi le zork reste en vie, regardons pourquoi il meurt lorsqu'un fork classique lui arrive dessus:

/!\ un fork ne copie rien en mémoire, il n'écrase rien non plus! il se contente de créer un nouveau processus à l'endroit indiqué en paramètre.

le processus du joueur ennemi est créé, puis il se déplace dans la mémoire (qui est vide, et donc n'effectue aucune action) jusqu'à la premiere commande de notre zork en l'occurence : 

l2: sti r1,%:live,%0x1

en l'effectuant, il va remplacer le nom de joueur de notre zork dans live (+1) par son registre 1 propre (souvent son nom de joueur à lui), notre zork devient donc

	l2: sti r1,%:live,%0x1 ;=> il stock ce qui est contenu dans le registre 1 à l'adresse du flag "live" + 1
		and r1,%0,r1
	live: live %1 ; il dit live, avec le registre 1 du joueur ennemi, puisque %1 a été remplacé
		zjmp %:live ; il retourne a l'adresse du flag live

  notre zork n'est donc plus viable, il ne dit plus qu'il est en vie, et meurt donc, le joueur ennemi gagne.
  
  maintenant qu'on a vu les bases, on va passer à une partie un peu plus complexe, une boucle! (en réalité ce programme n'est absolument pas performant, mais tres complexe et fait donc un exemple idéal pour un github ;)

je vous conseille de le copier et de l'executer pour voir ce qu'il donne :)

la taille indiquée a droite des commentaire est la taille que prends chacune des lignes en mémoire

	.name "Jessie: prepare for trouble! James: make it double!"
	.comment "duplicate properly"
	.extend
	
        st r1, :live+1 		        ;modifie live avec notre nom de joueur  		taille :  5
	start:  ld %4, r3               ;stock le nombre 4 dans r3              		taille :  7
	        ld %48, r2              ;stock le nombre 48 dans r2             		taille :  7
	loop:   sub r2, r3, r2          ;on soustrait r3 à r2                   		taille :  5
	        zjmp %:end              ;où aller une fois la boucle finie (saute à end)	taille :  3
	live:   live %1                 ;commande de la boucle                  		taille :  5
	        ldi %:start+3, r2, r5   ;stock les valeurs dans r5                    		taille :  6
	        sti r5, r2, %354        ;les copies 354 plus loin + la valeur dans r2      	taille :  6
	        sub r4, r4, r4          ;condition pour le prochain zjmp, set la carry à 1 	taille :  5
	        zjmp %:loop             ;retour au debut de boucle              		taille :  3
	end:    zjmp %344               ;saute au debut du programme copié (354 - la taille en mémoire depuis l'appel de sti donc 14 + 4 car le dernier stock se fait à 354+4)	taille :  3
	                                ;                                       		total  : 55
pour bien comprendre on va découper ce qu'elle effectue etape par etape (je vous conseille de suivre en regardant le programme et en marquant où se trouve le processus

stock le nom du jouer a coté de live

stock 4 dans le registre 3 car les registres contiennent 4 octets, il est donc normal que notre programme avance de 4 par 4

stock 48 dans le registre 2 (il n'est pas nécessaire de copier les deux premieres lignes (car on copiera le live avec le nom du joueur uploadé, et que r3 ne bouge pas par la suite) la taille a copier est donc de 7+5+3+5+6+6+5+3+3=43 il faut que ce soit un multiple de 4 donc 44 puisque la soustraction par 4 doit donner 0 pour set la carry a 1 et passer au zjmp , comme on effectue une soustraction par 4 avant de copier pour la premiere fois, ca donne 44+4=48

r2 = r2 - r3 = 44  la soustraction renvoie 44 != 0, la carry vaut 0

on ne jump pas, la carry vaut 0

on dit etre en vie

on stock 4 octets à partir de l'adresse: la valeur de l'adresse de start + 3 + r2 (44) donc start+47 donc 3 octets avant la fin, autrement dit on stock le zjmp %345 dans r5

on store r5 (donc ecrase la mémoire) à partir de 354+r2 donc 354+44, on store ce qu'on avait stocké en r5 donc notre zjmp

on soustrait r4 a r4, ce qui donne obligatoirement 0, et donc set la carry à 1

on saute au debut de la boucle

... (il se passe plusieurs itérations, la même chose se répète plus ou moins)

on soustrait r3 à r2 ; r2 = r2 - r3 = 4 - 4 = 0 la carry vaut donc 1

on saute à la fin

on saute 344 plus loin (au debut de ce qu'on a copié)

et notre programme recommence à stocker 48 dans r2, etc...
