### Installazione tramite Composer

1. [Installare Git][install-git]. Si noti che in Windows, è anche necessario aggiungere l'eseguibile Git alla variabile di ambiente PATH. 

2. Creare un file denominato **composer.json** nella radice del progetto, quindi aggiungere nel file il codice seguente:

	```
	{
      "require": {
        "microsoft/windowsazure": "^0.4"
      }
    }
	```

3. Scaricare **[composer.phar][composer-phar]** nella radice del progetto.

4. Aprire un prompt dei comandi ed eseguire il comando seguente nella radice del progetto

	```
	php composer.phar install
	```

[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar

<!---HONumber=AcomDC_0601_2016-->