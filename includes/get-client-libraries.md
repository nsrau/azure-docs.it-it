### Installazione tramite Composer

1.  [Installare Git][].

    <div class="dev-callout"> 
<b>Nota</b> 
<p>In Windows sar&agrave; inoltre necessario aggiungere l'eseguibile Git alla variabile di ambiente PATH.</p>
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

3.  Scaricare **[composer.phar][]** nella radice del progetto.

4.  Aprire un prompt dei comandi ed eseguire quanto segue nella radice del progetto

        php composer.phar install

### Installare manualmente

Per scaricare e installare manualmente le librerie client PHP per Azure, attenersi alla procedura seguente:

1.  Scaricare un archivio ZIP contenente le librerie da [GitHub][]. In alternativa, dopo il fork dell'archivio, clonare quest'ultimo nel computer locale. Per quest'ultima opzione è necessario un account GitHub e un'installazione locale di Git.

    <div class="dev-callout"> 
<b>Nota</b> 
<p>Le librerie client PHP per Azure hanno dipendenze dai pacchetti PEAR <a href="http://pear.php.net/package/HTTP_Request2">HTTP_Request2</a>, <a href="http://pear.php.net/package/Mail_mime">Mail_mime</a> e <a href="http://pear.php.net/package/Mail_mimeDecode">Mail_mimeDecode</a>. Il modo consigliato per risolvere queste dipendenze &egrave; installare tali pacchetti utilizzando <a href="http://pear.php.net/manual/en/installation.php">PEAR package manager</a>.</p> 
</div>

2.  Copiare la directory `WindowsAzure` dell'archivio scaricato nella struttura di directory dell'applicazione.

Per altre informazioni sull'installazione delle librerie client PHP per Azure, comprese informazioni sull'installazione di un pacchetto PEAR, vedere [Download di Azure SDK per PHP][].

  [Installare Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  [GitHub]: http://go.microsoft.com/fwlink/?LinkId=252719
  [HTTP\_Request2]: http://pear.php.net/package/HTTP_Request2
  [Mail\_mime]: http://pear.php.net/package/Mail_mime
  [Mail\_mimeDecode]: http://pear.php.net/package/Mail_mimeDecode
  [PEAR package manager]: http://pear.php.net/manual/en/installation.php
  [Download di Azure SDK per PHP]: ../php-download-sdk/
