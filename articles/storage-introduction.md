<properties linkid="storage-introduction" urlDisplayName="Introduction to Azure Storage" pageTitle="Introduction to Storage | Microsoft Azure" metaKeywords="Get started  Azure storage introduction  Azure storage overview  Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage  Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure " description="An overview of Microsoft Azure Storage." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="Introduction to Microsoft Azure Storage" authors="tamram" />

Introduzione ad Archiviazione di Microsoft Azure
================================================

In questo articolo viene presentato Archiviazione di Microsoft Azure per gli sviluppatori, i professionisti IT e i decisori aziendali. Leggendo l'articolo si apprenderà:

-   Che cos'è Archiviazione di Azure e come è possibile utilizzarlo nelle applicazioni cloud, mobili, server e desktop
-   Quali tipi di dati è possibile archiviare con i servizio Archiviazione di Azure: BLOB, tabelle e code
-   Come viene gestito l'accesso ai dati in Archiviazione di Azure
-   Come vengono protetti i dati di Archiviazione di Azure tramite la ridondanza e la replica
-   Come procedere per creare la prima applicazione di Archiviazione di Azure

Informazioni su Archiviazione di Azure
--------------------------------------

Il cloud computing consente di disporre di nuovi scenari per le applicazioni che richiedono un sistema di archiviazione scalabile, durevole e a disponibilità elevata per i propri dati. Questo è esattamente lo scopo per cui è stato sviluppato Archiviazione di Microsoft Azure. Oltre a consentire agli sviluppatori di compilare applicazioni di grande portata per supportare nuovi scenari, Archiviazione di Azure offre anche la base di archiviazione per le funzionalità di infrastruttura distribuita come servizio di Microsoft, un'ulteriore prova della sua affidabilità.

Archiviazione di Azure è estremamente scalabile, per consentire l'archiviazione e l'elaborazione di centinaia di terabyte di dati per supportare gli scenari di Big Data necessari per applicazioni scientifiche, di analisi finanziaria e multimediali. In alternativa, è possibile archiviare piccole quantità di dati necessarie per un piccolo sito Web aziendale. Qualsiasi siano le esigenze, il cliente paga solo per i dati archiviati. In Archiviazione di Azure sono al momento archiviate decine di migliaia di miliardi di oggetti univoci dei clienti e sono gestiti in media milioni di richieste al secondo.

Archiviazione di Azure è flessibile, per consentire la progettazione di applicazioni per un pubblico vasto e globale e per scalare tali applicazioni in base alle esigenze, sia in termini di quantità di dati archiviati che in termini di numero di transazioni richieste. Il cliente paga solo per ciò che utilizza e solo quando lo utilizza.

Archiviazione di Azure utilizza un sistema di partizionamento automatico che applica automaticamente il bilanciamento del carico dei dati in base al traffico. Pertanto, se le richieste dell'applicazione aumentano, vengono automaticamente allocate le risorse appropriate per soddisfare le nuove esigenze.

Archiviazione di Azure è accessibile da qualsiasi luogo, da qualsiasi tipo di applicazione, sia che venga eseguita nel cloud, sul desktop, su un server locale o su un dispositivo mobile o su un tablet. È possibile utilizzare Archiviazione di Azure in scenari mobili in cui l'applicazione archivia un subset di dati nel dispositivo e li sincronizza con un set completo di dati archiviati nel cloud.

Archiviazione di Azure supporta client che utilizzano vari sistemi operativi (inclusi Windows e Linux) e numerosi linguaggi di programmazione (inclusi .NET, Java e C++) per facilitare le operazioni di sviluppo. Espone inoltre le risorse di dati tramite API REST semplici, disponibili per qualsiasi client in grado di inviare e ricevere dati tramite HTTP/HTTPS.

Presentazione dei servizi di archiviazione di Azure
---------------------------------------------------

I servizi di archiviazione di Azure utilizzano BLOB, tabelle e code:

-   Nell'**archivio BLOB** sono archiviati i dati di file. Un BLOB può essere qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione.
-   Nell'**archivio tabelle** sono archiviati set di dati strutturati. L'archiviazione delle tabelle utilizza un archivio dati chiave-attributo NoSQL, che consente lo sviluppo e l'accesso rapido a grandi quantità di dati.
-   L'**archiviazione di accodamento** offre un sistema di messaggistica affidabile per l'elaborazione del flusso di lavoro e per la comunicazione tra i componenti dei servizi cloud.

Questi tre servizi sono inclusi in ogni account di archiviazione. Un account di archiviazione è uno spazio dei nomi univoco che consente di accedere ad Archiviazione di Azure. Ogni account di archiviazione può includere fino a 200 TB di BLOB, code e dati di tabelle combinati.

Prima di creare un account di archiviazione, è necessario effettuare una sottoscrizione di Azure, ovvero a un piano per accedere ai diversi servizi di Azure. È possibile creare un massimo di 20 account di archiviazione con denominazione univoca con una singola sottoscrizione.

Per iniziare a utilizzare Azure, è possibile scaricare una [versione di valutazione gratuita](/en-us/pricing/free-trial/). Se si decide di acquistare un piano, è possibile scegliere una delle numerose [opzioni di vendita](/en-us/pricing/purchase-options/). Gli utenti [iscritti a MSDN](/en-us/pricing/member-offers/msdn-benefits-details/) ricevono crediti mensili gratuiti che possono essere utilizzati con i servizi di Azure, incluso Archiviazione di Azure.

Archiviazione BLOB
------------------

Per gli utenti che dispongono di grandi quantità di dati non strutturati da archiviare nel cloud, l'archiviazione BLOB offre una soluzione conveniente e scalabile. È possibile utilizzare gli archivi BLOB per archiviare contenuto come:

-   Documenti
-   Dati dei social network come foto, video, musica e blog
-   Backup di file, computer, database e dispositivi
-   Immagini e testo per applicazioni Web
-   Dati di configurazione per applicazioni cloud
-   Big Data, come log e altri set di dati di grandi dimensioni

Ogni BLOB è organizzato in un contenitore. I contenitori sono inoltre una soluzione utile per assegnare criteri di sicurezza a gruppi di oggetti. Un account di archiviazione può contenere un numero qualsiasi di contenitori e un contenitore può contenere un numero qualsiasi di BLOB, fino a che non viene raggiunto il limite di capacità di 200 TB dell'account di archiviazione.

Gli archivi BLOB offrono due tipi di BLOB: i BLOB in blocchi e i BLOB di pagine (dischi). I BLOB in blocchi sono ottimizzati per lo streaming e l'archiviazione di oggetti cloud e sono una soluzione adatta per l'archiviazione di documenti, file multimediali, backup e così via. Un BLOB in blocchi può raggiungere una dimensione massima di 200 GB. I BLOB di pagine sono ottimizzati per la rappresentazione di dischi IaaS e per il supporto di scritture casuali. Possono raggiungere una dimensione massima di 1 TB. Un disco IaaS collegato a una rete di macchine virtuali Azure è un disco rigido virtuale archiviato come BLOB di pagine.

Se si dispone di set di dati molto grandi e i vincoli della rete ne impediscono il caricamento o il download in archivi BLOB tramite una connessione, è possibile spedire un'unità disco rigido a Microsoft per importare o esportare i dati direttamente dal data center tramite il [Servizio Importazione/Esportazione di Azure](http://www.windowsazure.com/documentation/articles/storage-import-export-service/). È inoltre possibile copiare i dati BLOB all'interno del proprio account di archiviazione o tra account di archiviazione diversi.

Archiviazione tabelle
---------------------

