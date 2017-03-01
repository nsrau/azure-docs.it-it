---
title: Introduzione ad Archiviazione di Azure | Documentazione Microsoft
description: Una panoramica di Archiviazione di Azure, l&quot;archiviazione dei dati online di Microsoft nel cloud. Informazioni su come usare nelle applicazioni la migliore soluzione di archiviazione cloud disponibile.
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/24/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: f4b26aa9a4dd110eafb8f9f8d658cc0f530136ec
ms.openlocfilehash: 4548d83525dba6024d1892950e3acbc6b857977a
ms.lasthandoff: 02/27/2017


---
# <a name="introduction-to-microsoft-azure-storage"></a>Introduzione ad Archiviazione di Microsoft Azure

## <a name="overview"></a>Panoramica
Archiviazione di Azure è la soluzione di archiviazione cloud per le applicazioni moderne basate su durata, disponibilità e scalabilità per soddisfare le esigenze dei clienti. Questo articolo offre a sviluppatori, professionisti IT e decision maker aziendali informazioni su:

* Che cos'è Archiviazione di Azure e come è possibile usarlo nelle applicazioni cloud, mobili, server e desktop
* Quali tipi di dati è possibile archiviare con i servizi Archiviazione di Azure: BLOB (oggetti), tabelle NoSQL, messaggi accodati e condivisione file.
* Come viene gestito l'accesso ai dati in Archiviazione di Azure
* Come vengono resi durevoli i dati di Archiviazione di Azure grazie a ridondanza e replica
* Come procedere per creare la prima applicazione di Archiviazione di Azure

Per usare ed eseguire rapidamente Archiviazione di Azure, vedere l'articolo di [introduzione ad Archiviazione di Azure in cinque minuti](storage-getting-started-guide.md).

Per informazioni dettagliate su strumenti, librerie e altre risorse per l'utilizzo di Archiviazione di Azure, vedere la sezione [Passaggi successivi](#next-steps) di seguito.

## <a name="what-is-azure-storage"></a>Informazioni su Archiviazione di Azure
Il cloud computing consente di disporre di nuovi scenari per le applicazioni che richiedono un sistema di archiviazione scalabile, durevole e a disponibilità elevata per i propri dati. Questo è esattamente lo scopo per cui è stato sviluppato Archiviazione di Microsoft Azure. Oltre a consentire agli sviluppatori di compilare applicazioni di grande portata per supportare nuovi scenari, Archiviazione di Azure offre anche la base di archiviazione per il servizio Macchine virtuali di Azure, un'ulteriore prova della sua affidabilità.

Archiviazione di Azure è estremamente scalabile, per consentire l'archiviazione e l'elaborazione di centinaia di terabyte di dati per supportare gli scenari di Big Data necessari per applicazioni scientifiche, di analisi finanziaria e multimediali. In alternativa, è possibile archiviare piccole quantità di dati necessarie per un piccolo sito Web aziendale. Qualsiasi siano le esigenze, il cliente paga solo per i dati archiviati. In Archiviazione di Azure sono al momento archiviate decine di migliaia di miliardi di oggetti univoci dei clienti e sono gestiti in media milioni di richieste al secondo.

Archiviazione di Azure è un servizio flessibile, per consentire la progettazione di applicazioni per un pubblico vasto e globale e per ridimensionare tali applicazioni in base alle esigenze, sia in termini di quantità di dati archiviati che di numero di richieste effettuate su tali dati. Il cliente paga solo per ciò che utilizza e solo quando lo utilizza.

Archiviazione di Azure utilizza un sistema di partizionamento automatico che applica automaticamente il bilanciamento del carico dei dati in base al traffico. Pertanto, se le richieste dell'applicazione aumentano, vengono automaticamente allocate le risorse appropriate per soddisfare le nuove esigenze.

Archiviazione di Azure è accessibile da qualsiasi luogo, da qualsiasi tipo di applicazione, sia che venga eseguita nel cloud, in un desktop, in un server locale o in un dispositivo mobile o un tablet. È possibile usare Archiviazione di Azure in scenari mobili in cui l'applicazione archivia un subset di dati nel dispositivo e li sincronizza con un set completo di dati archiviati nel cloud.

