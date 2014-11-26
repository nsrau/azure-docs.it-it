<properties title="Enterprise class WordPress on Azure Websites" pageTitle="Enterprise class WordPress on Azure Websites" description="Learn how to host an enterprise class WordPress site on Azure Websites" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="larryfr" videoId="" scriptId="" />

## WordPress di livello aziendale in Siti Web di Azure

Siti Web di Azure rende disponibile è un ambiente scalabile, sicuro e facile da usare per siti [WordPress][WordPress] cruciali e su vasta scala. La stessa Microsoft gestisce siti di livello aziendale come i blog di [Office][Office] e [Bing][Bing]. Questo documento illustra come usare Siti Web di Azure per stabilire e mantenere un sito WordPress di livello aziendale, basato sul cloud, in grado di gestire un numero elevato di visitatori.

## Contenuto dell'articolo

-   [Architettura e pianificazione][Architettura e pianificazione]: informazioni sull'architettura, i requisiti e le considerazioni sulle prestazioni da valutare prima di creare il sito

-   [Procedure][Procedure] per creare, distribuire e configurare il sito

-   [Altre risorse][Altre risorse]: risorse e informazioni aggiuntive

## <span id="plan"></span></a>Architettura e pianificazione

Un'installazione di base di WordPress prevede solo due requisiti.

-   **Database MySQL**, disponibile tramite [ClearDB in Azure Store][ClearDB in Azure Store]. In alternativa, è possibile gestire un'installazione personalizzata di MySQL in Macchine virtuali di Azure con [Windows][Windows] o [Linux][Linux].

    > [WACOM.NOTE] ClearDB offre diverse configurazioni di MySQL, con caratteristiche di prestazioni differenti per ognuna di esse. Per informazioni sulle offerte fornite tramite Azure Store, vedere [Azure Store][ClearDB in Azure Store] oppure per le offerte fornite direttamente da ClearDB, vedere la pagina dei [prezzi di ClearDB][prezzi di ClearDB].

-   **PHP 5.2.4 o versione successiva**: Siti Web di Azure fornisce attualmente le [versioni 5.3, 5.4 e 5.5 di PHP][versioni 5.3, 5.4 e 5.5 di PHP].

    > [WACOM.NOTE] È consigliabile eseguire sempre la versione più recente di PHP per assicurarsi di avere gli ultimi aggiornamenti per la sicurezza.

### Distribuzione di base

Con i soli requisiti di base, è possibile creare una soluzione di base all'interno di un'area di Azure.

![an Azure Website and MySQL Database hosted in a single Azure region][an Azure Website and MySQL Database hosted in a single Azure region]

Anche se questa soluzione offre scalabilità orizzontale per l'applicazione tramite la creazione di più istanze del sito Web, tutti i componenti sono ospitati all'interno dei data center di una specifica area geografica. I visitatori residenti all'esterno di quest'area possono riscontrare tempi di risposta rallentati quando usano il sito e, se si verifica un blocco dei data center nell'area, anche l'applicazione viene interrotta.

### Distribuzione in più aree

Con [Gestione traffico][Gestione traffico] di Azure, è possibile scalare il sito WordPress in più aree geografiche, fornendo ai visitatori un unico URL. Tutti i visitatori accedono tramite Gestione traffico e vengono quindi instradati in un'area in base alla configurazione del bilanciamento del carico.

![an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions][an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions]

All'interno di ogni area, il sito WordPress viene ancora scalato tra più istanze del sito Web, ma la scalabilità è specifica per l'area, ossia può essere diversa per le aree a traffico elevato rispetto a quelle a traffico ridotto.

La replica e il routing in più database MySQL possono essere eseguiti con il [router CDBR a disponibilità elevata][router CDBR a disponibilità elevata] di ClearDB, illustrato a sinistra, oppure con [MySQL Cluster CGE][MySQL Cluster CGE].

### Multi-region deployment with media storage and caching

