---
title: Impedisci l'autorizzazione con la chiave condivisa (anteprima)
titleSuffix: Azure Storage
description: Per richiedere ai client di usare Azure AD per autorizzare le richieste, è possibile non consentire le richieste all'account di archiviazione autorizzato con la chiave condivisa (anteprima).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/20/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 9bf656989dc331fdd4ce044126ea9d0be9414930
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088800"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account-preview"></a>Impedisci l'autorizzazione della chiave condivisa per un account di archiviazione di Azure (anteprima)

Ogni richiesta sicura a un account di archiviazione di Azure deve essere autorizzata. Per impostazione predefinita, le richieste possono essere autorizzate con le credenziali Azure Active Directory (Azure AD) oppure usando la chiave di accesso dell'account per l'autorizzazione della chiave condivisa. Di questi due tipi di autorizzazione, Azure AD offre sicurezza e facilità d'uso superiori rispetto alla chiave condivisa ed è consigliata da Microsoft. Per richiedere ai client di usare Azure AD per autorizzare le richieste, è possibile non consentire le richieste all'account di archiviazione autorizzato con la chiave condivisa (anteprima).

Quando si impedisce l'autorizzazione della chiave condivisa per un account di archiviazione, archiviazione di Azure rifiuta tutte le richieste successive a tale account che sono autorizzate con le chiavi di accesso dell'account. Verranno riuscite solo le richieste protette autorizzate con Azure AD. Per altre informazioni sull'uso di Azure AD, vedere [autorizzare l'accesso a BLOB e code usando Azure Active Directory](storage-auth-aad.md).

