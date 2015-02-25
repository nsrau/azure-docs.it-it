<properties 
	pageTitle="Migrazione di Drupal in Siti Web di Azure" 
	description="Eseguire la migrazione di un sito PHP Drupal in Siti Web di Azure." 
	services="web-sites" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="tomfitz"/>


# Migrazione di Drupal in Siti Web di Azure

Poiché Siti Web di Azure supporta sia PHP che MySQL, la migrazione di un sito di Drupal in Siti Web di Azure è relativamente semplice. Inoltre, poiché è possibile eseguire Drupal e PHP in qualsiasi piattaforma, il processo di spostamento di Drupal in Siti Web di Azure dovrebbe funzionare indipendentemente dalla piattaforma corrente. Le installazioni di Drupal, tuttavia, possono presentare notevoli differenze ed è pertanto possibile che siano necessari passaggi specifici per la migrazione non illustrati nel materiale seguente. Si noti che lo strumento Drush non viene usato, poiché non è supportato in Siti Web di Azure.

> [AZURE.NOTE]
> Per spostare un'applicazione Drupal complessa e di grandi dimensioni, è anche possibile provare a usare Servizi cloud di Azure. Per altre informazioni sulle differenze tra Siti Web e Servizi cloud, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=310123">Confronto tra Siti Web, Servizi Cloud e Macchine virtuali di Azure</a>. Per informazioni sullo spostamento di Drupal in Servizi cloud, vedere il blog relativo alla <a href="http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx">migrazione di un sito di Drupal da LAMP ad Azure</a>.

## Sommario

- [Creazione di un sito Web di Azure][]
- [Copia di un database][]
- [Modifica del file Settings.php][]
- [Distribuzione del codice di Drupal][]
- [Informazioni correlate][]
 
##<a name="create-siteanddb"></a>1. Creare un sito Web di Azure e un database MySQL

Eseguire innanzitutto l'esercitazione dettagliata relativa alla procedura per la creazione di un nuovo sito Web mediante MySQL: [Creare un sito Web di Azure PHP-MySQL e distribuirlo tramite Git][]. Se si desidera usare Git per pubblicare il sito di Drupal, eseguire la procedura disponibile nell'esercitazione che illustra come configurare un repository Git. Assicurarsi di seguire le istruzioni disponibili nella sezione **Recupero di informazioni sulla connessione remota a MySQL**, poiché tali informazioni saranno necessarie più avanti. Per le finalità della distribuzione del sito di Drupal è possibile ignorare la parte restante dell'esercitazione, ma se non si è esperti di Siti Web di Azure e di Git, la lettura di tali informazioni potrebbe risultare utile.

Dopo la configurazione di un nuovo sito Web con un database MySQL, saranno disponibili le informazioni sulla connessione al database e un repository Git (facoltativo). Il passaggio successivo consiste nel copiare il database in MySQL in Siti Web di Azure.

##<a name="copy-database"></a>2. Copiare il database in MySQL in Siti Web di Azure

È possibile eseguire in molti modi la migrazione di un database in Azure. La soluzione ideale per i database MySQL consiste nell'usare lo strumento [MySqlDump][]. Il comando seguente offre un esempio di come eseguire una copia da un computer locale a Siti Web di Azure:

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

È ovviamente necessario specificare il nome utente e la password per il database Drupal esistente in uso. È inoltre necessario specificare il nome host, il nome utente, la password e il nome del database MySQL creato nel primo passaggio. Tali informazioni sono disponibili nelle informazioni sulla stringa di connessione ottenute in precedenza. Il formato della stringa di connessione deve essere analogo al seguente:

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

In base alla dimensione del database, è possibile che il processo di copia richieda alcuni minuti.

Il database Drupal è ora disponibile in Siti Web di Azure. Prima di distribuire il codice Drupal, sarà necessario modificarlo in modo da consentirne la connessione al nuovo database.

##<a name="modify-settingsphp"></a>3. Modificare le informazioni di connessione del database nel file settings.PHP

Le informazioni sulla connessione al nuovo database sono necessarie anche in questo passaggio. Aprire il file **/drupal/sites/default/setting.php** in un editor di testo e sostituire i valori  'database', 'username', 'password' e 'host' nella matrice **$databases** con i valori corretti per il nuovo database. Al termine, si dovrebbe ottenere un risultato analogo al seguente:

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

##<a name="deploy-drupalcode"></a>4. Distribuire il codice Drupal mediante Git o FTP

L'ultimo passaggio consiste nel distribuire il codice in Siti Web di Azure mediante Git o FTP.

Se si usa FTP, ottenere il nome host e il nome utente per FTP dal dashboard del sito Web. Usare quindi un client FTP per caricare i file di Drupal nella cartella **/site/wwwroot** del sito remoto.

Se si usa Git, nei passaggi precedenti è stato configurato un repository Git. È necessario installare Git nel computer locale e quindi seguire le istruzioni fornite dopo la creazione del repository.

> [AZURE.NOTE]
> In base alle impostazioni specifiche per Git, potrebbe essere necessario modificare il file con estensione .gitignore. Si tratta di un file nascosto, elemento di pari livello rispetto alla cartella .git creata nella directory radice locale dopo l'esecuzione del commit di Git. In questo modo sarà possibile specificare i file da ignorare nell'applicazione Drupal. Se il file include file da distribuire, rimuovere le voci corrispondenti, in modo che tali file non vengano ignorati.

Dopo la distribuzione di Drupal in Siti Web di Azure, sarà possibile continuare a distribuire aggiornamenti mediante Git o FTP.

##<a name="related-information"></a>Informazioni correlate

Per altre informazioni, vedere i post e gli argomenti seguenti:

- [Siti Web di Azure: la prospettiva PHP][]
- [Situazioni in cui utilizzare Siti Web, Servizi cloud e Macchine virtuali di Azure][]
- [Configurazione di PHP in Siti Web di Azure mediante file .user.ini][]
- [Modulo di integrazione di Azure](https://drupal.org/project/azure_auth)
- [Modulo di archiviazione BLOB di Azure](https://drupal.org/project/azure_blob)

  [Creazione di un sito Web di Azure]: #create-siteanddb
  [Copia di un database]: #copy-database
  [Modifica del file Settings.php]: #modify-settingsphp
  [Distribuzione del codice di Drupal]: #deploy-drupalcode
  [Informazioni correlate]: #related-information
  [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git]: http://www.windowsazure.com/it-it/develop/php/tutorials/website-w-mysql-and-git/
  
  [Siti Web di Azure: la prospettiva PHP]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Situazioni in cui utilizzare Siti Web, Servizi cloud e Macchine virtuali di Azure]: http://go.microsoft.com/fwlink/?LinkId=310123
  [Configurazione di PHP in Siti Web di Azure mediante file .user.ini]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Modulo di integrazione di Azure]: http://drupal.org/project/azure


<!--HONumber=42-->