Se il sito accetta caricamenti o ospita file multimediali, usare l'archiviazione BLOB di Azure. Se sono necessarie soluzioni di memorizzazione nella cache, in [Azure Store][Azure Store] sono disponibili [Cache Redis][Cache Redis], [Memcache Cloud][Memcache Cloud], [MemCachier][MemCachier] e altre offerte.

![an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN][an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN]

L'archiviazione BLOB è distribuita geograficamente in più aree per impostazione predefinita, quindi non è necessario occuparsi della replica dei file in tutti i siti. È anche possibile abilitare la [Rete di distribuzione di contenuti (CDN)][Rete di distribuzione di contenuti (CDN)] di Azure per l'archiviazione BLOB, che distribuisce i file ai nodi finali più vicini ai visitatori.

### Pianificazione

#### Requisiti aggiuntivi

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Per</th>
<th align="left">Opzione</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>Caricamento o archiviazione di file di grandi dimensioni</strong></td>
<td align="left"><a href="https://wordpress.org/plugins/windows-azure-storage/">Plug-in di WordPress per l'uso dell'archiviazione BLOB</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Invio di posta elettronica</strong></td>
<td align="left"><a href="http://azure.microsoft.com/it-it/gallery/store/sendgrid/sendgrid-azure/">SendGrid</a> e il <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">plug-in di WordPress per l'uso di SendGrid</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Nomi di dominio personalizzati</strong></td>
<td align="left"><a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-custom-domain-name/">Nomi di dominio personalizzati con Siti Web di Azure</a></td>
</tr>
<tr class="even">
<td align="left"><strong>HTTPS</strong></td>
<td align="left"><a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-configure-ssl-certificate/">Supporto di HTTPS in Siti Web di Azure</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Convalida di preproduzione</strong></td>
<td align="left"><a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-staged-publishing/">Supporto della pubblicazione di gestione temporanea per Siti Web di Azure</a>
<p>Si noti che con il passaggio di un sito dalla gestione temporanea alla produzione viene trasferita anche la configurazione di WordPress. Assicurarsi che tutte le impostazioni siano aggiornate in base ai requisiti del sito di produzione prima di passare il sito di gestione temporanea in produzione.</p></td>
</tr>
<tr class="even">
<td align="left"><strong>Monitoraggio e risoluzione dei problemi</strong></td>
<td align="left"><a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-enable-diagnostic-log/">Registrazione diagnostica con Siti Web di Azure</a> e <a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-monitor/">Monitoraggio di Siti Web di Azure</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Distribuzione del sito</strong></td>
<td align="left"><a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-deploy/">Come distribuire un sito Web di Azure</a></td>
</tr>
</tbody>
</table>

#### Disponibilità e ripristino di emergenza

| Per                                                                  | Opzione                                                                    |
|----------------------------------------------------------------------|----------------------------------------------------------------------------|
| **Bilanciamento del carico** o **distribuzione geografica dei siti** | [Instradare il traffico con Gestione traffico di Azure][Gestione traffico] |
| **Backup e ripristino**                                              | [Backup di Siti Web di Azure][Backup di Siti Web di Azure] e [Ripristino di un sito Web di Azure][Ripristino di un sito Web di Azure]   |

#### Prestazioni

Le prestazioni nel cloud si ottengono prevalentemente tramite la memorizzazione nella cache e la scalabilità orizzontale. È tuttavia necessario considerare anche la memoria, la larghezza di banda e altri attributi dell'hosting di siti Web.

| Per                                                         | Opzione                                                                                                                                                                                                                                                                                     |
|-------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Identificare le funzionalità delle istanze del sito Web** | [Dettagli dei prezzi, incluse le funzionalità in base a dimensioni e modalità del sito Web][Dettagli dei prezzi, incluse le funzionalità in base a dimensioni e modalità del sito Web]                                                                                                                                                                                               |
| **Memorizzare risorse nella cache**                         | [Cache Redis][Cache Redis], [Memcache Cloud][Memcache Cloud], [MemCachier][MemCachier] o una delle altre offerte di memorizzazione nella cache disponibili in [Azure Store][Azure Store]                                                                                                                                                  |
| **Applicare la scalabilità all'applicazione**               | [Applicare la scalabilità a un sito Web di Azure][Applicare la scalabilità a un sito Web di Azure] e [routing a disponibilità elevata di ClearDB][router CDBR a disponibilità elevata]. Se si sceglie di ospitare e gestire un'installazione personalizzata di MySQL, tenere presente [MySQL Cluster CGE][MySQL Cluster CGE] per la scalabilità orizzontale |

