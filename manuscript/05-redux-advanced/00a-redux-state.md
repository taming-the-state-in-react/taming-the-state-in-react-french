# Structure de l'état de Redux et récupération

Dans les précédents chapitres, vous avez étudié Redux en standalone et Redux au sein de React. Vous devrez d'ors et déjà être capable de construire des applications de petite et moyenne taille avec cela. Avant que vous plongez plus profondément dans Redux, je vous recommande d'expérimenter vos récents acquis et de les appliquer dans vos applications. Si vous sautez directement sur les prochains chapitres, vous pourrez avoir le sentiment que Redux est dans l'excès (ce qui est pour la plupart des applications le cas). Cependant, les prochains chapitres devraient vous procurer des aides avancées quant à la mise à l'échelle de votre gestion d'état dans de plus imposante application. Il y a quelques techniques que vous pourrez alors appliquer.

Le prochain chapitre vous guide au travers de sujets plus avancés de Redux dans le but de gérer votre état. Vous serez amené à connaitre le *middleware* de Redux, vous en apprendrez plus au sujet d'une structure d'état normalisée et immutable, ainsi que sur comment retrouver un sous-état de façon simplifié à l'aide de *selectors* depuis l'état global.

## Middleware au sein de Redux

Dans Redux, vous pouvez utiliser un middleware. Toute action dispatchée dans Redux traversera ce middleware. Vous pouvez implémenter une fonctionnalité spécifique entre le fait de dispatcher une action et le moment ou ce dernier atteint le reducer.