Archiviazione di Azure supporta client che usano sistemi operativi di vario tipo (tra cui Windows e Linux) e numerosi linguaggi di programmazione (tra cui .NET, Java, Node.js, Python, Ruby, PHP, C++ e linguaggi di programmazione per dispositivi mobili) per facilitare le operazioni di sviluppo. Espone inoltre le risorse di dati tramite API REST semplici, disponibili per qualsiasi client in grado di inviare e ricevere dati tramite HTTP/HTTPS.

L'archiviazione premium di Azure offre prestazioni elevate, supporto disco a bassa latenza per carichi di lavoro con uso intensivo di I/O in esecuzione su Macchine virtuali di Azure. Con l'archiviazione premium di Azure, è possibile collegare più dischi di dati permanenti a una macchina virtuale e configurarli per poter soddisfare i requisiti di prestazioni. Ogni disco di dati è supportato da un disco SSD nell'archiviazione premium di Azure per le massime prestazioni di I/O. Per altre informazioni, vedere [Archiviazione premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage.md) .

## <a name="introducing-the-azure-storage-services"></a>Introduzione ai servizi di archiviazione di Azure
Archiviazione di Azure offre i quattro servizi seguenti: archiviazione BLOB, archiviazione tabelle, archiviazione code e archiviazione file.

* L'archiviazione BLOB archivia dati oggetto non strutturati. Un BLOB può essere qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. L'archivio BLOB è anche denominato archivio di oggetti.
* L'archiviazione tabelle archivia set di dati strutturati. L'archiviazione delle tabelle utilizza un archivio dati chiave-attributo NoSQL, che consente lo sviluppo e l'accesso rapido a grandi quantità di dati.
* L'archiviazione code offre un sistema di messaggistica affidabile per l'elaborazione del flusso di lavoro e per la comunicazione tra componenti dei servizi cloud.
* L'archiviazione file offre risorse di archiviazione condivise per le applicazioni legacy con il protocollo SMB standard. Le macchine virtuali e i servizi cloud di Azure possono condividere dati file tra componenti delle applicazioni tramite le condivisioni montate e le applicazioni locali possono accedere ai dati file in una condivisione tramite l'API REST del servizio file.

Un account di archiviazione di Azure è un account sicuro che consente di accedere ai servizi in Archiviazione di Azure. L'account di archiviazione offre uno spazio dei nomi univoco per le risorse di archiviazione. La figura seguente mostra le relazioni tra le risorse di archiviazione di Azure in un account di archiviazione:

![Risorse di archiviazione di Azure](./media/storage-introduction/storage-concepts.png)

[!INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

[!INCLUDE [storage-versions-include](../../includes/storage-versions-include.md)]

## <a name="blob-storage"></a>Archiviazione BLOB
Per gli utenti con grandi quantità di dati oggetto non strutturati da archiviare nel cloud, l'archiviazione BLOB offre una soluzione conveniente e scalabile. È possibile usare gli archivi BLOB per archiviare contenuto come:

* Documenti
* Dati dei social network come foto, video, musica e blog
* Backup di file, computer, database e dispositivi
* Immagini e testo per applicazioni Web
* Dati di configurazione per applicazioni cloud
* Big Data, come log e altri set di dati di grandi dimensioni

Ogni BLOB è organizzato in un contenitore. I contenitori sono inoltre una soluzione utile per assegnare criteri di sicurezza a gruppi di oggetti. Un account di archiviazione può contenere un numero qualsiasi di contenitori e un contenitore può contenere un numero qualsiasi di BLOB, fino a che non viene raggiunto il limite di capacità di 500 TB dell'account di archiviazione.

Gli archivi BLOB offrono tre tipi di BLOB: i BLOB in blocchi , i BLOB di accodamento e i BLOB di pagine (dischi).

* I BLOB in blocchi sono ottimizzati per lo streaming e l'archiviazione di oggetti cloud e sono una soluzione adatta per l'archiviazione di documenti, file multimediali, backup e così via.
* I BLOB di accodamento sono simili ai BLOB in blocchi, ma ottimizzati per le operazioni di Accodamento. Un BLOB di accodamento può essere aggiornato solo aggiungendo un nuovo blocco alla fine. I BLOB di accodamento sono una buona scelta per scenari quali la registrazione, dove i nuovi dati devono essere scritti solo fino alla fine del BLOB.
* I BLOB di pagine sono ottimizzati per la rappresentazione di dischi IaaS e per il supporto di scritture casuali. Possono raggiungere una dimensione massima di 1 TB. Un disco IaaS collegato a una rete di macchine virtuali Azure è un disco rigido virtuale archiviato come BLOB di pagine.

Se sono presenti set di dati molto grandi e i vincoli della rete ne impediscono il caricamento o il download in archivi BLOB tramite una connessione, è possibile spedire un'unità disco rigido a Microsoft per importare o esportare i dati direttamente dal data center. Vedere [Usare il servizio di importazione/esportazione di Microsoft Azure per trasferire i dati nell'archiviazione BLOB](storage-import-export-service.md).

