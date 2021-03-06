## Documentation compilateur While
### Introduction
Ce projet a pour objectif de compiler un programme écrit avec le langage While en un programme JavaScript. Nous travaillons en mode agile, les différents sprints ont une durée de une à deux semaines. A chaque fin de sprint, nous fournissons un délivrable pour construire le compilateur. Voici les membres de notre groupe :

Bastien Cloarec
Haozhi Li
Youssouf Maiga
Salifou Nguetcheu
Baptiste Buron

### Getting start
Nous avons créé un dépôt git pour avoir une bonne gestion du projet 

    git clone http://qfdk.github.io/While2JavaScript && cd While2JavaScript && make

## Machine cible
### Sémantique opérationnelle
Le langage ciblé étant le JavaScript, la gestion (statique, pile, tas) n'est pas de notre responsabilité.
Lorsqu'on utilise JavaScript, la mémoire est allouée lors de la création des objets puis libérée « automatiquement » lorsque ceux-ci ne sont plus utilisés. Cette libération automatique est appelée garbage collection en anglais ou ramasse-miettes. Le fait que ce processus soit automatique est souvent source de confusion et donne parfois l'impression que JavaScript (ou d'autres langages de haut niveau) ne permet pas de gérer la mémoire : nous allons voir que ce n'est pas le cas.
En 2012, l'ensemble des navigateurs web modernes disposent d'un ramasse-miettes implémentant cet algorithme mark-and-sweep. L'ensemble des améliorations apportées dans ce domaine de JavaScript représentent des améliorations basées sur cet algorithme, ce ne sont pas de nouveaux algorithmes ou une nouvelle définition pour les objets à supprimer.

## Style de machine cible
### Délivrables
1. Pretty Printer
2. Table des symboles
3. ?

## Spécification : Traduction de while en javascript
Pas d'erreur à la l'exécution implique que toutes les variables doivent être initialisés. Nous nous arrogeons à ce qu'il n'y ait pas d'erreur à l’exécution. 

### Variable non initialisé : 
La valeur par défaut en while est nil ===> undefined
undefined en javascript signifie variable non initialisé alors que null  est utilipsé par le programme pour dire. En faisant seulement var nil, sans initialisation, sa valeur est underfined

``` javascript
    var nil;
```

### 1. nop
Le rôle des commande est de modifier l'état de la mémoire. 
La commande nop ne fait rien. On la traduit par l'instruction 0+0 par exemple;

``` javascript
   console.log()
```
### 2. Commande c1;c2:
Nous devons exécuter C1 puis C2. La traduction en JavaScript donnera: 	

``` javascript
    traduction (C1)
    traduction (C2)
```
### 3. if E then C1 else C2 fi
Sa traduction donne :	

``` javascript
if  (traduction(E)!=nil)
     traduction(C1)
else 
      traduction (C2)
``` 

### 4.if E then C1 fi
Sa traduction donne :	

``` javascript
if  (traduction(E)!= nil)
     traduction(C1)
``` 
### 5. while E do C od
Sa traduction donne :	

``` javascript
While (E!= nil)
{
    traduction(C)
}
``` 
###6. for E do C od
Sa traduction donne :	

``` JavaScript
Var cpt = value(E) // value retourne le nombre de noeud sur la branche droite
for(var i = 0;i<cpt;i++)
{
    traduction(C);
}
``` 
### 7. foreach X in E do C od

``` JavaScript
for (let X of E) 
{
	traduction(C);
}
``` 


### 8. V1,...,Vn = E1,...En
Les Vi et les Ei designeront les traductions des noms de variable données par la table des variables.

```JavaScript
var tampon = new Array();
for(int i=1;i<=n;i++)
{
	tampon.push(Ei)
}
for(int i=1;i<=n;i++)
{
	Vi = tampon[i];
}

``` 

### 9.V1,...,Vm := f(E1,...,En)
Les Vi et les Ei designeront les traductions des noms de variable données par la table des variables.

``` javascript
var tampon = new Array(); // Pour stocker les write de f
executer(f,n,tampon) // Execution de la fonction f ayant n parametres
for(int i=0;i<m;i++)
{
	Vi+1 = tampon[i];
}

``` 
### 10. for X do X
X designera la traduction du nom de varible X comme le defini la table des variables.

``` JavaScript
var save = X;
var last;
while(X!=nil)
{
	fin = tl(X);
}
fin = Y;
``` 

### 11. X := (cons a b)

``` JavaScript
 // Un construction que nous allons definir
X = new BinTree(a,b) 
``` 

## Code  humain

### E -> nil   
    {E.code = nil}

### E -> X
    {E.code = X.code} : variables

### E -> S
    {E.code = S.code} : symboles

### E -> cons X
    {E.place = new-var();
    E.code = <cons , E.place, X,_>}

### E -> cons X Y
    {E.place = new-var();
     E.code = <cons, E.place, X , Y>}

### E -> hd X
    {E.code = <hd,E,X,_>}

### E -> tl X
    {E.code = <tl,E,X,_> }

### C->nop
    {C.code = ℇ}

### C -> C1	';' C2
    {C.code = C1.code '.' C2.code}
    {C.place = C1.place '.' C2.place}

### C -> C1
    {C.code = C1.code}
    {C.place = C1.place}

### C -> Vars ':=' Exprs
    {C.code = Exprs.code '.' < =, Vars.place, Exprs.place,_>}

### C -> while E do C' od
    {E.si-faux = new-label();
     E.si-vrai = new-label();			
     boucle	= new-label();
     C.code	= boucle : E.code '.' E.si-vrai : C'.code
     '.' <goto boucle,_,_,_> '.' E.si-faux :}	
    
### C -> if E then C' fi
    {E.si-faux = new-label();
     E.si-vrai = new-label();	
     C.code	= E.code '.' E.si-vrai: C'.code	'.' E.si-faux:}	

### C -> if	E then C' else C'' fi	
    {E.si-faux = new-label();
     E.si-vrai = new-label();		
     fin = new-label();	
     C.code	= E.code '.' E.si-vrai : C'.code '.' <goto fin,_,_,_> '.' E.si-faux : C''.code '.' fin:}	
    
### C -> for E do C' od
    {E.si-faux = new-label();
     E.si-vrai = new-label();
     debut = new-label();
     C.code	=  debut: E.code '.' E.si-vrai : C'.code '.' <goto debut,_,_,_> '.' E.si-faux:}	
    
### C -> foreach X in E do C' od
    {E.si-faux = new-label();
     E.si-vrai = new-label();
     X.si-vrai = new-label();
     debut = new-label();
     C.code = debut: E.code '.' E.si-vrai: X.code '.' X.si-vrai: C'.code '.' <goto debut,_,_,_>
     '.' X.si-faux '.' E.si-faux:}

### E -> E' and E''
    {E'.si-faux  = E.si-faux;
     E'.si-vrai  = new-label();
     E".si-faux  = E.si-faux;
     E''.si-vrai = E.si-vrai;
     E.code = E'.code '.' E'.si-vrai: E".code}
    
### E -> E'	or E''
    {E'.si-faux  = new-label();
     E'.si-vrai  = E.si-vrai;
     E''.si-faux = E.si-faux;
     E''.si-vrai = E.si-vrai;
     E.code = E'.code '.' E.si-faux:E ''.code}
 
### E -> not E'
    {E'.si-faux = E.si-vrai;
     E'.si-vrai = E.si-faux	;		
     E.code	= E'.code}	
## Code 3@ - Machine

### nop

```
	<nop,  _ ,  _,  _>
```

### C1;C2

```
	<C1,  _ ,  _,  _>
	<C2,  _ ,  _,  _>
```

### X := (cons a b)

```
	<cons, X, a, b>
```

### X := (list a b)

```
	<list, X, a, b>
```

### X := (tl Y)

```
	<tl, X, Y, _>
```

### X := (hd Y)

```
	<hd, X, Y, _>
```

### if E then C fi

```
	<if  E,  _ ,  C , _> ||  <if  E C,  _ ,  _ , _>
```

### if E then C1 else C2 fi

```
	<if E C1 C2, _,  _ , _>
```

### if E then C1 else C2 fi

```
	<if E C1 C2, _,  _ , _>
```

### While E do C od
```
	<while E C, _, _,_>
```

### for E do C od
```
	<for E C, _,  _ , _>
```

### foreach X in E do C' od
```
	<cons,  Y,  X, _>
	<for Y C, _,  _ , _>
```

###  R -> E and E'
```
	<and,R,E,E'>
```

###  R -> E or E'
```
	<or,R,E,E'>
```