#### Migrazione

Sono disponibili due metodi per eseguire la migrazione di un sito WordPress esistente in Siti Web di Azure.

-   **[Esportazione WordPress][Esportazione WordPress]**: vengono esportati i contenuti del blog, che possono poi essere importati in un nuovo sito WordPress in Azure con il [plug-in di importazione di WordPress][plug-in di importazione di WordPress].

    > [WACOM.NOTE] Questa procedura consente di eseguire la migrazione dei contenuti, ma non di eventuali plug-in, temi o altre personalizzazioni, che sarà necessario installare di nuovo manualmente.

-   **Migrazione manuale**: [eseguire il backup del sito][eseguire il backup del sito] e del [database][database], quindi ripristinarli manualmente in un sito Web di Azure e nel database MySQL associato, per eseguire la migrazione di siti ampiamente personalizzati ed evitare il fastidio di installare manualmente plug-in, temi e altre personalizzazioni.

## Procedure

### <span id="create"></span></a>Creare un nuovo sito WordPress

1.  Usare [Azure Store][ClearDB in Azure Store] per creare un database MySQL delle dimensioni identificate nella sezione [Architettura e pianificazione][Architettura e pianificazione], nell'area o nelle aree in cui si ospiterà il sito.

2.  Seguire i passaggi descritti in [Creazione di un sito Web WordPress dalla raccolta in Azure][Creazione di un sito Web WordPress dalla raccolta in Azure] per creare un nuovo sito WordPress. Durante la creazione del sito selezionare **Usare un database MySQL esistente** e scegliere il database creato nel passaggio 1.

Se si esegue la migrazione di un sito WordPress esistente, vedere [Eseguire la migrazione di un sito WordPress esistente][Eseguire la migrazione di un sito WordPress esistente] dopo aver creato un nuovo sito.

### <span id="migrate"></span></a>Eseguire la migrazione di un sito WordPress esistente in Azure

Come accennato nella sezione [Architettura e pianificazione][Architettura e pianificazione], esistono due modi per eseguire la migrazione di un sito Web WordPress.

-   **Esportazione e importazione**, per i siti senza molte personalizzazioni o nel caso in cui si vogliono trasferire solo i contenuti.

-   **Backup e ripristino**, per i siti con molte personalizzazione in cui si vuole trasferire tutto.

Usare una delle sezioni seguenti per eseguire la migrazione del sito.

#### Metodo di esportazione e importazione

1.  Usare la funzione di [esportazione di WordPress][Esportazione WordPress] per esportare il sito esistente.

2.  Creare un nuovo sito Web eseguendo i passaggi descritti nella sezione [Creare un nuovo sito WordPress][Creare un nuovo sito WordPress].

3.  Accedere al sito WordPress in Siti Web di Azure e fare clic su **Plug-in** -\> **Aggiungi nuovo**. Cercare e installare il plug-in **WordPress Importer**.

4.  Dopo l'installazione del plug-in, fare clic su **Strumenti** -\> **Importa** e quindi selezionare **WordPress** per usare il plug-in WordPress Importer.

5.  Nella pagina per importare WordPress fare clic sull'opzione per scegliere il file. Passare al file WXR esportato dal sito WordPress esistente e quindi scegliere l'opzione per caricare il file e importare.

6.  Fare clic su **Submit**. Verrà visualizzata la conferma della corretta esecuzione dell'importazione.

7.  Dopo aver completato tutti questi passaggi, riavviare il sito Web dal relativo dashboard nel [portale di gestione di Azure][portale di gestione di Azure].

Dopo l'importazione del sito, può essere necessario eseguire i passaggi seguenti per abilitare impostazioni non contenute nel file di importazione.