## <a name="table-storage"></a>Archiviazione tabelle
Le applicazioni moderne spesso richiedono l'utilizzo di archivi dati con scalabilità e flessibilità maggiori di quelli delle versioni precedenti del software. Il servizio di archiviazione tabelle offre un archivio a disponibilità elevata e altamente scalabile, per consentire la scalabilità automatica dell'applicazione in base alle richieste degli utenti. Archiviazione tabelle è l'archivio chiave-attributo NoSQL di Microsoft. Ha una struttura senza schema, il che lo rende diverso dai database relazionali tradizionali. Con un archivio dati senza schema è facile adattare i dati con il variare delle esigenze dell'applicazione. L'archiviazione tabelle è semplice da usare e consente di velocizzare lo sviluppo e la creazione di applicazioni. L'accesso ai dati è rapido e conveniente per tutti i tipi di applicazione.  L'archiviazione tabelle presenta in genere costi decisamente più bassi rispetto alle soluzioni SQL tradizionali per volumi simili di dati.

Archiviazione tabelle è un archivio chiave-attributo, ovvero ogni valore di una tabella è archiviato con un nome di proprietà tipizzato Archiviazione tabelle è un archivio chiave-attributo, ovvero ogni valore di una tabella è archiviato con un nome di proprietà tipizzato che può essere usato per filtrare i dati e specificare i criteri di selezione. Una raccolta di proprietà con i rispettivi valori costituisce un'entità. Poiché l'archiviazione tabelle non ha uno schema, due entità della stessa tabella possono contenere raccolte diverse di proprietà, che possono essere di tipi diversi.

È possibile usare l'archiviazione tabelle per archiviare set di dati flessibili, ad esempio i dati utente per le applicazioni Web, le rubriche, le informazioni sui dispositivi e altri tipi di metadati richiesti dal servizio.  In una tabella possono essere archiviate il numero desiderato di tabelle e un account di archiviazione può contenere un numero qualsiasi di tabelle, fino a che non viene raggiunto il limite di capacità dell'account di archiviazione.

In modo analogo ai BLOB e alle code, gli sviluppatori possono gestire e accedere all'archivio tabelle tramite protocolli REST standard. Tuttavia, l'archiviazione tabelle supporta anche un subset del protocollo OData, per semplificare le funzionalità di query avanzate e abilitare i formati JSON e AtomPub (basato su XML).

Per le attuali applicazioni basate su Internet, i database NoSQL come l'archivio tabelle offrono una comoda alternativa ai database relazionali tradizionali.

## <a name="queue-storage"></a>Archiviazione code
Durante la progettazione di applicazioni scalabili, i componenti dell'applicazione vengono spesso separati, per poter essere scalati in modo indipendente. L'archiviazione di accodamento offre una soluzione di messaggistica affidabile per la comunicazione asincrona tra i componenti dell'applicazione, che possono essere eseguiti nel cloud, in un desktop, in un server locale o in un dispositivo mobile. Supporta inoltre la gestione di attività asincrone e la creazione di flussi di lavoro dei processi.

