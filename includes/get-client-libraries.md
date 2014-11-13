### Installazione tramite Composer

1.  [Installare Git][Installare Git].

    <div class="dev-callout">

    **Nota**
    In Windows sarà inoltre necessario aggiungere l'eseguibile Git alla variabile di ambiente PATH.

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

3.  Scaricare **[composer.phar][composer.phar]** nella radice del progetto.

4.  Aprire un prompt dei comandi ed eseguire quanto segue nella radice del progetto

        php composer.phar install

### Installare manualmente

Per scaricare e installare manualmente le librerie client PHP per Azure, attenersi alla procedura seguente:

1.  Scaricare un archivio ZIP contenente le librerie da [GitHub][GitHub]. In alternativa, dopo il fork del repository, clonare quest'ultimo nel computer locale. Per quest'ultima opzione è necessario un account GitHub e un'installazione locale di Git.

    <div class="dev-callout">

    **Nota**
    Le librerie client PHP per Azure hanno dipendenze dai pacchetti PEAR [HTTP\_Request2][HTTP\_Request2], [Mail\_mime][Mail\_mime] e [Mail\_mimeDecode][Mail\_mimeDecode]. Il modo consigliato per risolvere queste dipendenze è installare tali pacchetti usando [PEAR package manager][PEAR package manager].

    </div>

2.  Copiare la directory `WindowsAzure` dell'archivio scaricato nella struttura di directory dell'applicazione.

Per altre informazioni sull'installazione delle librerie client PHP per Azure, comprese informazioni sull'installazione di un pacchetto PEAR, vedere [Download di Azure SDK per PHP][Download di Azure SDK per PHP].

  [Installare Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  [GitHub]: http://go.microsoft.com/fwlink/?LinkId=252719
  [HTTP\_Request2]: http://pear.php.net/package/HTTP_Request2
  [Mail\_mime]: http://pear.php.net/package/Mail_mime
  [Mail\_mimeDecode]: http://pear.php.net/package/Mail_mimeDecode
  [PEAR package manager]: http://pear.php.net/manual/en/installation.php
  [Download di Azure SDK per PHP]: ../php-download-sdk/
