# ReactJS
#react #javascript 

Link: https://www.javatpoint.com/reactjs-tutorial

## ReactJS
- Mempercepat proses developmen dari aplikasi.
- Menggunakan JavaScript virtual DOM
- Kebanyakan website saat ini dibuat dengan MVC (Model View Controller), React adalah V dari MVC.
- Terdiri dari banyak components, tiap component bertanggungjawab untuk mengeluarkan HTML code kecil yang dapat digunakan berulang-ulang.

## Penggunaan React
Instalasi React
```sh
npx create-react-app app-name
```

## React JSX (JavaScript Extension)
- Ekstensi syntax terhadap JavaScript.
- JSX memproduksi element pada React.
- Tidak diwajibkan untuk digunakan, tapi ini sangat membantu.
- Contoh:
```js
const element = <h1>Hello, world!</h1>;
```

JSX
```js
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>;

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

JSX mencegah serangan injeksi. User input yang terembed JSX bersifat aman.  
Secara default, React DOM melakukan escape kepada value yang terembed pada JSX sebelum melakukan render. Semua terkonversi sebagai string sebelum dirender. Hal ini dapat mencegah serangan XSS.
```js
const title = response.potentiallyMaliciousInput;
// This is safe:
const element = <h1>{title}</h1>;
```

## Render Element
- Berbeda dengan DOM pada browser, React elements bersifat plain dan mudah untuk dibuat. React DOM berfungsi mengupdate DOM sesuai dengan React elements.

Contoh:
```html
<!-- index.html -->

<div id="root"></div>
```

Pada "root" DOM node tersebut akan dimanage oleh React DOM. Untuk melakukan render, maka gunakan `reactDOM.render()`.

```js
const element = <h1>Hello, world</h1>;
ReactDOM.render(
	element,
	document.getElementById('root')
);
```

React element bersifat immutable, artinya setelah element dibuat, kamu tidak bisa mengganti children atau atributnya. (Element melambangkan UI pada satu waktu, anggap saja seperti satu frame pada movie). Artinya, cara untuk mengupdate UI satu-satunya adalah membuat element baru dan diberikan kepada `ReactDOM.render()`. Code di bawah ini memanggil `ReactDOM.render()` tiap detik melalui callback `setInterval()`.

```js
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(element, document.getElementById('root'));}

setInterval(tick, 1000);
```

React DOM membandingkan elemen dan children terhadap kondisi sebelumnya. React DOM melakukan update seperlunya untuk memastikan DOM berada pada kondisi yang diharapkan.

## Components dan Props(Properties)
- Components memperbolehkan pemisahan UI, dan membuat part-partnya reusable.
- Component berbentuk seperti function, component dapat menerima input bernama props, dan menghasilkan React Element yang akan ditampilkan pada layar.

```js
function Welcome(props) {
	return <h1>Hello, {props.name}</h1>
}

const element = <Welcome name="Sara" />;ReactDOM.render(
  element,
  document.getElementById('root')
);
```
Selalu gunakan huruf kapital untuk mendefinisikan components. React menganggap awalah huruf kecil sebagai DOM tag, contohnya div yang merepresentasikan HTML tag. Namun `<Welcome />` merepresentasikan component.  

### Composing Components
Component dapat mengacu kepada component lain pada output. Tombol, formulir, dialog, dan screen pada React biasa diekspresikan sebagai component.  
https://codepen.io/gaearon/pen/KgQKPr?editors=1010
```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
		<Welcome name="Sara" />
		<Welcome name="Cahal" />
		<Welcome name="Edite" />    
	</div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

### Extracting Components
Membagi component menjadi component yang lebih kecil.
```js
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```
Code di atas mengambil author, text, dan date sebagai props, kemudian memasukkan informasi tersebut ke dalam comment pada media sosial. Sangat direkomendasikan untuk menamai prop dari sudut pandang component, daripada konteks penggunaan.

Simplifikasi pertama. 
```js
function Avatar(props) {
  return (
    <img className="Avatar"      
		src={props.user.avatarUrl}      
		alt={props.user.name}    
	/>
  );
}
```
```js
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Avatar user={props.author} />  // penggunaan component Avatar
		<div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

Simplifikasi kedua. https://codepen.io/gaearon/pen/MjQWQO?editors=1010
```js
function UserInfo(props) {
  return (
    <div className="UserInfo">
		<Avatar user={props.user} /> // penggunaan component Avatar
		<div className="UserInfo-name">       
			{props.user.name}      
		</div>    
	</div>  
  );
}
```

```js
function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} /> // penggunaan component UserInfo
	  <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```
Membagi component membuat component yang sifatnya dapat digunakan kembali. Apabila bagian pada UI digunakan berkali-kali atau kompleks, ada baiknya untuk membagi bagian tersebut menjadi lebih kecil.

### Props Bersifat Read-Only
Apabila mendeklarasi sebuah component, component tersebut tidak diperbolehkan untuk mengubah props nya sendiri.
Contoh:
```js
function sum(a, b) {
	return a + b;
}
```

Contoh buruk karena mengubah input `amount`
```js
function withdraw(account, amount) {
	account.total -= amount;
}
```
Apabila terdapat proses dinamis pada React, lebih baik menggunakan State.

## State & Hooks
Awalnya State hanya bisa diakses melalui class components, React versi 16.8 dan setelahnya memperbolehkanmu untuk menggunakan state tanpa menuliskan class.  

Hook adalah fungsi yang memperbolehkanmu untuk masuk ke React state dan lifecycle melalui fungsi component. Hook tidak dapat bekerja di dalam class.  
  
Hook digunakan dengan perintah `useState` dan dipanggil pada function component untuk menambahkan local state. React akan menjaga state ini diantara render. `useState` mengembalikan pair (current state dan fungsi untuk mengupdate).
```js
import React, { useState } from 'react';

function Example() {
  // Declare a new state variable, which we'll call "count"  
  const [count, setCount] = useState(0); // dimulai dari 0
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

Deklarasi variabel state lebih dari satu di dalam sebuah component.
```js
function ExampleWithManyStates() {
  // Declare multiple state variables!
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
  // ...
}
```

### Effect Hook
Untuk melakukan "side effect": data fetching, subscriptions, dan secara manual mengubah DOM. `useEffect` menambahkan kemampuan yang sama dengan `componentDidMount`, `componentDidUpdate` dan `componentWillUnmount` pada class, namun dengan sebuah API.

```js
import React, { useState, useEffect } from 'react';
function Example() {
  const [count, setCount] = useState(0);

  // Similar to componentDidMount and componentDidUpdate:  
  useEffect(() => {    
    // Update the document title using the browser API    
    document.title = `You clicked ${count} times`;  
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

