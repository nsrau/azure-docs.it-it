<properties linkid="migrating-drupal-to-azure-websites" urlDisplayName="Migrating Drupal to Azure Web Sites" pageTitle="Migrating Drupal to Azure Web Sites" metaKeywords="Drupal, PHP, Web Sites" description="Migrate a Drupal PHP site to Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Migrating Drupal to Azure Web Sites" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

Migrazione di Drupal in Siti Web di Azure
=========================================

Poiché Siti Web di Azure supporta sia PHP che MySQL, la migrazione di un sito di Drupal in Siti Web di Azure è relativamente semplice. Poiché inoltre è possibile eseguire Drupal e PHP in qualsiasi piattaforma, il processo di spostamento di Drupal in Siti Web di Azure dovrebbe funzionare correttamente indipendentemente dalla piattaforma. Le installazioni di Drupal, tuttavia, possono presentare notevoli differenze ed è pertanto possibile che siano necessari passaggi specifici per la migrazione non illustrati nel materiale seguente. Si noti che lo strumento Drush non viene utilizzato, poiché non è supportato in Siti Web di Azure.

> [WACOM.NOTE] 
> Se si sposta un'applicazione Drupal complessa e di grandi dimensioni, è anche possibile valutare l'utilizzo di Servizi cloud di Azure. Per ulteriori informazioni sulle differenze tra Siti Web e Servizi cloud, vedere [Confronto tra siti Web, servizi cloud e macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkId=310123). Per informazioni sullo spostamento di Drupal in Servizi cloud, vedere il blog relativo alla [migrazione di un sito di Drupal da LAMP ad Azure](http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx).

Sommario
--------

-   [Creazione di un sito Web di Azure](#create-siteanddb)
-   [Copia di un database](#copy-database)
-   [Modifica del file Settings.php](#modify-settingsphp)
-   [Distribuzione del codice di Drupal](#deploy-drupalcode)
-   [Informazioni correlate](#related-information)

Creazione di un sito Web di Azure e di un database MySQL1. Creazione di un sito Web di Azure e di un database MySQL
-------------------------------------------------------------------------------------------------------------------

Eseguire innanzitutto l'esercitazione dettagliata relativa alla procedura per la creazione di un nuovo sito Web mediante MySQL: [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git](http://www.windowsazure.com/it-it/develop/php/tutorials/website-w-mysql-and-git/). Se si desidera utilizzare Git per pubblicare il sito di Drupal, eseguire la procedura disponibile nell'esercitazione che illustra come configurare un archivio Git. Assicurarsi di seguire le istruzioni disponibili nella sezione **Recupero di informazioni sulla connessione remota a MySQL**, poiché tali informazioni saranno necessarie più avanti. Per le finalità della distribuzione del sito di Drupal è possibile ignorare la parte restante dell'esercitazione, ma se non si è esperti di Siti Web di Azure e di Git, è possibile che la lettura di tali informazioni risulti utile.

Dopo la configurazione di un nuovo sito Web con un database MySQL, saranno disponibili le informazioni sulla connessione al database e un archivio Git (facoltativo). Il passaggio successivo consiste nel copiare il database in MySQL in Siti Web di Azure.

Copia di un database in MySQL in Siti Web di Azure2. Copia di un database in MySQL in Siti Web di Azure
-------------------------------------------------------------------------------------------------------

È possibile eseguire in molti modi la migrazione di un database in Azure. La soluzione ideale per i database MySQL consiste nell'utilizzare lo strumento [MySqlDump][]. Il comando seguente offre un esempio di come eseguire una copia da un computer locale a Siti Web di Azure:

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

È ovviamente necessario specificare il nome utente e la password per il database Drupal esistente in uso. È inoltre necessario specificare il nome host, il nome utente, la password e il nome del database MySQL creato nel primo passaggio. Tali informazioni sono disponibili nelle informazioni sulla stringa di connessione ottenute in precedenza. Il formato della stringa di connessione deve essere analogo al seguente:

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

In base alla dimensione del database, è possibile che il processo di copia richieda alcuni minuti.

Il database Drupal è ora disponibile in Siti Web di Azure. Prima di distribuire il codice Drupal, sarà necessario modificarlo in modo da consentirne la connessione al nuovo database.

Modifica delle informazioni sulla connessione al database nel file settings.php3. Modifica delle informazioni sulla connessione al database nel file settings.php
-----------------------------------------------------------------------------------------------------------------------------------------------------------------

Le informazioni sulla connessione al nuovo database sono necessarie anche in questo passaggio. Aprire il file **/drupal/sites/default/setting.php** in un editor di testo e sostituire i valori ‘database’, ‘username’, ‘password’ e ‘host’ nella matrice **\$databases** con i valori corretti per il nuovo database. Al termine, si dovrebbe ottenere un risultato analogo al seguente:

    $databases = array (
       'default' => 
       array (
         'default' => 
         array (
           'database' => 'remote_db_name',
           'username' => 'remote_username',
           'password' => 'remote_password',
           'host' => 'remote_host',
           'port' => '',
           'driver' => 'mysql',
           'prefix' => '',
         ),
       ),
     );

Salvare il file **settings.php**. È ora possibile iniziare la distribuzione.

Distribuzione del codice Drupal mediante Git o FTP4. Distribuzione del codice Drupal mediante Git o FTP
-------------------------------------------------------------------------------------------------------

L'ultimo passaggio consiste nel distribuire il codice in Siti Web di Azure mediante Git o FTP.

Se si utilizza FTP, ottenere il nome host e il nome utente per FTP dal dashboard del sito Web. Utilizzare quindi un client FTP per caricare i file di Drupal nella cartella **/site/wwwroot** del sito remoto.

Se si utilizza Git, nei passaggi precedenti è stato configurato un archivio Git. È necessario installare Git nel computer locale e quindi seguire le istruzioni fornite dopo la creazione dell'archivio.

> [WACOM.NOTE] 
> In base alle impostazioni specifiche per Git, potrebbe essere necessario modificare il file con estensione gitignore. Si tratta di un file nascosto, elemento di pari livello rispetto alla cartella .git creata nella directory radice locale dopo l'esecuzione del commit di Git. In questo modo sarà possibile specificare i file da ignorare nell'applicazione Drupal. Se il file include file da distribuire, rimuovere le voci corrispondenti, in modo che tali file non vengano ignorati.

Dopo la distribuzione di Drupal in Siti Web di Azure, sarà possibile continuare a distribuire aggiornamenti mediante Git o FTP.

Informazioni correlateInformazioni correlate
--------------------------------------------

Per ulteriori informazioni, vedere i post e gli argomenti seguenti:

-   [Siti Web di Azure: la prospettiva PHP](http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx)
-   [Confronto tra siti Web, servizi cloud e macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkId=310123)
-   [Configurazione di PHP in Siti Web di Azure mediante file .user.ini](http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx)
-   [Modulo di integrazione di Azure](https://drupal.org/project/azure_auth)
-   [Modulo di archiviazione BLOB di Azure](https://drupal.org/project/azure_blob)


