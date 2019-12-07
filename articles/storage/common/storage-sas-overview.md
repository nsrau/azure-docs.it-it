---
title: Concedere accesso limitato ai dati con firme di accesso condiviso (SAS)
titleSuffix: Azure Storage
description: Informazioni sull'uso delle firme di accesso condiviso (SAS) per delegare l'accesso alle risorse di archiviazione di Azure, tra cui BLOB, code, tabelle e file.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e4a5f83e3f4d26c2321ed1b4c48a385d07e6489d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895148"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso (SAS)

Una firma di accesso condiviso (SAS) fornisce accesso delegato sicuro alle risorse nell'account di archiviazione senza compromettere la sicurezza dei dati. Con una firma di accesso condiviso, si ha un controllo granulare sul modo in cui un client può accedere ai dati. È possibile controllare le risorse a cui il client può accedere, le autorizzazioni per tali risorse e il tempo di validità della firma di accesso condiviso, tra gli altri parametri.

## <a name="types-of-shared-access-signatures"></a>Tipi di firme di accesso condiviso

Archiviazione di Azure supporta tre tipi di firme di accesso condiviso:

- **SAS di delega utente (anteprima).** Una firma di accesso condiviso di delega utente è protetta con credenziali Azure Active Directory (Azure AD) e anche dalle autorizzazioni specificate per la firma di accesso condiviso. Una firma di accesso condiviso di delega utente si applica solo all'archiviazione BLOB.

    Per altre informazioni sulla firma di accesso condiviso della delega utente, vedere [creare una firma di accesso condiviso utente (API REST)](/rest/api/storageservices/create-user-delegation-sas).

- **Firma di accesso condiviso del servizio.** Una firma di accesso condiviso del servizio è protetta con la chiave dell'account di archiviazione. Una firma di accesso condiviso del servizio delega l'accesso a una risorsa in uno solo dei servizi di archiviazione di Azure: archiviazione BLOB, archiviazione code, archiviazione tabelle o File di Azure. 

    Per altre informazioni sulla firma di accesso condiviso del servizio, vedere creare una firma di accesso condiviso del [servizio (API REST)](/rest/api/storageservices/create-service-sas).

- **Firma di accesso condiviso dell'account.** Una firma di accesso condiviso dell'account è protetta con la chiave dell'account di archiviazione. Una firma di accesso condiviso dell'account delega l'accesso alle risorse in uno o più servizi di archiviazione. Tutte le operazioni disponibili tramite una firma di accesso condiviso dell'utente o del servizio sono disponibili anche tramite una firma di accesso condiviso dell'account. Con la firma di accesso condiviso dell'account è inoltre possibile delegare l'accesso alle operazioni che si applicano a livello del servizio, ad esempio **Get/Set Service Properties** e **Get Service stats** Operation. È anche possibile delegare l'accesso alle operazioni di lettura, scrittura ed eliminazioni in contenitori BLOB, tabelle, code e condivisioni file, che con una firma di accesso condiviso del servizio non è consentito. 

    Per altre informazioni sulla firma di accesso condiviso dell'account, creare una firma di accesso condiviso dell' [account (API REST)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> Microsoft consiglia di utilizzare le credenziali di Azure AD quando possibile come procedura di sicurezza consigliata, anziché utilizzare la chiave dell'account, che può essere compromessa in modo più semplice. Quando la progettazione dell'applicazione richiede firme di accesso condiviso per l'accesso all'archiviazione BLOB, usare le credenziali Azure AD per creare una firma di accesso condiviso di delega utente quando possibile per una sicurezza superiore.

Una firma di accesso condiviso può assumere una delle due forme seguenti:

- **SAS ad hoc:** Quando si crea una firma di accesso condiviso ad hoc, l'ora di inizio, l'ora di scadenza e le autorizzazioni per la firma di accesso condiviso vengono tutte specificate nell'URI della firma di accesso condiviso (o implicita se l'ora di inizio viene omessa). Qualsiasi tipo di firma di accesso condiviso può essere una firma di accesso condiviso ad hoc.
- Firma **di accesso condiviso del servizio con criteri di accesso archiviati:** Un criterio di accesso archiviato è definito in un contenitore di risorse, che può essere un contenitore BLOB, una tabella, una coda o una condivisione file. I criteri di accesso archiviati possono essere usati per gestire i vincoli per una o più firme di accesso condiviso del servizio. Quando si associa una firma di accesso condiviso del servizio a un criterio di accesso archiviato, la firma di accesso condiviso eredita i vincoli&mdash;l'ora di inizio, l'ora di scadenza e le autorizzazioni&mdash;definite per i criteri di accesso archiviati.

