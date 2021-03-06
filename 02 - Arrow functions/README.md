# [`ARROW FUNCTION`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Fonctions/Fonctions_fl%C3%A9ch%C3%A9es), [`FUNCTION`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Fonctions) & [`THIS`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Op%C3%A9rateurs/L_op%C3%A9rateur_this)

Ok donc pour déclarer des fonctions, tu connais sûrement :
```js
function doStuff() {
    // ... code
}
```

Mais il y aussi ce qu'on appelle des **fonctions anonymes** (tu verras plus tard en quoi elles sont pratiques).
```js
// en l'occurence, là j'assigne la fonction anonyme à ma variable doStuff
const doStuff = () => {
  // ... code
}
// Désormais je peux l'utiliser comme une fonction
doStuff()
```

## ARROW FUNCTION

Ce que je trouve cool chez les *arrow functions*:
 - plus concises (elles expriment beaucoup de choses en peu de mot )
 - return implicite (en une ligne)
 - on peut jouer avec le ```this```

### Return implicite

```js
// Avant ES6
function add(a, b){
  return a + b
}
// Depuis ES6
const add = (a, b) => a + b
```

### Plus concises
```js
const names = ['Barbès','Raspail', 'Haussman', 'Voltaire'];
// Elles renvoient toutes :
//[ 'Boulevard Barbès',
//  'Boulevard Raspail',
//  'Boulevard Haussman',
//  'Boulevard Voltaire' ]
  const fullNames = names.map(function(name) {
    return `Boulevard ${name}`
  })
  
  const fullNames2 = names.map((name) => {
    return `Boulevard ${name}`
  })

  const fullNames3 = names.map(name => {
    return `Boulevard ${name}`
  })

  const fullNames4 = names.map(name => `Boulevard ${name}`)

```

## This

Le ```this``` des *arrow functions* n'est pas le même que celui des *functions*. En effet, dans une *arrow function*, ```this``` est égal à la valeur de ```this``` dans le contexte précédent. Cela signifie que contrairement aux *functions*, les *arrow functions* ne crééent pas de nouveau ```this```.

Sans *arrow functions* :
```js
function myFunc() {
  this.myVar = 0;
  let self = this; // self = this trick
  setTimeout(
    function() { // Un nouveau *this* dans ce scope / contexte / bloc.
      self.myVar++;
      console.log(self.myVar) // 1

      console.log(this.myVar) // undefined
    },
    0
  );
}
```

Avec *arrow functions* :
```js
function myFunc() {
  this.myVar = 0;
  setTimeout(
    () => { // this est celui du scope précédent, c'est à dire celui de myFunc
      this.myVar++;
      console.log(this.myVar) // 1
    },
    0
  );
}
```

### Exemple avec HTML + CSS + JS
```css
    .wrap {
      min-height: 100vh;
      display:flex;
      justify-content: center;
      align-items: center;
      font-family: sans-serif;
      font-weight: 100;
      color:white;
    }

    .box {
      background:black url(https://unsplash.it/1500/1500?image=560&blur=0.5) center fixed no-repeat;
      width:50px;
      height:50px;
      padding:50px;
      transition: width 0.2s, height 0.6s;
      position: relative;
    }

    .box.opening {
      width:500px;
      height:500px;
    }

    .box h2 {
      position: absolute;
      width:100%;
      font-size: 100px;
      transform:translateX(-200%);
      transition: all 0.5s;
      top:0;
    }

    .box p {
      position: absolute;
      width:100%;
      transform:translateX(200%);
      transition: all 0.5s;
      bottom:0;
    }

    .box.open > * {
      transform:translateX(0%);
    }
```

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>Arrow Functions Exemples</title>
  <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>

  <div class="wrap">
    <div class="box">
      <h2>Amir</h2>
      <p class="social">@mir</p>
    </div>
  </div>

</body>
<script type="text/javascript" src="app.js"></script>
</html>
```

On voit bien ici que le `this` n'est pas le même quand on l'appelle depuis une `function` ou depuis une `arrow function` :
```js
const box = document.querySelector('.box')

box.addEventListener('click', () => console.log(this)) // Window

