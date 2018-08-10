# MobX

Les prochains chapitres du livre vous plongeront dans une bibliothèque alternative qui fournit une solution de gestion d'état : MobX. Cependant, le livre n'allouera pas la même importance que pour Redux et ne rentrera pas dans les détails. Car MobX n'est pas suffisamment orienté pour fournir une structure à votre gestion d'état, c'est difficile d'aborder tous ses angles. Il y a plusieurs solutions pour isoler et mettre à jour votre état. Le livre montre seulement quelques-unes de ces solutions, mais en aucun cas les catalogues toutes.

MobX s'annonce d'elle-même comme une bibliothèque de gestion d'état simple avec possibilité de mise à l'échelle. Elle a été créée et introduite par [Michel Weststrate](https://twitter.com/mweststrate) et demeure intensément utilisé, ainsi qu'ardemment testé, au sein de sa propre entreprise appelée Mendix. MobX est une alternative à Redux pour la gestion d'état. Elle a acquis en popularité même si seule une partie des développeurs l'utilise tant que gestion d'état alternative à Redux. Dans un prochain chapitre, vous pouvez lire les différences entre ces deux bibliothèques au sujet de la gestion d'état, car vous souhaiterez être en mesure de faire la bonne décision quant à l'utilisation de Redux ou MobX pour la mise à l'échelle de votre gestion d'état.

La bibliothèque utilise intensivement les décorateurs JavaScript qui n'est cependant pas encore très répandu et supporté par les navigateurs web. Mais ils ne sont pas obligatoires et vous pouvez éviter leur usage à l'aide de simples fonctions à la place. Vous pouvez trouver ces fonctions dans la [documentation officielle](https://mobx.js.org/). Cependant, ce livre vous présentera l'usage des décorateurs, car c'est une autre façon tout aussi intéressente d'utiliser JavaScript.

Au cours des prochains chapitres vous pouvez décider d'opter à tout moment pour le [MobX + React DevTools](https://github.com/mobxjs/mobx-react-devtools) . Vous pouvez installer le package node avec npm et suivre les instructions depuis le dépôt GitHub.

## Introduction

MobX est souvent utilisé dans les applications qui disposent d'une couche vue telle que React. Ainsi l'état, similairement à Redux, a besoin d'être connecté à la vue de telle façon que l'état peut être mis à jour et que le flux de mise à jour retourne auprès de la vue.

{title="Concept Playground",lang="text"}
~~~~~~~~
View -> MobX -> View
~~~~~~~~

Le schéma peut être élaboré pour donner plus de détail à propos de MobX et de ses composants.

{title="Concept Playground",lang="text"}
~~~~~~~~
View -> (Actions) -> State -> (Computed Values) -> Reactions -> View
~~~~~~~~

Il n'a pas nécessairement utile que cela soit la couche vue, mais lors de l'usage de MobX dans une application avec des composants, il est fort probable que la vue mutera directement l'état ou utilisera une action MobX pour muter. Cela peut être aussi simple qu'un handler `onClick` dans un composant qui déclenchera la mutation. Cependant, la mutation peut aussi être déclenchée par un effet de bord (ex les évènements programmés).

L'état dans MobX n'est pas immutable, ainsi vous pouvez muter l'état directement. Les actions dans MobX peuvent être utilisées pour muter l'état également, mais ils ne sont pas obligatoires. Vous êtes autorisé à muter l'état directement. Il n'y a pas d'obligation sur la manière de mettre à jour l'état. Vous devez imaginer vos propres conventions.

Au sein de MobX l'état devient *observable*. Ainsi, lors de changements d'état, l'application réagira au changement avec ce qui est appelé les *reactions* (= réactions). La partie de votre application qui utilise ces reactions devient réactive. Par exemple, une MobX reaction peut être aussi simple qu'une mise à jour de la vue. La couche vue dans MobX devient réactive en utilisant des *reactions*. Cela mettra à jour dès lors que l'état dans MobX change.

Entre un état  MobX observable et des MobX réactions des valeurs sont calculées. Elles ne sont pas obligatoires, similairement aux actions MobX, mais ajoutent une fine couche dans votre état. Les valeurs calculées sont des propriétés dérivées de l'état ou d'autre valeur calculées. À part pour l'état MobX en lui-même, elles sont également consommées par des réactions. Lors de l'utilisation de valeurs calculées, vous pouvez garder l'état lui-même comme une simple structure. Néanmoins vous pouvez dériver des propriétés plus complexes depuis l'état avec des valeurs calculées qui sont utilisées en réactions. Les valeurs calculées sont évaluées de façon *lazy* (= paresseuses) lorsque utilisé dans des réactions aux changements d'état. Elles ne sont pas tout le temps obligatoirement mis à jour à chaque changement d'état, mais seulement lorsqu'elles sont consommées dans une réaction qui met à jour la vue.

Ce sont en gros tous les composants de MobX. L'état peut être muté directement ou en utilisant une action MobX. Les réactions observent ces changements d'état et consomment l'état lui-même ou des valeurs calculées à partir de l'état ou encore d'autres valeurs calculées. Dans tous les cas, les actions et réactions, peuvent être simplement connectées vers une couche vue telle que React. La connexion peut se produire de façon très directe ou à l'aide d'une bibliothèque de liaison comme vous allez pouvoir en faire l'expérience dans les prochains chapitres.

### Un état observable

L'état dans MobX peut être n'importe quoi allant des primitives JavaScript aux objets complexes, tableaux ou bien seulement des références vers des classes qui encapsulent l'état. Toutes ces propriétés peuvent être édifiées au rang d'`observable` par MobX. Lorsque des changements d'état, toutes les réactions, par exemple la réaction de la couche vue, s'executera pour rerendre ladite vue. L'état au sein de MobX n'est pas administré par un seul objet global. Il est géré dans de multiples états qui sont la plupart du temps appelés des *stores* ou des *states*.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const { observable } = mobx;

class TodoStore {
  @observable todos = [];
}

const todoStore = new TodoStore();
~~~~~~~~

Gardez en mémoire qu'il n'a pas nécessité à être géré dans une instance de store qui provient d'une classe JavaScript. Cela peut être une simple liste de todos. La façon d'utiliser les stores pour gérer votre état MobX est déjà orientée. Puisqu'il a différente manière  sur l'endroit où stocker votre état dans MobX, le livre vous enseignera les façons les plus évidentes de manager cela dans des stores. In fine, les stores vous permettent de gérer un état prédictible où tout store peut conserver sa propre responsabilité pour son propre sous état.

L'état dans MobX peut être directement muté sans actions:

{title="Code Playground",lang="javascript"}
~~~~~~~~
todoStore.todos.push({ id: '0', name: 'learn redux', completed: true });
todoStore.todos.push({ id: '0', name: 'learn mobx', completed: false });
~~~~~~~~

Cela signifie, que les instances du store peuvent s'infiltrer dans la couche vue et un handler `onClick` peut muter l'état directement dans le store.

L'état et la couche vue peuvent avoir un très fort couplage au sein de MobX. En comparaison à Redux, il n'a pas besoin d'utiliser des actions explicites pour mettre à jour l'état indirectement. Vous en apprendrez davantage à propos des actions MobX dans un prochain chapitre.

### Autorun

La fonctionnalité d'autorun de MobX n'est pas souvent enseignée. C'est similaire à une méthode `subscription()` du Redux store. C'est toujours appelé lorsque l'état observable dans MobX change et une fois au démarrage lorsque l'état de MobX s'initialise. Similaire à la méthode `subscription()` du store Redux, c'est plus tard utilisé pour rendre la couche vue réactive avec MobX. La fonction d'`autorun` est seulement une façon de produire une réaction avec MobX.

Cependant, vous pouvez l'utiliser pour expérimenter les mises à jour de votre état pendant l'apprentissage de MobX. Vous pouvez simplement l'ajouter avec votre exemple du `TodoStore`.

{title="Code Playground",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const { observable, autorun } = mobx;
# leanpub-end-insert

class TodoStore {
  @observable todos = [];
}

const todoStore = new TodoStore();

# leanpub-start-insert
autorun(() => console.log(todoStore.todos.length));
# leanpub-end-insert

todoStore.todos.push({ id: '0', name: 'learn redux', completed: true });
todoStore.todos.push({ id: '0', name: 'learn mobx', completed: false });
~~~~~~~~
