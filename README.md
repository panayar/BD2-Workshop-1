# TALLER 1 BD 🗄️

> Status : Finished 

## Diagrama Relacional 👍

https://lucid.app/lucidchart/e761f93f-5de6-4371-8960-5493da2203dd/edit?invitationId=inv_348e8025-6a6c-4bf0-a55d-fc39744f3e7a


## Diccionario de datos 📖
https://docs.google.com/spreadsheets/d/1b98DMIE5QHRYJVl-72tYQ-ga9yJWSORG/edit?usp=sharing&ouid=109857548034765835556&rtpof=true&sd=true


## Vistas 🔎

```CREATE VIEW total_ventas_ano AS 
SELECT SUM(monto_final) FROM Comprador AS c 
INNER JOIN Compra AS co ON c.comprador_id = co.comprador_id 
WHERE co.fecha BETWEEN TO_DATE('2022/01/01','YYYY/MM/DD') 
AND NOW()::TIMESTAMP::DATE; 


CREATE VIEW info_proveedores_activos AS 
SELECT u.nombre , u.apellido, u.usuario_id 
FROM Usuario AS u 
INNER JOIN Proveedor AS p ON u.usuario_id = p.usuario_id 
WHERE p.estado = 'activo';


CREATE VIEW productos_marca AS 
SELECT p.nombre, p.precio, p.descripcion, p.stock FROM Producto AS p
INNER JOIN Marca AS m ON p.marca_id = m.marca_id WHERE m.nombre = 'apple';
```

## Preguntas ❓

**a. ¿Las vistas que decide creara qué requerimiento no funcional obedecen? Seguridad o facilidad de consulta.¿Deberían ser vistas materializadas?**

R=/ Las tres vistas que creamos se usaran con la finalidad de facilitar una consulta ya que si miramos la query nos traerla una información bien detallada pero a la vez muy útil para facilitar algunas tareas de consulta.

En cuanto a las vistas podemos decir que solo una de las tres puede estar materializada ya que la primera se trata del total de ventas por año, lo que significa que nos es necesario que se recargue o se mantenga actualizada a diario. como lo dice su nombre solo se va a ver una vez al año.

la segunda nos habla de los proveedores que están habilitados podemos guardar la información desde el momento que se creo la query ya que si se hace un análisis semanal, puede que hayan proveedores nuevos, o varios se fueran entonces necesitamos saber desde el primer momento.

Por ultimo la tercera que es una query que filtrar los productos es una vista normal debido a que la query se va a ejecutar y solo nos traerá los productos de dicha marca que estén en el origen.

**b. ¿Cuáles consultas a la base de datos, a partir de los requerimientos dados, pueden optimizarse mediante índices? ¿De qué tipos deben ser dichos índices? Argumente.**

R=/ Los índices que creamos tienen el objetivo de facilitar los procesos de búsquedas tanto de los usuarios como del stock y  los índices se crearon de tipo ordenado para buscar la información mas rápida.

## Scripts DDL 📜

