<properties 
	pageTitle="Migrazione da Drupal a Servizio app di Azure" 
	description="Eseguire la migrazione di un sito PHP Drupal in Servizio app di Azure." 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tomfitz"/>


# Migrazione da Drupal a Servizio app di Azure

Poiché Servizio app di Azure supporta sia PHP sia MySQL, la migrazione di un sito di Drupal nelle app Web di Servizio app di Azure è relativamente semplice. Poiché inoltre è possibile eseguire Drupal e PHP in qualsiasi piattaforma, il processo di spostamento di Drupal nelle app Web di Servizio app di Azure dovrebbe funzionare correttamente indipendentemente dalla piattaforma. Le installazioni di Drupal, tuttavia, possono presentare notevoli differenze ed è pertanto possibile che siano necessari passaggi specifici per la migrazione non illustrati nel materiale seguente. Si noti che lo strumento Drush non viene usato perché non è supportato in Servizio app di Azure.

Per spostare un'applicazione Drupal complessa e di grandi dimensioni, è anche possibile provare a usare Servizi cloud di Azure. Per altre informazioni sulle differenze tra Servizio app e Servizi cloud, vedere l'argomento relativo al <a href="http://go.microsoft.com/fwlink/?LinkId=310123">confronto tra Servizio app, Servizi Cloud e Macchine virtuali di Azure</a>. Per informazioni sullo spostamento di Drupal in Servizi cloud, vedere il blog relativo alla <a href="http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx">migrazione di un sito di Drupal da LAMP ad Azure</a>.
 
## Creare un'app Web e un database MySQL

Eseguire innanzitutto l'esercitazione dettagliata relativa alla procedura per la creazione di una nuova app Web con MySQL: [Creazione di un'app Web PHP-MySQL in Servizio app di Azure e distribuzione tramite Git][]. Se si desidera usare Git per pubblicare il sito di Drupal, eseguire la procedura disponibile nell'esercitazione che illustra come configurare un repository Git. Assicurarsi di seguire le istruzioni disponibili nella sezione relativa al **recupero di informazioni sulla connessione remota a MySQL** perché queste informazioni saranno necessarie più avanti. Ai fini della distribuzione del sito di Drupal è possibile ignorare la parte restante dell'esercitazione, ma se non si è esperti di Servizio app di Azure e di Git, è possibile che la lettura di tali informazioni risulti utile.

Dopo la configurazione di una nuova app Web con un database MySQL, saranno disponibili le informazioni sulla connessione al database e un repository Git (facoltativo). Il passaggio successivo consiste nel copiare il proprio database nel database MySQL dell'app Web.

## Copiare un database nel database MySQL dell'app Web

È possibile eseguire in molti modi la migrazione di un database in Azure. La soluzione ideale per i database MySQL consiste nell'usare lo strumento [MySqlDump][]. Il seguente comando offre un esempio di come eseguire una copia da un computer locale ad Azure:

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

È ovviamente necessario specificare il nome utente e la password per il database Drupal esistente in uso. È inoltre necessario specificare il nome host, il nome utente, la password e il nome del database MySQL creato nel primo passaggio. Tali informazioni sono disponibili nelle informazioni sulla stringa di connessione ottenute in precedenza. Il formato della stringa di connessione deve essere analogo al seguente:

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

In base alla dimensione del database, è possibile che il processo di copia richieda alcuni minuti.

Il database Drupal è ora disponibile in Azure. Prima di distribuire il codice Drupal, sarà necessario modificarlo in modo da consentirne la connessione al nuovo database.

## Modificare le informazioni di connessione del database nel file settings.PHP

Le informazioni sulla connessione al nuovo database sono necessarie anche in questo passaggio. Aprire il file **/drupal/sites/default/setting.php** in un editor di testo e sostituire i valori di  'database', 'username', 'password' e  'host' nella matrice **$databases** con i valori corretti per il nuovo database. Al termine, si dovrebbe ottenere un risultato analogo al seguente:

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

## Distribuire il codice Drupal mediante Git o FTP

L'ultimo passaggio consiste nel distribuire il codice nelle app Web mediante Git o FTP.

Se si usa FTP, ottenere il nome host e il nome utente per FTP dal pannello dell'app Web nel [portale di Azure](https://portal.azure.com). Usare quindi un client FTP per caricare i file di Drupal nella cartella **/site/wwwroot** del sito remoto.

Se si usa Git, nei passaggi precedenti è stato configurato un repository Git. È necessario installare Git nel computer locale e quindi seguire le istruzioni fornite dopo la creazione del repository.

> [AZURE.NOTE]
> In base alle impostazioni specifiche per Git, potrebbe essere necessario modificare il file con estensione gitignore. Si tratta di un file nascosto, elemento di pari livello rispetto alla cartella .git creata nella directory radice locale dopo l'esecuzione del commit di Git. In questo modo sarà possibile specificare i file da ignorare nell'applicazione Drupal. Se il file include file da distribuire, rimuovere le voci corrispondenti, in modo che tali file non vengano ignorati.

Dopo la distribuzione di Drupal nelle app Web, sarà possibile continuare a distribuire aggiornamenti mediante Git o FTP.

## Informazioni correlate

Per altre informazioni, vedere i post e gli argomenti seguenti:

- [App Web di Servizio app di Azure, una prospettiva PHP][]
- [Confronto tra Servizio app, Servizi cloud e Macchine virtuali di Azure][]
- [Configurazione di PHP nelle app Web di Servizio app di Azure con file .user.ini][]
- [Modulo di integrazione di Azure](https://drupal.org/project/azure_auth)
- [Modulo di archiviazione BLOB di Azure](https://drupal.org/project/azure_blob)

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Servizio app di Azure e relativo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida sul passaggio dal vecchio al nuovo portale, vedere: [Informazioni di riferimento per l'esplorazione del portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

  [Creazione di un'app Web PHP-MySQL in Servizio app di Azure e distribuzione tramite Git]: /develop/php/tutorials/website-w-mysql-and-git/
  
  [App Web di Servizio app di Azure, una prospettiva PHP]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Confronto tra Servizio app, Servizi cloud e Macchine virtuali di Azure]: http://go.microsoft.com/fwlink/?LinkId=310123
  [Configurazione di PHP nelle app Web di Servizio app di Azure con file .user.ini]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Modulo di integrazione di Azure]: http://drupal.org/project/azure


<!--HONumber=52--> 