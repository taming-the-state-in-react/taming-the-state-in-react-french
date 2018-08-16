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

Cependant, vous pouvez l'utiliser pour expérimenter les mises à jour de votre état pendant l'apprentissage de MobX. Vous pouvez simplement l'ajouter dans l'exemple du `TodoStore`.

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

Il sera exécuté la première fois lors de l'initialisation de l'état, et ensuite à chaque fois dès lors que l'état observable est mis à jour. Vous pouvez ouvrir le [MobX Playground](https://jsbin.com/gezibazuke/1/edit?js,console) pour expérimenter.

### Actions

Comme mentionné, l'état peut être muté directement dans MobX. Mais certaines personnes vous soutiendront que c'est une mauvaise pratique. lorsque vous commencez à muter l'état directement dans un `onClick` handler, cela couple la mutation de l'état avec la couche vue. Par conséquent, vous pouvez utiliser les actions MobX pour découpler le changement d'état et conserver à un seul endroit votre état à jour.

{title="Code Playground",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const { observable, autorun, action } = mobx;
# leanpub-end-insert

class TodoStore {
  @observable todos = [];

# leanpub-start-insert
  @action addTodo(todo) {
    this.todos.push(todo);
  }
# leanpub-end-insert
}

const todoStore = new TodoStore();

autorun(() => console.log(todoStore.todos.length));

todoStore.addTodo({ id: '0', name: 'learn redux', completed: true });
todoStore.addTodo({ id: '1', name: 'learn mobx', completed: false });
~~~~~~~~

Cependant, MobX n'a pas d'avis sur la façon de mettre à jour votre état. Vous pouvez utiliser les actions ou bien muter l'état directement sans aucune action.

{title="Code Playground",lang="javascript"}
~~~~~~~~
class TodoStore {
  @observable todos = [];

# leanpub-start-insert
  addTodo(todo) {
# leanpub-end-insert
    this.todos.push(todo);
  }
}
~~~~~~~~

Dans le but de renforcer les changements d'état avec des actions, vous pouvez opter pour une configuration avec la fonctionnalité `configure()` de MobX. Ici vous pouvez passer un objet dans le but de configurer globalement MobX tandis qu'il y a une configuration en particulier pour imposer les actions MobX. Après l'avoir fixé à *true*, tout changement d'état aura besoin de passer par une action. De cette façon vous consolidez le découplage entre la mutation de l'état et la vue.

{title="Code Playground",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const { observable, autorun, action, configure } = mobx;
# leanpub-end-insert

# leanpub-start-insert
configure({ enforceActions: true });
# leanpub-end-insert

class TodoStore {
  @observable todos = [];

# leanpub-start-insert
  @action addTodo(todo) {
# leanpub-end-insert
    this.todos.push(todo);
  }
}

const todoStore = new TodoStore();

autorun(() => console.log(todoStore.todos.length));

todoStore.addTodo({ id: '0', name: 'learn redux', completed: true });
todoStore.addTodo({ id: '1', name: 'learn mobx', completed: false });
~~~~~~~~

Vous pouvez tester l'action MobX et la fonction `configure()` avec les actions imposées dans le [MobX Playground](https://jsbin.com/zigapodeke/1/edit?js,console). Qui plus est, il fait toujours sens de bien penser vos actions. Dans le cas précédent, toute appelle de `addTodo()` entrainera les *reactions* reliées à s'exécuter. C'est pourquoi la fonction autorun se lance à chaque fois que vous ajoutez un élément todo. Donc comment ajouter plusieurs éléments todo sans déclencher des *reactions* à chaque fois? Vous pouvez ajouter une autre action qui prend un tableau d'éléments.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const { observable, autorun, action } = mobx;

class TodoStore {
  @observable todos = [];

  @action addTodo(todo) {
    this.todos.push(todo);
  }

# leanpub-start-insert
  @action addTodos(todos) {
    todos.forEach(todo => this.addTodo(todo));
  }
# leanpub-end-insert
}

const todoStore = new TodoStore();

autorun(() => console.log(todoStore.todos.length));

# leanpub-start-insert
todoStore.addTodos([
  { id: '0', name: 'learn redux', completed: true },
  { id: '1', name: 'learn mobx', completed: false },
]);
# leanpub-end-insert
~~~~~~~~

De cette façon, les *reaction* reliées seront évaluées seulement une fois après que l'action a été appelée. Vous pouvez trouver le code nécessaire pour faire des expériences dans le [MobX Playground](https://jsbin.com/yunebovose/1/edit?js,console).

### Les valeurs calculées

Les valeurs calculées sont des propriétés dérivées de l'état ou d'autres valeurs calculées. Ce sont des fonctions pures ils n'ont donc pas d'effet de bord. Les valeurs calculées vous aident à conserver votre data structure d'état simple tout en laissant la possibilité de les dériver pour obtenir des propriétés complexes à partir de ces dernières. Par exemple, lorsque vous souhaitez filtrer une liste de todos par leur propriété `completed`, vous pourrez calculer en valeurs les éléments todo incomplétés.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const { observable, action, computed } = mobx;

class TodoStore {
  @observable todos = [];

  @action addTodo(todo) {
    this.todos.push(todo);
  }

# leanpub-start-insert
  @computed get incompleteTodos() {
    return this.todos.filter(todo => !todo.completed);
  }
# leanpub-end-insert
}
~~~~~~~~

Le calcul se produit réactivement lorsque l'état a été modifié et qu'une *reaction* le demande. Ainsi, ces valeurs calculées sont à votre disposition, à part pour l'état en lui-même, mais plutôt votre couche vue. De plus, elles ne sont pas calculées activement à chaque fois mais seulement réactivement lorsqu'une réaction le demande. Vous pouvez l'expérimenter dans le [MobX Playground](https://jsbin.com/tapiyuricu/1/edit?js,console).

# MobX en React

Les bases en MobX devraient être claires maintenant. L'état dans MobX est mutable et peut-être muté directement ou par actions voire exclusivement par des actions en les imposant via configuration. Lors de la mise à échelle de votre gestion d'état en MobX, vous souhaiterez le conserver dans de multiples mais néanmoins administrables stores afin de le garder maintenable. Ces stores peuvent exposer les actions et les valeurs calculées, mais plus important encore ils rendent leurs propriétés observables. Tout cela vous procure en fait une version très orientée de comment stocker votre état (ex: avec les stores) et comment mettre à jour l'état (ex: les actions explicites avec les actions imposées). Cependant, vous pouvez décider d'utiliser une approche orientée différente.

Maintenant, à chaque fois qu'une propriété observable dans le store change, la fonction `autorun` de MobX est lancé. L'`autorun` rend possible le pont entre le changement d'état de MobX auprès des autres environnements. Par exemple, il peut être utilisé dans une couche vue, tel que React, pour re-rendre à chaque changement d'état. MobX et React s'accorde parfaitement. Les deux bibliothèques résolvent leur propre problème, mais peuvent être utilisées ensemble pour construire de complexe application grandissante. La couche vue de React peut recevoir l'état issu de MobX, mais aussi peut muter l'état.

Lorsque vous commencez à introduire React auprès de votre MobX Playground, vous pouvez commencer à afficher la liste des éléments todo issue votre `todoStore`.

{title="Code Playground",lang="javascript"}
~~~~~~~~
class TodoList extends React.Component {
  render() {
    return (
      <div>
        {this.props.todoStore.todos.map(todo =>
          <div key={todo.id}>
            {todo.name}
          </div>
        )}
      </div>
    );
  }
}

ReactDOM.render(
  <TodoList todoStore={todoStore} />,
  document.getElementById('app')
);
~~~~~~~~

Cependant, lorsque vous mettez à jour votre MobX store rien ne se produit. La couche vue n'est pas notifiée des changements d'état, car ceci se produit à l'extérieur de React. Vous pouvez utiliser la fonction `autorun` de MobX pour introduire un re-rendu naïf de la couche vue.

{title="Code Playground",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
function render() {
# leanpub-end-insert
  ReactDOM.render(
    <TodoList todoStore={todoStore} />,
    document.getElementById('app')
  );
# leanpub-start-insert
}
# leanpub-end-insert

# leanpub-start-insert
autorun(render);
# leanpub-end-insert
~~~~~~~~

Maintenant vous avez un rendu initial de la couche vue, car l'`autorun` est exécuté à l'initialement ainsi que des rendus successifs lors des changements d'état de MobX. Vous pouvez vous amuser avec cela sur le [MobX Playground](https://jsbin.com/delohuwidi/1/edit?js,output).

Il existe une bibliothèque bien maintenue qui s'occupe du pont depuis MobX vers React: [mobx-react](https://github.com/mobxjs/mobx-react). Elle vous permet d'économiser l'utilisation de la *reaction* `autorun` dans le but de re-rendre la couche vue. À la place elle utilise un décorateur `observer`, qui utilise la fonction `autorun` sous le capot, pour produire une *reaction*. La *reaction* propage simplement le changement vers le composant React pour le re-rendre. Cela rend votre couche vue React réactive et la re-rend lorsque l'état observable dans MobX a été modifié. Cela rend votre couche vue de React réactive et re-rend à chaque fois que l'état observable dans MobX a changé.

{title="Code Playground",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const { observer } = mobxReact;
# leanpub-end-insert

...

# leanpub-start-insert
@observer
# leanpub-start-insert
class TodoList extends React.Component {
  render() {
    return (
      <div>
        {this.props.todoStore.todos.map(todo =>
          <div key={todo.id}>
            {todo.name}
          </div>
        )}
      </div>
    );
  }
}

ReactDOM.render(
  <TodoList todoStore={todoStore} />,
  document.getElementById('app')
);

...
~~~~~~~~

Encore une fois, vous pouvez jouer avec dans le [MobX Playground](https://jsbin.com/rusewogaza/1/edit?html,js,output). Si vous souhaitez utiliser le composant `TodoList` en tant que composant fonctionnel, vous pouvez utiliser l'`observer` en tant que fonction plutôt qu'en tant que JavaScript décorateur.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const TodoList = observer(function (props) {
  return (
    <div>
      {props.todoStore.todos.map(todo =>
        <div key={todo.id}>
          {todo.name}
        </div>
      )}
    </div>
  );
});
~~~~~~~~

Vous pouvez trouver l'exemple dans le [MobX Playground](https://jsbin.com/wefoyocutu/1/edit?html,js,output) pour jouer avec.

## L'état local

Comme mentionné auparavant, MobX n'est pas orienté sur la façon de stocker votre état et comment le mettre à jour. Jusqu'ici, vous pouvez même échanger votre état local dans React, `this.state` and `this.setState()`, avec MobX. Par exemple, ici vous ne voudriez pas utiliser un store qui sépare l'état de la vue, mais utiliser à la place les propriétés de la classe du composant. Le cas peut être démontré en introduisant un composant qui ajoute un élément todo vers la liste des éléments todo de l'exemple précédent.

{title="Code Playground",lang="javascript"}
~~~~~~~~
ReactDOM.render(
# leanpub-start-insert
  <div>
    <TodoAdd todoStore={todoStore} />
# leanpub-end-insert
    <TodoList todoStore={todoStore} />
# leanpub-start-insert
  </div>,
# leanpub-end-insert
  document.getElementById('app')
);
~~~~~~~~

Le composant `TodoAdd` affiche seulement un champ d'entrée pour capturer le nom de l'élément todo et un bouton pour le créer.

{title="Code Playground",lang="javascript"}
~~~~~~~~
@observer
class TodoAdd extends React.Component {
  render() {
    return (
      <div>
        <input
          type="text"
          value={this.input}
          onChange={this.onChange}
        />
        <button
          type="button"
          onClick={this.onSubmit}
        >Add Todo</button>
      </div>
    );
  }
}
~~~~~~~~

Les deux méthodes handlers de classe sont manquantes. L'handler `onChange` peut-être une action à elle seule qui met à jour une valeur gérée internement du champ d'entrée.

{title="Code Playground",lang="javascript"}
~~~~~~~~
@observer
class TodoAdd extends React.Component {

# leanpub-start-insert
  @observable input = '';

  onChange = (event) => {
    this.input = event.target.value;
  }
# leanpub-end-insert

  render() {
    ...
  }
}
~~~~~~~~

De cette manière, la propriété `input` n'est pas allouée dans l'état local de React, mais dans l'état observable de MobX. Le décorateur `observer` s'assure que le composant reste réactif a ses propriétés observées. Finalement l'handler `onSubmit` créé un élément todo néanmoins altère l'état local du composant, car il doit réinitialiser la valeur du champ et incrémenter l'identifiant.

{title="Code Playground",lang="javascript"}
~~~~~~~~
@observer
class TodoAdd extends React.Component {

  @observable input = '';
# leanpub-start-insert
  @observable id = 0;

  onSubmit = () => {
    this.props.todoStore.addTodo({
      id: this.id,
      name: this.input,
      completed: false,
    });

    this.id++;
    this.input = '';
  }
# leanpub-end-insert

  onChange = (event) => {
    ...
  }

  render() {
    ...
  }
}
~~~~~~~~

MobX est capable de prendre le contrôle de l'état local de React. Vous n'aurez plus besoin d'utiliser `this.state` and `this.setState()`. L'exemple précédent peut être trouvé sur le [MobX Playground](https://jsbin.com/velihicomu/2/edit?html,js,output). De nouveau vous ressentez que MobX n'est pas orienté sur la façon dont l'état est géré. Vous pouvez la gestion d'état encapsulé dans une classe store ou le couplé à un composant comme état local. Cela peut rendre l'état local de React obsolète. Mais devrait-on faire cela? C'est à vous de juger.

## Mise à l'échelle des Réactions

Chaque composant peut être décoré avec un *observer* pour devenir réactif au changement d'état observable de MobX. Lors de l'introduction d'un nouveau composant pour afficher un élément todo, vous pouvez le décorer également. Ce composant `TodoItem` reçoit la propriété todo, mais aussi le `todoStore` dans le but de compléter un élément todo.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const TodoItem = observer(({ todo, todoStore }) => {
  return (
    <div>
      {todo.name}
      <button
         type="button"
         onClick={() => todoStore.toggleCompleted(todo)}
       >
       {todo.completed
         ? "Incomplete"
         : "Complete"
       }
      </button>
    </div>
  );
});
~~~~~~~~

Remarquez que le `TodoItem` est un composant *stateless* fonctionnel. De plus, dans le but de compléter l'élément todo, vous aurez à introduire l'action `toggleCompleted` dans le `TodoStore`.

{title="Code Playground",lang="javascript"}
~~~~~~~~
class TodoStore {
  @observable todos = [];

  ...

# leanpub-start-insert
   @action toggleCompleted(todo) {
    todo.completed = !todo.completed;
  }
# leanpub-end-insert
}
~~~~~~~~

Maintenant, le composant `TodoList` peut utiliser le composant `TodoItem`.

{title="Code Playground",lang="javascript"}
~~~~~~~~
@observer
class TodoList extends React.Component {
  render() {
    return (
      <div>
        {this.props.todoStore.todos.map(todo =>
# leanpub-start-insert
          <TodoItem
            todoStore={this.props.todoStore}
            todo={todo}
            key={todo.id}
          />
# leanpub-end-insert
        )}
      </div>
    );
  }
};
~~~~~~~~

Lors de l'exécution de l'application vous devriez être capable de compléter un élément todo. Le bénéfice de séparer un composant réactif en plusieurs composants réactifs peut-être vu lors de l'ajout de deux instructions `console.log()`.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const TodoItem = observer(({ todo, todoStore }) => {
# leanpub-start-insert
  console.log('TodoItem: ' + todo.name);
# leanpub-end-insert
  return (
    ...
  );
});