box.addEventListener('click', function() {
  console.log(this) // div class="bloc"
})

```

Maintenant pour obtenir ce résultat quand je clique sur la div : ![resultat HTML + CSS + JS](example.gif)

Avant ES6 :
```js
 box.addEventListener('click', function() {
      var self = this
      var first = 'opening'
      var second = 'open'

      if (this.classList.contains(first)) {
        var tmp = first
        first = second
        second =  tmp
      }

      this.classList.toggle(first) // supprime s'il y a, ajoute s'il n'y a pas
      setTimeout(function() {
        self.classList.toggle(second)
      })
    })
```

Depuis ES6 :
```js
box.addEventListener('click', function() {
      let first = 'opening'
      let second = 'open'

      if (this.classList.contains(first)) {
        [first, second] = [second, first] // destructuration
      }

      this.classList.toggle(first)
      setTimeout(() => this.classList.toggle(second))
    })
```

>En réalité, ici je peux remplacer le `this` par box et du coup, utiliser une arrow function.

## **Quand est-ce qu'il ne faut pas utiliser les** *arrow functions* **?**

### 1. Quand tu as besoin d'ajouter le prototype d'une fonction à une classe 
```js
class Car {
    constructor(marque, couleur) { 
      this.marque = marque
      this.couleur = couleur
    }
}
const merco = new Car('Mercedes', 'Grey')
const lambo = new Car('Lambo', 'White')
```

Pour ajouter une fonction à une classe :
```js
// celà fonctionnera même si on a déjà instancié deux nouveaux objets
class_name.prototype.method_name = function(argument){
  //...code
}
```

Ici, le ```this``` nous contraint de ne pas utiliser une *arrow function* :
```js
Car.prototype.description = () => {
 console.log(this.marque + ' ' + this.couleur) 
}
merco.description() // undefined undefined
lambo.description() // undefined undefined
```

Avec *arrow functions* :
```js
Car.prototype.description = function(){
 console.log(this.marque + ' ' + this.couleur) 
}
merco.description() // Mercedes Grey
lambo.description() // Lambo White
```

### 2. Quand tu as besoin du mot clé [`arguments`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Fonctions/arguments)

Sans *arrow functions* :
```js
function childrenToArray (){
  const children = Array.from(arguments)
  // const children = [...arguments] // donne le même résultat
  console.log(children)
}
childrenToArray('laurent', 'anojh') // [ 'laurent', 'anojh' ]
```

Avec les *arrow functions*, tu ne peux pas uitiliser le mot clé ``arguments`` :
```js
const childrenToArray = () => {
  const children = Array.from(arguments)
  // const children = [...arguments] // same result
  console.log(children)
}
childrenToArray('laurent', 'anojh') //  ReferenceError: arguments is not defined
```

Toutefois, tu peux utiliser le ``rest param`` (que l'on verra plus tard) :
```js
const childrenToArray = (...children) => console.log(children)

childrenToArray('laurent', 'anojh') // [ 'laurent', 'anojh' ]
```

### 3. Bind une méthode à un objet (question de goût)
```js
const person = {
  points: 23,
  score() {
    console.log(this) // { points: 23, score: [Function: score] }
    this.points++;
  }
}
person.score()
console.log(person.points) // 24
```

Il faudrait faire comme ça avec  une *arrow function* :
```js
const person = {
  points: 23,
  score : () => {
    person.points++
  }
}
person.score()
console.log(person.points) // 24
```

car sinon :
```js
const person = {
  points: 23,
  score : () => {
    console.log(this) // {}
    this.points++
  }
}
person.score()
console.log(person.points) // 23
```

# **CONCLUSION**

J'utilise les *arrow functions* par défaut, et le mot clé *function* que dans les cas où je dois contraint d'utiliser ```this```.

>**P.S:** ne cherchez pas à faire absolument toutes vos fonctions en une ligne car comme dirait ce [monsieur](https://medium.com/@danieldng/be-boring-55653537fc99) : *"En réalité, l'ingénierie logicielle ne consiste pas à écrire du code intelligent. Il s'agit d'écrire du code maintenable, **lisible** et ennuyeux."*
