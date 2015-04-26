<properties 
	pageTitle="Introduzione ad Archiviazione | Microsoft Azure" 
	description="Panoramica del servizio Archiviazione di Microsoft Azure." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/11/2014" 
	ms.author="tamram"/>

# Introduzione ad Archiviazione di Microsoft Azure

In questo articolo viene presentato Archiviazione di Microsoft Azure per gli sviluppatori, i professionisti IT e i decisori aziendali. Leggendo l'articolo si apprenderà:

- Che cos'è Archiviazione di Azure e come è possibile usarlo nelle applicazioni cloud, mobili, server e desktop
- Quali tipi di dati è possibile archiviare con i servizio Archiviazione di Azure: Archiviazione BLOB, tabelle, accodamento e file
- Come viene gestito l'accesso ai dati in Archiviazione di Azure
- Come vengono protetti i dati di Archiviazione di Azure tramite la ridondanza e la replica 
- Come procedere per creare la prima applicazione di Archiviazione di Azure

## Informazioni su Archiviazione di Azure ##

Il cloud computing consente di disporre di nuovi scenari per le applicazioni che richiedono un sistema di archiviazione scalabile, durevole e a disponibilità elevata per i propri dati. Questo è esattamente lo scopo per cui è stato sviluppato Archiviazione di Microsoft Azure. Oltre a consentire agli sviluppatori di compilare applicazioni di grande portata per supportare nuovi scenari, Archiviazione di Azure offre anche la base di archiviazione per il servizio Macchine virtuali di Azure, un'ulteriore prova della sua affidabilità. 

Archiviazione di Azure è estremamente scalabile, per consentire l'archiviazione e l'elaborazione di centinaia di terabyte di dati per supportare gli scenari di Big Data necessari per applicazioni scientifiche, di analisi finanziaria e multimediali. In alternativa, è possibile archiviare piccole quantità di dati necessarie per un piccolo sito Web aziendale. Qualsiasi siano le esigenze, il cliente paga solo per i dati archiviati. In Archiviazione di Azure sono al momento archiviate decine di migliaia di miliardi di oggetti univoci dei clienti e sono gestiti in media milioni di richieste al secondo. 

Archiviazione di Azure è un servizio flessibile, per consentire la progettazione di applicazioni per un pubblico vasto e globale e per ridimensionare tali applicazioni in base alle esigenze, sia in termini di quantità di dati archiviati che di numero di richieste effettuate su tali dati. Il cliente paga solo per ciò che usa e solo quando lo usa.

Archiviazione di Azure usa un sistema di partizionamento automatico che applica automaticamente il bilanciamento del carico dei dati in base al traffico. Pertanto, se le richieste dell'applicazione aumentano, vengono automaticamente allocate le risorse appropriate per soddisfare le nuove esigenze. 

Archiviazione di Azure è accessibile da qualsiasi luogo, da qualsiasi tipo di applicazione, sia che venga eseguita nel cloud, in un desktop, in un server locale o in un dispositivo mobile o un tablet. È possibile usare Archiviazione di Azure in scenari mobili in cui l'applicazione archivia un subset di dati nel dispositivo e li sincronizza con un set completo di dati archiviati nel cloud.

Archiviazione di Azure supporta client che usano vari sistemi operativi (inclusi Windows e Linux) e numerosi linguaggi di programmazione (inclusi .NET, Java e C++) per facilitare le operazioni di sviluppo. Espone inoltre le risorse di dati tramite API REST semplici, disponibili per qualsiasi client in grado di inviare e ricevere dati tramite HTTP/HTTPS.

L'archiviazione premium di Azure è ora disponibile in anteprima. L'archiviazione premium di Azure offre prestazioni elevate, supporto disco a bassa latenza per carichi di lavoro con uso intensivo di I/O in esecuzione su Macchine virtuali di Azure. Con l'archiviazione premium di Azure, è possibile collegare più dischi di dati permanenti a una macchina virtuale e configurarli per poter soddisfare i requisiti di prestazioni. Ogni disco di dati è supportato da un disco SSD nell'archiviazione premium di Azure per le massime prestazioni di I/O. Vedere la pagina [Archiviazione premium: Archiviazione ad alte prestazioni per i carichi di lavoro delle macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkId=521898) per altri dettagli. 

