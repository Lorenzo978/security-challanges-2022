## Challange
Read the content of `/flag`.
## Solution
In the `run` method there is a race condition since the file have the same name all times. We can write the "good code", pass the validation test and overwrite it again with the "bad code" before it's executed. This is done by calling two different threads, one that write good code and one with bad code.