---
title: Impedisci l'accesso in lettura pubblico anonimo a contenitori e BLOB
titleSuffix: Azure Storage
description: ''
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/06/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 90d7cd65bbc07524391f34fe0efce2b044664cef
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209647"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Impedisci l'accesso in lettura pubblico anonimo a contenitori e BLOB

L'accesso in lettura pubblico anonimo a contenitori e BLOB in archiviazione di Azure è un modo pratico per condividere i dati, ma può anche presentare un rischio per la sicurezza. È importante abilitare l'accesso anonimo in modo oculato e comprendere come valutare l'accesso anonimo ai dati. La complessità operativa, gli errori umani o gli attacchi dannosi contro i dati accessibili pubblicamente possono comportare violazioni dei dati costose. Microsoft consiglia di abilitare l'accesso anonimo solo quando necessario per lo scenario dell'applicazione.

Per impostazione predefinita, un account di archiviazione consente a un utente con le autorizzazioni appropriate di configurare l'accesso pubblico a contenitori e BLOB. È possibile disabilitare questa funzionalità a livello dell'account di archiviazione, in modo che i contenitori e i BLOB nell'account non possano essere configurati per l'accesso pubblico.

Questo articolo descrive come analizzare le richieste anonime in un account di archiviazione e come impedire l'accesso anonimo per l'intero account di archiviazione o per un singolo contenitore.

## <a name="detect-anonymous-requests-from-client-applications"></a>Rilevare richieste anonime da applicazioni client

Quando si disabilita l'accesso in lettura pubblico per un account di archiviazione, si rischia di rifiutare le richieste a contenitori e BLOB attualmente configurati per l'accesso pubblico. La disabilitazione dell'accesso pubblico per un account di archiviazione sostituisce le impostazioni di accesso pubblico per tutti i contenitori nell'account di archiviazione. Quando l'accesso pubblico è disabilitato per l'account di archiviazione, eventuali richieste anonime future a tale account avranno esito negativo.

Per comprendere in che modo la disabilitazione dell'accesso pubblico può influire sulle applicazioni client, Microsoft consiglia di abilitare la registrazione e le metriche per tale account e analizzare i modelli di richieste anonime in un intervallo di tempo. Usare le metriche per determinare il numero di richieste anonime per l'account di archiviazione e usare i log per determinare a quali contenitori viene eseguito l'accesso in modo anonimo.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Monitorare le richieste anonime con Esplora metriche

Per tenere traccia delle richieste anonime a un account di archiviazione, usare Azure Esplora metriche nel portale di Azure. Per ulteriori informazioni su Esplora metriche, vedere la pagina relativa all' [Introduzione ad Azure Esplora metriche](../../azure-monitor/platform/metrics-getting-started.md).

Seguire questa procedura per creare una metrica per tenere traccia delle richieste anonime:

1. Passare all'account di archiviazione nel portale di Azure. Nella sezione **monitoraggio** selezionare **metriche**.
1. Selezionare **Aggiungi metrica**. Nella finestra di dialogo **metrica** specificare i valori seguenti:
    1. Lasciare il campo ambito impostato sul nome dell'account di archiviazione.
    1. Impostare lo **spazio dei nomi della metrica** su *BLOB*. Questa metrica segnalerà le richieste solo nell'archivio BLOB.
    1. Impostare il campo **metrica** su *transazioni*.
    1. Impostare il campo di **aggregazione** su *Sum*.

    La nuova metrica visualizzerà la somma del numero di transazioni nell'archiviazione BLOB in un determinato intervallo di tempo. La metrica risultante viene visualizzata come illustrato nell'immagine seguente:

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Screenshot che illustra come configurare la metrica per la somma delle transazioni BLOB":::

1. Selezionare quindi il pulsante **Aggiungi filtro** per creare un filtro sulla metrica per le richieste anonime.
1. Nella finestra di dialogo **filtro** specificare i valori seguenti:
    1. Impostare il valore della **Proprietà** su *Authentication*.
    1. Impostare il campo **operatore** sul segno di uguale (=).
    1. Impostare il campo **valori** su *Anonimo*.