| Se si usano...                                | Effettuare l'operazione seguente:                                                                                                                                                              |
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Collegamenti permanenti**                   | Nel dashboard di WordPress del nuovo sito fare clic su **Settings** -\> **Permalinks** e quindi aggiornare la struttura dei collegamenti permanenti                                            |
| **Collegamenti a immagini/file multimediali** | Per aggiornare i collegamenti al nuovo percorso, usare il plug-in [Velvet Blues Update URLs][Velvet Blues Update URLs] o uno strumento di ricerca e sostituzione oppure eseguire l'operazione manualmente nel database |
| **Temi**                                      | Passare ad **Aspetto** -\> **Temi** e aggiornare il tema del sito Web secondo necessità                                                                                                        |
| **Menu**                                      | Se il tema supporta i menu, nei collegamenti alla home page potrebbe ancora essere incorporata la vecchia sottodirectory. Passare ad **Aspetto** -\> **Menu** e aggiornarli                    |

#### Metodo di backup e ripristino

1.  Eseguire il backup dell'attuale sito WordPress usando le informazioni riportate nella pagina sui [backup di WordPress][eseguire il backup del sito].

2.  Eseguire il backup dell'attuale database usando le informazioni riportate nella pagina sul [backup del database][database].

3.  Creare un nuovo database e ripristinare il backup.

    1.  Acquistare un nuovo database in [Azure Store][ClearDB in Azure Store] oppure configurare un database MySQL in una VM di [Windows][Windows] o [Linux][Linux].

    2.  Usando un client MySQL come [MySQL Workbench][MySQL Workbench], connettersi al nuovo database e importare il database WordPress.

    3.  Aggiornare il database per cambiare le voci di dominio nel nuovo dominio del sito Web di Azure, ad esempio mywordpress.azurewebsites.net. Usare lo [script di ricerca e sostituzione per database WordPress][script di ricerca e sostituzione per database WordPress] per cambiare in modo sicuro tutte le istanze.

4.  Creare un nuovo sito Web e pubblicare il backup di WordPress.

    1.  Creare un nuovo sito Web nel [portale di gestione di Azure][portale di gestione di Azure] con un database scegliendo **Nuovo** -\> **Sito Web** -\> **Creazione personalizzata**. Verrà creato un sito vuoto.

    2.  Nel backup di WordPress individuare il file **wp-config.php** e aprirlo in un editor. Sostituire le voci seguenti con le informazioni del nuovo database MySQL.

        -   **DB\_NAME**: nome utente del database

        -   **DB\_USER**: nome utente per accedere al database

        -   **DB\_PASSWORD**: password utente

        Dopo aver cambiato queste voci, salvare e chiudere il file **wp-config.php**.

    3.  Usare le informazioni riportate in [Come distribuire un sito Web di Azure][Come distribuire un sito Web di Azure] per abilitare il metodo di distribuzione che si desidera usare e quindi distribuire il backup di WordPress nel sito Web di Azure.

5.  Dopo la distribuzione del sito WordPress, dovrebbe essere possibile accedere al nuovo sito usando l'URL \*.azurewebsite.net.

### Configurare il sito

