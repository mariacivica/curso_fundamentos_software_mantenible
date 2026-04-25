# 02 Programación Orientada a Objetos (POO)
Es la búsqueda del bajo acoplamiento y la alta cohesión en todo el código.
Conceptos estándar:
 - Abstracción
 - Encapsulación
 - Herencia
 - Polimorfismo

#### Abstracción
Consiste en ocultar detalles de implementación y quedarnos con un interfaz más simple.
La idea es que el código exprese **qué** queremos hacer, **no cómo** se hace internamente.

Ejemplo:
Estamos en la capa del controlador que recibe una petición HTTP. Ahí no interesa ver validaciones concretas SQL o detalles técnicos de implementación.

En capas como la del controlador, interesa aplicar abstracción para que no aparezca el detalle técnico de implementación.

En lugar de tener en el controlador todo el proceso paso a paso, extraemos esos detalles a funciones o métodos que encapsulan esa lógica. Así queda más limpio, legible y centrado en su responsabilidad.

Código sin abstracción:
```
const username = "javiercane";
const email = "javi@example.com";
const password = "J4vl3r";

if (username.length < 3 || password.length < 8) {
console.error("Invalid username or password");
}

sql`
INSERT INTO users (username, email, password)
VALUES (${username}, ${email}, ${password})
`;
```
Código con abstracción:
```
const username = "javiercane";
const email = "javi@example.com";
const password = "J4vl3r";
registerUser(username, email, password);
```
#### Encapsulación
Consiste en mover la lógica y las reglas al objeto o clase que mejor representa esa responsabilidad, para que desde fuera no haya que conocer los detalles internos ni repetir validaciones.

Encapsulamos cada responsabilidad en su sitio de forma que las funciones del ejemplo anterior _registerUser()_ desaparecen.

Ejemplo:
- `UserRegister`: coordina el caso de uso de registro de usuario  
Capa de Aplicación
(No debe contener detalles técnicos de infraestructura ni mucha lógica de dominio; solo debe orquestar)
- `User`: encapsula aún más la validación 
Capa de Dominio
(Representa la entidad de negocio y encapsula las reglas propias: validar que el usuario sea correcto)
- `UserRepository`: encapsula la persistencia  
Capa de Infraestructura
(Normalmente su interfaz se declara en aplicación o dominio y su implementación en infraestructura)

`UserRegister`:
```
class UserRegister {

constructor(
readonly repository: UserRepository,
) {}

register(username: string, email: string, password: string) {
const user = new User(username, email, password);
this.repository.save(user);
}}
```
`User`:
```
class User {

constructor(
public username: string,
public email: string,
public password: string,
) {

if (!this.isValid(username, email, password)) {
throw new Error("Invalid user");
}}

private isValid(username: string, email: string, password: string): boolean {
return username.length > 3
&& email.includes("@")
&& password.length > 8;
}}
```
`UserRepository`:
```
class UserRepository {

save(user: User): void {
sql`
INSERT INTO users (username, email, password)
VALUES (${user.username}, ${user.email}, ${user.password})
`;
}}
```
#### Herencia
Consiste en crear clases específicas a partir de una clase más general, reutilizando comportamiento común y especializando lo necesario.


Ejemplo:
- `CodelyError`: define una estructura común para los errores y obliga a que cada error concreto implemente _message()._
- `UserNotExistsError`: concreta el error para el caso en que un usuario no existe.

`CodelyError`:
```
export abstract class CodelyError extends Error {

constructor(
readonly params: Record<string, unknown> = {},
) {
super();
}

abstract message(): string;
}
```
`UserNotExistsError`:
```
export class UserNotExistError extends CodelyError {

constructor(readonly id: string) {
super({ id });
}

message(): string {
return `The user ${this.id} does not exist`;
}}
```	

#### Polimorfismo
Consiste en poder tratar distintos objetos a través de un mismo tipo común, confiando en que todos comparten una misma interfaz o contrato.

Eso permite que el código cliente no necesite conocer la implementación concreta con la que está trabajando, sino solo el comportamiento que ese contrato garantiza.

Ejemplo (polimorfismo con errores):
Si capturamos un `CodelyError`, podemos llamar a _message()_ sin importar cuál sea el error concreto, porque todas sus clases hijas implementan ese método.
```
try {
const finder = new UserFinder();
return finder.find(userId);
} 
catch (error: CodelyError) {
console.log(error.message());
}
```
Ejemplo (polimorfismo con repositorios):
Si `UserFinder` depende de `UserRepository`, da igual que le pasemos un `MySqlUserRepository` o un `PostgresUserRepository`.  
Ambos respetan el mismo contrato, así que el caso de uso no necesita cambiar.
```
try {
const repository = new MySqlUserRepository();
const finder = new UserFinder(repository);
return finder.find(userId);
} 
catch (error: CodelyError) {
console.log(error.message());
}
```
Si mañana se cambia a Postgres, `UserFinder` no cambia:
```
try {
const repository = new PostgresUserRepository();
const finder = new UserFinder(repository);
return finder.find(userId);
}
catch (error: CodelyError) {
console.log(error.message());
}
```
Relación con SOLID:
Esto se apoya en inversión de dependencias: la clase de aplicación depende de una abstracción (`UserRepository`) y no de una implementación concreta.

## Qué es un objeto
En Programación Orientada a Objetos, un objeto es algo que tiene por un lado datos y por otro lado comportamiento.

La clase no es el objeto en sí; el objeto es el resultado de aplicar el constructor de una clase.  
Es decir, cuando hacemos _new User(...)_, lo que obtenemos es una instancia, es decir, un objeto.

En este paradigma se busca que los datos y la lógica que trabaja sobre esos datos estén juntos.  

Por eso se entiende que un objeto debe agrupar:
 - estado: sus datos o propiedades
 - comportamiento: los métodos que operan sobre ese estado
 
En POO se busca **alta cohesión**, es decir, que el comportamiento esté lo más cerca posible de los datos a los que hace referencia.

Por ejemplo, en una clase `User`:
 - los datos pueden ser _username_, _email_ y _password_
- y el comportamiento puede ser _usernameFormatted()_ o _isValid()_

La idea es que, si una operación pertenece al usuario, lo natural es que viva dentro de `User` y no en otra clase o función externa.

Eso evita:
 - dispersar la lógica,
 - depender de getters de otras clases,
 - y tener que llamar a métodos lejanos para operar con esos datos.

Comparación con un objeto plano: una función también puede devolver un objeto, pero no siempre estaremos trabajando en el mismo sentido que en POO.

Ejemplo:
Una función como _newUser()_ puede devolver un objeto con datos, pero ese objeto no pertenece a una clase ni encapsula comportamiento de la misma forma.

En cambio, una clase como `User` sí permite agrupar:

 - datos
 - validación
 - formato
 - y demás lógica relacionada

### Acoplamiento
_xxx_


---
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIxMjk2NjAwMzYsLTE4MTM4NjAxNDEsLT
E3ODE0ODk5NSwtODU3NzA4OTA5XX0=
-->