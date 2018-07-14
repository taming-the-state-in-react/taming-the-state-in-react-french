## Prérequis

Quels sont les prérequis pour lire ce livre? Avant tout, vous devez être familier avec les bases en développement web. Vous devez savoir comment utiliser de l'HTML, CSS et JavaScript. Peut-être qu'il est aussi intéressant de connaitre le terme d'[API](https://www.robinwieruch.de/what-is-an-api-javascript/), car vous utiliserez des APIs dans ce livre. De plus, Je vous encourage à rejoindre le [groupe Slack officiel](https://slack-the-road-to-learn-react.wieruch.com/) du livre pour recevoir de l'aide ou aider les autres.

### React

Le livre utilise React en tant que bibliothèque pour enseigner la gestion d'état moderne. C'est un choix parfait pour la démonstration et l'apprentissage de la gestion d'état au sein des applications modernes. Car React est seulement la couche vue, cela revient à vous de décider comment traiter l'état au sein de votre application. La couche de gestion d'état est interchangeable.

Après tout, ce n'est pas nécessaire d'être un développeur React dans le but d'apprendre la gestion d'état dans les applications modernes. Si vous êtes en train de développer avec un autre framework SPA, tel qu'Angular, ou une bibliothèque, tel que Vue, toutes ces affaires attenantes à la gestion d'état enseigné dans ce livre peuvent toujours être appliquées dans vos applications.

Malgré cela, puisque le livre utilise React pour l'intérêt de l'enseignement de la gestion d'état dans un contexte approprié, si vous n'êtes pas familier avec React ou avait besoin d'un rafraichissement sur le sujet, je vous encourage à lire le précédent livre [The Road to learn React](https://www.robinwieruch.de/the-road-to-learn-react/) ([version française](https://leanpub.com/the-road-to-learn-react-french). C'est gratuit et devrait permettre à tout le monde d'apprendre React. Néanmoins, vous pouvez décider de faire une donation pour supporter le projet.



Même si le livre est gratuit, les personnes en manque d'accès à l'éduction ne dispose pas de ces ressources. Ils ont besoin d'apprendre l'anglais ou le français pour être en capacité d'accéder à l'information. Ainsi, de manière occasionnel The Road to learn React tente [de supporter l'éducation dans les pays en voie de developpement](https://www.robinwieruch.de/giving-back-by-learning-react/), mais cela reste une tâche difficile puisque le livre lui-même est sur la base d'un prix libre.

De plus, The Road to learn Rect vous enseigne à réaliser la transition de JavaScript ES5 vers JavaScript ES6. Après lecture de The Road to learn React, vous devrez posséder toutes les connaissances pour lire ce livre. Il vous prépare parfaitement à React.

### Éditeur et terminal

Qu'en est-il de l'environnement de développement? Vous aurez besoin d'un éditeur ou d'un IDE et d'un terminal (*command line tool*). Vous pouvez [suivre mon guide d'installation](https://www.robinwieruch.de/developer-setup/). C'est illustré pour les utilisateurs de MacOS, mais aussi vous pouvez trouver un guide d'installation Windows pour React. Il y a une pléthore d'articles qui vous montreront comment installer un environnement de développement pour le web et de façon plus élaborée pour votre OS.

Optionnellement, vous pouvez utiliser Git et Github par vous-même, pendant la réalisation des exercices du livre, pour conserver vos projets et la progression dans les dépôts de Github. Il existe un [petit guide](https://www.robinwieruch.de/git-essential-commands/) sur comment utiliser ces outils. Mais encore une fois, ce n'est pas obligatoire pour le livre et peut devenir contraignant lors de l'apprentissage de tout cela en même temps à partir de rien. Donc vous pouvez sauter cela si vous êtes un novice dans le développement web pour vous concentrer sur les parties essentielles apprises dans ce livre.

### Node et NPM

Dernier point, mais pas des moindres, vous aurez besoin d'une installation de [node and npm](https://nodejs.org/en/). Les deux sont utilisés pour gérer les bibliothèques dont vous aurez besoin tout du long. Dans ce livre, vous installerez des *external node packages* via npm (node package manager). Ces *node packages* peuvent être des bibliothèques ou des frameworks en entier.

Vous pouvez vérifier vos versions de node et npm en ligne de commande. Si vous n'obtenez aucune sortie sur le terminal, vous aurez au préalable besoin d'installer node et npm. Ce sont seulement mes versions au moment où j'écris ce livre :

{title="Command Line",lang="text"}
~~~~~~~~
node --version
*v8.2.1
npm --version
*v5.3.0
~~~~~~~~

Si vous avez lu The Road to learn React, vous devez déjà être familier avec la configuration. Le livre vous fournit aussi une petite introduction à l'intérieur de l'écosystème npm en ligne de commande. Donc si vous n'êtes pas familier avec cela, encore une fois vous pouvez récupérer le livre Road to learn React.
