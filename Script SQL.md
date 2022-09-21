create database ecommerce;
use ecommerce;

create table clients(
	idClient int auto_increment primary key,
    Fname varchar(20),
    Minit char(3),
    Lname varchar(20),
    Address varchar(200),
    CPF char(11) not null,
    constraint unique_cpf_client unique (CPF),
    Bday date
);
alter table clients auto_increment = 1;

create table product(
	idProduct int auto_increment primary key,
    Pname varchar(250) not null,
    SKU varchar(20),
    Category varchar(20),
    pDescription varchar(200),
    pValue varchar(20),
    Note float
);

alter table product modify column Pname varchar(250) not null;

create table payment(
	idClient int,
    idPayment int,
    typePayment enum("Boleto", "Cartão Digital"),
    limitAvaliable float,
    primary key (idClient, idPayment)
);

create table request(
	idRequest int auto_increment primary key,
	idRequestClient int,
    rStatus enum("Em andamento", "Processando", "Enviado", "Entregue") default "Em andamento",
    rDescription varchar(200),
    rShipping float default 10,
    paymentCash bool default false,
    constraint fk_request_client foreign key (idRequestClient) references clients(idClient)
);

create table pStorage(
	idpStorage int auto_increment primary key,
    psSKU varchar(20),
    psLocation varchar(50),
    psQuantity int default 0
);

create table supplier(
	idSupplier int auto_increment primary key,
    SocialName varchar(200) not null,
    CNPJ char(14) not null,
    sAddress varchar(200) not null,
    sContact varchar(20) not null,
    constraint unique_supp unique (CNPJ)
);

create table seller(
	idSeller int auto_increment primary key,
    sSocialName varchar(200) not null,
    sName varchar(200),
    CNPJ char(14) not null,
    CPF char(11) not null,
    sLAddress varchar(200) not null,
    sContact varchar(20) not null,
    constraint unique_cnpj_seller unique (CNPJ),
	constraint unique_cpf_seller unique (CPF)
);

create table pSeller(
	idpSeller int,
    idProduct int,
    pQuantity int default 1,
	primary key (idpSeller, idProduct),
	constraint fk_product_seller foreign key (idpSeller) references seller(idSeller),
	constraint fk_product_product foreign key (idProduct) references product(idProduct)
);

create table pRequest(
	idprProduct int,
	idprRequest int,
    prQuantity int default 1,
	prStatus enum("Disponível", "Sem estoque") default "Disponível",
	primary key (idprProduct, idprRequest),
    constraint fk_prequest_seller foreign key (idprProduct) references product(idProduct),
	constraint fk_prequest_product foreign key (idprRequest) references request(idRequest)
);

create table storageLocation(
	idLproduct int,
	idLstorage int,
	location varchar(200) not null,
	primary key (idLproduct, idLstorage),
	constraint fk_storage_location_product foreign key (idLproduct) references product(idProduct),
	constraint fk_storage_location_storage foreign key (idLstorage) references pStorage(idpStorage)
);

create table pSupplier(
	idpSupplier int,
    idpsppProduct int,
    psppQuantity int default 1,
	primary key (idpSupplier, idpsppProduct),
	constraint fk_product_supp_supplier foreign key (idpSupplier) references supplier(idsupplier),
	constraint fk_product_supp_product foreign key (idpsppProduct) references product(idProduct)
);

use information_schema;
desc referential_constraints;
select * from referential_constraints where constraint_schema ="Ecommerce";

insert into clients (Fname, Minit, Lname, CPF, Address)
	values ("Lucas", "M", "Roberto", 123456789, "Rua do Limoeiro 20, Centro - Jardim das Flores"),
		   ("Roberto", "D", "Atalaia", 123456888, "Rua da Laranja 28, Centro - Jardim das Flores"),
		   ("Marcos", "A", "Dalponte", 445456888, "Rua da Laranja 98, Centro - Jardim das Flores"),
		   ("Julia", "A", "Martins", 111456888, "Rua Alameda Asturias 1298, Centro - Jardim das Flores"),
		   ("Carlos", "S", "Cesar", 134456418, "Rua Alameda Asturias 101, Centro - Jardim das Flores"),
		   ("Karol", "A", "Damasceno", 136456748, "Avenida Asturias 123, Centro - Jardim das Flores");
		
