### Installare tramite Composer

1. [Installazione di Git][install-git]. 

 
	> [AZURE.NOTE] In Windows sarà inoltre necessario aggiungere l'eseguibile Git alla variabile di ambiente PATH.

2. Creare un file denominato **composer.json** nella radice del progetto, quindi aggiungere nel file il seguente codice:

		{
			"require": {
				"microsoft/windowsazure": "*"
			},			
			"repositories": [
				{
					"type": "pear",
					"url": "http://pear.php.net"
				}
			],
			"minimum-stability": "dev"
		}

3. Scaricare **[composer.phar][composer-phar]** nella radice del progetto.

4. Aprire un prompt dei comandi ed eseguire quanto segue nella radice del progetto

		php composer.phar install

### Installare manualmente

Per scaricare e installare manualmente le librerie client PHP per Azure, seguire questa procedura:

1. Scaricare un archivio ZIP contenente le librerie da [GitHub][php-sdk-github]. In alternativa, dopo il fork del repository, clonare quest'ultimo nel computer locale. Per quest'ultima opzione è necessario un account GitHub e un'installazione locale di Git.

	
	> [AZURE.NOTE] Le librerie client PHP per Azure hanno dipendenze dai pacchetti PEAR [HTTP_Request2](http://pear.php.net/package/HTTP_Request2), [Mail_mime](http://pear.php.net/package/Mail_mime) e [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode). Il modo consigliato per risolvere queste dipendenze è installare tali pacchetti usando [PEAR package manager](http://pear.php.net/manual/en/installation.php).


2. Copiare la directory `WindowsAzure` dell'archivio scaricato nella struttura di directory dell'applicazione.

Per altre informazioni sull'installazione delle librerie client PHP per Azure, comprese informazioni sull'installazione di un pacchetto PEAR, vedere [Download di Azure SDK per PHP][download-SDK-PHP].


[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../php-download-sdk/
[composer-phar]: http://getcomposer.org/composer.phar

<!--HONumber=47-->
