# PHP – functions that simplify working with a PDO database connector

PDO database connectors are great for preventing SQL injection attacks and maintaining a more flexible architecture, since you can use parametrized queries plus multiple types of SQL databases in addition to MySQL. The transition to using a PDO connector however is not always smooth, and some of the functions offered by PHP to work with PDO can be further simplified, so that you can use them more naturally and efficiently in your code. Here are some suggestions for functions that make life easier when working with PDO:

```php
<?php
 
// this part handles connecting to the database
$pdo_db_type = "";
$pdo_db_host = '';
$pdo_db_database_name = '';
$pdo_db_username = '';
$pdo_db_password = '';
 
try
{
 $GLOBALS['PDO_DB'] = new PDO($pdo_db_type.":host=".$pdo_db_host.";dbname=".$pdo_db_database_name,$pdo_db_username,$pdo_db_password);
 $GLOBALS['PDO_DB'] ->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
}
catch (PDOException $e)
{
 error_log('Connection to database failed: ' . $e->getMessage());
}
// end of the part that connects to the database
 
 
function pdo_query($sql_query)
{
    // this function is to simplify querying the DB
    $result = $GLOBALS['PDO_DB'] ->query($sql_query);
    return $result; 
}
 
function pdo_parametrized_query($sql_query, $parameters) 
{   
    // this function is to simplify using parametrized queries
     
    // for example, your $sql_query can be a string: "SELECT :s1, :s2 FROM users"
    // and your $parameters could be an array of strings: array("name", "telephone")
     
    // parametrized queries are critical for the security of the database (else you are exposed to SQL injection attacks)
     
    $statement = $GLOBALS['PDO_DB']->prepare($sql_query);
    foreach ($parameters as $key => $value)
    {
        $statement->bindValue(':'.$key, $value); 
    }
    $statement -> execute();
    return $statement; 
} 
 
function pdo_query_value($sql_query, $value)
{
    // this is to quickly query for a single value
    // Example:
    // $sql_query = "SELECT name FROM users WHERE nickname='augustus'";
    // $value = "name";
    // this would return a single value, such as for example "octavian"
     
    $result = pdo_query($sql_query);
    $row = $result -> fetch(PDO::FETCH_ASSOC);
    return $row[$value];
}
 
function pdo_fetch_array($statement)
{
    return $statement -> fetch(PDO::FETCH_ASSOC);
}
 
function pdo_last_insert_id()
{
    // this function returns the ID of the last inserted row
    return $GLOBALS['PDO_DB'] -> lastInsertId();
}
?>
```
