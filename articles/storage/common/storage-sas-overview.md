---
title: Concedere accesso limitato ai dati con firme di accesso condiviso (SAS)
titleSuffix: Azure Storage
description: Informazioni sull'uso delle firme di accesso condiviso (SAS) per delegare l'accesso alle risorse di archiviazione di Azure, tra cui BLOB, code, tabelle e file.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: cc3a2a1ee28ad3c1fdefad08c8ec133ad45cb408
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435076"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso (SAS)

Una firma di accesso condiviso (SAS) fornisce accesso delegato sicuro alle risorse nell'account di archiviazione. Con una firma di accesso condiviso, si ha un controllo granulare sul modo in cui un client può accedere ai dati. Ad esempio:
 
- Risorse a cui il client può accedere.

- Quali autorizzazioni hanno per tali risorse.

- Quanto tempo è valido per la firma di accesso condiviso.

## <a name="types-of-shared-access-signatures"></a>Tipi di firme di accesso condiviso

Archiviazione di Azure supporta tre tipi di firme di accesso condiviso:

- SAS di delega utente

- Firma di accesso condiviso del servizio

- Firma di accesso condiviso dell'account

### <a name="user-delegation-sas"></a>SAS di delega utente

Una firma di accesso condiviso di delega utente è protetta con credenziali Azure Active Directory (Azure AD) e anche dalle autorizzazioni specificate per la firma di accesso condiviso. Una firma di accesso condiviso di delega utente si applica solo all'archiviazione BLOB.

Per altre informazioni sulla firma di accesso condiviso della delega utente, vedere [creare una firma di accesso condiviso utente (API REST)](/rest/api/storageservices/create-user-delegation-sas).

### <a name="service-sas"></a>Firma di accesso condiviso del servizio

Una firma di accesso condiviso del servizio è protetta con la chiave dell'account di archiviazione. Una firma di accesso condiviso del servizio delega l'accesso a una risorsa in uno solo dei servizi di archiviazione di Azure: archiviazione BLOB, archiviazione code, archiviazione tabelle o File di Azure.

Per altre informazioni sulla firma di accesso condiviso del servizio, vedere creare una firma di accesso condiviso del [servizio (API REST)](/rest/api/storageservices/create-service-sas).

### <a name="account-sas"></a>Firma di accesso condiviso dell'account

Una firma di accesso condiviso dell'account è protetta con la chiave dell'account di archiviazione. Una firma di accesso condiviso dell'account delega l'accesso alle risorse in uno o più servizi di archiviazione. Tutte le operazioni disponibili tramite una firma di accesso condiviso dell'utente o del servizio sono disponibili anche tramite una firma di accesso condiviso dell'account. 

È inoltre possibile delegare l'accesso agli elementi seguenti:

- Operazioni a livello di servizio (ad esempio, le **Proprietà Get/Set Service** e **Get Service Statistics** ). 

- Operazioni di lettura, scrittura ed eliminazione non consentite con una firma di accesso condiviso del servizio. 