1. Nell'angolo in alto a destra selezionare l'intervallo di tempo in cui si desidera visualizzare la metrica. È anche possibile indicare la granularità dell'aggregazione delle richieste, specificando gli intervalli da 1 minuto a 1 mese.

Dopo aver configurato la metrica, le richieste anonime inizieranno a essere visualizzate nel grafico. Nell'immagine seguente vengono mostrate le richieste anonime aggregate negli ultimi trenta minuti.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Screenshot che mostra le richieste anonime aggregate sull'archiviazione BLOB":::

È anche possibile configurare una regola di avviso per ricevere una notifica quando viene effettuato un determinato numero di richieste anonime nell'account di archiviazione. Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Analizzare i log per identificare i contenitori che ricevono richieste anonime

I log di archiviazione di Azure acquisiscono informazioni dettagliate sulle richieste effettuate nell'account di archiviazione, inclusa la modalità di autorizzazione di una richiesta. È possibile analizzare i log per determinare quali contenitori ricevono richieste anonime.

Per registrare le richieste nell'account di archiviazione di Azure per valutare le richieste anonime, è possibile usare la registrazione di archiviazione di Azure in monitoraggio di Azure (anteprima). Per altre informazioni, vedere [monitorare archiviazione di Azure](../common/monitor-storage.md).