insert into product (Pname, SKU, Category, pDescription, pValue, Note) 
	values ("Fone de Ouvido", 6542, "Eletrônico", "Música e esportes", "125.00", "5"),
		   ("Teclado mecânico", 1532, "Eletrônico", "Escritório e games", "375.00", "4"),
		   ("Headset com Microfone", 122, "Eletrônico", "Escritório e games", "275.00", "5"),
		   ("Cadeira gamer", 42122, "Móveis", "Escritório", "1455.00", "3"),
		   ("Mousepad speed", 195, "Eletrônico", "Escritório e games", "140.00", "3"),
		   ("Monitor 27 polegadas 144hz", 1295, "Eletrônico", "Escritório e games", "1855.00", "5");
           
insert into request (idRequestClient, rStatus, rDescription, rShipping, paymentCash)
	values (1, "Em andamento", null, 15, 1),
		   (2, "Processando", "Confirmado", 25, 1),
		   (3, "Entregue", "Confirmado", 15, 0),
		   (4, "Em andamento", null, 15, 1);

insert into pStorage (psSKU, psLocation, psQuantity)
values 
	(12, "Rio de Janeiro", 20),
	(43, "Rio de Janeiro", 150),
	(112, "São Paulo", 120),
	(1, "São Paulo", 10),
	(2, "São Paulo", 5),
	(33, "Curitiba", 80);

insert into supplier (SocialName, CNPJ, sAddress, sContact)
	values
		("Eletrônicos datacenter", 12345678912345, "Bairro dos Perdizes, RJ", 2198458724),
		("Eletrônicos dotcom", 12323478912345, "Bairro das Nações, RJ", 2198453324),
		("Eletrônicos siridot", 32123478912435, "Bairro da Liberdade, SP", 2174453324),
		("Eletrônicos Anasdaq", 55523478912435, "Bairro da Liberdade, SP", 2174453378);

insert into seller (sSocialName, sName, CNPJ, CPF, sLAddress, sContact)
	values
		("Boutique da eletronica", null, 34567832491234, 12345678912, "São Paulo", 1194324675),
		("Ponto da eletronica", null, 56767832321234, 32145678912, "São Paulo", 1176324623),
		("Mundo dos monitores", null, 11167833391234, 56785678912, "São Paulo", 1133325675);

insert into pSeller (idpSeller, idProduct, pQuantity)
	values
		(1, 1,  12),
		(2, 2, 3345),
		(3, 3, 120);

insert into pRequest (idprProduct, idprRequest, prQuantity, prStatus)
	values
		(1, 3, 10, default),
		(2, 2, 3, "Disponível"),
		(3, 1, 0, "Sem estoque");
        
insert into storageLocation (idLproduct, idLstorage, location)
values
	(2, 3, "São Paulo"),
	(1, 5, "São Paulo"),
	(4, 8, "Rio de Janeiro");

insert into pSupplier (idpSupplier, idpsppProduct, psppQuantity)
values
	(1, 4, default),
	(1, 2, 120),
	(3, 1, 10);

select count(*) from clients;

select * from clients c, request o where c.idClient = idRequestClient;

select concat(Fname, " ", Lname) as Name, idRequest, rStatus from clients c, request o where c.idClient = idRequestClient;

select * from clients c, request o
	where c.idClient = idRequestClient
	group by idRequest;
    
select * from clients
	left outer join request on idClient = idRequestClient;
    
select distinct p.pName as Product_Name, concat(c.Fname, " ", c.Lname) as Names, Address
	from clients as c, product as p, request as r
	where (c.idClient = r.idRequestClient)
	order by c.Fname, c.Lname, p.pName;
    
select idprProduct, Fname, count(*)
	from pRequest, clients
    where idprProduct = 1
    group by Fname, idprProduct;
    
select concat(Fname, " ", Lname) as Names, idRequest as Requests
	from clients, request
    where paymentCash = 1;
 
