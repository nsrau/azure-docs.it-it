<properties 
	pageTitle="WordPress di livello aziendale in Servizio app di Azure" 
	description="Informazioni su come ospitare un sito WordPress aziendale in Servizio app di Azure" 
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="03/24/2015" 
	ms.author="tomfitz"/>

# WordPress di livello aziendale in Servizio app di Azure

Servizio app di Azure rende disponibile un ambiente scalabile, sicuro e facile da usare per siti [WordPress][wordpress] cruciali e su vasta scala. Microsoft stessa gestisce siti di livello aziendale come i blog di [Office][officeblog] e [Bing][bingblog]. Questo documento illustra come usare le app Web di Servizio app di Azure per stabilire e mantenere un sito WordPress di livello aziendale, basato sul cloud, in grado di gestire un numero elevato di visitatori.

## Architettura e pianificazione

Un'installazione di base di WordPress prevede solo due requisiti.

* **Database MySQL**: disponibile tramite [ClearDB in Azure Store][cdbnstore]. In alternativa, è possibile gestire un'installazione personalizzata di MySQL in Macchine virtuali di Azure tramite [Windows][mysqlwindows] o [Linux][mysqllinux].

    > [AZURE.NOTE] ClearDB offre diverse configurazioni di MySQL, con caratteristiche di prestazioni differenti per ognuna di esse. Per informazioni sulle offerte disponibili tramite Azure Store, vedere [Azure Store][cdbnstore] oppure, per le offerte fornite direttamente da ClearDB, vedere la [pagina dei prezzi di ClearDB](http://www.cleardb.com/pricing.view).
    
* **PHP 5.2.4 o versioni successive**: Servizio app di Azure attualmente fornisce le [versioni PHP 5.3, 5.4 e 5.5][phpwebsite].

	> [AZURE.NOTE] È consigliabile eseguire sempre la versione di PHP più recente per assicurarsi di disporre degli ultimi aggiornamenti per la sicurezza.

### Distribuzione di base

Con i soli requisiti di base, è possibile creare una soluzione di base all'interno di un'area di Azure.

![un'app Web di Azure e il database MySQL ospitati in un'unica area di Azure][basic-diagram]

Anche se questa soluzione offre scalabilità orizzontale per l'applicazione tramite la creazione di più istanze delle app Web, tutti i componenti sono ospitati all'interno dei data center di una specifica area geografica. I visitatori residenti all'esterno di quest'area possono riscontrare tempi di risposta rallentati quando usano il sito e, se si verifica un blocco dei data center nell'area, anche l'applicazione viene interrotta.


### Distribuzione in più aree

Con [Gestione traffico][trafficmanager] di Azure, è possibile scalare il sito WordPress in più aree geografiche, fornendo ai visitatori un unico URL. Tutti i visitatori accedono tramite Gestione traffico e vengono quindi instradati in un'area in base alla configurazione del bilanciamento del carico.

![un'app Web di Azure, ospitata in più aree, con l'uso del router CDBR a disponibilità elevata per il routing a MySQL tra le aree][multi-region-diagram]

All'interno di ogni area, il sito WordPress viene ancora scalato tra più istanze delle app Web, ma la scalabilità è specifica per l'area, ossia può essere diversa per le aree a traffico elevato rispetto a quelle a traffico ridotto.

La replica e il routing in più database MySQL possono essere eseguiti con il [router CDBR a disponibilità elevata][cleardbscale] di ClearDB, illustrato a sinistra, oppure con [MySQL Cluster CGE][cge]. 

### Distribuzione in più aree con memorizzazione nella cache e archiviazione di contenuti multimediali

Se il sito accetta caricamenti o ospita file multimediali, usare l'archiviazione BLOB di Azure. Se è necessaria la memorizzazione nella cache, provare a usare [Cache Redis][rediscache], [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) o una delle altre offerte disponibili in [Azure Store](http://azure.microsoft.com/gallery/store/).

![un'app Web di Azure, ospitata in più aree, con l'uso del router CDBR a disponibilità elevata per MySQL, con cache gestita, archiviazione BLOB e rete CDN][performance-diagram]

L'archiviazione BLOB è distribuita geograficamente in più aree per impostazione predefinita, quindi non è necessario occuparsi della replica dei file in tutti i siti. È anche possibile abilitare la [rete di distribuzione di contenuti (CDN)][cdn] di Azure per l'archiviazione BLOB, che distribuisce i file ai nodi finali più vicini ai visitatori.

### Pianificazione

#### Requisiti aggiuntivi

Per | Opzione
------------------------|-----------
**Caricare o archiviare file di grandi dimensioni** | [Plug-in di WordPress per l'uso dell'archiviazione BLOB][storageplugin]
**Inviare messaggi e-mail** | [SendGrid][storesendgrid] e il [plug-in di WordPress per l'uso di SendGrid][sendgridplugin]
**Nomi di dominio personalizzati** | [Configurare un nome di dominio personalizzato in Servizio app di Azure][customdomain]
**HTTPS** | [Abilitare HTTPS per un'app Web in Servizio app di Azure][httpscustomdomain]
**Convalida pre-produzione** | [Impostare ambienti di gestione temporanea per le app Web in Servizio app di Azure][staging] <p>Si noti che con il passaggio di un'app Web dalla gestione temporanea alla produzione viene trasferita anche la configurazione di WordPress. Assicurarsi che tutte le impostazioni siano aggiornate in base ai requisiti dell'app di produzione prima di passare dalla gestione temporanea alla produzione.</p> 
**Monitoraggio e risoluzione dei problemi** | [Abilitare la registrazione diagnostica per le app Web in Servizio app di Azure][log] e [monitorare le app Web in Servizio app di Azure][monitor]
**Distribuire il sito** | [Distribuire un'app Web in Servizio app di Azure][deploy]

#### Disponibilità e ripristino di emergenza

Per | Opzione
------------------------|-----------
**Bilanciamento del carico** o **distribuzione geografica dei siti** | [Instradare il traffico con Gestione traffico di Azure][trafficmanager]
**Backup e ripristino** | [Eseguire il backup di un'app Web in ][backup] e [ripristinare un'app Web in Servizio app di Azure][restore]

#### Prestazioni

Le prestazioni nel cloud si ottengono prevalentemente tramite la memorizzazione nella cache e la scalabilità orizzontale. È tuttavia necessario considerare anche la memoria, la larghezza di banda e altri attributi dell'hosting delle app Web.

Per | Opzione
------------------------|-----------
**Identificare le funzionalità delle istanze di Servizio app di Azure** | [Dettagli dei prezzi, incluse le funzionalità dei livelli di Servizio app di Azure][websitepricing]
**Risorse di memorizzazione nella cache** | [Cache Redis][rediscache], [Memcache Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/) o una delle altre offerte di memorizzazione nella cache disponibili in [Azure Store](/gallery/store/)
**Scalare l'applicazione** | [Scalare un'app Web in Servizio app di Azure][websitescale] e usare il [routing a disponibilità elevata di ClearDB][cleardbscale]. Se si sceglie di ospitare e gestire un'installazione personalizzata di MySQL, provare a usare [MySQL Cluster CGE][cge] per la scalabilità orizzontale

#### Migrazione

Sono disponibili due metodi per eseguire la migrazione di un sito WordPress esistente in Servizio app di Azure.

* **[Esportazione di WordPress][export]**: viene esportato il contenuto del blog, che può quindi essere importato in un nuovo sito WordPress in Servizio app di Azure tramite il [plug-in di importazione di WordPress][import].

	> [AZURE.NOTE] Questa procedura consente di eseguire la migrazione del contenuto, ma non di eventuali plug-in, temi o altre personalizzazioni, che sarà necessario installare di nuovo manualmente.

* **Migrazione manuale**: [eseguire il backup del sito][wordpressbackup] e del [database][wordpressdbbackup], quindi ripristinarli manualmente in un'app Web di Servizio app di Azure e nel database MySQL associato per eseguire la migrazione di siti con un elevato livello di personalizzazione senza doversi preoccupare di installare manualmente plug-in, temi e altre personalizzazioni.

## Istruzioni dettagliate

### Creare un nuovo sito WordPress

1. Usare [Azure Store][cdbnstore] per creare un database MySQL delle dimensioni identificate nella sezione [Architettura e pianificazione](#planning) nell'area o nelle aree in cui si ospiterà il sito.

2. Seguire la procedura descritta nell'argomento relativo alla [creazione di un'app Web WordPress in Servizio app di Azure][createwordpress] per creare una nuova app Web WordPress. Durante la creazione dell'app Web, selezionare **Utilizzare un database MySQL esistente** e scegliere il database creato nel passaggio 1.

Se si esegue la migrazione di un sito WordPress esistente, vedere l'argomento relativo alla [migrazione di un sito WordPress esistente in Azure](#Migrate-an-existing-WordPress-site-to-Azure) dopo aver creato una nuova app Web.

### Eseguire la migrazione di un sito WordPress esistente in Azure

Come accennato nella sezione [Architettura e pianificazione](#planning), vi sono due modi per eseguire la migrazione di un sito WordPress.

* **Esportazione e importazione** - Per i siti senza molte personalizzazioni o nel caso in cui si voglia trasferire solo il contenuto.

* **Backup e ripristino** - Per i siti con molte personalizzazioni, nel caso in cui si voglia trasferire tutto.

Usare una delle sezioni seguenti per eseguire la migrazione del sito.

#### Metodo di esportazione e importazione

1. Usare la funzione di [esportazione di WordPress][export] per esportare il sito esistente.

2. Creare una nuova app Web seguendo la procedura riportata nella sezione [Creare un nuovo sito WordPress](#Create-a-new-WordPress-site) .

3. Accedere al sito WordPress nelle app Web e fare clic su **Plug-in** -> **Aggiungi nuovo**. Cercare e installare il plug-in di **importazione di WordPress**.

4. Dopo l'installazione del plug-in, fare clic su **Strumenti** -> **Importa** e quindi selezionare **WordPress** per usare il plug-in di importazione di WordPress.

5. Nella pagina per l'importazione di WordPress, fare clic sull'opzione per la scelta dei file. Passare al file WXR esportato dal sito WordPress esistente e quindi scegliere l'opzione per caricare il file e importare.

6. Fare clic su **Invia**. Verrà visualizzata la conferma della corretta esecuzione dell'importazione.

8. Dopo aver completato tutti questi passaggi, riavviare il sito dal relativo pannello dell'app Web nel [portale di Azure][mgmtportal].

Dopo l'importazione del sito, può essere necessario eseguire i passaggi seguenti per abilitare impostazioni non contenute nel file di importazione.

Se si usano... | Effettuare l'operazione seguente...
------------------ | ----------
**Collegamenti permanenti** | Nel dashboard di WordPress del nuovo sito fare clic su **Impostazioni** -> **Collegamenti permanenti** e quindi aggiornare la struttura dei collegamenti permanenti
**Collegamenti a immagini/contenuti multimediali** | Per aggiornare i collegamenti al nuovo percorso, usare il [plug-in Velvet Blues Update URLs][velvet] o uno strumento di ricerca e sostituzione, oppure eseguire l'operazione manualmente nel database
**Temi** | Passare ad **Aspetto** -> **Tema** e aggiornare il tema del sito come necessario
**Menu** | Se il tema supporta i menu, i collegamenti alla home page potrebbero ancora contenere la vecchia sottodirectory. Passare ad **Aspetto** -> **Menu** e aggiornarli

#### Metodo di backup e ripristino

1. Eseguire il backup del sito WordPress esistente in base alle informazioni disponibili nella pagina relativa ai [backup di WordPress][wordpressbackup].

2. Eseguire il backup del database esistente in base alle informazioni disponibili nella pagina relativa al [backup del database][wordpressdbbackup].

3. Creare un nuovo database e ripristinare il backup.

	1. Acquistare un nuovo database in [Azure Store][cdbnstore] oppure impostare un database MySQL in una macchina virtuale [Windows][mysqlwindows] o [Linux][mysqllinux].

	2. Usando un client MySQL come [MySQL Workbench][workbench], connettersi al nuovo database e importare il database WordPress.

	3. Aggiornare il database per cambiare le voci di dominio in base al nuovo dominio di Servizio app di Azure, ad esempio mywordpress.azurewebsites.net. Usare lo [script di ricerca e sostituzione per database WordPress][searchandreplace] per cambiare in modo sicuro tutte le istanze.

4. Creare una nuova app Web nel portale di Azure e pubblicare il backup di WordPress.

	1. Creare una nuova app Web nel [portale di Azure][mgmtportal] con un database mediante **Nuovo** -> **Web e dispositivi mobili** -> **Azure Marketplace** -> **App Web** -> **App Web ed SQL** (o **App Web e MySQL**) -> **Crea**. Configurare tutte le impostazioni necessarie per creare un'app Web vuota.

	2. Nel backup di WordPress individuare il file **wp-config.php** e aprirlo in un editor. Sostituire le voci seguenti con le informazioni del nuovo database MySQL.

		* **DB_NAME** - Nome utente del database

		* **DB_USER** - Nome utente usato per accedere al database

		* **DB_PASSWORD** - Password utente

		Dopo aver cambiato queste voci, salvare e chiudere il file **wp-config.php**.

	3. Fare riferimento alle informazioni disponibili nell'argomento relativo alla [distribuzione di un'app Web in Servizio app di Azure][deploy] per abilitare il metodo di distribuzione che si desidera usare, quindi distribuire il backup di WordPress nell'app Web in Servizio app di Azure.

5. Dopo la distribuzione del sito WordPress, dovrebbe essere possibile accedere al nuovo sito (come app Web di Servizio app di Azure) usando l'URL *.azurewebsite.net.

### Configurare il sito

Dopo la creazione o la migrazione del sito WordPress, usare le informazioni seguenti per migliorare le prestazioni o abilitare altre funzionalità.

Per | Opzione
------------- | -----------
**Impostare la modalità e la dimensione del piano di Servizio app di Azure e abilitare la scalabilità** | [Scalare un'app Web in Servizio app di Azure][websitescale]
**Abilitare connessioni di database persistenti** <p>Per impostazione predefinita, WordPress non usa connessioni di database permanenti, che potrebbero causare rallentamenti dopo numerose connessioni.</p>  | <ol><li><p>Modificare il file <strong>wp-includes/wp-db.php</strong>.</p></li><li><p>Trovare la riga seguente.</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>Sostituire la riga precedente con quella seguente.</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>Trovare la riga seguente.</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>Sostituire la riga precedente con quella seguente.</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p>Salvare il file <strong>wp-includes/wp-db.php</strong> e ridistribuire il sito.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><p>Queste modifiche possono essere sovrascritte quando WordPress viene aggiornato.</p><p>Per impostazione predefinita, gli aggiornamenti di WordPress sono automatici, ma questa opzione può essere disabilitata modificando il file <strong>wp-config.php</strong> e aggiungendo <code>define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>Un altro modo per gestire gli aggiornamenti consiste nell'usare un processo Web che monitora il file <strong>wp-db.php</strong> ed esegue le suddette modifiche ogni volta che il file viene aggiornato. Per altre informazioni, vedere l'<a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">introduzione ai processi Web</a>.</p></div>
**Migliorare le prestazioni** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Disabilitare il cookie ARR</a>. In questo modo è possibile migliorare le prestazioni quando si esegue WordPress in più istanze delle app Web</p></li><li><p>Abilitare la memorizzazione nella cache. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">È possibile usare Cache Redis</a> (anteprima) con il <a href="https://wordpress.org/plugins/redis-object-cache/">plug-in di WordPress per la cache oggetti Redis</a> oppure usare una delle altre offerte di memorizzazione nella cache disponibili in <a href="/gallery/store/">Azure Store</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Aumentare la velocità di WordPress con Wincache</a>. Wincache è abilitata per impostazione predefinita per le app Web</p></li><li><p><a href="../web-sites-scale/">Scalare un'app Web in Servizio app di Azure</a> e usare il <a href="http://www.cleardb.com/developers/cdbr/introduction">routing ClearDB a disponibilità elevata</a> o <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li></ul>
**Usare i BLOB per l'archiviazione** | <ol><li><p><a href="../storage-create-storage-account/">Creare un account di archiviazione di Azure</a></p></li><li><p>Informazioni su come <a href="../cdn-how-to-use/">usare la rete CDN</a> per distribuire geograficamente i dati archiviati in BLOB.</p></li><li><p>Installare e configurare il <a href="https://wordpress.org/plugins/windows-azure-storage/">plug-in di archiviazione di Azure per WordPress</a>.</p><p>Per informazioni dettagliate sull'installazione e la configurazione del plug-in, vedere il <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">manuale dell'utente</a>.</p> </li></ol>
**Abilitare la posta elettronica** | <ol><li><p><a href="/gallery/store/sendgrid/sendgrid-azure/">Abilitare SendGrid con Azure Store</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Installare il plug-in SendGrid per WordPress</a></p></li></ol>
**Configurare un nome di dominio personalizzato** | [Configurare un nome di dominio personalizzato in Servizio app di Azure][customdomain]
**Abilitare HTTPS per un nome di dominio personalizzato** | [Abilitare HTTPS per un'app Web in Servizio app di Azure][httpscustomdomain]
**Bilanciare il carico o distribuire geograficamente il sito** | [Instradare il traffico con Gestione traffico di Azure][trafficmanager]. Se si usa un dominio personalizzato, vedere l'argomento relativo alla [configurazione di un nome di dominio personalizzato in Servizio app di Azure][customdomain] per informazioni sull'uso di Gestione traffico con nomi di dominio personalizzati
**Abilitare i backup automatizzati** | [Eseguire il backup di un'app Web in Servizio app di Azure][backup]
**Abilitare la registrazione diagnostica** | [Abilitare la registrazione diagnostica per le app Web in Servizio app di Azure][log]

## Passaggi successivi

* [Ottimizzazione di WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Conversione di WordPress in un multisito in Servizio app di Azure](web-sites-php-convert-wordpress-multisite.md)

* [Aggiornamento guidato di ClearDB per Azure](http://www.cleardb.com/store/azure/upgrade)

* [Hosting di WordPress in una sottocartella dell'app Web in Servizio app di Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Procedura dettagliata: Creare un sito WordPress con Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Ospitare il blog WordPress esistente in Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Abilitare collegamenti permanenti efficaci in WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Come eseguire la migrazione e gestire il blog WordPress in Servizio app di Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Come eseguire gratuitamente WordPress in Servizio app di Azure](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress in Azure in un massimo di 2 minuti](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Trasferimento di un blog WordPress in Azure - Parte 1: Creazione di un blog WordPress in Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Trasferimento di un blog WordPress in Azure - Parte 2: Trasferimento del contenuto](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Trasferimento di un blog WordPress in Azure - Parte 3: Configurazione di un dominio personalizzato](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Trasferimento di un blog WordPress in Azure - Parte 4: Collegamenti permanenti efficaci e regole di riscrittura degli URL](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Trasferimento di un blog WordPress in Azure - Parte 5: Trasferimento da una sottocartella alla radice](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Come configurare un'app Web WordPress nel proprio account Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Supporto di WordPress in Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Suggerimenti per WordPress in Azure](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Servizio app di Azure e relativo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida sul passaggio dal vecchio al nuovo portale, vedere: [Informazioni di riferimento per l'esplorazione del portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

[performance-diagram]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: /blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: virtual-machines-mysql-windows-server-2008r2.md
[mysqllinux]: virtual-machines-linux-mysql-use-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: install-configure-powershell.md
[xplat-cli]: xplat-cli.md
[storesendgrid]: /gallery/store/sendgrid/sendgrid-azure/
[cdn]: cdn-how-to-use.md


<!--HONumber=52-->