# MySql - Base de datos de una tienda de venta de Ropa

create schema TiendaDeRopa;

create table TiendaDeRopa.Talla (
Id_ventas int not null auto_increment,
Nombre_talla varchar(10) null,
primary key (Id_ventas));

create table TiendaDeRopa.Marca(
Id_marca int not null auto_increment,
nombre_marca varchar(20),
primary key (Id_marca));

create table TiendaDeRopa.Departamento(
Id_departamento int not null auto_increment,
nombre_depart varchar(50),
primary key (Id_departamento));

create table TiendaDeRopa.Proveedor(
Id_proveedor int not null auto_increment,
nombre_proveedor varchar(30),
primary key (Id_proveedor));

create table TiendaDeRopa.Cliente(
Id_cliente int not null auto_increment,
nombre_cliente varchar(50),
direccion_cliente varchar(60),
telefono int,
primary key (Id_cliente));

create table TiendaDeRopa.Prenda(
Id_prenda int not null auto_increment,
Id_marca int references Marca,
Id_talla int references Talla,
Id_depa int references Departamento,
Id_proveedor int not null references Proveedor,
precio int,
primary key (Id_prenda));

create table TiendaDeRopa.Ventas(
Id_venta int not null auto_increment,
Id_prenda int references Prenda, 
Id_cliente int references Cliente,
vendido int,
fecha timestamp,
primary key (Id_venta));

select * from Talla;

Insert into Talla ( Id_ventas, Nombre_talla)
values ('1', 'Small'),
('2','Medium'),
('3', 'Large'),
('4', 'XL');

select * from Cliente;

Insert into Cliente (Id_cliente, nombre_cliente, direccion_cliente, telefono)
values
('1', 'Angela Romero', 'Zona 5', '56849756'),
('2', 'Juan Perez', 'zona 9', '32569854'),
('3', 'Andres Mejia', 'zona 7', '25698547'),
('4', 'Gabriela Silva', 'zona 3', '56248697'),
('5',' Victoria Galindo', ' zona 5','23658964'),
('6','Diego Samayoa', 'Zona 7', '35687458'),
('7', 'Daniela Orantes', 'zona 9', '56478694'),
('8', 'Susana Garcia', 'zona 1','32548968');

select * from Marca;

insert into Marca ( Id_marca, nombre_marca)
values
('1', 'Zara'),
('2', 'Totto'),
('3', 'Forever 21'),
('4', 'Tommy'),
('5', 'Patito');

Select * from Proveedor;

insert into Proveedor (Id_proveedor, nombre_proveedor)
values
('1', 'Productos S.A'),
('2', 'Shopofy'),
('3', 'Kalies');

select * from Departamento;

insert into Departamento (Id_departamento, nombre_depart)
values
('1', 'Compras'),
('2', 'Logistica');

select * from Prenda;

insert into Prenda ( Id_prenda, Id_marca, Id_talla, Id_depa, Id_proveedor, precio)
values
('1', '1','1','1','1', 250),
('2','1','2','1','1',175),
('3','2','3','2','3', 300),
('4','5','5','1','2', 150),
('5','3','1','2','4', 175);


select * from Ventas;

insert into Ventas (Id_venta, Id_prenda, Id_cliente, vendido, fecha)
values
('1', '2','5', '5','2020-05-20 00:00:00'),
('2', '4','3', '10','2020-02-01 00:00:00'),
('3','3','6','3','2020-05-20 00:00:00'),
('4','5','8','20', '2021-04-03 00:00:00'),
('5','3','3','2','2021-03-25 00:00:00'),
('6','1','3','4','2022-06-30 00:00:00'),
('7','2','4','7','2022-06-30 00:00:00');

/*Mostrar la cantidad de productos vendidos en una fecha especifica */
Select fecha as Fecha,
sum(vendido) as Productos_vendidos
from Ventas
where Ventas.fecha = '2022-06-30'
group by fecha;

/* Se agrega la columna existencias a la tabla Prenda */
ALTER TABLE TiendaDeRopa.Prenda 
ADD COLUMN existencias INT NULL DEFAULT NULL AFTER precio;

/*Se agregan datos a la columna existencias de la tabla prenda */
UPDATE TiendaDeRopa.Prenda SET existencias = '50' WHERE (Id_prenda = '1');
UPDATE TiendaDeRopa.Prenda SET existencias = '30' WHERE (Id_prenda = '2');
UPDATE TiendaDeRopa.Prenda SET existencias = '70' WHERE (Id_prenda = '3');
UPDATE TiendaDeRopa.Prenda SET existencias = '100' WHERE (Id_prenda = '4');
UPDATE TiendaDeRopa.Prenda SET existencias = '40' WHERE (Id_prenda = '5');

/* suma de las existencias de las prendas por cada proveedor segun las fechas anteriores incluyendo la fecha colocada*/
Select nombre_proveedor as Proveedor,
sum(existencias) as Compras
from Prenda Pr, Proveedor Pro, Ventas
where Pro.Id_proveedor = Pr.Id_proveedor and Ventas.fecha <='2022-06-30'
group by Pro.Id_proveedor, Pr.Id_proveedor, nombre_proveedor, fecha, existencias;

/* Mostrar los articulos comprados por el cliente antes de la fecha mencionada*/
select nombre_cliente as Cliente, vendido as Articulos_Comprados, fecha as Fecha
from Ventas, Cliente
where Ventas.Id_cliente = Cliente.Id_cliente and Ventas.fecha <'2022-06-30';

/* Mostrar las existencias por la marca que tenga de 70 articulos para abajo*/
select nombre_marca as Nombre_de_Marca, existencias as Existencias_menores_a_70
from Marca, Prenda
where Prenda.existencias<=70 and Marca.Id_marca = Prenda.Id_marca;

select nombre_cliente as Nombre_del_cliente, vendido as Articulos_comprados, fecha as Fecha_de_compra
from Cliente, Ventas
where Cliente.Id_cliente = Ventas.Id_cliente and Ventas.fecha <='2021-04-03';