Registrazione di archiviazione di Azure in monitoraggio di Azure supporta l'uso di query di log per analizzare i dati di log. Per eseguire query sui log, è possibile usare un'area di lavoro di Azure Log Analytics. Per altre informazioni sulle query di log, vedere [esercitazione: Introduzione alle query log Analytics](../../azure-monitor/log-query/get-started-portal.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Creare un'impostazione di diagnostica nel portale di Azure

Per registrare i dati di archiviazione di Azure con monitoraggio di Azure e analizzarli con Log Analytics di Azure, è prima necessario creare un'impostazione di diagnostica che indichi quali tipi di richieste e per quali servizi di archiviazione si desidera registrare i dati. Per creare un'impostazione di diagnostica nella portale di Azure, attenersi alla procedura seguente:

1. Registrare l' [Anteprima di registrazione di archiviazione di Azure in monitoraggio di Azure](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Creare una nuova area di lavoro Log Analytics nella sottoscrizione che contiene l'account di archiviazione di Azure. Dopo aver configurato la registrazione per l'account di archiviazione, i log saranno disponibili nell'area di lavoro Log Analytics. Per altre informazioni, vedere [Creare un'area di lavoro Log Analytics nel portale di Azure](../../azure-monitor/learn/quick-create-workspace.md).
1. Passare all'account di archiviazione nel portale di Azure.
1. Nella sezione monitoraggio selezionare impostazioni di **diagnostica (anteprima)**.
1. Selezionare **BLOB** per registrare le richieste effettuate nell'archivio BLOB.
1. Selezionare **Aggiungi impostazioni di diagnostica**.
1. Consente di specificare un nome per l'impostazione di diagnostica.
1. In **Dettagli categoria**, nella sezione **log** , scegliere i tipi di richieste da registrare. Tutte le richieste anonime saranno richieste di lettura, quindi selezionare **StorageRead** per acquisire richieste anonime.
1. In **Dettagli destinazione**selezionare **Invia a log Analytics**. Selezionare la sottoscrizione e l'area di lavoro Log Analytics creata in precedenza, come illustrato nella figura seguente.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="Screenshot che illustra come creare un'impostazione di diagnostica per la registrazione delle richieste":::

Dopo aver creato l'impostazione di diagnostica, le richieste all'account di archiviazione vengono registrate successivamente in base a tale impostazione. Per altre informazioni, vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche delle risorse in Azure](../../azure-monitor/platform/diagnostic-settings.md).

Per informazioni di riferimento sui campi disponibili nei log di archiviazione di Azure in monitoraggio di Azure, vedere [log delle risorse (anteprima)](../common/monitor-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-anonymous-requests"></a>Log di query per le richieste anonime

I log di archiviazione di Azure in monitoraggio di Azure includono il tipo di autorizzazione usata per effettuare una richiesta a un account di archiviazione. Nella query di log filtrare sulla proprietà **AuthenticationType** per visualizzare le richieste anonime.

Per recuperare i log degli ultimi 7 giorni per le richieste anonime nell'archivio BLOB, aprire l'area di lavoro Log Analytics. Incollare quindi la query seguente in una nuova query di log ed eseguirla. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

È anche possibile configurare una regola di avviso basata su questa query per inviare notifiche relative alle richieste anonime. Per altre informazioni, vedere [creare, visualizzare e gestire gli avvisi dei log con monitoraggio di Azure](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-anonymous-public-access"></a>Correggere l'accesso pubblico Anonimo

Dopo aver valutato le richieste anonime a contenitori e BLOB nell'account di archiviazione, è possibile intervenire per limitare o impedire l'accesso pubblico. Se è possibile che alcuni contenitori nell'account di archiviazione debbano essere disponibili per l'accesso pubblico, è possibile configurare l'impostazione di accesso pubblico per ogni contenitore nell'account di archiviazione. Questa opzione consente di controllare in modo più granulare l'accesso pubblico. Per altre informazioni, vedere [impostare il livello di accesso pubblico per un contenitore](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

Per una maggiore sicurezza, è possibile disabilitare l'accesso pubblico per un intero account di archiviazione. L'impostazione di accesso pubblico per un account di archiviazione sostituisce le singole impostazioni per i contenitori nell'account. Quando si disabilita l'accesso pubblico per un account di archiviazione, qualsiasi contenitore configurato per consentire l'accesso pubblico non è più accessibile in modo anonimo. Per altre informazioni, vedere [abilitare o disabilitare l'accesso in lettura pubblico per un account di archiviazione](anonymous-read-access-configure.md#enable-or-disable-public-read-access-for-a-storage-account).

Se lo scenario richiede che determinati contenitori siano disponibili per l'accesso pubblico, potrebbe essere consigliabile spostare tali contenitori e i relativi BLOB in account di archiviazione riservati per l'accesso pubblico. È quindi possibile disabilitare l'accesso pubblico per qualsiasi altro account di archiviazione.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Verificare che l'accesso pubblico a un BLOB non sia consentito

Per verificare che l'accesso pubblico a un blob specifico venga negato, è possibile provare a scaricare il BLOB tramite l'URL. Se il download ha esito positivo, il BLOB è ancora disponibile pubblicamente. Se il BLOB non è accessibile pubblicamente perché l'accesso pubblico è stato disabilitato per l'account di archiviazione, verrà visualizzato un messaggio di errore che indica che l'accesso pubblico non è consentito in questo account di archiviazione.

L'esempio seguente illustra come usare PowerShell per provare a scaricare un BLOB tramite il relativo URL. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Verificare che la modifica dell'impostazione di accesso pubblico del contenitore non sia consentita

Per verificare che non sia possibile modificare l'impostazione di accesso pubblico di un contenitore dopo che l'accesso pubblico è stato disabilitato per l'account di archiviazione, è possibile tentare di modificare l'impostazione. La modifica dell'impostazione di accesso pubblico del contenitore avrà esito negativo se l'accesso pubblico è disabilitato per l'account di archiviazione.

L'esempio seguente illustra come usare PowerShell per tentare di modificare l'impostazione di accesso pubblico di un contenitore. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Verificare che la creazione di un contenitore con accesso pubblico abilitato non sia consentita

Se l'accesso pubblico è disabilitato per l'account di archiviazione, non sarà possibile creare un nuovo contenitore con accesso pubblico abilitato. Per verificare, è possibile provare a creare un contenitore con accesso pubblico abilitato.

L'esempio seguente illustra come usare PowerShell per tentare di creare un contenitore con accesso pubblico abilitato. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:
 
```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'accesso in lettura pubblico anonimo per contenitori e BLOB](anonymous-read-access-configure.md)
- [Accesso anonimo a contenitori e BLOB pubblici con .NET](anonymous-read-access-client.md)