@observer
class TodoList extends React.Component {
# leanpub-start-insert
  console.log('TodoList');
# leanpub-end-insert
  render() {
    ...
  }
};
~~~~~~~~

Vous pouvez ouvrir l'application dans le [MobX Playground](https://jsbin.com/qecawoweja/2/edit?js,console,output). Lors de l'ajout d'un élément todo, vous obtiendrez les sorties de `console.log()` pour le composant `TodoList` et le composant `TodoItem` nouvellement créé. Quand vous complétez un élément todo, vous aurez uniquement le `console.log()` de la complétion du composant `TodoItem`. Le composant réactif se met à jour uniquement lorsque leur état observable change. Pas plus, car le décorateur `observer` implémente sous le capot la méthode du cycle de vie `shouldComponentUpdate()` de React pour empêcher le composant de se mettre à jour lorsque rien à changé. Vous pouvez en lire plus à propos de l'optimisation de performance MobX au sein de React sur la [documentation officielle](https://mobx.js.org/best/react-performance.html).

## Injecter les Stores

Jusqu'ici, l'application transmet le store aux composants enfants à partir des points d'entrées de React à savoir les props. Ils sont déjà transmis sur plus qu'une couche. Cependant, les stores peuvent être utilisés directement dans les composants (lorsque accessible dans le fichier). Ils ne sont qu'un état observable. Puisque MobX n'est pas orienté sur l'emplacement pour présenter l'état, l'état observable et dans notre cas des stores, vous pouvez le faire vivre depuis partout. Mais comme mentionné, le livre essaye de fournir une approche orientée comme meilleur pratique.

La bibliothèque [mobx-react](https://github.com/mobxjs/mobx-react) vous fournit deux assistants pour propager l'état observable implicitement vers les composants (via le contexte React) plutôt que de le passer explicitement au travers de chaque composant.

Le premier assistant est le `Provider` qui transmet tous les états observables nécessaires.

{title="Code Playground",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const { observer, Provider } = mobxReact;
# leanpub-end-insert

...
~~~~~~~~

Vous pouvez l'utiliser au sein de points d'entrée de React pour entourer votre arbre de composant. De plus, vous pouvez passer n'importe quel état observable qui devrait être transmis plus bas. Dans ce cas, l'état observable est l'instance du store.

{title="Code Playground",lang="javascript"}
~~~~~~~~
...

ReactDOM.render(
# leanpub-start-insert
  <Provider todoStore={todoStore}>
    <div>
      <TodoAdd />
      <TodoList />
    </div>
  </Provider>,
# leanpub-end-insert
  document.getElementById('app')
);
~~~~~~~~

Cependant, il peut y avoir plusieurs stores ou seulement quelques primitives observables.

{title="Code Playground",lang="javascript"}
~~~~~~~~
<Provider
  storeOne={storeOne}
  storeTwo={storeTwo}
  anyOtherState={anyOtherState}
>
  ...
</Provider>
~~~~~~~~

Le second assistant de la bibliothèque est le décorateur `inject`. Vous pouvez l'utiliser pour tout composant en aval qui est entouré par le composant `Provider` quelque part en amont de votre arbre de composant. Il retrouve l'état observable fourni auprès du contexte de React en tant que props.

{title="Code Playground",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const { observer, inject, Provider } = mobxReact;
# leanpub-end-insert

...

# leanpub-start-insert
@inject('todoStore') @observer
# leanpub-end-insert
class TodoAdd extends React.Component {
  ...
}
~~~~~~~~

Maintenant le composant `TodoAdd` a accès au `todoStore`. Vous pouvez également ajouter l'injection auprès d'autres composants. Il peut être utilisé en tant que fonction pour les composants fonctionnels sans état.

{title="Code Playground",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const TodoItem = inject('todoStore')(observer(({
# leanpub-end-insert
  todo, todoStore
}) =>
  <div>
    {todo.name}
    <button
      type="button"
      onClick={() => todoStore.toggleCompleted(todo)}
    >
    {todo.completed
      ? "Incomplete"
      : "Complete"
    }
    </button>
  </div>
# leanpub-start-insert
));
# leanpub-end-insert
~~~~~~~~

Le composant `TodoList` n'a plus besoin de transmettre manuellement le `todoStore` en aval. Le `TodoItem` y accède via son assistant `inject`.

{title="Code Playground",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
@inject('todoStore') @observer
# leanpub-end-insert
class TodoList extends React.Component {
  render() {
    return (
      <div>
        {this.props.todoStore.todos.map(todo =>
# leanpub-start-insert
          <TodoItem
            todo={todo}
            key={todo.id}
          />
# leanpub-end-insert
        )}
      </div>
    );
  }
};
~~~~~~~~

N'importe quel composant peut accéder à l'état observable, qui est passé au composant `Provider`, avec le décorateur `inject`. De cette façon vous pouvez conserver une séparation nette entre l'état et la vue. De nouveau vous pouvez accéder au projet dans le [MobX Playground](https://jsbin.com/xubewezeji/3/edit?js,output).

## MobX avancé

MobX n'est pas orienté. Ainsi il vous fournit des outils pratiques pour contrôler la gestion d'état de votre propre manière. Il serait tout à fait suffisant d'utiliser les bases de MobX pour introduire la gestion d'état au sein de votre application. Mais il y a énormément d'outils cachés que ce chapitre est sur le point de vous présenter. De plus, il devrait vous fournir quelques enseignements pour comprendre et utiliser de votre façon MobX avec succès.

### Les autres réactions

Vous avez jusqu'ici rencontré deux réactions: `autorun` et `observer`. L'`observer` produit une réaction car il utilise l'autorun derrière. Il est seulement utilisé dans le package mobx-react. Ainsi, les deux fonctions sont utilisées pour créer des réactions basées sur les changements d'état observable. Alors que la fonction d'autorun peut être utilisée pour rerendre naïvement la UI (User Interface), elle peut aussi être utilisée dans des domaines plus variés. Tandis que le décorateur `observer` est seulement utilisé pour rendre la couche vue réactive.

Cependant, MobX embarque plus de réactions. Le livre ne s'éternisera pas sur les détails, mais il n'y a pas de mal à être alerte sur les autres options disponibles. La [MobX when](https://mobx.js.org/refguide/when.html) est une autre fonction qui produit une réaction. Elle est basée sur des prédicats et des effets. Un prédicat est calculé jusqu'à ce qu'il retourne true. Lorsqu'il retourne true, l'effet est appelé. Après quoi l'autorunner est prêt. La fonction `when` retourne un *disposer* pour annuler l'autorunner prématurément, donc avant qu'un effet puisse être appelé.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const { observable, autorun, computed, when } = mobx;

class TodoStore {
  @observable todos = [];

  constructor() {
    when(
      // once (predicate)...
      () => this.hasCompleteTodos,
      // ... then (effect)
      () => this.celebrateAccomplishment()
    );
  }

  @computed get completeTodos() {
    return this.todos.filter(todo => todo.completed);
  }

  @computed get hasCompleteTodos() {
    return this.completeTodos.length > 0;
  }

  celebrateAccomplishment() {
    console.log('First todo completed, celebrate it!');
  }
}

const todoStore = new TodoStore();

autorun(() => console.log(todoStore.todos.length));

todoStore.todos.push({ id: '0', name: 'finish the book', completed: false });
todoStore.todos.push({ id: '1', name: 'learn redux', completed: true });
todoStore.todos.push({ id: '2', name: 'learn mobx basics', completed: true });
todoStore.todos.push({ id: '3', name: 'learn mobx', completed: false });
~~~~~~~~

Alors combien de fois la réaction est elle exécutée? Vous pouvez prendre vos paris et ensuite ouvrir le [MobX Playground](https://jsbin.com/kuzadiwagi/1/edit?js,console) pour expérimenter par vous-même la réaction. En gros, la fonction `when` déclenche son effet lorsque le prédicat retourne true. Mais il le déclenche une seule fois. Comme vous pouvez le voir, deux éléments todo complétés sont ajoutés. Cependant, la méthode `celebrateAccomplishment()` s'exécute une seule fois. De cette façon MobX vous permet d'utiliser ses réactions pour déclencher des effets de bord. Vous pouvez déclencher n'importe quoi allant de l'animation à l'appelle d'API.

Une autre fonction dans MobX, [la reaction fonction](https://mobx.js.org/refguide/reaction.html) en elle-même, peut être également utilisé pour produire des réactions MobX. C'est une version affinée de l'autorun. Alors que l'autorun sera toujours exécuté lorsque l'état observable a été modifié, la fonction reaction se lance seulement lorsqu'un état observable particulier fourni a été modifié.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const { observable, autorun, computed, reaction } = mobx;

class TodoStore {
  @observable todos = [];

  constructor() {

    reaction(
      () => this.completeTodos.length,
      sizeCompleteTodos => console.log(sizeCompleteTodos + " todos completed!")
    );
  }

  @computed get completeTodos() {
    return this.todos.filter(todo => todo.completed);
  }
}

const todoStore = new TodoStore();

autorun(() => console.log(todoStore.todos.length));

todoStore.todos.push({ id: '0', name: 'finish the book', completed: false });
todoStore.todos.push({ id: '1', name: 'learn redux', completed: true });
todoStore.todos.push({ id: '2', name: 'learn mobx basics', completed: true });
todoStore.todos.push({ id: '3', name: 'learn mobx', completed: false });
~~~~~~~~

Combien de fois la réaction s'exécute? En premier tenté de deviner, ensuite vous pouvez confirmer ou infirmer votre intuition en essayant par vous-même dans le [MobX Playground](https://jsbin.com/jizidoyoge/1/edit?js,console).

Maintenant vous avez vu deux fonctions supplémentaires au sein de MobX qui produisent des réactions: `when` et `reaction`. Alors que la fonction `when` lance seulement un *effect* dès lors que le prédicat retourne null, la `reaction` s'exécute à chaque fois dès lors qu'un état observable particulier a changé. Vous pouvez utiliser ces deux fonctions pour déclencher des effets de bord, tel qu'un appelle d'API.l.

### Soyez orienté

MobX vous fournit tous les outils dont vous avez besoin pour gérer l'état au sein d'applications JavaScript moderne. Cependant, il ne vous fournit pas un point de vue sur la manière de l'implémenter. De cette façon vous êtes libre de gérer votre état même s'il peut être difficile de suivre les meilleurs pratiques ou d'aligner une équipe sur la même philosophie. C'est pourquoi il est important de trouver votre propre façon d'orienter les choses au sein de MobX. Vous devez vous conformer à une seule et unique façon pour administrer votre état.

Les chapitres précédents vous ont montrés que l'état observable au sein de MobX peut être géré très loin dans les stores, cependant il pourrait tout aussi bien être utilisé dans l'état local de la vue. Doit-on au sein de React privilégier MobX à la place de `this.state` et `this.setState()`? Soyez clair sur au combien vous souhaitez laisser votre état MobX près de votre vue.

De plus vous devez avoir une opinion sur la façon de mettre à jour votre état observable. Mutez-vous l'état directement au sein de votre vue? Cette façon de penser vous conduit à coupler votre état au plus proche de votre vue. À l'opposé, vous pouvez utiliser les actions MobX explicitent. Cela pourrait maintenir votre mutation d'état à un seul endroit. Vous pouvez même les rendre obligatoires en utilisant la fonctionnalité `configure()` qui forcera l'utilisation d'actions. De cette façon, toute mutation d'état aura a passer par une action explicite. Plus aucunes mutations directes de l'état ne seront acceptées.
Recommandation: Vous devriez rendre vos mutations d'état aussi explicite que possible avec les actions, `configure()` et le flag `enforceActions` fixé à true.

Lors de l'utilisation de MobX pour compléter votre couche vue, vous aurez besoin de vous décider sur la manière de transmettre votre état. Vous pouvez tout simplement allouer votre état à côté de vos composants, l'importer directement depuis un autre fichier en utilisant les instructions d'import et d'export de JavaScript ES6, le transmettre explicitement (ex: dans React à l'aide des props) ou le transmettre implicitement depuis votre composant racine avec la fonction `inject()` et le composant `Provider`. Vous devriez éviter autant que faire se peut de tous mélanger et suivre une façon de faire. Recommandation: Vous devriez utiliser la fonction `inject()` et le composant `Provider` pour rendre votre état implicitement accessible auprès de votre couche vue.

Enfin mais pas des moindres, vous aurez besoin de vous conformer à une structure d'état. L'état Observable dans MobX peut être n'importe quoi? Cela peut être des primitives, des objets ou tableaux mais il peut aussi stocker des instances dérivées de classes JavaScript. Sans pour autant tout mélanger, vous aurez besoin de vous aligner sur une architecture d'état propre. L'approche qui consiste à gérer votre état dans les stores, comme montrer dans les chapitres précédents, vous fournit une façon maintenable pour gérer votre état pour des domaines spécifiques. De plus, vous êtes capable de conserver les actions, les valeurs calculés et même les réactions telles que l'`autorun`, `reaction` et `when` au sein de votre store. Recommandation: Vous devriez utiliser les classes JavaScript pour gérer votre état dans les stores. Ainsi, votre gestion d'état reste maintenable avec des stores responsables de domaine associé.

Comme vous pouvez le voir, il y a un certain nombre de décisions à faire avant toute utilisation de MobX. Il vous donne une pleine liberté de décision, et en fin de compte vous devez établir votre façon, votre orientation et la respecter.

## Alternative à Redux?

Après tout, MobX est-il une alternative viable à Redux? Tout cela dépend de vous-même et de vos attentes. Les deux solutions sont différentes dans leur philosophie, aussi bien dans leurs mécanismes sous-jacents  que dans leur utilisation. Alors que Redux vous fournit une façon très orientée pour la gestion de votre état, MobX vous donne seulement les outils pour gérer votre état mais pas la manière concrète d'implémentation. Redux a une importante communauté, un écosystème vivant de bibliothèques et une excellente sélection des meilleurs pratiques. MobX est une bibliothèque plus jeune comparativement, mais elle vous fournit une approche différente sur les moyens de gestion de votre état et débarque également avec plein de fonctionnalités puissantes.

Les forces de MobX proviennent de sa nature réactive. Comme vous l'avez vu avec les observers lorsque vous avez connecté votre vue à MobX, seuls les composants réactifs qui sont reliés à un état observable changes. Tout le reste demeure inchangé. Au sein de grandes applications, l'utilisation de MobX de façon appropriée peut vous permettre de conserver vos modifications de la couche vue au strict minimum.

Dans MobX vous n'avez pas besoin de normaliser votre état. Vous pouvez travailler avec des références ou conserver votre état imbriqué. Cela reste très facile de mettre à jour l'état avec mutations sans s'inquiéter d'immutabilité. De l'autre côté, vous devez faire attention sur le couplage entre votre état et votre couche vue. À la fin, lorsque l'état est aussi proche de votre vue , on peut en arriver à la même résultante qu'avec la première génération de SPAs où le *two-way data binding* devient un véritable bazar.

Si vous souhaitez en lire davantage sur les différences de Redux et MobX, je vous recommande de jeter un oeil sur l'article suivant: [Redux ou MobX: Une tentative pour faire disparaître la confusion](https://www.robinwieruch.de/redux-mobx-confusion/). Après cela vous devriez être bien mieux informé sur ce que vous souhaitez utiliser pour la gestion d'état au sein de votre application.
