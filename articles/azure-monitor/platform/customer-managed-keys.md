---
title: Chiave gestita dal cliente di monitoraggio di Azure
description: Informazioni e procedure per configurare la chiave gestita dal cliente (CMK) per crittografare i dati nelle aree di lavoro Log Analytics usando una chiave di Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 05/07/2020
ms.openlocfilehash: c78d8d603b6686d382ec7edcccc24d5dacc4745a
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982225"
---
# <a name="azure-monitor-customer-managed-key"></a>Chiave gestita dal cliente di monitoraggio di Azure 

Questo articolo fornisce informazioni generali e procedure per la configurazione delle chiavi gestite dal cliente (CMK) per le aree di lavoro Log Analytics. Una volta configurata, tutti i dati inviati alle aree di lavoro vengono crittografati con la chiave Azure Key Vault.

Prima della configurazione è consigliabile esaminare [limitazioni e vincoli](#limitations-and-constraints) .

## <a name="disclaimers"></a>Dichiarazioni di non responsabilità

- Monitoraggio di Azure CMK è una funzionalità di accesso anticipato e abilitata per le sottoscrizioni registrate.

- La distribuzione di CMK descritta in questo articolo viene fornita in qualità di produzione e supportata come tale, sebbene si tratta di una funzionalità di accesso anticipato.

- La funzionalità CMK viene distribuita in un cluster Log Analytics dedicato, ovvero un cluster fisico e un archivio dati adatto per i clienti che inviano 1 TB al giorno o più

- Il modello di determinazione prezzi CMK non è attualmente disponibile e non è trattato in questo articolo. Nel secondo trimestre dell'anno di calendario (CY) 2020 è previsto un modello di determinazione dei prezzi per il cluster di Log Analytics dedicato, che verrà applicato a tutte le distribuzioni di CMK esistenti.

## <a name="customer-managed-key-cmk-overview"></a>Panoramica della chiave gestita dal cliente (CMK)

La [crittografia di](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) dati inattivi è un requisito comune per la privacy e la sicurezza nelle organizzazioni. È possibile lasciare che Azure gestisca completamente la crittografia inattiva, mentre sono disponibili diverse opzioni per gestire le chiavi di crittografia o crittografia.

Archiviazione di monitoraggio di Azure garantisce che tutti i dati siano crittografati a riposo usando chiavi gestite da Azure, archiviate in archiviazione di Azure. Monitoraggio di Azure offre anche un'opzione per la crittografia dei dati usando la propria chiave archiviata nel [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), a cui è possibile accedere tramite l'autenticazione dell' [identità gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) assegnata dal sistema. Questa chiave può essere [software o hardware-HSM protetto](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
L'uso della crittografia da parte di monitoraggio di Azure è identico a quello usato per la [crittografia di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) .

La frequenza con cui l'archiviazione di monitoraggio di Azure accede Key Vault per le operazioni di wrapping e unwrap è compresa tra 6 e 60 secondi.Archiviazione di monitoraggio di Azure rispetta sempre le modifiche apportate alle autorizzazioni chiave entro un'ora.

I dati inseriti negli ultimi 14 giorni vengono anche mantenuti nella cache a caldo (con supporto SSD) per un'efficace operazione del motore di query. Questi dati rimangono crittografati con le chiavi di Microsoft indipendentemente dalla configurazione di CMK, ma il controllo sui dati SSD è conforme alla [revoca della chiave](#cmk-kek-revocation) e non è accessibile. Ci stiamo impegnando per la crittografia dei dati SSD con CMK nella seconda metà del 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Funzionamento di CMK in monitoraggio di Azure

Monitoraggio di Azure sfrutta l'identità gestita assegnata dal sistema per concedere l'accesso al Azure Key Vault.L'identità gestita assegnata dal sistema può essere associata solo a una singola risorsa di Azure. L'identità del cluster Log Analytics dedicato è supportata a livello di cluster e ciò impone che la funzionalità CMK sia distribuita in un cluster Log Analytics dedicato. Per supportare CMK su più aree di lavoro, una nuova risorsa *Cluster* log Analytics viene eseguita come connessione di identità intermedia tra la Key Vault e le aree di lavoro log Analytics. Questo concetto mantiene l'identità tra il cluster Log Analytics dedicato e la risorsa *cluster* log Analytics, mentre i dati delle aree di lavoro associate sono protetti con la chiave di Key Vault. Lo spazio di archiviazione dedicato Log Analytics cluster usa l'identità\'gestita associata alla risorsa *cluster* per l'autenticazione e l'accesso ai Azure Key Vault tramite Azure Active Directory.

![Panoramica di CMK](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Key Vault del cliente.
2.    Risorsa *cluster* log Analytics del cliente con identità gestita con autorizzazioni per Key Vault: l'identità è supportata a livello di cluster log Analytics dedicato.
3.    Cluster Log Analytics dedicato.
4.    Aree di lavoro del cliente associate alla risorsa *cluster* per la crittografia CMK.

## <a name="encryption-keys-operation"></a>Operazione sulle chiavi di crittografia

La crittografia dei dati di archiviazione include tre tipi di chiavi:

- Chiave di crittografia della chiave **KEK** (CMK)
- **AEK** -chiave di crittografia dell'account
- Chiave di crittografia dei dati di **decrittografia**

Sono applicabili le regole seguenti:

- Gli account di archiviazione del cluster Log Analytics dedicati generano una chiave di crittografia univoca per ogni account di archiviazione, noto come AEK.

- L'AEK viene usato per derivare chiavi DEK, ovvero le chiavi usate per crittografare ogni blocco di dati scritti su disco.

- Quando si configura la chiave in Key Vault e vi si fa riferimento nella risorsa *cluster* , il servizio di archiviazione di Azure invia le richieste al Azure Key Vault per eseguire il wrapping e annullare il wrapping dell'AEK per eseguire operazioni di crittografia e decrittografia dei dati.

- La KEK non lascia mai il Key Vault e, nel caso di una chiave HSM, non lascia mai l'hardware.

- Archiviazione di Azure usa l'identità gestita associata alla risorsa *cluster* per l'autenticazione e l'accesso ai Azure Key Vault tramite Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Procedura di provisioning CMK

1. Elenco elementi consentiti per la sottoscrizione: questa funzionalità è necessaria per questa funzionalità di accesso anticipato
2. Creazione di Azure Key Vault e archiviazione della chiave
3. Creazione di una risorsa *cluster*
5. Concessione delle autorizzazioni all'Key Vault
6. Associazione di aree di lavoro Log Analytics

La procedura non è attualmente supportata nell'interfaccia utente e il processo di provisioning viene eseguito tramite l'API REST.

> [!IMPORTANT]
> Qualsiasi richiesta API deve includere un token di autorizzazione di porta nell'intestazione della richiesta.

Ad esempio:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Dove *eyJ0eXAiO....* rappresenta il token di autorizzazione completo. 

È possibile acquisire il token usando uno di questi metodi:

1. Utilizzare [registrazioni app](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) metodo.
2. Nel portale di Azure
    1. Passa a portale di Azure in "strumento di sviluppo" (F12)
    1. Cercare la stringa di autorizzazione in "intestazioni richiesta" in una delle istanze "batch? API-Version". Ha un aspetto simile al seguente: "Authorization: Bearer eyJ0eXAiO....". 
    1. Copiarlo e aggiungerlo alla chiamata API per gli esempi riportati di seguito.
3. Passare al sito della documentazione REST di Azure. Premere "try it" su qualsiasi API e copiare il token di porta.

### <a name="asynchronous-operations-and-status-check"></a>Operazioni asincrone e controllo dello stato

Alcune operazioni in questa procedura di configurazione vengono eseguite in modo asincrono perché non possono essere completate rapidamente. La risposta per l'operazione asincrona restituisce inizialmente un codice di stato HTTP 200 (OK) e un'intestazione con la proprietà *Azure-AsyncOperation* quando accettata:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

È possibile controllare lo stato dell'operazione asincrona inviando una richiesta GET al valore dell'intestazione *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

La risposta contiene informazioni sull'operazione e il relativo *stato*. Può essere uno dei seguenti:

Operazione in corso
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
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

### <a name="subscription-whitelisting"></a>Elenco elementi consentiti per la sottoscrizione

La funzionalità CMK è una funzionalità di accesso anticipato. Le sottoscrizioni in cui si prevede di creare le risorse del *cluster* devono essere inserite nell'elenco elementi consentiti in anticipo dal gruppo di prodotti Azure. Usare i contatti in Microsoft per fornire gli ID delle sottoscrizioni.

> [!IMPORTANT]
> La funzionalità CMK è a livello di area. Il Azure Key Vault, la risorsa *cluster* e le aree di lavoro associate log Analytics devono trovarsi nella stessa area, ma possono trovarsi in sottoscrizioni diverse.

### <a name="storing-encryption-key-kek"></a>Archiviazione della chiave di crittografia (KEK)

Creare o usare un Azure Key Vault già necessario per generare o importare una chiave da usare per la crittografia dei dati. Il Azure Key Vault deve essere configurato come reversibile per proteggere la chiave e l'accesso ai dati in monitoraggio di Azure. È possibile verificare questa configurazione nelle proprietà della Key Vault, deve essere abilitata la protezione *eliminazione* temporanea e *ripulitura* .

![Impostazioni di protezione e eliminazione temporanea](media/customer-managed-keys/soft-purge-protection.png)

Queste impostazioni sono disponibili tramite l'interfaccia della riga di comando e PowerShell:
- [Eliminazione temporanea](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- Ripulire le protezioni di [protezione](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) da forzare l'eliminazione del segreto/insieme di credenziali anche dopo l'eliminazione temporanea

### <a name="create-cluster-resource"></a>Crea risorsa *cluster*

Questa risorsa viene usata come connessione di identità intermedia tra il Key Vault e le aree di lavoro del Log Analytics. Dopo aver ricevuto la conferma che le sottoscrizioni sono state inserite nell'elenco elementi consentiti, creare una risorsa *Cluster* log Analytics nell'area in cui si trovano le aree di lavoro.

Quando si crea una risorsa *cluster* , è necessario specificare il livello di *prenotazione della capacità* (SKU). Il livello di *prenotazione della capacità* può essere compreso tra 1.000 e 2.000 GB al giorno ed è possibile aggiornarlo nei passaggi di 100 in un secondo momento. Se è necessario un livello di prenotazione di capacità superiore a 2.000 GB al giorno, LAIngestionRate@microsoft.comcontattare Microsoft all'indirizzo. [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)
    
La proprietà *billingType* determina l'attribuzione della fatturazione per la risorsa *cluster* e i relativi dati:
- *cluster* (impostazione predefinita): la fatturazione viene attribuita alla sottoscrizione che ospita la risorsa *cluster*
- *aree di lavoro* : la fatturazione viene attribuita alle sottoscrizioni che ospitano le aree di lavoro in modo proporzionale 

> [!NOTE]
> Dopo aver creato la risorsa *cluster* , è possibile aggiornarla con *SKU*, *keyVaultProperties* o *billingType* usando la richiesta patch Rest.

**Crea**

Questa Gestione risorse richiesta è un'operazione asincrona.

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
L'identità viene assegnata alla risorsa *cluster* al momento della creazione.

**Risposta**

200 OK e intestazione.

Durante il completamento del provisioning del cluster dedicato di Log Analytics, è possibile controllare lo stato di provisioning in due modi:

1. Copiare il valore di URL di Azure-AsyncOperation dalla risposta e seguire la [Verifica dello stato delle operazioni asincrone](#asynchronous-operations-and-status-check).
2. Inviare una richiesta GET sulla risorsa *cluster* ed esaminare il valore *provisioningState* . Viene *ProvisioningAccount* durante il provisioning e il completamento è *riuscito* .


```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Copiare e salvare la risposta perché sono necessari i dettagli nei passaggi successivi.

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
    "clusterType": "LogAnalytics",
    "billingType": "cluster",
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

Il GUID "principalId" viene generato dal servizio di gestione delle identità gestito per la risorsa *cluster* .

### <a name="grant-key-vault-permissions"></a>Concedere autorizzazioni Key Vault

Aggiornare la Key Vault con i nuovi criteri di accesso che concedono le autorizzazioni per la risorsa *cluster* . Queste autorizzazioni vengono usate dall'archiviazione di monitoraggio di Azure sottostante per la crittografia dei dati. Aprire il Key Vault in portale di Azure e fare clic su "criteri di accesso" e quindi su "+ Aggiungi criteri di accesso" per creare un criterio con le impostazioni seguenti:

- Autorizzazioni per le chiavi: selezionare le autorizzazioni "Get", "wrap Key" e "Unwrap Key".
- Seleziona entità: immettere il valore dell'ID entità restituito nella risposta nel passaggio precedente.

![concedere autorizzazioni Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

L'autorizzazione *Get* è necessaria per verificare che il Key Vault sia configurato come reversibile per proteggere la chiave e l'accesso ai dati di monitoraggio di Azure.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Aggiornare la risorsa cluster con i dettagli dell'identificatore di chiave

Questo passaggio viene eseguito durante gli aggiornamenti delle versioni chiave iniziali e future nel Key Vault. Informa l'archiviazione di monitoraggio di Azure sulla versione della chiave da usare per la crittografia dei dati. Quando viene aggiornata, la nuova chiave viene usata per eseguire il wrapping e l'unwrapping alla chiave di archiviazione (AEK).

Per aggiornare la risorsa *cluster* con i dettagli dell' *identificatore di chiave* Key Vault, selezionare la versione corrente della chiave in Azure Key Vault per ottenere i dettagli dell'identificatore di chiave.

![Concedere autorizzazioni Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Aggiornare la risorsa *cluster* KeyVaultProperties con i dettagli dell'identificatore di chiave.

**Aggiornamento**

Questa Gestione risorse richiesta è un'operazione asincrona quando si aggiornano i dettagli dell'identificatore di chiave, mentre è sincrono durante l'aggiornamento del valore della capacità.

> [!Note]
> È possibile fornire il corpo parziale nella risorsa *cluster* per aggiornare uno *SKU*, *keyVaultProperties* o *billingType*.

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
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       }
   },
   "location":"<region-name>"
}
```
"KeyVaultProperties" contiene i dettagli dell'identificatore di chiave Key Vault.

**Risposta**

200 OK e intestazione.
Per completare la propagazione dell'identificatore di chiave, è sufficiente pochi minuti. È possibile controllare lo stato di aggiornamento in due modi:
1. Copiare il valore di URL di Azure-AsyncOperation dalla risposta e seguire la [Verifica dello stato delle operazioni asincrone](#asynchronous-operations-and-status-check).
2. Inviare una richiesta GET sulla risorsa *cluster* ed esaminare le proprietà *KeyVaultProperties* . I dettagli dell'identificatore di chiave aggiornati di recente devono restituire nella risposta.

Una risposta alla richiesta GET sulla risorsa *cluster* dovrebbe avere un aspetto simile al seguente quando l'aggiornamento dell'identificatore di chiave è completato:

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
      keyVaultUri: "https://key-vault-name.vault.azure.net",
      kyName: "key-name",
      keyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Associazione dell'area di lavoro alla risorsa *cluster*

Per eseguire questa operazione, è necessario disporre delle autorizzazioni ' Write ' sia per l'area di lavoro che per la risorsa *cluster* , incluse le azioni seguenti:

- Area di lavoro: Microsoft. OperationalInsights/Workspaces/Write
- Nella risorsa *cluster* : Microsoft. OperationalInsights/Clusters/Write

> [!IMPORTANT]
> Questo passaggio deve essere eseguito solo dopo il completamento del provisioning del cluster dedicato Log Analytics. Se si associano le aree di lavoro e si inseriscono dati prima del provisioning, i dati inseriti verranno eliminati e non saranno recuperabili.

**Associare un'area di lavoro**

Questa Gestione risorse richiesta è un'operazione asincrona.

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

I dati inseriti vengono archiviati crittografati con la chiave gestita dopo l'operazione di associazione, che può richiedere fino a 90 minuti per il completamento. È possibile controllare lo stato dell'associazione dell'area di lavoro in due modi:

1. Copiare il valore di URL di Azure-AsyncOperation dalla risposta e seguire la [Verifica dello stato delle operazioni asincrone](#asynchronous-operations-and-status-check).
2. Inviare un' [area di lavoro: ottenere](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) una richiesta e osservare la risposta. l'area di lavoro associata avrà una clusterResourceId in "funzionalità".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
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
    "retentionInDays": days,
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

## <a name="cmk-kek-revocation"></a>Revoca CMK (KEK)

È possibile revocare l'accesso ai dati disabilitando la chiave o eliminando i criteri di accesso alle risorse *cluster* nel Key Vault. L'archiviazione di monitoraggio di Azure rispetta sempre le modifiche delle autorizzazioni chiave entro un'ora, in genere prima e l'archiviazione non sarà più disponibile. Tutti i dati inseriti nelle aree di lavoro associate alla risorsa *cluster* vengono eliminati e le query avranno esito negativo. I dati inseriti in precedenza rimangono inaccessibili nell'archiviazione di monitoraggio di Azure, purché si tratti della risorsa *cluster* e le aree di lavoro non vengono eliminate. I dati inaccessibili sono regolati dai criteri di conservazione dei dati e verranno eliminati al raggiungimento della conservazione. 

I dati inseriti negli ultimi 14 giorni vengono anche mantenuti nella cache a caldo (con supporto SSD) per un'efficace operazione del motore di query. Questi dati rimangono crittografati con le chiavi di Microsoft indipendentemente dalla configurazione di CMK, ma vengono eliminati durante l'operazione di revoca della chiave e diventano inaccessibili.

Lo spazio di archiviazione esegue periodicamente il polling del Key Vault per tentare di annullare il wrapping della chiave di crittografia e, una volta eseguito l'accesso, l'inserimento e la ripresa dei dati entro 30

## <a name="cmk-kek-rotation"></a>Rotazione CMK (KEK)

La rotazione di CMK richiede un aggiornamento esplicito della risorsa *cluster* con la nuova versione della chiave in Azure Key Vault. Per aggiornare monitoraggio di Azure con la nuova versione della chiave, seguire le istruzioni riportate nel passaggio "aggiornare la risorsa *cluster* con i dettagli dell'identificatore di chiave". Se si aggiorna la versione della chiave in Key Vault e non si aggiornano i nuovi dettagli dell'identificatore di chiave nella risorsa *cluster* , l'archiviazione di monitoraggio di Azure continuerà a usare la chiave precedente.
Tutti i dati sono accessibili dopo l'operazione di rotazione delle chiavi, inclusi i dati inseriti prima della rotazione e dopo tale operazione, poiché tutti i dati restano crittografati con la chiave di crittografia dell'account (AEK) mentre AEK viene crittografato dalla nuova versione della chiave di crittografia della chiave (KEK).

## <a name="limitations-and-constraints"></a>Limitazioni e vincoli

- CMK è supportato in un cluster Log Analytics dedicato adatto per i clienti che inviano 1 TB al giorno o più.

- Il numero massimo di risorse *cluster* per area e sottoscrizione è 2

- È possibile associare un'area di lavoro alla risorsa *cluster* e quindi deassociarla quando CMK per i relativi dati non sono più necessari o altri motivi. Il numero di associazioni dell'area di lavoro che è possibile eseguire in un'area di lavoro in un periodo di 30 giorni è limitato a 2

- L'associazione dell'area di lavoro alla risorsa *cluster* deve essere eseguita solo dopo aver verificato che il provisioning del cluster log Analytics dedicato è stato completato. I dati inviati all'area di lavoro prima del completamento verranno eliminati e non saranno recuperabili.

- La crittografia CMK si applica ai dati appena inseriti dopo la configurazione di CMK. I dati inseriti prima della configurazione di CMK rimangono crittografati con la chiave Microsoft. È possibile eseguire facilmente query sui dati inseriti prima e dopo la configurazione di CMK.

- Il Azure Key Vault deve essere configurato come reversibile. Queste proprietà non sono abilitate per impostazione predefinita e devono essere configurate tramite CLI o PowerShell:

  - L' [eliminazione](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) temporanea deve essere attivata
  - La [protezione ripulisce](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) deve essere attivata per prevenire l'eliminazione forzata del segreto/insieme di credenziali anche dopo l'eliminazione temporanea.

- Lo spostamento di risorse *cluster* in un altro gruppo di risorse o sottoscrizione non è attualmente supportato.

- Il Azure Key Vault, la risorsa *cluster* e le aree di lavoro associate devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure ad), ma possono trovarsi in sottoscrizioni diverse.

- L'associazione dell'area di lavoro alla risorsa *cluster* non riuscirà se è associata a un'altra risorsa *cluster*


## <a name="management"></a>Gestione

- **Ottenere tutte le risorse *cluster* per un gruppo di risorse**

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
              keyVaultUri: "https://key-vault-name.vault.azure.net",
              keyName: "key-name",
              keyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- **Ottenere tutte le risorse *cluster* per una sottoscrizione**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Risposta**
    
  Stessa risposta di ' risorse*cluster* per un gruppo di risorse ', ma nell'ambito della sottoscrizione.

- **Aggiornare la *prenotazione di capacità* nella risorsa *cluster***

  Quando il volume di dati per le aree di lavoro associate cambia nel tempo e si desidera aggiornare il livello di prenotazione di capacità in modo appropriato. Seguire la [risorsa di aggiornamento *cluster* ](#update-cluster-resource-with-key-identifier-details) e fornire il nuovo valore di capacità. Può essere compreso tra 1.000 e 2.000 GB al giorno e nei passaggi di 100. Per un livello superiore a 2.000 GB al giorno, raggiungi il contatto Microsoft per abilitarlo. Si noti che non è necessario fornire il corpo completo della richiesta REST e includere lo SKU:

  ```json
  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ``` 

- **Aggiornare *billingType* nella risorsa *cluster***

  La proprietà *billingType* determina l'attribuzione della fatturazione per la risorsa *cluster* e i relativi dati:
  - *cluster* (impostazione predefinita): la fatturazione viene attribuita alla sottoscrizione che ospita la risorsa cluster
  - *aree di lavoro* : la fatturazione viene attribuita alle sottoscrizioni che ospitano le aree di lavoro in modo proporzionale
  
  Seguire la [risorsa Aggiorna *cluster* ](#update-cluster-resource-with-key-identifier-details) e specificare il nuovo valore billingType. Si noti che non è necessario fornire il corpo completo della richiesta REST e includere il *billingType*:

  ```json
  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Annulla associazione area di lavoro**

  Per eseguire questa operazione, sono necessarie le autorizzazioni ' Write ' per l'area di lavoro e la risorsa *cluster* . È possibile annullare l'associazione di un'area di lavoro dalla risorsa *cluster* in qualsiasi momento. I nuovi dati inseriti dopo l'operazione di deassociazione vengono archiviati nella risorsa di archiviazione Log Analytics e crittografati con la chiave Microsoft. È possibile eseguire query sui dati inseriti nell'area di lavoro prima e dopo la deassociazione senza interruzioni fino a quando la risorsa *cluster* viene sottoposta a provisioning e configurata con una chiave di Key Vault valida.

  Questa Gestione risorse richiesta è un'operazione asincrona.

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  ```

  **Risposta**

  200 OK e intestazione.

  I dati inseriti dopo l'operazione di deassociazione vengono archiviati nella risorsa di archiviazione Log Analytics. il completamento di questa operazione può richiedere 90 minuti. È possibile controllare lo stato di deassociazione dell'area di lavoro in due modi:

  1. Copiare il valore di URL di Azure-AsyncOperation dalla risposta e seguire la [Verifica dello stato delle operazioni asincrone](#asynchronous-operations-and-status-check).
  2. Inviare un' [area di lavoro: ottenere](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) la richiesta e osservare la risposta, l'area di lavoro deassociata non avrà il *clusterResourceId* in *funzionalità*.


- **Eliminare la risorsa *cluster***

  Per eseguire questa operazione, sono necessarie le autorizzazioni ' Write ' per la risorsa *cluster* . Viene eseguita un'operazione di eliminazione temporanea per consentire il ripristino della risorsa *cluster* , inclusi i dati entro 14 giorni, se l'eliminazione è stata accidentale o intenzionale. Il nome della risorsa *cluster* rimane riservato durante il periodo di eliminazione temporanea e non è possibile creare un nuovo cluster con tale nome. Dopo il periodo di eliminazione temporanea, viene rilasciato il nome della risorsa *cluster* , i dati e la risorsa *cluster* vengono eliminati definitivamente e non sono recuperabili. Tutte le aree di lavoro associate vengono deassociate dalla risorsa *cluster* durante l'operazione di eliminazione. I nuovi dati inseriti vengono archiviati nella risorsa di archiviazione Log Analytics e crittografati con la chiave Microsoft. L'operazione di deassociazione dell'area di lavoro è asincrona e può richiedere fino a 90 minuti.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Risposta**

  200 - OK

- **Ripristinare la risorsa *cluster* e i dati** 
  
  Una risorsa *cluster* eliminata negli ultimi 14 giorni è in stato di eliminazione temporanea e può essere ripristinata. Questa operazione viene eseguita manualmente dal gruppo di prodotti. Usare il canale Microsoft per le richieste di ripristino.


## <a name="troubleshooting"></a>Risoluzione dei problemi
- Comportamento con Key Vault disponibilità
  - Durante il normale funzionamento--archiviazione memorizza nella cache l'AEK per brevi periodi di tempo e torna Key Vault per annullare il wrapping periodicamente.
    
  - Errori di connessione temporanei: l'archiviazione gestisce gli errori temporanei (timeout, errori di connessione, problemi relativi a DNS) consentendo alle chiavi di rimanere nella cache per un breve periodo di tempo e questo supera i piccoli tempi di disponibilità. Le funzionalità di query e inserimento continuano senza interruzioni.
    
  - Sito Live: la mancata disponibilità di circa 30 minuti comporterà la mancata disponibilità dell'account di archiviazione. La funzionalità di query non è disponibile e i dati inseriti vengono memorizzati nella cache per diverse ore usando la chiave Microsoft per evitare la perdita di dati. Quando viene ripristinato l'accesso Key Vault, la query diventa disponibile e i dati temporanei memorizzati nella cache vengono inseriti nell'archivio dati e crittografati con CMK.

- Se si crea una risorsa *cluster* e si specifica immediatamente il KeyVaultProperties, l'operazione potrebbe non riuscire perché i criteri di accesso non possono essere definiti fino a quando non viene assegnata l'identità del sistema alla risorsa *cluster* .

- Se si aggiorna la risorsa *cluster* esistente con KeyVaultProperties e i criteri di accesso alla chiave ' Get ' mancano nell'Key Vault, l'operazione avrà esito negativo.

- Se si tenta di eliminare una risorsa *cluster* associata a un'area di lavoro, l'operazione di eliminazione avrà esito negativo.

- Se si verifica un errore di conflitto durante la creazione di una risorsa *cluster* , è possibile che la risorsa *cluster* sia stata eliminata negli ultimi 14 giorni ed è in un periodo di eliminazione temporanea. Il nome della risorsa *cluster* rimane riservato durante il periodo di eliminazione temporanea e non è possibile creare un nuovo cluster con tale nome. Il nome viene rilasciato dopo il periodo di eliminazione temporanea quando la risorsa *cluster* viene definitivamente eliminata.

- Se si aggiorna la risorsa *cluster* mentre è in corso un'operazione, l'operazione avrà esito negativo.