Per altre informazioni sulla firma di accesso condiviso dell'account, creare una firma di accesso condiviso dell' [account (API REST)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> Microsoft consiglia di utilizzare le credenziali di Azure AD quando possibile come procedura di sicurezza consigliata, anziché utilizzare la chiave dell'account, che può essere compromessa in modo più semplice. Quando la progettazione dell'applicazione richiede firme di accesso condiviso per l'accesso all'archiviazione BLOB, usare le credenziali Azure AD per creare una firma di accesso condiviso di delega utente quando possibile per una sicurezza superiore. Per altre informazioni, vedere [autorizzare l'accesso a BLOB e code usando Azure Active Directory](storage-auth-aad.md).

Una firma di accesso condiviso può assumere una delle due forme seguenti:

- **SAS ad hoc**. Quando si crea una firma di accesso condiviso ad hoc, l'ora di inizio, l'ora di scadenza e le autorizzazioni vengono specificate nell'URI della firma di accesso condiviso. Qualsiasi tipo di firma di accesso condiviso può essere una firma di accesso condiviso ad hoc.

- Firma **di accesso condiviso del servizio con criteri di accesso archiviati**. Un criterio di accesso archiviato è definito in un contenitore di risorse, che può essere un contenitore BLOB, una tabella, una coda o una condivisione file. I criteri di accesso archiviati possono essere usati per gestire i vincoli per una o più firme di accesso condiviso del servizio. Quando si associa una firma di accesso condiviso del servizio a un criterio di accesso archiviato, la firma di accesso condiviso eredita i vincoli &mdash; l'ora di inizio, l'ora di scadenza e le autorizzazioni &mdash; definite per i criteri di accesso archiviati.

> [!NOTE]
> Una firma di accesso condiviso dell'utente o una firma di accesso condiviso dell'account deve essere una SAS ad hoc. I criteri di accesso archiviati non sono supportati per la firma di accesso condiviso utente o la firma di accesso condiviso dell'account.

## <a name="how-a-shared-access-signature-works"></a>Funzionamento della firma di accesso condiviso

Una firma di accesso condiviso è un URI firmato che punta a una o più risorse di archiviazione. L'URI include un token che contiene un set speciale di parametri di query. Il token indica la modalità di accesso alla risorsa da parte del client. Uno dei parametri di query, ovvero la firma, viene creato dai parametri SAS e firmato con la chiave usata per creare la firma di accesso condiviso. Questa firma viene usata da Archiviazione di Azure per autorizzare l'accesso alla risorsa di archiviazione.

### <a name="sas-signature-and-authorization"></a>Firma e autorizzazione SAS

È possibile firmare un token SAS con una chiave di delega utente o con una chiave dell'account di archiviazione (chiave condivisa). 

#### <a name="signing-a-sas-token-with-a-user-delegation-key"></a>Firma di un token SAS con una chiave di delega utente

È possibile firmare un token SAS usando una *chiave di delega utente* creata con le credenziali Azure Active Directory (Azure ad). Una firma di accesso condiviso di delega utente è firmata con la chiave di delega utente.

Per ottenere la chiave e quindi creare la firma di accesso condiviso, è necessario assegnare a un'entità di sicurezza Azure AD un ruolo di Azure che includa l' `Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey` azione. Per altre informazioni, vedere [creare una firma di accesso condiviso di delega utente (API REST)](/rest/api/storageservices/create-user-delegation-sas).

#### <a name="signing-a-sas-token-with-an-account-key"></a>Firma di un token SAS con una chiave dell'account

Una firma di accesso condiviso del servizio e una firma di accesso condiviso dell'account sono firmate con la chiave account di archiviazione Per creare una firma di accesso condiviso con la chiave dell'account, un'applicazione deve avere accesso alla chiave dell'account.

Quando una richiesta include un token di firma di accesso condiviso, la richiesta viene autorizzata in base al modo in cui il token SAS è firmato. La chiave di accesso o le credenziali usate per creare un token SAS vengono usate anche da archiviazione di Azure per concedere l'accesso a un client che possiede la firma di accesso condiviso.

La tabella seguente riepiloga il modo in cui ogni tipo di token SAS è autorizzato.

| Tipo di firma di accesso condiviso | Tipo di autorizzazione |
|-|-|
| SAS di delega utente (solo archiviazione BLOB) | Azure AD |
| Firma di accesso condiviso del servizio | Chiave condivisa |
| Firma di accesso condiviso dell'account | Chiave condivisa |

Microsoft consiglia di usare una firma di accesso condiviso di delega utente quando possibile per una sicurezza superiore.

### <a name="sas-token"></a>Token di firma di accesso condiviso

Il token di firma di accesso condiviso è una stringa generata sul lato client, ad esempio usando una delle librerie client di archiviazione di Azure. Il token di firma di accesso condiviso non viene rilevato in alcun modo da archiviazione di Azure. È possibile creare un numero illimitato di token di firma di accesso condiviso sul lato client. Dopo aver creato una firma di accesso condiviso, è possibile distribuirla alle applicazioni client che richiedono l'accesso alle risorse nell'account di archiviazione.

Le applicazioni client forniscono l'URI SAS ad archiviazione di Azure come parte di una richiesta. Il servizio controlla quindi i parametri SAS e la firma per verificare che siano validi. Se il servizio conferma che la firma è valida, la richiesta viene autorizzata. In caso contrario, la richiesta viene rifiutata con il codice errore 403 (accesso negato).

Ecco un esempio di URI di firma di accesso condiviso del servizio, che mostra l'URI della risorsa e il token di firma di accesso condiviso:

![Componenti di un URI di firma di accesso condiviso del servizio](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Quando usare una firma di accesso condiviso

Usare una firma di accesso condiviso per concedere l'accesso sicuro alle risorse nell'account di archiviazione a qualsiasi client che non disponga in altro modo delle autorizzazioni per tali risorse.

Uno scenario comune in cui la firma di accesso condiviso risulta utile è costituito da un servizio in cui gli utenti leggono e scrivono i propri dati nell'account di archiviazione di un utente specifico. Uno scenario in cui i dati utente vengono archiviati nell'account di archiviazione è caratterizzato da due modelli di progettazione standard:

1. I client caricano e scaricano dati tramite un servizio proxy front-end che esegue l'autenticazione. Questo servizio proxy front-end consente la convalida delle regole business. Per grandi quantità di dati o transazioni con volume elevato, tuttavia, la creazione di un servizio che può essere ridimensionato in base alla domanda può essere costosa o complessa.

   ![Diagramma dello scenario: il servizio proxy front-end](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

2. Un servizio semplificato autentica il client in base alle necessità e quindi genera una firma di accesso condiviso. Quando l'applicazione client riceve la firma di accesso condiviso, può accedere direttamente alle risorse dell'account di archiviazione. Le autorizzazioni di accesso vengono definite dalla firma di accesso condiviso e per l'intervallo consentito dalla firma di accesso condiviso. La firma di accesso condiviso consente di ridurre la necessità di instradare tutti i dati tramite il servizio proxy front-end.

   ![Diagramma dello scenario: servizio provider di firma di accesso condiviso](./media/storage-sas-overview/sas-storage-provider-service.png)

Molti servizi reali possono usare una combinazione di questi due approcci. Ad esempio, alcuni dati potrebbero essere elaborati e convalidati tramite il proxy front-end. Altri dati vengono salvati e/o letti direttamente tramite la firma di accesso condiviso.

Inoltre, è necessaria una firma di accesso condiviso per autorizzare l'accesso all'oggetto di origine in un'operazione di copia in determinati scenari:

- Quando si copia un BLOB in un altro BLOB che risiede in un account di archiviazione diverso. 
  
  È possibile usare una firma di accesso condiviso anche per autorizzare l'accesso al BLOB di destinazione.

- Quando si copia un file in un altro file che risiede in un account di archiviazione diverso. 

  È possibile usare una firma di accesso condiviso anche per autorizzare l'accesso al file di destinazione.

- Quando si copia un BLOB in un file o un file in un BLOB. 

  È necessario usare una firma di accesso condiviso anche se gli oggetti di origine e di destinazione si trovano nello stesso account di archiviazione.

## <a name="best-practices-when-using-sas"></a>Procedure consigliate per l'uso di SAS

Quando si utilizzano le firme di accesso condiviso nell'applicazione, è necessario essere consapevoli di due rischi potenziali:

- In caso di diffusione di una firma di accesso condiviso, chiunque la ottiene può usarla e questo potrebbe compromettere l'account di archiviazione.

- Se una firma di accesso condiviso fornita a un'applicazione client scade e l'applicazione non è in grado di recuperarne una nuova dal servizio, è possibile che le funzionalità dell'applicazione potrebbero risentirne.

Per mitigare questi rischi, è consigliabile attenersi ai consigli seguenti relativi all'utilizzo di firme di accesso condiviso:

- **Usare sempre HTTPS** per creare o distribuire una firma di accesso condiviso. Se una firma di accesso condiviso viene passata tramite HTTP e intercettata, un utente malintenzionato che esegue un attacco man-in-the-Middle è in grado di leggere la firma di accesso condiviso. Quindi, possono usare la firma di accesso condiviso così come l'utente previsto. Ciò può compromettere i dati sensibili o consentire il danneggiamento dei dati da parte di utenti malintenzionati.

- **Quando possibile, utilizzare una firma di accesso condiviso di delega utente.** Una firma di accesso condiviso di delega utente garantisce una sicurezza superiore a una firma di accesso condiviso del servizio o a una firma Una firma di accesso condiviso di delega utente è protetta con Azure AD credenziali, in modo che non sia necessario archiviare la chiave dell'account con il codice.

- **Disporre di un piano di revoca per una firma di accesso condiviso.** Assicurarsi di essere pronti a rispondere se una firma di accesso condiviso è compromessa.

- **Definire un criterio di accesso archiviato per una firma di accesso condiviso del servizio.** I criteri di accesso archiviati offrono la possibilità di revocare le autorizzazioni per una firma di accesso condiviso del servizio senza dover rigenerare le chiavi dell'account di archiviazione. Impostare la loro scadenza a un momento molto lontano nel futuro (o infinito) e assicurarsi che venga aggiornata regolarmente in modo che ricorra nel futuro.

- **Usare i tempi di scadenza a breve termine per una firma di accesso condiviso ad hoc o una firma di accesso condiviso del servizio.** In questo modo, anche se una firma di accesso condiviso viene compromessa, è valida solo per un breve periodo di tempo. Questo consiglio è particolarmente importante se non è possibile fare riferimento a criteri di accesso archiviati Una scadenza breve consente anche di limitare la quantità di dati che è possibile scrivere in un BLOB riducendo il tempo disponibile per il caricamento.

- **Se necessario, chiedere ai client di rinnovare automaticamente la firma di accesso condiviso..** È consigliabile rinnovare la firma di accesso condiviso nei client prima della scadenza in modo da avere la possibilità di effettuare altri tentativi qualora il servizio che fornisce la firma di accesso condiviso non sia disponibile. Questa operazione potrebbe non essere necessaria in alcuni casi. Ad esempio, è possibile che la firma di accesso condiviso venga utilizzata per un numero ridotto di operazioni immediate e di breve durata. Queste operazioni dovrebbero essere completate entro il periodo di scadenza. Di conseguenza, non si prevede che la firma di accesso condiviso venga rinnovata. Tuttavia, se si dispone di un client che esegue periodicamente richieste tramite SAS, viene rilevata la possibilità di scadenza. 

- **Prestare attenzione all'ora di inizio della firma di accesso condiviso.** Se si imposta l'ora di inizio per una firma di accesso condiviso sull'ora corrente, è possibile che si verifichino errori intermittenti per i primi minuti. Ciò è dovuto a computer diversi con tempi correnti leggermente diversi, noti come sfasamento dell'orologio. In generale, impostare l'ora di inizio ad almeno 15 minuti prima. Oppure evitare di impostarla, in modo che la firma diventi immediatamente valida in tutti i casi. Le stesse considerazioni sono valide anche per la scadenza, pertanto è consigliabile osservare fino a 15 minuti di sfasamento di orario in entrambe le direzioni per qualsiasi richiesta. Per i client che usano una versione REST precedente alla 2012-02-12, la durata massima di una firma di accesso condiviso che non fa riferimento a criteri di accesso archiviati è di 1 ora. Tutti i criteri che specificano un periodo di tempo più lungo di un'ora avranno esito negativo.

- **Prestare attenzione al formato DateTime SAS.** Per alcune utilità (ad esempio AzCopy), è necessario che i formati DateTime siano ' +% Y-% m-% dT% H:%M:% SZ '. Questo formato include in particolare i secondi. 

- **Indicare in modo specifico la risorsa cui accedere.** Una procedura di sicurezza consigliata consiste nel fornire a un utente i privilegi minimi necessari. Se un utente necessita solo dell'accesso in lettura a una singola entità, concedere solo tale tipo di accesso per tale entità e non l'accesso in lettura/scrittura/eliminazione per tutte le entità. Ciò consente anche di ridurre i danni se una firma di accesso condiviso viene compromessa in quanto la firma è meno potente nelle mani di un utente malintenzionato.

- **Tenere presente che l'account verrà fatturato per qualsiasi utilizzo, inclusa la firma di accesso condiviso.** Se si fornisce l'accesso in scrittura a un BLOB, un utente può scegliere di caricare un BLOB di 200 GB. Se si offre anche l'accesso in lettura, l'utente potrebbe scegliere di scaricarlo 10 volte e ciò potrebbe comportare 2 TB di costi in uscita. Anche in questo caso, fornire autorizzazioni limitate per ridurre l'impatto potenziale delle azioni di utenti malintenzionati. Per ridurre questa minaccia, usare firme di accesso condiviso di breve durata, prestando però attenzione allo sfasamento di orario per la scadenza.

- **Convalida i dati scritti con una firma di accesso condiviso.**  Quando un'applicazione client scrive i dati nell'account di archiviazione, tenere presente che tali dati potrebbero causare problemi. Se si prevede di convalidare i dati, eseguire la convalida dopo che i dati sono stati scritti e prima che vengano usati dall'applicazione. Questa procedura consente inoltre di evitare la scrittura di dati danneggiati o dannosi nell'account da parte da un utente che ha acquisito correttamente la firma di accesso condiviso o di un utente che sfrutta una firma diffusa per errore.

- **Verificare quando non usare una firma di accesso condiviso.** A volte i rischi associati a una determinata operazione sull'account di archiviazione superano i vantaggi derivanti dall'uso di una firma di accesso condiviso. Per tali operazioni creare un servizio di livello intermedio che effettui operazioni di scrittura nell'account di archiviazione dopo autenticazione, controllo e convalida di regole di business. Talvolta è inoltre più semplice gestire l'accesso in modi diversi. Se ad esempio si vogliono rendere pubblicamente leggibili tutti i BLOB di un contenitore, è possibile rendere pubblico il contenitore anziché fornire una firma di accesso condiviso a ogni client per l'accesso.

- **Usare monitoraggio di Azure e i log di archiviazione di Azure per monitorare l'applicazione.** Gli errori di autorizzazione possono verificarsi a causa di un'interruzione nel servizio del provider SAS. Possono anche verificarsi da una rimozione accidentale di criteri di accesso archiviati. È possibile usare monitoraggio di Azure e la registrazione di analisi archiviazione per osservare eventuali picchi in questi tipi di errori di autorizzazione. Per altre informazioni, vedere [metriche di archiviazione di Azure in monitoraggio di Azure](../blobs/monitor-blob-storage.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) e [analisi archiviazione di Azure registrazione](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Introduzione a SAS

Per iniziare a usare le firme di accesso condiviso, vedere gli articoli seguenti per ogni tipo di firma di accesso condiviso.

### <a name="user-delegation-sas"></a>SAS di delega utente

- [Creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con PowerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Creare una firma di accesso condiviso per un contenitore o un BLOB con l'interfaccia della riga di comando di Azure](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Firma di accesso condiviso del servizio

- [Creare una firma di accesso condiviso del servizio per un contenitore o un BLOB con .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Firma di accesso condiviso dell'account

- [Creare una firma di accesso condiviso con .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Passaggi successivi

- [Delega dell'accesso con una firma di accesso condiviso (API REST)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Creare una firma di accesso condiviso per la delega utente (API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Creare una firma di accesso condiviso del servizio (API REST)](/rest/api/storageservices/create-service-sas)
- [Creare una firma di accesso condiviso dell'account (API REST)](/rest/api/storageservices/create-account-sas)