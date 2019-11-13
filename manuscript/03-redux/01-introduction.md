# Redux
Redux est une des bibliothèques qui vous aide à implémenter une gestion d'état complexe au sein de vos applications. Il va au delà de l'état local. C'est l'une des solutions que vous embarquerez au sein d'une application grandissante dans le but de dompter l'état. Une application React est une correspondance parfaite pour Redux, cependant d'autres bibliothèques et frameworks ont également intensivement adoptés ces concepts.

**Pourquoi Redux est aussi populaire au sein de la communauté JavaScript?** Dans le but d'apporter une réponse à cette question, je vais devoir retourner un petit peu dans le passé des applications JavaScript. Au début, il y avait une bibliothèque qui dominait toute les autres : jQuery. C'était principalement utilisé pour manipuler le DOM, pour s'amuser avec les animations et pour implémenter des widgets réutilisables. C'était la bibliothèque JavaScript numéro une. Il n'y avait pas débat autour de cela. Cependant, l'utilisation de jQuery explosa et les applications grandissèrent en taille, mais pas en termes de taille HTML et CSS. Mais bien en taille de code JavaScript. Finalement, le code au sein de ces applications devena un bazard, car il n'y avait pas d'architecture appropriée autour de ce dernier. Le fameux code spaghetti devint un problème dans les applications JavaScripts.

C'était le moment pour de nouvelles solutions d'émerger qui irez au delà de jQuery. Ces bibliothèques, la plupart d'entre eux des frameworks, apportèrent les outils pour des architectures convenables au sein des applicatons frontend. De plus, ils apportèrent des approches orientées pour résoudre les problèmes. Ces solutions permettèrent aux développeurs d'implémenter des single page applications (SPAs).

Les single page applications devinrent populaire lorsque la première génération de frameworks et de bibliohtèques, parmis eux Angular 1, Ember et Backbone, furent sortis. Tout à coup, les developpeurs eurent des frameworks pour construire des applications frontend grandissante. Cependant, comme l'histoire se répète d'elle même, toutes nouvelles technologies apportent son lots de problèmes. Dans les SPAs toutes les solutions avaient une approche différente pour la gestion de l'état. Par exemple, Angular 1 utilisait le fameux two-way data binding. Il adopta un flux de données bidirectionnel. Seulement après un grossissement des applications, le problème de la gestion d'état devena largement répandu.

Pendant ce temps React fut sorti par Facebook. Il faisait partie de la seconde génération de solutions SPAs. Comparé à la première génération, il était seulement une bibliothèque qui amélioré seulement la couche vue. Il arrivit avec sa propre solution de la gestion d'état : la gestion local d'état.

Au sein de React, le principe de flux de données unidirectionnelle devint propulaire. La gestion d'état devait être plus prédictible dans le but de pouvoir entreprendre un véritable raisonnement avec ce dernier. Cependant, la gestion d'état local n'était pas suffisant sur certains point. Les applications React grandissé très bien, mais se heurtaient au même problème de prédiction et de maintenabilité de la gestion d'état lors de la construction d'applications plus imposantes, il y avait encore un problème de mise à l'échelle. C'était l'époque où Facebook introduisit l'architecture Flux.

L'architecture Flux est un patron de conception pour traiter la gestion d'état au sein d'applications grandissantes. Le site officiel affirme qu'*"[un] flux de données unidirectionnel est central au patron de conception Flux [...]"*. Les flux de données vont seulement dans une seule direction. Hormis le flux de données unidirectionnel, l'architecture Flux arrive avec 4 composants essentiels: Action, Dispatcher, Store et View. La View est en gros l'arbre de composants au sein d'une application moderne. Un utilisateur peut interagir avec la View dans le but de déclencher une Action. Une Action devrait encapsuler toutes les informations nécessaires pour mettre à jour l'état du/des Store(s). Le Dispatcher délègue au passage les Actions au(x) Store(s). L'état mis à jour sera propagé aux Views de nouveau afin de les mettre à jour.

Le flux de données se propage dans un seul sens. Une View peut déclencher une Action, qui traverse le Dispatcher et le Store, et pourrait finalement modifier la View après que l'état dans le Store a été modifié. Le flux de données unidirectionnel est clôturé par cette boucle. Alors de nouveau, une View peut déclencher une autre Action. Depuis que Facebook a introduit l'architecture Flux, la View était associé à React et ses composants.

Vous pouvez [en apprendre davantage sur l'architecture Flux](https://facebook.github.io/flux/)  sur le site officiel. Là vous trouverez aussi une [vidéo d'une conférence à propos de son introduction](https://youtu.be/nYkdrAPrdcw?list=PLb0IAmt7-GS188xDYE-u1ShQmFFGbrk0v) . Si vous êtes intéressé à propos de l'origine de Redux, je recommande chaudement la lecture et le visionnage de ces documents.

Après tout, Redux est devenu la bibliothèque successeuse de l'architecture Flux. Même s'il y avait plusieurs solutions autour de l'architecture Flux, Redux a réussi. Mais pourquoi a-t-il réussi?

[Dan Abramov](https://twitter.com/dan_abramov) et [Andrew Clark](https://twitter.com/acdlite) sont les créateurs de Redux. Il fut [introduit par Dan Abramov à la React Europe](https://www.youtube.com/watch?v=xsSnOQynTHs) en 2015. Cependant, le talk de Dan ne présenta pas à proprement parler de Redux. À la place, le talk présenta un problème que Dan Abramov fit face qui le conduisit à l'implémentation de Redux. Je ne veux pas déformer le contenu du talk, c'est pourquoi je vous encourage à regarder la vidéo par vous-même. Si vous êtes passionné par l'apprentissage de Redux, vous devriez vous plonger dans le problème qu'il a permis de résoudre.

Cependant, un an plus tard, de nouveau à la React Europe, Dan Abramov donna une bonne image sur le chemin parcouru par Redux et ses réussites. Il mentionna quelques points qui ont fait le succès de Redux d'après lui.

Redux a été développé pour résoudre un problème. Le problème a été exliqué par Dan Abramov un an plus tôt lorsqu'il introduisit Redux. C'était une bibliothèque qui résolut un unique problème. Le stress test de Redux fut le **Time Traveling** et l'**Hot Reloading** .

Les contraintes de Redux furent un autre facteur clé vers son succès. Redux s'occupe de repousser le problème à l'aide d'une API simple et d'une façon bien réfléchie pour résoudre le problème de la gestion d'état lui-même.

[Vous pouvez également regarder le talk](https://www.youtube.com/watch?v=uvAXVMwHJXU). Je le recommande vivement. Vous pouvez le regarder maintenant ou après le prochain chapitre qui vous présente les bases de Redux.