## How to shred/parse/extract JSON in Dedicated Pool(formerly Azure SQL Warehouse) in Azure Synapse Studio 

### Create a Table in Dedicated SQL Pool to store the JSON contents
```
CREATE TABLE piestore1(
    id INT NULL,
    name VARCHAR(255) NULL,
    price VARCHAR(255) NULL,
    sweet VARCHAR(255) NULL
)
```

### Define the JSON
```
DECLARE @json NVARCHAR(4000) = N'{ 
    "pies" : {
            "sweet" : [
            { "id" : 1, "name" : "Cherry", "price" : "3.50" },
            { "id" : 2, "name" : "Chocolate", "price" : "4.99" },
            { "id" : 3, "name" : "Pumpkin", "price" : "4.00" }
        ],
            "savory" : [
            { "name" : "Meat", "price" : "6.00" },
            { "name" : "Chicken", "price" : "5.00" },
            { "name" : "Veggie", "price" : "4.00" }
        ]
    }
}';
```

### Insert into the table using the OPENJSON function
```
INSERT into piestore1
SELECT *
FROM OPENJSON(@json, '$.pies.sweet')
WITH (
    int INT '$.id',
    name VARCHAR(255) '$.name',
    price VARCHAR(255) '$.price',
    sweet NVARCHAR(max) '$' AS JSON
)
```

### Query the data in the table
```
SELECT * FROM piestore1;
SELECT * FROM OPENJSON((SELECT sweet FROM piestore1 WHERE id=1))
```
