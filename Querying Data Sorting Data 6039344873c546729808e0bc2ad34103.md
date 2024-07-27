# Querying Data: Sorting Data

- Order BY
- Top 100
- Order of Execution

### Script

In this lecture I’ll show you how to sort your data based on a column or number of columns.

I’ll also show you how to limit the number of records returned.

Ok so let me query the orders table.

```jsx
select * from tutorial_schema.orders
```

Let’s say I want the results sorted in ascending date order, I can type this

```jsx
select * from tutorial_schema.orders order by created_at asc;
```

asc stands for ascending.

So let me run this.

When I remove asc the result will be the same. This is because by default its always in ascending order unless otherwise specified.

As you can see the results are now in ascending date order. Since this is a datetime data type the engine understands how to process it in ascending order or the datetime value.

Let me know sort it by price but in descending order.

```jsx
select * from tutorial_schema.orders order by unit_price desc;
```

desc is for descending.

You can also sort by multiple columns and by using expressions.

So let me sort by the user and by the date.

```jsx
select * from tutorial_schema.orders order by user_id, created_at
```

So this returns the data first sorted by user id, then within each value of user id its sorted by created_at.

Notice when I scroll to the next user its again in ascending date order

By default its ascending.

If I want them sorted in descending user_id and created at order I can type this.

```jsx
select * from tutorial_schema.orders order by user_id desc, created_at desc;
```

So now we get the highest user id value and then within each user id its sorted from oldest to newest order.

***TOP***

If you want to limit the results returned, for example to 10 you can use the top keyword after the select keyword. Like so.

```jsx
select top 10 * from tutorial_schema.orders order by user_id desc, created_at desc;
```

Now we return only 10 records.

Great. So that’s how to order and limit your results using SQL.