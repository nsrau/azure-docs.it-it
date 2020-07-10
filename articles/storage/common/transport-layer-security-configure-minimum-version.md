---
title: Configurare la versione minima richiesta di Transport Layer Security (TLS) per un account di archiviazione
titleSuffix: Azure Storage
description: Configurare un account di archiviazione per richiedere una versione minima di Transport Layer Security (TLS) per i client che effettuano richieste in archiviazione di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: ab83f0ee656dfc717284c1e26d10dcb814fe1c9e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209591"
---
# <a name="configure-minimum-required-version-of-transport-layer-security-tls-for-a-storage-account"></a>Configurare la versione minima richiesta di Transport Layer Security (TLS) per un account di archiviazione

La comunicazione tra un'applicazione client e un account di archiviazione di Azure viene crittografata con Transport Layer Security (TLS). TLS è un protocollo di crittografia standard che garantisce la privacy e l'integrità dei dati tra i client e i servizi su Internet. Per ulteriori informazioni su TLS, vedere [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Archiviazione di Azure supporta attualmente tre versioni del protocollo TLS: 1,0, 1,1 e 1,2. TLS 1,2 è la versione più sicura di TLS. Archiviazione di Azure usa TLS 1,2 su endpoint HTTPs pubblici, mentre TLS 1,0 e TLS 1,1 sono ancora supportati per la compatibilità con le versioni precedenti.

Per impostazione predefinita, gli account di archiviazione di Azure consentono ai client di inviare e ricevere dati con la versione meno recente di TLS, TLS 1,0 e versioni successive. Per applicare misure di sicurezza più restrittive, è possibile configurare l'account di archiviazione in modo da richiedere ai client di inviare e ricevere dati con una versione più recente di TLS. Se un account di archiviazione richiede una versione minima di TLS, le richieste effettuate con una versione precedente avranno esito negativo.

Questo articolo descrive come configurare un account di archiviazione per richiedere che i client inviino richieste con una versione minima di TLS. Per informazioni su come specificare una determinata versione di TLS quando si invia una richiesta da un'applicazione client, vedere [configurare Transport Layer Security (TLS) per un'applicazione client](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Rilevare la versione di TLS utilizzata dalle applicazioni client

Quando si applica una versione minima di TLS per l'account di archiviazione, si rischia di rifiutare le richieste provenienti dai client che inviano dati a una versione precedente di TLS. Per comprendere in che modo la configurazione della versione minima di TLS potrebbe influire sulle applicazioni client, Microsoft consiglia di abilitare la registrazione per l'account di archiviazione di Azure e di analizzare i log dopo un intervallo di tempo per determinare quali versioni di applicazioni client TLS stanno usando.

Per registrare le richieste nell'account di archiviazione di Azure e determinare la versione di TLS usata dal client, è possibile usare la registrazione di archiviazione di Azure in monitoraggio di Azure (anteprima). Per altre informazioni, vedere [monitorare archiviazione di Azure](monitor-storage.md).

Registrazione di archiviazione di Azure in monitoraggio di Azure supporta l'uso di query di log per analizzare i dati di log. Per eseguire query sui log, è possibile usare un'area di lavoro di Azure Log Analytics. Per altre informazioni sulle query di log, vedere [esercitazione: Introduzione alle query log Analytics](../../azure-monitor/log-query/get-started-portal.md).

Per registrare i dati di archiviazione di Azure con monitoraggio di Azure e analizzarli con Log Analytics di Azure, è prima necessario creare un'impostazione di diagnostica che indichi quali tipi di richieste e per quali servizi di archiviazione si desidera registrare i dati. Per creare un'impostazione di diagnostica nella portale di Azure, attenersi alla procedura seguente:

1. Registrare l' [Anteprima di registrazione di archiviazione di Azure in monitoraggio di Azure](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Creare una nuova area di lavoro Log Analytics nella sottoscrizione che contiene l'account di archiviazione di Azure. Dopo aver configurato la registrazione per l'account di archiviazione, i log saranno disponibili nell'area di lavoro Log Analytics. Per altre informazioni, vedere [Creare un'area di lavoro Log Analytics nel portale di Azure](../../azure-monitor/learn/quick-create-workspace.md).
1. Passare all'account di archiviazione nel portale di Azure.
1. Nella sezione monitoraggio selezionare impostazioni di **diagnostica (anteprima)**.
1. Selezionare il servizio di archiviazione di Azure per cui si vogliono registrare le richieste. Ad esempio, scegliere **BLOB** per registrare le richieste nell'archivio BLOB.
1. Selezionare **Aggiungi impostazioni di diagnostica**.
1. Consente di specificare un nome per l'impostazione di diagnostica.
1. In **Dettagli categoria**, nella sezione **log** , scegliere i tipi di richieste da registrare. È possibile registrare le richieste di lettura, scrittura ed eliminazione. La scelta di **StorageRead** e **StorageWrite** , ad esempio, registrerà le richieste di lettura e scrittura al servizio selezionato.
1. In **Dettagli destinazione**selezionare **Invia a log Analytics**. Selezionare la sottoscrizione e l'area di lavoro Log Analytics creata in precedenza, come illustrato nella figura seguente.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Screenshot che illustra come creare un'impostazione di diagnostica per la registrazione delle richieste":::

Dopo aver creato l'impostazione di diagnostica, le richieste all'account di archiviazione vengono registrate successivamente in base a tale impostazione. Per altre informazioni, vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche delle risorse in Azure](../../azure-monitor/platform/diagnostic-settings.md).

Per informazioni di riferimento sui campi disponibili nei log di archiviazione di Azure in monitoraggio di Azure, vedere [log delle risorse (anteprima)](monitor-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Eseguire query sulle richieste registrate in base alla versione di TLS

I log di archiviazione di Azure in monitoraggio di Azure includono la versione TLS usata per inviare una richiesta a un account di archiviazione. Usare la proprietà **TlsVersion** per controllare la versione TLS di una richiesta registrata.

Per recuperare i log degli ultimi 7 giorni e determinare il numero di richieste effettuate nell'archivio BLOB con ogni versione di TLS, aprire l'area di lavoro Log Analytics. Incollare quindi la query seguente in una nuova query di log ed eseguirla. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

I risultati mostrano il conteggio del numero di richieste effettuate con ogni versione di TLS:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Screenshot che mostra i risultati della query di log Analytics per restituire la versione TLS":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Eseguire query sulle richieste registrate per indirizzo IP chiamante e intestazione agente utente

I log di archiviazione di Azure in monitoraggio di Azure includono anche l'indirizzo IP del chiamante e l'intestazione dell'agente utente che consentono di valutare quali applicazioni client hanno eseguito l'accesso all'account di archiviazione. È possibile analizzare questi valori per decidere se le applicazioni client devono essere aggiornate per usare una versione più recente di TLS o se è accettabile interrompere la richiesta di un client se non viene inviata con la versione minima di TLS.

Per recuperare i log degli ultimi 7 giorni e determinare quali client hanno effettuato richieste con una versione di TLS prima di TLS 1,2, incollare la query seguente in una nuova query di log ed eseguirla. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="configure-the-minimum-tls-version-for-an-account"></a>Configurare la versione minima di TLS per un account

Per configurare la versione minima di TLS per un account di archiviazione, usare l'portale di Azure o l'interfaccia della riga di comando di Azure per impostare la versione di **minimumTlsVersion** per l'account. Questa proprietà è disponibile per tutti gli account di archiviazione creati con il modello di distribuzione Azure Resource Manager. Per altre informazioni, vedere [Panoramica dell'account di archiviazione](storage-account-overview.md).

# <a name="portal"></a>[Portale](#tab/portal)

Per configurare la versione minima di TLS per un account di archiviazione con la portale di Azure, seguire questa procedura:

1. Passare all'account di archiviazione nel portale di Azure.
1. Selezionare l'impostazione di **configurazione** .
1. In **versione minima di TLS**usare l'elenco a discesa per selezionare la versione minima di TLS necessaria per accedere ai dati nell'account di archiviazione, come illustrato nella figura seguente.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Screenshot che illustra come configurare la versione minima di TLS nel portale di Azure":::

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per configurare la versione minima di TLS per un account di archiviazione con l'interfaccia della riga di comando di Azure, ottenere prima di tutto l'ID risorsa per l'account di archiviazione chiamando il comando [AZ Resource Show](/cli/azure/resource#az-resource-show) . Chiamare quindi il comando [AZ Resource Update](/cli/azure/resource#az-resource-update) per impostare la proprietà **minimumTlsVersion** per l'account di archiviazione. I valori validi per **minimumTlsVersion** sono `TLS1_0` , `TLS1_1` e `TLS1_2` .

Nell'esempio seguente viene impostata la versione minima di TLS su 1,2. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
storage_account_id=$(az resource show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --resource-type Microsoft.Storage/storageAccounts \
  --query id \
  --output tsv)

az resource update \
  --ids $storage_account_id \
  --set properties.minimumTlsVersion="TLS1_2"
```

---

> [!NOTE]
> Dopo aver aggiornato la versione minima di TLS per l'account di archiviazione, potrebbe essere necessario attendere fino a 30 secondi prima che la modifica venga propagata completamente.

## <a name="check-the-minimum-required-tls-version-for-an-account"></a>Verificare la versione minima richiesta di TLS per un account

Per determinare la versione minima richiesta di TLS configurata per un account di archiviazione, controllare la proprietà Azure Resource Manager **minimumTlsVersion** . Per controllare in una sola volta questa proprietà per un numero elevato di account di archiviazione, usare Azure Resource Graph Explorer.

Per impostazione predefinita, la proprietà **minimumTlsVersion** non viene impostata e non restituisce alcun valore finché non viene impostata in modo esplicito. Per impostazione predefinita, l'account di archiviazione consente le richieste inviate con TLS versione 1,0 o successive se il valore della proprietà è null.

### <a name="check-the-minimum-required-tls-version-for-a-single-storage-account"></a>Verificare la versione minima richiesta di TLS per un singolo account di archiviazione

Per verificare la versione minima richiesta di TLS per un singolo account di archiviazione usando l'interfaccia della riga di comando di Azure, chiamare il comando [AZ Resource Show](/cli/azure/resource#az-resource-show) ed eseguire una query per la proprietà **minimumTlsVersion** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv
```

### <a name="check-the-minimum-required-tls-version-for-a-set-of-storage-accounts"></a>Verificare la versione minima richiesta di TLS per un set di account di archiviazione

Per controllare la versione minima richiesta di TLS in un set di account di archiviazione con prestazioni ottimali, è possibile usare Azure Resource Graph Explorer nella portale di Azure. Per altre informazioni sull'uso di Esplora grafico risorse, vedere [Guida introduttiva: eseguire la prima query di Resource Graph con Esplora risorse di Azure](/azure/governance/resource-graph/first-query-portal).

Eseguendo la query seguente in Resource Graph Explorer viene restituito un elenco di account di archiviazione e viene visualizzata la versione minima di TLS per ogni account:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

---

## <a name="test-the-minimum-tls-version-from-a-client"></a>Testare la versione minima di TLS da un client

Per verificare che la versione minima richiesta di TLS per un account di archiviazione vieti le chiamate effettuate con una versione precedente, è possibile configurare un client per l'uso di una versione precedente di TLS. Per ulteriori informazioni sulla configurazione di un client per l'utilizzo di una versione specifica di TLS, vedere [configurare Transport Layer Security (TLS) per un'applicazione client](transport-layer-security-configure-client-version.md).

Quando un client accede a un account di archiviazione usando una versione di TLS che non soddisfa la versione minima di TLS configurata per l'account, archiviazione di Azure restituisce il codice di errore 400 (richiesta non valida) e un messaggio che indica che la versione di TLS usata non è consentita per l'esecuzione di richieste in questo account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare Transport Layer Security (TLS) per un'applicazione client](transport-layer-security-configure-client-version.md)
- [Raccomandazioni sulla sicurezza per archiviazione BLOB](../blobs/security-recommendations.md)