Un account di archiviazione può contenere il numero desiderato di code. Una coda può contenere un numero qualsiasi di messaggi, fino al raggiungimento del limite di capacità dell'account di archiviazione. I singoli messaggi possono avere una dimensione massima di 64 KB.

## <a name="file-storage"></a>Archiviazione file
Archiviazione file offre condivisioni file SMB basate sul cloud per consentire la migrazione delle applicazioni legacy ad Azure in modo veloce e senza costi di riscrittura. Archiviazione di File di Azure, le applicazioni in esecuzione in macchine virtuali di Azure o i servizi cloud consentono di montare una condivisione di file nel cloud, esattamente come un'applicazione desktop che consente di montare una condivisione SMB tipica. Non ci sono limiti per i componenti delle applicazioni che possono montare e accedere contemporaneamente alla condivisione di archiviazione file.

Poiché una condivisione di archiviazione file è una condivisione file SMB standard, le applicazioni in esecuzione in Azure possono accedere ai dati nella condivisione tramite le API di I/O del file di sistema. Gli sviluppatori possono quindi riutilizzare il codice esistente e le competenze acquisite per eseguire la migrazione delle applicazioni esistenti. I professionisti IT possono usare i cmdlet di PowerShell per creare, montare e gestire condivisioni di archiviazione file nell'ambito delle attività di amministrazione per le applicazioni Azure.

Come altri servizi di archiviazione di Azure, archiviazione file espone un'API REST per l'accesso ai dati in una condivisione. Le applicazioni locali possono chiamare l'API REST di archiviazione file per accedere ai dati in una condivisione file. Le aziende possono quindi decidere di eseguire la migrazione di alcune applicazioni legacy ad Azure e di continuare a eseguirne altre dall'interno dell'organizzazione locale. Si noti che è possibile montare una condivisione file solo per le applicazioni in esecuzione in Azure. Le applicazioni locali possono accedere alla condivisione file solo tramite l'API REST.

Le applicazioni distribuite possono anche usare l'archiviazione file per archiviare e condividere dati di applicazioni e strumenti di sviluppo e test utili. Ad esempio, un'applicazione può archiviare file di configurazione e dati di diagnostica quali log, metriche e dump di arresto anomalo del sistema in una condivisione di archiviazione file in modo che siano disponibili per più macchine virtuali o ruoli. Gli sviluppatori e gli amministratori possono archiviare le utilità necessarie per compilare o gestire un'applicazione in una condivisione di archiviazione file disponibile per tutti i componenti anziché installarle in ogni macchina virtuale o istanza del ruolo.

## <a name="access-to-blob-table-queue-and-file-resources"></a>Accedere a risorse BLOB, tabelle, code e file
Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere alle risorse dell'account. Per garantire la sicurezza dei dati, ogni richiesta di risorse dell'account deve essere autenticata. L'autenticazione si basa su un modello di chiave condivisa. I BLOB possono inoltre essere configurati per supportare l'autenticazione anonima.

Durante la creazione dell'account di archiviazione, vengono assegnate due chiavi di accesso private utilizzate per l'autenticazione. La presenza di due chiavi garantisce la disponibilità dell'applicazione quando si rigenerano le chiavi nell'ambito delle normali operazioni di gestione delle chiavi di sicurezza.

Se è necessario consentire agli utenti l'accesso controllato alle risorse di archiviazione, è possibile creare una firma di accesso condiviso. Una firma di accesso condiviso (SAS) è un token che può essere aggiunto a un URL per consentire l'accesso delegato a una risorsa di archiviazione. Coloro che possiedono il token possono accedere alla risorsa a cui esso fa riferimento con le autorizzazioni specificate e per il periodo di validità del token stesso. A partire dalla versione del 4/5/2015, Archiviazione di Azure supporta due tipi di firme di accesso condiviso: firma di accesso condiviso del servizio e firma di accesso condiviso dell'account.

La firma di accesso condiviso del servizio delega l'accesso a una risorsa in uno solo dei servizi di archiviazione, ovvero nel servizio BLOB, nel servizio di accodamento, nel servizio tabelle o nel servizio file.

