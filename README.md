# Corewar-Championship
projet epitech

ceci est une aide pour comprendre le sujet du corewar championship

Le sujet n'étant pas tres explicite, je vais réaliser une petite aide

mais je vous conseille de relire ce tuto plusieurs fois, la plupart des notions sont plutot complexes et expliquées en plusieurs parties disparates...

commencons par le commencement, comment dire "live" ?

regardons ce que le zork fait:

	l2: sti r1,%:live,%0x1 ;=> il stock ce qui est contenu dans le registre 1 à l'adresse du flag "live" + 1
		and r1,%0,r1 ;=> cela permet de set la carry à 0, ce qui est necessaire pour zjmp
	live: live %1 ; il dit live, avec le bon nom de joueur, puisque %1 aura été remplacé (voir plus loin)
		zjmp %:live ; il retourne a l'adresse du flag live


pour bien comprendre, il faut savoir que le nom de joueur est contenu dans le registre 1 au début de la partie,

un registre est plus ou moins comme une variable, il contient 4 octets, donc lorsqu'on le stock dans la memoire il va écraser les

4 octets à partir de l'adresse indiquée (ici à partir de live + 1, donc les 4 octets suivant la fonction... autrement dit il ecrasera le 1er argument, voila pourquoi beaucoup de personne disent que le parametre de live n'est pas important).

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

  notre zork n'est donc plus viable, il ne dit plus qu'il est en vie, et meurt donc, le joueur ennemi gagne donc