Dopo la creazione o la migrazione del sito WordPress, usare le informazioni seguenti per migliorare le prestazioni o abilitare altre funzionalità.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Per</th>
<th align="left">Opzione</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>Impostare la modalità e le dimensioni del sito Web e abilitare la scalabilità</strong></td>
<td align="left"><a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-scale/">Come applicare la scalabilità ai siti Web</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Abilitare connessioni di database permanenti</strong>
<p>Per impostazione predefinita, WordPress non usa connessioni di database permanenti, che potrebbero causare rallentamenti dopo numerose connessioni.</p></td>
<td align="left"><ol>
<li><p>Modificare il file <strong>wp-includes/wp-db.php</strong>.</p></li>
<li><p>Trovare la riga seguente.</p>
<p><code data-inline="1">$this-&gt;dbh = mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags );</code></p></li>
<li><p>Sostituire la riga precedente con quella seguente.</p>
<p><code>$this-&gt;dbh = mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); if ( false !== $error_reporting ) { /br/&gt;  error_reporting( $error_reporting ); } </code></p></li>
<li><p>Trovare la riga seguente.</p>
<p><code data-inline="1">$this-&gt;dbh = @mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags ); </code></p></li>
<li><p>Sostituire la riga precedente con quella seguente.</p>
<p><code data-inline="1">$this-&gt;dbh = @mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); </code></p></li>
<li><p>Salvare il file <strong>wp-includes/wp-db.php</strong> e ridistribuire il sito.</p></li>
</ol>
<div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>NOTA:</h5><p>Queste modifiche possono essere sovrascritte quando WordPress viene aggiornato.</p><p>Per impostazione predefinita, gli aggiornamenti di WordPress sono automatici, ma questa opzione pu&ograve; essere disabilitata modificando il file <strong>wp-config.php</strong> e aggiungendo <code data-inline="1">define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>Un altro modo per gestire gli aggiornamenti consiste nell'usare un processo Web che monitora il file <strong>wp-db.php</strong> ed esegue le suddette modifiche ogni volta che il file viene aggiornato. Per altre informazioni, vedere l'<a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">introduzione ai processi Web</a>.</p></div></td>
</tr>
<tr class="odd">
<td align="left"><strong>Migliorare le prestazioni</strong></td>
<td align="left"><ul>
<li><p><a href="http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Disabilitare il cookie ARR</a>. In questo modo è possibile migliorare le prestazioni quando si esegue WordPress in più istanze del sito Web</p></li>
<li><p>Abilitare la memorizzazione nella cache. È possibile usare <a href="http://msdn.microsoft.com/it-it/library/azure/dn690470.aspx">Cache Redis</a> (anteprima) con il <a href="https://wordpress.org/plugins/redis-object-cache/">plug-in di WordPress per l'oggetto cache Redis</a> oppure usare una delle altre offerte di memorizzazione nella cache disponibili in <a href="http://azure.microsoft.com/it-it/gallery/store/">Azure Store</a></p></li>
<li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Aumentare la velocità di WordPass con Wincache</a>. Wincache è abilitata per impostazione predefinita per i siti Web</p></li>
<li><p><a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-scale/">Applicare la scalabilità al sito Web di Azure</a> e usare il <a href="http://www.cleardb.com/developers/cdbr/introduction">routing ClearDB a disponibilità elevata</a> o <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li>
</ul></td>
</tr>
<tr class="even">
<td align="left"><strong>Usare i BLOB per l'archiviazione</strong></td>
<td align="left"><ol>
<li><p><a href="http://azure.microsoft.com/it-it/documentation/articles/storage-create-storage-account/">Creare un account di archiviazione di Azure</a></p></li>
<li><p>Leggere come <a href="http://azure.microsoft.com/it-it/documentation/articles/cdn-how-to-use/">usare la rete CDN</a> per distribuire geograficamente i dati archiviati in BLOB.</p></li>
<li><p>Installare e configurare il <a href="https://wordpress.org/plugins/windows-azure-storage/">plug-in di archiviazione di Azure per WordPress</a>.</p>
<p>Per informazioni dettagliate sull'installazione e la configurazione del plug-in, vedere il <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">manuale dell'utente</a>.</p></li>
</ol></td>
</tr>
<tr class="odd">
<td align="left"><strong>Abilitare la posta elettronica</strong></td>
<td align="left"><ol>
<li><p><a href="http://azure.microsoft.com/it-it/gallery/store/sendgrid/sendgrid-azure/">Abilitare SendGrid con Azure Store</a></p></li>
<li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Installare il plug-in SendGrid per WordPress</a></p></li>
</ol></td>
</tr>
<tr class="even">
<td align="left"><strong>Configurare un nome di dominio personalizzato</strong></td>
<td align="left"><a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-custom-domain-name/">Usare un nome di dominio personalizzato per un sito Web di Azure</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Abilitare HTTPS per un nome di dominio personalizzato</strong></td>
<td align="left"><a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-configure-ssl-certificate/">Usare HTTPS con un sito Web di Azure</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Bilanciare il carico o distribuire geograficamente il sito</strong></td>
<td align="left"><a href="http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/">Instradare il traffico con Gestione traffico di Azure</a>. Se si usa un dominio personalizzato, vedere <a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-custom-domain-name/">Configurazione di un nome di dominio personalizzato per un sito Web di Azure</a> per informazioni sull'uso di Gestione traffico con nomi di dominio personalizzati</td>
</tr>
<tr class="odd">
<td align="left"><strong>Abilitare i backup automatici del sito Web</strong></td>
<td align="left"><a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-backup/">Eseguire il backup di siti Web di Azure</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Abilitare la registrazione diagnostica</strong></td>
<td align="left"><a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-enable-diagnostic-log/">Abilitare la registrazione diagnostica per Siti Web di Azure</a></td>
</tr>
</tbody>
</table>

