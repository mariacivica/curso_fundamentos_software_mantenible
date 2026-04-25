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

Encapsulamos cada responsabilidad en su sitio de forma que las funciones del ejemplo anterior _registerUser()_ desaparecen:

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

class UserRegister {

constructor(
readonly repository: UserRepository,
) {}

register(username: string, email: string, password: string) {
const user = new User(username, email, password);
this.repository.save(user);
}
}

#### Herencia
_xxx_

#### Polimorfismo
_xxx_

## Qué es un objeto
_xxx_

### Acoplamiento
_xxx_


---
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3ODM5MTg1NDcsLTE4MTM4NjAxNDEsLT
E3ODE0ODk5NSwtODU3NzA4OTA5XX0=
-->