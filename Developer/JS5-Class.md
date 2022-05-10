# Class
#javascript
https://www.youtube.com/watch?v=2ZphE5HcQPQ&t=125


```js
class Rectangle {
	// constructor, rendered once when object is created, to setup the object
  constructor (_width, _height, _color) {
  	console.log("The Rectangle is being created!");
    
    this.width = _width;
    this.height = _height;
    this.color = _color;
  }
  
  getArea () {
  	return this.width * this.height;
  }
  
  printDescription () {
  	console.log(`I am a rectangle of ${this.width} x ${this.height} and I am ${this.color} `);
  }
}

// create new object
let myRectangle1 = new Rectangle(5, 3, "blue");
let myRectangle2 = new Rectangle(8, 5, "red")

console.log(myRectangle1.getArea());
console.log(myRectangle2.getArea());

myRectangle1.printDescription();
```


## Getters dan Setters
```js
class Square {
	constructor (_width) {
  	this.width = _width;
    this.height = _width;
    this.numOfRequestsForArea = 0
  }
  
  // getters
  get area () {
  	this.numOfRequestsForArea++;
  	return this.width * this.height;
  }
  
  // setters
  set area (area) {
  	this.width = Math.sqrt(area);
    this.height = this.width;
  }
}

let square1 = new Square(4);

// no parenthesis, area() because it is a property
console.log(square1.area);

square1.area = 25;
console.log(square1.width);
console.log(square1.height);

console.log(square1.area);
console.log(square1.area);
console.log(square1.area);
console.log(square1.area);

console.log(square1.numOfRequestsForArea);
```


## Helper Function or Static Function
```js
class Square {
	constructor (_width) {
  	this.width = _width;
    this.height = _width;
  }
  
  // static method or helper function
  static equals (a, b) {
  	return a.width * a.height === b.width * b.height;
  }
  
  static isValidDimensions (width, height) {
  	return width === height;
  }
}

let square1 = new Square(8);
let square2 = new Square(9);

// call the equals static methods
console.log(Square.equals(square1, square2));

console.log(Square.isValidDimensions(6, 6));
console.log(Square.isValidDimensions(6, 9));
```

## Inheritance
```js
class Person {
	constructor (_name, _age) {
  	this.name = _name;
    this.age = _age;
  }
  
  describe () {
  	console.log(`I am ${this.name} and I am ${this.age} years old`)
  }
}

// child class
class Programmer extends Person {
	constructor (_name, _age, _yearsOfExperience) {
  	// inheritance
    super(_name, _age);
    
    // custom behaviour
    this.yearsOfExperience = _yearsOfExperience;
  }
}

let person1 = new Person("Jeff", 45);
let programmer1 = new Programmer ("Dom", 56, 12)

console.log(person1);
console.log(programmer1);
```

Output
```js
-   { age: 45, name: "Jeff" }
-   { age: 56, name: "Dom", yearsOfExperience: 12 }
```

Dengan class yang sama, namun dengan input berupa array.
```js
const programmers = [
  new Programmer("Dom", 56, 12),
  new Programmer("John", 28, 5),
  new Programmer("Doe", 35, 8)
];

function developSoftware (programmers) {
	for ( let programmer of programmers ) {
  	console.log(programmer);
    programmer.code();
  }
}

developSoftware(programmers);
```
Output
```js
-   { age: 56, name: "Dom", yearsOfExperience: 12 }
-   "Dom is coding"
-   { age: 28, name: "John", yearsOfExperience: 5 }
-   "John is coding"
-   { age: 35, name: "Doe", yearsOfExperience: 8 }
-   "Doe is coding"
```

## Polymorphism
```js
class Person {
	constructor (_name) {
  	this.name = _name;
  }
  
  capability () {
  	console.log(`${this.name} can breath`);
  }
  
  location () {
  	console.log("location at home");
  }
}

// child class
class Programmer extends Person {
	constructor (_name) {
    super(_name);
  }
  
  capability () {
  	super.capability();
  	console.log( `${this.name} can code` );
  }
  
  // polymorphism
  location () {
  	console.log("location at office");
  }
}

let programmer = new Programmer("Andi");

programmer.location();
programmer.capability();
```

Output
```js
-   "location at office"
-   "Andi can breath"
-   "Andi can code"
```

## Try It...
```html
<ul id="myList">
    <!-- To be filled with JavaScript -->
    <li>Item 1</li>
    <li>Item 2</li>
  </ul>

  <script src="ListBinding.js"></script>

  <script type="text/javascript">
    const myList = document.getElementById("myList");
    const listBinding = new ListBinding(myList);
  </script>
```

ListBinding.js
```js
class ListBinding {
  constructor (element) {
    this.listElement = element;
    this.textList = ["dcode", "bottle"];
  }

  /* Makes an <li>text</li> element/tag */
  static createListItem (text) {
    const li = document.createElement("li");

    li.textContent = text;

    return li;
  }

  update () {
    /* remove all existing <li> elements/tags */
    while (this.listElement.firstChild) {
      this.listElement.removeChild(this.listElement.firstChild);
    }

    /* Fill <ul>/<ol> tag with <li> */
    for (const text of this.textList) {
      this.listElement.appendChild(ListBinding.createListItem(text));
    }
  }

  add (text) {
    this.textList.push(text);
    this.update();
  }

  remove (index) {
    this.textList.splice(index, 1);
    this.update();
  }
}
```

Adding item to the list.
```js
listBinding.add("abcdef")

```