Le applicazioni moderne spesso richiedono l'utilizzo di archivi dati con scalabilità e flessibilità maggiori di quelli delle versioni precedenti del software. Il servizio di archiviazione tabelle offre un archivio a disponibilità elevata e altamente scalabile, per consentire la scalabilità automatica dell'applicazione in base alle richieste degli utenti. L'archivio tabelle è l'archivio chiave-attributo NoSQL di Microsoft. Ha una struttura senza schema, il che lo rende diverso dai database relazionali tradizionali. Con un archivio dati senza schema è facile adattare i dati con il variare delle esigenze dell'applicazione. L'archiviazione tabelle è semplice da utilizzare e consente di velocizzare lo sviluppo e la creazione di applicazioni. L'accesso ai dati è rapido e conveniente per tutti i tipi di applicazione. L'archiviazione tabelle presenta in genere costi decisamente più bassi rispetto alle soluzioni SQL tradizionali per volumi simili di dati.

L'archivio tabelle è un archivio chiave-attributo, ovvero ogni entità di una tabella è archiviata con un nome di proprietà, che costituisce la chiave utilizzata per filtrare i dati e specificare i criteri di selezione. La chiave, con un insieme di proprietà e i relativi valori, costituisce un'entità. Poiché l'archiviazione tabelle non ha uno schema, le entità della stessa tabella possono contenere insiemi diversi di proprietà, che possono essere di tipi diversi.

È possibile utilizzare l'archiviazione tabelle per archiviare set di dati flessibili, ad esempio i dati utente per le applicazioni Web, le rubriche, le informazioni sui dispositivi e altri tipi di metadati richiesti dal servizio. In una tabella possono essere archiviate il numero desiderato di tabelle e un account di archiviazione può contenere un numero qualsiasi di tabelle, fino a che non viene raggiunto il limite di capacità di 200 TB dell'account di archiviazione.

In modo analogo ai BLOB e alle code, gli sviluppatori possono gestire e accedere all'archivio tabelle tramite protocolli REST standard, anche se l'archiviazione tabelle supporta anche un subset del protocollo OData, per semplificare le funzionalità di query avanzate e abilitare i formati JSON e AtomPub (basato su XML).

Per le attuali applicazioni basate su Internet, i database NoSQL come l'archivio tabelle offrono una comoda alternativa ai database relazionali tradizionali.

Archiviazione di accodamento
----------------------------

Durante la progettazione di applicazioni scalabili, i componenti dell'applicazione vengono spesso separati, per poter essere scalati in modo indipendente. L'archiviazione di accodamento offre una soluzione di messaggistica affidabile per la comunicazione asincrona tra i componenti dell'applicazione, che possono essere eseguiti nel cloud, sul desktop, su un server locale o in un dispositivo mobile. Supporta inoltre la gestione di attività asincrone e la creazione di flussi di lavoro dei processi.

Un account di archiviazione può contenere il numero desiderato di code. Una coda può contenere un numero qualsiasi di messaggi, fino al raggiungimento del limite di capacità di 200 TB dell'account di archiviazione. I singoli messaggi possono avere una dimensione massima di 64 KB.

Accesso a risorse BLOB, tabella e coda
--------------------------------------

Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere alle risorse dell'account. Per garantire la sicurezza dei dati, ogni richiesta di risorse dell'account deve essere autenticata. L'autenticazione si basa su un modello di chiave condivisa. I BLOB possono inoltre essere configurati per supportare l'autenticazione anonima.

Durante la creazione dell'account di archiviazione, vengono assegnate due chiavi di accesso private utilizzate per l'autenticazione. La presenza di due chiavi garantisce la disponibilità dell'applicazione quando si rigenerano le chiavi nell'ambito delle normali operazioni di gestione delle chiavi di sicurezza.

Se è necessario consentire agli utenti l'accesso controllato alle risorse di archiviazione, è possibile creare una [firma di accesso condiviso](../storage-dotnet-shared-access-signature-part-1/). Una firma di accesso condiviso è un token che può essere aggiunto a un URL per consentire l'accesso delegato a un contenitore, BLOB, tabella o coda. Coloro che possiedono il token possono accedere alla risorsa a cui esso fa riferimento con le autorizzazioni specificate e per il periodo di validità del token stesso.