```
create table Documento(
    id_documento serial not null ,
    tipo_documento varchar(50),
    estado boolean,
    primary key (id_documento)
);
create table Rol(
    id_rol serial not null ,
    descripcion varchar(50),
    estado boolean,
    primary key (id_rol)
);
create table Usuario(
    usuario_id serial not null ,
    rol_id int not null ,
    doc_id int not null ,
    username varchar(70),
    num_doc int,
    nombres varchar (100),
    apellidos varchar(100),
    telefono bigint,
    correo varchar(100),
    clave varchar(256),
    estado boolean,
    primary key (usuario_id),
    foreign key (rol_id) references Rol (id_rol),
    foreign key (doc_id) references Documento(id_documento)
);
create table Telefono(
    telefono_id serial not null ,
    id_usuario int not null ,
    numero bigint,
    estado boolean,
    primary key (telefono_id),
    foreign key (id_usuario) references Usuario(usuario_id)

);
create table Direccion(
    direccion_id serial not null ,
    id_usuario int not null ,
    departamento varchar(80),
    ciudad varchar(80),
    barrio varchar(80),
    via varchar(80),
    numero int,
    descripcion varchar(100),
    estado boolean,
    primary key (direccion_id),
    foreign key (id_usuario) references Usuario (usuario_id)
);
create table Marca(
    marca_id serial not null ,
    nombre varchar(80),
    estado boolean,
    primary key (marca_id)
);
create table Administrador(
    administrador_id serial not null ,
    usuario_id int not null ,
    estado boolean,
    primary key (administrador_id),
    foreign key (usuario_id) references Usuario(usuario_id)
);

create table Proveedor (
    proveedor_id serial not null ,
    usuario_id int not null ,
    estado boolean,
    primary key (proveedor_id),
    foreign key (usuario_id) references Usuario(usuario_id)
);
create table Comprador(
    comprador_id serial not null ,
    usuario_id int not null ,
    estado boolean,
    primary key (comprador_id),
    foreign key (usuario_id) references Usuario(usuario_id)
);
create table Carrito(
    carrito_id serial not null ,
    comprador_id int not null ,
    fecha date,
    monto_total float,
    estado boolean,
    primary key (carrito_id),
    foreign key (comprador_id) references Comprador(comprador_id)
);

create table Compra(
    compra_id serial not null ,
    comprador_id int not null ,
    carrito_id int not null ,
    fecha date,
    monto float,
    estado boolean,
    primary key (compra_id),
    foreign key (comprador_id) references Comprador(comprador_id),
    foreign key (carrito_id) references Carrito (carrito_id)
);
create  table Envio(
    envio_id serial not null ,
    compra_id int not null ,
    fecha date,
    estado boolean,
    primary key (envio_id),
    foreign key (compra_id) references Compra (compra_id)
);
create table Estado(
    estado_id serial not null ,
    tipo varchar(50),
    primary key (estado_id)
);
create table Estado_detalle (
    id serial not null ,
    estado_id int not null ,
    envio_id int not null ,
    proveedor_id int not null ,
    fecha date,
    primary key (id),
    foreign key (estado_id) references Estado (estado_id),
    foreign key (envio_id) references Envio (envio_id),
    foreign key (proveedor_id) references  Proveedor (proveedor_id)
);

create table Categoria(
    categoria_id serial not null ,
    nombre varchar(80),
    estado boolean,
    primary key (categoria_id)
);
create table Producto(
    producto_id serial not null ,
    marca_id int not null ,
    proveedor_id int not null ,
    titulo varchar(80),
    variante varchar(80),
    precio float,
    caracteristicas varchar(100),
    descripcion varchar(100),
    stock int ,
    estado boolean,
    primary key (producto_id),
    foreign key (marca_id) references Marca(marca_id),
    foreign key (proveedor_id) references Proveedor(proveedor_id)
);

create table Foto(
    foto_id serial not null ,
    producto_id int not null ,
    archivo bytea,
    descripcion varchar(80),
    estado boolean,
    primary key (foto_id),
    foreign key (producto_id) references Producto(producto_id)

);
create table producto_categoria(
    id serial not null ,
    producto_id int not null ,
    categoria_id int not null ,
    primary key (id),
    foreign key (producto_id)references Producto(producto_id),
    foreign key (categoria_id)references Categoria(categoria_id)
);
create table Compra_detalle(
    producto_compra_id serial not null ,
    producto_id int not null ,
    compra_id int not null ,
    cantidad int,
    precio_unidad float,
    estado boolean,
    primary key (producto_compra_id),
    foreign key (producto_id) references Producto(producto_id),
    foreign key (compra_id) references Compra(compra_id)
);
create table Carrito_detalle(
    carrito_det_id serial not null ,
    carrito_id int not null ,
    producto_id int not null ,
    cantidad int,
    estado boolean,
    primary key (carrito_det_id),
    foreign key (carrito_id) references Carrito (carrito_id),
    foreign key (producto_id) references Producto (producto_id)
);
``` 


## Evidencia de Ejecución 🖼️


![Untitled](https://user-images.githubusercontent.com/71273441/155451893-47641019-8361-4af2-b95d-09e1276cfbc3.png)
![Untitled (1)](https://user-images.githubusercontent.com/71273441/155452064-35ed8b9c-154e-442c-ba6c-332bdbe41e71.png)

## Foto del PGAdmin sin ejecutar todavía el script
![Untitled (2)](https://user-images.githubusercontent.com/71273441/155452115-9e92339a-22c5-4270-bd05-4cd26229fcf3.png)

## En este caso como usamos Datagrip Creamos la conexión con postgres y la database Unbosque que fue la que creamos, como podemos observar el test de conexión fue valido.
![Untitled (3)](https://user-images.githubusercontent.com/71273441/155452156-40321484-0e44-4c1e-b95f-5fc1767a2e4f.png)

## Se crea el Script con las tablas definidas en el modelo y se ejecuta

## Como podemos ver en la consola de data grip se crearon todas las tablas 



## En postgres ya aparecen las 20 tablas que creamos ✅


## Integrantes 👧🏻🦸‍♂️👨‍🚀
* Paula Andrea Anaya Ramirez 
* Luis Esteban Cardenas Cortes
* Cristian David Sanchez Malagon 