Una firma di accesso condiviso dell'account delega l'accesso alle risorse in uno o più servizi di archiviazione. È possibile delegare l'accesso alle operazioni a livello di servizio che non sono disponibili con un servizio SAS. È anche possibile delegare l'accesso alle operazioni di lettura, scrittura ed eliminazioni in contenitori BLOB, tabelle, code e condivisioni file, che con una firma di accesso condiviso del servizio non è consentito.

È infine possibile specificare che un contenitore e i relativi BLOB o che un BLOB specifico sia disponibile per l'accesso pubblico. Quando si indica che un contenitore o un BLOB è pubblico, qualsiasi utente lo potrà leggere in forma anonima, in quanto non è necessaria l'autenticazione.  I contenitori e i BLOB pubblici sono utili per esporre risorse come file multimediali e documenti ospitati in siti Web.  Per ridurre la latenza di rete per un pubblico globale, è possibile memorizzare nella cache i dati BLOB usati dai siti Web con la rete CDN di Azure.

Per altre informazioni sulle firme di accesso condiviso, vedere [Using Shared Access Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) (Uso di firme di accesso condiviso). Per altre informazioni sull'accesso sicuro all'account di archiviazione, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](storage-manage-access-to-resources.md) e [Autenticazione per i servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx).

## <a name="replication-for-durability-and-high-availability"></a>Replica per la durabilità e la disponibilità elevata
I dati nell'account di archiviazione di Microsoft Azure vengono sempre replicati per assicurarne la durabilità e la disponibilità elevata. La replica copia i dati nello stesso data center o in uno secondario, a seconda dell'opzione di replica scelta. Consente di proteggere i dati e mantiene operativa l'applicazione in caso di errori hardware temporanei. Se i dati vengono replicati in un secondo data center, la replica li protegge anche in caso di un errore irreversibile nella posizione primaria.

Garantisce infine che l'account di archiviazione soddisfi il [Contratto di servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage/) anche in caso di errori. Altre informazioni sulla garanzia di durabilità e disponibilità di Archiviazione di Azure sono reperibili nel Contratto di servizio.

Quando si crea un account di archiviazione, è possibile selezionare una delle opzioni di replica seguenti:

* **Archiviazione con ridondanza locale (LRS).** Con l'archiviazione con ridondanza locale vengono conservate tre copie dei dati. Tale tipo di archiviazione viene replicato per tre volte all'interno di un unico data center di una singola area. Questa opzione di replica protegge i dati dai normali errori hardware ma non dagli errori di un unico data center.

    L'archiviazione con ridondanza locale viene offerta a tariffe scontate. Per la massima durabilità, è consigliabile usare l'archiviazione con ridondanza geografica descritta di seguito.
* **Archiviazione con ridondanza della zona (ZRS).** Con l'archiviazione con ridondanza della zona vengono conservate tre copie dei dati. Tale tipo di archiviazione viene replicato per tre volte in due o tre strutture all'interno di una singola area o in due aree, fornendo una durabilità maggiore rispetto all'archiviazione con ridondanza locale. Questa opzione di replica assicura la durabilità dei dati all'interno di una singola area.

    L'archiviazione con ridondanza della zona fornisce un livello di durabilità maggiore rispetto all'archiviazione con ridondanza locale. Tuttavia, per la massima durabilità, è consigliabile usare l'archiviazione con ridondanza geografica descritta di seguito.

  > [!NOTE]
  > ZRS è attualmente disponibile solo per i BLOB in blocchi ed è supportata solo nelle versioni 2014-02-14 e versioni successive.
  >
  > Dopo aver creato l'account di archiviazione e selezionato l'archiviazione con ridondanza della zona, non è possibile convertirlo per usarlo con un altro tipo di replica o viceversa.
  >
  >