È infine possibile specificare che un contenitore e i relativi BLOB o che un BLOB specifico sia disponibile per l'accesso pubblico. Quando si indica che un contenitore o un BLOB è pubblico, qualsiasi utente lo potrà leggere in forma anonima, in quanto non è necessaria l'autenticazione. I contenitori e i BLOB pubblici sono utili per esporre risorse come file multimediali e documenti ospitati in siti Web. Per ridurre la latenza di rete per un pubblico globale, è possibile memorizzare nella cache i dati BLOB utilizzati dai siti Web con la rete CDN di Azure.

Replica per garantire la durabilità e la disponibilità elevata
--------------------------------------------------------------

I dati dell'account di archiviazione vengono replicati per assicurarne la durabilità e l'elevata disponibilità, in modo da soddisfare il [contratto di servizio di Archiviazione di Azure](/en-us/support/legal/sla/) anche in caso di errori hardware temporanei. Sono disponibili tre opzioni per replicare i dati nell'account di archiviazione:

-   *Archiviazione con ridondanza locale*: i dati vengono replicati tre volte all'interno di un unico data center. Quando si scrivono dati in un BLOB, in una coda o in una tabella, l'operazione di scrittura viene eseguita simultaneamente in tutte e tre le repliche. Questa opzione di replica protegge i dati dai normali errori hardware.
-   *Archiviazione con ridondanza geografica*: i dati vengono replicati tre volte in un'unica area geografica e in modo asincrono in una seconda area geografica a centinaia di chilometri di distanza dalla prima. Questo tipo di replica conserva un equivalente di 6 copie (repliche) dei dati (3 in ogni area geografica). In questo modo, Microsoft può eseguire il failover in una seconda area geografica nel caso in cui non sia possibile ripristinare i dati nella prima area a causa di un guasto importante o in situazioni di emergenza. L'archiviazione con ridondanza geografica è consigliata rispetto all'archiviazione con ridondanza locale.
-   *Archiviazione con ridondanza geografica e accesso in lettura*: garantisce tutti i vantaggi dell'archiviazione con ridondanza geografica e consente inoltre l'accesso in lettura ai dati nell'area geografica secondaria in caso di non disponibilità dell'area geografica principale. Questo tipo di archiviazione è consigliato per ottenere la massima disponibilità oltre alla durabilità dei dati.

Le differenze di prezzo tra i tre tipi di archiviazione sono disponibili nella pagina [Dettagli prezzi di archiviazione](/en-us/pricing/details/storage/).

Prezzi
------

I costi addebitati ai clienti per Archiviazione di Azure si basano su quattro fattori: la capacità di archiviazione utilizzata, l'opzione di replica selezionata, il numero di richieste effettuate al servizio e i dati in uscita.

La capacità di archiviazione fa riferimento alla quantità di unità di servizio dell'account di archiviazione in uso per l'archiviazione dei dati. Il costo dell'archiviazione dei dati è determinato dalla quantità di dati archiviata e dalla modalità di replica dei dati. Ogni operazione di lettura e scrittura in Archiviazione di Azure effettua una richiesta al servizio. I dati in uscita sono i dati trasferiti al di fuori di un'area geografica di Azure. Quando un'applicazione non in esecuzione nella stessa area geografica accede ai dati nell'account di archiviazione, indipendentemente dal fatto che l'applicazione sia un servizio cloud o un altro tipo di applicazione, verrà addebitato il trasferimento dei dati in uscita. Per i servizi di Azure è possibile raggruppare dati e servizi nello stesso data center per ridurre o eliminare gli addebiti per il trasferimento dei dati e dei processi in uscita.

Nella pagina [Dettagli prezzi di archiviazione](/en-us/pricing/details/storage/) vengono fornite informazioni dettagliate sui prezzi per la capacità, la replica e le transazioni relative all'archiviazione. Nella pagina [Dettagli prezzi dei trasferimenti di dati](/en-us/pricing/details/data-transfers/) vengono fornite informazioni dettagliate sui prezzi per il trasferimento dei dati in uscita. È possibile utilizzare la pagina per il [calcolo dei prezzi di Archiviazione di Azure](/en-us/pricing/calculator/?scenario=data-management) per stimare i costi.