> [!NOTE]
> Una firma di accesso condiviso dell'utente o una firma di accesso condiviso dell'account deve essere una SAS ad hoc. I criteri di accesso archiviati non sono supportati per la firma di accesso condiviso utente o la firma di accesso condiviso dell'account.

## <a name="how-a-shared-access-signature-works"></a>Funzionamento della firma di accesso condiviso

Una firma di accesso condiviso è un URI con fimra che punta a una o più risorse di archiviazione e include un token che contiene un set di parametri di query speciale. Il token indica la modalità di accesso alla risorsa da parte del client. Uno dei parametri di query, ovvero la firma, viene creato dai parametri SAS e firmato con la chiave usata per creare la firma di accesso condiviso. Questa firma viene usata da Archiviazione di Azure per autorizzare l'accesso alla risorsa di archiviazione.

### <a name="sas-signature"></a>Firma SAS

È possibile firmare una firma di accesso condiviso in uno dei due modi seguenti:

- Con una *chiave di delega utente* creata con le credenziali Azure Active Directory (Azure ad). Una firma di accesso condiviso di delega utente è firmata con la chiave di delega utente.

    Per ottenere la chiave di delega utente e creare la firma di accesso condiviso, è necessario assegnare a un'entità di sicurezza Azure AD un ruolo di controllo degli accessi in base al ruolo che includa l'azione **Microsoft. storage/storageAccounts/blobServices/generateUserDelegationKey** . Per informazioni dettagliate sui ruoli di controllo degli accessi in base al ruolo con autorizzazioni per ottenere la chiave di delega utente, vedere creare una firma di accesso condiviso [utente (API REST)](/rest/api/storageservices/create-user-delegation-sas).

- Con la chiave dell'account di archiviazione. Una firma di accesso condiviso del servizio e una firma di accesso condiviso dell'account sono firmate con la chiave account di archiviazione Per creare una firma di accesso condiviso con la chiave dell'account, un'applicazione deve avere accesso alla chiave dell'account.

### <a name="sas-token"></a>Token di firma di accesso condiviso

Il token di firma di accesso condiviso è una stringa generata sul lato client, ad esempio usando una delle librerie client di archiviazione di Azure. Il token di firma di accesso condiviso non viene rilevato in alcun modo da archiviazione di Azure. È possibile creare un numero illimitato di token di firma di accesso condiviso sul lato client. Dopo aver creato una firma di accesso condiviso, è possibile distribuirla alle applicazioni client che richiedono l'accesso alle risorse nell'account di archiviazione.

Quando un'applicazione client fornisce un URI di firma di accesso condiviso ad archiviazione di Azure come parte di una richiesta, il servizio controlla i parametri SAS e la firma per verificare che sia valido per l'autorizzazione della richiesta. Se il servizio conferma che la firma è valida, la richiesta viene autorizzata. In caso contrario, la richiesta viene rifiutata con il codice errore 403 (accesso negato).

Ecco un esempio di URI di firma di accesso condiviso del servizio, che mostra l'URI della risorsa e il token di firma di accesso condiviso:

![Componenti di un URI di firma di accesso condiviso del servizio](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Quando usare una firma di accesso condiviso

Usare una firma di accesso condiviso quando si vuole fornire l'accesso sicuro alle risorse nell'account di archiviazione a qualsiasi client che non disponga in altro modo delle autorizzazioni per tali risorse.

Uno scenario comune in cui la firma di accesso condiviso risulta utile è costituito da un servizio in cui gli utenti leggono e scrivono i propri dati nell'account di archiviazione di un utente specifico. Uno scenario in cui i dati utente vengono archiviati nell'account di archiviazione è caratterizzato da due modelli di progettazione standard:

1. I client caricano e scaricano dati tramite un servizio proxy front-end che esegue l'autenticazione. Tale servizio presenta il vantaggio di consentire la convalida delle regole di business, tuttavia per grandi quantità di dati o per transazioni con volumi elevati, la creazione di un servizio scalabile in base alla domanda può risultare oneroso o complesso.

   ![Diagramma dello scenario: il servizio proxy front-end](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Un servizio semplificato autentica il client in base alle necessità e quindi genera una firma di accesso condiviso. Quando l'applicazione client riceve la firma di accesso condiviso, può accedere alle risorse dell'account di archiviazione direttamente con le autorizzazioni definite dalla firma di accesso condiviso e per l'intervallo consentito dalla firma di accesso condiviso. La firma di accesso condiviso consente di ridurre la necessità di instradare tutti i dati tramite il servizio proxy front-end.

   ![Diagramma dello scenario: servizio provider di firma di accesso condiviso](./media/storage-sas-overview/sas-storage-provider-service.png)

Molti servizi reali possono usare una combinazione di questi due approcci. Ad esempio, alcuni dati potrebbero essere elaborati e convalidati tramite il proxy front-end, mentre altri vengono salvati e/o letti direttamente tramite la firma di accesso condiviso.

Inoltre, è necessaria una firma di accesso condiviso per autorizzare l'accesso all'oggetto di origine in un'operazione di copia in determinati scenari:

- Quando si copia un BLOB in un altro BLOB che si trova in un account di archiviazione diverso, è necessario usare una firma di accesso condiviso per autorizzare l'accesso al BLOB di origine. È possibile usare una firma di accesso condiviso anche per autorizzare l'accesso al BLOB di destinazione.
- Quando si copia un file in un altro file che si trova in un account di archiviazione diverso, è necessario usare una firma di accesso condiviso per autorizzare l'accesso al file di origine. È possibile usare una firma di accesso condiviso anche per autorizzare l'accesso al file di destinazione.
- Quando si copia un BLOB in un file o un file in un BLOB, è necessario usare una firma di accesso condiviso per autorizzare l'accesso all'oggetto di origine, anche se gli oggetti di origine e di destinazione si trovano nello stesso account di archiviazione.

## <a name="best-practices-when-using-sas"></a>Procedure consigliate per l'uso di SAS

Quando si utilizzano le firme di accesso condiviso nell'applicazione, è necessario essere consapevoli di due rischi potenziali:

- In caso di diffusione di una firma di accesso condiviso, chiunque la ottiene può usarla e questo potrebbe compromettere l'account di archiviazione.
- Se una firma di accesso condiviso fornita a un'applicazione client scade e l'applicazione non è in grado di recuperarne una nuova dal servizio, è possibile che le funzionalità dell'applicazione potrebbero risentirne.

Per mitigare questi rischi, è consigliabile attenersi ai consigli seguenti relativi all'utilizzo di firme di accesso condiviso:

- **Usare sempre HTTPS** per creare o distribuire una firma di accesso condiviso. Se una firma di accesso condiviso passata tramite HTTP viene intercettata, un utente malintenzionato che esegue un attacco man-in-the-middle è in grado di leggere la firma e quindi di usarla come se fosse l'utente cui è destinata, e questo potrebbe comportare la compromissione di dati sensibili o il danneggiamento di dati da parte dell'utente malintenzionato.
- **Quando possibile, utilizzare una firma di accesso condiviso di delega utente.** Una firma di accesso condiviso di delega utente garantisce una sicurezza superiore a una firma di accesso condiviso del servizio o a una firma Una firma di accesso condiviso di delega utente è protetta con Azure AD credenziali, in modo che non sia necessario archiviare la chiave dell'account con il codice.
- **Disporre di un piano di revoca per una firma di accesso condiviso.** Assicurarsi di essere pronti a rispondere se una firma di accesso condiviso è compromessa.
- **Definire un criterio di accesso archiviato per una firma di accesso condiviso del servizio.** I criteri di accesso archiviati offrono la possibilità di revocare le autorizzazioni per una firma di accesso condiviso del servizio senza dover rigenerare le chiavi dell'account di archiviazione. Impostare la loro scadenza a un momento molto lontano nel futuro (o infinito) e assicurarsi che venga aggiornata regolarmente in modo che ricorra nel futuro.
- **Usare i tempi di scadenza a breve termine per una firma di accesso condiviso ad hoc o una firma di accesso condiviso del servizio.** In questo modo, anche se una firma di accesso condiviso viene compromessa, è valida solo per un breve periodo di tempo. Questo consiglio è particolarmente importante se non è possibile fare riferimento a criteri di accesso archiviati Una scadenza breve consente anche di limitare la quantità di dati che è possibile scrivere in un BLOB riducendo il tempo disponibile per il caricamento.
- **Se necessario, chiedere ai client di rinnovare automaticamente la firma di accesso condiviso.** È consigliabile rinnovare la firma di accesso condiviso nei client prima della scadenza in modo da avere la possibilità di effettuare altri tentativi qualora il servizio che fornisce la firma di accesso condiviso non sia disponibile. Se la firma di accesso condiviso è destinata a essere utilizzata per poche operazioni immediate e di breve durata da completare entro la data di scadenza, il rinnovo della firma di accesso condiviso non è necessario. Se tuttavia si dispone di client che effettuano normalmente richieste tramite la firma di accesso condiviso, è necessario considerare la possibilità che la firma scada. In questo caso è essenziale trovare un punto di equilibrio tale da garantire che la firma di accesso condiviso sia di breve durata (come indicato in precedenza) e che il client richieda il rinnovo in tempo utile per evitare malfunzionamenti causati dalla scadenza della firma prima del rinnovo.
- **Prestare attenzione all'ora di inizio della firma di accesso condiviso.** Se si imposta l'ora di inizio della firma di accesso condiviso su **ora**, a causa dello sfasamento di orario, ovvero delle differenze dell'ora corrente a seconda del computer in uso, potrebbero verificarsi problemi intermittenti nei primi minuti. In generale, impostare l'ora di inizio ad almeno 15 minuti prima. Oppure evitare di impostarla, in modo che la firma diventi immediatamente valida in tutti i casi. Le stesse considerazioni sono valide anche per la scadenza, pertanto è consigliabile osservare fino a 15 minuti di sfasamento di orario in entrambe le direzioni per qualsiasi richiesta. Per i client che usano una versione di REST precedente la 2012-02-12, la durata massima della firma di accesso condiviso che non fa riferimento a criteri di accesso archiviati è di 1 ora, pertanto tutti i criteri di durata maggiore non saranno validi.
- **Indicare in modo specifico la risorsa cui accedere.** Una procedura di sicurezza consigliata consiste nel fornire a un utente i privilegi minimi necessari. Se un utente necessita solo dell'accesso in lettura a una singola entità, concedere solo tale tipo di accesso per tale entità e non l'accesso in lettura/scrittura/eliminazione per tutte le entità. Ciò consente anche di ridurre i danni se una firma di accesso condiviso viene compromessa in quanto la firma è meno potente nelle mani di un utente malintenzionato.
- **Tenere presente che l'account verrà fatturato per qualsiasi utilizzo, inclusa la firma di accesso condiviso.** Se si fornisce l'accesso in scrittura a un BLOB, un utente può scegliere di caricare un BLOB di 200 GB. Se si offre anche l'accesso in lettura, l'utente potrebbe scegliere di scaricarlo 10 volte e ciò potrebbe comportare 2 TB di costi in uscita. Anche in questo caso, fornire autorizzazioni limitate per ridurre l'impatto potenziale delle azioni di utenti malintenzionati. Per ridurre questa minaccia, usare firme di accesso condiviso di breve durata, prestando però attenzione allo sfasamento di orario per la scadenza.
- **Convalida i dati scritti con una firma di accesso condiviso.** Quando un'applicazione client scrive i dati nell'account di archiviazione, tenere presente che tali dati potrebbero causare problemi. Se l'applicazione richiede che i dati siano convalidati o autorizzati prima dell'uso, è necessario eseguire la convalida dopo la scrittura dei dati e prima che vengano usati dall'applicazione. Questa procedura consente inoltre di evitare la scrittura di dati danneggiati o dannosi nell'account da parte da un utente che ha acquisito correttamente la firma di accesso condiviso o di un utente che sfrutta una firma diffusa per errore.
- **Verificare quando non usare una firma di accesso condiviso.** A volte i rischi associati a una determinata operazione sull'account di archiviazione superano i vantaggi derivanti dall'uso di una firma di accesso condiviso. Per tali operazioni creare un servizio di livello intermedio che effettui operazioni di scrittura nell'account di archiviazione dopo autenticazione, controllo e convalida di regole di business. Talvolta è inoltre più semplice gestire l'accesso in modi diversi. Se ad esempio si vogliono rendere pubblicamente leggibili tutti i BLOB di un contenitore, è possibile rendere pubblico il contenitore anziché fornire una firma di accesso condiviso a ogni client per l'accesso.
- **Usare monitoraggio di Azure e i log di archiviazione di Azure per monitorare l'applicazione.** È possibile usare monitoraggio di Azure e la registrazione di analisi archiviazione per osservare eventuali picchi di errori di autorizzazione causati da un'interruzione del servizio del provider SAS o dalla rimozione accidentale di criteri di accesso archiviati. Per altre informazioni, vedere [metriche di archiviazione di Azure in monitoraggio di Azure](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) e [analisi archiviazione di Azure registrazione](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Introduzione a SAS

Per iniziare a usare le firme di accesso condiviso, vedere gli articoli seguenti per ogni tipo di firma di accesso condiviso.

### <a name="user-delegation-sas"></a>SAS di delega utente

- [Creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con PowerShell (anteprima)](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Creare una firma di accesso condiviso di delega utente per un contenitore o BLOB con l'interfaccia della riga di comando di Azure (anteprima)](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con .NET (anteprima)](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Firma di accesso condiviso del servizio

- [Creare una firma di accesso condiviso del servizio per un contenitore o un BLOB con .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Firma di accesso condiviso dell'account

- [Creare una firma di accesso condiviso con .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Passaggi successivi

- [Delega dell'accesso con una firma di accesso condiviso (API REST)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Creare una firma di accesso condiviso per la delega utente (API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Creare una firma di accesso condiviso del servizio (API REST)](/rest/api/storageservices/create-service-sas)
- [Creare una firma di accesso condiviso dell'account (API REST)](/rest/api/storageservices/create-account-sas)
