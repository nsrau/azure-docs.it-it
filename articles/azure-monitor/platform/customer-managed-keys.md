---
title: Chiave gestita dal cliente di Monitoraggio di Azure
description: Informazioni e procedure per configurare la chiave gestita dal cliente (CMK) per crittografare i dati nelle aree di lavoro Log Analytics usando una chiave di Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 09/09/2020
ms.openlocfilehash: 532d96163e2ec66730dc3fdf87f10904fd584224
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107998"
---
# <a name="azure-monitor-customer-managed-key"></a>Chiave gestita dal cliente di Monitoraggio di Azure 

Questo articolo fornisce informazioni generali e procedure per la configurazione delle chiavi gestite dal cliente (CMK) per le aree di lavoro Log Analytics. Dopo aver eseguito la configurazione, tutti i dati inviati alle aree di lavoro vengono crittografati con la chiave di Azure Key Vault.

È consigliabile esaminare [limiti e vincoli](#limitationsandconstraints) di seguito prima di procedere alla configurazione.

## <a name="customer-managed-key-cmk-overview"></a>Panoramica della chiave gestita dal cliente (CMK)

La [crittografia di](../../security/fundamentals/encryption-atrest.md) dati inattivi è un requisito comune per la privacy e la sicurezza nelle organizzazioni.  È possibile lasciare che Azure gestisca completamente la crittografia di dati inattivi, mentre sono disponibili diverse opzioni per gestire con precisione la crittografia o le chiavi di crittografia.

Monitoraggio di Azure garantisce che tutti i dati e le query salvate siano crittografati a riposo usando chiavi gestite da Microsoft (MMK). Monitoraggio di Azure offre anche un'opzione per la crittografia usando la propria chiave archiviata nel [Azure Key Vault](../../key-vault/general/overview.md) e a cui si accede tramite l'archiviazione tramite l'autenticazione dell' [identità gestita](../../active-directory/managed-identities-azure-resources/overview.md) assegnata dal sistema. Questa chiave (CMK) può essere [software o hardware-HSM protetto](../../key-vault/general/overview.md). L'uso della crittografia da parte di monitoraggio di Azure è identico a quello di [Azure Storage Encryption](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) .

La funzionalità CMK viene fornita su cluster Log Analytics dedicati e fornisce il controllo per revocare l'accesso ai dati in qualsiasi momento e proteggerli con il controllo dell' [Archivio](#customer-lockbox-preview) . Per verificare di disporre della capacità necessaria per il cluster dedicato nella propria area, è necessario che la sottoscrizione sia consentita in anticipo. Usare il contatto Microsoft per ottenere la sottoscrizione consentita prima di iniziare a configurare CMK.

Il [modello di determinazione dei prezzi per i cluster log Analytics](./manage-cost-storage.md#log-analytics-dedicated-clusters) usa le prenotazioni di capacità a partire da un livello di 1000 GB/giorno.

I dati inseriti negli ultimi 14 giorni vengono anche mantenuti nella cache ad accesso frequente (con supporto SSD) per un efficace funzionamento del motore di query. Questi dati rimangono crittografati con le chiavi di Microsoft indipendentemente dalla configurazione di CMK, ma il controllo sui dati SSD rispetta la [revoca delle chiavi](#cmk-kek-revocation). Microsoft sta lavorando per offrire la crittografia dei dati SSD con chiave gestita dal cliente nella seconda metà del 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Funzionamento della chiave gestita dal cliente in Monitoraggio di Azure

Monitoraggio di Azure sfrutta l'identità gestita assegnata dal sistema per concedere l'accesso ad Azure Key Vault. L'identità gestita assegnata dal sistema può essere associata solo a una singola risorsa di Azure, mentre l'identità del cluster Log Analytics è supportata a livello di cluster. Ciò impone che la funzionalità di chiave gestita dal cliente sia distribuita in un cluster Log Analytics dedicato. Per supportare la chiave gestita dal cliente in più aree di lavoro, una nuova risorsa *Cluster* Log Analytics funge da connessione di identità intermedia tra Key Vault e le aree di lavoro Log Analytics. Lo spazio di archiviazione del cluster Log Analytics usa l'identità gestita \' associata alla risorsa *Cluster* per l'autenticazione ad Azure Key Vault tramite Azure Active Directory. 

Dopo la configurazione di CMK, tutti i dati inseriti nelle aree di lavoro collegate al cluster dedicato vengono crittografati con la chiave in Key Vault. È possibile scollegare le aree di lavoro dal cluster in qualsiasi momento. I nuovi dati vengono inseriti nell'archivio di Log Analytics e crittografati con la chiave Microsoft, mentre è possibile eseguire facilmente query sui dati nuovi e meno recenti.


![Panoramica della chiave gestita dal cliente (CMK)](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. La risorsa *Cluster* di Log Analytics con identità gestita con le autorizzazioni per Key Vault: l'identità viene propagata all'archivio cluster sottostante dedicato Log Analytics
3. Cluster Log Analytics dedicato
4. Aree di lavoro collegate alla risorsa *cluster* per la crittografia CMK

## <a name="encryption-keys-operation"></a>Funzionamento delle chiavi di crittografia

La crittografia dei dati di archiviazione include tre tipi di chiavi:

- **KEK** - chiave di crittografia della chiave (CMK)
- **AEK** - chiave di crittografia dell'account
- **DEK** - chiave di crittografia dei dati

Sono applicabili le regole seguenti:

- Gli account di archiviazione del cluster Log Analytics generano una chiave di crittografia univoca per ogni account di archiviazione, nota come AEK.

- La chiave AEK viene usata per ottenere chiavi DEK, ovvero le chiavi usate per crittografare ogni blocco di dati scritti su disco.

- Quando si configura la chiave in Key Vault e vi si fa riferimento nel cluster, archiviazione di Azure invia le richieste al Azure Key Vault per eseguire il wrapping e annullare il wrapping dell'AEK per eseguire operazioni di crittografia e decrittografia dei dati.

- La chiave KEK non esce mai da Key Vault e, nel caso di una chiave HSM, non esce mai dall'hardware.

- Archiviazione di Azure usa l'identità gestita associata alla risorsa *cluster* per l'autenticazione e l'accesso ai Azure Key Vault tramite Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Procedura di provisioning della chiave gestita dal cliente

1. Concessione della sottoscrizione: la funzionalità CMK viene distribuita su cluster Log Analytics dedicati. Per verificare di disporre della capacità necessaria nella propria area, è necessario che la sottoscrizione sia consentita in anticipo. Usare il contatto Microsoft per ottenere la sottoscrizione.
2. Creazione di Azure Key Vault e archiviazione della chiave
3. Creazione del cluster
4. Concessione delle autorizzazioni a Key Vault
5. Collegamento di aree di lavoro Log Analytics

La configurazione di CMK non è supportata nel portale di Azure e il provisioning viene eseguito tramite [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/), l' [interfaccia](https://docs.microsoft.com/cli/azure/monitor/log-analytics) della riga di comando o le richieste [Rest](https://docs.microsoft.com/rest/api/loganalytics/) .

### <a name="asynchronous-operations-and-status-check"></a>Operazioni asincrone e controllo dello stato

Alcuni passaggi di configurazione vengono eseguiti in modo asincrono perché non possono essere completati rapidamente. Quando si usano richieste REST nella configurazione, la risposta restituisce inizialmente un codice di stato HTTP 200 (OK) e un'intestazione con la proprietà *Azure-AsyncOperation* quando accettata:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

È quindi possibile controllare lo stato dell'operazione asincrona inviando una richiesta GET al valore dell'intestazione *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
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

L'eliminazione del cluster è in corso: quando si elimina un cluster con aree di lavoro collegate, viene eseguita un'operazione di scollegamento per ciascuna area di lavoro in modo asincrono e l'operazione può richiedere alcuni minuti.
Questo non è pertinente quando si elimina un cluster senza area di lavoro collegata, in questo caso il cluster viene eliminato immediatamente.
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
> La funzionalità della chiave gestita dal cliente è a livello di area. Le aree di lavoro Azure Key Vault, cluster e Log Analytics collegate devono trovarsi nella stessa area, ma possono trovarsi in sottoscrizioni diverse.

### <a name="storing-encryption-key-kek"></a>Archiviazione della chiave di crittografia (KEK)

Creare o usare un'istanza di Azure Key Vault già disponibile per generare o importare una chiave da usare per la crittografia dei dati. L'istanza di Azure Key Vault deve essere configurata come recuperabile per proteggere la chiave e l'accesso ai dati in Monitoraggio di Azure. È possibile verificare questa configurazione nelle proprietà dell'istanza di Key Vault. Devono essere abilitate sia *Eliminazione temporanea* che *Protezione dall'eliminazione*.

![Impostazioni Eliminazione temporanea e Protezione dall'eliminazione](media/customer-managed-keys/soft-purge-protection.png)

Queste impostazioni possono essere aggiornate tramite l'interfaccia della riga di comando e PowerShell:

- [eliminazione temporanea](../../key-vault/general/soft-delete-overview.md)
- [Protezione dall'eliminazione](../../key-vault/general/soft-delete-overview.md#purge-protection) protegge dall'eliminazione forzata del segreto/insieme di credenziali anche dopo l'eliminazione temporanea

### <a name="create-cluster"></a>Creare cluster

Seguire la procedura illustrata nell' [articolo sui cluster dedicati](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#creating-a-cluster). 

> [!IMPORTANT]
> Copiare e salvare la risposta perché i dettagli saranno necessari nei passaggi successivi.

### <a name="grant-key-vault-permissions"></a>Concedere le autorizzazioni di Key Vault

Aggiornare la Key Vault con i nuovi criteri di accesso per concedere le autorizzazioni al cluster. Queste autorizzazioni vengono usate dall'archiviazione di Monitoraggio di Azure sottostante per la crittografia dei dati. Aprire Key Vault nel portale di Azure e fare clic su "Criteri di accesso" e quindi su "+ Aggiungi un criterio di accesso" per creare un criterio con le impostazioni seguenti:

- Autorizzazioni chiave: selezionare le autorizzazioni "Recupera", "Esegui il wrapping della chiave" e "Annulla il wrapping della chiave".
- Seleziona entità: immettere il nome del cluster o il valore dell'ID entità restituito nella risposta nel passaggio precedente.

![concedere le autorizzazioni di Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

L'autorizzazione *Recupera* è necessaria per verificare che l'istanza di Key Vault sia configurata come recuperabile per proteggere la chiave e l'accesso ai dati di Monitoraggio di Azure.

### <a name="update-cluster-with-key-identifier-details"></a>Aggiornare il cluster con i dettagli dell'identificatore di chiave

Per tutte le operazioni nel cluster è necessaria l'autorizzazione Microsoft. OperationalInsights/Clusters/Write. Questa autorizzazione può essere concessa tramite il proprietario o il collaboratore che contiene l' *azione/Write o tramite il ruolo collaboratore log Analytics che contiene l'azione Microsoft. OperationalInsights/* .

Questo passaggio Aggiorna l'archiviazione di monitoraggio di Azure con la chiave e la versione da usare per la crittografia dei dati. Quando viene aggiornata, la nuova chiave viene usata per eseguire il wrapping e annullare il wrapping della chiave di archiviazione (AEK).

Selezionare la versione corrente della chiave in Azure Key Vault per ottenere i dettagli dell'identificatore di chiave.

![Concedere le autorizzazioni di Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Aggiornare KeyVaultProperties nel cluster con i dettagli dell'identificatore di chiave.

L'operazione è asincrona e può richiedere del tempo per il completamento.

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

> [!NOTE]
> È possibile aggiornare lo *SKU*del cluster, *keyVaultProperties* o *billingType* usando patch.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
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

**Risposta**

200 OK e intestazione.
Per completare la propagazione dell'identificatore di chiave sono necessari alcuni minuti. È possibile controllare lo stato di aggiornamento in due modi:
1. Copiare il valore dell'URL di Azure-AsyncOperation dalla risposta e seguire la [verifica dello stato delle operazioni asincrone](#asynchronous-operations-and-status-check).
2. Inviare una richiesta GET nel cluster ed esaminare le proprietà *KeyVaultProperties* . I dettagli dell'identificatore di chiave aggiornati di recente verranno restituiti nella risposta.

Una risposta alla richiesta GET dovrebbe avere un aspetto simile al seguente quando l'aggiornamento dell'identificatore di chiave è completato:

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

### <a name="link-workspace-to-cluster"></a>Collega area di lavoro a cluster

Per eseguire questa operazione, è necessario disporre delle autorizzazioni ' Write ' sia per l'area di lavoro che per il cluster, incluse le azioni seguenti:

- Nell'area di lavoro: Microsoft.OperationalInsights/workspaces/write
- In cluster: Microsoft. OperationalInsights/Clusters/Write

> [!IMPORTANT]
> Questo passaggio deve essere eseguito solo dopo il completamento del provisioning del cluster Log Analytics. Se si collegano le aree di lavoro e si inseriscono dati prima del provisioning, i dati inseriti verranno eliminati e non saranno recuperabili.

Questa operazione è asincrona e può essere completata.

Seguire la procedura illustrata nell' [articolo sui cluster dedicati](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#link-a-workspace-to-the-cluster).

## <a name="cmk-kek-revocation"></a>Revoca della chiave gestita dal cliente (KEK)

È possibile revocare l'accesso ai dati disabilitando la chiave o eliminando i criteri di accesso del cluster nel Key Vault. Lo spazio di archiviazione del cluster Log Analytics rispetta sempre al massimo entro un'ora le modifiche apportate alle autorizzazioni delle chiavi e l'archivio non sarà più disponibile. Tutti i nuovi dati inseriti nelle aree di lavoro collegate al cluster vengono eliminati e non possono essere recuperati, i dati non sono accessibili e le query a queste aree di lavoro hanno esito negativo. I dati inseriti in precedenza rimangono nello spazio di archiviazione purché il cluster e le aree di lavoro non vengano eliminati. I dati inaccessibili sono regolati dai criteri di conservazione dei dati e verranno eliminati al raggiungimento della scadenza della conservazione. 

I dati inseriti negli ultimi 14 giorni vengono anche mantenuti nella cache ad accesso frequente (con supporto SSD) per un efficace funzionamento del motore di query. Anche questi dati vengono eliminati durante l'operazione di revoca della chiave e diventano inaccessibili.

L'archiviazione esegue periodicamente il polling di Key Vault per tentare di annullare il wrapping della chiave di crittografia e, una volta eseguito l'accesso, l'inserimento dei dati e la query riprendono entro 30 minuti.

## <a name="cmk-kek-rotation"></a>Rotazione della chiave gestita dal cliente (KEK)

La rotazione di CMK richiede un aggiornamento esplicito al cluster con la nuova versione della chiave in Azure Key Vault. Seguire le istruzioni riportate nel passaggio "aggiornare il cluster con i dettagli dell'identificatore di chiave". Se non si aggiornano i nuovi dettagli dell'identificatore di chiave nel cluster, l'archiviazione del cluster Log Analytics continuerà a usare la chiave precedente per la crittografia. Se si disabilita o si elimina la chiave precedente prima di aggiornare la nuova chiave nel cluster, si otterrà lo stato di [revoca della chiave](#cmk-kek-revocation) .

Tutti i dati rimarranno accessibili dopo l'operazione di rotazione della chiave perché i dati vengono sempre crittografati con la chiave di crittografia dell'account (AEK), mentre la chiave AEK viene ora crittografata con la nuova versione della chiave di crittografia della chiave (KEK) in Key Vault.

## <a name="cmk-for-queries"></a>CMK per le query

Il linguaggio di query utilizzato nel Log Analytics è espressivo e può contenere informazioni riservate nei commenti aggiunti alle query o nella sintassi della query. Alcune organizzazioni richiedono che tali informazioni vengano mantenute protette nell'ambito dei criteri CMK ed è necessario salvare le query crittografate con la chiave. Monitoraggio di Azure consente di archiviare le query salvate e per le *ricerche* con *avvisi di log* crittografate con la chiave nel proprio account di archiviazione quando si è connessi all'area di lavoro. 

> [!NOTE]
> Log Analytics le query possono essere salvate in diversi archivi a seconda dello scenario utilizzato. Le query rimangono crittografate con la chiave Microsoft (MMK) negli scenari seguenti indipendentemente dalla configurazione di CMK: cartelle di lavoro in monitoraggio di Azure, dashboard di Azure, app per la logica di Azure, Azure Notebooks e automazione manuali operativi.

Quando si porta la propria risorsa di archiviazione (BYOS) e la si collega all'area di lavoro, il servizio carica le query *salvate* e di *log-alerts* nell'account di archiviazione. Ciò significa che è possibile controllare l'account di archiviazione e i [criteri di crittografia](../../storage/common/customer-managed-keys-overview.md) dei dati inattivi usando la stessa chiave usata per crittografare i dati in log Analytics cluster o una chiave diversa. Si sarà tuttavia responsabili dei costi associati all'account di archiviazione. 

**Considerazioni prima di impostare CMK per le query**
* È necessario disporre delle autorizzazioni di scrittura per l'area di lavoro e l'account di archiviazione
* Assicurarsi di creare l'account di archiviazione nella stessa area in cui si trova l'area di lavoro Log Analytics
* Il *salvataggio delle ricerche* nell'archiviazione viene considerato come artefatti del servizio e il relativo formato potrebbe cambiare
* Le *ricerche di salvataggio* esistenti vengono rimosse dall'area di lavoro. Copiare ed eventuali *ricerche di salvataggio* necessarie prima della configurazione. È possibile visualizzare le *ricerche salvate* usando  [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)
* La cronologia delle query non è supportata e non sarà possibile visualizzare le query eseguite
* È possibile collegare un singolo account di archiviazione all'area di lavoro allo scopo di salvare le query, ma è possibile usarlo per le query di *ricerca salvate* e di *log-alerts*
* Il PIN al dashboard non è supportato

**Configurare BYOS per le query di ricerca salvate**

Collegare un account di archiviazione per la *query* all'area di lavoro: le query *salvate* vengono salvate nell'account di archiviazione. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
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

Collegare un account di archiviazione per gli *avvisi* all'area di lavoro: le query *log-alerts* vengono salvate nell'account di archiviazione. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
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

## <a name="customer-lockbox-preview"></a>Customer Lockbox (anteprima)
Archivio dati consente di approvare o rifiutare la richiesta del tecnico Microsoft per accedere ai dati durante una richiesta di supporto.

In monitoraggio di Azure questo controllo sui dati nelle aree di lavoro collegate al cluster Log Analytics dedicato. Il controllo dell'archivio protetto si applica ai dati archiviati in un Log Analytics cluster dedicato, dove viene mantenuto isolato negli account di archiviazione del cluster nella sottoscrizione protetta da file protetto.  

Altre informazioni su [Customer Lockbox per Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="cmk-management"></a>Gestione CMK

- **Ottenere tutti i cluster in un gruppo di risorse**
  
  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response**.
  
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

- **Ottenere tutti i cluster in una sottoscrizione**
  
  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **Response**.
    
  Stessa risposta di ' cluster in un gruppo di risorse ', ma nell'ambito della sottoscrizione.

- **Aggiornare la *prenotazione di capacità* nel cluster**

  Quando il volume di dati nelle aree di lavoro collegate cambia nel tempo e si vuole aggiornare il livello di prenotazione di capacità in modo appropriato. Seguire il [cluster di aggiornamento](#update-cluster-with-key-identifier-details) e fornire il nuovo valore della capacità. Può essere compreso tra 1000 e 3000 GB al giorno e nei passaggi di 100. Per un livello superiore a 3000 GB al giorno, raggiungi il contatto Microsoft per abilitarlo. Si noti che non è necessario fornire il corpo della richiesta REST completo, ma deve includere lo SKU:

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity "daily-ingestion-gigabyte"
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- **Aggiornare *billingType* nel cluster**

  La proprietà *billingType* determina l'attribuzione della fatturazione per il cluster e i relativi dati:
  - *cluster* (impostazione predefinita): la fatturazione viene attribuita alla sottoscrizione che ospita la risorsa Cluster
  - *workspaces*: la fatturazione viene attribuita alle sottoscrizioni che ospitano le aree di lavoro in modo proporzionale
  
  Seguire il [cluster di aggiornamento](#update-cluster-with-key-identifier-details) e fornire il nuovo valore billingType. Si noti che non è necessario fornire il corpo completo della richiesta REST ed è necessario includere *billingType*:

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Unlink workspace** (Scollegare l'area di lavoro)

  Per eseguire questa operazione, sono necessarie le autorizzazioni ' Write ' per l'area di lavoro e il cluster. È possibile scollegare un'area di lavoro dal cluster in qualsiasi momento. I nuovi dati inseriti dopo l'operazione di scollegamento vengono archiviati nella risorsa di archiviazione Log Analytics e crittografati con la chiave Microsoft. È possibile eseguire query sui dati inseriti nell'area di lavoro prima e dopo lo scollegamento senza interruzioni fino a quando il cluster viene sottomesso a provisioning e configurato con una chiave di Key Vault valida.

  Questa operazione è asincrona e può essere completata.

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Risposta**

  200 OK e intestazione.

  I dati inseriti dopo l'operazione di scollegamento vengono archiviati nella risorsa di archiviazione Log Analytics, il completamento di questa operazione può richiedere 90 minuti. È possibile controllare lo stato di scollegamento dell'area di lavoro in due modi:

  1. Copiare il valore dell'URL di Azure-AsyncOperation dalla risposta e seguire la [verifica dello stato delle operazioni asincrone](#asynchronous-operations-and-status-check).
  2. Inviare un' [area di lavoro: ottenere](/rest/api/loganalytics/workspaces/get) la richiesta e osservare la risposta, l'area di lavoro non collegata non avrà *clusterResourceId* in *funzionalità*.

- **Verifica stato collegamento area di lavoro**
  
  Eseguire un'operazione get sull'area di lavoro e osservare se la proprietà *clusterResourceId* è presente nella risposta in *features*. Un'area di lavoro collegata avrà la proprietà *clusterResourceId* .

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **Eliminare il cluster**

  Per eseguire questa operazione, sono necessarie le autorizzazioni ' Write ' per il cluster. Viene eseguita un'operazione di eliminazione temporanea per consentire il ripristino del cluster, inclusi i dati entro 14 giorni, se l'eliminazione è stata accidentale o intenzionale. Il nome del cluster rimane riservato durante il periodo di eliminazione temporanea e non è possibile creare un nuovo cluster con tale nome. Dopo il periodo di eliminazione temporanea, il nome del cluster viene rilasciato e il cluster e i relativi dati vengono eliminati definitivamente e non sono recuperabili. Ogni area di lavoro collegata viene scollegata dal cluster durante l'operazione di eliminazione. I nuovi dati inseriti vengono archiviati nell'archivio Log Analytics e crittografati con la chiave Microsoft. 
  
  L'operazione di scollegamento è asincrona e può richiedere fino a 90 minuti per il completamento.

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Risposta**

  200 - OK

- **Ripristinare il cluster e i dati** 
  
  Un cluster eliminato negli ultimi 14 giorni è in stato di eliminazione temporanea e può essere recuperato con i relativi dati. Poiché tutte le aree di lavoro sono state scollegate dal cluster alla relativa eliminazione, è necessario collegare nuovamente le aree di lavoro dopo il ripristino per la crittografia CMK. L'operazione di ripristino viene al momento eseguita manualmente dal gruppo dei prodotti. Usare il canale Microsoft per le richieste di ripristino.

## <a name="limitationsandconstraints"></a>Limiti e vincoli

- CMK è supportato in un cluster Log Analytics dedicato e adatto per i clienti che inviano 1 TB al giorno o più.

- Il numero massimo di cluster per area e sottoscrizione è 2

Il numero massimo di aree di lavoro collegate al cluster è 100

- È possibile collegare un'area di lavoro al cluster e quindi scollegarla se CMK non è necessario per l'area di lavoro. Il numero di operazioni di collegamento dell'area di lavoro su un'area di lavoro specifica è limitato a 2 in un periodo di 30 giorni.

- Il collegamento dell'area di lavoro al cluster deve essere eseguito solo dopo aver verificato il completamento del provisioning del cluster Log Analytics.  I dati inviati all'area di lavoro prima del completamento verranno eliminati e non saranno recuperabili.

- La crittografia CMK si applica ai dati appena inseriti dopo la configurazione di CMK.  I dati inseriti prima della configurazione della chiave gestita dal cliente rimangono crittografati con la chiave Microsoft.  È possibile eseguire query sui dati inseriti prima e dopo la configurazione della chiave gestita dal cliente.

- Il Azure Key Vault deve essere configurato come reversibile. Queste proprietà non sono abilitate per impostazione predefinita e devono essere configurate tramite CLI o PowerShell:<br>
  - [eliminazione temporanea](../../key-vault/general/soft-delete-overview.md)
  - La [protezione ripulisce](../../key-vault/general/soft-delete-overview.md#purge-protection) deve essere attivata per prevenire l'eliminazione forzata del segreto/insieme di credenziali anche dopo l'eliminazione temporanea.

- Il passaggio del cluster a un altro gruppo di risorse o a una sottoscrizione non è attualmente supportato.

- Il Azure Key Vault, il cluster e le aree di lavoro collegate devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse.

- Il collegamento dell'area di lavoro al cluster non riuscirà se è collegato a un altro cluster

## <a name="troubleshooting"></a>Risoluzione dei problemi

- Comportamento con la disponibilità di Key Vault
  - Nel funzionamento normale: l'archiviazione memorizza la chiave AEK nella cache per brevi periodi di tempo e torna a Key Vault per annullare il wrapping periodicamente.
    
  - Errori di connessione temporanei: l'archiviazione gestisce gli errori temporanei (timeout, errori di connessione, problemi relativi a DNS) consentendo alle chiavi di rimanere nella cache per un po' più di tempo, superando così piccoli problemi di disponibilità. Le funzionalità di query e inserimento proseguono senza interruzioni.
    
  - Sito Live: una indisponibilità di circa 30 minuti comporterà la mancata disponibilità dell'account di archiviazione. La funzionalità di query non sarà disponibile e i dati inseriti verranno memorizzati nella cache per diverse ore usando la chiave Microsoft per evitare la perdita dei dati. Quando viene ripristinato l'accesso a Key Vault, la query diventa disponibile e i dati temporanei memorizzati nella cache vengono inseriti nell'archivio dati e crittografati con la chiave gestita dal cliente.

  - Frequenza di accesso a Key Vault: la frequenza con cui l'archivio di Monitoraggio di Azure accede a Key Vault per le operazioni di wrapping e annullamento del wrapping è compresa tra 6 e 60 secondi.

- Se si crea un cluster e si specifica immediatamente il KeyVaultProperties, l'operazione potrebbe non riuscire perché i criteri di accesso non possono essere definiti fino a quando non viene assegnata l'identità del sistema al cluster.

- Se si aggiorna il cluster esistente con KeyVaultProperties e i criteri di accesso alla chiave ' Get ' mancano nell'Key Vault, l'operazione avrà esito negativo.

- Se si verifica un errore di conflitto durante la creazione di un cluster, è possibile che sia stato eliminato il cluster negli ultimi 14 giorni ed è in fase di eliminazione temporanea. Il nome del cluster rimane riservato durante il periodo di eliminazione temporanea e non è possibile creare un nuovo cluster con tale nome. Il nome viene rilasciato dopo il periodo di eliminazione temporanea quando il cluster viene eliminato definitivamente.

- Se si aggiorna il cluster mentre è in corso un'operazione, l'operazione avrà esito negativo.

- Se non si riesce a distribuire il cluster, verificare che le aree di lavoro Azure Key Vault, cluster e Log Analytics collegate si trovino nella stessa area. Le sottoscrizioni possono essere diverse.

- Se si aggiorna la versione della chiave in Key Vault e non si aggiornano i nuovi dettagli dell'identificatore di chiave nel cluster, il cluster Log Analytics continuerà a usare la chiave precedente e i dati diventeranno inaccessibili. Aggiornare i nuovi dettagli dell'identificatore di chiave nel cluster per riprendere l'inserimento dei dati e la possibilità di eseguire query sui dati.

- Alcune operazioni sono lunghe e possono richiedere del tempo per essere completate, ovvero creazione di cluster, aggiornamento della chiave del cluster e eliminazione del cluster. È possibile controllare lo stato dell'operazione in due modi:
  1. Quando si usa REST, copiare il valore di Azure-AsyncOperation URL dalla risposta e seguire la [Verifica dello stato delle operazioni asincrone](#asynchronous-operations-and-status-check).
  2. Inviare una richiesta GET a un cluster o a un'area di lavoro e osservare la risposta. Ad esempio, l'area di lavoro scollegata non avrà *clusterResourceId* in *funzionalità*.

- Per il supporto e l'assistenza relativi alla chiave gestita dal cliente, usare i contatti di Microsoft.

- messaggi di errore
  
  Creazione cluster:
  -  400--il nome del cluster non è valido. Il nome del cluster può contenere i caratteri a-z, A-Z, 0-9 e la lunghezza di 3-63.
  -  400: il corpo della richiesta è null o in formato non valido.
  -  400--il nome dello SKU non è valido. Impostare il nome dello SKU su capacityReservation.
  -  400: è stata specificata la capacità, ma lo SKU non è capacityReservation. Impostare il nome dello SKU su capacityReservation.
  -  400: capacità mancante nello SKU. Impostare valore capacità su 1000 o superiore nei passaggi di 100 (GB).
  -  400--la capacità nello SKU non è compresa nell'intervallo. Deve essere minimo 1000 e fino alla capacità massima consentita disponibile in ' utilizzo e costo stimato ' nell'area di lavoro.
  -  400: la capacità è bloccata per 30 giorni. La riduzione della capacità è consentita 30 giorni dopo l'aggiornamento.
  -  400--non è stato impostato alcuno SKU. Impostare il nome dello SKU su capacityReservation e il valore della capacità su 1000 o una versione successiva nei passaggi di 100 (GB).
  -  400--Identity è null o vuoto. Impostare Identity con systemAssigned Type.
  -  400--KeyVaultProperties impostati al momento della creazione. Aggiornare KeyVaultProperties dopo la creazione del cluster.
  -  400: non è possibile eseguire l'operazione ora. L'operazione asincrona è in uno stato diverso da succeeded. Prima di eseguire qualsiasi operazione di aggiornamento, il cluster deve completare l'operazione.

  Aggiornamento cluster
  -  400--il cluster è in stato di eliminazione. L'operazione asincrona è in corso. Prima di eseguire qualsiasi operazione di aggiornamento, il cluster deve completare l'operazione.
  -  400--KeyVaultProperties non è vuoto ma presenta un formato non valido. Vedere [aggiornamento dell'identificatore di chiave](#update-cluster-with-key-identifier-details).
  -  400--non è stato possibile convalidare la chiave in Key Vault. La causa potrebbe essere la mancanza di autorizzazioni o la chiave non esiste. Verificare di aver [impostato i criteri di accesso e chiave](#grant-key-vault-permissions) in Key Vault.
  -  400--la chiave non è reversibile. Key Vault deve essere impostato su soft-delete ed Purge-Protection. Vedere la [documentazione di Key Vault](../../key-vault/general/soft-delete-overview.md)
  -  400: non è possibile eseguire l'operazione ora. Attendere il completamento dell'operazione asincrona e riprovare.
  -  400--il cluster è in stato di eliminazione. Attendere il completamento dell'operazione asincrona e riprovare.

  Cluster Get:
    -  404: Impossibile trovare il cluster. è possibile che sia stato eliminato. Se si tenta di creare un cluster con tale nome e si verifica un conflitto, il cluster viene eliminato temporaneamente per 14 giorni. È possibile contattare il supporto tecnico per ripristinarlo o usare un altro nome per creare un nuovo cluster. 

  Eliminazione del cluster
    -  409: non è possibile eliminare un cluster in stato di provisioning. Attendere il completamento dell'operazione asincrona e riprovare.

  Collegamento area di lavoro:
  -  404--area di lavoro non trovata. L'area di lavoro specificata non esiste o è stata eliminata.
  -  409--collegamento dell'area di lavoro o operazione di scollegamento del collegamento nel processo.
  -  400--cluster non trovato, il cluster specificato non esiste o è stato eliminato. Se si tenta di creare un cluster con tale nome e si verifica un conflitto, il cluster viene eliminato temporaneamente per 14 giorni. È possibile contattare il supporto tecnico per recuperarlo.

  Scollegamento dell'area di lavoro:
  -  404--area di lavoro non trovata. L'area di lavoro specificata non esiste o è stata eliminata.
  -  409--collegamento dell'area di lavoro o operazione di scollegamento del collegamento nel processo.
