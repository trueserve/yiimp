[![Build Status](https://travis-ci.org/Revasz/yiimp.svg?branch=MergeAllTheThings)](https://travis-ci.org/Revasz/yiimp)

***"yiimp"*** - crypto-mining-pool-framework.  Forked from *"yaamp"* and based on the *"yii"* framework.

Originally developed by *"globalzon"*, now maintained and further enhanced by *"tpruvot"* and various users from *"GitHub"*.

**Required:**

	"Linux", "MySQL/MariaDB", "php7.0+", "Memcached", a web-server ("Lighttpd" or "Nginx" recommended).

**Basic configuration for *"Nginx"*:**

	location / {
		try_files $uri @rewrite;
	}

	location @rewrite {
		rewrite ^/(.*)$ /index.php?r=$1;
	}

	location ~ \.php$ {
		fastcgi_pass unix:/var/run/php7.0-fpm.sock;
		fastcgi_index index.php;
		include fastcgi_params;
	}

**If you use *"Apache"*, it should be something like that. (Already set in "web/.htaccess"):**

	RewriteEngine on

	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^(.*) index.php?r=$1 [QSA]

**If you use *"Lighttpd"*, use the following configuration:**

	$HTTP["host"] =~ "YourServerName" {
	        server.document-root = "/var/web"
	        url.rewrite-if-not-file = (
			"^(.*)/([0-9]+)$" => "index.php?r=$1&id=$2",
			"^(.*)\?(.*)" => "index.php?r=$1&$2",
	                "^(.*)" => "index.php?r=$1",
	                "." => "index.php"
	        )

		url.access-deny = ( "~", ".dat", ".log" )
	}

For the database, import the initial dump present in the *"sql/"* folder.

Then, apply the migration scripts to be in sync with the current git, they are sorted by date of change.

Your database need at least 2 users, one for the web site (*"php7.0+"*)\
and one for the *"stratum"* connections.\
(password set in *"config/algo.conf"*)

The recommended install folder for the stratum engine is *"/var/stratum."*\
Copy all the *".conf"* files, *"run.sh"*, the *"stratum"* binary\
and the *"blocknotify"* binary to this folder.

Some scripts are expecting the web folder to be *"/var/web"*.\
You can use directory *"symlinks"*.

**Add your exchange *"API"* public and secret keys in these two separated files:**

	"/etc/yiimp/keys.php" - Fixed path in code.
	"web/serverconfig.php" - Use sample as basic configuration.

You can find sample configuration files in\
*"web/serverconfig.sample.php"*\
and\
*"web/keys.sample.php"*.

This web application includes some command line tools, add *"bin/"* folder to your path\
and type *"yiimp"* to list them, *"yiimp checkup"* can help to test your initial setup.\
Future scripts and maybe the *"cron"* jobs will then use this *"yiic"* interface.

**You need at least three backend shells (in *"screen"*) running these scripts:**

	"web/main.sh"
	"web/loop2.sh"
	"web/block.sh"

**Start one *"stratum"* per algorithm using the *"run.sh2"* script with the algorithm as parameter.\
For example, for *"x11"*:**

	"run.sh x11"

Edit each *".conf"* file with proper values.

Look at *"rc.local"*, it starts all three backend shells and all *"stratum"* processes.\
Copy it to the *"/etc"* folder so that all *"screen shells"* are started at boot up.

**All your *"coin's"* configuration files need to *"blocknotify"*\
their corresponding *"stratum"* using something like:**

	blocknotify=blocknotify yaamp.com:port coinid %s

On your, new *"yiimp"*, website, go to *"http://yourserver.any/site/adminRights"* to login as administrator.\
You have to change it to something different in the code (*"web/yaamp/modules/site/SiteController.php"*).\
A real *"administrator"* login may be added later, but you can setup a password authentication with your web server.

**Sample for *"Lighttpd"*:**

	htpasswd -c /etc/yiimp/admin.htpasswd <adminuser>

**and in the *"Lighttpd"* configuration file:**

	# Admin access
	$HTTP["url"] =~ "^/site/adminRights" {
	        auth.backend = "htpasswd"
	        auth.backend.htpasswd.userfile = "/etc/yiimp/admin.htpasswd"
	        auth.require = (
	                "/" => (
	                        "method" => "basic",
	                        "realm" => "Yiimp Administration",
	                        "require" => "valid-user"
	                )
	        )
	}

And finally remove the *"IP-filter-check"* in *"SiteController.php"*.

There are logs generated in the *"/var/stratum"* folder and *"/var/log/stratum/debug.log"* for the *"php7.0+"* log.

**CREDITS:**

Thanks to *"globalzon"* for the release of the *"yaamp-sourcecode"*.

Thanks to *"tpruvot"* for developing and maintaining the *"yiimp-repository"*.

For my *"fork"*, I have used some code, that I found useful, from the following repositories/users:
(In no particular order)
	
https://github.com/crackfoo/yiimp
		
https://github.com/Tristian/yiimp
		
https://github.com/AlmazDelDiablo/yiimp
		
https://github.com/Infernoman/yiimp
		
https://github.com/AltMinerNet/yiimp
		
https://github.com/Jaerin/yiimp
		
https://github.com/fastman/yiimp
		
https://github.com/phm87/yiimp
		
https://github.com/LePetitBloc/yiimp
		
So, thanks to them too.

You can support this project by donating to *"tpruvot"*:

	BTC: 1Auhps1mHZQpoX4mCcVL8odU81VakZQ6dR
	
and/or *"Revasz"* (That's me.)

	BTC: 1i6yhynkkaDN2Y1RiNoZRxcQkEELdePUV
 
	ETH: 0x222eD19EAA80eE530B55b3a8394cF841DFb41Af6

	XMR: 4AbuFAvg6wUKxH4uZafgcyJuUkksxiZBz1N8sNvtQbYNe9bDfCnSFxcPs3ZPfaeDzNc9rWorxw4piBvEpuKvWL8dPSJxcPu

	BCH: 1Po5XaiwZg8iWDjZDwoNU7M56DpxRmkmed

***Revasz,* 2018**