## [][]Risorse aggiuntive

-   [Ottimizzazione di WordPress][Ottimizzazione di WordPress]

-   [Convertire un sito WordPress in un multisito][Convertire un sito WordPress in un multisito]

-   [Aggiornamento guidato di ClearDB per Azure][Aggiornamento guidato di ClearDB per Azure]

-   [Hosting di WordPress in una sottocartella del sito Web di Azure][Hosting di WordPress in una sottocartella del sito Web di Azure]

-   [Procedura dettagliata: Creare un sito WordPress con Azure][Procedura dettagliata: Creare un sito WordPress con Azure]

-   [Ospitare il blog WordPress esistente in Azure][Ospitare il blog WordPress esistente in Azure]

-   [Abilitare collegamenti permanenti efficaci in WordPress][Abilitare collegamenti permanenti efficaci in WordPress]

-   [Come eseguire la migrazione e gestire il blog WordPress in Siti Web di Azure][Come eseguire la migrazione e gestire il blog WordPress in Siti Web di Azure]

-   [Come eseguire gratuitamente WordPress in Siti Web di Azure][Come eseguire gratuitamente WordPress in Siti Web di Azure]

-   [WordPress in Azure in un massimo di 2 minuti][WordPress in Azure in un massimo di 2 minuti]

-   [Trasferimento di un blog WordPress in Azure - Parte 1: Creazione di un blog WordPress in Azure][Trasferimento di un blog WordPress in Azure - Parte 1: Creazione di un blog WordPress in Azure]

-   [Trasferimento di un blog WordPress in Azure - Parte 2: Trasferimento del contenuto][Trasferimento di un blog WordPress in Azure - Parte 2: Trasferimento del contenuto]

-   [Trasferimento di un blog WordPress in Azure - Parte 3: Configurazione di un dominio personalizzato][Trasferimento di un blog WordPress in Azure - Parte 3: Configurazione di un dominio personalizzato]

-   [Trasferimento di un blog WordPress in Azure - Parte 4: Collegamenti permanenti efficaci e regole di riscrittura degli URL][Trasferimento di un blog WordPress in Azure - Parte 4: Collegamenti permanenti efficaci e regole di riscrittura degli URL]

-   [Trasferimento di un blog WordPress in Azure - Parte 5: Trasferimento da una sottocartella alla radice][Trasferimento di un blog WordPress in Azure - Parte 5: Trasferimento da una sottocartella alla radice]

-   [Come configurare un sito Web WordPress nel proprio account Azure][Come configurare un sito Web WordPress nel proprio account Azure]

-   [Supporto di WordPress in Azure][Supporto di WordPress in Azure]