## Presentazione dei servizi di archiviazione di Azure ##

Un account di archiviazione di Azure è un account sicuro che consente di accedere ai servizi in Archiviazione di Azure. L'account di archiviazione offre uno spazio dei nomi univoco per le risorse di archiviazione. Sono disponibili due tipi di account di archiviazione:

- Un account di archiviazione standard include l'archiviazione BLOB, tabelle, di accodamento e file.
- Un account di archiviazione premium attualmente supporta solo dischi di macchine virtuali di Azure. L'archiviazione premium di Azure è disponibile su richiesta tramite la [pagina Anteprima di Azure](/it-it/services/preview/).

Prima di creare un account di archiviazione, è necessario effettuare una sottoscrizione di Azure, ovvero a un piano per accedere ai diversi servizi di Azure. È possibile creare un massimo di 100 account di archiviazione con denominazione univoca con una singola sottoscrizione. Vedere la pagina dei [dettagli sui prezzi di Archiviazione](http://azure.microsoft.com/pricing/details/storage/) per informazioni sui prezzi in base al volume.

Per iniziare a usare Azure, è possibile scaricare una [versione di valutazione gratuita](/it-it/pricing/free-trial/). Se si decide di acquistare un piano, è possibile scegliere una delle numerose [opzioni di acquisto](/it-it/pricing/purchase-options/).. Gli [abbonati MSDN](/it-it/pricing/member-offers/msdn-benefits-details/) ricevono crediti mensili gratuiti che possono essere usati con i servizi di Azure, incluso il servizio Archiviazione di Azure.

### Account di archiviazione standard

Un account di archiviazione standard consente di accedere all'archiviazione BLOB, all'archiviazione tabelle, all'archiviazione di accodamento e all'archiviazione file:

- Nell'**archivio BLOB** sono archiviati i dati di file. Un BLOB può essere qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. 
- Nell'**archivio tabelle** sono archiviati set di dati strutturati. L'archiviazione delle tabelle usa un archivio dati chiave-attributo NoSQL, che consente lo sviluppo e l'accesso rapido a grandi quantità di dati.
- L'**archiviazione di accodamento** offre un sistema di messaggistica affidabile per l'elaborazione del flusso di lavoro e per la comunicazione tra i componenti dei servizi cloud.
- L'**archiviazione file (anteprima)** offre uno spazio di archiviazione condiviso per le applicazioni tradizionali che usano il protocollo SMB 2.1 standard. Le macchine virtuali e i servizi cloud di Azure possono condividere dati file tra componenti delle applicazioni tramite le condivisioni montate e le applicazioni locali possono accedere ai dati file in una condivisione tramite l'API REST del servizio file. L'archiviazione file è disponibile su richiesta tramite la [pagina Anteprima di Azure](/it-it/services/preview/). 

Ogni account di archiviazione standard può includere fino a 500 TB di dati combinati di BLOB, code, tabelle e file. Per informazioni sulla capacità dell'account di archiviazione standard, vedere [Obiettivi di scalabilità e prestazioni di Azure](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx).

La figura seguente mostra le relazioni tra le risorse di archiviazione di Azure in un account di archiviazione standard:

![Azure Storage Resources](./media/storage-introduction/storage-concepts.png)

Quando si è pronti a creare un account di archiviazione standard, vedere l'argomento relativo alla [creazione, gestione o eliminazione di un account di archiviazione](storage-create-storage-account.md) per altri dettagli.

### Account di archiviazione premium (anteprima)

L'archiviazione premium di Azure attualmente supporta solo dischi di macchine virtuali di Azure. L'archiviazione premium di Azure è disponibile su richiesta tramite la [pagina Anteprima di Azure](/it-it/services/preview/). Per una panoramica approfondita dell'archiviazione premium di Azure, vedere [Archiviazione premium: Archiviazione ad alte prestazioni per i carichi di lavoro delle macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkId=521898).

## Archiviazione BLOB ##

Per gli utenti che dispongono di grandi quantità di dati non strutturati da archiviare nel cloud, l'archiviazione BLOB offre una soluzione conveniente e scalabile. È possibile usare gli archivi BLOB per archiviare contenuto come:

- Documenti 
- Dati dei social network come foto, video, musica e blog
- Backup di file, computer, database e dispositivi
- Immagini e testo per applicazioni Web
- Dati di configurazione per applicazioni cloud
- Big Data, come log e altri set di dati di grandi dimensioni

Ogni BLOB è organizzato in un contenitore. I contenitori sono inoltre una soluzione utile per assegnare criteri di sicurezza a gruppi di oggetti. Un account di archiviazione può contenere un numero qualsiasi di contenitori e un contenitore può contenere un numero qualsiasi di BLOB, fino a che non viene raggiunto il limite di capacità di 500 TB dell'account di archiviazione.  

Gli archivi BLOB offrono due tipi di BLOB: i BLOB in blocchi e i BLOB di pagine (dischi). I BLOB in blocchi sono ottimizzati per lo streaming e l'archiviazione di oggetti cloud e sono una soluzione adatta per l'archiviazione di documenti, file multimediali, backup e così via. Un BLOB in blocchi può raggiungere una dimensione massima di 200 GB. I BLOB di pagine sono ottimizzati per la rappresentazione di dischi IaaS e per il supporto di scritture casuali. Possono raggiungere una dimensione massima di 1 TB. Un disco IaaS collegato a una rete di macchine virtuali Azure è un disco rigido virtuale archiviato come BLOB di pagine.

Se si dispone di set di dati molto grandi e i vincoli della rete ne impediscono il caricamento o il download in archivi BLOB tramite una connessione, è possibile spedire un'unità disco rigido a Microsoft per importare o esportare i dati direttamente dal data center tramite il [Servizio Importazione/Esportazione di Azure](../articles/storage-import-export-service/). È inoltre possibile copiare i dati BLOB all'interno del proprio account di archiviazione o tra account di archiviazione diversi. 

## Archiviazione tabelle ##

Le applicazioni moderne spesso richiedono l'uso di archivi dati con scalabilità e flessibilità maggiori di quelli delle versioni precedenti del software. Il servizio di archiviazione tabelle offre un archivio a disponibilità elevata e altamente scalabile, per consentire la scalabilità automatica dell'applicazione in base alle richieste degli utenti. Archiviazione tabelle è l'archivio chiave-attributo NoSQL di Microsoft. Ha una struttura senza schema, il che lo rende diverso dai database relazionali tradizionali. Con un archivio dati senza schema è facile adattare i dati con il variare delle esigenze dell'applicazione. L'archiviazione tabelle è semplice da usare e consente di velocizzare lo sviluppo e la creazione di applicazioni. L'accesso ai dati è rapido e conveniente per tutti i tipi di applicazione.  L'archiviazione tabelle presenta in genere costi decisamente più bassi rispetto alle soluzioni SQL tradizionali per volumi simili di dati.

Archiviazione tabelle è un archivio chiave-attributo, ovvero ogni valore di una tabella è archiviato con un nome di proprietà tipizzato che può essere usato per filtrare i dati e specificare i criteri di selezione. Una raccolta di proprietà con i rispettivi valori costituisce un'entità. Poiché l'archiviazione tabelle non ha uno schema, due entità della stessa tabella possono contenere raccolte diverse di proprietà, che possono essere di tipi diversi. 

È possibile usare l'archiviazione tabelle per archiviare set di dati flessibili, ad esempio i dati utente per le applicazioni Web, le rubriche, le informazioni sui dispositivi e altri tipi di metadati richiesti dal servizio.  In una tabella possono essere archiviate il numero desiderato di tabelle e un account di archiviazione può contenere un numero qualsiasi di tabelle, fino a che non viene raggiunto il limite di capacità dell'account di archiviazione.

In modo analogo ai BLOB e alle code, gli sviluppatori possono gestire e accedere all'archivio tabelle tramite protocolli REST standard, anche se l'archiviazione tabelle supporta anche un subset del protocollo OData, per semplificare le funzionalità di query avanzate e abilitare i formati JSON e AtomPub (basato su XML).

Per le attuali applicazioni basate su Internet, i database NoSQL come l'archivio tabelle offrono una comoda alternativa ai database relazionali tradizionali. 

## Archiviazione di accodamento ##

Durante la progettazione di applicazioni scalabili, i componenti dell'applicazione vengono spesso separati, per poter essere scalati in modo indipendente. L'archiviazione di accodamento offre una soluzione di messaggistica affidabile per la comunicazione asincrona tra i componenti dell'applicazione, che possono essere eseguiti nel cloud, in un desktop, in un server locale o in un dispositivo mobile. Supporta inoltre la gestione di attività asincrone e la creazione di flussi di lavoro dei processi. 

Un account di archiviazione può contenere il numero desiderato di code. Una coda può contenere un numero qualsiasi di messaggi, fino al raggiungimento del limite di capacità dell'account di archiviazione. I singoli messaggi possono avere una dimensione massima di 64 KB.

## Archiviazione file (anteprima) ##

Molte applicazioni legacy sono basate su condivisioni file, una dipendenza che si è complicata a seguito dello spostamento delle applicazioni nel cloud. Archiviazione file offre condivisioni file basate sul cloud per consentire la migrazione delle applicazioni legacy ad Azure in modo veloce e senza costi di riscrittura. 

Le applicazioni in esecuzione nelle macchine virtuali o nei servizi cloud di Azure possono montare una condivisione di archiviazione file per accedere a dati file, come se si montasse una condivisione SMB tipica per un'applicazione desktop. Non ci sono limiti per i componenti delle applicazioni che possono montare e accedere contemporaneamente alla condivisione di archiviazione file.

Poiché una condivisione di archiviazione file è una condivisione file SMB 2.1 standard, le applicazioni in esecuzione in Azure possono accedere ai dati nella condivisione tramite le API di I/O del file system. Gli sviluppatori possono quindi riusare il codice esistente e le competenze acquisite per eseguire la migrazione delle applicazioni esistenti. I professionisti IT possono usare i cmdlet di PowerShell per creare, montare e gestire condivisioni di archiviazione file nell'ambito delle attività di amministrazione per le applicazioni Azure.

Come altri servizi di archiviazione di Azure, archiviazione file espone un'API REST per l'accesso ai dati in una condivisione. Le applicazioni locali possono chiamare l'API REST di archiviazione file per accedere ai dati in una condivisione file. Le aziende possono quindi decidere di eseguire la migrazione di alcune applicazioni legacy ad Azure e di continuare a eseguirne altre dall'interno dell'organizzazione locale. Si noti che è possibile montare una condivisione file solo per le applicazioni in esecuzione in Azure. Le applicazioni locali possono accedere alla condivisione file solo tramite l'API REST.

Le applicazioni distribuite possono anche usare l'archiviazione file per archiviare e condividere dati di applicazioni e strumenti di sviluppo e test utili. Ad esempio, un'applicazione può archiviare file di configurazione e dati di diagnostica quali log, metriche e dump di arresto anomalo del sistema in una condivisione di archiviazione file in modo che siano disponibili per più macchine virtuali o ruoli. Gli sviluppatori e gli amministratori possono archiviare le utilità necessarie per compilare o gestire un'applicazione in una condivisione di archiviazione file disponibile per tutti i componenti anziché installarle in ogni macchina virtuale o istanza del ruolo.


## Accedere a risorse BLOB, tabelle, accodamento e file ##

Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere alle risorse dell'account. Per garantire la sicurezza dei dati, ogni richiesta di risorse dell'account deve essere autenticata. L'autenticazione si basa su un modello di chiave condivisa. I BLOB possono inoltre essere configurati per supportare l'autenticazione anonima. 

Durante la creazione dell'account di archiviazione, vengono assegnate due chiavi di accesso private usate per l'autenticazione. La presenza di due chiavi garantisce la disponibilità dell'applicazione quando si rigenerano le chiavi nell'ambito delle normali operazioni di gestione delle chiavi di sicurezza.

Se è necessario consentire agli utenti l'accesso controllato alle risorse di archiviazione, è possibile creare una [firma di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md). Una firma di accesso condiviso è un token che può essere aggiunto a un URL per consentire l'accesso delegato a un contenitore, BLOB, tabella o coda. Coloro che possiedono il token possono accedere alla risorsa a cui esso fa riferimento con le autorizzazioni specificate e per il periodo di validità del token stesso. Si noti che l'archiviazione file di Azure attualmente non supporta le firme di accesso condiviso.

È infine possibile specificare che un contenitore e i relativi BLOB o che un BLOB specifico sia disponibile per l'accesso pubblico. Quando si indica che un contenitore o un BLOB è pubblico, qualsiasi utente lo potrà leggere in forma anonima, in quanto non è necessaria l'autenticazione.  I contenitori e i BLOB pubblici sono utili per esporre risorse come file multimediali e documenti ospitati in siti Web.  Per ridurre la latenza di rete per un pubblico globale, è possibile memorizzare nella cache i dati BLOB usati dai siti Web con la rete CDN di Azure.

## Replica per garantire la durabilità e la disponibilità elevata ##

[AZURE.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

## Prezzi ##

I costi addebitati ai clienti per Archiviazione di Azure si basano su quattro fattori: la capacità di archiviazione usata, l'opzione di replica selezionata, il numero di richieste effettuate al servizio e i dati in uscita. 

La capacità di archiviazione fa riferimento alla quantità di unità di servizio dell'account di archiviazione in uso per l'archiviazione dei dati. Il costo dell'archiviazione dei dati è determinato dalla quantità di dati archiviata e dalla modalità di replica dei dati. Ogni operazione di lettura e scrittura in Archiviazione di Azure effettua anche una richiesta al servizio. I dati in uscita sono i dati trasferiti al di fuori di un'area di Azure. Quando un'applicazione non in esecuzione nella stessa area geografica accede ai dati nell'account di archiviazione, indipendentemente dal fatto che l'applicazione sia un servizio cloud o un altro tipo di applicazione, verrà addebitato il trasferimento dei dati in uscita. Per i servizi di Azure è possibile raggruppare dati e servizi nello stesso data center per ridurre o eliminare gli addebiti per il trasferimento dei dati e dei processi in uscita. 

Nella pagina [Prezzi di Archiviazione di Azure](/it-it/pricing/details/storage/) vengono fornite informazioni dettagliate sui prezzi per la capacità, la replica e le transazioni relative all'archiviazione. Nella pagina [Dettagli prezzi dei trasferimenti di dati](/it-it/pricing/details/data-transfers/) vengono fornite informazioni dettagliate sui prezzi per il trasferimento dei dati in uscita. È possibile usare la [pagina per il calcolo dei prezzi di Archiviazione di Azure](/it-it/pricing/calculator/?scenario=data-management) per stimare i costi.

## Sviluppo per l'archiviazione ##

Archiviazione di Azure espone risorse di archiviazione tramite un'[API REST](http://msdn.microsoft.com/library/windowsazure/dd179355.aspx) che può essere chiamata da qualsiasi linguaggio in grado di effettuare richieste HTTP/HTTPS. In Archiviazione di Azure sono inoltre disponibili librerie di programmazione per diversi linguaggi comuni. Tali librerie semplificano molti aspetti dell'uso di Archiviazione di Azure gestendo dettagli come la chiamata sincrona e asincrona, l'esecuzione delle operazioni in batch, la gestione delle eccezioni, la ripetizione automatica dei tentativi, il comportamento operativo e così via. Le librerie attualmente disponibili riguardano i linguaggi e le piattaforme seguenti, ma presto ne saranno disponibili altre:

- [.NET](http://go.microsoft.com/fwlink/?LinkID=390731)
- [Codice nativo](http://msdn.microsoft.com/library/dn495438.aspx)
- [Java/Android](/it-it/develop/java/)
- [Node.js](/it-it/develop/nodejs/)
- [PHP](/it-it/develop/php/)
- [Ruby](/it-it/develop/ruby/)
- [Python](/it-it/develop/python/)
- [PowerShell](http://msdn.microsoft.com/library/dn495240.aspx)

## Passaggi successivi ##

Per iniziare a usare Archiviazione di Azure, vedere le risorse seguenti:

### Download

- [Pacchetto NuGet per l'Archiviazione di Azure - Librerie client per .NET, Windows Phone e Windows Runtime](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Azure SDK e strumenti](http://azure.microsoft.com/downloads/)
- [Emulatore di archiviazione di Azure](http://www.microsoft.com/en-in/download/details.aspx?id=43709)

### Codice sorgente

- [Librerie di Archiviazione di Microsoft Azure per .NET](https://github.com/Azure/azure-storage-net)

### Documentazione

- [Documentazione di Archiviazione di Azure](/it-it/documentation/services/storage/)
- [Informazioni di riferimento sulle API REST dei servizi di archiviazione](http://msdn.microsoft.com/library/dd179355.aspx)
- [Introduzione all'utilità della riga di comando AzCopy](http://azure.microsoft.com/documentation/articles/storage-use-azcopy/)

<h3>Per gli utenti PowerShell</h3>
- [Cmdlet di Archiviazione di Azure](http://msdn.microsoft.com/library/azure/dn806401.aspx)

<h3>Per gli sviluppatori .NET</h3>

- [Informazioni di riferimento sulla libreria client .NET](http://msdn.microsoft.com/library/wa_storage_30_reference_home.aspx)
- [Come usare l'archiviazione BLOB da .NET](storage-dotnet-how-to-use-blobs.md)
- [Come usare l'archiviazione tabelle da .NET](storage-dotnet-how-to-use-tables.md)
- [Come usare l'archiviazione di accodamento da .NET](storage-dotnet-how-to-use-queues.md)

<h3>Per sviluppatori Java/Android</h3>

- [Informazioni di riferimento sulla libreria client Java]()
- [Come usare l'archiviazione BLOB da Java/Android](storage-java-how-to-use-blob-storage.md)
- [Come usare l'archiviazione tabelle da Java/Android](storage-java-how-to-use-table-storage.md)
- [Come usare l'archiviazione di accodamento da Java/Android](storage-java-how-to-use-queue-storage.md)

<h3>Per gli sviluppatori Node.js</h3>

- [Come usare l'archiviazione BLOB da Node.js](storage-nodejs-how-to-use-blob-storage.md)
- [Come usare l'archiviazione tabelle da Node.js](storage-nodejs-how-to-use-table-storage.md)
- [Come usare l'archiviazione di accodamento da Node.js](storage-nodejs-how-to-use-queues.md)

<h3>Per gli sviluppatori PHP</h3>

- [Come usare l'archiviazione BLOB da PHP](storage-php-how-to-use-blobs.md)
- [Come usare l'archiviazione tabelle da PHP](storage-php-how-to-use-table-storage.md)
- [Come usare l'archiviazione di accodamento da PHP](storage-php-how-to-use-queues.md)

<h3>Per gli sviluppatori Ruby</h3>

- [Come usare l'archiviazione BLOB da Ruby](storage-ruby-how-to-use-blob-storage.md)
- [Come usare l'archiviazione tabelle da Ruby](storage-ruby-how-to-use-table-storage.md)
- [Come usare l'archiviazione di accodamento da Ruby](storage-ruby-how-to-use-queue-storage.md)

<h3>Per gli sviluppatori Python</h3>

- [Come usare l'archiviazione BLOB da Python](storage-python-how-to-use-blob-storage.md)
- [Come usare l'archiviazione tabelle da Python](storage-python-how-to-use-table-storage.md)
- [Come usare l'archiviazione di accodamento da Python](storage-python-how-to-use-queue-storage.md)

<!--HONumber=42-->
