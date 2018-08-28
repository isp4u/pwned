# pwned
For checking user passwords against "Have I Been Pwned" local password list.

# Requirement
PHP 7.0

# Building
Create a domain like pwned.yourpersonaldomain.de on your webserver and secure it with HTTPS.  
Download the password list via torrent or direct link from https://haveibeenpwned.com/Passwords
$ wget https://downloads.pwnedpasswords.com/passwords/pwned-passwords-ordered-by-count.7z

Extraction will take some time:
$ nohup 7z e pwned-passwords-ordered-by-count.7z &

After extracting you can delete the pwned-passwords-ordered-by-count.7z with:
$ rm pwned-passwords-ordered-by-count.7z 

Now create the SQlite3 database files:
$ nohup php pwnedCreateDBs new pwned-passwords-ordered-by-count.txt secretDatabasePassword.db secretDatabaseCustomPassword.db &
secretDatabaseCustomPassword.db is needed to be specified. You can delete it afterwards if you set $pwnedTrackPasswords to False within the pwnedConf.php.

This also takes a long time, have some more coffee and go to lunch. When finished you could delete the pwned-passwords-ordered-by-count.txt file, create a dummy index.html and edit the pwnedconf.php:
$ rm pwned-passwords-ordered-by-count.txt
$ touch index.html
$ joe pwnedConf.php

<?php
  if (!defined('pwned')) {
    die ('No direct access!');
  }
  $pwnedWithApiKey = True;      // global Apikey allowed
  $pwnedAllowGET = False;		    // $_GET and/or $_POST. $_POST is prefered, since there is no serverlog of password hash.    
  $pwnedAuthUser = 'default';	  // default User for global access
  $pwnedAuthApikey ='default';	// default Apikey for global access
  $pwnedDB = 'pwned.db3'; 		  // used to store pwned-passwords, please use random filename
  $pwnedTrackPasswords = False;	// store user password hash SHA1 in secretDatabaseCustomPassword.db
  $pwnedPasswordDB = '';		    // use random filename for secretDatabaseCustomPassword.db
?>

Now you can adapt pwnedUserAuth.php for custom user access.

# Testing 
With $pwnedAllowGet = True in pwnedConf.php you can test it easly by calling the following URLs. First URL is pwned. Second URL is not pwned. Please use $pwnedAllowGet = True only for testing!

https://pwned.yourpersonaldomain.de/pwnedCheck.php??user=default&apikey=default&pwhash=00000000DD7F2A1C68A35673713783CA390C9E93

Response:
{"error":false,"msg":"","pwned":1}

https://pwned.yourpersonaldomain.de/pwnedCheck.php??user=default&apikey=default&pwhash=00000000DD7F2A1C68A35673713783CA390C9E92

Response:
{"error":false,"msg":"","pwned":0}

# License
Distributed under BSD license. (c) by 0bj UG (haftungsbeschränkt) https://0bj.de

# Links
[1] Have I Been Pwned: https://haveibeenpwned.com/
[2] Project german homepage: https://0bj.de/de/projekte/eigene-projekte/pwned/
[3] Project english homepage: https://0bj.de/en/projects/own-projects/pwned/
