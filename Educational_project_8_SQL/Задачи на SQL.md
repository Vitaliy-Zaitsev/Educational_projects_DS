# Задачи, решенные мной на языке SQL
**Задания постепенно усложняются**

*Схема базы данных*

<img src="https://github.com/Vitaliy-Zaitsev/Educational_projects_DS/assets/120369294/6149324a-72d3-4410-94c7-29fb626f03cc" width="400" />

**[ER-диаграмма с описанием таблиц](https://github.com/Vitaliy-Zaitsev/Educational_projects_DS/blob/main/Educational_project_8_SQL/ER-диаграммы%20с%20описанием%20таблиц.md)**

1. Посчитайте, сколько компаний закрылось.

```sql
SELECT COUNT(id)
FROM company
WHERE status='closed';
```
---

2. Отобразите количество привлечённых средств для новостных компаний США. Используйте данные из таблицы company. Отсортируйте таблицу по убыванию значений в поле funding_total .

```sql
SELECT funding_total
FROM company
WHERE category_code = 'news'
AND country_code = 'USA'
ORDER BY funding_total DESC;
```
---
3. Найдите общую сумму сделок по покупке одних компаний другими в долларах. Отберите сделки, которые осуществлялись только за наличные с 2011 по 2013 год включительно.

```sql
SELECT SUM(price_amount)
FROM acquisition
WHERE term_code = 'cash'
AND EXTRACT(YEAR FROM acquired_at::date) >= '2011'
AND EXTRACT(YEAR FROM acquired_at::date) <= '2013';
```
---
4. Отобразите имя, фамилию и названия аккаунтов людей в твиттере, у которых названия аккаунтов начинаются на 'Silver'.

```sql
SELECT first_name,
       last_name,
       twitter_username
FROM people
WHERE twitter_username LIKE 'Silver%';
```
---
5. Выведите на экран всю информацию о людях, у которых названия аккаунтов в твиттере содержат подстроку 'money', а фамилия начинается на 'K'.

```sql
SELECT *
FROM people
WHERE twitter_username LIKE '%money%'
AND last_name LIKE 'K%';
```
---
6. Для каждой страны отобразите общую сумму привлечённых инвестиций, которые получили компании, зарегистрированные в этой стране. Страну, в которой зарегистрирована компания, можно определить по коду страны. Отсортируйте данные по убыванию суммы.

```sql
SELECT country_code,       
       SUM(funding_total)
FROM company
GROUP BY country_code
ORDER BY SUM(funding_total) DESC;
```
---
7. Составьте таблицу, в которую войдёт дата проведения раунда, а также минимальное и максимальное значения суммы инвестиций, привлечённых в эту дату.
Оставьте в итоговой таблице только те записи, в которых минимальное значение суммы инвестиций не равно нулю и не равно максимальному значению.

```sql
SELECT funded_at,
       MIN(raised_amount),
       MAX(raised_amount)
FROM funding_round
GROUP BY funded_at
HAVING MIN(raised_amount)<>0
AND MIN(raised_amount)<>MAX(raised_amount);
```
---
8. Создайте поле с категориями:
* Для фондов, которые инвестируют в 100 и более компаний, назначьте категорию high_activity.
* Для фондов, которые инвестируют в 20 и более компаний до 100, назначьте категорию middle_activity.
* Если количество инвестируемых компаний фонда не достигает 20, назначьте категорию low_activity.

Отобразите все поля таблицы fund и новое поле с категориями.
```sql
SELECT *,
       CASE
           WHEN invested_companies < 20 THEN 'low_activity'
           WHEN invested_companies < 100 THEN 'middle_activity'
           ELSE 'high_activity'
       END
FROM fund;
```
---
9. Для каждой из категорий, назначенных в предыдущем задании, посчитайте округлённое до ближайшего целого числа среднее количество инвестиционных раундов, в которых фонд принимал участие. Выведите на экран категории и среднее число инвестиционных раундов. Отсортируйте таблицу по возрастанию среднего.

```sql
SELECT 
       CASE
           WHEN invested_companies>=100 THEN 'high_activity'
           WHEN invested_companies>=20 THEN 'middle_activity'
           ELSE 'low_activity'
       END AS activity,
       ROUND(AVG(investment_rounds), 0)
FROM fund
GROUP BY activity
ORDER BY round;
```
---
10. Проанализируйте, в каких странах находятся фонды, которые чаще всего инвестируют в стартапы. \
Для каждой страны посчитайте минимальное, максимальное и среднее число компаний, в которые инвестировали фонды этой страны, основанные с 2010 по 2012 год включительно. Исключите страны с фондами, у которых минимальное число компаний, получивших инвестиции, равно нулю. \
Выгрузите десять самых активных стран-инвесторов: отсортируйте таблицу по среднему количеству компаний от большего к меньшему. Затем добавьте сортировку по коду страны в лексикографическом порядке.
```sql
SELECT country_code,
       MIN(invested_companies),
       MAX(invested_companies),
       AVG(invested_companies)
FROM fund
WHERE EXTRACT(YEAR FROM founded_at)>='2010'
AND EXTRACT(YEAR FROM founded_at)<='2012'
GROUP BY country_code
HAVING MIN(invested_companies)!=0
ORDER BY AVG(invested_companies) DESC, country_code
LIMIT 10;
```
---
11. Отобразите имя и фамилию всех сотрудников стартапов. Добавьте поле с названием учебного заведения, которое окончил сотрудник, если эта информация известна.

```sql
SELECT p.first_name,
       p.last_name,
       e.instituition
FROM people AS p
LEFT JOIN education AS e ON p.id=e.person_id;
```
---
12. Для каждой компании найдите количество учебных заведений, которые окончили её сотрудники. Выведите название компании и число уникальных названий учебных заведений. Составьте топ-5 компаний по количеству университетов.

```sql
SELECT com.name,
       COUNT(DISTINCT e.instituition)
FROM company AS com
JOIN people AS peo ON com.id=peo.company_id
JOIN education AS e ON peo.id=e.person_id
GROUP BY com.name
ORDER BY COUNT(DISTINCT e.instituition) DESC
LIMIT 5;
```
---
13. Составьте список с уникальными названиями закрытых компаний, для которых первый раунд финансирования оказался последним.

```sql
SELECT DISTINCT name
FROM company
WHERE status='closed'
AND id IN (SELECT company_id
          FROM funding_round
          WHERE is_first_round = 1 AND is_last_round = 1);
```
---
14. Составьте список уникальных номеров сотрудников, которые работают в компаниях, отобранных в предыдущем задании.

```sql
WITH comp_id AS (SELECT DISTINCT id
              FROM company
              WHERE status='closed'
              AND id IN (SELECT company_id
                         FROM funding_round
                         WHERE is_first_round = 1 AND is_last_round = 1))

SELECT DISTINCT id
FROM people
WHERE company_id IN (SELECT DISTINCT id
              FROM company
              WHERE status='closed'
              AND id IN (SELECT company_id
                         FROM funding_round
                         WHERE is_first_round = 1 AND is_last_round = 1));
```
---
15. Составьте таблицу, куда войдут уникальные пары с номерами сотрудников из предыдущей задачи и учебным заведением, которое окончил сотрудник.

```sql
SELECT DISTINCT person_id,
       instituition
FROM education
WHERE person_id IN (SELECT DISTINCT id
                    FROM people
                    WHERE company_id IN (SELECT DISTINCT id
                                         FROM company
                                         WHERE status='closed'
                                         AND id IN (SELECT company_id
                                                    FROM funding_round
                                                    WHERE is_first_round = 1 AND is_last_round = 1)));
```
---
16. Посчитайте количество учебных заведений для каждого сотрудника из предыдущего задания. При подсчёте учитывайте, что некоторые сотрудники могли окончить одно и то же заведение дважды.

```sql
SELECT person_id,
       COUNT(instituition)
FROM education
WHERE person_id IN (SELECT DISTINCT id
                    FROM people
                    WHERE company_id IN (SELECT DISTINCT id
                                         FROM company
                                         WHERE status='closed'
                                         AND id IN (SELECT company_id
                                                    FROM funding_round
                                                    WHERE is_first_round = 1 AND is_last_round = 1)))
GROUP BY person_id;
```
---
17. Дополните предыдущий запрос и выведите среднее число учебных заведений (всех, не только уникальных), которые окончили сотрудники разных компаний. Нужно вывести только одну запись, группировка здесь не понадобится.

```sql
SELECT AVG(count) FROM
(SELECT person_id,
       COUNT(instituition)
FROM education
WHERE person_id IN (SELECT DISTINCT id
                    FROM people
                    WHERE company_id IN (SELECT DISTINCT id
                                         FROM company
                                         WHERE status='closed'
                                         AND id IN (SELECT company_id
                                                    FROM funding_round
                                                    WHERE is_first_round = 1 AND is_last_round = 1)))
GROUP BY person_id) AS pt;
```
---
18. Напишите похожий запрос: выведите среднее число учебных заведений (всех, не только уникальных), которые окончили сотрудники Facebook (сервис, запрещённый на территории РФ).
```sql
SELECT AVG(count) FROM
(SELECT person_id,
       COUNT(instituition)
FROM education
WHERE person_id IN (SELECT DISTINCT id
                    FROM people
                    WHERE company_id IN (SELECT DISTINCT id
                                         FROM company
                                         WHERE name = 'Facebook'))
GROUP BY person_id) AS pt;
```
---
19. Составьте таблицу из полей:
* name_of_fund — название фонда;
* name_of_company — название компании;
* amount — сумма инвестиций, которую привлекла компания в раунде.

В таблицу войдут данные о компаниях, в истории которых было больше шести важных этапов, а раунды финансирования проходили с 2012 по 2013 год включительно.
```sql
SELECT f.name AS name_of_fund,
       comp.name AS name_of_company,
       fr.raised_amount AS amount
FROM investment AS inv JOIN company AS comp ON inv.company_id=comp.id
JOIN fund AS f ON inv.fund_id=f.id
JOIN funding_round AS fr ON inv.funding_round_id=fr.id
WHERE comp.milestones > 6
AND EXTRACT(YEAR FROM fr.funded_at) IN (2012,2013);
```
---
20. Выгрузите таблицу, в которой будут такие поля:
* название компании-покупателя;
* сумма сделки;
* название компании, которую купили;
* сумма инвестиций, вложенных в купленную компанию;
* доля, которая отображает, во сколько раз сумма покупки превысила сумму вложенных в компанию инвестиций, округлённая до ближайшего целого числа.

Не учитывайте те сделки, в которых сумма покупки равна нулю. Если сумма инвестиций в компанию равна нулю, исключите такую компанию из таблицы. 
Отсортируйте таблицу по сумме сделки от большей к меньшей, а затем по названию купленной компании в лексикографическом порядке. Ограничьте таблицу первыми десятью записями.
```sql
SELECT cb.name,
       ac.price_amount,
       cs.name AS company,
       cs.funding_total,
       ROUND(ac.price_amount/cs.funding_total, 0)
FROM acquisition AS ac 
LEFT JOIN company AS cb ON ac.acquiring_company_id=cb.id
LEFT JOIN company AS cs ON ac.acquired_company_id=cs.id
WHERE ac.price_amount<>0
AND cs.funding_total<>0
ORDER BY ac.price_amount DESC, company
LIMIT 10;
```
---
21. Выгрузите таблицу, в которую войдут названия компаний из категории social, получившие финансирование с 2010 по 2013 год включительно. Проверьте, что сумма инвестиций не равна нулю. Выведите также номер месяца, в котором проходил раунд финансирования.

```sql
SELECT company.name,
       EXTRACT(MONTH FROM funding_round.funded_at)
FROM company JOIN funding_round ON company.id=funding_round.company_id 
WHERE company.category_code = 'social'
AND funding_round.raised_amount <> 0
AND EXTRACT(YEAR FROM funding_round.funded_at) IN (2010,2011,2012,2013);
```
---
22. Отберите данные по месяцам с 2010 по 2013 год, когда проходили инвестиционные раунды. Сгруппируйте данные по номеру месяца и получите таблицу, в которой будут поля:
* номер месяца, в котором проходили раунды;
* количество уникальных названий фондов из США, которые инвестировали в этом месяце;
* количество компаний, купленных за этот месяц;
* общая сумма сделок по покупкам в этом месяце.

```sql
WITH
f AS (SELECT inv.funding_round_id,
      fund.name
      FROM investment AS inv 
      LEFT JOIN fund ON inv.fund_id=fund.id
      WHERE fund.country_code='USA'),
asq AS (SELECT EXTRACT(MONTH FROM acquired_at) AS asq_month,
               COUNT(acquired_company_id),
               SUM(price_amount)
        FROM acquisition
        WHERE EXTRACT(YEAR FROM acquired_at) IN (2010,2011,2012,2013)
        GROUP BY EXTRACT(MONTH FROM acquired_at))

SELECT a.month,
       a.cnt_com,
       asq.count,
       asq.sum
FROM (SELECT EXTRACT(MONTH FROM fr.funded_at) AS month,
      COUNT(DISTINCT f.name) AS cnt_com       
      FROM (SELECT *             
            FROM funding_round
            WHERE EXTRACT(YEAR FROM funded_at) IN (2010,2011,2012,2013)) AS fr
      FULL JOIN f ON fr.id=f.funding_round_id
      GROUP BY EXTRACT(MONTH FROM fr.funded_at)) AS a
JOIN asq ON a.month=asq.asq_month;
```
---
23. Составьте сводную таблицу и выведите среднюю сумму инвестиций для стран, в которых есть стартапы, зарегистрированные в 2011, 2012 и 2013 годах. Данные за каждый год должны быть в отдельном поле. Отсортируйте таблицу по среднему значению инвестиций за 2011 год от большего к меньшему.

```sql
SELECT cn.country_code,
       a,
       b,
       c
FROM (SELECT country_code,
             AVG(funding_total) AS a
      FROM company
      WHERE EXTRACT(YEAR FROM founded_at)=2011
      GROUP BY country_code) AS cn
JOIN (SELECT country_code,
             AVG(funding_total) AS b
      FROM company
      WHERE EXTRACT(YEAR FROM founded_at)=2012
      GROUP BY country_code) AS a ON cn.country_code=a.country_code
JOIN (SELECT country_code,
      AVG(funding_total) AS c
      FROM company
      WHERE EXTRACT(YEAR FROM founded_at)=2013
      GROUP BY country_code) AS c ON cn.country_code=c.country_code
ORDER BY a DESC;
```
---
