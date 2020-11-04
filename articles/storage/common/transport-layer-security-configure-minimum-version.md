---
title: Applicare una versione minima richiesta di Transport Layer Security (TLS) per le richieste in ingresso
titleSuffix: Azure Storage
description: Configurare un account di archiviazione per richiedere una versione minima di Transport Layer Security (TLS) per i client che effettuano richieste in archiviazione di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 5f772bd996b126a4cd7182a2ce088c2d3edc8e7d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312016"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>Applicare una versione minima richiesta di Transport Layer Security (TLS) per le richieste a un account di archiviazione

La comunicazione tra un'applicazione client e un account di archiviazione di Azure viene crittografata con Transport Layer Security (TLS). TLS è un protocollo di crittografia standard che garantisce la privacy e l'integrità dei dati tra i client e i servizi su Internet. Per ulteriori informazioni su TLS, vedere [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Archiviazione di Azure supporta attualmente tre versioni del protocollo TLS: 1,0, 1,1 e 1,2. Archiviazione di Azure usa TLS 1,2 su endpoint HTTPS pubblici, mentre TLS 1,0 e TLS 1,1 sono ancora supportati per la compatibilità con le versioni precedenti.

Per impostazione predefinita, gli account di archiviazione di Azure consentono ai client di inviare e ricevere dati con la versione meno recente di TLS, TLS 1,0 e versioni successive. Per applicare misure di sicurezza più restrittive, è possibile configurare l'account di archiviazione in modo da richiedere ai client di inviare e ricevere dati con una versione più recente di TLS. Se un account di archiviazione richiede una versione minima di TLS, le richieste effettuate con una versione precedente avranno esito negativo.

Questo articolo descrive come usare un Framework di trascinamento (rilevamento-monitoraggio e aggiornamento-controllo) per gestire in modo continuo TLS sicuro per gli account di archiviazione.

Per informazioni su come specificare una determinata versione di TLS quando si invia una richiesta da un'applicazione client, vedere [configurare Transport Layer Security (TLS) per un'applicazione client](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Rilevare la versione di TLS utilizzata dalle applicazioni client

Quando si applica una versione minima di TLS per l'account di archiviazione, si rischia di rifiutare le richieste provenienti dai client che inviano dati a una versione precedente di TLS. Per comprendere in che modo la configurazione della versione minima di TLS potrebbe influire sulle applicazioni client, Microsoft consiglia di abilitare la registrazione per l'account di archiviazione di Azure e di analizzare i log dopo un intervallo di tempo per individuare le versioni di applicazioni client TLS utilizzate.

Per registrare le richieste nell'account di archiviazione di Azure e determinare la versione di TLS usata dal client, è possibile usare la registrazione di archiviazione di Azure in monitoraggio di Azure (anteprima). Per altre informazioni, vedere [monitorare archiviazione di Azure](../blobs/monitor-blob-storage.md).

Registrazione di archiviazione di Azure in monitoraggio di Azure supporta l'uso di query di log per analizzare i dati di log. Per eseguire query sui log, è possibile usare un'area di lavoro di Azure Log Analytics. Per altre informazioni sulle query di log, vedere [esercitazione: Introduzione alle query log Analytics](../../azure-monitor/log-query/get-started-portal.md).

Per registrare i dati di archiviazione di Azure con monitoraggio di Azure e analizzarli con Log Analytics di Azure, è prima necessario creare un'impostazione di diagnostica che indichi quali tipi di richieste e per quali servizi di archiviazione si desidera registrare i dati. Per creare un'impostazione di diagnostica nella portale di Azure, attenersi alla procedura seguente:

1. Registrare l' [Anteprima di registrazione di archiviazione di Azure in monitoraggio di Azure](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Creare una nuova area di lavoro Log Analytics nella sottoscrizione che contiene l'account di archiviazione di Azure. Dopo aver configurato la registrazione per l'account di archiviazione, i log saranno disponibili nell'area di lavoro Log Analytics. Per altre informazioni, vedere [Creare un'area di lavoro Log Analytics nel portale di Azure](../../azure-monitor/learn/quick-create-workspace.md).
1. Passare all'account di archiviazione nel portale di Azure.
1. Nella sezione monitoraggio selezionare impostazioni di **diagnostica (anteprima)**.
1. Selezionare il servizio di archiviazione di Azure per cui si vogliono registrare le richieste. Ad esempio, scegliere **BLOB** per registrare le richieste nell'archivio BLOB.
1. Selezionare **Aggiungi impostazioni di diagnostica**.
1. Consente di specificare un nome per l'impostazione di diagnostica.
1. In **Dettagli categoria** , nella sezione **log** , scegliere i tipi di richieste da registrare. È possibile registrare le richieste di lettura, scrittura ed eliminazione. La scelta di **StorageRead** e **StorageWrite** , ad esempio, registrerà le richieste di lettura e scrittura al servizio selezionato.
1. In **Dettagli destinazione** selezionare **Invia a log Analytics**. Selezionare la sottoscrizione e l'area di lavoro Log Analytics creata in precedenza, come illustrato nella figura seguente.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Screenshot che illustra come creare un'impostazione di diagnostica per la registrazione delle richieste":::

Dopo aver creato l'impostazione di diagnostica, le richieste all'account di archiviazione vengono registrate successivamente in base a tale impostazione. Per altre informazioni, vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche delle risorse in Azure](../../azure-monitor/platform/diagnostic-settings.md).

Per informazioni di riferimento sui campi disponibili nei log di archiviazione di Azure in monitoraggio di Azure, vedere [log delle risorse (anteprima)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Eseguire query sulle richieste registrate in base alla versione di TLS

I log di archiviazione di Azure in monitoraggio di Azure includono la versione TLS usata per inviare una richiesta a un account di archiviazione. Usare la proprietà **TlsVersion** per controllare la versione TLS di una richiesta registrata.

Per determinare il numero di richieste effettuate nell'archivio BLOB con diverse versioni di TLS negli ultimi sette giorni, aprire l'area di lavoro Log Analytics. Incollare quindi la query seguente in una nuova query di log ed eseguirla. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

I risultati mostrano il conteggio del numero di richieste effettuate con ogni versione di TLS:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Screenshot che mostra i risultati della query di log Analytics per restituire la versione TLS":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Eseguire query sulle richieste registrate per indirizzo IP chiamante e intestazione agente utente

I log di archiviazione di Azure in monitoraggio di Azure includono anche l'indirizzo IP del chiamante e l'intestazione dell'agente utente che consentono di valutare quali applicazioni client hanno eseguito l'accesso all'account di archiviazione. È possibile analizzare questi valori per decidere se le applicazioni client devono essere aggiornate per usare una versione più recente di TLS o se è accettabile interrompere la richiesta di un client se non viene inviata con la versione minima di TLS.

Per determinare quali client hanno effettuato richieste con una versione di TLS precedente a TLS 1,2 negli ultimi sette giorni, incollare la query seguente in una nuova query di log ed eseguirla. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>Correggere i rischi per la sicurezza con una versione minima di TLS

Quando si è certi che il traffico proveniente dai client che usano versioni precedenti di TLS è minimo o che è accettabile non rispondere alle richieste effettuate con una versione precedente di TLS, è possibile iniziare a applicare una versione minima di TLS nell'account di archiviazione. Richiedere che i client usino una versione minima di TLS per eseguire richieste a un account di archiviazione fanno parte di una strategia per ridurre al minimo i rischi di sicurezza per i dati.

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>Configurare la versione minima di TLS per un account di archiviazione

Per configurare la versione minima di TLS per un account di archiviazione, impostare la versione di **MinimumTlsVersion** per l'account. Questa proprietà è disponibile per tutti gli account di archiviazione creati con il modello di distribuzione Azure Resource Manager. Per altre informazioni sul modello di distribuzione Azure Resource Manager, vedere [Panoramica dell'account di archiviazione](storage-account-overview.md).

Per impostazione predefinita, la proprietà **MinimumTlsVersion** non viene impostata e non restituisce alcun valore finché non viene impostata in modo esplicito.  Se il valore della proprietà è **null** , l'account di archiviazione consentirà le richieste inviate con TLS versione 1,0 o successiva.

# <a name="portal"></a>[Portale](#tab/portal)

Quando si crea un account di archiviazione con la portale di Azure, per impostazione predefinita la versione minima di TLS è impostata su 1,2.

Per configurare la versione minima di TLS per un account di archiviazione esistente con la portale di Azure, attenersi alla seguente procedura:

1. Passare all'account di archiviazione nel portale di Azure.
1. Selezionare l'impostazione di **configurazione** .
1. In **versione minima di TLS** usare l'elenco a discesa per selezionare la versione minima di TLS necessaria per accedere ai dati nell'account di archiviazione, come illustrato nella figura seguente.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Screenshot che illustra come configurare la versione minima di TLS nel portale di Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per configurare la versione minima di TLS per un account di archiviazione con PowerShell, installare [Azure PowerShell versione 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) o successiva. Successivamente, configurare la proprietà **MinimumTLSVersion** per un account di archiviazione nuovo o esistente. I valori validi per **MinimumTlsVersion** sono `TLS1_0` , `TLS1_1` e `TLS1_2` .

L'esempio seguente crea un account di archiviazione e imposta il valore di **MinimumTLSVersion** su TLS 1,1, quindi aggiorna l'account e imposta **MinimumTLSVersion** su TLS 1,2. Nell'esempio viene recuperato anche il valore della proprietà in ogni caso. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per configurare la versione minima di TLS per un account di archiviazione con l'interfaccia della riga di comando di Azure, installare l'interfaccia della riga di comando di Azure 2.9.0 Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Successivamente, configurare la proprietà **minimumTlsVersion** per un account di archiviazione nuovo o esistente. I valori validi per **minimumTlsVersion** sono `TLS1_0` , `TLS1_1` e `TLS1_2` .

L'esempio seguente crea un account di archiviazione e imposta il valore di **minimumTLSVersion** su TLS 1,1. Aggiorna quindi l'account e imposta la proprietà **minimumTLSVersion** su TLS 1,2. Nell'esempio viene recuperato anche il valore della proprietà in ogni caso. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[Modello](#tab/template)

Per configurare la versione minima di TLS per un account di archiviazione con un modello, creare un modello con la proprietà **MinimumTLSVersion** impostata su `TLS1_0` , `TLS1_1` o `TLS1_2` . Nei passaggi seguenti viene descritto come creare un modello nel portale di Azure.

1. Nella portale di Azure scegliere **Crea una risorsa**.
1. In **Cerca nel Marketplace** Digitare **distribuzione modello** , quindi premere **invio**.
1. Scegliere **distribuzione modelli (Distribuisci usando i modelli personalizzati) (anteprima)** , scegliere Crea, quindi **creare** **un modello personalizzato nell'editor**.
1. Nell'editor dei modelli incollare il codice JSON seguente per creare un nuovo account e impostare la versione minima di TLS su TLS 1,2. Ricordarsi di sostituire i segnaposto tra parentesi angolari con valori personalizzati.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Salvare il modello.
1. Specificare il parametro del gruppo di risorse, quindi scegliere il pulsante **Verifica + crea** per distribuire il modello e creare un account di archiviazione con la proprietà **MinimumTLSVersion** configurata.

---

> [!NOTE]
> Dopo aver aggiornato la versione minima di TLS per l'account di archiviazione, potrebbe essere necessario attendere fino a 30 secondi prima che la modifica venga propagata completamente.

Per configurare la versione minima di TLS è necessaria la versione 2019-04-01 o successiva del provider di risorse di archiviazione di Azure. Per altre informazioni, vedere [API REST del provider di risorse di archiviazione di Azure](/rest/api/storagerp/).

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>Verificare la versione minima richiesta di TLS per più account

Per controllare la versione minima richiesta di TLS in un set di account di archiviazione con prestazioni ottimali, è possibile usare Azure Resource Graph Explorer nella portale di Azure. Per altre informazioni sull'uso di Esplora grafico risorse, vedere [Guida introduttiva: eseguire la prima query di Resource Graph con Esplora risorse di Azure](../../governance/resource-graph/first-query-portal.md).

Eseguendo la query seguente in Resource Graph Explorer viene restituito un elenco di account di archiviazione e viene visualizzata la versione minima di TLS per ogni account:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>Testare la versione minima di TLS da un client

Per verificare che la versione minima richiesta di TLS per un account di archiviazione vieti le chiamate effettuate con una versione precedente, è possibile configurare un client per l'uso di una versione precedente di TLS. Per ulteriori informazioni sulla configurazione di un client per l'utilizzo di una versione specifica di TLS, vedere [configurare Transport Layer Security (TLS) per un'applicazione client](transport-layer-security-configure-client-version.md).

Quando un client accede a un account di archiviazione usando una versione di TLS che non soddisfa la versione minima di TLS configurata per l'account, archiviazione di Azure restituisce il codice di errore 400 (richiesta non valida) e un messaggio che indica che la versione di TLS usata non è consentita per l'esecuzione di richieste in questo account di archiviazione.

## <a name="use-azure-policy-to-audit-for-compliance"></a>Usare i criteri di Azure per controllare la conformità

Se si dispone di un numero elevato di account di archiviazione, è consigliabile eseguire un controllo per assicurarsi che tutti gli account siano configurati per la versione minima di TLS richiesta dall'organizzazione. Per controllare un set di account di archiviazione per la conformità, usare criteri di Azure. Criteri di Azure è un servizio che è possibile usare per creare, assegnare e gestire criteri che applicano regole alle risorse di Azure. Criteri di Azure consente di garantire che le risorse siano conformi agli standard aziendali e ai contratti di servizio. Per altre informazioni, vedere [Panoramica di Criteri di Azure](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Creare un criterio con un effetto di controllo

Criteri di Azure supporta gli effetti che determinano cosa accade quando una regola dei criteri viene valutata rispetto a una risorsa. L'effetto di controllo Crea un avviso quando una risorsa non è conforme, ma non interrompe la richiesta. Per altre informazioni sugli effetti, vedere [comprendere gli effetti dei criteri di Azure](../../governance/policy/concepts/effects.md).

Per creare un criterio con un effetto di controllo per la versione minima di TLS con il portale di Azure, attenersi alla procedura seguente:

1. Nel portale di Azure passare al servizio criteri di Azure.
1. Nella sezione **creazione e modifica** selezionare **definizioni**.
1. Selezionare **Aggiungi definizione criteri** per creare una nuova definizione dei criteri.
1. Per il campo **percorso definizione** selezionare il pulsante **altro** per specificare dove si trova la risorsa dei criteri di controllo.
1. Specificare un nome per il criterio. Facoltativamente, è possibile specificare una descrizione e una categoria.
1. In **regola dei criteri** aggiungere la definizione di criteri seguente alla sezione **policyRule** .

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
              "equals": "TLS1_2"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. Salvare il criterio.

### <a name="assign-the-policy"></a>Assegnare i criteri

Assegnare quindi il criterio a una risorsa. L'ambito dei criteri corrisponde alla risorsa e alle risorse sottostanti. Per altre informazioni sull'assegnazione dei criteri, vedere [struttura di assegnazione dei criteri di Azure](../../governance/policy/concepts/assignment-structure.md).

Per assegnare i criteri alla portale di Azure, attenersi alla procedura seguente:

1. Nel portale di Azure passare al servizio criteri di Azure.
1. Nella sezione **creazione e modifica** selezionare **assegnazioni**.
1. Selezionare **assegna criterio** per creare una nuova assegnazione di criteri.
1. Per il campo **ambito** selezionare l'ambito dell'assegnazione di criteri.
1. Per il campo **definizione criteri** selezionare il pulsante **altro** , quindi selezionare i criteri definiti nella sezione precedente dall'elenco.
1. Consente di specificare un nome per l'assegnazione dei criteri. La descrizione è facoltativa.
1. Lasciare impostato su *abilitato* per l' **imposizione dei criteri** . Questa impostazione non ha alcun effetto sui criteri di controllo.
1. Selezionare **Verifica + crea** per creare l'assegnazione.

### <a name="view-compliance-report"></a>Visualizza report conformità

Dopo aver assegnato il criterio, è possibile visualizzare il report di conformità. Il report di conformità per i criteri di controllo fornisce informazioni sugli account di archiviazione che non sono conformi ai criteri. Per ulteriori informazioni, vedere [ottenere i dati di conformità dei criteri](../../governance/policy/how-to/get-compliance-data.md).

Potrebbero essere necessari alcuni minuti per rendere disponibile il report di conformità dopo la creazione dell'assegnazione dei criteri.

Per visualizzare il report di conformità nel portale di Azure, attenersi alla seguente procedura:

1. Nel portale di Azure passare al servizio criteri di Azure.
1. Selezionare **conformità**.
1. Filtrare i risultati per il nome dell'assegnazione di criteri creata nel passaggio precedente. Il report Mostra il numero di risorse non conformi ai criteri.
1. È possibile eseguire il drill-down del report per ulteriori dettagli, incluso un elenco di account di archiviazione non conformi.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="Screenshot che mostra il report di conformità per i criteri di controllo per la versione minima di TLS":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>Usare i criteri di Azure per applicare la versione minima di TLS

Criteri di Azure supporta la governance del cloud garantendo che le risorse di Azure siano conformi ai requisiti e agli standard. Per applicare un requisito di versione minima di TLS per gli account di archiviazione nell'organizzazione, è possibile creare un criterio che impedisce la creazione di un nuovo account di archiviazione che imposta il requisito TLS minimo per una versione precedente di TLS rispetto a quella dettata dai criteri. Questo criterio impedisce inoltre tutte le modifiche alla configurazione di un account esistente se l'impostazione minima della versione di TLS per l'account non è conforme ai criteri.

Il criterio di imposizione USA l'effetto Deny per impedire a una richiesta di creare o modificare un account di archiviazione in modo che la versione minima di TLS non rispetti più gli standard dell'organizzazione. Per altre informazioni sugli effetti, vedere [comprendere gli effetti dei criteri di Azure](../../governance/policy/concepts/effects.md).

Per creare un criterio con un effetto negazione per una versione minima di TLS inferiore a TLS 1,2, seguire la stessa procedura descritta in [usare i criteri di Azure per controllare la conformità](#use-azure-policy-to-audit-for-compliance), ma fornire il codice JSON seguente nella sezione **policyRule** della definizione dei criteri:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
          "equals": "TLS1_2"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Dopo aver creato il criterio con l'effetto nega e averlo assegnato a un ambito, un utente non può creare un account di archiviazione con una versione minima di TLS precedente alla 1,2. Né può apportare modifiche di configurazione a un account di archiviazione esistente che attualmente richiede una versione minima di TLS precedente alla 1,2. Il tentativo di eseguire questa operazione genera un errore. La versione minima richiesta di TLS per l'account di archiviazione deve essere impostata su 1,2 per procedere con la creazione o la configurazione dell'account.

La figura seguente mostra l'errore che si verifica se si prova a creare un account di archiviazione con la versione minima di TLS impostata su TLS 1,0 (impostazione predefinita per un nuovo account) quando un criterio con un effetto di negazione richiede che la versione minima di TLS sia impostata su TLS 1,2.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="Screenshot che mostra l'errore che si verifica quando si crea un account di archiviazione in violazione dei criteri":::

## <a name="network-considerations"></a>Considerazioni per la rete

Quando un client invia una richiesta all'account di archiviazione, prima di elaborare le richieste il client stabilisce prima di tutto una connessione con l'endpoint pubblico dell'account di archiviazione. L'impostazione minima della versione di TLS viene controllata dopo che è stata stabilita la connessione. Se la richiesta usa una versione precedente di TLS rispetto a quella specificata dall'impostazione, la connessione continuerà ad avere esito positivo, ma la richiesta avrà esito negativo. Per altre informazioni sugli endpoint pubblici per archiviazione di Azure, vedere [sintassi dell'URI della risorsa](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare Transport Layer Security (TLS) per un'applicazione client](transport-layer-security-configure-client-version.md)
- [Raccomandazioni sulla sicurezza per archiviazione BLOB](../blobs/security-recommendations.md)