-   [Suggerimenti per WordPress in Azure][Suggerimenti per WordPress in Azure]

  [WordPress]: http://www.microsoft.com/web/wordpress
  [Office]: http://blogs.office.com/
  [Bing]: http://blogs.bing.com/
  [Architettura e pianificazione]: #planning
  [Procedure]: #howto
  [Altre risorse]: #resources
  [ClearDB in Azure Store]: http://www.cleardb.com/store/azure
  [Windows]: http://azure.microsoft.com/it-it/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
  [Linux]: http://azure.microsoft.com/it-it/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
  [prezzi di ClearDB]: http://www.cleardb.com/pricing.view
  [versioni 5.3, 5.4 e 5.5 di PHP]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-php-configure/
  [an Azure Website and MySQL Database hosted in a single Azure region]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
  [Gestione traffico]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
  [an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
  [router CDBR a disponibilità elevata]: http://www.cleardb.com/developers/cdbr/introduction
  [MySQL Cluster CGE]: http://www.mysql.com/products/cluster/
  [Azure Store]: http://azure.microsoft.com/it-it/gallery/store/
  [Cache Redis]: http://msdn.microsoft.com/it-it/library/azure/dn690470.aspx
  [Memcache Cloud]: http://azure.microsoft.com/it-it/gallery/store/garantiadata/memcached/
  [MemCachier]: http://azure.microsoft.com/it-it/gallery/store/memcachier/memcachier/
  [an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
  [Rete di distribuzione di contenuti (CDN)]: http://azure.microsoft.com/it-it/documentation/articles/cdn-how-to-use/
  [Come distribuire un sito Web di Azure]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-deploy/
  [Backup di Siti Web di Azure]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-backup/
  [Ripristino di un sito Web di Azure]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-restore/
  [Dettagli dei prezzi, incluse le funzionalità in base a dimensioni e modalità del sito Web]: https://azure.microsoft.com/it-it/pricing/details/web-sites/
  [Applicare la scalabilità a un sito Web di Azure]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-scale/
  [Esportazione WordPress]: http://en.support.wordpress.com/export/
  [plug-in di importazione di WordPress]: http://wordpress.org/plugins/wordpress-importer/
  [eseguire il backup del sito]: http://codex.wordpress.org/WordPress_Backups
  [database]: http://codex.wordpress.org/Backing_Up_Your_Database
  [Creazione di un sito Web WordPress dalla raccolta in Azure]:  "http://azure.microsoft.com/it-it/documentation/articles/web-sites-php-web-site-gallery/"
  [Eseguire la migrazione di un sito WordPress esistente]: #migrate
  [Creare un nuovo sito WordPress]: #create
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [Velvet Blues Update URLs]: https://wordpress.org/plugins/velvet-blues-update-urls/
  [MySQL Workbench]: http://www.mysql.com/products/workbench/
  [script di ricerca e sostituzione per database WordPress]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
  []: resources
  [Ottimizzazione di WordPress]: http://codex.wordpress.org/WordPress_Optimization
  [Convertire un sito WordPress in un multisito]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-php-convert-wordpress-multisite/
  [Aggiornamento guidato di ClearDB per Azure]: http://www.cleardb.com/store/azure/upgrade
  [Hosting di WordPress in una sottocartella del sito Web di Azure]: http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx
  [Procedura dettagliata: Creare un sito WordPress con Azure]: http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx
  [Ospitare il blog WordPress esistente in Azure]: http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx
  [Abilitare collegamenti permanenti efficaci in WordPress]: http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress
  [Come eseguire la migrazione e gestire il blog WordPress in Siti Web di Azure]: http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/
  [Come eseguire gratuitamente WordPress in Siti Web di Azure]: http://architects.dzone.com/articles/how-run-wordpress-azure
  [WordPress in Azure in un massimo di 2 minuti]: http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/
  [Trasferimento di un blog WordPress in Azure - Parte 1: Creazione di un blog WordPress in Azure]: http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1
  [Trasferimento di un blog WordPress in Azure - Parte 2: Trasferimento del contenuto]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content
  [Trasferimento di un blog WordPress in Azure - Parte 3: Configurazione di un dominio personalizzato]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain
  [Trasferimento di un blog WordPress in Azure - Parte 4: Collegamenti permanenti efficaci e regole di riscrittura degli URL]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules
  [Trasferimento di un blog WordPress in Azure - Parte 5: Trasferimento da una sottocartella alla radice]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root
  [Come configurare un sito Web WordPress nel proprio account Azure]: http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/
  [Supporto di WordPress in Azure]: http://www.johnpapa.net/wordpress-on-azure/
  [Suggerimenti per WordPress in Azure]: http://www.johnpapa.net/azurecleardbmysql/
