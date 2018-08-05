## Définitions

Avant de plonger dans la gestion d'état, ce chapitre vous fournit les définitions générales ainsi que pour la gestion d'état afin de se construire un vocabulaire commun au livre. Cela pourra vous aider pour suivre le livre sans peine, sans laisser de place à la confusion.

Before we dive into state management, this chapter gives you general definitions and definitions for state managements to build up a common vocabulary for state management for this book. It should help you to follow the book effortlessly when reading it without leaving space for confusion.

### Les fonctions pures

Les fonctions pures sont un concept issu du paradigme de programmation fonctionnelle. Cela dit qu'une fonction pure retourne toujours la même sortie si la même entrée est donnée. Il y n'a pas de strate entre, qui pourrait altérer la sortie en chemin lorsque l'entrée reste inchangée. La strate entre, qui peut potentiellement altérer la sortie, est appelé un **side-effect** (= effet de bord). Ainsi, les fonctions pures n'ont pas d'effet de bord. Deux principaux bénéfices de ces fonctions pures sont la prédictabilité et la testabilité.

### Immutabilité

L'immutabilité est aussi un concept de programmation fonctionnelle. Cela dit que la structure de données est immutable lorsqu'elle ne peut être modifiée. Lorsqu'il y a besoin de modifier la structure de données immutable, par exemple d'un objet, vous devrez toujours retourner un nouvel objet. Plutôt que d'altérer l'objet actuel à portée, vous créerez un nouvel objet basé sur l'ancien objet ainsi que la modification. L'ancien et le nouvel objet auront leurs propres instances.

Les structures de données immutables ont l'avantage d'être prédictible. Par exemple, lors du partage d'un objet au travers de toute l'application, cela peut conduire à des bugs dès lors de l'altération directe de l'objet, car toutes parties prenantes possèdent une référence à cet objet potentiellement altéré. Cela serait imprédictible dès lors qu'un objet change et que plusieurs des parties prenantes dépendent de cet objet. Au sein d'une application grandissante, c'est difficile à superviser les endroits où l'objet est actuellement utilisé par sa référence.

L'inverse d'immutabilité est appelé mutabilité. Cela dit qu'un objet ne peut être modifié.

### L'état

L'état est un mot assez vaste dans les applications modernes. Lorsque vient le fait de parler d'état d'application, cela peut être n'importe quoi qui est besoin de vivre et d'être modifié au sein du navigateur. Cela pourrait être une entité d'état qui a été retrouvé depuis le backend de l'application ou un état de la vue de l'application, par exemple, lorsqu'une popup montre des informations additionnelles.

Je ferais référence à la première forme en tant qu'**entity state** et à la seconde forme en tant que **view state**. L'**entity state** est une donnée issue du backend. Cela pourrait être une liste d'auteurs ou l'objet utilisateur décrivant l'utilisateur qui est actuellement connecté dans l'application. De l'autre côté, le **view state**, n'a pas besoin d'être stocké au sein du backend. C'est utilisé lorsque vous ouvrez une modale ou changez d'une boîte de prévisualisation à un mode édition par exemple.

Lorsque l'on parle de gestion d'état, cela signifie l'initialisation, la modification ainsi que la suppression d'état, tout ceci est compris dans le terme gestion d'état. Cependant, la gestion d'état est un sujet beaucoup plus large. Alors que les actions mentionnées sont des opérations de bas niveau, la plupart des détails d'implémentations, l'architecture, les meilleures pratiques et les patrons de conception autour de la gestion d'état restent abstrait. **La gestion d'état** aborde tous ces thèmes pour conserver votre état d'application durable.

#### La taille de l'état

L'état peut être un objet atomique ou un important objet agrégé. Lorsque l'on parle du **view state**, qui détermine seulement si une popup est ouverte ou fermé, c'est un  **atomic state object** (objet d'état atomique). Lorsque tout l'état de l'application peut être dérivé depuis un objet agrégé, qui inclut tous les objets d'état atomique, c'est appelé un **global state object** (= objet d'état global). Souvent, un **global state object** implique qu'il soit accessible depuis partout, mais ce n'est pas forcément le cas.

L'état en lui-même peut être différencié en **local state** (= état local) et **sophisticated state** (état complexe). La gestion de cet état est appelé **local state management** (= gestion d'état local) et **sophisticated state management** (= gestion d'état complexe).

#### État local

La signification d'état local est largement accepté au sein de la communauté de développement web. Un autre terme pourrait être l'**internal component state** (= état interne du composant).

L'état local est borné au composant. Il vit dans la couche vue. Il n'est pas stocké autre part. C'est pourquoi il est appelé état local car il est localisé avec le composant.

Dans React, l'état local est assuré en utilisant `this.state` et `this.setState()`. Mais il peut avoir une implémentation et un usage différent dans d'autres vues ou solutions SPAs. Le livre explique et montre l'état local au sein de React avant de plonger dans la gestion d'état complexe avec des bibliothèques externes.

#### État complexe

Je ne pourrais dire qu'il est largement accepté de l'appeler le état complexe au sein de la communauté de développement web.  Cependant, on a parfois besoin d'un terme pour le distinguer de l'état local. C'est pourquoi je le nomme souvent en tant qu'état complexe. Dans d'autres ressources, vous pourriez le trouver référencé en tant qu'**external state** (état externe), car il vit en dehors du composant local ou en dehors de la couche vue.

Le plus souvent, l'**état complexe** est externalisé vers des bibliothèques qui sont des bibliothèques ou frameworks agnostiques et ainsi agnostique de la couche vue. Mais le plus souvent ils fournissent un pont pour accéder et modifier l'état depuis la couche vue. Lors de l'utilisation seule de l'état local, dans une application grandissante, vous allouerez trop d'état au travers de vos composants au sein de la couche vue. Cependant, à un moment vous souhaiterez séparer ces problèmes. C'est là que l' **état complexe** entre en jeu.

Deux bibliothèques appelées Redux et MobX sont connues pour gérer l' **état complexe**. Ces deux bibliothèques seront expliquées, discutées et présentées dans ce livre.

#### Visibilité de l'état

Depuis que l'état local est seulement borné à l'instance de composant, seul le composant lui-même est conscient que ces propriétés constituent l'état. Cependant, le composant peut partager l'état à ses composants enfants. Dans React, les composants enfants sont inconscients que ces propriétés formes l'état de leur composant parent. Ils reçoivent seulement ces propriétés en tant que **props**.

De l'autre côté, l'**état complexe** est souvent accessible globalement. En théorie, l'état peut être accessible par chaque composant. Souvent, ce n'est pas la meilleure pratique de donner un accès à l'objet de l'état global à tous les composants, ainsi c'est à la charge du développeur de connecter à l'objet global de l'état seulement des composants sélectionnés. Tous les autres composants restent inconscients de l'état et reçoivent seulement des propriétés comme **props** qui agissent sur eux.