* **Archiviazione con ridondanza geografica (GRS)**. Con tale tipo di archiviazione vengono conservate sei copie dei dati. Con questa opzione di replica, i dati vengono replicati per tre volte all'interno dell'area primaria e per tre volte in un'area secondaria situata a centinaia di chilometri di distanza dall'area primaria, fornendo il massimo livello di durabilità. In caso di errore nell'area primaria, il servizio di archiviazione di Azure eseguirà il failover all'area secondaria. L'archiviazione con ridondanza geografica assicura la durabilità dei dati in due aree distinte.

    Per informazioni sulle associazioni primarie e secondarie per area, vedere [Aree di Azure](https://azure.microsoft.com/regions/).
* **Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)**. L'archiviazione con ridondanza geografica e accesso in lettura replica i dati in una posizione geografica secondaria e fornisce inoltre l'accesso in lettura ai dati nella posizione secondaria. È possibile accedere ai dati dalla posizione primaria o secondaria, qualora una di queste diventi non disponibile. L'archiviazione con ridondanza geografica e accesso in lettura è abilitata per impostazione predefinita quando si crea l'account di archiviazione.

  > [!IMPORTANT]
  > È possibile cambiare la modalità di replica dei dati dopo la creazione dell'account di archiviazione, a meno che non sia stata specificata l'archiviazione con ridondanza della zona al momento della creazione dell'account. Si noti tuttavia che se si passa dall'archiviazione con ridondanza locale all'archiviazione con ridondanza geografica o all'archiviazione con ridondanza geografica e accesso in lettura, potrebbe essere addebitato un costo una tantum per il trasferimento dei dati.
  >
  >

Per altri dettagli sulle opzioni di replica di archiviazione, vedere [Replica di Archiviazione di Azure](storage-redundancy.md) .

Per informazioni sui prezzi per la replica dell'account di archiviazione, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/). Per altre informazioni sui servizi disponibili in ogni area, vedere [Aree di Azure](https://azure.microsoft.com/regions/#services) .

Per i dettagli architetturali sulla durabilità con Archiviazione di Azure, vedere [Paper SOSP - Archiviazione di Microsoft Azure: Un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

## <a name="transferring-data-to-and-from-azure-storage"></a>Trasferimento dei dati da e verso Archiviazione di Azure
È possibile usare l'utilità della riga di comando AzCopy per copiare BLOB, file e dati della tabella all'interno dell'account di archiviazione o tra account di archiviazione. Per altre informazioni, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md) .

AzCopy si basa sulla [libreria di spostamento dei dati di Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), attualmente disponibile in anteprima.

Il servizio di importazione/esportazione di Azure fornisce un modo per importare o esportare i dati BLOB dall'account di archiviazione tramite un disco rigido inviato ai data center di Azure. Per altre informazioni sul servizio di importazione/esportazione, vedere [Usare il servizio di importazione/esportazione di Microsoft Azure per trasferire dati nell'archivio BLOB](storage-import-export-service.md).

