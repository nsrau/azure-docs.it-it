---
title: Chiave gestita dal cliente di Monitoraggio di Azure
description: Informazioni e procedure per configurare la chiave gestita dal cliente (CMK) per crittografare i dati nelle aree di lavoro Log Analytics usando una chiave di Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 07/05/2020
ms.openlocfilehash: aab0de11972f7d1abaaa0140da002f838e319fdf
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86134618"
---
# <a name="azure-monitor-customer-managed-key"></a>Chiave gestita dal cliente di Monitoraggio di Azure 

Questo articolo fornisce informazioni generali e procedure per la configurazione delle chiavi gestite dal cliente (CMK) per le aree di lavoro Log Analytics. Dopo aver eseguito la configurazione, tutti i dati inviati alle aree di lavoro vengono crittografati con la chiave di Azure Key Vault.

È consigliabile esaminare [limiti e vincoli](#limitationsandconstraints) di seguito prima di procedere alla configurazione.

## <a name="customer-managed-key-cmk-overview"></a>Panoramica della chiave gestita dal cliente (CMK)

[La crittografia di dati inattivi](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) è un requisito comune per la privacy e la sicurezza nelle organizzazioni. È possibile lasciare che Azure gestisca completamente la crittografia di dati inattivi, mentre sono disponibili diverse opzioni per gestire con precisione la crittografia o le chiavi di crittografia.

Monitoraggio di Azure garantisce che tutti i dati siano crittografati quando sono inattivi usando chiavi gestite da Azure. Monitoraggio di Azure offre inoltre un'opzione per la crittografia dei dati tramite la propria chiave archiviata in  [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) e a cui è possibile accedere tramite l'archivio usando l' [identità gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)  assegnata dal sistema. Questa chiave può essere [software o protetta dal modulo di protezione hardware](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

L'uso della crittografia da parte di Monitoraggio di Azure è identico a quello della [crittografia di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) .

La chiave gestita dal cliente consente di controllare l'accesso ai dati e di revocarlo in qualsiasi momento. L'archiviazione di Monitoraggio di Azure rispetta sempre le modifiche apportate alle autorizzazioni delle chiavi entro un'ora. I dati inseriti negli ultimi 14 giorni vengono anche mantenuti nella cache ad accesso frequente (con supporto SSD) per un efficace funzionamento del motore di query. Questi dati rimangono crittografati con le chiavi Microsoft indipendentemente dalla configurazione della chiave gestita dal cliente, ma il controllo sui dati SSD è conforme alla [revoca delle chiavi](#cmk-kek-revocation). Microsoft sta lavorando per offrire la crittografia dei dati SSD con chiave gestita dal cliente nella seconda metà del 2020.

La funzionalità della chiave gestita dal cliente viene fornita su cluster Log Analytics dedicati. Per verificare di disporre della capacità necessaria nella propria area, è necessario che la sottoscrizione sia consentita in anticipo. Usare il contatto Microsoft per ottenere la sottoscrizione consentita prima di iniziare a configurare CMK.

Il [modello di determinazione prezzi dei cluster Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters) usa le prenotazioni di capacità a partire da un livello di 1000 GB/giorno.

## <a name="how-cmk-works-in-azure-monitor"></a>Funzionamento della chiave gestita dal cliente in Monitoraggio di Azure

Monitoraggio di Azure sfrutta l'identità gestita assegnata dal sistema per concedere l'accesso ad Azure Key Vault. L'identità gestita assegnata dal sistema può essere associata solo a una singola risorsa di Azure, mentre l'identità del cluster Log Analytics è supportata a livello di cluster. Ciò impone che la funzionalità di chiave gestita dal cliente sia distribuita in un cluster Log Analytics dedicato. Per supportare la chiave gestita dal cliente in più aree di lavoro, una nuova risorsa *Cluster* Log Analytics funge da connessione di identità intermedia tra Key Vault e le aree di lavoro Log Analytics. Lo spazio di archiviazione del cluster Log Analytics usa l'identità gestita \' associata alla risorsa *Cluster* per l'autenticazione ad Azure Key Vault tramite Azure Active Directory. 

Dopo la configurazione della chiave gestita dal cliente, tutti i dati inseriti nelle aree di lavoro associate alla risorsa *Cluster* vengono crittografati con la chiave in Key Vault. È possibile dissociare le aree di lavoro dalla risorsa *Cluster* in qualsiasi momento. I nuovi dati vengono inseriti nell'archivio di Log Analytics e crittografati con la chiave Microsoft, mentre è possibile eseguire facilmente query sui dati nuovi e meno recenti.


![Panoramica della chiave gestita dal cliente (CMK)](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. La risorsa *Cluster* di Log Analytics con identità gestita con le autorizzazioni per Key Vault: l'identità viene propagata all'archivio cluster sottostante dedicato Log Analytics
3. Cluster Log Analytics dedicato
4. Aree di lavoro associate alla risorsa *Cluster* per la crittografia della chiave gestita dal cliente

## <a name="encryption-keys-operation"></a>Funzionamento delle chiavi di crittografia

La crittografia dei dati di archiviazione include tre tipi di chiavi:

- **KEK** - chiave di crittografia della chiave (CMK)
- **AEK** - chiave di crittografia dell'account
- **DEK** - chiave di crittografia dei dati

Sono applicabili le regole seguenti:

- Gli account di archiviazione del cluster Log Analytics generano una chiave di crittografia univoca per ogni account di archiviazione, nota come AEK.

- La chiave AEK viene usata per ottenere chiavi DEK, ovvero le chiavi usate per crittografare ogni blocco di dati scritti su disco.

- Quando si configura la chiave in Key Vault e vi si fa riferimento nella risorsa *Cluster*, il servizio Archiviazione di Azure invia le richieste ad Azure Key Vault per eseguire il wrapping e annullare il wrapping della chiave AEK per eseguire operazioni di crittografia e decrittografia dei dati.

- La chiave KEK non esce mai da Key Vault e, nel caso di una chiave HSM, non esce mai dall'hardware.

- Archiviazione di Azure usa l'identità gestita associata alla risorsa *Cluster* per l'autenticazione e l'accesso ad Azure Key Vault tramite Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Procedura di provisioning della chiave gestita dal cliente

1. Concessione della sottoscrizione: la funzionalità CMK viene distribuita su cluster Log Analytics dedicati. Per verificare di disporre della capacità necessaria nella propria area, è necessario che la sottoscrizione sia consentita in anticipo. Usare il contatto Microsoft per ottenere la sottoscrizione.
2. Creazione di Azure Key Vault e archiviazione della chiave
3. Creazione di una risorsa *Cluster*
4. Concessione delle autorizzazioni a Key Vault
5. Associazione delle aree di lavoro Log Analytics

La procedura non è supportata in portale di Azure e il provisioning viene eseguito tramite PowerShell o le richieste REST.

> [!IMPORTANT]
> Qualsiasi richiesta REST deve includere un token di autorizzazione di porta nell'intestazione della richiesta.

Ad esempio:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Dove *eyJ0eXAiO...* rappresenta il token di autorizzazione completo. 

È possibile acquisire il token usando uno di questi metodi:

1. Usare il metodo [Registrazioni app](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens).
2. Nel portale di Azure
    1. Passare al portale di Azure mentre ci si trova nello "strumento di sviluppo" (F12)
    1. Cercare la stringa di autorizzazione in "Request Headers" in una delle istanze "batch?api-version". L'aspetto sarà simile al seguente: "authorization: Bearer eyJ0eXAiO....". 
    1. Copiarla e aggiungerla alla chiamata API secondo gli esempi riportati di seguito.
3. Passare al sito della documentazione REST di Azure. Premere "Try it" (Prova) su qualsiasi API e copiare il bearer token.

### <a name="asynchronous-operations-and-status-check"></a>Operazioni asincrone e controllo dello stato

Alcune operazioni in questa procedura di configurazione vengono eseguite in modo asincrono perché non possono essere completate rapidamente. Quando si usano richieste REST nella configurazione, la risposta restituisce inizialmente un codice di stato HTTP 200 (OK) e un'intestazione con la proprietà *Azure-AsyncOperation* quando accettata:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

È quindi possibile controllare lo stato dell'operazione asincrona inviando una richiesta GET al valore dell'intestazione *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

La risposta contiene informazioni sull'operazione e il relativo *stato*. I possibili valori sono i seguenti:

L'operazione è in corso
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

L'operazione di aggiornamento dell'identificatore di chiave è in corso
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

È in corso l'eliminazione della risorsa *Cluster*: quando si elimina una risorsa *Cluster* con aree di lavoro associate, viene eseguita la dissociazione per ogni area di lavoro in operazioni asincrone che possono richiedere tempo.
Questo non accade quando si elimina una risorsa *Cluster* senza aree di lavoro associate: in questo caso la risorsa *Cluster* viene eliminata immediatamente.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Operazione completata
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Operazione non riuscita
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="allowing-subscription-for-cmk-deployment"></a>Concessione della sottoscrizione per la distribuzione di CMK

La funzionalità della chiave gestita dal cliente viene fornita su cluster Log Analytics dedicati.Per verificare di disporre della capacità necessaria nella propria area, è necessario che la sottoscrizione sia consentita in anticipo. Usare i contatti in Microsoft per fornire gli ID delle sottoscrizioni.

> [!IMPORTANT]
> La funzionalità della chiave gestita dal cliente è a livello di area. L'istanza di Azure Key Vault, la risorsa *Cluster* e le aree di lavoro Log Analytics associate devono trovarsi nella stessa area, ma possono essere in sottoscrizioni diverse.

### <a name="storing-encryption-key-kek"></a>Archiviazione della chiave di crittografia (KEK)

Creare o usare un'istanza di Azure Key Vault già disponibile per generare o importare una chiave da usare per la crittografia dei dati. L'istanza di Azure Key Vault deve essere configurata come recuperabile per proteggere la chiave e l'accesso ai dati in Monitoraggio di Azure. È possibile verificare questa configurazione nelle proprietà dell'istanza di Key Vault. Devono essere abilitate sia *Eliminazione temporanea* che *Protezione dall'eliminazione*.

![Impostazioni Eliminazione temporanea e Protezione dall'eliminazione](media/customer-managed-keys/soft-purge-protection.png)

Queste impostazioni sono disponibili tramite l'interfaccia della riga di comando e PowerShell:
- [eliminazione temporanea](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Protezione dall'eliminazione](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) protegge dall'eliminazione forzata del segreto/insieme di credenziali anche dopo l'eliminazione temporanea

### <a name="create-cluster-resource"></a>Creare la risorsa *Cluster*

Questa risorsa viene usata come connessione di identità intermedia tra Key Vault e aree di lavoro Log Analytics. Dopo aver ricevuto la conferma che le sottoscrizioni sono state consentite, creare una risorsa *Cluster* log Analytics nell'area in cui si trovano le aree di lavoro.

Quando si crea una risorsa *Cluster* è necessario specificare il livello (sku) di *prenotazione della capacità*. Il livello di *prenotazione della capacità* può essere compreso tra 1.000 e 2.000 GB al giorno ed è possibile aggiornarlo di 100 in volta in un secondo momento. Se è necessario un livello di prenotazione della capacità superiore a 2.000 GB al giorno, contattare LAIngestionRate@microsoft.com. [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)

La proprietà *billingType* determina l'attribuzione della fatturazione per la risorsa *Cluster* e i relativi dati:
- *Cluster* (impostazione predefinita): i costi di prenotazione della capacità per il cluster sono attribuiti alla risorsa *cluster* .
- *Aree di lavoro* : i costi di prenotazione della capacità per il cluster sono attribuiti proporzionalmente alle aree di lavoro del cluster, con la fatturazione della risorsa *cluster* in parte dell'utilizzo se il totale dei dati inseriti per la giornata è inferiore alla prenotazione della capacità. Per ulteriori informazioni sul modello di determinazione prezzi del cluster, vedere [log Analytics cluster dedicati](manage-cost-storage.md#log-analytics-dedicated-clusters) . 

> [!NOTE]
> * Dopo aver creato la risorsa *Cluster* è possibile aggiornarla con *sku*, *keyVaultProperties* o *billingType* usando una richiesta PATCH REST.
> * È possibile aggiornare *billingType* usando la richiesta REST attualmente non supportata in PowerShell

Questa operazione è asincrona e può essere completata.

> [!IMPORTANT]
> Copiare e salvare la risposta perché i dettagli saranno necessari nei passaggi successivi.
> 

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -Location "region-name" -SkuCapacity "daily-ingestion-gigabyte" 
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

L'identità viene assegnata alla risorsa *Cluster* al momento della creazione.

**Risposta**

200 OK e intestazione.

Mentre è in corso il provisioning del cluster di Log Analytics che richiede del tempo, è possibile controllare lo stato in due modi:

1. Copiare il valore dell'URL di Azure-AsyncOperation dalla risposta e seguire la [verifica dello stato delle operazioni asincrone](#asynchronous-operations-and-status-check).
2. Inviare una richiesta GET alla risorsa *Cluster* e osservare il valore di *provisioningState*. Il valore è *ProvisioningAccount* durante il provisioning e *Succeeded* al termine dell'operazione.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**Risposta**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "provisioningState": "ProvisioningAccount",
    "billingType": "cluster",
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

Il GUID "principalId" viene generato dal servizio di gestione delle identità gestite per la risorsa *Cluster*.

### <a name="grant-key-vault-permissions"></a>Concedere le autorizzazioni di Key Vault

Aggiornare la Key Vault con i nuovi criteri di accesso per concedere le autorizzazioni per la risorsa *cluster* . Queste autorizzazioni vengono usate dall'archiviazione di Monitoraggio di Azure sottostante per la crittografia dei dati. Aprire Key Vault nel portale di Azure e fare clic su "Criteri di accesso" e quindi su "+ Aggiungi un criterio di accesso" per creare un criterio con le impostazioni seguenti:

- Autorizzazioni chiave: selezionare le autorizzazioni "Recupera", "Esegui il wrapping della chiave" e "Annulla il wrapping della chiave".
- Seleziona entità: immettere il nome della risorsa *cluster* o il valore dell'ID entità restituito nella risposta nel passaggio precedente.

![concedere le autorizzazioni di Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

L'autorizzazione *Recupera* è necessaria per verificare che l'istanza di Key Vault sia configurata come recuperabile per proteggere la chiave e l'accesso ai dati di Monitoraggio di Azure.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Aggiornare la risorsa Cluster con i dettagli dell'identificatore della chiave

Questo passaggio viene eseguito durante gli aggiornamenti iniziali e futuri delle versioni della chiave nell'istanza di Key Vault. Informa l'archiviazione di Monitoraggio di Azure sulla versione della chiave da usare per la crittografia dei dati. Quando viene aggiornata, la nuova chiave viene usata per eseguire e annullare il wrapping per la chiave di archiviazione (AEK).

Per aggiornare la risorsa *Cluster* con i dettagli dell'*identificatore di chiave* di Key Vault, selezionare la versione corrente della chiave in Azure Key Vault per ottenere i dettagli dell'identificatore di chiave.

![Concedere le autorizzazioni di Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Aggiornare il valore KeyVaultProperties della risorsa *Cluster* con i dettagli dell'identificatore di chiave.

Questa operazione è asincrona quando si aggiornano i dettagli dell'identificatore di chiave e il completamento può richiedere del tempo. È sincrono durante l'aggiornamento del valore della capacità.

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

> [!NOTE]
> È possibile aggiornare lo *SKU*di risorse *cluster* , *keyVaultProperties* o *billingType* usando patch.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

"KeyVaultProperties" contiene i dettagli dell'identificatore di chiave di Key Vault.

**Risposta**

200 OK e intestazione.
Per completare la propagazione dell'identificatore di chiave sono necessari alcuni minuti. È possibile controllare lo stato di aggiornamento in due modi:
1. Copiare il valore dell'URL di Azure-AsyncOperation dalla risposta e seguire la [verifica dello stato delle operazioni asincrone](#asynchronous-operations-and-status-check).
2. Inviare una richiesta GET alla risorsa *Cluster* e osservare le proprietà *KeyVaultProperties*. I dettagli dell'identificatore di chiave aggiornati di recente verranno restituiti nella risposta.

Una risposta alla richiesta GET sulla risorsa *Cluster* avrà l'aspetto seguente quando l'aggiornamento dell'identificatore di chiave è stato completato:

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Associazione dell'area di lavoro alla risorsa *Cluster*

Per eseguire questa operazione è necessario avere le autorizzazioni di scrittura sia per l'area di lavoro che per la risorsa *Cluster*, incluse le azioni seguenti:

- Nell'area di lavoro: Microsoft.OperationalInsights/workspaces/write
- Nella risorsa *Cluster*: Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Questo passaggio deve essere eseguito solo dopo il completamento del provisioning del cluster Log Analytics. Se si associano le aree di lavoro e si inseriscono dati prima del provisioning, i dati inseriti verranno eliminati e non saranno recuperabili.

Questa operazione è asincrona e può essere completata.

```powershell
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name").id
Set-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**Risposta**

200 OK e intestazione.

I dati inseriti vengono archiviati crittografati con la chiave gestita dopo l'operazione di associazione, che può richiedere fino a 90 minuti. È possibile controllare lo stato dell'associazione dell'area di lavoro in due modi:

1. Copiare il valore dell'URL di Azure-AsyncOperation dalla risposta e seguire la [verifica dello stato delle operazioni asincrone](#asynchronous-operations-and-status-check).
2. Inviare una richiesta [Workspaces – Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) e osservare la risposta. L'area di lavoro associata avrà un elemento clusterResourceId in "features".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**Risposta**

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="cmk-kek-revocation"></a>Revoca della chiave gestita dal cliente (KEK)

È possibile revocare l'accesso ai dati disabilitando la chiave o eliminando i criteri di accesso per la risorsa *Cluster* in Key Vault. Lo spazio di archiviazione del cluster Log Analytics rispetta sempre al massimo entro un'ora le modifiche apportate alle autorizzazioni delle chiavi e l'archivio non sarà più disponibile. Tutti i nuovi dati inseriti nelle aree di lavoro associate alla risorsa  *Cluster*  vengono eliminati e non possono essere recuperati; i dati non saranno accessibili e le query per queste aree di lavoro avranno esito negativo. I dati inseriti in precedenza rimangono nello spazio di archiviazione purché la risorsa *Cluster* e le aree di lavoro non vengano eliminate. I dati inaccessibili sono regolati dai criteri di conservazione dei dati e verranno eliminati al raggiungimento della scadenza della conservazione. 

I dati inseriti negli ultimi 14 giorni vengono anche mantenuti nella cache ad accesso frequente (con supporto SSD) per un efficace funzionamento del motore di query. Anche questi dati vengono eliminati durante l'operazione di revoca della chiave e diventano inaccessibili.

L'archiviazione esegue periodicamente il polling di Key Vault per tentare di annullare il wrapping della chiave di crittografia e, una volta eseguito l'accesso, l'inserimento dei dati e la query riprendono entro 30 minuti.

## <a name="cmk-kek-rotation"></a>Rotazione della chiave gestita dal cliente (KEK)

Per la rotazione della chiave gestita dal cliente è necessario eseguire l'aggiornamento esplicito per la risorsa *Cluster* con la nuova versione della chiave in Azure Key Vault. Seguire le istruzioni disponibili nel passaggio "Aggiornare la risorsa *Cluster* con i dettagli dell'identificatore della chiave". Se non si aggiornano i nuovi dettagli dell'identificatore di chiave nella risorsa *Cluster*, l'archivio del cluster Log Analytics continuerà a usare la chiave precedente per la crittografia. Se si disabilita o si elimina la chiave precedente prima di aggiornare la nuova chiave nella risorsa *Cluster*, si passerà allo stato di [revoca della chiave](#cmk-kek-revocation).

Tutti i dati rimarranno accessibili dopo l'operazione di rotazione della chiave perché i dati vengono sempre crittografati con la chiave di crittografia dell'account (AEK), mentre la chiave AEK viene ora crittografata con la nuova versione della chiave di crittografia della chiave (KEK) in Key Vault.

## <a name="cmk-for-queries"></a>CMK per le query

Il linguaggio di query utilizzato nel Log Analytics è espressivo e può contenere informazioni riservate nei commenti aggiunti alle query o nella sintassi della query. Alcune organizzazioni richiedono che tali informazioni vengano mantenute protette nell'ambito dei criteri CMK ed è necessario salvare le query crittografate con la chiave. Monitoraggio di Azure consente di archiviare le query salvate e per le *ricerche* con *avvisi di log* crittografate con la chiave nel proprio account di archiviazione quando si è connessi all'area di lavoro. 

> [!NOTE]
> CMK per le query utilizzate nelle cartelle di lavoro e nei dashboard di Azure non è ancora supportato. Queste query rimangono crittografate con la chiave Microsoft.  

Quando si [porta la propria](https://docs.microsoft.com/azure/azure-monitor/platform/private-storage) risorsa di archiviazione (BYOS) e la si associa all'area di lavoro, il servizio carica le query *salvate* e gli *avvisi di log* nell'account di archiviazione. Ciò significa che è possibile controllare l'account di archiviazione e i [criteri di crittografia](https://docs.microsoft.com/azure/storage/common/encryption-customer-managed-keys) dei dati inattivi usando la stessa chiave usata per crittografare i dati in log Analytics cluster o una chiave diversa. Si sarà tuttavia responsabili dei costi associati all'account di archiviazione. 

**Considerazioni prima di impostare CMK per le query**
* È necessario disporre delle autorizzazioni di scrittura per l'area di lavoro e l'account di archiviazione
* Assicurarsi di creare l'account di archiviazione nella stessa area in cui si trova l'area di lavoro Log Analytics
* Il *salvataggio delle ricerche* nell'archiviazione viene considerato come artefatti del servizio e il relativo formato potrebbe cambiare
* Le *ricerche di salvataggio* esistenti vengono rimosse dall'area di lavoro. Copiare ed eventuali *ricerche di salvataggio* necessarie prima della configurazione. È possibile visualizzare le *ricerche salvate* usando [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/Get-AzOperationalInsightsSavedSearch)
* La cronologia delle query non è supportata e non sarà possibile visualizzare le query eseguite
* È possibile associare un singolo account di archiviazione all'area di lavoro allo scopo di salvare le query, ma è possibile usarlo per le query di *ricerca salvate* e di *log-alerts*
* Il PIN al dashboard non è supportato

**Configurare BYOS per le query di ricerca salvate**

Associare l'account di archiviazione per la *query* all'area di lavoro: le query *salvate* vengono salvate nell'account di archiviazione. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "resource-group-name"storage-account-name"resource-group-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-03-01-preview
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

Dopo la configurazione, qualsiasi nuova query di *ricerca salvata* verrà salvata nella risorsa di archiviazione.

**Configurare BYOS per le query log-alerts**

Associare l'account di archiviazione per gli *avvisi* all'area di lavoro: le query *log-alerts* vengono salvate nell'account di archiviazione. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "resource-group-name"storage-account-name"resource-group-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-03-01-preview
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

Dopo la configurazione, qualsiasi nuova query di avviso verrà salvata nella risorsa di archiviazione.

## <a name="cmk-management"></a>Gestione CMK

- **Ottenere tutte le risorse *Cluster*  per un gruppo di risorse**
  
  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Risposta**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

- **Ottenere tutte le risorse *Cluster* per una sottoscrizione**
  
  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Risposta**
    
  La stessa risposta di 'risorse *Cluster* per un gruppo di risorse', ma nell'ambito della sottoscrizione.

- **Aggiornare la *prenotazione della capacità* nella risorsa *Cluster***

  Quando il volume di dati per le aree di lavoro associate cambia nel tempo e si vuole aggiornare il livello di prenotazione della capacità in modo appropriato. Seguire il passaggio di [aggiornamento della risorsa *Cluster*](#update-cluster-resource-with-key-identifier-details) e fornire il nuovo valore di capacità. Questo valore può essere compreso tra 1.000 e 2.000 GB al giorno e modificato di 100 alla volta. Per un livello superiore a 2.000 GB al giorno, contattare Microsoft. Si noti che non è necessario fornire il corpo completo della richiesta REST ed è necessario includere lo SKU:

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity "daily-ingestion-gigabyte"
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- **Aggiornare *billingType* nella risorsa *Cluster***

  La proprietà *billingType* determina l'attribuzione della fatturazione per la risorsa *Cluster* e i relativi dati:
  - *cluster* (impostazione predefinita): la fatturazione viene attribuita alla sottoscrizione che ospita la risorsa Cluster
  - *workspaces*: la fatturazione viene attribuita alle sottoscrizioni che ospitano le aree di lavoro in modo proporzionale
  
  Seguire il passaggio di [aggiornamento della risorsa *Cluster*](#update-cluster-resource-with-key-identifier-details) e fornire il nuovo valore di billingType. Si noti che non è necessario fornire il corpo completo della richiesta REST ed è necessario includere *billingType*:

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Dissociare l'area di lavoro**

  Per eseguire questa operazione sono necessarie le autorizzazioni di scrittura per l'area di lavoro e la risorsa *Cluster*. È possibile dissociare un'area di lavoro dalla risorsa *Cluster* in qualsiasi momento. I nuovi dati inseriti dopo l'operazione di dissociazione vengono archiviati nell'archivio Log Analytics e crittografati con la chiave Microsoft. È possibile eseguire query sui dati che sono stati inseriti nell'area di lavoro prima e dopo la dissociazione, purché siano stati eseguiti il provisioning e la configurazione della risorsa *Cluster* con una chiave di Key Vault valida.

  Questa operazione è asincrona e può essere completata.

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Risposta**

  200 OK e intestazione.

  I dati inseriti dopo l'operazione di dissociazione vengono archiviati nell'archivio Log Analytics. Questa operazione può richiedere 90 minuti. È possibile controllare lo stato della dissociazione dell'area di lavoro in due modi:

  1. Copiare il valore dell'URL di Azure-AsyncOperation dalla risposta e seguire la [verifica dello stato delle operazioni asincrone](#asynchronous-operations-and-status-check).
  2. Inviare una richiesta [Workspaces – Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) e osservare la risposta. L'area di lavoro dissociata non avrà l'elemento *clusterResourceId* in *features*.

- **Verificare lo stato dell'associazione dell'area di lavoro**
  
  Eseguire un'operazione get sull'area di lavoro e osservare se la proprietà *clusterResourceId* è presente nella risposta in *features*. L'area di lavoro associata avrà la proprietà *clusterResourceId* .

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **Eliminare la risorsa *Cluster***

  Per eseguire questa operazione sono necessarie le autorizzazioni di scrittura per la risorsa *Cluster*. Viene eseguita un'operazione di eliminazione temporanea per consentire il ripristino della risorsa *Cluster*, inclusi i dati degli ultimi 14 giorni, a prescindere che l'eliminazione sia stata accidentale o intenzionale. Il nome della risorsa *Cluster* rimane riservato durante il periodo di eliminazione temporanea e non è possibile creare un nuovo cluster con lo stesso nome. Dopo il periodo di eliminazione temporanea, il nome della risorsa *Cluster* viene reso disponibile; la risorsa *Cluster* e i dati vengono eliminati definitivamente e non sono recuperabili. Ogni area di lavoro associata viene dissociata dalla risorsa *Cluster* durante l'operazione di eliminazione. I nuovi dati inseriti vengono archiviati nell'archivio Log Analytics e crittografati con la chiave Microsoft. 
  
  L'operazione di dissociazione dell'area di lavoro è asincrona e può richiedere fino a 90 minuti.

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Risposta**

  200 - OK

- **Recuperare la risorsa *Cluster* e i dati** 
  
  Una risorsa *Cluster* eliminata negli ultimi 14 giorni è in stato di eliminazione temporanea e può essere recuperata con i relativi dati. Tutte le aree di lavoro sono state dissociate dalla risorsa *Cluster* con l'eliminazione della risorsa *Cluster* stessa, quindi è necessario associare nuovamente le aree di lavoro dopo il ripristino per la crittografia della chiave gestita dal cliente. L'operazione di ripristino viene al momento eseguita manualmente dal gruppo dei prodotti. Usare il canale Microsoft per le richieste di ripristino.

## <a name="limitationsandconstraints"></a>Limiti e vincoli

\- La chiave gestita dal cliente (CMK) è supportata nel cluster Log Analytics dedicato ed è adatta ai clienti che inviano almeno 1 TB al giorno.

\- Il numero massimo di risorse  *Cluster*  per area geografica e sottoscrizione è 2

\- È possibile associare un'area di lavoro alla risorsa  *Cluster*  e quindi dissociarla se la chiave gestita dal cliente non è necessaria per l'area di lavoro. Il numero di associazioni dell'area di lavoro in una determinata area di lavoro in un periodo di 30 giorni è limitato a 2

\- L'associazione dell'area di lavoro alla risorsa  *Cluster*  deve essere eseguita SOLO dopo aver verificato il completamento del provisioning del cluster Log Analytics. I dati inviati all'area di lavoro prima del completamento verranno eliminati e non saranno recuperabili.

\- La crittografia CMK si applica ai dati inseriti dopo la configurazione della     chiave gestita dal cliente. I dati inseriti prima della configurazione della     chiave gestita dal cliente rimangono crittografati con la chiave Microsoft. È possibile eseguire query sui     dati inseriti prima e dopo la configurazione della chiave gestita dal cliente.

\- Azure Key Vault deve essere configurato come recuperabile.Queste proprietà non sono abilitate per impostazione predefinita e devono essere configurate tramite CLI o PowerShell:

  - [Eliminazione temporanea](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 
      è necessario che sia attivata    - la  [protezione ripulitura](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)   per evitare l'eliminazione forzata del segreto/insieme di credenziali anche dopo l'eliminazione temporanea.

Il trasferimento di una risorsa - *Cluster*  in un altro gruppo di risorse o in un'altra sottoscrizione     non è attualmente supportato.

\- L'istanza di Azure Key Vault, la risorsa  *Cluster*  e le aree di lavoro associate devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD) ma possono essere in sottoscrizioni diverse.

- L'associazione dell'area di lavoro alla risorsa  *Cluster* avrà esito negativo se eseguita per un'altra risorsa  *Cluster* 

## <a name="troubleshooting"></a>Risoluzione dei problemi

- Comportamento con la disponibilità di Key Vault
  - Nel funzionamento normale: l'archiviazione memorizza la chiave AEK nella cache per brevi periodi di tempo e torna a Key Vault per annullare il wrapping periodicamente.
    
  - Errori di connessione temporanei: l'archiviazione gestisce gli errori temporanei (timeout, errori di connessione, problemi relativi a DNS) consentendo alle chiavi di rimanere nella cache per un po' più di tempo, superando così piccoli problemi di disponibilità. Le funzionalità di query e inserimento proseguono senza interruzioni.
    
  - Sito Live: una indisponibilità di circa 30 minuti comporterà la mancata disponibilità dell'account di archiviazione. La funzionalità di query non sarà disponibile e i dati inseriti verranno memorizzati nella cache per diverse ore usando la chiave Microsoft per evitare la perdita dei dati. Quando viene ripristinato l'accesso a Key Vault, la query diventa disponibile e i dati temporanei memorizzati nella cache vengono inseriti nell'archivio dati e crittografati con la chiave gestita dal cliente.

  - Frequenza di accesso a Key Vault: la frequenza con cui l'archivio di Monitoraggio di Azure accede a Key Vault per le operazioni di wrapping e annullamento del wrapping è compresa tra 6 e 60 secondi.

- Se si crea una risorsa *Cluster* e si specifica immediatamente il valore di KeyVaultProperties, l'operazione potrebbe non riuscire perché i criteri di accesso non possono essere definiti fino a quando non viene assegnata l'identità del sistema alla risorsa *Cluster*.

- Se si aggiorna la risorsa *Cluster* esistente con KeyVaultProperties e il criterio di accesso "Recupera" non è presente in Key Vault, l'operazione avrà esito negativo.

- Se si verifica un errore di conflitto durante la creazione di una risorsa *Cluster*, è possibile che la risorsa *Cluster* sia stata eliminata negli ultimi 14 giorni e si trovi nel periodo di eliminazione temporanea. Il nome della risorsa *Cluster* rimane riservato durante il periodo di eliminazione temporanea e non è possibile creare un nuovo cluster con lo stesso nome. Il nome viene reso disponibile dopo il periodo di eliminazione temporanea quando la risorsa *Cluster* viene eliminata definitivamente.

- Se si aggiorna la risorsa *Cluster* mentre è in corso un'operazione, l'operazione avrà esito negativo.

- Se non si riesce a distribuire la risorsa *Cluster*, verificare che Azure Key Vault, la risorsa  *Cluster*  e le aree di lavoro Log Analytics associate si trovino nella stessa area geografica. Le sottoscrizioni possono essere diverse.

- Se si aggiorna la versione della chiave in Key Vault e non si aggiornano i nuovi dettagli dell'identificatore di chiave nella risorsa *Cluster*, il cluster Log Analytics continuerà a usare la chiave precedente e i dati diventeranno inaccessibili. Aggiornare i nuovi dettagli dell'identificatore di chiave nella risorsa *Cluster* per riprendere l'inserimento dei dati e la possibilità di eseguire query sui dati.

- Alcune operazioni sono lunghe e possono richiedere del tempo per essere completate, ovvero creazione di *cluster* , aggiornamento della chiave del *cluster* e eliminazione del *cluster* . È possibile controllare lo stato dell'operazione in due modi:
  1. Quando si usa REST, copiare il valore di URL di Azure-AsyncOperation dalla risposta e seguire la [Verifica dello stato delle operazioni asincrone](#asynchronous-operations-and-status-check).
  2. Inviare una richiesta GET a un *cluster* o a un'area di lavoro e osservare la risposta. Ad esempio, l'area di lavoro dissociata non avrà *clusterResourceId* in *funzionalità*.

- Per il supporto e l'assistenza relativi alla chiave gestita dal cliente, usare i contatti di Microsoft.
