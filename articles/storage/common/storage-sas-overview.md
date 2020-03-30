---
title: Concedere l'accesso limitato ai dati con firme di accesso condivisoGrant limited access to data with shared access signatures (SAS)
titleSuffix: Azure Storage
description: Informazioni sull'uso delle firme di accesso condiviso per delegare l'accesso alle risorse di Archiviazione di Azure, inclusi BLOB, code, tabelle e file.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7a5967f52a187fe289c6fb1ca72af2d5fd17f010
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255236"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Concedere l'accesso limitato alle risorse di Archiviazione di Azure usando le firme di accesso condivisoGrant limited access to Azure Storage resources using shared access signatures (SAS)

Una firma di accesso condiviso fornisce l'accesso delegato sicuro alle risorse nell'account di archiviazione senza compromettere la sicurezza dei dati. Con una sAS, si ha un controllo granulare su come un client può accedere ai dati. È possibile controllare le risorse a cui il client può accedere, le autorizzazioni di cui dispone per tali risorse e per quanto tempo la firma di accesso client è valida, tra gli altri parametri.

## <a name="types-of-shared-access-signatures"></a>Tipi di firme di accesso condiviso

Archiviazione di Azure supporta tre tipi di firme di accesso condiviso:Azure Storage supports three types of shared access signatures:

- **SAS di delega degli utenti.** Una connessione di accesso condiviso di delega utente è protetta con le credenziali di Azure Active Directory (Azure AD) e anche con le autorizzazioni specificate per la chiamata di accesso condiviso. Una verifica di accesso base della delega utente si applica solo all'archiviazione BLOB.

    Per altre informazioni sulla sAS di delega utente, vedere [Creare una sAS (REST)](/rest/api/storageservices/create-user-delegation-sas)di delega utente.

- **Firma di accesso condiviso del servizio.** Una chiave di accesso sAS del servizio è protetta con la chiave dell'account di archiviazione. A service SAS delegates access to a resource in only one of the Azure Storage services: Blob storage, Queue storage, Table storage, or Azure Files. 

    Per altre informazioni sulla sAS del servizio, vedere [Creare una sAS (REST)](/rest/api/storageservices/create-service-sas)del servizio.

- **Firma di accesso condiviso dell'account.** Una gestione della chiave di accesso sas dell'account è protetta con la chiave dell'account di archiviazione. Una firma di accesso condiviso dell'account delega l'accesso alle risorse in uno o più servizi di archiviazione. Tutte le operazioni disponibili tramite una console di accesso livello del servizio o della delega di utenti sono disponibili anche tramite una console di accesso livello dell'account. Inoltre, con la sAS dell'account, è possibile delegare l'accesso alle operazioni che si applicano a livello del servizio, ad esempio le **operazioni Get/Set Service Properties** e Get Service **Stats.** È anche possibile delegare l'accesso alle operazioni di lettura, scrittura ed eliminazioni in contenitori BLOB, tabelle, code e condivisioni file, che con una firma di accesso condiviso del servizio non è consentito. 

    Per altre informazioni sulla sAS dell'account, creare una [sAS (REST) dell'account.](/rest/api/storageservices/create-account-sas)

> [!NOTE]
> Microsoft consiglia di usare le credenziali di Azure AD quando possibile come procedura consigliata per la sicurezza, anziché usare la chiave dell'account, che può essere compromessa più facilmente. Quando la progettazione dell'applicazione richiede firme di accesso condiviso per l'accesso all'archiviazione BLOB, usare le credenziali di Azure AD per creare una firma di accesso condiviso di delega utente quando possibile per una sicurezza superiore.

Una firma di accesso condiviso può assumere una delle due forme seguenti:

- **SAS ad hoc:** Quando si crea una sAS ad hoc, l'ora di inizio, l'ora di scadenza e le autorizzazioni per la sAS sono tutte specificate nell'URI di sAS (o implicite, se l'ora di inizio viene omessa). Qualsiasi tipo di sAS può essere una sAS ad hoc.
- **SAS del servizio con criteri di accesso archiviati:Service SAS with stored access policy:** I criteri di accesso archiviati vengono definiti in un contenitore di risorse, che può essere un contenitore BLOB, una tabella, una coda o una condivisione file. I criteri di accesso archiviati possono essere utilizzati per gestire i vincoli per una o più firme di accesso condiviso del servizio. Quando si associa una sAS del servizio a un criterio&mdash;di accesso archiviato, la&mdash;provider di servizi di accesso client eredita i vincoli dell'ora di inizio, dell'ora di scadenza e delle autorizzazioni definite per i criteri di accesso archiviati.

> [!NOTE]
> Una sAS di delega utente o una sAS di account deve essere una sAS ad hoc. I criteri di accesso archiviati non sono supportati per la delega utente sAS o la sAS dell'account.

## <a name="how-a-shared-access-signature-works"></a>Funzionamento della firma di accesso condiviso

Una firma di accesso condiviso è un URI con fimra che punta a una o più risorse di archiviazione e include un token che contiene un set di parametri di query speciale. Il token indica la modalità di accesso alla risorsa da parte del client. Uno dei parametri di query, la firma, viene costruito dai parametri di firma di accesso sole e firmato con la chiave usata per creare la firma di accesso base. Questa firma viene usata da Archiviazione di Azure per autorizzare l'accesso alla risorsa di archiviazione.

### <a name="sas-signature"></a>Firma SAS

È possibile firmare una firma di accesso base in uno dei due modi seguenti:You can sign a SAS in one of two ways:

- Con una chiave di *delega utente* creata usando le credenziali di Azure Active Directory (Azure AD). Una firma di accesso livello di accesso sAS di delega utente è firmata con la chiave di delega utente.

    Per ottenere la chiave di delega utente e creare la provider di servizi di accesso locale, a un'entità di sicurezza di Azure AD deve essere assegnato un ruolo di controllo di accesso in base al ruolo che include l'azione **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.To** get the user delegation key and create the SAS, an Azure AD security principal must be assigned a role-based access control (RBAC) role that includes the Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey action. Per informazioni dettagliate sui ruoli RBAC con autorizzazioni per ottenere la chiave di delega utente, vedere Creare una licenza di accesso [sas (REST)](/rest/api/storageservices/create-user-delegation-sas)di delega degli utenti.

- Con la chiave dell'account di archiviazione. Sia una firma di accesso livello accesso livello del servizio che una firma di accesso livello dell'account sono firmate con la chiave dell'account di archiviazione. Per creare una firma di accesso utente firmata con la chiave dell'account, un'applicazione deve avere accesso alla chiave dell'account.

### <a name="sas-token"></a>Token di firma di accesso condiviso

Il token di firma di accesso condiviso è una stringa generata sul lato client, ad esempio usando una delle librerie client di Archiviazione di Azure.The SAS token is a string that you generate on the client side, for example by using one of the Azure Storage client libraries. Il token di firma di accesso condiviso non viene registrato in alcun modo da Archiviazione di Azure.The SAS token is not tracked by Azure Storage in any way. È possibile creare un numero illimitato di token di firma di accesso condiviso sul lato client. Dopo aver creato una configurazione di accesso locale, è possibile distribuirla alle applicazioni client che richiedono l'accesso alle risorse nell'account di archiviazione.

Quando un'applicazione client fornisce un URI di firma di accesso condiviso ad Archiviazione di Azure come parte di una richiesta, il servizio controlla i parametri e la firma di firma di accesso condiviso per verificare che siano validi per l'autorizzazione della richiesta. Se il servizio conferma che la firma è valida, la richiesta viene autorizzata. In caso contrario, la richiesta viene rifiutata con il codice errore 403 (accesso negato).

Ecco un esempio di URI di firma di accesso condiviso del servizio che mostra l'URI della risorsa e il token di firma di accesso condiviso:Here's an example of a service SAS URI, showing the resource URI and the SAS token:

![Componenti di un URI di accesso condiviso del servizioComponents of a service SAS URI](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Quando usare una firma di accesso condivisoWhen to use a shared access signature

Usare una server di accesso sAS quando si vuole fornire l'accesso sicuro alle risorse nell'account di archiviazione a qualsiasi client che non disponga in altro modo delle autorizzazioni per tali risorse.

Uno scenario comune in cui la firma di accesso condiviso risulta utile è costituito da un servizio in cui gli utenti leggono e scrivono i propri dati nell'account di archiviazione di un utente specifico. Uno scenario in cui i dati utente vengono archiviati nell'account di archiviazione è caratterizzato da due modelli di progettazione standard:

1. I client caricano e scaricano dati tramite un servizio proxy front-end che esegue l'autenticazione. Tale servizio presenta il vantaggio di consentire la convalida delle regole di business, tuttavia per grandi quantità di dati o per transazioni con volumi elevati, la creazione di un servizio scalabile in base alla domanda può risultare oneroso o complesso.

   ![Diagramma dello scenario: il servizio proxy front-end](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Un servizio semplificato autentica il client in base alle necessità e quindi genera una firma di accesso condiviso. Quando l'applicazione client riceve la sAS, può accedere direttamente alle risorse dell'account di archiviazione con le autorizzazioni definite dalla sAS e per l'intervallo consentito dalla sAS. La firma di accesso condiviso consente di ridurre la necessità di instradare tutti i dati tramite il servizio proxy front-end.

   ![Diagramma dello scenario: servizio provider di firma di accesso condiviso](./media/storage-sas-overview/sas-storage-provider-service.png)

Molti servizi reali possono usare una combinazione di questi due approcci. Ad esempio, alcuni dati potrebbero essere elaborati e convalidati tramite il proxy front-end, mentre altri vengono salvati e/o letti direttamente tramite la firma di accesso condiviso.

Inoltre, è necessaria una configurazione di accesso base per autorizzare l'accesso all'oggetto di origine in un'operazione di copia in determinati scenari:Additionally, a SAS is required to authorize access to the source object in a copy operation in certain scenarios:

- Quando si copia un BLOB in un altro BLOB che si trova in un account di archiviazione diverso, è necessario usare una firma di accesso condiviso per autorizzare l'accesso al BLOB di origine. È possibile usare una firma di accesso condiviso anche per autorizzare l'accesso al BLOB di destinazione.
- Quando si copia un file in un altro file che si trova in un account di archiviazione diverso, è necessario usare una firma di accesso condiviso per autorizzare l'accesso al file di origine. È possibile usare una firma di accesso condiviso anche per autorizzare l'accesso al file di destinazione.
- Quando si copia un BLOB in un file o un file in un BLOB, è necessario usare una firma di accesso condiviso per autorizzare l'accesso all'oggetto di origine, anche se gli oggetti di origine e di destinazione si trovano nello stesso account di archiviazione.

## <a name="best-practices-when-using-sas"></a>Procedure consigliate per l'uso di SAS

Quando si utilizzano le firme di accesso condiviso nell'applicazione, è necessario essere consapevoli di due rischi potenziali:

- In caso di diffusione di una firma di accesso condiviso, chiunque la ottiene può usarla e questo potrebbe compromettere l'account di archiviazione.
- Se una firma di accesso condiviso fornita a un'applicazione client scade e l'applicazione non è in grado di recuperarne una nuova dal servizio, è possibile che le funzionalità dell'applicazione potrebbero risentirne.

Per mitigare questi rischi, è consigliabile attenersi ai consigli seguenti relativi all'utilizzo di firme di accesso condiviso:

- **Usare sempre HTTPS** per creare o distribuire una firma di accesso condiviso. Se una firma di accesso condiviso passata tramite HTTP viene intercettata, un utente malintenzionato che esegue un attacco man-in-the-middle è in grado di leggere la firma e quindi di usarla come se fosse l'utente cui è destinata, e questo potrebbe comportare la compromissione di dati sensibili o il danneggiamento di dati da parte dell'utente malintenzionato.
- **Quando possibile, usare una server di gestione accesso qualità utente.** Una sAS di delega degli utenti garantisce una sicurezza superiore a una sAS del servizio o a una sAS dell'account. Una firma di accesso condiviso della delega utente è protetta con le credenziali di Azure AD, pertanto non è necessario archiviare la chiave dell'account con il codice.
- **Disporre di un piano di revoca per una sAS.** Assicurarsi di essere pronti a rispondere se una sAS è compromessa.
- **Definire criteri di accesso archiviati per una provider di servizi di accesso client per il servizio.** I criteri di accesso archiviati offrono la possibilità di revocare le autorizzazioni per una sessione di accesso client senza dover rigenerare le chiavi dell'account di archiviazione. Impostare la loro scadenza a un momento molto lontano nel futuro (o infinito) e assicurarsi che venga aggiornata regolarmente in modo che ricorra nel futuro.
- **Usare i tempi di scadenza a breve termine in una sAS o una sAS di account per il servizio SAS ad hoc.** In questo modo, anche se una firma di accesso condiviso viene compromessa, è valida solo per un breve periodo di tempo. Questo consiglio è particolarmente importante se non è possibile fare riferimento a criteri di accesso archiviati Una scadenza breve consente anche di limitare la quantità di dati che è possibile scrivere in un BLOB riducendo il tempo disponibile per il caricamento.
- **Se necessario, chiedere ai client di rinnovare automaticamente la firma di accesso condiviso..** È consigliabile rinnovare la firma di accesso condiviso nei client prima della scadenza in modo da avere la possibilità di effettuare altri tentativi qualora il servizio che fornisce la firma di accesso condiviso non sia disponibile. Se la firma di accesso condiviso è destinata a essere utilizzata per poche operazioni immediate e di breve durata da completare entro la data di scadenza, il rinnovo della firma di accesso condiviso non è necessario. Se tuttavia si dispone di client che effettuano normalmente richieste tramite la firma di accesso condiviso, è necessario considerare la possibilità che la firma scada. In questo caso è essenziale trovare un punto di equilibrio tale da garantire che la firma di accesso condiviso sia di breve durata (come indicato in precedenza) e che il client richieda il rinnovo in tempo utile per evitare malfunzionamenti causati dalla scadenza della firma prima del rinnovo.
- **Prestare attenzione all'ora di inizio della firma di accesso condiviso.** Se si imposta l'ora di inizio della firma di accesso condiviso su **ora**, a causa dello sfasamento di orario, ovvero delle differenze dell'ora corrente a seconda del computer in uso, potrebbero verificarsi problemi intermittenti nei primi minuti. In generale, impostare l'ora di inizio ad almeno 15 minuti prima. Oppure evitare di impostarla, in modo che la firma diventi immediatamente valida in tutti i casi. Le stesse considerazioni sono valide anche per la scadenza, pertanto è consigliabile osservare fino a 15 minuti di sfasamento di orario in entrambe le direzioni per qualsiasi richiesta. Per i client che usano una versione di REST precedente la 2012-02-12, la durata massima della firma di accesso condiviso che non fa riferimento a criteri di accesso archiviati è di 1 ora, pertanto tutti i criteri di durata maggiore non saranno validi.
- **Prestare attenzione con il formato datetime SAS.** Se si imposta l'ora di inizio e/o la scadenza per una firma di accesso condiviso, per alcune utilità (ad esempio per l'utilità della riga di comando AzCopy) è necessario che il formato datetime sia '%Y-%m-%dT%H:%M:%S', includendo in particolare i secondi affinché funzioni utilizzando il token di firma di accesso condiviso.  
- **Indicare in modo specifico la risorsa cui accedere.** Una procedura di sicurezza consigliata consiste nel fornire a un utente i privilegi minimi necessari. Se un utente necessita solo dell'accesso in lettura a una singola entità, concedere solo tale tipo di accesso per tale entità e non l'accesso in lettura/scrittura/eliminazione per tutte le entità. Ciò consente anche di ridurre i danni se una firma di accesso condiviso viene compromessa in quanto la firma è meno potente nelle mani di un utente malintenzionato.
- **Tieni presente che al tuo account verrà addebitato qualsiasi utilizzo, anche tramite una sAS.** Se si fornisce l'accesso in scrittura a un BLOB, un utente può scegliere di caricare un BLOB da 200 GB. Se si offre anche l'accesso in lettura, l'utente potrebbe scegliere di scaricarlo 10 volte e ciò potrebbe comportare 2 TB di costi in uscita. Anche in questo caso, fornire autorizzazioni limitate per ridurre l'impatto potenziale delle azioni di utenti malintenzionati. Per ridurre questa minaccia, usare firme di accesso condiviso di breve durata, prestando però attenzione allo sfasamento di orario per la scadenza.
- **Convalidare i dati scritti utilizzando una sAS.**  Quando un'applicazione client scrive i dati nell'account di archiviazione, tenere presente che tali dati potrebbero causare problemi. Se l'applicazione richiede che i dati siano convalidati o autorizzati prima dell'uso, è necessario eseguire la convalida dopo la scrittura dei dati e prima che vengano usati dall'applicazione. Questa procedura consente inoltre di evitare la scrittura di dati danneggiati o dannosi nell'account da parte da un utente che ha acquisito correttamente la firma di accesso condiviso o di un utente che sfrutta una firma diffusa per errore.
- **Sapere quando non usare una sAS.** A volte i rischi associati a una particolare operazione sull'account di archiviazione superano i vantaggi dell'uso di una sAS. Per tali operazioni creare un servizio di livello intermedio che effettui operazioni di scrittura nell'account di archiviazione dopo autenticazione, controllo e convalida di regole di business. Talvolta è inoltre più semplice gestire l'accesso in modi diversi. Se ad esempio si vogliono rendere pubblicamente leggibili tutti i BLOB di un contenitore, è possibile rendere pubblico il contenitore anziché fornire una firma di accesso condiviso a ogni client per l'accesso.
- **Usare Monitoraggio di Azure e i log di Archiviazione di Azure per monitorare l'applicazione.** È possibile usare Monitoraggio di Azure e la registrazione dell'analisi dell'archiviazione per osservare eventuali errori di autorizzazione dovuti a un'interruzione del servizio del provider di servizi di disponibilità di accesso utenti o alla rimozione accidentale di criteri di accesso archiviati. Per altre informazioni, vedere [Metriche di Archiviazione di Azure in Monitoraggio](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) di Azure e Registrazione di Analisi archiviazione di Azure.For more information, see Azure Storage metrics in Azure Monitor and [Azure Storage Analytics logging](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Introduzione alla sAS

Per iniziare a usare le firme di accesso condiviso, vedere gli articoli seguenti per ogni tipo di firma di accesso condiviso.

### <a name="user-delegation-sas"></a>SAS di delega utente

- [Creare una chiave di accesso client di delega utente per un contenitore o un BLOB con PowerShellCreate a user delegation SAS for a container or blob with PowerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Creare una chiave di accesso client di delega utente per un contenitore o un BLOB con l'interfaccia della riga di comando di AzureCreate a user delegation SAS for a container or blob with the Azure CLI](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Creare una classe di accesso client di delega utente per un contenitore o un BLOB con .NETCreate a user delegation SAS for a container or blob with .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>SAS del servizio

- [Creare una classe di accesso client per un contenitore o un BLOB con .NETCreate a service SAS for a container or blob with .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Gestione di schie reperiture per account

- [Creare una sAS dell'account con .NETCreate an account SAS with .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Passaggi successivi

- [Delegare l'accesso con una firma di accesso condiviso (API REST)Delegate access with a shared access signature (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Creare una sAS (GH) di delega utenteCreate a user delegation SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Creare una sAS del servizio (API REST)Create a service SAS (REST API)](/rest/api/storageservices/create-service-sas)
- [Creare una sAS (REST) dell'accountCreate an account SAS (REST API)](/rest/api/storageservices/create-account-sas)