## <a name="pricing"></a>Prezzi
[!INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

## <a name="storage-apis-libraries-and-tools"></a>API di archiviazione, librerie e strumenti
Le risorse di archiviazione di Azure sono accessibile da qualsiasi linguaggio in grado di eseguire richieste HTTP/HTTPS. In Archiviazione di Azure sono inoltre disponibili librerie di programmazione per diversi linguaggi comuni. Tali librerie semplificano molti aspetti dell'utilizzo di Archiviazione di Azure gestendo dettagli come la chiamata sincrona e asincrona, l'esecuzione delle operazioni in batch, la gestione delle eccezioni, la ripetizione automatica dei tentativi, il comportamento operativo e così via. Le librerie attualmente disponibili riguardano i linguaggi e le piattaforme seguenti, ma presto ne saranno disponibili altre:

### <a name="azure-storage-data-services"></a>Servizi dati di Archiviazione di Azure
* [API REST dei servizi di archiviazione](http://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Libreria client per .NET, Windows Phone e Windows Runtime](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [Libreria client di archiviazione per C++](https://github.com/Azure/azure-storage-cpp)
* [Libreria client di archiviazione per Java/Android](https://azure.microsoft.com/develop/java/)
* [Libreria client di archiviazione per Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Libreria client di archiviazione per PHP](https://azure.microsoft.com/develop/php/)
* [Libreria client di archiviazione per Ruby](https://azure.microsoft.com/develop/ruby/)
* [Libreria client di archiviazione per Python](https://azure.microsoft.com/develop/python/)
* [Cmdlet di archiviazione per PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt269418.aspx)

### <a name="azure-storage-management-services"></a>Servizi di gestione di Archiviazione di Azure
* [Informazioni di riferimento sulle API REST del provider di risorse di archiviazione](/rest/api/storagerp/)
* [Libreria client del provider di risorse di archiviazione per .NET](/dotnet/api/microsoft.azure.management.storage)
* [Cmdlet del provider di risorse di archiviazione per PowerShell 1.0](/powershell/storage/)
* [API REST di gestione del servizio di archiviazione (classico)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-services"></a>Servizi di spostamento dei dati di Archiviazione di Azure
* [API REST del servizio di importazione/esportazione dell'archiviazione](storage-import-export-service.md)
* [Libreria client di spostamento dei dati di archiviazione per .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### <a name="tools-and-utilities"></a>Strumenti e utilità
* [Azure Storage Explorer](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Strumento client di Archiviazione di Azure](storage-explorers.md)
* [Azure SDK e strumenti](https://azure.microsoft.com/tools/)
* [Emulatore di archiviazione di Azure](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Utilità da riga di comando di AzCopy](http://aka.ms/downloadazcopy)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Archiviazione di Azure, vedere le risorse seguenti:

### <a name="documentation"></a>Documentazione
* [Documentazione di Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Creare un account di archiviazione](storage-create-storage-account.md)
* [Introduzione a Azure Storage in cinque minuti](storage-getting-started-guide.md)

### <a name="for-administrators"></a>Per amministratori
* [Uso di Azure PowerShell con Archiviazione di Azure](storage-powershell-guide-full.md)
* [Uso dell'interfaccia della riga di comando di Azure con Archiviazione di Azure](storage-azure-cli.md)

### <a name="for-net-developers"></a>Per sviluppatori .NET
* [Introduzione all'archiviazione BLOB di Azure con .NET](storage-dotnet-how-to-use-blobs.md)
* [Introduzione all'archiviazione tabelle di Azure con .NET](storage-dotnet-how-to-use-tables.md)
* [Introduzione all'archiviazione code di Azure con .NET](storage-dotnet-how-to-use-queues.md)
* [Introduzione ad Archiviazione file di Azure in Windows](storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>Per sviluppatori Java/Android
* [Come usare l'archiviazione BLOB da Java](storage-java-how-to-use-blob-storage.md)
* [Come usare l'archiviazione tabelle da Java](storage-java-how-to-use-table-storage.md)
* [Come usare l'archiviazione di accodamento da Java](storage-java-how-to-use-queue-storage.md)
* [Come usare l'archiviazione file da Java](storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>Per sviluppatori Node.js
* [Come usare l'archiviazione BLOB da Node.js](storage-nodejs-how-to-use-blob-storage.md)
* [Come usare l'archiviazione tabelle da Node.js](storage-nodejs-how-to-use-table-storage.md)
* [Come usare l'archiviazione di accodamento da Node.js](storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Per sviluppatori PHP
* [Come usare l'archiviazione BLOB da PHP](storage-php-how-to-use-blobs.md)
* [Come usare l'archiviazione tabelle da PHP](storage-php-how-to-use-table-storage.md)
* [Come usare l'archiviazione di accodamento da PHP](storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Per sviluppatori Ruby
* [Come usare l'archiviazione BLOB da Ruby](storage-ruby-how-to-use-blob-storage.md)
* [Come usare l'archiviazione tabelle da Ruby](storage-ruby-how-to-use-table-storage.md)
* [Come usare l'archiviazione di accodamento da Ruby](storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Per sviluppatori Python
* [Come usare l'archiviazione BLOB da Python](storage-python-how-to-use-blob-storage.md)
* [Come usare l'archiviazione tabelle da Python](storage-python-how-to-use-table-storage.md)
* [Come usare l'archiviazione di accodamento da Python](storage-python-how-to-use-queue-storage.md)
* [Come usare l'archiviazione file da Python](storage-python-how-to-use-file-storage.md)

