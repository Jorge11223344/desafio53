# database-boilerplate
database

sentencia para copiar archivo a contenedor de postgres
docker cp dataset.csv postgres_container:/docker-entrypoint-initdb.d/dataset.csv


A continuacion se copia misma secuencia de sentencias que esta en archivo pgadmin como desafio53


--Crear base de datos llamada peliculas (donde estamos trabajando)

--Cargar ambos archivos a su tabla correspondiente
create table peliculas (id int, Titulo text,año_estreno int,Director text);

copy peliculas from '/docker-entrypoint-initdb.d/peliculas.csv' with (format csv, header true);

select * from peliculas;



create table reparto (id_pelicula int, actor text);
--Había cometido el error de definir mal id_peliculas y lo tuve que cambiar con alter
ALTER TABLE reparto ALTER COLUMN id_pelicula TYPE integer USING id_pelicula::integer; 

copy reparto from '/docker-entrypoint-initdb.d/reparto.csv' with (format csv, header true);

select * from reparto;


--Obtener id de pelicula Titanic
select id, Titulo 
from peliculas 
where Titulo = 'Titanic';



--Listar a todos los actores que aparecen en la pelicula titanic
SELECT reparto.actor
FROM   reparto
JOIN   peliculas
  ON   reparto.id_pelicula = peliculas.id
WHERE  peliculas.titulo = 'Titanic';


--Consuultar en cuantas peliculas del top 100 participa Harrison Ford (primero listamos las peliculas
-- y en segunda sentencia calcula el número)
select id from peliculas
inner join reparto on peliculas.id = reparto.id_pelicula
where reparto.actor = 'Harrison Ford';

select count(*) from reparto where actor = 'Harrison Ford';

--Indicar las peliculas  estrenadas entre los años 1990 y 1999 ordenadas por titulo de manera ascendente (se solicita ordenar por titulo y no por año , se deja el año para tener una referencia)
select Titulo,año_estreno from peliculas
where año_estreno>= '1990' and año_estreno <= '1999'
order by Titulo asc;


--Hacer una consulta SQL que muestre los titulos con su longitud, la longitud debe ser nombrado para la consulta como "longitud_titulo"
SELECT *, LENGTH(Titulo)
as "longitud_titulo"
FROM peliculas
order by LENGTH(Titulo);


--Consultar cual es la longitud mas grande entre todos los titulos de las peliculas
select id,titulo, length(Titulo) 
as "longitud_titulo" from peliculas
where length(Titulo)= (select MAX(LENGTH(Titulo)) from peliculas);