> [!WARNING]
> Archiviazione di Azure supporta Azure AD autorizzazione solo per le richieste all'archiviazione BLOB e code. Se non si consente l'autorizzazione con la chiave condivisa per un account di archiviazione, le richieste di File di Azure o l'archiviazione tabelle che usano l'autorizzazione della chiave condivisa avranno esito negativo.
>
> Durante l'anteprima, le richieste di File di Azure o l'archiviazione tabelle che usano token di firma di accesso condiviso (SAS) generate con le chiavi di accesso dell'account riusciranno quando l'autorizzazione della chiave condivisa non è consentita. Per ulteriori informazioni, vedere [informazioni sull'anteprima](#about-the-preview).
>
> La disattivazione dell'accesso con chiave condivisa per un account di archiviazione non influisce sulle connessioni SMB ai File di Azure.
>
> Microsoft consiglia di eseguire la migrazione di tutti i dati di archiviazione di File di Azure o tabelle in un account di archiviazione separato prima di impedire l'accesso all'account tramite chiave condivisa o di non applicare questa impostazione agli account di archiviazione che supportano i carichi di lavoro di File di Azure o di archiviazione tabelle.

Questo articolo descrive come rilevare le richieste inviate con l'autorizzazione della chiave condivisa e come correggere l'autorizzazione della chiave condivisa per l'account di archiviazione. Per informazioni su come eseguire la registrazione per l'anteprima, vedere [informazioni sull'anteprima](#about-the-preview).

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>Rilevare il tipo di autorizzazione utilizzata dalle applicazioni client

Quando si impedisce l'autorizzazione della chiave condivisa per un account di archiviazione, le richieste provenienti dai client che usano le chiavi di accesso dell'account per l'autorizzazione della chiave condivisa avranno esito negativo. Per comprendere in che modo la disabilitazione dell'autorizzazione della chiave condivisa può influire sulle applicazioni client prima di apportare questa modifica, abilitare la registrazione e le metriche per l'account di archiviazione. È quindi possibile analizzare i modelli di richieste al proprio account in un determinato periodo di tempo per determinare il modo in cui le richieste vengono autorizzate.

Usare le metriche per determinare il numero di richieste ricevute dall'account di archiviazione autorizzate con la chiave condivisa o con una firma di accesso condiviso (SAS). Usare i log per determinare quali client inviano tali richieste.

Per ulteriori informazioni sull'interpretazione delle richieste effettuate con una firma di accesso condiviso durante l'anteprima, vedere [informazioni sull'anteprima](#about-the-preview).

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>Monitorare il numero di richieste autorizzate con la chiave condivisa

Per tenere traccia del modo in cui vengono autorizzate le richieste a un account di archiviazione, usare Azure Esplora metriche nel portale di Azure. Per ulteriori informazioni su Esplora metriche, vedere la pagina relativa all' [Introduzione ad Azure Esplora metriche](../../azure-monitor/platform/metrics-getting-started.md).

Seguire questa procedura per creare una metrica per tenere traccia delle richieste effettuate con la chiave condivisa o la firma di accesso condiviso:

1. Passare all'account di archiviazione nel portale di Azure. Nella sezione **monitoraggio** selezionare **metriche**.
1. Selezionare **Aggiungi metrica**. Nella finestra di dialogo **metrica** specificare i valori seguenti:
    1. Lasciare il campo **ambito** impostato sul nome dell'account di archiviazione.
    1. Impostare lo **spazio dei nomi della metrica** su *account*. Questa metrica segnalerà tutte le richieste nell'account di archiviazione.
    1. Impostare il campo **metrica** su *transazioni*.
    1. Impostare il campo di **aggregazione** su *Sum*.

    La nuova metrica visualizzerà la somma del numero di transazioni rispetto all'account di archiviazione in un determinato intervallo di tempo. La metrica risultante viene visualizzata come illustrato nell'immagine seguente:

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="Screenshot che illustra come configurare la metrica per le transazioni Sum effettuate con la chiave condivisa o con la firma di accesso condiviso":::

1. Selezionare quindi il pulsante **Aggiungi filtro** per creare un filtro sulla metrica per il tipo di autorizzazione.
1. Nella finestra di dialogo **filtro** specificare i valori seguenti:
    1. Impostare il valore della **Proprietà** su *Authentication*.
    1. Impostare il campo **operatore** sul segno di uguale (=).
    1. Nel campo **valori** selezionare *chiave account* *e firma di*accesso condiviso.
1. Nell'angolo in alto a destra selezionare l'intervallo di tempo per il quale si desidera visualizzare la metrica. È anche possibile indicare la granularità dell'aggregazione delle richieste, specificando gli intervalli da 1 minuto a 1 mese. Ad esempio, impostare l' **intervallo di tempo** su 30 giorni e la **granularità dell'ora** su 1 giorno per visualizzare le richieste aggregate per giorno negli ultimi 30 giorni.

Dopo aver configurato la metrica, le richieste all'account di archiviazione inizieranno a essere visualizzate nel grafico. Nell'immagine seguente vengono illustrate le richieste autorizzate con chiave condivisa o effettuate con un token SAS. Le richieste vengono aggregate ogni giorno negli ultimi 30 giorni.

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="Screenshot che mostra le richieste aggregate autorizzate con la chiave condivisa":::

È anche possibile configurare una regola di avviso per ricevere una notifica quando viene effettuato un determinato numero di richieste autorizzate con chiave condivisa per l'account di archiviazione. Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>Analizzare i log per identificare i client che autorizzano le richieste con chiave condivisa o SAS

I log di archiviazione di Azure acquisiscono informazioni dettagliate sulle richieste effettuate nell'account di archiviazione, inclusa la modalità di autorizzazione di una richiesta. È possibile analizzare i log per determinare quali client autorizzano le richieste con la chiave condivisa o un token di firma di accesso condiviso.

Per registrare le richieste nell'account di archiviazione di Azure per valutare il modo in cui sono autorizzate, è possibile usare la registrazione di archiviazione di Azure in monitoraggio di Azure (anteprima). Per altre informazioni, vedere [monitorare archiviazione di Azure](../common/monitor-storage.md).

Registrazione di archiviazione di Azure in monitoraggio di Azure supporta l'uso di query di log per analizzare i dati di log. Per eseguire query sui log, è possibile usare un'area di lavoro di Azure Log Analytics. Per altre informazioni sulle query di log, vedere [esercitazione: Introduzione alle query log Analytics](../../azure-monitor/log-query/get-started-portal.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Creare un'impostazione di diagnostica nel portale di Azure

Per registrare i dati di archiviazione di Azure con monitoraggio di Azure e analizzarli con Log Analytics di Azure, è prima necessario creare un'impostazione di diagnostica che indichi quali tipi di richieste e per quali servizi di archiviazione si desidera registrare i dati. Per creare un'impostazione di diagnostica nella portale di Azure, attenersi alla procedura seguente:

1. Registrare l' [Anteprima di registrazione di archiviazione di Azure in monitoraggio di Azure](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Creare una nuova area di lavoro Log Analytics nella sottoscrizione che contiene l'account di archiviazione di Azure oppure usare un'area di lavoro Log Analytics esistente. Dopo aver configurato la registrazione per l'account di archiviazione, i log saranno disponibili nell'area di lavoro Log Analytics. Per altre informazioni, vedere [Creare un'area di lavoro Log Analytics nel portale di Azure](../../azure-monitor/learn/quick-create-workspace.md).
1. Passare all'account di archiviazione nel portale di Azure.
1. Nella sezione monitoraggio selezionare impostazioni di **diagnostica (anteprima)**.
1. Selezionare il servizio di archiviazione di Azure per cui si vogliono registrare le richieste. Ad esempio, scegliere **BLOB** per registrare le richieste nell'archivio BLOB.
1. Selezionare **Aggiungi impostazioni di diagnostica**.
1. Consente di specificare un nome per l'impostazione di diagnostica.
1. In **Dettagli categoria**, nella sezione **log** , scegliere **StorageRead**, **StorageWrite**e **StorageDelete** per registrare tutte le richieste di dati al servizio selezionato.
1. In **Dettagli destinazione**selezionare **Invia a log Analytics**. Selezionare la sottoscrizione e l'area di lavoro Log Analytics creata in precedenza, come illustrato nella figura seguente.

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="Screenshot che illustra come creare un'impostazione di diagnostica per la registrazione delle richieste":::

È possibile creare un'impostazione di diagnostica per ogni tipo di risorsa di archiviazione di Azure nell'account di archiviazione.

Dopo aver creato l'impostazione di diagnostica, le richieste all'account di archiviazione vengono registrate successivamente in base a tale impostazione. Per altre informazioni, vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche delle risorse in Azure](../../azure-monitor/platform/diagnostic-settings.md).

Per informazioni di riferimento sui campi disponibili nei log di archiviazione di Azure in monitoraggio di Azure, vedere [log delle risorse (anteprima)](../common/monitor-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>Log di query per le richieste effettuate con la chiave condivisa o SAS

I log di archiviazione di Azure in monitoraggio di Azure includono il tipo di autorizzazione usata per effettuare una richiesta a un account di archiviazione. Per recuperare i log delle richieste effettuate negli ultimi sette giorni autorizzati con la chiave condivisa o la firma di accesso condiviso, aprire l'area di lavoro Log Analytics. Incollare quindi la query seguente in una nuova query di log ed eseguirla. Questa query Visualizza i dieci indirizzi IP che hanno inviato più volte le richieste che sono state autorizzate con la chiave condivisa o la firma di accesso condiviso:

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

È anche possibile configurare una regola di avviso basata su questa query per notificare all'utente le richieste autorizzate con la chiave condivisa o la firma di accesso condiviso. Per altre informazioni, vedere [creare, visualizzare e gestire gli avvisi dei log con monitoraggio di Azure](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-authorization-via-shared-key"></a>Correggere l'autorizzazione tramite chiave condivisa

Dopo aver analizzato l'autorizzazione delle richieste per l'account di archiviazione, è possibile intervenire per impedire l'accesso tramite chiave condivisa. Prima di tutto, è necessario aggiornare tutte le applicazioni che usano l'autorizzazione della chiave condivisa per usare invece Azure AD. È possibile monitorare i log e le metriche come descritto in [rilevare il tipo di autorizzazione usato dalle applicazioni client](#detect-the-type-of-authorization-used-by-client-applications) per tenere traccia della transizione. Per altre informazioni sull'uso di Azure AD con i dati di BLOB e di Accodamento, vedere [autorizzare l'accesso a BLOB e code usando Azure Active Directory](storage-auth-aad.md).

Quando si è certi che è possibile rifiutare in modo sicuro le richieste autorizzate con la chiave condivisa, è possibile impostare la proprietà **AllowSharedKeyAccess** per l'account di archiviazione su **false**.

Per impostazione predefinita, la proprietà **AllowSharedKeyAccess** non viene impostata e non restituisce alcun valore finché non viene impostata in modo esplicito. L'account di archiviazione consente le richieste che sono autorizzate con la chiave condivisa quando il valore della proprietà è **null** o quando è **true**.

> [!WARNING]
> Se un client sta attualmente accedendo ai dati nell'account di archiviazione con chiave condivisa, Microsoft consiglia di eseguire la migrazione di tali client a Azure AD prima di impedire l'accesso con chiave condivisa all'account di archiviazione.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per non consentire l'autorizzazione della chiave condivisa per un account di archiviazione nel portale di Azure, attenersi alla procedura seguente:

1. Passare all'account di archiviazione nel portale di Azure.
1. Individuare l'impostazione di **configurazione** in **Impostazioni**.
1. Impostare **Consenti accesso chiave condivisa** su **disabilitato**.

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="Screenshot che illustra come impedire l'accesso con chiave condivisa per l'account":::

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per impedire l'autorizzazione della chiave condivisa per un account di archiviazione con l'interfaccia della riga di comando di Azure, installare l'interfaccia della riga di comando di Azure 2.9.1 Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Successivamente, configurare la proprietà **allowSharedKeyAccess** per un account di archiviazione nuovo o esistente.

L'esempio seguente illustra come impostare la proprietà **allowSharedKeyAccess** con l'interfaccia della riga di comando di Azure. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
$storage_account_id=$(az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowSharedKeyAccess=false

az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowSharedKeyAccess \
    --output tsv
```

---

Dopo la disattivazione dell'autorizzazione della chiave condivisa, l'esecuzione di una richiesta all'account di archiviazione con autorizzazione della chiave condivisa avrà esito negativo con codice di errore 403 (accesso negato). Archiviazione di Azure restituisce un errore che indica che l'autorizzazione basata su chiavi non è consentita nell'account di archiviazione.

### <a name="verify-that-shared-key-access-is-not-allowed"></a>Verificare che l'accesso alla chiave condivisa non sia consentito

Per verificare che l'autorizzazione della chiave condivisa non sia più consentita, è possibile provare a chiamare un'operazione sui dati con la chiave di accesso dell'account. Nell'esempio seguente viene effettuato un tentativo di creazione di un contenitore utilizzando la chiave di accesso. Questa chiamata avrà esito negativo quando l'autorizzazione della chiave condivisa non è consentita per l'account di archiviazione. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!NOTE]
> Le richieste anonime non sono autorizzate e continueranno se sono stati configurati l'account di archiviazione e il contenitore per l'accesso in lettura pubblico anonimo. Per altre informazioni, vedere [configurare l'accesso in lettura pubblico anonimo per contenitori e BLOB](../blobs/anonymous-read-access-configure.md).

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>Controllare l'impostazione di accesso alla chiave condivisa per più account

Per controllare l'impostazione di accesso alla chiave condivisa in un set di account di archiviazione con prestazioni ottimali, è possibile usare Azure Resource Graph Explorer nella portale di Azure. Per altre informazioni sull'uso di Esplora grafico risorse, vedere [Guida introduttiva: eseguire la prima query di Resource Graph con Esplora risorse di Azure](/azure/governance/resource-graph/first-query-portal).

Eseguendo la query seguente in Resource Graph Explorer viene restituito un elenco di account di archiviazione e viene visualizzata l'impostazione di accesso alla chiave condivisa per ogni account:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>Informazioni su come la disabilitazione della chiave condivisa influisca sui token SAS

Quando la chiave condivisa non è consentita per l'account di archiviazione, archiviazione di Azure gestisce i token SAS in base al tipo di firma di accesso condiviso e al servizio di destinazione della richiesta. La tabella seguente illustra come viene autorizzato ogni tipo di firma di accesso condiviso e come archiviazione di Azure gestirà tale firma di accesso condiviso quando la proprietà **AllowSharedKeyAccess** per l'account di archiviazione è **false**.

| Tipo di firma di accesso condiviso | Tipo di autorizzazione | Comportamento quando AllowSharedKeyAccess è false |
|-|-|-|
| SAS di delega utente (solo archiviazione BLOB) | Azure AD | La richiesta è consentita. Microsoft consiglia di usare una firma di accesso condiviso di delega utente quando possibile per una sicurezza superiore. |
| Firma di accesso condiviso del servizio | Chiave condivisa | La richiesta è stata negata per l'archiviazione BLOB. La richiesta è consentita per l'archiviazione delle code e tabelle e per File di Azure. Per altre informazioni, vedere le richieste con token di firma di accesso condiviso [sono consentite per le code, le tabelle e i file quando AllowSharedKeyAccess è false](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false) nella sezione **About The Preview** . |
| Firma di accesso condiviso dell'account | Chiave condivisa | La richiesta è stata negata per l'archiviazione BLOB. La richiesta è consentita per l'archiviazione delle code e tabelle e per File di Azure. Per altre informazioni, vedere le richieste con token di firma di accesso condiviso [sono consentite per le code, le tabelle e i file quando AllowSharedKeyAccess è false](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false) nella sezione **About The Preview** . |

Per altre informazioni sulle firme di accesso condiviso, vedere [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso](storage-sas-overview.md).

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>Considerare la compatibilità con altri strumenti e servizi di Azure

Molti servizi di Azure usano l'autorizzazione per la chiave condivisa per comunicare con archiviazione di Azure. Se non si consente l'autorizzazione della chiave condivisa per un account di archiviazione, questi servizi non saranno in grado di accedere ai dati nell'account e le applicazioni potrebbero essere compromesse.

Alcuni strumenti di Azure offrono la possibilità di usare Azure AD autorizzazione per accedere ad archiviazione di Azure. La tabella seguente elenca alcuni strumenti di Azure più diffusi e rileva se possono usare Azure AD per autorizzare le richieste ad archiviazione di Azure.

| Strumento di Azure | Azure AD autorizzazione per archiviazione di Azure |
|-|-|
| Portale di Azure | Supportata. Per informazioni sull'autorizzazione con l'account Azure AD dall'portale di Azure, vedere [scegliere come autorizzare l'accesso ai dati BLOB nel portale di Azure](../blobs/authorize-blob-access-portal.md). |
| AzCopy | Supportato per l'archiviazione BLOB. Per informazioni su come autorizzare le operazioni di AzCopy, vedere [scegliere come fornire le credenziali di autorizzazione](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials) nella documentazione di AzCopy. |
| Esplora archivi Azure | Supportato solo per l'archiviazione BLOB e Azure Data Lake Storage Gen2. Azure AD l'accesso all'archiviazione code non è supportato. Assicurarsi di selezionare il tenant Azure AD corretto. Per ulteriori informazioni, vedere [Introduzione a Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows#sign-in-to-azure) |
| Azure PowerShell | Supportata. Per informazioni su come autorizzare i comandi di PowerShell per operazioni BLOB o di Accodamento con Azure AD, vedere [eseguire comandi di PowerShell con Azure ad credenziali per accedere ai dati BLOB](../blobs/authorize-active-directory-powershell.md) o [eseguire comandi di PowerShell con Azure ad credenziali per accedere ai dati della coda](../queues/authorize-active-directory-powershell.md). |
| Interfaccia della riga di comando di Azure | Supportata. Per informazioni su come autorizzare i comandi dell'interfaccia della riga di comando di Azure con Azure AD per l'accesso ai dati BLOB e di Accodamento, vedere [eseguire comandi dell'interfaccia della riga di comando di Azure con Azure ad credenziali per accedere ai dati BLOB](authorize-data-operations-cli.md) |
| Hub IoT Azure | Supportata. Per altre informazioni, vedere [supporto dell'hub Internet per le reti virtuali](../../iot-hub/virtual-network-support.md). |
| Azure Cloud Shell | Azure Cloud Shell è una shell integrata nel portale di Azure. Azure Cloud Shell ospita file per la persistenza in una condivisione file di Azure in un account di archiviazione. Questi file diventeranno inaccessibili se l'autorizzazione della chiave condivisa non è consentita per l'account di archiviazione. Per altre informazioni, vedere [connettere l'archiviazione dei file di Microsoft Azure](/azure/cloud-shell/overview#connect-your-microsoft-azure-files-storage). <br /><br /> Per eseguire i comandi in Azure Cloud Shell per gestire gli account di archiviazione per cui non è consentito l'accesso con chiave condivisa, verificare prima di tutto che siano state concesse le autorizzazioni necessarie per questi account tramite il controllo degli accessi in base al ruolo (RBAC). Per altre informazioni, vedere informazioni [sul controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md). |

## <a name="about-the-preview"></a>Informazioni sulla versione di anteprima

L'anteprima per la disabilitazione dell'autorizzazione della chiave condivisa è disponibile nel cloud pubblico di Azure. È supportato per gli account di archiviazione che usano solo il modello di distribuzione Azure Resource Manager. Per informazioni sugli account di archiviazione che usano il modello di distribuzione Azure Resource Manager, vedere [tipi di account di archiviazione](storage-account-overview.md#types-of-storage-accounts).

Per eseguire la registrazione per l'anteprima, vedere [archiviazione di Azure Consenti l'anteprima pubblica di accesso con chiave condivisa limitata](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUN1o4TUtUUzZBV0JYVlhKQ1FITDlVUUU0Ui4u).

> [!IMPORTANT]
> Questa versione di anteprima è destinata solo all'uso in ambienti non di produzione.

Nell'anteprima sono incluse le limitazioni descritte nelle sezioni riportate di seguito.

### <a name="metrics-and-logging-report-all-requests-made-with-a-sas-regardless-of-how-they-are-authorized"></a>Le metriche e la registrazione segnalano tutte le richieste effettuate con una firma di accesso condiviso indipendentemente dalla loro autorizzazione

Le metriche e la registrazione di Azure in monitoraggio di Azure non distinguono tra i diversi tipi di firme di accesso condiviso nell'anteprima. Il filtro **SAS** in Azure Esplora metriche e il campo **SAS** nella registrazione di archiviazione di Azure in Azure monitorano entrambe le richieste di report autorizzate con qualsiasi tipo di firma di accesso condiviso. Tuttavia, i diversi tipi di firme di accesso condiviso sono autorizzati in modo diverso e si comportano in modo diverso quando l'accesso alla chiave condivisa non è consentito:

- Un token di firma di accesso condiviso del servizio o un token di firma di accesso condiviso dell'account è autorizzato con chiave condivisa e non sarà consentito per una richiesta all'archiviazione BLOB quando la proprietà **AllowSharedKeyAccess** è impostata su **false**.
- Una firma di accesso condiviso di delega utente è autorizzata con Azure AD e sarà consentita su una richiesta di archiviazione BLOB quando la proprietà **AllowSharedKeyAccess** è impostata su **false**.

Quando si valuta il traffico verso l'account di archiviazione, tenere presente che le metriche e i log come descritto in [rilevare il tipo di autorizzazione usato dalle applicazioni client](#detect-the-type-of-authorization-used-by-client-applications) possono includere le richieste effettuate con una firma di accesso condiviso dell'utente. Per altre informazioni sul modo in cui archiviazione di Azure risponde a una firma di accesso condiviso quando la proprietà **AllowSharedKeyAccess** è impostata su **false**, vedere [informazioni sulla disabilitazione della chiave condivisa sui token SAS](#understand-how-disallowing-shared-key-affects-sas-tokens).

### <a name="requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false"></a>Le richieste con token di firma di accesso condiviso sono consentite per le code, le tabelle e i file quando AllowSharedKeyAccess è false

Quando l'accesso con chiave condivisa non è consentito per l'account di archiviazione durante l'anteprima, le firme di accesso condiviso che hanno come destinazione le risorse di Accodamento, tabelle o File di Azure continuano a essere consentite. Questa limitazione si applica sia ai token SAS del servizio sia ai token di firma di accesso condiviso dell'account. Entrambi i tipi di firma di accesso condiviso sono autorizzati con chiave condivisa.

## <a name="next-steps"></a>Passaggi successivi

- [Autorizzazione dell'accesso ai dati in archiviazione di Azure](storage-auth.md)
- [Autorizzare l'accesso a BLOB e code usando Azure Active Directory](storage-auth-aad.md)
- [Autorizzare con la chiave condivisa](/rest/api/storageservices/authorize-with-shared-key)