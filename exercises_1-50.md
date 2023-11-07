5) Найдите номер модели, скорость и размер жесткого диска ПК, имеющих 12x или 24x CD и цену менее 600 дол.

```
Select model,speed, hd
from PC
where (cd = '12x'
or cd = '24x')
and price < 600
```
7) Найдите номера моделей и цены всех имеющихся в продаже продуктов (любого типа) производителя B (латинская буква).

```
Select p.model, pc.price
from product p, PC pc
where maker = 'B'
and p.model = pc.model
union 
Select p.model, l.price
from product p, Laptop l
where maker = 'B'
and p.model = l.model
union
Select p.model, prt.price
from product p, Printer prt
where maker = 'B'
and p.model = prt.model
```
8)  Найдите производителя, выпускающего ПК, но не ПК-блокноты. 

```
select maker from (
select maker from product where type='PC'
except 
select maker from product where type ='Laptop'
) a
```

9)  Найдите производителей ПК с процессором не менее 450 Мгц. Вывести: Maker 

```
select distinct maker from product
where model in (select model from PC where speed >= 450)
```

10)  Найдите модели принтеров, имеющих самую высокую цену. Вывести: model, price 

```
select model, price
from Printer
where price in (select max(price) from Printer)
```

12)  Найдите среднюю скорость ПК-блокнотов, цена которых превышает 1000 дол. 

```
Select avg(speed)
from Laptop
where price > 1000
```
13)  Найдите среднюю скорость ПК, выпущенных производителем A. 

```
Select avg(speed) 
from PC
where model in (select model from product where maker = 'A')
```
14)  Найдите класс, имя и страну для кораблей из таблицы Ships, имеющих не менее 10 орудий. 

```
select s.class, s.name, c.country
from Classes c
join Ships s on c.class = s.class
and numGuns >= 10
```
15)  Найдите размеры жестких дисков, совпадающих у двух и более PC. Вывести: HD 

```
Select hd
from PC
group by hd
having count(hd) >= 2
```
16)  Найдите пары моделей PC, имеющих одинаковые скорость и RAM. В результате каждая пара указывается только один раз, т.е. (i,j), но не (j,i), Порядок вывода: модель с большим номером, модель с меньшим номером, скорость и RAM. 

```
select c.model, p.model, p.speed, p.ram
from PC p
join PC c on p.speed = c.speed and p.ram = c.ram and p.model < c.model
group by c.model, p.model, p.speed, p.ram
```
17) Найдите модели ПК-блокнотов, скорость которых меньше скорости каждого из ПК.
Вывести: type, model, speed 

```
select distinct p.type, p.model, l.speed
from product p, Laptop l
where p.model = l.model 
and speed < all(select speed from PC)
```
18)  Найдите производителей самых дешевых цветных принтеров. Вывести: maker, price 

```
select distinct maker, pr.price
from product p inner join Printer pr
on pr.model = p.model
where price = (select min(price) as PriceA from Printer where color = 'y' )
and pr.color = 'y'
```
19) Для каждого производителя, имеющего модели в таблице Laptop, найдите средний размер экрана выпускаемых им ПК-блокнотов.
Вывести: maker, средний размер экрана. 

```
select maker, avg(l.screen)
from product p inner join Laptop l on p.model = l.model
group by maker
```
20) Найдите производителей, выпускающих по меньшей мере три различных модели ПК. Вывести: Maker, число моделей ПК. 

```
select p.maker, count(distinct p.model) Count_Model
from product p
where type='PC'
group by maker
having count(distinct p.model) >= 3
```
23) Найдите производителей, которые производили бы как ПК
со скоростью не менее 750 МГц, так и ПК-блокноты со скоростью не менее 750 МГц.
Вывести: Maker 

```
select maker from (
select maker from product p join PC pc on p.model = pc.model where speed >= 750
intersect
select maker FROM product p join Laptop pc on p.model = pc.model  where  speed >= 750
) a
```
24) Перечислите номера моделей любых типов, имеющих самую высокую цену по всей имеющейся в базе данных продукции. 

```
with o as (
select model, max(price) price from PC 
group by model
union all
select model, max(price) price  from Laptop 
group by model
union all
select model, max(price) price  from Printer 
group by model
)
select model from o
where price in ( select max(price) FROM o)
```
40) Найти производителей, которые выпускают более одной модели, при этом все выпускаемые производителем модели являются продуктами одного типа.
Вывести: maker, type 

```
select maker, type
from product
where maker in (select maker from product
group by maker
having count(*) > 1 and count(distinct type) = 1
)
group by maker, type
```
43) Укажите сражения, которые произошли в годы, не совпадающие ни с одним из годов спуска кораблей на воду. 

```
select name from battles
where datepart(year from date) not in (select launched from ships where launched is not null)
```
47) Определить страны, которые потеряли в сражениях все свои корабли.

```
with b as (select country, o.ship
from outcomes o
join classes c on o.ship = c.class
union 
select country, s.name
from ships s
join classes c on s.class= c.class
)
select country from (
select country, count(*) al
from b
group by country
intersect
select country, count(*) al
from b b
left join outcomes o on b.ship = o.ship
where result = 'sunk'
group by country
)b
```