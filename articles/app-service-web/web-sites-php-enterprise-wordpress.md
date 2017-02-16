---
title: WordPress di livello aziendale in Servizio app di Azure | Documentazione Microsoft
description: Informazioni su come ospitare un sito WordPress aziendale nel servizio app di Azure
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 22d68588-2511-4600-8527-c518fede8978
ms.service: app-service-web
ms.devlang: php
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
translationtype: Human Translation
ms.sourcegitcommit: 4f37777eb42051921087c90a57138cb4085dd7cd
ms.openlocfilehash: 032d5cbc19113d966b20cec1ef332eb6fdca5708


---
# <a name="enterprise-class-wordpress-on-azure-app-service"></a>WordPress di livello aziendale nel servizio app di Azure
Il Servizio app di Azure rende disponibile un ambiente scalabile, sicuro e facile da usare per siti [WordPress][wordpress] di importanza critica e su vasta scala. Anche Microsoft gestisce siti di livello aziendale come i blog di [Office][officeblog] e [Bing][bingblog]. Questo articolo illustra come usare la funzionalità App Web del Servizio app di Microsoft Azure per stabilire e mantenere un sito WordPress di livello aziendale, basato sul cloud, in grado di gestire un numero elevato di visitatori.

## <a name="architecture-and-planning"></a>Architettura e pianificazione
Un'installazione di base di WordPress prevede solo due requisiti:

* **Database MySQL**: questo requisito è disponibile tramite [ClearDB in Azure Marketplace][cdbnstore]. In alternativa, è possibile gestire l'installazione di MySQL su macchine virtuali di Azure tramite [Windows][mysqlwindows] o [Linux][mysqllinux].

  > [!NOTE]
  > ClearDB prevede diverse configurazioni di MySQL. Ciascuna configurazione presenta diverse caratteristiche di prestazioni. Per informazioni sulle offerte disponibili tramite Azure Store, vedere [Azure Store][cdbnstore] oppure direttamente il [sito Web di ClearDB](http://www.cleardb.com/pricing.view).
  >
  >
* **PHP 5.2.4 o versioni successive**: il Servizio app di Azure fornisce attualmente le [versioni PHP 5.4, 5.5 e 5.6][phpwebsite].

  > [!NOTE]
  > Si raccomanda di eseguire sempre l'ultima versione di PHP in modo da disporre delle correzioni per la protezione più recenti.
  >
  >

### <a name="basic-deployment"></a>Distribuzione di base
Con i soli requisiti di base, è possibile creare una soluzione di base all'interno di un'area di Azure.

![Un'app Web di Azure e il database MySQL ospitati in un'unica area di Azure][basic-diagram]

Anche se questa soluzione offre un aumento delle istanze per l'applicazione tramite la creazione di più istanze delle app Web del sito, tutti i componenti sono ospitati all'interno dei data center di una specifica area geografica. I visitatori residenti all'esterno di quest'area possono riscontrare tempi di risposta rallentati quando usano il sito. Se si verifica un blocco dei data center nell'area, anche l'applicazione viene interrotta.

### <a name="multi-region-deployment"></a>Distribuzione in più aree
Con [Gestione traffico][trafficmanager] di Azure, è possibile ridimensionare il sito WordPress in più aree geografiche, fornendo ai visitatori un unico URL. Tutti i visitatori accedono tramite Gestione traffico e vengono quindi instradati in un'area in base alla configurazione del bilanciamento del carico.

![Un'app Web di Azure, ospitata in più aree, con l'uso del router CDBR a disponibilità elevata per il routing a MySQL tra le aree][multi-region-diagram]

All'interno di ogni area il sito WordPress viene ancora ridimensionato tra più istanze delle app Web, ma la scalabilità è specifica per l'area, ossia può essere diversa per le aree a traffico elevato rispetto a quelle a traffico ridotto.

Per replicare e instradare il traffico a più database MySQL, è possibile usare [router ClearDB a disponibilità elevata (CDBR)][cleardbscale] (mostrati a sinistra) o [MySQL Cluster Carrier Grade Edition (CGE)][cge].

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>Distribuzione in più aree con memorizzazione nella cache e archiviazione di contenuti multimediali
Se il sito accetta caricamenti o ospita file multimediali, usare l'archiviazione BLOB di Azure. Se sono necessarie soluzioni di memorizzazione nella cache, valutare [Cache Redis][rediscache], [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) o una delle altre offerte disponibili in [Azure Store](http://azure.microsoft.com/gallery/store/).

![Un'app Web di Azure, ospitata in più aree, con l'uso del router CDBR a disponibilità elevata per MySQL, con cache gestita, archiviazione BLOB e rete per la distribuzione di contenuti][performance-diagram]

L'archiviazione BLOB è distribuita geograficamente in più aree per impostazione predefinita, quindi non è necessario occuparsi della replica dei file in tutti i siti. È anche possibile abilitare la [rete per la distribuzione di contenuti][cdn] di Azure per l'archiviazione BLOB, che distribuisce i file ai nodi finali più vicini ai visitatori.

### <a name="planning"></a>Pianificazione
#### <a name="additional-requirements"></a>Requisiti aggiuntivi
| Per | Opzione |
| --- | --- |
| **Caricare o archiviare file di grandi dimensioni** |[Plug-in di WordPress per l'uso dell'archiviazione BLOB][storageplugin] |
| **Inviare posta elettronica** |[SendGrid][storesendgrid] e il [plug-in di WordPress per l'uso di SendGrid][sendgridplugin] |
| **Nomi di dominio personalizzati** |[Configurare un nome di dominio personalizzato nel Servizio app di Azure][customdomain] |
| **HTTPS** |[Abilitare HTTPS per un'app Web nel Servizio app di Azure][httpscustomdomain] |
| **Convalida di preproduzione** |[Configurare ambienti di gestione temporanea per le app Web nel Servizio app di Azure][staging] <p>Con il passaggio di un'app Web dalla gestione temporanea alla produzione viene trasferita anche la configurazione di WordPress. Assicurarsi che tutte le impostazioni siano aggiornate in base ai requisiti dell'app di produzione prima di passare dalla gestione temporanea alla produzione.</p> |
| **Monitoraggio e risoluzione dei problemi** |[Abilitare la registrazione diagnostica per le app Web nel Servizio app di Azure][log] e [Monitorare le app Web nel Servizio app di Azure][monitor] |
| **Distribuire il sito** |[Distribuire un'app Web nel Servizio app di Azure][deploy] |

#### <a name="availability-and-disaster-recovery"></a>Disponibilità e ripristino di emergenza
| Per | Opzione |
| --- | --- |
| **Bilanciare il carico dei siti** o **distribuire geograficamente i siti** |[Instradare il traffico con Gestione traffico di Azure][trafficmanager] |
| **Backup e ripristino** |[Eseguire il backup di un'app Web nel Servizio app di Azure][backup] e [Ripristinare un'app Web nel Servizio app di Azure][restore] |

#### <a name="performance"></a>Prestazioni
Le prestazioni nel cloud si ottengono prevalentemente tramite la memorizzazione nella cache e l'aumento delle istanze. È tuttavia necessario considerare anche la memoria, la larghezza di banda e altri attributi dell'hosting delle app Web.

| Per | Opzione |
| --- | --- |
| **Identificare le funzionalità delle istanze del servizio app** |[Dettagli dei prezzi, incluse le funzionalità dei livelli del Servizio app][websitepricing] |
| **Memorizzare risorse nella cache** |[Cache Redis][rediscache], [Memcache Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/), o una delle altre offerte di memorizzazione nella cache disponibili in [Azure Store](/gallery/store/) |
| **Scalare l'applicazione** |[Ridimensionare un'app Web nel Servizio app di Azure][websitescale] e [Routing ClearDB a disponibilità elevata][cleardbscale]. Se si sceglie di ospitare e gestire un'installazione personalizzata di MySQL, tenere presente [MySQL Cluster CGE][cge] per la scalabilità orizzontale. |

#### <a name="migration"></a>Migrazione
Sono disponibili due metodi per eseguire la migrazione di un sito WordPress esistente nel Servizio app di Azure:

* **[Esportazione di WordPress][export]**: questo metodo consente di esportare il contenuto del blog. È quindi possibile importare il contenuto in un nuovo sito WordPress nel Servizio app di Azure tramite il [plug-in WordPress Importer][import].

  > [!NOTE]
  > Questa procedura consente di eseguire la migrazione del contenuto, ma non di eventuali plug-in, temi o altre personalizzazioni. È necessario reinstallare questi componenti manualmente.
  >
  >
* **Migrazione manuale**: [eseguire il backup del sito ][wordpressbackup] e del [database][wordpressdbbackup], quindi eseguire il ripristino manuale in un'app Web nel Servizio app di Azure e database MySQL associato. Questo metodo è utile per eseguire la migrazione di siti altamente personalizzati in quanto evita la necessità di installare manualmente plug-in, temi o altre personalizzazioni.

## <a name="step-by-step-instructions"></a>Istruzioni dettagliate
### <a name="create-a-wordpress-site"></a>Creare un sito WordPress
1. Usare [Azure Marketplace][cdbnstore] per creare un database MySQL delle dimensioni identificate nella sezione [Architettura e pianificazione](#planning) nell'area o nelle aree in cui si ospiterà il sito.
2. Seguire la procedura descritta in [Creare un'app WordPress nel Servizio app di Azure][createwordpress] per creare una nuova app Web WordPress. Durante la creazione dell'app Web, selezionare **Utilizzare un database MySQL esistente** e scegliere il database creato nel passaggio 1.

Se si esegue la migrazione di un sito WordPress esistente, vedere [Eseguire la migrazione di un sito WordPress esistente in Azure](#Migrate-an-existing-WordPress-site-to-Azure) dopo aver creato una nuova app Web.

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>Eseguire la migrazione di un sito WordPress esistente in Azure
Come accennato nella sezione [Architettura e pianificazione](#planning) esistono due modi per eseguire la migrazione di un sito Web WordPress:

* **Esportazione e importazione**, per i siti senza molte personalizzazioni o nel caso in cui si vogliano trasferire solo i contenuti.
* **Backup e ripristino**, per i siti con molte personalizzazioni in cui si vuole trasferire tutto.

Usare una delle sezioni seguenti per eseguire la migrazione del sito.

#### <a name="the-export-and-import-method"></a>Metodo di esportazione e importazione
1. Usare la funzione di [esportazione di WordPress][export] per esportare il sito esistente.
2. Creare un'app Web seguendo la procedura riportata nella sezione [Creare un sito WordPress](#Create-a-new-WordPress-site).
3. Accedere al sito WordPress nel [portale di Azure][mgmtportal] e fare clic su **Plug-in** > **Aggiungi nuovo**. Cercare e installare il plug-in **WordPress Importer**.
4. Dopo l'installazione del plug-in, fare clic su **Strumenti** > **Importa** e quindi selezionare **WordPress** per usare il plug-in WordPress Importer.
5. Nella pagina ****per importare WordPress fare clic sull'opzione ****per scegliere il file. Passare al file WXR esportato dal sito WordPress esistente e quindi scegliere l'opzione per **caricare il file e importare**.
6. Fare clic su **Submit**. Viene visualizzata la conferma della corretta esecuzione dell'importazione.
7. Dopo aver completato tutti questi passaggi, riavviare il sito dal relativo pannello dei **Servizi app** nel [portale di Azure][mgmtportal].

Dopo l'importazione del sito, può essere necessario eseguire i passaggi seguenti per abilitare impostazioni non contenute nel file di importazione.

| Se si usano... | Effettuare l'operazione seguente: |
| --- | --- |
| **Collegamenti permanenti** |Nel dashboard di WordPress del nuovo sito fare clic su **Impostazioni** > **Permalinks** (Collegamenti permanenti) e quindi aggiornare la struttura dei collegamenti permanenti. |
| **Collegamenti a immagini/file multimediali** |Per aggiornare i collegamenti al nuovo percorso, usare il [plug-in Velvet Blues Update URLs ][velvet], uno strumento di ricerca e sostituzione oppure eseguire l'operazione manualmente nel database. |
| **Temi** |Passare ad **Aspetto** > **Tema** e aggiornare il tema del sito come necessario. |
| **Menu** |Se il tema supporta i menu, nei collegamenti alla home page potrebbe ancora essere incorporata la vecchia sottodirectory. Passare ad **Aspetto** > **Menu** e aggiornarli. |

#### <a name="the-backup-and-restore-method"></a>Metodo di backup e ripristino
1. Eseguire il backup dell'attuale sito WordPress usando le informazioni riportate nella pagina sui [backup di WordPress][wordpressbackup].
2. Eseguire il backup dell'attuale database usando le informazioni riportate nella pagina sul [backup del database][wordpressdbbackup].
3. Creare un database e ripristinare il backup.

   1. Acquistare un nuovo database da [Azure Marketplace][cdbnstore] oppure impostare un database MySQL in una macchina virtuale [Windows][mysqlwindows] o [Linux][mysqllinux].
   2. Usando un client MySQL, come [MySQL Workbench][workbench], connettersi al nuovo database e importare il database WordPress.
   3. Aggiornare il database per cambiare le voci di dominio in base al nuovo dominio del Servizio app di Azure, ad esempio mywordpress.azurewebsites.net. Usare lo [script di ricerca e sostituzione per database WordPress][searchandreplace] per cambiare in modo sicuro tutte le istanze.
4. Creare un'app Web nel portale di Azure e pubblicare il backup di WordPress.

   1. Creare un'app Web nel [portale di Azure][mgmtportal] con un database mediante **Nuovo** > **Web e dispositivi mobili** > **Azure Marketplace** > **App Web** > **App Web e SQL** (o **App Web e MySQL**) -> **Crea**. Configurare tutte le impostazioni necessarie per creare un'app Web vuota.
   2. Nel backup di WordPress individuare il file **wp-config.php** e aprirlo in un editor. Sostituire le voci seguenti con le informazioni del nuovo database MySQL:

      * **DB_NAME**: nome utente del database.
      * **DB_USER**: nome utente per accedere al database.
      * **DB_PASSWORD**: password utente.

        Dopo aver cambiato queste voci, salvare e chiudere il file **wp-config.php**.
   3. Fare riferimento alle informazioni disponibili in [Distribuire un'app Web nel Servizio app di Azure][deploy] per abilitare il metodo di distribuzione che si desidera usare, quindi distribuire il backup di WordPress nell'app Web nel Servizio app di Azure.
5. Dopo la distribuzione del sito WordPress, dovrebbe essere possibile accedere al nuovo sito (come app Web di Servizio app) usando l'URL *.azurewebsite.net.

### <a name="configure-your-site"></a>Configurare il sito
Dopo la creazione o la migrazione del sito WordPress, usare le informazioni seguenti per migliorare le prestazioni o abilitare altre funzionalità.

| Per | Opzione |
| --- | --- |
| **Impostare la modalità del piano e le dimensioni del servizio app e abilitare la scalabilità** |[Ridimensionare un'app Web nel Servizio app di Azure][websitescale]. |
| **Abilitare connessioni di database permanenti** |Per impostazione predefinita, WordPress non usa connessioni di database permanenti, che potrebbero causare rallentamenti dopo numerose connessioni. Per abilitare le connessioni permanenti, installare il [plug-in di aggiornamento delle connessioni permanenti](https://wordpress.org/plugins/persistent-database-connection-updater/installation/). |
| **Migliorare le prestazioni** |<ul><li><p><a href="https://azure.microsoft.com/en-us/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/">Disabilitare il cookie ARR</a>. In questo modo è possibile migliorare le prestazioni quando si esegue WordPress in più istanze delle app Web.</p></li><li><p>Abilitare il caching. È possibile usare <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Cache Redis</a> (anteprima) con il <a href="https://wordpress.org/plugins/redis-object-cache/">plug-in di WordPress per l'oggetto Cache Redis</a> oppure usare una delle altre offerte di memorizzazione nella cache disponibili in <a href="/gallery/store/">Azure Store</a>.</p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Come rendere più veloce WordPress con WinCache</a>. Lo strumento WinCache è abilitato per impostazione predefinita per le app Web.</p></li><li><p>[Ridimensionare un'app Web nel Servizio app di Azure][websitescale] e usare il <a href="http://www.cleardb.com/developers/cdbr/introduction">routing a disponibilità elevata di ClearDB</a> o <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a>.</p></li></ul> |
| **Usare i BLOB per l'archiviazione** |<ol><li><p>[Creare un account di Archiviazione di Azure](../storage/storage-create-storage-account.md).</p></li><li><p>Leggere come [usare la rete CDN](../cdn/cdn-create-new-endpoint.md) per distribuire geograficamente i dati archiviati in BLOB.</p></li><li><p>Installare e configurare il <a href="https://wordpress.org/plugins/windows-azure-storage/">plug-in di archiviazione di Azure per WordPress</a>.</p><p>Per informazioni dettagliate su installazione e configurazione del plug-in, vedere il <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">manuale dell'utente</a>.</p> </li></ol> |
| **Abilitare la posta elettronica** |Abilitare <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a> con Azure Store. Installare il <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">plug-in SendGrid</a> per WordPress. |
| **Configurare un nome di dominio personalizzato** |[Configurare un nome di dominio personalizzato nel Servizio app di Azure][customdomain]. |
| **Abilitare HTTPS per un nome di dominio personalizzato** |[Abilitare HTTPS per un'app Web nel Servizio app di Azure][httpscustomdomain]. |
| **Bilanciare il carico o distribuire geograficamente il sito** |[Instradare il traffico con Gestione traffico di Azure][trafficmanager]. Se si usa un dominio personalizzato, vedere [Configurare un nome di dominio personalizzato nel Servizio app di Azure][customdomain] per informazioni sull'uso di Gestione traffico con nomi di dominio personalizzati. |
| **Abilitare i backup automatici** |[Eseguire il backup di un'app Web nel Servizio app di Azure][backup]. |
| **Abilitare la registrazione diagnostica** |[Abilitare la registrazione diagnostica per le app Web nel Servizio app di Azure][log]. |

## <a name="next-steps"></a>Passaggi successivi
* [Ottimizzazione di WordPress](http://codex.wordpress.org/WordPress_Optimization)
* [Convertire WordPress in un multisito nel Servizio app di Azure](web-sites-php-convert-wordpress-multisite.md)
* [Aggiornamento guidato di ClearDB per Azure](http://www.cleardb.com/store/azure/upgrade)
* [Hosting di WordPress in una sottocartella dell'app Web nel servizio app di Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)
* [Procedura dettagliata: Creare un sito WordPress con Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)
* [Ospitare il blog WordPress esistente in Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)
* [Abilitare collegamenti permanenti efficaci in WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)
* [Come eseguire la migrazione e gestire il blog WordPress nel servizio app di Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)
* [Come eseguire gratuitamente WordPress nel servizio app di Azure](http://architects.dzone.com/articles/how-run-wordpress-azure)
* [WordPress in Azure in un massimo di due minuti](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)
* [Trasferimento di un blog WordPress in Azure - Parte 1: Creazione di un blog WordPress in Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)
* [Trasferimento di un blog WordPress in Azure - Parte 2: Trasferimento dei contenuti](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)
* [Trasferimento di un blog WordPress in Azure - Parte 3: Configurazione del dominio personalizzato](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)
* [Trasferimento di un blog WordPress in Azure - Parte 4: Collegamenti permanenti efficaci e regole di riscrittura degli URL](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)
* [Trasferimento di un blog WordPress in Azure - Parte 5: Passaggio da una sottocartella alla radice](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)
* [Come configurare un'app Web WordPress nel proprio account Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)
* [Supporto di WordPress in Azure](http://www.johnpapa.net/wordpress-on-azure/)
* [Suggerimenti per WordPress in Azure](http://www.johnpapa.net/azurecleardbmysql/)

> [!NOTE]
> Per iniziare a usare il Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il Servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel Servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
>
>

## <a name="whats-changed"></a>Modifiche apportate
Per una guida relativa al passaggio da siti Web al Servizio App, vedere [Servizio app di Azure e i servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: ../virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md
[mysqllinux]: ../virtual-machines/virtual-machines-linux-classic-mysql-on-opensuse.md
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
[posh]: /powershell/azureps-cmdlets-docs
[Azure CLI]: ../xplat-cli-install.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md



<!--HONumber=Dec16_HO2-->


