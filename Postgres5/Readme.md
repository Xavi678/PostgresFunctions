### Postgres 5 : Funcions

## 1) Quin serà el resultat obtingut desprès d’executar la funció Exemple ?



## 2) Creeu una taula, anomenada RegEntrada, amb els següents camps:


## 3) Teclejeu les dues funcions següents i responeu les preguntes:
Nota: busqueu la funció now en la vostra versió de Postgres que retorna la data actual.

  a ) Que fan aquestes funcions ?
    - Les dues funcions inserten la data actual, i el text que li fiquis com a entrada, i retorna la data actual
  b ) Funcionen les dues igual ? En cas contrari on esta la diferència i perquè ?
    - No canvien a l'hora d'instanciar l'hora, la primera inserta directament l'hora, en canvi la segona primer instancia un timestamp al cual li assigna l'hora actual per després insertar-la
## 4) Crear una funció que retorni cert si existeix l’assignatura amb idassignatura igual a 3 i fals altrament.

 Create  or replace function ExisteixAssignatura(id integer ) returns Boolean as $$
declare
contador integer;

Begin
select count(*) into contador from assignatura where idassignatura=3;
IF contador=0 then

return false;
else

return true;

end if;



End;
$$ Language plpgsql;

## 5) Modificar la funció anterior per tal que puguem passar com a paràmetre el codi de l’assignatura a buscar.

Create  or replace function ExisteixAssignatura(id integer ) returns Boolean as $$
declare
contador integer;

Begin
select count(*) into contador from assignatura where idassignatura=id;
IF contador=0 then

return false;
else

return true;

end if;



End;
$$ Language plpgsql;
## 6) Crear una funció que retorni la suma de les notes de tots els alumnes de ‘Lleida’.

Create  or replace function sumaAssignatures( ) returns integer as $$
declare
suma integer;

Begin
 select sum(n.nota) into suma  from alumne as a,notes as n where a.idalumne=n.idalumne and a.ciutat like 'Lleida';

return suma;


End;
$$ Language plpgsql;

## 7) Crear una funció que doni d’alta un Alumne. Cal passar-li com a paràmetres tots els camps menys l’identificador.
Create  or replace function afegirAlumne(nomA character varying(20),edatA integer, ciutatA character varying(20)) returns void as $$


Begin
 

insert into alumne(nom,edat,ciutat) values(nomA,edatA,ciutatA);


End;
$$ Language plpgsql;
## 8)Crear una funció que doni de baixa un Alumne donat. (passant-li com a paràmetre l’idalumne).
Create  or replace function deleteAlumne( id integer ) returns void as $$


Begin
 
delete from notes where idalumne=id;
delete from alumne where idalumne=id;



End;
$$ Language plpgsql;
## 9) Crear una funció que esborri una nota donada passant només l’idalumne i l’idassignatura i que ens retorni la nota que hi havia a la taula notes. En cas de no existir aquesta nota ficada cal retornar –1.
Create  or replace function removeNota( idAlum integer, idAssign integer ) returns 
integer as $$
declare 
notaTmp integer;

Begin

 if((SELECT COUNT(*) FROM notes where idalumne=idAlum and idassignatura=idAssign)=0) then

 return -1;
 else
SELECT  nota into notaTmp FROM notes where idalumne=idAlum and idassignatura=idAssign;
delete from notes where idalumne=idAlum and idassignatura=idAssign;

return notaTmp;
end if;


End;
$$ Language plpgsql;
## 10) Crear una funció que doni d’alta a la taula Notes. Aquesta funció només tindrà els 3 paràmetres d’aquesta taula. En cas de que el codi de l’alumne no existeixi cal donar d’alta un nou Alumne, amb valors per defecte (Nom = ‘Ferran’, Edat = 21, Ciutat = ‘Lleida’), i assignar a la taula notes el nou codi de l’alumne assignat. Fer el mateix pel cas de la taula Assignatura (Nom = ‘Clau’, Numalumnes = 10). 

Create  or replace function altaNota( idAlum integer, idAssign integer ,notaH integer) returns 
void as $$
declare 
idatmp integer;
idastmp integer;

Begin

 if((SELECT COUNT(*) FROM alumne where idalumne=idAlum )=0) then

 insert into alumne(nom,edat,ciutat) values('Ferran',21,'Lleida');

select idalumne into idatmp from alumne order  by idalumne desc limit 1;

insert into notes values(idatmp,idAssign,notaH);
 
 elsif ((SELECT COUNT(*) FROM assignatura where idassignatura=idAssign )=0) then
 
 insert into assignatura(nom,numalumnes) values('Clau',10);

select idassignatura into idastmp from assignatura  order by idassignatura desc limit 1;

insert into notes values(idAlum,idastmp,notaH);

else

insert into notes values(idAlum,idAssign,notaH);
end if;


End;
$$ Language plpgsql;