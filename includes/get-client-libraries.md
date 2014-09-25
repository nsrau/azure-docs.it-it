
### Installazione tramite Composer

1.  [Installare Git][1].
    
	<div class="dev-callout"> 
	<b>Nota</b>
	<p>In Windows sarà inoltre necessario aggiungere l</p>
	</div>



2.  Creare un file denominato **composer.json** nella radice del progetto, quindi aggiungere nel file il codice seguente:
    
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

3.  Scaricare **[composer.phar][2]** nella radice del progetto.

4.  Aprire un prompt dei comandi ed eseguire quanto segue nella radice del progetto
    
         php composer.phar install

### Installare manualmente

Per scaricare e installare manualmente le librerie client PHP per Azure, attenersi alla procedura seguente:

1.  Scaricare un archivio ZIP contenente le librerie da [GitHub][3]. In alternativa, dopo il fork dell'archivio, clonare quest'ultimo nel computer locale. Per quest'ultima opzione è necessario un account GitHub e un'installazione locale di Git.
    
   

	**Nota**

	Le librerie client PHP per Azure hanno dipendenze dai pacchetti PEAR [HTTP\_Request2][4], [Mail\_mime][5] e [Mail\_mimeDecode][6]. Il modo consigliato per risolvere queste dipendenze è installare tali pacchetti utilizzando [PEAR package manager][7].


    


2.  Copiare la directory `WindowsAzure` dell'archivio scaricato nella struttura di directory dell'applicazione.

Per ulteriori informazioni sull'installazione delle librerie client PHP per Azure, comprese informazioni sull'installazione di un pacchetto PEAR, vedere [Download di Azure SDK per PHP](../php-download-sdk/).










[1]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[2]: http://getcomposer.org/composer.phar
[3]: http://go.microsoft.com/fwlink/?LinkId=252719
[4]: http://pear.php.net/package/HTTP_Request2
[5]: http://pear.php.net/package/Mail_mime
[6]: http://pear.php.net/package/Mail_mimeDecode
[7]: http://pear.php.net/manual/en/installation.php