Sviluppo per l'archiviazione
----------------------------

Archiviazione di Azure espone risorse di archiviazione tramite un'[API REST](http://msdn.microsoft.com/library/windowsazure/dd179355.aspx) che può essere chiamata da qualsiasi linguaggio in grado di effettuare richieste HTTP/HTTPS. In Archiviazione di Azure sono inoltre disponibili librerie di programmazione per diversi linguaggi comuni. Tali librerie semplificano molti aspetti dell'utilizzo di Archiviazione di Azure gestendo dettagli come la chiamata sincrona e asincrona, l'esecuzione delle operazioni in batch, la gestione delle eccezioni, la ripetizione automatica dei tentativi, il comportamento operativo e così via. Le librerie attualmente disponibili riguardano i linguaggi e le piattaforme seguenti, ma presto ne saranno disponibili altre:

-   [.NET](http://msdn.microsoft.com/library/dn495001(v=azure.10).aspx)
-   [Codice nativo](http://msdn.microsoft.com/library/dn495438.aspx)
-   [Java](/en-us/develop/java/)
-   [Node.js](../storage/#node)
-   [PHP](../storage/#php)
-   [Ruby](../storage/#ruby)
-   [Python](../storage/#python)
-   [PowerShell](http://msdn.microsoft.com/library/dn495240.aspx)

Passaggi successivi
-------------------

Per iniziare a utilizzare Archiviazione di Azure, vedere le risorse seguenti:

-   [Documentazione di Archiviazione di Azure](/en-us/documentation/services/storage/)
-   [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx)

### Per gli sviluppatori .NET

-   [Come utilizzare l'archiviazione BLOB da .NET](../storage-dotnet-how-to-use-blobs-20/)
-   [Come utilizzare l'archiviazione tabelle da .NET](../storage-dotnet-how-to-use-tables-20/)
-   [Come utilizzare l'archiviazione di accodamento da .NET](../storage-dotnet-how-to-use-queues-20/)

### Per gli sviluppatori Java

-   [Come utilizzare l'archiviazione BLOB da Java](../storage-java-how-to-use-blob-storage/)
-   [Come utilizzare l'archiviazione tabelle da Java](..storage-java-how-to-use-table-storage/)
-   [Come utilizzare l'archiviazione di accodamento da Java](..storage-java-how-to-use-queue-storage/)

### Per gli sviluppatori Node.js

-   [Come utilizzare l'archiviazione BLOB da Node.js](../storage-nodejs-how-to-use-blob-storage/)
-   [Come utilizzare l'archiviazione tabelle da Node.js](../storage-nodejs-how-to-use-table-storage/)
-   [Come utilizzare l'archiviazione di accodamento da Node.js](../storage-nodejs-how-to-use-queue-storage/)

### Per gli sviluppatori PHP

-   [Come utilizzare l'archiviazione BLOB da PHP](../storage-php-how-to-use-blob-storage/)
-   [Come utilizzare l'archiviazione tabelle da PHP](..storage-php-how-to-use-table-storage/)
-   [Come utilizzare l'archiviazione di accodamento da PHP](..storage-php-how-to-use-queue-storage/)

### Per gli sviluppatori Ruby

-   [Come utilizzare l'archiviazione BLOB da Ruby](../storage-ruby-how-to-use-blob-storage/)
-   [Come utilizzare l'archiviazione tabelle da Ruby](..storage-ruby-how-to-use-table-storage/)
-   [Come utilizzare l'archiviazione di accodamento da Ruby](..storage-ruby-how-to-use-queue-storage/)

### Per gli sviluppatori Python

-   [Come utilizzare l'archiviazione BLOB da Python](../storage-python-how-to-use-blob-storage/)
-   [Come utilizzare l'archiviazione tabelle da Python](..storage-python-how-to-use-table-storage/)
-   [Come utilizzare l'archiviazione di accodamento da Python](..storage-python-how-to-use-queue-storage/)

