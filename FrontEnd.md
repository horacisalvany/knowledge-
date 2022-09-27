# Typescript

## How to debug a component
```
  constructor() {    
    window['c'] = this;
  }
```

## Generic Types
https://www.typescriptlang.org/docs/handbook/2/generics.html#working-with-generic-type-variables
Playing arround -> https://playcode.io/931122/

#### Funcio generica:
Important: ho es quan afegim **<Pepe> angle bracket ** a la signatura
```
function identity<Type>(arg: Type): Type {
  return arg;
}
```
#### Generic type parameter	
Important: podem posar el nom que vulguem, lo important es l'ordre dels parametres
```
let myIdentity: <Jose>(arg: Jose) => Jose = identity;
console.log(myIdentity); // output: ƒ identity()
```
Podem reescriure-ho amb amb la signatura del type
` let myIdentity: { <Type>(arg: Type): Type } = identity; `
	
#### Generic interface	
```
interface GenericIdentityFn<Type> {
  (arg: Type): Type;
}

let myIdentity3: GenericIdentityFn<number> = identity;
console.log(myIdentity3('z')); // output: z
```
#### Generic classes
```
class GenericNumber<Pepe> {
  zeroValue: Pepe;
  add: (x: Pepe, y: Pepe) => Pepe;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function (x, y) {
  return x + y;
};

console.log(myGenericNumber.add(1, 2)); // output: 3

let stringNumeric = new GenericNumber<string>();
stringNumeric.zeroValue = 'sub_';
stringNumeric.add = function (x, y) {
  return x + y;
};

console.log(stringNumeric.add(stringNumeric.zeroValue, 'test')); // output: sub_test
```	
	

### Lambda functions
Difference between one line return or block
`(x) => x     // Equivalent to (x) => {return x}`
```	
colores.map((color) => {  
    color = 'verde'
    return color = color + '1'
}) // Returns: ['verde1', 'verde1']
```

# Scss
Alinear el contingut d'un element i els fills. Dos maneres:
1. Desde el pare: 
	```
	text-align: left;
  	display: inline-block;
	```
2. Desde el fill:
	```
	margin-left: 0;
	margin-right: auto;
	```