Il y a des bibliothèques utiles pour ajouter des fonctionnalités au sein de votre middleware Redux. Juste après, vous serez amené à connaitre l'une d'entre elles:  [redux-logger](https://github.com/evgenyrodionov/redux-logger). Lorsque vous l'utilisez, elle ne change fondamentalement rien dans votre application. Mais elle vous facilitera la vie  en tant que développeur lorsque vient le moment de traiter Redux. Que fait-elle? Elle logs simplement les actions dans votre console développeur du navigateur avec un `console.log()`. En tant que développeur, elle vous fournit de la clarté sur quelle action est dispatchée et comment le précédent état ainsi que le nouveau sont structurés.

Mais où appliquer ce middleware dans Redux? C'est le Redux store qui peut être initialisé avec ce dernier. La fonctionnalité `createStore()`  de Redux prend en troisième argument un *enhancer*. La bibliothèque Redux débarque avec l'un de ces *enhancers*: `applyMiddleware()`.

{title="Code Playground",lang="javascript"}
~~~~~~~~
import { applyMiddleware, createStore } from 'redux';

const store = createStore(
  reducer,
  undefined,
  applyMiddleware(...)
);
~~~~~~~~

Si vous ne disposez pas d'un état initial pour votre état Redux, vous pouvez utiliser `undefined` à la place. Maintenant, lors de l'utilisation du redux-logger, vous pouvez passer une instance `logger` auprès de la fonction `applyMiddleware()`.

{title="Code Playground",lang="javascript"}
~~~~~~~~
import { applyMiddleware, createStore } from 'redux';
# leanpub-start-insert
import { createLogger } from 'redux-logger';

const logger = createLogger();
# leanpub-end-insert

const store = createStore(
  reducer,
  undefined,
# leanpub-start-insert
  applyMiddleware(logger)
# leanpub-end-insert
);
~~~~~~~~

Et c'est tout! Maintenant toutes actions devraient être visible dans votre console développeur du navigateur dès lors qu'elles sont dispatchées. Et ainsi vos changements d'état deviennent plus prédictibles lors de la phase de développement sans même avoir le besoin de logger toutes les actions par vous-même.

La fonctionnalité `applyMiddleware()` prend n'importe quel nombre de middleware: `applyMiddleware(firstMiddleware, secondMiddleware, ...);`. L'action traversera tous les middlewares avant d'atteindre les reducers. Parfois, vous devez vous assurer de les appliquer dans le bon ordre. Par exemple, le middleware `redux-logger` doit obligatoirement être le dernier dans la chaine de middleware dans le but de sortir les actions et états corrects.

Cependant, c'est seulement le cas du middleware redux-logger. Au cours de l'implémentation d'applications Redux, vous trouverez surement des fonctionnalités utiles qui peuvent être appliquées via le Redux middleware. La plupart de ces fonctionnalités sont déjà implémentées dans des bibliothèques que vous trouverez publié sur npm. Par exemple, les actions asynchrones dans Redux sont possibles en utilisant le Redux middleware. Ces actions asynchrones seront expliquées plus tard dans ce livre.

## L'état immutable

Redux adopte un état immutable. Vos reducers retourneront toujours un nouvel objet état. Vous ne muterez jamais l'état entrant. Par conséquent, vous devriez vous habituer à différentes fonctionnalités JavaScript et syntaxe pour adopter des structures de données immutables.

Jusqu'ici, vous avez utilisé les fonctionnalités par défaut de JavaScript afin de conserver vos structures de données immutable. Tel que `array.map()` et `array.concat(item)` pour vos tableaux ou  `Object.assign()` pour vos objets. Toutes ces fonctionnalités retournent de nouvelles instances de tableaux ou d'objets sans en altérer les anciens tableaux et objets. Souvent, vous devez lire la documentation officielle JavaScript pour vous assurer qu'il retourne une nouvelle instance dudit tableau ou objet. Autrement, vous violerez les contraintes de Redux car vous serez amené à muter l'instance précédente qui ici est souvent l'état ou l'action.

Mais ce n'est pas tout. Vous devez en connaitre un peu plus sur les outils permettant de conserver les structures de données immutables en JavaScript. Il y a quelques bibliothèques tierses qui peuvent vous assister à conserver l'immutable.

* [immutable.js](https://github.com/facebook/immutable-js)
* [immer.js](https://github.com/mweststrate/immer)
* [mori.js](https://github.com/swannodette/mori)
* [seamless-immutable.js](https://github.com/rtfeldman/seamless-immutable)
* [baobab.js](https://github.com/Yomguithereal/baobab)

Mais la plupart d'entre elles embarquent trois défauts. Premièrement, ils ajoutent une autre couche de complexité à votre application. Deuxièmement, vous devez encore apprendre une nouvelle bibliothèque. Et troisièmement, vous devez déshydrater et réhydrater vos données, car la plupart de ces bibliothèques entourent vos simple objets et tableaux JavaScript dans un objet ou tableau de données immutables spécifiques à la bibliothèque. C'est un tableau/objet immutable dans votre Redux store, mais encore une fois vous souhaitez pour l'utiliser dans React le transformer en simple tableau/objet JavaScript. Personnellement je vous recommanderais d'utiliser de telles bibliothèques seulement dans deux cas :

* Vous n'êtes pas à votre aise pour conserver des structures de données immutables en JavaScript ES5, JavaScript ES6 et plus.
* Vous voulez améliorer la performance des structures de données immutables lors de l'utilisation d'imposantes quantités de données

Si ces deux déclarations sont fausses, je vous recommande chaudement de rester en simple JavaScript. Comme vous avez pu le voir, les fonctionnalités par défaut de JavaScript sont déjà d'une grande aide pour conserver les structures de données immutables. En JavaScript ES6 et au-delà, vous avez une fonctionnalité additionnelle pour conserver vos structures de données immutables : [Opérateurs de décomposition](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Spread_operator). Décomposer un objet ou un tableau en un nouvel objet ou tableau vous donne toujours en résultat un nouvel objet ou un nouveau tableau.

Vous rappelez vous comment vous aviez ajouté un nouvel élément todo ou comment vous avez complété un élément todo dans vos reducers?

{title="Code Playground",lang="javascript"}
~~~~~~~~
// adding todo
const todo = Object.assign({}, action.todo, { completed: false });
const newTodos = todos.concat(todo);

// toggling todo
const newTodos = todos.map(todo =>
  todo.id === action.todo.id
    ? Object.assign({}, todo, { completed: !todo.completed })
    : todo
  );
~~~~~~~~

Ajoutez un peu plus de JavaScript ES6 en utilisant l'opérateur de décomposition, ainsi vous pouvez faire ceci de façon plus concise.

{title="Code Playground",lang="javascript"}
~~~~~~~~
// adding todo
const todo = { ...action.todo, completed: false };
const newTodos = [ ...todos, todo ];

// toggling todo
const newTodos = todos.map(todo =>
  todo.id === action.todo.id
    ? { ...todo, completed: !todo.completed }
    : todo
  );
~~~~~~~~

JavaScript vous fournit assez d'outils pour conserver vos structures de données immutables. Il n'y a pas besoin d'utiliser une bibliothèque tierce hormis pour les deux cas d'utilisation mentionnés précédemment. Cependant, il pourrait y avoir un troisième cas d'utilisation où de telle bibliothèque aurait une utilité: en Redux les structures de données profondément imbriquées ont besoin de rester immutables. Il est vrai que cela devient plus difficile à conserver des structures de données immutables lorsqu'elles sont imbriquées. Mais, comme mentionnée précédemment dans le livre, c'est une mauvaise pratique que de posséder des structures de données profondément imbriquées en Redux. C'est ici que le prochain chapitre du livre entre en jeu car il peut être utilisé pour conserver vos structures de données applaties au sein du Redux store.

## Un état normalisé

Une bonne pratique en Redux consiste à avoir une structure d'état aplati. Vous ne voulez pas maintenir une structure immutable pour votre état lorsque ce dernier est profondément imbriqué. Cela devient fastidieux et illisible même à l'aide d'opérateurs de décompositions. Mais souvent vous n'avez pas le contrôle sur votre structure de données, car elle provient d'une application d'une API backend. Lorsque l'on possède une structure de données profondémment imbriquées, vous avez deux options :

* L'enregistrer dans le store comme elle est et retarder le problème (à éviter)
* L'enregistrer en tant que structure de données normalisée au sein du store (à privilégier)

Vous devez tendre vers la seconde option. Vous traitez le problème une seule fois et toutes les parties sous-jacentes de votre application en seront reconnaissants. Avançons au travers d'un scénario pour illustrer la normalisation de l'état. Imaginez-vous avez la structure de données imbriquée suivante :

{title="Code Playground",lang="javascript"}
~~~~~~~~
const todos = [
  {
    id: '0',
    name: 'create redux',
    completed: true,
    assignedTo: {
      id: '99',
      name: 'Dan Abramov',
    },
  },
  {
    id: '1',
    name: 'create mobx',
    completed: true,
    assignedTo: {
      id: '77',
      name: 'Michel Weststrate',
    },
  }
];
~~~~~~~~

Deux créateurs de bibliothèques, Dan Abramov et Michel Weststrate, on fait un excellent travail : Ils ont créé des bibliothèques populaires pour la gestion de l'état. La première option, comme mentionnée, consisterait à enregistrer les todos tels qu'ils sont dans le store. Les todos eux-même pourraient posséder les informations imbriquées de l'objet `assignedTo` à l'intérieur de l'objet `todo`. Maintenant, si une action venait à corriger un utilisateur assigné, le reducer devrait traiter avec la structure de données imbriqué. Ajoutons Andrew Clark en tant que créateur de Redux.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const ASSIGNED_TO_CHANGE = 'ASSIGNED_TO_CHANGE';

store.dispatch({
  type: ASSIGNED_TO_CHANGE,
  payload: {
    todoId: '0',
    name: 'Dan Abramov and Andrew Clark',
  },
});

function todoReducer(state = [], action) {
  switch(action.type) {
    case ASSIGNED_TO_CHANGE : {
      return applyChangeAssignedTo(state, action);
    }

    ...

    default : return state;
  }
}

function applyChangeAssignedTo(state, action) {
  return state.map(todo => {
    if (todo.id === action.payload.todoId) {
      const assignedTo = { ...todo.assignedTo, name: action.payload.name };
      return { ...todo, assignedTo };
    } else {
      return todo;
    }
  });
}
~~~~~~~~

Cela vous conduira à la liste d'élément todos suivante au sein de votre Redux store après que l'action a été dispatchée :

{title="Code Playground",lang="javascript"}
~~~~~~~~
const todos = [
  {
    id: '0',
    name: 'create redux',
    completed: true,
    assignedTo: {
      id: '99',
      name: 'Dan Abramov and Andrew Clark',
    },
  },
  {
    id: '1',
    name: 'create mobx',
    completed: true,
    assignedTo: {
      id: '77',
      name: 'Michel Weststrate',
    },
  }
];
~~~~~~~~

Comme vous pouvez le voir depuis le reducer, le plus profond vous devez entrer dans la structure de données imbriquées immutables, le plus vous devait prêter attention à conserver votre structure de données immutable. Chaque niveau de données imbriqué ajoute un travail fastidieux de maintien. Par conséquent, vous pouvez utiliser une bibliothèque appelée [normalizr](https://github.com/paularmstrong/normalizr) pour aplatir (normaliser) votre état. La bibliothèque utilise des définitions de schéma pour transformer profondément les structures de données en dictionnaires qui ont des entités et une liste d'ids correspondant.

À quoi cela peut-il bien ressembler? Prenons la précédente liste d'éléments todo comme exemple. Tout d'abord, vous aurez à définir les schémas pour vos entités seulement une fois :

{title="Code Playground",lang="javascript"}
~~~~~~~~
import { schema } from 'normalizr';

const assignedToSchema = new schema.Entity('assignedTo');

const todoSchema = new schema.Entity('todo', {
  assignedTo: assignedToSchema,
});
~~~~~~~~

Deuxièmement, vous pouvez normaliser vos données quand vous le souhaitez :

{title="Code Playground",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
import { schema, normalize } from 'normalizr';
# leanpub-end-insert

const assignedToSchema = new schema.Entity('assignedTo');

const todoSchema = new schema.Entity('todo', {
  assignedTo: assignedToSchema,
});

# leanpub-start-insert
const normalizedData = normalize(todos, [ todoSchema ]);
# leanpub-end-insert
~~~~~~~~

La sortie sera la suivante :
The output would be the following:

{title="Code Playground",lang="javascript"}
~~~~~~~~
{
  entities: {
    assignedTo: {
      77: {
        id: "77",
        name: "Michel Weststrate"
      },
      99: {
        id: "99",
        name: "Dan Abramov"
      }
    },
    todo: {
      0: {
        assignedTo: "99",
        completed: true,
        id: "0",
        name: "create redux"
      },
      1: {
        assignedTo: "77",
        completed: true,
        id: "1",
        name: "create mobx"
      }
    }
  },
  result: ["0", "1"]
}
~~~~~~~~

Les données imbriquées sont devenues une structure de données aplaties regroupée par entitées. Chaque entité peut référencer une autre entité par son id. C'est comme vouloir conserver ces entités dans une base de données. Elles sont découplées maintenant. Après quoi, dans votre application Redux, vous pouvez avoir un reducer qui stocke et traite les entités `assignedTo` et un autre reducer qui traite les entitées `todo`. La structure de données est aplatie et regroupée par entités devenant par là même plus facile d'accès et de maintien.

Il y a un autre bénéfice dans la normalisation de données. Lorsque vos données ne sont pas normalisées, les entités sont souvent dupliquées dans votre stuctures de données imbriquée. Imaginez l'objet suivant avec des todos.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const todos = [
  {
    id: '0',
    name: 'write a book',
    completed: true,
    assignedTo: {
      id: '55',
      name: 'Robin Wieruch',
    },
  },
  {
    id: '1',
    name: 'call it taming the state in react',
    completed: true,
    assignedTo: {
      id: '55',
      name: 'Robin Wieruch',
    },
  }
];
~~~~~~~~

Si vous stockez de telles données dénormalisées au sein de votre redux store, vous tomberez probablement sur un problème majeur : Imaginez vous souhaitiez mettre à jour la propriété `name` de valeur `Robin Wieruch` de toutes les propriétés `assignedTo`. Vous devrez traverser tous les todos dans le but de mettre à jour l'ensemble des propriétés `assignedTo` avec l'id `55`. Le problème : Il n'y a pas une seule source de vérité. Vous oublierez probablement de mettre à jour une entité et vous vous retrouverez avec un état finalement périmé. Par conséquent, la meilleure pratique est de stocker votre état normalisé ainsi chaque entité représente une seule et unique source de vérité. Il n'y aura pas de duplication d'entités et ainsi pas d'état périmé au moment de mise à jour de l'unique et seul source de vérité, car chaque todo fera référence à l'entité `assignedTo` mise à jour.

{title="Code Playground",lang="javascript"}
~~~~~~~~
{
  entities: {
    assignedTo: {
      55: {
        id: "55",
        name: "Robin Wieruch"
      }
    },
    todo: {
      0: {
        assignedTo: "55",
        completed: true,
        id: "0",
        name: "write a book"
      },
      1: {
        assignedTo: "55",
        completed: true,
        id: "1",
        name: "call it taming the state in react"
      }
    }
  },
  result: ["0", "1"]
}
~~~~~~~~

En conclusion, la normalisation de votre état a deux bénéfices. Il conserve votre état aplati devenant ainsi plus facilement géreable avec des structures de données immutables. De plus, il regroupe les entités en des sources uniques de vérité sans aucune duplication. Lorsque vous normalisez votre état, vous serez amené à automatiquement regrouper par entités qui pourront être géré par leurs propres reducers. Cependant, vous ne souhaitez pas dès le début commencé par la normalisation de votre état. Premièrement, vous devez essayer d'introduire Redux lui-même au sein de votre application. Une fois que vous expérimenteriez les difficultés lors de l'altération de votre état, car tout est imbriqué, ou que vous expérimentez des problèmes lors de la mise à jour de simples entités, car il n'y a pas une seule source de vérité, vous voudrez introduire une normalisation d'état au sein de votre application redux.

Avant que vous appliquez la normalisation en tant qu'exercice dans votre propre application Todo, sachez qu'il existe encore un autre avantage lors de la normalisation de votre état avec une bibliothèque telle que normalizr. C'est à propos de la dénormalisation : comment faire en sorte que les composants s'y retrouvent avec un état normalisé? Vous apprendrez cela dans le prochain chapitre.

## Les sélecteurs

Dans Redux, il y a le concept de *selectors* (= sélecteur) pour retrouver des propriétés dérivées de votre état. Un sélecteur est une fonction qui prend l'état en argument et retourne un sous-état ou des propriétés dérivées de ce dernier. Ils peuvent simplement retourner un sous-état de votre état global ou bien ils peuvent dors et déjà préporcesser votre état pour retourner des propriétés dérivées. La fonction peut prendre des arguments optionnels pour soutenir le processus de sélection.

### Les sélecteurs pures

Les sélecteurs suivent habituellement une seule syntaxe. L'argument obligatoire d'un sélecteur est l'état à partir duquel il doit faire la sélection. Il peut y avoir des arguments optionnels qui sont dans un rôle d'assistant pour sélectionner le sous-état ou les propriétés dérivées.

{title="Code Playground",lang="javascript"}
~~~~~~~~
// sélecteur
(state) => derived properties
// sélecteur avec arguments optionnels
(state, args) => derived properties
~~~~~~~~

Les sélecteurs ne sont pas obligatoires. Quand on médite sur toutes les parties de Redux, seules les actions, les reducers et le Redux store sont des prérequis. Similairement aux créateurs d'action, les sélecteurs peuvent être utilisés pour améliorer l'expérience développeur dans une architecture Redux, mais vous n'avez pas besoin de les utiliser. À quoi ressemble un sélecteur? C'est une simple fonction, comme mentionnée, qui peut vivre n'importe ou dans votre application. Cependant, lors de l'utilisation de Redux dans React, vous l'utiliserez, peut-être l'importerez, dans votre fonction `mapStateToProps()`. Donc au lieu de retrouver l'état explicitement :


{title="Code Playground",lang="javascript"}
~~~~~~~~
function mapStateToProps(state) {
  return {
    todos: state.todoState,
  };
}
~~~~~~~~

Vous les retrouverez implicitement via un sélecteur :

{title="Code Playground",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
function getTodos(state) {
  return state.todoState;
}
# leanpub-end-insert

function mapStateToProps(state) {
  return {
# leanpub-start-insert
    todos: getTodos(state),
# leanpub-end-insert
  };
}
~~~~~~~~

C'est similaire au concept d'action et reducer. Au lieu de manipuler l'état directement dans le Redux store, vous utiliserez les actions et reducers pour l'altérer indirectement. La même chose est appliquée pour les sélecteurs qui ne récupère pas les propriétés dérivées directement mais indirectement depuis l'état global.

Vous pourrez vous demander : Pourquoi les sélecteurs sont un avantage? Il y a plusieurs bénéfices. Un sélecteur peut être réutilisé. Vous pourrez rencontrer des cas où vous sélectionnez plus souvent à partir de l'état global des propriétés dérivées ou bien le sous-état. Par exemple, vous pourriez avoir plusieurs endroits de votre application où vous voudrez montrer seulement les éléments todo complétés. Et c'est toujours un bon signe que d'utiliser une fonction en premier lieu. De plus, les sélecteurs peuvent être testés séparément. Ce sont des fonctions pures et donc une partie facilement testable dans votre application ainsi que dans l'ensemble de l'architecture Redux. Enfin mais pas des moindres, les propriétés dérivées depuis l'état peuvent devenir une entreprise complexe dans une application sans cesse grandissante. Comme mentionné, un sélecteur peut avoir des arguments optionnels pour dériver des propriétés plus compliquées à partir de votre état. La fonction `selector` en elle-même deviendrait plus complexe, mais elle pourrait être encapsulée dans une autre fonction à la place, dans plusieurs fonctions `mapStateToProps()`, pour le cas de React et Redux.

### Dénormaliser l'état dans les sélecteurs

Dans le chapitre précédent portant sur la normalisation, il y avait un bénéfice inexpliqué. C'est sur le fait de sélectionner un état normalisé depuis votre couche d'état pour la passer à votre couche vue. Personnellement, Je défendrais le fait qu'une structure de données normalisée rend la sélection d'un sous-état plus commode. Lorsque vous vous remémorez la structure d'état normalisé, cela ressemble à quelque chose comme cela :

{title="Code Playground",lang="javascript"}
~~~~~~~~
{
  entities: ...
  ids: ...
}
~~~~~~~~

Par exemple, dans une réelle application cela pourrait ressembler comme suit :

{title="Code Playground",lang="javascript"}
~~~~~~~~
// state
[
  { id: '0', name: 'learn redux' },
  { id: '1', name: 'learn mobx' },
]

// normalized state
{
  entities: {
    0: {
      id: '0',
      name: 'learn redux',
    },
    1: {
      id: '1',
      name: 'learn mobx',
    },
  },
  ids: ['0', '1'],
}
~~~~~~~~

Si vous vous remémorez de Redux dans le chapitre React, ici vous transmettez la liste des todos depuis le composant `TodoList`, car c'est un composant connecté, jusqu'en bas de l'arbre de composant. Comment résoudre cela avec l'état normalisé de dessus?

Supposons que le reducer stocke l'état dans une structure de données immutable, vous passerez uniquement la liste de todo `ids` à votre composant `TodoList`. Car ce composant gère la liste et non les entités en elle-même, cela fait sens qu'il ne dispose uniquement de la liste avec des références vers les entités.

{title="Code Playground",lang="javascript"}
~~~~~~~~
function TodoList({ todosAsIds }) {
  return (
    <div>
      {todosAsIds.map(todoId => <ConnectedTodoItem
        key={todoId}
        todoId={todoId}
      />)}
    </div>
  );
}

function getTodosAsIds(state) {
  return state.todoState.ids;
}

function mapStateToProps(state) {
  return {
    todosAsIds: getTodosAsIds(state),
  };
}

const ConnectedTodoList = connect(mapStateToProps)(TodoList);
~~~~~~~~

Maintenant le composant `ConnectedTodoItem`, qui passe déjà l'handler `onToggleTodo()` via la fonction `mapDispatchToProps()` à son simple composant `TodoItem`, pourrait sélectionner l'entité todo correspondante à la propriété `todoId` entrante.

{title="Code Playground",lang="javascript"}
~~~~~~~~
function getTodoAsEntity(state, id) {
  return state.todoState.entities[id];
}

function mapStateToProps(state, props) {
  return {
    todo: getTodoAsEntity(state, props.todoId),
  };
}

function mapDispatchToProps(dispatch) {
  return {
     onToggleTodo: id => dispatch(doToggleTodo(id)),
  };
}

const ConnectedTodoItem = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoItem);
~~~~~~~~

Le composant `TodoItem` lui-même restera inchangé. Il aura encore l'élément `todo` et l'handler `onToggleTodo()` en tant qu'argument. Qui plus est, vous pouvez remarquer deux concepts supplémentaires qui ont été expliqué plus tôt. Premièrement, le sélecteur gagne en complexité car il dispose d'arguments optionnels pour sélectionner des propriétés dérivées depuis l'état. Deuxièmement, la fonction `mapStateToProps()` fait l'usage des *props* entrantes du composant `TodoList` qui utilise le composant `ConnectedTodoItem`.

Comme vous pouvez le voir, l'état normalisé requiert d'utiliser plus de composants connectés. Plus de composants sont responsables de sélectionner leurs propriétés dérivées requises. Mais dans une application grandissante, le modèle suivant peut rendre le raisonnement beaucoup plus facile. Vous transmettez seulement des propriétés qui sont véritablement nécessaires au composant. Dans le dernier cas, le composant `TodoList` est seulement concerné par une liste de références, tandis que le composant `TodoItem` est seulement concerné par l'entité qui est selectionnée en utilisant la référence transmise par le composant `TodoList`.

Il existe une autre manière de dénormaliser votre état normalisé lors de l'utilisation d'une bibliothèque telle que normalizr. Le précédent scénario vous permettez de passer le minimum de propriété aux composants. Chaque composant était responsable de sélectionner son état. Dans le prochain cas, vous dénormaliserez votre état dans un seul composant ainsi les autres composants n'ont pas besoin de s'en préoccuper. Vous utiliserez le même schéma, que lors de la normalisation initiale, mais cette fois-ci pour inverser la normalisation.

{title="Code Playground",lang="javascript"}
~~~~~~~~
import { denormalize, schema } from 'normalizr';

const todoSchema = new schema.Entity('todos');
const todosSchema = { todos: [ todoSchema ] };

function TodoList({ todos }) {
  return (
    <div>
      {todos.map(todo => <ConnectedTodoItem
        key={todo.id}
        todo={todo}
      />)}
    </div>
  );
}

function getTodos(state) {
  const entities = state.todoState.entities;
  const ids = state.todoState.ids;
  return denormalize(ids, [ todoSchema ], entities);
}

function mapStateToProps(state) {
  return {
    todos: getTodos(state),
  };
}

const ConnectedTodoList = connect(mapStateToProps)(TodoList);
~~~~~~~~

Dans ce scénario, l'entièreté de la structure de données normalisée a été dénormalisé au sein du sélecteur. Vous avez toute la liste de todos dans votre composant `TodoList`. Le composant `TodoItem` n'aura pas besoin de se préoccuper de la dénormalisation.

Comme vous pouvez le voir, il y a essentiellement deux façons de traiter un état normalisé : dans vos sélecteurs ou en général dans les fonctions `mapStateToProps()`. C'est à vous de trouver l'implémentation qui convient le mieux à votre propre cas d'utilisation. Peut-être, qu'en premier lieu vous n'avez même pas besoin de normaliser votre état, car il est déjà aplati ou qu'il n'est pas très profondément imbriqué.

### Reselect

Lors de l'utilisation de sélecteurs dans une application grandissante, vous devez prendre en considération une bibliothèque appelée [reselect](https://github.com/reactjs/reselect) qui vous fournit des sélecteurs avancés. En gros, elle utilise le même concept de simples sélecteurs comme vous l'avez appris avant, mais débarque avec deux améliorations.

Un simple sélecteur a une contrainte :

* *"les sélecteurs peuvent calculer des données dérivées, permettant à Redux de stocker l'état minimal."*

Il y a deux contraintes additionnelles lors de l'utilisation de sélecteurs au travers de la bibliothèque reselect :

* *"Les sélecteurs sont efficaces. Un sélecteur n'est pas recalculé à moins que l'un de ses arguments change."*
* *"Les sélecteurs sont composables. Ils peuvent être utilisés en tant qu'entrée pour d'autres sélecteurs."*

Les sélecteurs sont des fonctions pures sans aucun effet de bord. La sortie ne change pas lorsque l'entrée reste la même. Par conséquent, lorsqu'une fonction est appelée deux fois et ses arguments restent inchangés, elle retourne la même sortie. Ce postulat est utilisé dans les sélecteurs de reselect. Ce principe est appelé en programmation la mémoïzation. Un sélecteur n'a pas besoin de tout recalculer dès lors que ses entrées ne changent pas. Il retournera simplement la même sortie, car c'est une fonction pure. Avec la memoïzation il se souvient des précédentes entrées et si elles demeurent inchangées il retourne la précédente sortie. Dans une application grandissante cela peut avoir un impact de performance.

Un autre bénéfice, lors de l'utilisation de reselect, c'est la possibilité de composer les sélecteurs. Il supporte le fait d'implémenter des sélecteurs réutilisables qui résolvent seulement un problème. Ensuite, ils peuvent être composés avec une approche de programmation fonctionnelle.

Le livre ne plongera pas plus profondément dans la bibliothèque reselect. Lors de l'apprentissage de Redux il est bon d'avoir connaissance de ces sélecteurs avancés, mais vous pouvez tout aussi bien utiliser les simples sélecteurs pour débuter. Si vous ne pouvez attendre, vous pouvez lire les exemples d'utilisation dans le [dépôt officiel GitHub](https://github.com/reactjs/reselect) et l'appliquer dans vos projets tout en lisant le livre.

## Cas pratique: Todo avec Redux avancé

Maintenant, dans l'application Todo, vous pouvez tout refactorer pour utiliser les techniques plus avancées que vous avez apprises dans les chapitres précédents : un middleware, une structure de données immutable utilisant les opérateurs de décomposition de JavaScript, une structure d'état normalisée et des sélecteurs. Continuons avec l'application Todo que vous avez construite lorsque vous avez connecté React et Redux. La dernière version peut être trouvée sur ce [dépôt GitHub](https://github.com/rwieruch/taming-the-state-todo-app/tree/3.0.0).

En premier lieu, utilisons le middleware [redux-logger](https://github.com/evgenyrodionov/redux-logger). Ainsi, vous devez l'installer à l'aide de la ligne de commande :

{title="Command Line: /",lang="text"}
~~~~~~~~
npm install --save redux-logger
~~~~~~~~

Par la suite vous pouvez l'utiliser lorsque vous créez votre store :

{title="src/index.js",lang="javascript"}
~~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';
# leanpub-start-insert
import { applyMiddleware, combineReducers, createStore } from 'redux';
# leanpub-end-insert
import { Provider, connect } from 'react-redux';
# leanpub-start-insert
import { createLogger } from 'redux-logger';
# leanpub-end-insert
import './index.css';

...

// store

const rootReducer = combineReducers({
  todoState: todoReducer,
  filterState: filterReducer,
});

# leanpub-start-insert
const logger = createLogger();

const store = createStore(
  rootReducer,
  undefined,
  applyMiddleware(logger)
);
# leanpub-end-insert
~~~~~~~~

Maintenant, lorsque que vous démarrez l'application Todo, vous devez voir la sortie du `logger` dans la console développeur de votre navigateur dès lors que vous dispatcher des actions. L'application Todo avec le middleware utilisant redux-logger peut-être trouvé sur ce [dépôt GitHub](https://github.com/rwieruch/taming-the-state-todo-app/tree/4.0.0).

La seconde partie de ce chapitre utilise les opérateurs de décomposition JavaScript à la place de la fonction `Object.assign()` pour conserver la structure de données immutable. Vous pouvez l'appliquer dans vos fonctions reducer :

{title="src/index.js",lang="javascript"}
~~~~~~~~
function applyAddTodo(state, action) {
# leanpub-start-insert
  const todo = { ...action.todo, completed: false };
  return [ ...state, todo ];
# leanpub-end-insert
}

function applyToggleTodo(state, action) {
  return state.map(todo =>
    todo.id === action.todo.id
# leanpub-start-insert
      ? { ...todo, completed: !todo.completed }
# leanpub-end-insert
      : todo
  );
}
~~~~~~~~

L'application doit fonctionner pareillement, mais cette fois avec l'opérateur de décomposition qui conserve la structure de données immutable et ainsi un objet d'état immutable. Le code source peut être trouvé de nouveau dans ce [dépôt GitHub](https://github.com/rwieruch/taming-the-state-todo-app/tree/5.0.0).

pour la troisième partie de la mise en place des techniques avancées issues des chapitres précédents, vous utiliserez une structure d'état normalisé. Par conséquent vous pouvez installer en ligne de commande la superbe bibliothèque [normalizr](https://github.com/paularmstrong/normalizr) :

{title="Command Line: /",lang="text"}
~~~~~~~~
npm install --save normalizr
~~~~~~~~

Regardons un peu l'état initial pour le `todoReducer`. Vous pouvez faire un état initial pour ce dernier. Par exemple, pourquoi ne pas compléter tous les exemples de code de ce livre en ayant des éléments todo pour cela?

{title="src/index.js",lang="javascript"}
~~~~~~~~
const todos = [
  { id: '1', name: 'Redux Standalone with advanced Actions' },
  { id: '2', name: 'Redux Standalone with advanced Reducers' },
  { id: '3', name: 'Bootstrap App with Redux' },
  { id: '4', name: 'Naive Todo with React and Redux' },
  { id: '5', name: 'Sophisticated Todo with React and Redux' },
  { id: '6', name: 'Connecting State Everywhere' },
  { id: '7', name: 'Todo with advanced Redux' },
  { id: '8', name: 'Todo but more Features' },
  { id: '9', name: 'Todo with Notifications' },
  { id: '10', name: 'Hacker News with Redux' },
];

function todoReducer(state = todos, action) {
  ...
}
~~~~~~~~

Vous pouvez utiliser normalizr pour normaliser cette structure de données. Premièrement, vous devez definir un schéma :

{title="src/index.js",lang="javascript"}
~~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import { applyMiddleware, combineReducers, createStore } from 'redux';
import { Provider, connect } from 'react-redux';
import { createLogger } from 'redux-logger';
# leanpub-start-insert
import { schema, normalize } from 'normalizr';
# leanpub-end-insert
import './index.css';

# leanpub-start-insert
// schemas

const todoSchema = new schema.Entity('todo');
# leanpub-end-insert
~~~~~~~~

Deuxièmement, vous pouvez utiliser le schéma pour normaliser votre todos initiale et l'utiliser en tant que paramètre par défaut de votre `todoReducer`.

{title="src/index.js",lang="javascript"}
~~~~~~~~
// reducers

const todos = [
  ...
];

# leanpub-start-insert
const normalizedTodos = normalize(todos, [todoSchema]);
const initialTodoState = {
  entities: normalizedTodos.entities.todo,
  ids: normalizedTodos.result,
};

function todoReducer(state = initialTodoState, action) {
# leanpub-end-insert
  ...
}
~~~~~~~~

Troisièmement, votre `todoReducer` a besoin de manipuler la structure d'état normalisé. Il doit traiter avec les `entities` et un `result` (= liste des identifiants). Vous pouvez afficher les todos normalisés même si l'application Todo crash lorsque vous tentez de la démarrer.

{title="src/index.js",lang="javascript"}
~~~~~~~~
const normalizedTodos = normalize(todos, [todoSchema]);
console.log(normalizedTodos);
~~~~~~~~

Le reducer ajusté aura les fonctions internes suivantes :

{title="src/index.js",lang="javascript"}
~~~~~~~~
function applyAddTodo(state, action) {
  const todo = { ...action.todo, completed: false };
  const entities = { ...state.entities, [todo.id]: todo };
  const ids = [ ...state.ids, action.todo.id ];
  return { ...state, entities, ids };
}

function applyToggleTodo(state, action) {
  const id = action.todo.id;
  const todo = state.entities[id];
  const toggledTodo = { ...todo, completed: !todo.completed };
  const entities = { ...state.entities, [id]: toggledTodo };
  return { ...state, entities };
}
~~~~~~~~

Elles opèrent sur `entities` et `ids`, car ceci est la sortie de la normalisation. Enfin mais pas des moindres, lors de la connexion de Redux avec React, le composant a besoin d'être conscient de la structure d'état normalisée. Premièrement, la connexion entre le store et les composants :

{title="src/index.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
function mapStateToPropsList(state) {
  return {
    todosAsIds: state.todoState.ids,
  };
}

function mapStateToPropsItem(state, props) {
  return {
     todo: state.todoState.entities[props.todoId],
  };
}
# leanpub-end-insert

# leanpub-start-insert
function mapDispatchToPropsItem(dispatch) {
# leanpub-end-insert
  return {
     onToggleTodo: id => dispatch(doToggleTodo(id)),
  };
}

# leanpub-start-insert
const ConnectedTodoList = connect(
  mapStateToPropsList
)(TodoList);
const ConnectedTodoItem = connect(
  mapStateToPropsItem,
  mapDispatchToPropsItem
)(TodoItem);
# leanpub-end-insert
~~~~~~~~

Deuxièmement, le composant `TodoList` reçoit seulement `todosAsIds` et le `TodoItem` reçoit l'entité `todo`.

{title="src/index.js",lang="javascript"}
~~~~~~~~
function TodoApp() {
  return <ConnectedTodoList />;
}

# leanpub-start-insert
function TodoList({ todosAsIds }) {
# leanpub-end-insert
  return (
    <div>
# leanpub-start-insert
      {todosAsIds.map(todoId => <ConnectedTodoItem
        key={todoId}
        todoId={todoId}
      />)}
# leanpub-end-insert
    </div>
  );
}

function TodoItem({ todo, onToggleTodo }) {
  ...
}
~~~~~~~~

L'application doit fonctionner de nouveau. Démarrer là et jouer avec. Vous pouvez trouver le code source sur le [dépôt GitHub](https://github.com/rwieruch/taming-the-state-todo-app/tree/6.0.0). Vous avez normalisé votre structure d'état initial et ajusté votre reducer pour traiter avec la nouvelle structure de données.

Dans la quatrième et dernière partie, vous allez utiliser les sélecteurs pour votre architecture Redux. Ce refactoring est assez évident. Vous devez extraire les parties qui opèrent sur l'état dans vos fonctions  `mapStateToProps()` vers des fonctions de sélection. Premièrement, définissez les fonctions de sélection :

{title="src/index.js",lang="javascript"}
~~~~~~~~
// selectors

function getTodosAsIds(state) {
  return state.todoState.ids;
}

function getTodo(state, todoId) {
  return state.todoState.entities[todoId];
}
~~~~~~~~

Deuxièmement, vous pouvez utiliser ces fonctions à la place d'opérer directement sur l'état dans vos fonctions `mapStateToProps()` :

{title="src/index.js",lang="javascript"}
~~~~~~~~
// Liaison React et Redux

function mapStateToPropsList(state) {
  return {
# leanpub-start-insert
    todosAsIds: getTodosAsIds(state),
# leanpub-end-insert
  };
}

function mapStateToPropsItem(state, props) {
  return {
# leanpub-start-insert
     todo: getTodo(state, props.todoId),
# leanpub-end-insert
  };
}
~~~~~~~~

L'application Todo doit maintenant fonctionner avec des sélecteurs. De nouveau, vous pouvez la trouver sur le [dépôt GitHub](https://github.com/rwieruch/taming-the-state-todo-app/tree/7.0.0).

## Cas pratique: Todo mais avec plus de fonctionnalités

Dans l'application Todo, il y a deux pièces manquantes en matière de fonctionnalité souhaitée : la possibilité d'ajouter un todo et de filtrer mes todos par leur état complet. Débutons avec la création d'un élément todo. Tout d'abord, vous avez besoin d'un composant où vous pouvez écrire le nom du todo et soumettre sa création.

{title="src/index.js",lang="javascript"}
~~~~~~~~
class TodoCreate extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      value: '',
    };

    this.onCreateTodo = this.onCreateTodo.bind(this);
    this.onChangeName = this.onChangeName.bind(this);
  }

  onChangeName(event) {
    this.setState({ value: event.target.value });
  }

  onCreateTodo(event) {
    this.props.onAddTodo(this.state.value);
    this.setState({ value: '' });
    event.preventDefault();
  }

  render() {
    return (
      <div>
        <form onSubmit={this.onCreateTodo}>
          <input
            type="text"
            placeholder="Add Todo..."
            value={this.state.value}
            onChange={this.onChangeName}
          />
          <button type="submit">Add</button>
        </form>
      </div>
    );
  }
}
~~~~~~~~

De nouveau remarquez que le composant est totalement inconscient de Redux. Il met simplement à jour sa `value` locale d'état et une fois le formulaire soumis, il utilise la `value` local de l'état dans la fonction de callback `onAddTodo()` qui est accessible dans l'objet `props`. Le composant ne sait pas si la fonction de callback met à jour l'état local d'un composant parent ou bien le Redux store. Ensuite, vous pouvez utiliser la version connectée de ce composant dans le composant `TodoApp`.

{title="src/index.js",lang="javascript"}
~~~~~~~~
function TodoApp() {
  return (
# leanpub-start-insert
    <div>
      <ConnectedTodoCreate />
# leanpub-end-insert
      <ConnectedTodoList />
# leanpub-start-insert
    </div>
# leanpub-end-insert
  );
}
~~~~~~~~

La dernière étape est de câbler le composant React au Redux store en faisant un composant connecté.

{title="src/index.js",lang="javascript"}
~~~~~~~~
function mapDispatchToPropsCreate(dispatch) {
  return {
    onAddTodo: name => dispatch(doAddTodo(uuid(), name)),
  };
}

const ConnectedTodoCreate = connect(null, mapDispatchToPropsCreate)(TodoCreate);
~~~~~~~~

Il utilise la fonction `mapDispatchToPropsCreate()` pour avoir accès à la méthode dispatch du Redux store. Le créateur d'action `doAddTodo()` prend le nom de l'élément todo, provenant du composant `TodoCreate`, et génère un unique identifiant à l'aide de la fonction `uuid()`. La fonction `uuid()` est une fonction assistante qui provient de la bibliothèque [uuid](https://github.com/kelektiv/node-uuid). Premièrement, vous devez l'installer :

{title="Command Line: /",lang="text"}
~~~~~~~~
npm install --save uuid
~~~~~~~~

Et deuxièmement, vous pouvez l'importer pour générer des identifiants uniques :

{title="src/index.js",lang="javascript"}
~~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import { applyMiddleware, combineReducers, createStore } from 'redux';
import { Provider, connect } from 'react-redux';
import { createLogger } from 'redux-logger';
import { schema, normalize } from 'normalizr';
# leanpub-start-insert
import uuid from 'uuid/v4';
# leanpub-end-insert
import './index.css';
~~~~~~~~

Maintenant, vous pouvez essayer de créer un élément todo dans votre application Todo. Cela doit fonctionner. Ensuite vous voulez rendre possible l'utilisation de votre fonctionnalité de filtre afin de filtrer la liste des éléments todo par leur propriété `completed`. Premièrement, vous devez ajouter un composant `Filter`.

{title="src/index.js",lang="javascript"}
~~~~~~~~
function Filter({ onSetFilter }) {
  return (
    <div>
      Show
      <button
        type="button"
        onClick={() => onSetFilter('SHOW_ALL')}>
        All</button>
      <button
        type="button"
        onClick={() => onSetFilter('SHOW_COMPLETED')}>
        Completed</button>
      <button
        type="button"
        onClick={() => onSetFilter('SHOW_INCOMPLETED')}>
        Incompleted</button>
    </div>
  );
}
~~~~~~~~

Le composant `Filter` reçoit seulement une fonction de callback. De nouveau il n'a aucune connaissance de la gestion d'état qui se produit au-dessus dans Redux ou autre part. La fonction de callback est seulement utilisé dans différents boutons pour mettre en place des types de filtre spécifique. De nouveau, vous pouvez utiliser le composant connecté dans le composant `TodoApp`.

{title="src/index.js",lang="javascript"}
~~~~~~~~
function TodoApp() {
  return (
    <div>
# leanpub-start-insert
      <ConnectedFilter />
# leanpub-end-insert
      <ConnectedTodoCreate />
      <ConnectedTodoList />
    </div>
  );
}
~~~~~~~~

Enfin mais pas des moindres, vous vous devez de connecter le composant `Filter` pour en faites l'utiliser dans le composant `TodoApp`. Cela dispatche le créateur d'action `doSetFilter` en passant le type de filtre issu des boutons sous-jacents du composant `Filter`.

{title="src/index.js",lang="javascript"}
~~~~~~~~
function mapDispatchToPropsFilter(dispatch) {
  return {
    onSetFilter: filterType => dispatch(doSetFilter(filterType)),
  };
}

const ConnectedFilter = connect(null, mapDispatchToPropsFilter)(Filter);
~~~~~~~~

Lorsque vous démarrez votre Todo application, vous verrez que le `filterState` changera une fois que vous cliquer sur l'un de vos bouttons de filtre. Mais rien ne se produira au niveau de vos todos affichées. Ils ne sont pas filtrés ceci est dû au fait que dans votre sélecteur vous sélectionnez la liste complète de todos. La prochaine étape consisterait à régler le sélecteur afin de retenir seulement les todos dans la liste qui correspondent au filtre. Premièrement, vous pouvez définir des fonctions de filtres qui font correspondre les todos par rapport à leurs états `completed`.

{title="src/index.js",lang="javascript"}
~~~~~~~~
// filters

const VISIBILITY_FILTERS = {
  SHOW_COMPLETED: item => item.completed,
  SHOW_INCOMPLETED: item => !item.completed,
  SHOW_ALL: item => true,
};
~~~~~~~~

Deuxièmement, vous pouvez utiliser votre sélecteur pour sélectionner seulement les todos correspondant au filtre. Vous avez déjà tous les sélecteurs en place. Mais vous avez besoin de régler l'un d'entre eux pour filtrer les todos en rapport avec le `filterState` issu du Redux store.

{title="src/index.js",lang="javascript"}
~~~~~~~~
// selectors

function getTodosAsIds(state) {
# leanpub-start-insert
  return state.todoState.ids
    .map(id => state.todoState.entities[id])
    .filter(VISIBILITY_FILTERS[state.filterState])
    .map(todo => todo.id);
# leanpub-end-insert
}

function getTodo(state, todoId) {
  return state.todoState.entities[todoId];
}
~~~~~~~~

Puisque votre état est normalisé, vous devez opérer une projection au travers de tous vos `ids` pour obtenir une liste de `todos`, les filtrer à l'aide de `filterState`, et refaire une projection de nouveau pour sélectionner les `ids`. C'est un compromis que vous allez rencontrer lorsque votre structure de données est normalisée, car un moment donné vous devez toujours le dénormaliser. Maintenant, votre fonctionnalité de filtre doit fonctionner une fois que vous démarrer votre application de nouveau.

Vous pouvez trouver la version finale de l'application sur ce [dépôt GitHub](https://github.com/rwieruch/taming-the-state-todo-app/tree/8.0.0). Cela applique tous les connaissances au sujet du Redux middleware, des structures de données normalisées et immutables ainsi que des sélecteurs.
