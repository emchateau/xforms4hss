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
Cette leçon propose une initiation à [XForms](https://fr.wikipedia.org/wiki/XForms) à travers l’élaboration d’un formulaire pour la saisie de données sérielles issues d’annuaires historiques : les Almanachs royaux. Elle vous permettra d’apprendre à rédiger un formulaire web dynamique et contrôlé, et d’enregistrer les données saisies directement au format XML.

### Prérequis
Cette leçon suppose que vous ayez :
- une connaissance de base des langages [HTML](https://html.spec.whatwg.org/multipage/), [XML](https://www.w3.org/TR/xml/) et [XPath](https://www.w3.org/TR/xpath-31/). La maîtrise de la [Text Encoding Initiative](https://tei-c.org/) est un plus, mais n’est pas nécessaire ;
- téléchargé le client [XSLTForms](https://github.com/AlainCouthures/declarative4all/releases/latest) ;
- accès à une machine sur laquelle vous pouvez déployer un serveur local, ou avoir accès à un serveur web, pour servir des documents HTML ou XML.

### XForms et XSLTForms
[XForms](https://www.w3.org/TR/xforms11/) est un dialecte XML spécialisé dans la création de formulaires web standardisé par le W3C à partir de 2006. Il s’appuie sur une architecture Modèle - Vue - Contrôleur (MVC). En distinguant fond et forme, cette technologie offre de nombreux avantages en termes de possibiliité de réutilisation des formulaires, de compatibilité avec les différents types de périphériques (ordinateurs, téléphones, tablettes, etc.), et d’accessibilité. Plus qu’un simple langage de formulaire, dans sa version 1.1 XForms est devenu un langage [Turing-complet](https://fr.wikipedia.org/wiki/Turing-complet) qui adopte un modèle de [programmation déclaratif](https://fr.wikipedia.org/wiki/Programmation_d%C3%A9clarative) où l’on s’attache à décrire ce que l’on souhaite faire plutôt que comment. Ce langage est capable de soutenir le développement d’applications à grande échelle.

Dans le champ des sciences humaines et sociales, la communauté scientifique a largement adopté le format XML pour la gestion des métadonnées. Plusieurs standards de métadonnées internationaux peuvent ainsi être utiles aux chercheurs à l’instar de la [Text Encoding Initiative (TEI)](https://fr.wikipedia.org/wiki/Text_Encoding_Initiative) ou encore de l’[Encoded Archival Description (EAD)](https://fr.wikipedia.org/wiki/Description_archivistique_encod%C3%A9e), ou encore l’[Encoded Archival Context - Corporate bodies, Persons, Families (EAC-CPF)](https://eac.staatsbibliothek-berlin.de). L’utilisation du format XML du W3C offre beaucoup d’avantages de le contexte de la production des métadonnées en raison de l’existance de technologies de schémas qui permettent la validation des documents et garantit leur validation. Cela permet d’assurer un haut niveau de portabilité et d’interopérabilité, par ailleurs ce format présente une grande souplesse d’utilisation qui s’articule facilement avec de nombreux systèmes d’information. Pour autant, lorsqu’il s’agit de produire des données au format XML, peu de solutions s’offrent réellement aux équipes de recherche mis à par l’édition de documents directement dans un éditeur de texte.

La création de formulaires avec XForms permet d’éditer des instances XML directement dans un navigateur, tout en embarquant des fonctionnalités avancées propres aux technologies XML. Elle constitue donc une solution de choix pour faciliter l’édition de documents XML. Celle-ci présente ainsi un intérêt particulier pour les ingénieurs et les chercheurs qui souhaitent travailler dans l’environnement XML. En effet, XForms facilite la mise en place de formulaires dynamiques qui peuvent parfois s’avérer très complexes à mettre en œuvre avec d’autres technologies lorsqu’ils comportent, par exemple, des répétitions, des contrôles sur les valeurs ou des règles de contraintes avancées.  

Malgré des qualités indéniables, cette recommandation du W3C n’a toutefois jamais été prise en charge par les navigateurs et nécessite actuellement l’utilisation d’un client pour s’exécuter. Pour cette leçon, nous utiliserons le client *open source* [XSLTForms](https://github.com/AlainCouthures/declarative4all) développé par Alain Couthures.

### Les Almanachs royaux
Publiés au format in-8° chaque année entre 1700 et 1792, les [Almanachs royaux](https://fr.wikipedia.org/wiki/Almanach_royal) comportaient, entre autres choses, la liste des membres de la famille royale, des principaux officiers du royaume, ou encore des hauts représentants du clergé. De par la richesse des informations qu’ils contiennent (patronyme des individus, appartenance et position dans un corps, adresse) ces sources intéressent de nombreux historiens. Les Almanachs royaux sont accessibles dans leur intégralité via [Gallica](https://gallica.bnf.fr/ark:/12148/cb34454105m/date.item) ou encore le [centre de recherche du château de Versailles](https://www.chateauversailles-recherche.fr/francais/ressources-documentaires/corpus-electroniques/sources-imprimees/periodiques/l-almanach-royal-1683-1792.html).

L’exemple utilisé dans le cadre de cette leçon est celui des listes d’experts-jurés parisiens du bâtiment, répartis, à partir de 1690, en deux colonnes : les architectes experts bourgeois et les experts entrepreneurs. Arbitrairement, nous nous appuierons sur la liste des experts pour l’année 1750, disponible en ligne à cette adresse : [<https://gallica.bnf.fr/ark:/12148/bpt6k204719d/f342.item>](https://gallica.bnf.fr/ark:/12148/bpt6k204719d/f342.item). Outre la répartition en deux colonnes, la liste indique pour chaque individu son année d’entrée dans le corps des experts, son patronyme, éventuellement son statut au sein de la communauté, et enfin son adresse.

## Configuration de l’environnement de développement
Préalablement à cette leçon, vous devez télécharger le client [XSLTForms](https://github.com/AlainCouthures/declarative4all/releases/latest) (`xsltforms.zip`), puis décompresser l’archive à l’emplacement de votre choix sur votre ordinateur. 

Les instances XForms doivent être servies par l’intermédiaire d’un serveur web. Si vous n’en disposez pas, voici comment déployer rapidement un serveur local avec [Python](https://www.python.org/), un langage de programmation largement utilisé et généralement installé par défaut sur les systèmes macOS et Linux, et qu’il vous faudra éventuellement installer sous Windows. 

Tout d’abord, il est important de déterminer la version de Python installée sur votre machine. Pour ce faire, vous pouvez entrer l’une des deux commandes suivantes dans votre [émulateur de terminal :

```bash
python -v
# ou
python3 -v
```

Cette commande retourne la version de Python installée (généralement `Python 2.XX` ou `Python 3.XX`). 

À l’aide de la commande `cd`, changez maintenant votre répertoire de travail pour le dossier `xsltforms` que vous avez précédemment dézippé.

 ```bash
cd chemin/vers/le/répertoire/xsltfoms
 ```

Si vous n’êtes pas très à l’aise avec l’utilisation du terminal, vous pouvez toujours vous référez à la leçon « [Introduction à l’interface en ligne de commande Bash et Zsh](https://doi.org/10.46430/phfr0031) » de Ian Milligan et James Baker, traduite en français par Melvin Hersent.

Ensuite, selon la version de Python disponible sur votre système, entrez l’une des commandes suivantes pour démarrer le serveur local sur le port `8000` :

```bash
# Python 3.XX
python3 -m http.server 8000
# Python 2.XX
python -m SimpleHTTPServer 8000
```

Dans votre navigateur, vous pouvez désormais vous rendre à l’adresse [`localhost:8000`](http://localhost:8000/). Par défaut, vous devriez voir la liste des fichiers contenus dans le dossier `xsltforms`.

D’autres solutions existent pour déployer un serveur local (Julia, NodeJS, Apache, PHP, etc). Vous pouvez consulter ce repo Github afin les autres possibilités : <[https://github.com/sardinecan/localhost/](https://github.com/sardinecan/localhost/)>

Si vous utilisez VisualStudio, l’extension [Live Preview](https://marketplace.visualstudio.com/items?itemName=ms-vscode.live-server) peut vous permettre de servir le contenu d’un dossier dans un navigateur. 

## Principes de mise en œuvre 
 À l’aide d’un éditeur de texte, ouvrez le fichier `hello.xml`, l’exemple fourni avec `xsltforms`, afin d’étudier la structure d’un formulaire XForms.

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
Reposant sur la syntaxe XML, XForms a été conçu pour s’intégrer directement dans des pages XHTML et pour s’articuler parfaitement avec les autres standards du W3C (CSS et XPath). Afin de distinguer les segments XForms du code XHTML, on utilise des [espaces de noms](https://fr.wikipedia.org/wiki/Espace_de_noms). Dans l’exemple ci-dessus, l’espace de nom XForms est déclaré par l’intermédiaire de la déclaration d’espace de nom `xmlns:xf` (`<html xmlns:xf="http://www.w3.org/2002/xforms"/>`). L’utilisation du préfixe `xf` permet ensuite d’identifier facilement les éléments XForms dans le reste du document.

```xml
<xf:input  ref="PersonGivenName"  incremental="true">
	<xf:label>Please enter your first name: </xf:label>
</xf:input>
```

La déclaration du client XSLTForms se fait très simplement, par l’intermédiaire d’une instruction de traitement qui est ajoutée dans le préambule du document pointant vers la feuille de transformation `xsltforms.xsl` :

```xml
<?xml-stylesheet href="xsltforms.xsl" type="text/xsl"?>
```

### L’architecture modèle-vue-contrôleur de XForms
Contrairement aux formulaires XHTML, XForms adopte une [architecture Modèle-Vue-Contrôleur](https://fr.wikipedia.org/wiki/Mod%C3%A8le-vue-contr%C3%B4leur) (MVC) destinée à faciliter la maintenance des formulaires et à favoriser leur réutilisation.

####  Modèle
Le **modèle** XForms [`<xf:model/>`](https://www.w3.org/TR/xforms11/#structure-model) contient le modèle de données exprimé sous la forme d’une ou plusieurs instances XML contenues dans un ou plusieurs éléments [`<xf:instance/>`](https://www.w3.org/TR/xforms11/#structure-model-instance).

```xml
<xf:model>
	<xf:instance>
		<data  xmlns="">
			<PersonGivenName/>
		</data>
	</xf:instance>
</xf:model>
```

Le modèle peut également contenir des règles de validation, des contraintes, des déclarations de types de données ou encore des paramètres de soumission, dont nous verrons quelques applications à la fin de cette leçon. Plusieurs modèles peuvent être déclarés au sein d’une même page.

#### Vue
La **vue** régit l’affichage et la présentation des données. Elle détermine comment les données du modèle sont représentées à l’écran.

```xml
<xf:input  ref="PersonGivenName"  incremental="true">
	<xf:label>First name: </xf:label>
	<xf:hint>Please enter your first name</xf:hint>
</xf:input>
<xf:output  value="concat('Hello ', PersonGivenName, '. We hope you like XForms!')">
	<xf:label>Output: </xf:label>
</xf:output>
```

C’est ici que l’on définit les différents champs de saisie des données (`<xf:input/>`), les éléments relatifs aux étiquettes ou à l’aide (`<xf:label/>`, `<xf:hint/>`), ou encore des balises pour afficher dynamiquement des données (`<xf:output/>`).

#### Contrôleur
La couche **contrôleur** gère les interactions entre la vue et le modèle. Elle orchestre la mise à jour des données en réponse aux actions des utilisateurs et peut exécuter des actions spécifiques comme la soumissions des données. Nous verrons des exemples dans la suite de cette leçon.

Maintenant que nous avons passé en revue les grands principes qui régissent XForms, réalisons notre formulaire pour la saisie des annuaires historiques.

## *Text Encoding Initiative* et modèle de données
L’étape préalable à la création d’un formulaire de saisie est l’élaboration d’un modèle de données. Pour cette leçon, nous nous appuierons sur le modèle proposé par la [*Text Encoding Initiative*](https://tei-c.org/), un dialecte XML largement adopté par la communauté des sciences humaines et sociales et spécialisé dans le balisage de ressources textuelles.

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
À l’image des fichiers HTML, un document XML-TEI minimal se compose de deux parties : 
- l’entête [`<teiHeader/>`](https://tei-c.org/release/doc/tei-p5-doc/fr/html/ref-teiHeader.html), contenant les informations sur la source et les autres métadonnées ; 
- le contenu principal [`<text/>`](https://tei-c.org/release/doc/tei-p5-doc/fr/html/ref-text.html), rassemblant les données textuelles.

Pour cette leçon, nous laisserons de côté le `<teiHeader/>`, afin de nous concentrer sur le corps du document, contenu dans la balise `<body/>`. Toutefois, les mécanismes détaillés plus bas pourront tout-à-fait être réutilisés pour renseigner l’entête des documents.

L’analyse des annuaires révèle une structure relativement simple, presque tabulaire, constituée de titres, de paragraphes et de listes, où, pour chaque expert, sont indiqués son année d’entrée dans la corps des experts, son patronyme, éventuellement son statut dans la communauté et enfin son adresse.

{% include figure.html filename="figure1.jpg" caption="Figure 1 : Liste des experts parisiens du bâtiment pour l’année 1750." %}

Avec ces éléments, nous pouvons envisager la modélisation suivante :

- le titre de la liste et le paragraphe sont encodés respectivement avec les balises `<tei:head/>` et `<tei:p/>` ;
- chaque colonne est placée dans un élément `<tei:div type="column"/>`, et son titre dans une balise `<tei:head/>` ;
- chaque liste est ensuite étiquetée avec un élément `<tei:list/>`, comportant un élément `<tei:head/>` pour le petit chapeau, et une suite d’éléments `<tei:item/>` pour chaque entrée, contenant les sous-éléments :
	- `<tei:date/>` pour la date, 
	- `<tei:persName/>` pour le patronyme, 
	- `<tei:roleName/>` pour le statut, 
	- et `<tei:address/>` pour l’adresse

<!-- @quest ce n’est pas super sémantique, ne veut-on pas plutôt faire une liste de personnes ? -->

Le tout est placé dans une balise `<div/>` de la manière suivante : 

```xml
<!-- // xsltforms/instanceAlmanach.xml -->
<?xml version="1.0" encoding="UTF-8"?>
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
                        <addrLine/><!--ligne d’adresse-->
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

Bien évidemment, ce n’est qu’une modélisation possible parmi beaucoup d’autres.

Cette instance, vide pour le moment, peut être sauvegardée dans un fichier que vous nommerez pour l’exercice `instanceAlmanach.xml`, et que vous placerez dans le dossier `xsltforms`. Les commentaires peuvent cependant être supprimés.

## Construction du formulaire
Les formulaires XForms ont été conçus pour s’intégrer au sein de pages XHTML. Commencez par créer une page web minimaliste dans le dossier `xsltforms` que vous nommerez `formulaireAlmanach.xml`

<!-- @quest ne faut-il pas une information sur le fait qu’il doit s’agir d’un document XML ou XHTML ? enjeux pour le service de al page -->

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<html xmlns="http://www.w3.org/1999/xhtml" lang="fr">
	<head>
		<title>Formulaire almanach</title>
	</head>
	<body><!-- ... --></body>
</html>
```

Afin d’implémenter le client XSLTForms, vous devez ajouter l’instruction de traitement vue plus haut dans le préambule de votre page web.

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet href="xsltforms.xsl" type="text/xsl"?>
<html xmlns="http://www.w3.org/1999/xhtml" lang="fr">
	<head>
		<title>Formulaire almanach</title>
	</head>
		<body><!-- ... --></body>
</html>
```

Ensuite, il est essentiel de déclarer les espaces de nom pour les différents dialectes XML que nous utiliserons :
- `xmlns:xf="http://www.w3.org/2002/xforms"` pour XForms ;
- `xmlns:tei="http://www.tei-c.org/ns/1.0"` pour la TEI ;
- `xmlns:ev="http://www.w3.org/2001/xml-events"` pour les [événements XML](https://en.wikipedia.org/wiki/XML_Events), dont nous aurons besoin ultérieurement.

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

Si vous travaillez avec Firefox, sachez que ce navigateur ne prend que partiellement en charge les espaces de noms. Il est possible de contourner cette limitation en ajoutant l’attribut `tei:bogus="https://bugzilla.mozilla.org/show_bug.cgi?id=94270"`.

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<html  
	xmlns="http://www.w3.org/1999/xhtml"
	xmlns:xf="http://www.w3.org/2002/xforms"
	xmlns:tei="http://www.tei-c.org/ns/1.0"
  xmlns:ev="http://www.w3.org/2001/xml-events"
	tei:bogus="https://bugzilla.mozilla.org/show_bug.cgi?id=94270"
	lang="fr"/>
```

<!-- @quest il y avait la balise fermante </div> dans le document, je ne crois pas qu’elle serve à qqchose -->

### Déclaration du modèle
Le modèle doit être placé dans l’entête de la page web avec la balise `<xf:model/>`. Au sein de cette balise, vous ajouterez un sous-élément `<xf:instance/>` pointant vers le fichier `instanceAlmanach.xml` de la manière suivante :

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<head>
   <title>Formulaire almanach</title>
   <xf:model>
      <xf:instance src="instanceAlmanach.xml"/>
   </xf:model>
</head>
```

### Le formulaire
À l’instar de HTML, XForms propose plusieurs types de champs. Les principaux sont les suivants :

- [`<xf:input/>`](https://www.w3.org/TR/xforms11/#ui-input) : champ texte ;
- [`<xf:textarea/>`](https://www.w3.org/TR/xforms11/#ui-textarea) : champ bloc de texte ;
- [`<xf:select/>`](https://www.w3.org/TR/xforms11/#ui-selectMany) : champ sélection multiple (type *checkbox*) ;
- [`<xf:select1/>`](https://www.w3.org/TR/xforms11/#ui-selectOne) : champ sélection unique (type *radio*).

Chaque champ du formulaire peut être directement relié à un nœud de l’instance XML par l’intermédiaire d’un chemin XPath qui est indiqué dans un attribut `@ref. Il est par ailleurs possible d’ajouter une étiquette avec la balise [`<xf:label/>`](https://www.w3.org/TR/xforms11/#ui-commonelems-label).

```xml
<xf:input ref="mon/chemin/xpath">
	<xf:label>Mon premier champ XForms</xf:label>
</xf:input>
```

Ajoutons les premiers champs à notre formulaire. Si ce n’est déjà fait, ouvrez le fichier `formulaireAlmanach.xml` dans votre éditeur de texte. Pour créer les deux premiers champs, nous aurons besoin des balises `<xf:input/>` pour le titre et `<xf:textarea/>` pour le premier paragraphe. Ces éléments doivent être placés dans le corps de votre page web, à l’intérieur de l’élément `<body/>`.

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

<!-- @todo Faire un point rapide sur XPath s’il reste de la place à la fin ? -->

Vous pouvez définir une hiérarchie dans votre formulaire à l’aide de l’élément [`<xf:group/>`](https://www.w3.org/TR/xforms11/#ui-group). Parallèlement, l’utilisation du module groupe présente l’avantage de définir un contexte dont vont hériter tous les descendants. D’un point de vue pratique, cela simplifie grandement les expressions XPath et les rend beaucoup plus lisibles !

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<xf:group ref="/tei:TEI/tei:text/tei:body/tei:div">
	<xf:label>Liste des Experts dans l’almanach royal de l’année 1750</xf:label>
	<xf:input ref="tei:head">
		<xf:label>Titre</xf:label>
	</xf:input>
	<br/>
	<xf:textarea ref="tei:p">
		<xf:label>Description</xf:label>
	</xf:textarea>
</xf:group>
```

Durant la phase de développement du formulaire, et accessoirement pour observer un peu la magie de XForms, il est utile d’afficher la sérialisation de l’instance XML. Cela vous permettra d’observer les modifications que vous apportez à votre modèle de données en temps réel. Pour ce faire, vous pouvez utiliser l’élément [`<xf:output/>`](https://www.w3.org/TR/xforms11/#ui-output), qui permet d’afficher dynamiquement des données, et la fonction XPath [`fn:serialize()`](https://www.w3.org/TR/xpath-functions-30/#func-serialize).

<!-- @todo faire un point sur la gestion de XPath par XForms et XSLTForms -->

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<xf:group ref="/tei:TEI/tei:text/tei:body/tei:div">
	<xf:label>Liste des Experts dans l’almanach royal de l’année 1750</xf:label>
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

Vous aurez noté l’ajout de l’attribut `@incremental="true"` sur les champs `<xf:input/>` et `<xf:textarea/>`. Sans entrer trop dans les détails, cet attribut modifie le déclanchement de l’événement [`xforms-value-changed`](https://www.w3.org/TR/xforms11/#evt-valueChanged) pour un champ donné. Concrètement, cela déclenche la mise à jour du modèle à chaque action de l’utilisateur, au fur et à mesure que vous entrez des informations dans dans un champ par exemple.

En vous rendant à l’adresse [http://localhost:8000/formulaireAlmanach.xml](http://localhost:8000/formulaireAlmanach.xml), vous devriez voir votre formulaire contenant les deux premiers champs et la sérialisation de votre instance. En tapant quelques lettres dans un des champs, vous devriez voir votre modèle se mettre à jour instantanément.

{% include figure.html filename="figure2.jpg" caption="Figure 2 : le formulaire en cours de construction." %}

Concernant la répartition en deux colonnes de notre liste d’experts, plusieurs solutions sont possibles pour établir la partie du formulaire afférante. 

La première solution consiste à tout coder « en dur ». Cette méthode présente l’avantage d’être simple à mettre en œuvre, dans la mesure où la structure de nos deux colonnes sont identiques. En revanche, cette solution est verbeuse car elle oblige à répéter plusieurs fois les mêmes choses. Elle manque également de souplesse : pour ajouter une troisième colonne, il faudrait modifier le formulaire et le modèle manuellement. 

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

Heureusement, les développeurs de XForms ont pensé à tout, il est ainsi très facile de gérer une collection d’éléments qui partagent une même structure. En effet, la balise [`<xf:repeat/>`](https://www.w3.org/TR/xforms11/#ui-repeat) permet de propager des règles communes à un ensemble de nœuds identifiés par l’attribut `@nodeset`. En l’espèce, plutôt que d’ajouter deux éléments `<xf:group/>` correspondant aux deux colonnes, nous pouvons simplement ajouter un élément `<xf:repeat nodeset="tei:div[@type='column']"/>`. Tous les champs ou contrôles que vous ajouterez à l’intérieur de cette balise seront automatiquement mapper vers le nœuds cibles.

Afin d’expérimenter cette fonctionnalité, vous pouvez ajouter un champ texte pour le titre des colonnes, puis observez le résultat dans votre navigateur.

```xml
<!-- // xsltforms/formulaireAlmanach.xml -->
<xf:repeat  nodeset="tei:div[@type='column']">
	<xf:input  ref="tei:head"  incremental="true">
		<xf:label>Titre de la colonne</xf:label>
	</xf:input>
</xf:repeat>
```
{% include figure.html filename="figure3.jpg" caption="Figure 3 : l’élément repeat." %}

Efficace, non ? Concernant les listes à proprement parlé, elles ne devraient pas vous poser de difficulté particulière, il suffit simplement d’appliquer les mêmes méthodes que nous avons vues jusqu’à présent, cela va très vite à faire ! 

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

Concernant le statut, nous avons utilisé une liste à choix unique ([`<xf:select1/>`](https://www.w3.org/TR/xforms11/#ui-selectOne)) afin de vous illustrer la syntaxe, mais selon votre envie, vous pouvez tout aussi bien choisir un champ texte classique. Dans une liste à choix unique ou multiple, chaque choix est définit par une balise `<xf:item/>`. Cette dernière possède deux sous-éléments :

- `<xf:label/>` pour l’étiquette qui s’affichera dans votre interface ;
- `<xf:value/>` pour la valeur qui sera attribuée au nœud ciblé (`roleName` en l’occurence).

En revanche, comme vous l’aurez remarqué, nous n’avons mis qu’un seul élément `<item/>` par `<list/>` dans le modèle de données. C’est tout simplement parce que, à la différence de la répartition des experts en deux colonnes qui est constante tout au long du 18e siècle, il est impossible de savoir à l’avance combien chaque colonne comptera d’experts bourgeois ou d’experts entrepreneurs. Il est donc nécessaire d’ajouter des contrôles afin d’être en mesure d’insérer (ou de supprimer) des `<item/>` directement depuis l’interface de saisie.

<!-- @quest peut être pas ici, mais il faudrait sans doute quelque part comparer ce que propose HTML et XForms. Au sens où le modèle déclaratif de XForms permet une meilleure gestion des champs de formulaires ainsi qu’une meilleure structuration -->

### Les contrôles
Les contrôles vont nous permettre d’intéragir directement avec le modèle au regard d’événements déclenchés par des actions : par exemple, lors d’un clic sur un bouton, lors de la mise à jour de la valeur d’un champ, lors de la soumission du formulaire, etc.

#### Insérer des éléments
La balise [`<insert/>`](https://www.w3.org/TR/xforms11/#action-insert) autorise l’insertion d’éléments. Ses attributs permettent de préciser quel(s) élément(s) doivent être cloné(s) et où ils doivent être placés. Cette insertion est déclenchée par un événements. Rappelez-vous, au début de cette leçon nous avons déclaré un espace de nom de manière anticipée (`xmlns:ev="http://www.w3.org/2001/xml-events"`), nous allons nous en servir maintenant.

En premier lieu, il est nécessaire de créer un bouton avec une balise [`<xf:trigger/>`](https://www.w3.org/TR/xforms11/#ui-trigger), puis d’y ajouter un sous-élément `<xf:insert/>`. Vous compléterez ce dernier avec les quatre attributs suivant :

- `@nodeset` : cet attribut évalue le groupe de nœuds à mettre à jour, ici `"tei:item"`. C’est le dernier membre de cette collection qui est cloné pour produire le nœud qui sera inséré à la position définie par les attributs `@at` et `@position` ;
- `@at` : cet attribut prend la valeur d’un index. Pour préciser « à la fin de la collection », vous devez indiquer la fonction XPath `"last()"` comme valeur ; 
- `@position` : indique l’emplacement où sera inséré le nœud au regard de l’élément ciblé par l’index (`@at`), vous pouvez inscrire `"before"` ou `"after"` respectivement pour « avant » ou « après » ;
- `@ev:event` : pour indiquer l’événement déclencheur, ici [`"DOMActivate"`](https://developer.mozilla.org/en-US/docs/Web/API/Element/DOMActivate_event) pour déclencher l’insertion lors d’un clic sur le bouton. 

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
   <xf:label>Liste des Experts dans l’almanach royal de l’année 1750</xf:label>
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

L’utilisation conjointe des éléments `<xf:repeat/>` et `<xf:insert/>` autorise la création de formulaire dynamique très facilement, toutefois, il est possible d’aller encore plus loin ! Lors de la transcription d’une liste, qui n’a jamais sauté une entrée ? Et dans une telle éventualité, comment réintégrer une ligne à sa bonne place ? Encore une fois les développeurs de XForms ont pensé à tout !

Commençons par ajouter un identifiant (`@id`) à notre élément `<xf:repeat/>` : 

```xml
<xf:repeat  id="listItem"  nodeset="tei:item">
	<!-- […] -->
</xf:repeat>
```

L’ajout de cet identifiant créer un index des « répétitions » qu’il est possible de d’interroger avec la fonction [`index()`](https://www.w3.org/TR/2003/PR-xforms-20030801/slice7.html#fn-index). Ensuite, sur l’élément ``<xf:insert/>`, il suffit simplement de modifier la valeur de l'attribut `@at="last()"` par `@at="index('listItem')"`. Dès lors, si vous modifiez une entrée de la liste et que vous cliquez sur le bouton pour ajouter une entrée, cette dernière sera insérée à la suite de l'item sur lequel est établi le focus.

{% include figure.html filename="figure4.jpg" caption="Figure 4 : la fonction index()." %}

#### Mettre à jour la valeur d’un nœud
Si le contenu du dernier item n’est pas vide, lorsque vous cliquez sur le bouton « ajouter un expert », il ne vous aura pas échappé que le contenu de cet item est également copié. C’est simplement parce que `<xf:insert/>` induit une [copie profonde](https://developer.mozilla.org/fr/docs/Glossary/Deep_copy) (*deep copy*). Il est cependant possible d’effacer le contenu des éléments nouvellement créés avec la balise [`<xf:setvalue/>`](https://www.w3.org/TR/xforms11/#action-setvalue). Trois attributs nous seront utiles :

- `@ref` : permet de cibler le nœud dont nous souhaitons mettre à jour la valeur ;
- `@value` : précise la valeur que nous souhaitons attribuer au nœud ciblé avec `@ref ` ;
- `@ev:event` : pour indiquer l’événement déclencheur.

Ici aussi, il est possible de tirer partie de la fonction `index()` vue plus haut, simplement en l’utilisant comme prédicat dans notre chemin XPath :

```xml
<xf:repeat  id="listItem"  nodeset="tei:item">
	<xf:insert nodeset="tei:item" at="index('listItem')" position="after" ev:event="DOMActivate"/>
	<xf:setvalue ref="tei:item[index('listItem')]/tei:date" value="''" ev:event="DOMActivate"/>
	<xf:setvalue ref="tei:item[index('listItem')]/tei:persName" value="''" ev:event="DOMActivate"/>
	<xf:setvalue ref="tei:item[index('listItem')]/tei:roleName" value="''" ev:event="DOMActivate"/>
	<xf:setvalue ref="tei:item[index('listItem')]/tei:address/addrLine" value="''" ev:event="DOMActivate"/>
</xf:repeat>
```

Pour les néophytes des technologies XML, vous noterez que pour indiquer une valeur nulle, on utilise l’expression `@value="''"` avec des guillemets simples placés à l’intérieur des guillemets doubles.


#### Supprimer un nœud
Avec la balise [`<xf:delete/>`](https://www.w3.org/TR/xforms11/#action-delete) vous pouvez supprimer un ou plusieurs nœuds. Comme toutes les actions, elle est déclenchée par un événement. Dans le formulaire que nous construisons, il est souhaitable d’autoriser la suppression des items dans les listes d’experts.

Commençons par créer un bouton à l’intérieur du `<xf:repeat id="listItem"  nodeset="tei:item"/>`. Vous pouvez choisir de le placer à la suite des champs, ou bien dans l’élément `<xf:label/>`, à vous de voir !  Ajoutez à ce bouton l’élément `<xf:delete/>` et conditionnez sont déclenchement au clic sur le bouton avec l’attribut `@ev:event="DOMActivate"`. Enfin, le nœud à supprimer doit être indiqué à l’aide de l’attribut `@nodeset` de valeur `"."`, qui signifie « le nœud courant ». Vous devriez obtenir quelque chose comme ceci :

```xml
<xf:label>
   Expert
   <xf:trigger>
      <xf:label>🗑</xf:label>
      <xf:delete nodeset="." ev:event="DOMActivate"/>
   </xf:trigger>
</xf:label>
```

Houston, on a un problème ! Comment empêcher de supprimer tous les items de la liste par erreur ? Avec XForms, les actions peuvent être conditionnées au respect de certaines modalités avec l’emploi de l’attribut `@if`, qui accepte une expression XPath comme valeur. Pour éviter de vider la liste, Nous pouvons par exemple compter les éléments `<item/>` et conditionner l’action au fait que ce nombre soit supérieur à 1, ce qui s’exprime de la manière suivante avec XPath : `count(parent::tei:list/tei:item) > 1`.

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
XForms propose bien évidemment des fonctionnalités avancées pour contraindre les données. Il est notamment possible de rendre obligatoire certains champs, d’ajouter des contraintes relatives à la valeur des nœuds, de confronter ces mêmes valeurs aux types XML, ou encore de calculer des valeurs automatiquement. Toutes ces contraintes doivent être déclarées dans le `<xf:model/>` à l’aide de la balise [`<xf:bind/>`](https://www.w3.org/TR/xforms11/#structure-bind-element).


<div  class="alert alert-warning">

Le standard XForms propose aussi la validation d’instances au regard de schemas XML, mais cette fonctionnalité n’est pas encore disponible avec le client XSLTForms. D’autres clients XForms ont intégré cette possibilité, à l’image de [betterForm](https://github.com/betterFORM/betterFORM), qui n’est malheureusement plus maintenu.

```xml
<xf:model schema="schema.xsd">
   <xf:instance src="instance.xml"/>
</xf:model>
```

</div> <!-- @quest balise div ? -->

#### Champ obligatoire
Avant de soumettre un formulaire à l’enregistrement, vous pourriez souhaiter que certains champs soient renseignés. C’est précisément l’objet de l’attribut `@required`, qui prend pour valeur les booléens `true()` ou `false()`. Dans le cadre de notre formulaire nous pourrions souhaiter qu’à minima le titre principale soit renseigné.

Commençons par ajouter un élément `<xf:bind/>` dans le modèle. Cet élément est attaché aux nœuds que nous souhaitons contrôler par l’intermédiaire de l’attribut `@nodeset`. Il suffit ensuite d’ajouter la propriété `@required="true()"` pour les rendre obligatoires.

```xml
<xf:model>
   <xf:instance src="instanceAlmanach.xml"/>
   <xf:bind nodeset="/tei:TEI/tei:text/tei:body/tei:div/tei:head" required="true()"/>
</xf:model>
```

En rechargeant votre formulaire, vous devriez voir une petite étoile rouge à côté du label attaché au champ, indiquant qu’une valeur est attendue.

#### Typage des données
L’attribut `@type` permet de contrôler le type de données attendu pour un nœud (*string*, *integer*, *date*, *boolean*, *card-number*, *email*, etc.). Il est possible d’appeler directement un type XML, ou d’en définir un avec une règle `xs:schema`.

Toujours dans le `<xf:model/>`, ajoutons un contrôle du type pour le nœud `<date/>`. Dans les almanachs, seule l’année est indiquée, et le type XML correspondant est [`gYear`](https://www.w3.org/TR/xmlschema-2/#gYear).

```xml
<xf:model>
   <xf:instance src="instanceAlmanach.xml"/>
   <xf:bind nodeset="/tei:TEI/tei:text/tei:body/tei:div/tei:head" required="true()"/>
   <xf:bind nodeset="/tei:TEI/tei:text/tei:body/tei:div/tei:div/tei:list/tei:item/tei:date" type="gYear"/>
</xf:model>
```

Le typage des données peut avoir des implications plus profondes sur votre formulaire. Par exemple, si vous choisissez un type `date` pour le nœud `<date/>`, vous observerez que le champ devient un sélecteur de date.

{% include figure.html filename="figure5.jpg" caption="Figure 5 : typage des données et sélecteur de date." %}

À la différence des formulaires web classiques, le selecteur de date est proposé non pas parce que le champ a été déterminé comme un champ de type `date`, c’est-à-dire uniquement fondé sur un aspect présentationnel, mais bien parce que le type de donnée attendu est une date ! En tout état de cause, il est possible de désactiver cette fonctionnalité pour n’afficher qu’un champ texte classique.

#### Contraindre les valeurs
<!-- @todo vérifier la date de fin des colonnes -->
Pour aller plus loin concernant les dates, il est établi que les deux colonnes d’experts ont existé entre 1690 et 1792, les valeurs acceptées par le champ date doivent donc être comprises entre ces deux nombres. Plus précisément, la valeur du champ doit être supérieure ou égale à 1690, mais inférieure ou égale à 1792. En l’espèce, l’expression XPath correspondante est `. >= 1690 and . <= 1792`. Avec XForms, ce contrôle est rendu possible par l’emploi de l’attribut [`@constraint`](https://www.w3.org/TR/xforms11/#model-prop-constraint) qui prend pour valeur toute expression XPath qui retourne un [booléen](https://developer.mozilla.org/fr/docs/Glossary/Boolean). Vous pouvez donc compléter la règle créée précédemment de la manière suivante : 

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

Avec XML, les signes `<` et `>` sont réservés, ils ont donc été remplacés par les [entités](https://fr.wikipedia.org/wiki/Liste_des_entit%C3%A9s_de_caract%C3%A8re_de_XML_et_HTML) correspondantes.

#### Aides à la saisie
En outre, vous pouvez également ajouter des aides à la saisie ou des alertes à l’aide des éléments [`<xf:help/>`](https://www.w3.org/TR/xforms11/#ui-commonelems-help), [`<xf:hint/>`](https://www.w3.org/TR/xforms11/#ui-commonelems-hint) et [`<xf:alert/>`](https://www.w3.org/TR/xforms11/#ui-commonelems-alert) :

- `<xf:help/>` apporte une aide à la saisie ;
- `<xf:hint/>` propose des suggestions ;
- `<xf:alert/>` retourne un message d’alerte. Toutefois, il ne bénéficie pas d’un traitement par défaut, et nécessite un implémentation au niveau de l’application XForms développée.

Ces trois éléments sont placés directement dans un champ de formulaire (`<xf:input/>`, `<xf:textarea/>`, `<xf:select/>`, etc.). Si vous disposez d’une documentation au format XML, au hasard un fichier [TEI-ODD](https://tei-c.org/guidelines/customization/getting-started-with-p5-odds/), vous pouvez faire pointer ces assistances vers votre documentation avec un attribut `@ref`, sous réserve que cette dernière soit appelée dans vos instances.

Dans ce formulaire, nous pouvons par exemepl ajouter des assistances aux champs `date` de la manière suivante :

```xml
<xf:input ref="tei:date">
   <xf:label>Date</xf:label>
   <xf:hint>Année au format 'AAAA', par exemple : 1750.</xf:hint>
   <xf:help>La valeur de ce champ doit être comprise entre 1696 et 1792.</xf:help>
</xf:input>
```

### Soumettre le formulaire
Voilà, le formulaire est pour ainsi dire terminé, il ne reste plus qu’à l’enregistrer. XForms propose un module de soumission très complet, comprenant de multiples paramètres de sérialisation et compatible avec de nombreux protocoles. Comme indiqué plus haut, les paramètres de soumission sont déclarés dans le `<xf:model/>` à l’aide de la balise [`<xf:submission/>`](https://www.w3.org/TR/xforms11/#submit).

Pour cette leçon, Nous allons voir comment récupérer un fichier contenant notre instance XML complétée.

Commencez par ajouter une balise `<xf:submission/>` dans votre `<xf:model/>`, puis précisez la [méthode](https://developer.mozilla.org/fr/docs/Web/HTTP/Methods) de soumission souhaitée avec l’attribut `@method="PUT"` et le [type MIME](https://developer.mozilla.org/fr/docs/Web/HTTP/Basics_of_HTTP/MIME_types) afin de préciser la nature et le format du document envoyé avec l’attribut `@mediatype="application/xml"`.

Il reste encore à préciser où envoyer votre document. C’est le rôle de l’attribut `@resource`. Vous pourriez choisir d’envoyer votre formulaire vers un scrip, qui s’occupera de processer les données envoyer, ou d’utiliser le scheme `file:` pour récupérer l’instance dans un fichier. 

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

Finalement, il ne reste plus qu’à créer un bouton pour initier la soumission. Pour cela, il vous suffit d’ajouter un élément `<xf:submit/>` à votre formulaire, avec un `<xf:label/>` de votre choix.

```xml
<xf:submit>
   <xf:label>Sauvegarder</xf:label>
</xf:submit>
```

Félicitations, vous avez créé un formulaire XForms reposant sur un modèle de données TEI, comportant des contrôles avancés et des aides à saisie, et capable d’enregistrer votre travail, le tout en utilisant une unique pile technologique : XML !

## Conclusion ? 
parler les aspects non abordés ? mais il y a en a beaucoup
Design ?
renvoyer vers le repo avec les fichiers sources ?

## Bibliographie ?

[Wiki XSLTForms](https://en.wikibooks.org/wiki/XSLTForms)
[Wiki XForms](https://en.wikibooks.org/wiki/XForms)
[W3C, XForms 1.1 ](https://www.w3.org/TR/xforms11/)