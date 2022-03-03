Краткая информация о базе данных "Корабли"

Рассматривается база данных кораблей, участвовавших во Второй Мировой войне. Имеются следующие отношения:

+ Classes (class, type, country, numGuns, bore, displacement) 
+ Ships (name, class, launched)
+ Battles (name, date)
+ Outcomes (ship, battle, result) 
  
Корабли в «классах» построены по одному и тому же проекту, и классу присваивается либо имя первого корабля, построенного по данному проекту, либо названию класса дается имя проекта, которое не совпадает ни с одним из кораблей в БД. Корабль, давший название классу, называется головным.  Отношение Classes содержит имя класса, тип (bb для боевого (линейного) корабля или bc для боевого крейсера), страну, в которой построен корабль, число главных орудий, калибр орудий (диаметр ствола орудия в дюймах) и водоизмещение ( вес в тоннах). В отношении Ships записаны название корабля, имя его класса и год спуска на воду. В отношение Battles включены название и дата битвы, в которой участвовали корабли, а в отношении Outcomes – результат участия данного корабля в битве (потоплен-sunk, поврежден - damaged или невредим - OK). 
Замечания. 1) В отношение Outcomes могут входить корабли, отсутствующие в отношении Ships. 2) Потопленный корабль в последующих битвах участия не принимает. 

Схема БД "Корабли"
![Схема](https://www.sql-ex.ru/images/ships.gif)

##  Задание:
Укажите сражения, которые произошли в годы, не совпадающие ни с одним из годов спуска кораблей на воду.

##  Решение:
``` SQL
SELECT name FROM Battles
WHERE YEAR(date) NOT IN (
  SELECT DISTINCT YEAR(date) FROM Battles B JOIN Ships S
  ON YEAR(date) = launched
)
;
```

## Задание:
Найдите названия всех кораблей в базе данных, начинающихся с буквы R.

## Решение:
``` SQL
SELECT name FROM (
  SELECT name FROM Ships
  UNION
  SELECT ship FROM Outcomes
) x
WHERE name LIKE 'R%'
;
```

##  Задание:
Для каждого производителя, у которого присутствуют модели хотя бы в одной из таблиц PC, Laptop или Printer,
определить максимальную цену на его продукцию. 
Вывод: имя производителя, если среди цен на продукцию данного производителя присутствует NULL, то выводить для этого производителя NULL, 
иначе максимальную цену.

## Решение:
``` SQL
WITH Info AS (
  SELECT model, price FROM PC
  UNION 
  SELECT model, price FROM Laptop
  UNION 
  SELECT model, price FROM Printer
)

SELECT maker,
CASE WHEN COUNT(*) = COUNT(price) THEN MAX(price) END m_price
FROM Product P JOIN Info
ON P.model = Info.model
GROUP BY maker
```

## Задание:
Найти производителей, которые выпускают более одной модели, при этом все выпускаемые производителем модели являются продуктами одного типа.
Вывести: maker, type

## Решение:
``` SQL
SELECT maker, type FROM Product
WHERE maker IN (
  SELECT maker FROM (
    SELECT maker, type, COUNT(*) AS Count
    FROM Product
    GROUP BY maker, type
  ) x
  GROUP BY maker HAVING COUNT(*) = 1
) 
GROUP BY maker, type HAVING COUNT(*) > 1 ;
```
