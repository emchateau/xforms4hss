---
title: Saisir des annuaires historiques avec XForms
collection: lessons
layout: lesson
authors:
- Josselin Morvan
- Emmanuel Château-Dutier
---

<!-- se conformer à https://programminghistorian.org/fr/consignes-auteurs -->

{% include toc.html %}

  
## Introduction
### Objectifs de la leçon

Cette leçon propose une initiation à XForms dans le cadre de l’élaboration d’un formulaire pour la saisie de données sérielles issues d’annuaires historiques : les Almanachs royaux. Publiés au format in-8° chaque année entre 1700 et 1792, les Almanachs royaux comportaient, entre autres choses, la liste des membres de la famille royale, des principaux officiers du royaume, ou encore des hauts représentants du clergé. De par la richesse des informations qu’ils contiennent (patronyme des individus, appartenance et position dans un corps, adresse) ces sources intéressent de nombreux historiens. Les Almanachs royaux sont accessibles dans leur intégralité via [Gallica](https://gallica.bnf.fr/ark:/12148/cb34454105m/date.item) et le [centre de recherche du château de Versailles](https://www.chateauversailles-recherche.fr/francais/ressources-documentaires/corpus-electroniques/sources-imprimees/periodiques/l-almanach-royal-1683-1792.html).

L’exemple utilisé dans le cadre de cette leçon est celui des listes d’experts-jurés parisiens du bâtiment, répartis, à partir de 1690, en deux colonnes : les architectes experts bourgeois et les experts entrepreneurs. Arbitrairement, nous nous appuierons sur la liste des experts pour l'année 1750, disponible à cette adresse : [<https://gallica.bnf.fr/ark:/12148/bpt6k204719d/f342.item>](https://gallica.bnf.fr/ark:/12148/bpt6k204719d/f342.item). Outre la répartition en deux colonnes, la liste indique pour chaque expert son année d'entrée dans le corps des experts, son patronyme, eventuellement son statut au sein de la communauté, et enfin son adresse.

### Prérequis
- connaissance des langages [HTML](https://html.spec.whatwg.org/multipage/), [XML](https://www.w3.org/TR/xml/) et [XPath](https://www.w3.org/TR/xpath-31/), et du dialecte de la [Text Encoding Initiative](https://tei-c.org/)<!-- renvoyer vers https://programminghistorian.org/en/lessons/transforming-xml-with-xsl --> ;
- disposer du client [XSLTForms](https://github.com/AlainCouthures/declarative4all/releases/latest) ;
- disposer d'un serveur web ou local capable de servir des documents HTML ou XML ;  

### XForms et XSLTForms
[XForms](https://www.w3.org/TR/xforms11/) est un dialecte XML spécialisé dans la création de formulaires web standardisé par le W3C à partir de 2006. Il s’appuie sur une architecture Modèle - Vue - Contrôleur (MVC). En distinguant fond et forme, cette technologie offre de nombreux avantages en termes de réutilisation des formulaires, de compatibilité avec les différents types de périphériques (ordinateurs, téléphones, tablettes, etc.), et d’accessibilité. Plus qu’un simple langage de formulaire, dans sa version 1.1 XForms est devenu un langage Turing-complet, qui adopte un modèle de programmation déclaratif où l’on s’attache à décrire ce que l’on souhaite faire plutôt que comment, et capable de soutenir le développement d’applications à grande échelle.

Dans le champ des sciences humaines et sociales, la communauté scientifique a largement adopté XML, dans lequel elle trouve l’expression de plusieurs formats de métadonnées et standards internationaux : Text Encoding Initiative (TEI), Encoded Archival Description (EAD) ou encore Encoded Archival Context - Corporate bodies, Persons, Families (EAC-CPF) en tête. Ici, l’utilisation du standard XML du W3C offre beaucoup d’avantages grâce à l’utilisation de schémas pour la validation tout en assurant un haut niveau de portabilité et d'interopérabilité, et une grande souplesse d'utilisation qui s’articule facilement avec de nombreux systèmes d’information. Pour autant, lorsqu'il s'agit de produire des données au format XML, peu de solutions s’offrent réellement aux équipes de recherche, qui sont souvent contraintes de rédiger leurs documents directement dans un éditeur de texte.

La création de formulaires avec XForms permet d’éditer des instances XML directement dans un navigateur, tout en embarquant des fonctionnalités avancées propres aux technologies XML. Elle constitue donc une solution de choix pour faciliter l’édition de documents XML. Elle présente ainsi un intérêt particulier pour les ingénieurs et les chercheurs qui souhaitent travailler dans l’environnement XML. En effet, XForms facilite la mise en place de formulaires dynamiques et très complexes, comportant par exemple des répétitions, des contrôles sur les valeurs ou des règles de contraintes avancées relativement rapidement.  

Malgré des qualités indéniables, cette recommandation du W3C n’a toutefois jamais été prise en charge par les navigateurs et nécessite donc l’utilisation d’un client pour s’exécuter. Pour cette leçon, nous utiliserons le client *open source* [XSLTForms](https://github.com/AlainCouthures/declarative4all) développé par Alain Couthures.

## Configuration de l'environnement de développement
Préalablement à cette leçon, vous devez télécharger le client [XSLTForms](https://github.com/AlainCouthures/declarative4all/releases/latest) (`xsltforms.zip`) puis dézipper l'archive à l'emplacement de votre choix. 

Les instances XForms doivent être servies par l'intermédiaire d'un serveur web. Si vous n'en disposez pas, voici comment déployer rapidement un serveur local avec [Python](https://www.python.org/), un langage de programmation largement utilisé et généralement installé par défaut sur les systèmes macOS et Linux, et qu'il vous faudra éventuellement installer sous Windows.

Tout d'abord, il est important de déterminer la version de Python installée sur votre machine. Pour ce faire, vous pouvez entrer l'une des deux commandes suivantes dans votre invite de commandes ou votre terminal :
```bash
python -v
# ou
python3 -v
```
Cette commande devrait vous retourner la version de Python installée (généralement `Python 2.XX` ou `Python 3.XX`). Ensuite, en utilisant la commande `cd`, changez votre répertoire de travail pour le dossier `xsltforms` dézippé un peu plus tôt.

 ```bash
cd chemin/vers/le/répertoire/xsltfoms
 ```

Selon votre version de Python, entrez l'une des commandes suivantes pour démarrer le serveur local sur le port 8000 :
```bash
# Python 3.XX
python3 -m http.server 8000
# Python 2.XX
python -m SimpleHTTPServer 8000
```
Dans votre navigateur, vous pouvez maintenant vous rendre à l'adresse [localhost:8000](http://localhost:8000/). Par défaut, vous devriez voir la liste des fichiers contenus dans le dossier `xsltforms`.

D'autres solutions existent pour déployer un serveur local (Julia, NodeJS, Apache, PHP, etc). Vous pouvez consulter ce repo Github afin d'examiner les 
alternatives <[https://github.com/sardinecan/localhost/](https://github.com/sardinecan/localhost/)>

## Principes de mise en œuvre 
 À l'aide d'un éditeur de texte, ouvrez le fichier `hello.xml`, l'exemple fourni avec `xsltforms`, afin d'étudier l'implémentation d'un formulaire XForms.

```xml
<?xml-stylesheet href="xsltforms.xsl" type="text/xsl"?>
<html  xmlns="http://www.w3.org/1999/xhtml"  xmlns:xf="http://www.w3.org/2002/xforms"  lang="en">
<head>
	<meta  name="viewport"  content="initial-scale=1, width=device-width, viewport-fit=cover"/>
	<title>Hello World in XForms</title>
	<xf:model>
			<xf:instance>
				<data  xmlns="">
					<PersonGivenName/>
				</data>
			</xf:instance>
	</xf:model>
</head>
<body>
	<p>Type your first name in the input box. <br/>If you are running XForms, the output should be displayed in the output area.</p>
	<xf:input  ref="PersonGivenName"  incremental="true">
		<xf:label>Please enter your first name: </xf:label>
	</xf:input>
	<br  />
	<xf:output  value="concat('Hello ', PersonGivenName, '. We hope you like XForms!')">
		<xf:label>Output: </xf:label>
	</xf:output>
</body>
</html>
```

### Intégration dans une page web
Reposant sur la syntaxe XML, XForms a été conçu pour s'intégrer directement dans des pages XHTML et pour s'articuler parfaitement avec les autres standards du W3C (CSS et XPath). Afin de distinguer les segments XForms du code XHTML, on utilise les espaces de noms. Dans l'exemple ci-dessus, l'espace de nom XForms est déclaré avec la balise `<html xmlns:xf="http://www.w3.org/2002/xforms"/>`. L'utilisation du préfixe `xf` permet ensuite d'identifier facilement les éléments XForms dans le reste du document. 
```xml
<xf:input  ref="PersonGivenName"  incremental="true">
	<xf:label>Please enter your first name: </xf:label>
</xf:input>
```
Notons au passage que la déclaration du client XSLTForms se fait très simplement, avec l'ajout d'une instruction de traitement dans le préambule du document pointant vers la feuille de transformation `xsltforms.xsl` :
```xml
<?xml-stylesheet href="xsltforms.xsl" type="text/xsl"?>
```

### L’architecture modèle-vue-contrôleur de XForms
Contrairement aux formulaires XHTML, XForms adopte une [architecture Modèle-Vue-Contrôleur](https://fr.wikipedia.org/wiki/Mod%C3%A8le-vue-contr%C3%B4leur) (MVC), facilitant la maintenance des formulaires et favorisant les réutilisations. 

####  Modèle
Le **modèle** XForms `<xf:model/>` contient le modèle de données exprimé sous la forme d'une ou plusieurs instances XML `<xf:instance/>`.
```xml
<xf:model>
	<xf:instance>
		<data  xmlns="">
			<PersonGivenName/>
		</data>
	</xf:instance>
</xf:model>
```
Il peut également contenir les règles de validation, les contraintes, les déclarations de types de données ou encore les paramètres de soumission que nous verrons à la fin de cette leçon. Plusieurs modèles peuvent être déclarés au sein d'une même page.

#### Vue
La **vue** régit l'affichage et la présentation des données. Elle détermine comment les données du modèle sont rendues à l'écran.
```xml
<xf:input  ref="PersonGivenName"  incremental="true">
	<xf:label>First name: </xf:label>
	<xf:hint>Please enter your first name</xf:hint>
</xf:input>
<xf:output  value="concat('Hello ', PersonGivenName, '. We hope you like XForms!')">
	<xf:label>Output: </xf:label>
</xf:output>
```
Cette couche inclut les différents champs de saisie des données (`<xf:input/>`), les éléments relatifs aux étiquettes ou à l'aide (`<xf:label/>`), ou encore des balises pour afficher dynamiquement des données (`<xf:output/>`).

#### Contrôleur
La couche **contrôleur** gère les interactions entre la vue et le modèle. Elle orchestre la mise à jour des données en réponse aux actions des utilisateurs et peut exécuter des actions spécifiques comme la soumissions des données. Nous verrons des exemples dans la suite de cette leçon.

Maintenant que nous avons fait un tour des grands principes qui régissent XForms, réalisons notre formulaire pour la saisie des annuaires historiques.

## *Text Encoding Initiative* et modèle de données
L'étape préalable à la création d'un formulaire de saisie est l'élaboration d'un modèle de données. Pour cette leçon, nous nous appuierons sur le modèle proposé par la [*Text Encoding Initiative*](https://tei-c.org/), un dialecte XML largement adopté par la communauté des sciences humaines et sociales et spécialisé dans le balisage de ressources textuelles.

Un document XML-TEI minimal ressemble à ceci :
```xml
<TEI xmlns="http://www.tei-c.org/ns/1.0">
  <teiHeader>
      <fileDesc>
         <titleStmt>
            <title>Title</title>
         </titleStmt>
         <publicationStmt>
            <p>Publication Information</p>
         </publicationStmt>
         <sourceDesc>
            <p>Information about the source</p>
         </sourceDesc>
      </fileDesc>
  </teiHeader>
  <text>
      <body>
         <p>Some text here.</p>
      </body>
  </text>
</TEI>
```
Pour la leçon, nous laisserons de côté les métadonnées du `<teiHeader/>`, afin de nous concentrer sur le corps du document, contenu dans la balise `<body/>`. Toutefois, les mécanismes détaillés plus bas pourront tout-à-fait être réutilisés pour décrire l'entête.

L'analyse des annuaires révèle une structure relativement simple, presque tabulaire, constituée de titres, de paragraphes et de listes, où, pour chaque expert, sont indiqués son année d'entrée dans la corps des experts, son patronyme, éventuellement son statut dans la communauté et enfin son adresse.

{% include figure.html filename="figure1.jpg" caption="Figure 1 : Liste des experts parisiens du bâtiment pour l'année 1750." %}

Avec ces éléments, nous pouvons envisager la modélisation suivante :

- le titre de la liste et le paragraphe sont encodés respectivement avec les balises `<head/>` et `<p/>` ;
- chaque colonne est placée dans un élément `<div type="column"/>`, et son titre dans une balise `<head/>` ;
- chaque liste est ensuite étiquetée avec un élément `<list/>`, comportant un élément `<head/>` pour le petit chapeau, et une suite d'éléments `<item/>` pour chaque entrée, contenant les sous-éléments :
	- `<date/>` pour la date, 
	- `<persName/>` pour le patronyme, 
	- `<roleName/>` pour le statut, 
	- et `<address/>` pour l'adresse.

Ajoutons que les éléments `<item/>` sont répétables. Le tout est placé dans une balise `<div/>` de la manière suivante : 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- // xsltforms/instanceAlmanach.xml -->
<TEI xmlns="http://www.tei-c.org/ns/1.0">
  <teiHeader>
      <fileDesc>
         <titleStmt>
            <title>Title</title>
         </titleStmt>
         <publicationStmt>
            <p/>
         </publicationStmt>
         <sourceDesc>
            <p/>
         </sourceDesc>
      </fileDesc>
  </teiHeader>
  <text>
      <body>
         <div>
            <head/><!-- titre de la liste -->
            <p/><!-- paragraphe -->
            <div type="column"><!-- première colonne -->
               <head/><!-- titre de la colonne -->
               <list><!-- liste des experts -->
                  <head/><!-- chapeau de liste -->
                  <item><!-- expert / répétable -->
                     <date when=""/><!-- date -->
                     <persName/><!-- patronyme -->
                     <roleName/><!-- statut -->
                     <address><!-- adresse -->
                        <addrLine/><!--ligne d'adresse-->
                     </address>
                  </item>
               </list>
            </div>
						<div type="column"><!-- seconde colonne -->
               <head/>
               <list>
                  <head/>
                  <item>
                     <date when=""/>
                     <persName/>
                     <roleName/>
                     <address>
                        <addrLine/>
                     </address>
                  </item>
               </list>
            </div>
         </div>
      </body>
  </text>
</TEI>
```

Cette instance, vide pour le moment, peut être sauvegardée dans un fichier que vous nommerez pour l'exercice `instanceAlmanach.xml`, et que vous placerez dans le dossier `xsltforms` que vous avez précédemment dézippé. Les commentaires peuvent cependant être supprimés.

## Construction du formulaire
Nous l'avons vu, les formulaires XForms ont été conçus pour s'intégrer au sein de pages XHTML. Commencez par créer une page web minimaliste dans le dossier `xsltforms` que vous nommerez, par exemple, `formulaireAlmanach.xml`
```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<html xmlns="http://www.w3.org/1999/xhtml" lang="fr">
	<head>
		<title>Formulaire almanach</title>
	</head>
	<body></body>
</html>
```

Afin d'implémenter le client XSLTForms, vous devez ajouter l'instruction de commande vue plus haut dans le préambule de votre page web. Vous en profiterez pour déclarer l'espace de nom XForms sur la balise `<html/>` afin de passer à l'étape suivante.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- // xsltforms/formulaireAlmanach.xml -->
<?xml-stylesheet href="xsltforms.xsl" type="text/xsl"?>
<html
	xmlns="http://www.w3.org/1999/xhtml"
	xmlns:xf="http://www.w3.org/2002/xforms"
	lang="fr">
	<head>
		<title>Formulaire almanach</title>
	</head>
	<body></body>
</html>
```

### Déclaration du modèle
Le modèle doit être déclaré dans l'entête de la page web avec la balise `<xf:model/>`. Au sein de cette balise, vous ajouterez un sous-élément `<xf:instance/>` pointant vers le fichier `instanceAlmanach.xml` précédemment créé.

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<head>
   <title>Formulaire almanach</title>
   <xf:model>
      <xf:instance src="instanceAlmanach.xml"/>
   </xf:model>
</head>
```

L'instance étant sous l'espace de nom TEI, il est essentiel de le déclarer. Vous ajouterez également la déclaration de l'espace de nom des événements XML, dont vous aurez besoin ultérieurement .

```xml
<!-- // xsltforms/formulaireAlmanachs.xml -->
<html  
	xmlns="http://www.w3.org/1999/xhtml"
	xmlns:xf="http://www.w3.org/2002/xforms"
	xmlns:tei="http://www.tei-c.org/ns/1.0"
	xmlns:ev="http://www.w3.org/2001/xml-events"
	lang="fr"/>
```

<div  class="alert alert-warning">

Si vous travaillez avec Firefox, sachez que ce navigateur ne prend en charge que très partiellement les espaces de noms. Pour éviter toute erreur, il est nécessaire d'ajouter en plus l'attribut `tei:bogus="https://bugzilla.mozilla.org/show_bug.cgi?id=94270"`.

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<html  
	xmlns="http://www.w3.org/1999/xhtml"
	xmlns:xf="http://www.w3.org/2002/xforms"
	xmlns:tei="http://www.tei-c.org/ns/1.0"
	tei:bogus="https://bugzilla.mozilla.org/show_bug.cgi?id=94270"
	xmlns:ev="http://www.w3.org/2001/xml-events"
	lang="fr"/>
```

</div>

Le modèle est déclaré, il est possible de passer à l'élaboration du formulaire.

### Le formulaire
Comme avec XHTML, il existe plusieurs types de champs XForms, dont les principaux sont :

- `<xf:input/>` : champ texte ;
- `<xf:textarea/>` : champ bloc de texte ;
- `<xf:select/>` : champ sélection multiple (type *checkbox*) ;
- `<xf:select1/>` : champ sélection unique (type *radio*).

Chaque champ du formulaire est ensuite relié directement à un nœud de l'instance XML par l'intermédiaire d'un chemin XPath indiqué dans un attribut `@ref`, tandis qu'une étiquette peut être assignée à l'aide de la balise `<xf:label/>`.

```xml
<xf:input ref="mon/chemin/xpath">
	<xf:label>Mon premier champ XForms</xf:label>
</xf:input>
```
Ajoutons maintenant les premiers champs à notre formulaire. Si ce n'est déjà fait, ouvrez le fichier `formulaireAlmanach.xml` dans votre éditeur de texte. Pour créer les deux premiers champs, vous aurez besoin des balises `<xf:input/>` et `<xf:textarea/>` pour le titre et le premier paragraphe de la liste des experts. Ces éléments sont placés dans le `<body/>` de votre page.

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<body>
	<xf:input ref="/tei:TEI/tei:text/tei:body/tei:div/tei:head">
		<xf:label>Titre</xf:label>
	</xf:input>
	<br/>
	<xf:textarea ref="/tei:TEI/tei:text/tei:body/tei:div/tei:p">
		<xf:label>Description</xf:label>
	</xf:textarea>
</body>
```
<!-- @todo Faire un point rapide sur XPath s'il reste de la place à la fin ? -->

Vous pouvez définir une hiérarchie dans votre formulaire à l'aide de l'élément `<xf:group/>`. Parallèlement, l'utilisation de cette balise `<xf:group/>` présente l'avantage de définir un contexte dont vont hériter tous les descendants. D'un point de vue pratique, cela simplifie grandement les expressions XPath et les rend beaucoup plus lisible !

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<xf:group ref="/tei:TEI/tei:text/tei:body/tei:div">
	<xf:label>Liste des Experts dans l'almanach royal de l'année 1750</xf:label>
	<xf:input ref="tei:head">
		<xf:label>Titre</xf:label>
	</xf:input>
	<br/>
	<xf:textarea ref="tei:p">
		<xf:label>Description</xf:label>
	</xf:textarea>
</xf:group>
```

Durant la phase de développement du formulaire, et accessoirement pour observer un peu la magie de XForms, il est utile d'afficher la sérialisation de l'instance XML. Cela vous permettra d'observer les modifications que vous apportez à votre modèle de données. Pour ce faire, vous pouvez utiliser l'élément `<xf:output/>`, qui permet d'afficher dynamiquement des données, et la fonction [`fn:serialize()`](https://www.w3.org/TR/xpath-functions-30/#func-serialize).
<!-- @todo faire un point sur la gestion de XPath par XForms et XSLTForms -->

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<xf:group ref="/tei:TEI/tei:text/tei:body/tei:div">
	<xf:label>Liste des Experts dans l'almanach royal de l'année 1750</xf:label>
	<xf:input ref="tei:head" incremental="true">
		<xf:label>Titre</xf:label>
	</xf:input>
	<br/>
	<xf:textarea ref="tei:p" incremental="true">
		<xf:label>Description</xf:label>
	</xf:textarea>
	<xf:output  value="serialize(.)"/>
</xf:group>
```

Notez l'ajout de l'attribut `@incremental="true"` sur les champs `<xf:input/>` et `<xf:textarea/>`. Sans rentrer trop dans les détails, cet attribut modifie le déclanchement de l'événement  `xforms-value-changed` pour un champ donné. Concrètement, cela déclenche la mise à jour du modèle à chaque action de l’utilisateur, au fur et à mesure de la frappe par exemple.

En vous rendant à l'adresse [http://localhost:8000/formulaireAlmanach.xml](http://localhost:8000/formulaireAlmanach.xml), vous devriez voire votre formulaire contenant les deux premiers champs et la sérialisation de votre instance. En tapant quelques lettres dans un des champs, vous devriez voir votre modèle se mettre à jour instantanément.

{% include figure.html filename="figure2.jpg" caption="Figure 2 : le formulaire en cours de construction." %}

Concernant la répartition en deux colonnes de notre liste d'experts, plusieurs solutions sont possibles pour établir la partie du formulaire afférante. 

La première solution consiste à tout coder en "dur". Cette solution a l'avantage d'être simple à mettre en œuvre, dans la mesure où la structure de nos deux colonnes sont identiques. En revanche, cela en fait une solution verbeuse, obligeant à répéter plusieurs fois les mêmes choses. D'autre part elle est peu souple : si nous devions ajouter une troisième colonne par exemple, il faudrait modifier le formulaire manuellement, ainsi que notre modèle. 
```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<xf:group ref="/tei:TEI/tei:text/tei:body/tei:div">
	<xf:label>Liste des Experts</xf:label>
	<xf:input ref="tei:head" incremental="true">
		<xf:label>Titre</xf:label>
	</xf:input>
	<br/>
	<xf:textarea ref="tei:p" incremental="true">
		<xf:label>Description</xf:label>
	</xf:textarea>
	<xf:group ref="tei:div[@type='column'][1]">
		<!-- input, textarea, etc. pour la première colonne -->
	</xf:group>
	<xf:group ref="tei:div[@type='column'][2]">
		<!-- répétition de la même structure pour la seconde colonne -->
	</xf:group>
</xf:group>
```

Heureusement, les développeurs de XForms ont pensé à tout, il est très facile avec XForms de gérer une collection d'éléments qui partagent une même structure. En effet, la balise `<xf:repeat/>` permet de propager des règles communes à un ensemble de nœuds identifiés avec l'attribut `@nodeset`. En l'espèce plutôt que d'ajouter deux éléments `<xf:group/>` correspondant aux deux colonnes, vous pouvez simplement ajouter un élément `<xf:repeat nodeset="tei:div[@type='column']"/>`. Toutes champs ou contrôles que vous ajouterez à l'intérieur de cette balise seront automatique mapper vers le nœuds cibles.

Afin d'expérimenter cette fonctionnalité, vous pouvez ajouter un champ texte pour le titre des colonnes et observer sa propagation.

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<xf:repeat  nodeset="tei:div[@type='column']">
	<xf:input  ref="tei:head"  incremental="true">
		<xf:label>Titre de la colonne</xf:label>
	</xf:input>
</xf:repeat>
```
{% include figure.html filename="figure3.jpg" caption="Figure 3 : l'élément repeat." %}

Efficace, non ?

Concernant les listes à proprement parlé, elles ne devraient pas vous poser de difficulté particulière, il suffit simplement d'appliquer les méthodes que nous avons vues jusqu'à présent, cela va très vite à faire ! 

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<xf:group ref="tei:list">
   <xf:input ref="tei:head">
      <xf:label>Chapeau de liste</xf:label>
   </xf:input>
   <xf:repeat nodeset="tei:item">
      <xf:label>Expert</xf:label>
      <xf:input ref="tei:date">
         <xf:label>Date</xf:label>
      </xf:input>
      <xf:input ref="tei:persName">
         <xf:label>Patronyme</xf:label>
      </xf:input>
      <xf:select1 ref="tei:roleName">
         <xf:label>Statut</xf:label>
         <xf:item>
            <xf:label>Aucun</xf:label>
            <xf:value/>
         </xf:item>
         <xf:item>
            <xf:label>Adjoint</xf:label>
            <xf:value>adjoint</xf:value>
         </xf:item>                     
         <xf:item>
            <xf:label>Doyen</xf:label>
            <xf:value>doyen</xf:value>
         </xf:item>
         <xf:item>
            <xf:label>Syndic</xf:label>
            <xf:value>syndic</xf:value>
         </xf:item>
      </xf:select1>
      <xf:input ref="tei:address/tei:addrLine">
         <xf:label>Adresse</xf:label>
      </xf:input>                  
   </xf:repeat>
</xf:group>
```

Notez que pour le statut nous avons utilisé ici une liste à choix unique afin de vous illustrer la syntaxe, mais selon votre envie, vous pouvez tout aussi bien choisir un champ texte classique. Dans un liste à choix unique ou multiple, chaque élément de la liste est définit par une balise `<xf:item/>`. Cette dernière possède deux sous-éléments :

- `<xf:label/>` pour l'étiquette qui s'affichera dans votre interface ;
- `<xf:value/>` pour la valeur qui sera attribuée au nœud ciblé (`roleName` en l'occurence).

En revanche, comme vous l'aurez remarqué, dans le modèle de données nous n'avons mis qu'un seul élément `<item/>` par `<list/>`. C'est tout simplement parce que, à la différence de la répartition des experts en deux colonnes qui est constante tout au long du 18e siècle, il est impossible de savoir à l'avance combien chaque colonne comptera d'experts bourgeois ou d'experts entrepreneurs. Il est donc nécessaire d'ajouter des contrôles afin d'être en mesure d'insérer (ou de supprimer) des `<item/>` directement depuis l'interface de saisie.
 
### Les contrôles

Les contrôles vont vous permettre d'intéragir directement avec le modèle au regard d'événements déclenchés par des actions : par exemple, lors d'un click sur un bouton, lors de la mise à jour de la valeur d'un champ, lors de la soumission du formulaire, etc.
<!-- @todo ajouter les liens vers la documentation des événements et des actions -->

#### insérer des éléments
La balise `<insert/>` autorise l'insertion d'éléments. Ses attributs permettent de spécifier quel(s) élément(s) doivent être cloné(s) et où ils doivent être placés. Cette insertion est déclenché par un événements. Rappelez-vous, au début de cette leçon nous avons déclaré un espace de nom de manière anticipée `xmlns:ev="http://www.w3.org/2001/xml-events"`, nous allons nous en servir maintenant. 
En premier lieu, il est nécessaire de créer un bouton avec la balise `<xf:trigger/>`, puis d'y ajouter un sous-élément `<xf:insert/>`. Vous compléterez ce dernier par quatre attributs : 

- `@nodeset` : cet attribut évalue le groupe de nœuds à mettre à jour, ici `"tei:item"`. C'est le dernier membre de cette collection qui est cloné pour produire le nœud qui sera inséré à la position spécifiée par les attributs `@at` et `@position` ;
- `@at` : cet attribut prend la valeur d'un index. Pour préciser « à la fin de la collection », vous devez indiquer la fonction XPath `"last()"` comme valeur <!-- @rmq il s'agit aussi du traitement par défaut -->  ; 
- `@position` : l'emplacement où sera inséré le nœud au regard de l'élément ciblé par l'index (`@at`), vous pouvez inscrire `"before"` ou `"after"` respectivement pour « avant » ou « après » <!-- @rmq after est traitement par défaut si l'attribut est omis -->;
- `@ev:event` : pour indiquer l'événement déclencheur, ici [`"DOMActivate"`](https://developer.mozilla.org/en-US/docs/Web/API/Element/DOMActivate_event) pour déclencher l'insertion lors d'un click sur le bouton. 

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<xf:trigger>
	<xf:label>Ajouter un expert</xf:label>
	<xf:insert 
		nodeset="tei:item"
		at="last()"
		position="after"
		ev:event="DOMActivate"/>
</xf:trigger>
```

Votre formulaire devrait maintenant ressembler à ceci : 

```xml
<xf:group ref="/tei:TEI/tei:text/tei:body/tei:div">
   <xf:label>Liste des Experts dans l'almanach royal de l'année 1750</xf:label>
   <xf:input ref="tei:head" incremental="true">
      <xf:label>Titre</xf:label>
   </xf:input>
   <br/>
   <xf:textarea ref="tei:p" incremental="true">
      <xf:label>Description</xf:label>
   </xf:textarea>
   <xf:repeat nodeset="tei:div[@type='column']">
      <xf:input ref="tei:head" incremental="true">
         <xf:label>Titre de la colonne</xf:label>
      </xf:input>
      <xf:group ref="tei:list">
         <xf:input ref="tei:head">
            <xf:label>Chapeau de liste</xf:label>
         </xf:input>
         <xf:repeat nodeset="tei:item">
            <xf:label>Expert</xf:label>
            <xf:input ref="tei:date">
               <xf:label>Date</xf:label>
            </xf:input>
            <xf:input ref="tei:persName">
               <xf:label>Patronyme</xf:label>
            </xf:input>
            <xf:select1 ref="tei:roleName">
               <xf:label>Statut</xf:label>
               <xf:item>
                  <xf:label>aucun</xf:label>
                  <xf:value/>
               </xf:item>
               <xf:item>
                  <xf:label>Adjoint</xf:label>
                  <xf:value>adjoint</xf:value>
               </xf:item>                     
               <xf:item>
                  <xf:label>Doyen</xf:label>
                  <xf:value>doyen</xf:value>
               </xf:item>
               <xf:item>
                  <xf:label>Syndic</xf:label>
                  <xf:value>syndic</xf:value>
               </xf:item>
            </xf:select1>
            <xf:input ref="tei:address/tei:addrLine">
               <xf:label>Adresse</xf:label>
            </xf:input>                  
         </xf:repeat>
         <xf:trigger>
            <xf:label>Ajouter un expert</xf:label>
            <xf:insert nodeset="tei:item" at="last()" position="after" ev:event="DOMActivate"/>
         </xf:trigger>
      </xf:group>
   </xf:repeat>
   <xf:output value="serialize(.)"/>
</xf:group>
```

L'utilisation conjointe des éléments `<xf:repeat/>` et `<xf:insert/>` autorise la création de formulaire dynamique très facilement, toutefois, il est possible d'aller encore plus loin ! Lors de la transcription d'une liste, qui n'a jamais sauté une entrée ? Et dans une telle éventualité, comment réintégrer une ligne à la bonne place ? Encore une fois les développeurs de XForms ont pensé à tout !

Commencez par ajouter un `@id` au dernier élément `<xf:repeat/>` : 
```xml
<xf:repeat  id="listItem"  nodeset="tei:item">
	<!-- […] -->
</xf:repeat>
```
L'ajout de cet identifiant créer un index des « répétitions » qu'il est possible de d'interroger avec la fonction [`index()`](https://www.w3.org/TR/2003/PR-xforms-20030801/slice7.html#fn-index). Ensuite, il suffit simplement de modifier la valeur de l'attribut `@at="last()"` par `@at="index('listItem')"`. Dès lors, si vous modifiez une entrée de la liste et que vous cliquez sur le bouton pour ajouter une entrée, cette dernière sera insérée à la suite de l'item en cours de modification. 

{% include figure.html filename="figure4.jpg" caption="Figure 4 : la fonction index()." %}

#### Mettre à jour la valeur d'un nœud

Vous l'aurez sans doute remarqué, si le contenu du dernier item n'est pas vide, lorsque vous cliquez sur le bouton « ajouter un expert », le contenu est également copié. C'est simplement parce que `<xf:insert/>` induit une [copie profonde](https://developer.mozilla.org/fr/docs/Glossary/Deep_copy) (*deep copy*), il est toutefois possible d'effacer le contenu des éléments nouvellement créés avec la balise `<xf:setvalue/>`. Trois attributs vous seront utiles :

- `@ref` permet de cibler le nœud dont nous souhaitons mettre à jour la valeur ;
- `@value` précise la valeur que nous souhaitons attribuer au nœud ciblé avec `@ref ` ;
- `@ev:event` pour indiquer l'événement déclencheur.

Ici aussi, il est possible de tirer partie de la fonction `index()` vue plus haut, simplement en l'utilisant comme prédicat dans notre chemin XPath : 

```xml
<xf:repeat  id="listItem"  nodeset="tei:item">
	<xf:insert nodeset="tei:item" at="index('listItem')" position="after" ev:event="DOMActivate"/>
	<xf:setvalue ref="tei:item[index('listItem')]/tei:date" value="''" ev:event="DOMActivate"/>
	<xf:setvalue ref="tei:item[index('listItem')]/tei:persName" value="''" ev:event="DOMActivate"/>
	<xf:setvalue ref="tei:item[index('listItem')]/tei:roleName" value="''" ev:event="DOMActivate"/>
	<xf:setvalue ref="tei:item[index('listItem')]/tei:address/addrLine" value="''" ev:event="DOMActivate"/>
</xf:repeat>
```

Pour les néophytes des technologies XML, vous noterez que pour indiquer une valeur nulle, on utilise l'expression `@value="''"` avec des guillemets simples placés à l'intérieur des guillemets doubles.


#### Supprimer un nœud
Avec la balise [`<xf:delete/>`](https://www.w3.org/TR/xforms11/#action-delete) vous pouvez supprimer un ou plusieurs nœuds. Comme toutes les actions, elle est déclenchée par un événement. Dans le formulaire que vous construisez, il peut être souhaitable d'autoriser la suppression des items dans les listes d'experts.

Commencez par créer un bouton à l'intérieur du `<xf:repeat id="listItem"  nodeset="tei:item"/>`. Vous pouvez choisir de le placer à la suite des champs, ou bien dans l'élément `<xf:label/>`, à vous de voir !  Ajoutez à ce bouton l'élément `<xf:delete/>` et conditionnez sont déclenchement au click sur le bouton avec l'attribut `@ev:event="DOMActivate"`. Enfin le nœud à supprimer doit être précisé à l'aide de l'attribut `@nodeset` de valeur `"."`, afin d'indiquer le nœud courant. Vous devriez obtenir quelque chose comment ceci :

```xml
<xf:label>
   Expert
   <xf:trigger>
      <xf:label>🗑</xf:label>
      <xf:delete nodeset="." ev:event="DOMActivate"/>
   </xf:trigger>
</xf:label>
``` 
Houston, on a un problème ! Comment empêcher de supprimer tous les items de la liste par erreur ? Avec XForms, les actions peuvent être conditionnées avec l'emploi d'un attribut `@if`, qui accepte une expression XPath comme valeur. Pour éviter de vider la liste, vous pouvez par exemple compter les éléments `<item/>` et conditionner l'action au fait que ce nombre soit supérieur à 1, ce qui donne l'expression XPath `count(parent::tei:list/tei:item) > 1`.

```xml
<xf:label>
   Expert
   <xf:trigger>
      <xf:label>🗑</xf:label>
      <xf:delete 
	      if="count(parent::tei:list/tei:item) > 1"
	      nodeset="."
	      ev:event="DOMActivate"/>
   </xf:trigger>
</xf:label>
```

### Typage et contraintes sur les données
XForms propose bien évidemment des fonctionnalités avancées pour contraindre les données. Il est notamment possible de rendre obligatoire certains champs, d'ajouter des contraintes relatives à la valeur des nœuds, de confronter ces mêmes valeurs aux types XML, ou encore de calculer des valeurs automatiquement. Toutes ces contraintes doivent être déclarées dans le `<xf:model/>` avec la balise `<xf:bind/>`.


<div  class="alert alert-warning">

Le standard XForms propose aussi la validation d'instances au regard de schemas XML, mais cette fonctionnalité n'est pas encore disponible avec le client XSLTForms. D'autres clients XForms ont intégré cette possibilité, à l'image de betterForm, qui n'est malheureusement plus maintenu.

```xml
<xf:model schema="schema.xsd">
   <xf:instance src="instance.xml"/>
</xf:model>
```
</div>

#### Champ obligatoire
Avant de soumettre un formulaire à l'enregistrement, vous pourriez souhaiter que certains champs soient renseignés. C'est précisément l'objet de l'attribut `@required`, qui prend pour valeur `true()` ou `false()`. Dans le cadre de notre formulaire nous pourrions souhaiter qu'à minima le titre principale soit renseigné.

Commencez par ajouter un élément `<xf:bind/>` dans votre modèle. Cet élément est relié aux nœuds que vous souhaitez contrôler par l'intermédiaire de l'attribut `@nodeset`, puis ajoutez simplement la propriété `@required="true()"`.

```xml
<xf:model>
   <xf:instance src="instanceAlmanach.xml"/>
   <xf:bind nodeset="/tei:TEI/tei:text/tei:body/tei:div/tei:head" required="true()"/>
</xf:model>
```

En rechargeant votre formulaire, vous devriez voir une petite étoile rouge à côté du label attaché au champ, indiquant qu'une valeur est attendue.

#### Typage des données
L'attribut `@type` permet de contrôler le type de données attendu pour un nœud (*string*, *integer*, *date*, *boolean*, *card-number*, *email*, etc.). Il est possible d'appeler directement un type XML, ou d'en définir un avec une règle `xs:schema`.
Toujours dans le `<xf:model/>`, ajoutez un contrôle du type pour le nœud `<date/>`. Dans les almanachs, seule l'année est indiquée, et le type XML correspondant est `gYear`.

```xml
<xf:model>
   <xf:instance src="instanceAlmanach.xml"/>
   <xf:bind nodeset="/tei:TEI/tei:text/tei:body/tei:div/tei:head" required="true()"/>
   <xf:bind nodeset="/tei:TEI/tei:text/tei:body/tei:div/tei:div/tei:list/tei:item/tei:date" type="gYear"/>
</xf:model>
```

Le typage des données peut avoir des implications plus profondes sur votre formulaire. Par exemple, si vous choisissez un type `date` pour le nœud `<date/>`, vous observerez que le champ devient un sélecteur de date.

{% include figure.html filename="figure5.jpg" caption="Figure 5 : typage des données et sélecteur de date." %}

#### Contraindre les valeurs
<!-- @todo vérifier la date de fin des colonnes -->
Pour aller plus loin concernant les dates, il est établi que les deux colonnes d'experts ont existé entre 1690 et 1792, les valeurs acceptée par le champ date doivent donc être comprises entre ces deux nombres. Plus précisément, la valeur du champ doit être supérieure ou égale à 1690, mais inférieure ou égale à 1792. En l'espèce, l'expression XPath correspondante est `. >= 1690 and . <= 1792`. Ce contrôle est rendu possible par l'utilisation de l'attribut [`@constraint`](https://www.w3.org/TR/xforms11/#model-prop-constraint) qui prend pour valeur toute expression XPath qui retourne un [booléen](https://developer.mozilla.org/fr/docs/Glossary/Boolean). Vous pouvez donc compléter la règle créée précédemment de la manière suivante : 

```xml
<xf:model>
   <xf:instance src="instanceAlmanach.xml"/>
   <xf:bind nodeset="/tei:TEI/tei:text/tei:body/tei:div/tei:head" required="true()"/>
   <xf:bind 
      nodeset="/tei:TEI/tei:text/tei:body/tei:div/tei:div/tei:list/tei:item/tei:date"
      type="gYear"
      constraint=". &gt;= 1690 and . &lt;= 1792"/>
</xf:model>
```

Avec XML, les signes `<` et `>` sont réservés, vous aurez donc remarqué qu'ils ont été remplacés les entités correspondantes. 

<u>#### Calculer des valeurs automatiquement</u>
<!-- @rmq pas forcément pertinent, ici si ce n'est montrer que la fonctionnalité existe, mais dans l'exemple, ce ne serait qu'une recopie (fonctionnalité un peu détournée…) si on ne l'utilise pas, retirer l'attribut @when de l'élément date. -->

#### Aides à la saisie
En outre, vous pouvez également ajouter des aides à la saisie ou des alertes à l'aide des éléments `<xf:help/>`, `<xf:hint/>` et `<xf:alert/>`

- `<xf:help/>` apporte une aide à la saisie ;
- `<xf:hint/>` propose des suggestions ;
- `<xf:alert/>` retourne un message d'alerte. Toutefois, il ne bénéficie pas d'un traitement par défaut, et nécessite un implémentation au niveau de l'application XForms développée.

Ces trois éléments sont placés directement dans un champ de formulaire (`<xf:input/>`, `<xf:textarea/>`, `<xf:select/>`, etc.). Si vous disposez d'une documentation au format XML, au hasard un fichier [ODD](https://tei-c.org/guidelines/customization/getting-started-with-p5-odds/), vous pouvez faire pointer ces assistances vers votre documentation avec un attribut `@ref`, sous réserve que cette dernière soit appelée dans vos instances.

Dans ce formulaire, vous pouvez ajouter des assistances aux champs `date` de la manière suivante :

```xml
<xf:input ref="tei:date">
   <xf:label>Date</xf:label>
   <xf:hint>Année au format 'AAAA', par exemple : 1750.</xf:hint>
   <xf:help>La valeur de ce champ doit être comprise entre 1696 et 1792.</xf:help>
</xf:input>
```

### Soumettre le formulaire
Voilà, le formulaire est pour ainsi dire terminé, il ne reste plus qu'à l'enregistrer. XForms propose un module de soumission très complet, comprenant de multiples paramètres de sérialisation et compatible avec de nombreux protocoles. Comme indiqué plus haut, les paramètres de soumission sont déclarés dans le `<xf:model/>` à l'aide de la balise [`<xf:submission/>`](https://www.w3.org/TR/xforms11/#submit).

Pour cette leçon, vous allez apprendre à récupérer un fichier correspondant à l'instance XML modifiée.

Commencez par ajouter une balise `<xf:submission/>` dans votre `<xf:model/>`, puis précisez la [méthode](https://developer.mozilla.org/fr/docs/Web/HTTP/Methods) de soumission souhaitée (attribut `@method`) et le [type MIME](https://developer.mozilla.org/fr/docs/Web/HTTP/Basics_of_HTTP/MIME_types) afin de préciser la nature et le format du document envoyé (attribut `@mediatype`). Les méthodes les plus utilisées sont généralement `POST` et `PUT` ; en l'espèce vous aurez recours à la méthode `PUT` (`@method="put"`). Concernant le type MIME, vous indiquerez `@mediatype="application/xml"`

Il reste encore à préciser où envoyer votre document. C'est le rôle de l'attribut `@resource`. Vous pouvre choisir d'envoyer votre formulaire vers un scrip, qui s'occupera processer les données envoyer, ou d'utiliser le scheme `file:` pour récupérer l'instance. 

```xml
<xf:model>
   <xf:instance src="instanceAlmanach.xml"/>
   
   <xf:bind nodeset="/tei:TEI/tei:text/tei:body/tei:div/tei:head" required="true()"/>
   <xf:bind 
      nodeset="/tei:TEI/tei:text/tei:body/tei:div/tei:div/tei:list/tei:item/tei:date"
      type="gYear"
      constraint=". &gt;= 1690 and . &lt;= 1792"/>
   <xf:bind nodeset="/tei:TEI/tei:text/tei:body/tei:div/tei:div/tei:list/tei:item/tei:date/@when" calculate="parent::tei:date"/>
   
   <xf:submission method="put" resource="file:" mediatype="application/xml"/>
</xf:model>
```

Finalement, il ne reste plus qu'à créer un bouton pour initier la soumission. Pour ce faire, vous devez simplement ajouter un élément `<xf:submit/>` dans votre formulaire, accompagné d'un `<xf:label/>` de votre choix.

```xml
<xf:submit>
   <xf:label>Sauvegarder</xf:label>
</xf:submit>
```

Félicitations, vous avez créé un formulaire XForms reposant sur un modèle de données TEI, comportant des contrôles avancés et des aides à saisie, et capable d'enregistrer votre travail, le tout en utilisant une unique pile technologique : XML !

## Conclusion ? 
parler les aspects non abordés ? mais il y a en a beaucoup
Design ?
renvoyer vers le repo avec les fichiers sources ?

## Bibliographie ?

[Wiki XSLTForms](https://en.wikibooks.org/wiki/XSLTForms)
[Wiki XForms](https://en.wikibooks.org/wiki/XForms)
[W3C, XForms 1.1 ](https://www.w3.org/TR/xforms11/)