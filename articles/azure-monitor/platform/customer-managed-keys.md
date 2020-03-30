---
title: Configurazione della chiave gestita dal cliente di Azure MonitorAzure Monitor customer-managed key configuration
description: Informazioni e procedure per configurare la chiave gestita dal cliente (CMK) per crittografare i dati nelle aree di lavoro di Log Analytics usando una chiave dell'insieme di credenziali delle chiavi di Azure.Information and steps to configure Customer-Managed Key (CMK) to encrypt data in your Log Analytics workspaces using an Azure Key Vault key.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 03/26/2020
ms.openlocfilehash: 563a50d4589a83f710caa8ff2d2d95065909fc5f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384178"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Configurazione della chiave gestita dal cliente di Azure MonitorAzure Monitor customer-managed key configuration 

In questo articolo vengono fornite informazioni di base e la procedura per configurare le chiavi gestite dal cliente (CMK) per le aree di lavoro di Log Analytics e i componenti di Application Insights. Una volta configurati, tutti i dati inviati alle aree di lavoro o ai componenti vengono crittografati con la chiave dell'insieme di credenziali delle chiavi di Azure.Once configured, any data sent to your workspaces or components is encrypted with your Azure Key Vault key.

Si consiglia di [esaminare Limitazioni e vincoli riportati](#limitations-and-constraints) di seguito prima della configurazione.

## <a name="disclaimers"></a>Dichiarazioni di non responsabilità

- Azure Monitor CMK è una funzionalità di accesso anticipato e abilitata per le sottoscrizioni registrate.

- La distribuzione CMK descritta in questo articolo viene fornita in qualità di produzione e supportata come tale, anche se è una funzionalità di accesso anticipato.

- La funzionalità CMK viene fornita in un cluster di archivio dati dedicato, ovvero un cluster Azure Data Explorer (ADX) e adatto ai clienti che inviano 1 TB al giorno o più. 

- Il modello di determinazione dei prezzi CMK non è attualmente disponibile e non è trattato in questo articolo. Un modello di determinazione dei prezzi per il cluster ADX dedicato è previsto nel secondo trimestre dell'anno di calendario (CY) 2020 e si applicherà a tutte le distribuzioni CMK esistenti.

- In questo articolo viene descritta la configurazione CMK per le aree di lavoro di Log Analytics. Anche i componenti di CMK per Application Insights sono supportati utilizzando questo articolo, mentre le differenze sono elencate nell'Appendice.

> [!NOTE]
> Log Analytics e Application Insights usano la stessa piattaforma di archiviazione dati e lo stesso motore di query.
> Questi due archivi vengono riuniti tramite l'integrazione di Application Insights in Log Analytics per creare un singolo archivio di log unificato in Monitoraggio di Azure.We are bringing these two stores together via integration of Application Insights into Log Analytics to create a single unified logs store under Azure Monitor. Questa modifica è prevista per il secondo trimestre dell'anno civile 2020. Se non è necessario distribuire CMK per i dati di Application Insights entro tale data, è consigliabile attendere il completamento del consolidamento poiché tali distribuzioni verranno interrotte dal consolidamento e sarà necessario riconfigurare CMK dopo la migrazione a Log Area di lavoro Analisi. Il minimo di 1 TB al giorno si applica a livello di cluster e fino al completamento del consolidamento durante il secondo trimestre, Application Insights e Log Analytics richiedono cluster separati.

## <a name="customer-managed-key-cmk-overview"></a>Panoramica della chiave gestita dal cliente (CMK)Customer-managed key (CMK) overview

[La crittografia inattivi](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) è un requisito comune di privacy e sicurezza nelle organizzazioni. È possibile consentire ad Azure di gestire completamente la crittografia inattivi, mentre sono disponibili varie opzioni per gestire da vicino la crittografia o le chiavi di crittografia.

L'archivio dati di Monitoraggio di Azure garantisce che tutti i dati crittografati inattivi usando chiavi gestite da Azure archiviati in Archiviazione di Azure.The Azure Monitor data-store ensures that all data encrypted at rest using Azure-managed keys while stored in Azure Storage. Monitoraggio di Azure offre anche un'opzione per la crittografia dei dati usando la propria chiave archiviata in [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), a cui si accede tramite l'autenticazione [dell'identità gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) assegnata dal sistema. Questa chiave può essere [protetta da software o da hardware-HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
L'uso della crittografia da Monitoraggio di Azure è identico al funzionamento della crittografia di [Archiviazione di Azure.The](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) Azure Monitor use of encryption is identical to the way Azure Storage encryption operates.

La frequenza con cui archiviazione di Monitoraggio di Azure accede all'insieme di credenziali delle chiavi per le operazioni di wrapping e annullamento del wrapping è compresa tra 6 e 60 secondi.Archiviazione di Monitoraggio di Azure rispetta sempre le modifiche apportate alle autorizzazioni chiave entro un'ora.

I dati ingeriti negli ultimi 14 giorni vengono mantenuti anche nella hot-cache (supportato da SSD) per un'efficiente gestione delle query. Questi dati rimangono crittografati con le chiavi Microsoft indipendentemente dalla configurazione CMK, ma stiamo lavorando per avere l'SSD crittografato con CMK all'inizio del 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Funzionamento di CMK in Monitoraggio di AzureHow CMK works in Azure Monitor

Azure Monitor leverages system-assigned managed identity to grant access to your Azure Key Vault.L'identità gestita assegnata dal sistema può essere associata solo a una singola risorsa di Azure.System-assigned managed identity can only be associated with a single Azure resource. L'identità dell'archivio dati di Monitoraggio di Azure (cluster ADX) è supportata a livello di cluster e ciò impone che la funzionalità CMK venga fornita in un cluster ADX dedicato. Per supportare CMK in più aree di lavoro, una nuova risorsa log Analytics (*Cluster*) esegue come connessione di identità intermedia tra l'insieme di credenziali delle chiavi e le aree di lavoro di Log Analytics. Questo concetto è conforme al vincolo di identità assegnato dal sistema e l'identità viene mantenuta tra il cluster ADX e la risorsa *cluster* Log Analytics, mentre i dati di tutte le aree di lavoro associate sono protetti con la chiave dell'insieme di credenziali delle chiavi. L'archiviazione cluster ADX sottoposta\'usa l'identità gestita associata alla risorsa Cluster per autenticare e accedere all'insieme di credenziali delle chiavi di Azure tramite Azure Active Directory.The underlay ADX cluster storage uses the managed identity s associated with the *Cluster* resource to authenticate and access your Azure Key Vault via Azure Active Directory.

![CMK Panoramica](media/customer-managed-keys/cmk-overview.png)
1.  Key Vault del cliente.
2.  Risorsa *cluster* Log Analytics del cliente con identità gestita con autorizzazioni per L'insieme di credenziali delle chiavi: l'identità è supportata a livello di archivio dati (cluster ADX).
3.  Cluster ADX dedicato di Azure Monitor.Azure Monitor dedicated ADX cluster.
4.  Aree di lavoro del cliente associate alla risorsa cluster per la crittografia CMK.

## <a name="encryption-keys-management"></a>Gestione delle chiavi di crittografia

Esistono 3 tipi di chiavi coinvolte nella crittografia dei dati di archiviazione:There are 3 types of keys involved in Storage data encryption:

- **KEK** - Chiave di crittografia chiave in Key Vault (CMK)
- **AEK** - Chiave di crittografia account
- **DEK** - Chiave di crittografia dati

Sono applicabili le regole seguenti:

- L'account di archiviazione ADX genera una chiave di crittografia univoca per ogni account di archiviazione, noto come AEK.

- L'AEK viene utilizzato per derivare DEK, ovvero le chiavi utilizzate per crittografare ogni blocco di dati scritti su disco.

- Quando si configura la chiave nell'insieme di credenziali delle chiavi e vi si fa riferimento nella risorsa *Cluster,* Archiviazione di Azure invia richieste all'insieme di credenziali delle chiavi di Azure per eseguire il wrapping e l'annullamento del wrapping dell'aEK per eseguire operazioni di crittografia e decrittografia dei dati.

- La tua KEK non lascia mai il tuo Key Vault e, nel caso di una chiave HSM, non lascia mai l'hardware.

- Archiviazione di Azure usa l'identità gestita associata alla risorsa Cluster per l'autenticazione e l'accesso all'insieme di credenziali delle chiavi di Azure tramite Azure Active Directory.Azure Storage uses the managed identity that's associated with the *Cluster* resource to authenticate and access to Azure Key Vault via Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Procedura di provisioning CMK

Per la configurazione CMK di Application Insights, seguire il contenuto dell'Appendice per i passaggi 3 e 6.

1. Whitelisting degli abbonamenti -- questa opzione è necessaria per questa funzionalità di accesso anticipato
2. Creazione dell'insieme di chiavi di Azure e archiviazione della chiaveCreating Azure Key Vault and storing key
3. Creazione di una risorsa *clusterCreating a Cluster* resource
4. Provisioning dell'archivio dati (cluster ADX) di Azure MonitorAzure Data-Store (ADX cluster) provisioning
5. Concessione di autorizzazioni all'insieme di credenziali delle chiavi
6. Associazione delle aree di lavoro di Log Analytics

La procedura non è attualmente supportata nell'interfaccia utente e il processo di provisioning viene eseguito tramite l'API REST.

> [!IMPORTANT]
> Qualsiasi richiesta API deve includere un token di autorizzazione Bearer nell'intestazione della richiesta.

Ad esempio:

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Dove *eyJ0eXAiO....* rappresenta il token di autorizzazione completo. 

È possibile acquisire il token utilizzando uno dei metodi seguenti:You can acquire the token using one of these methods:

1. Utilizzare il metodo di [registrazione app.](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)

2. Nel portale di Azure
    1. Passare al portale di Azure in "developer tool (F12)
    1. Cercare la stringa di autorizzazione in "Intestazioni richieste" in una delle istanze "batch?api-version". Si presenta come: "autorizzazione:\>gettone Bearer \<". 
    1. Copiarlo e aggiungerlo alla chiamata API in base agli esempi seguenti.

3. Passare al sito della documentazione REST di Azure.Navigate to Azure REST documentation site. Premere "Prova" su qualsiasi API e copiare il token Bearer.

### <a name="subscription-whitelisting"></a>Whitelisting delle sottoscrizioni

La funzionalità CMK è una funzionalità di accesso anticipato. Le sottoscrizioni in cui si prevede di creare risorse cluster devono essere elencate in anticipo dal gruppo di prodotti Azure.The subscriptions where you plan to create *Cluster* resources must be whitehandby by the Azure product group. Utilizzare i contatti in Microsoft per fornire gli ID di sottoscrizione.

> [!IMPORTANT]
> La funzionalità CMK è regionale. L'insieme di credenziali delle chiavi di Azure, la risorsa *cluster* e le aree di lavoro di Log Analytics associate devono trovarsi nella stessa area, ma possono trovarsi in sottoscrizioni diverse.

### <a name="storing-encryption-key-kek"></a>Archiviazione della chiave di crittografia (KEK)

Creare o usare un insieme di credenziali delle chiavi di Azure che è già necessario generare o importare una chiave da usare per la crittografia dei dati. The Azure Key Vault must be configured as recoverable to protect your key and the access to your data in Azure Monitor. È possibile verificare questa configurazione in proprietà nell'insieme di credenziali delle chiavi, sia la protezione *eliminazione temporanea* che la *protezione Elimina.*

Queste impostazioni sono disponibili tramite l'interfaccia della riga di comando e PowerShell:These settings are available via CLI and PowerShell:
- [eliminazione temporanea](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Elimina le](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) protezioni contro la cancellazione forzata del segreto / vault anche dopo l'eliminazione temporanea

### <a name="create-cluster-resource"></a>Crea risorsa *cluster*

Questa risorsa viene utilizzata come connessione di identità intermedia tra l'insieme di credenziali delle chiavi e le aree di lavoro. Dopo aver ricevuto la conferma che le sottoscrizioni sono state inserite nella whitelist, creare una risorsa *Cluster* di Log Analytics nell'area in cui si trovano le aree di lavoro. Application Insights e Log Analytics richiedono tipi di risorse *cluster* separati. Il tipo della risorsa *Cluster* viene definito al momento della creazione impostando la proprietà "clusterType" su "LogAnalytics" o "ApplicationInsights". Il tipo di risorsa Cluster non può essere modificato dopo.

Per la configurazione CMK di Application Insights, seguire il contenuto dell'Appendice.

È necessario specificare il livello di prenotazione della capacità (sku) per la risorsa *cluster* durante la creazione di una risorsa *cluster.* Il livello di prenotazione della capacità può essere compreso tra 1000 e 2000 ed è possibile aggiornarlo nei passaggi di 100 in un secondo momento. Se è necessario un livello di prenotazione della capacità superiore a 2000, contattare il contatto Microsoft per abilitarlo. Questa proprietà non influisce attualmente sulla fatturazione: una volta introdotto il modello di determinazione dei prezzi per il cluster dedicato, la fatturazione verrà applicata a tutte le distribuzioni CMK esistenti.

**Crea**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterType": "LogAnalytics",
    },
  "location": "<region-name>",
}
```
L'identità viene assegnata alla risorsa *cluster* al momento della creazione.

**Response**

202 Accettato. Si tratta di una risposta standard di Resource Manager per le operazioni asincrone.

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Provisioning dell'archivio dati (cluster ADX) di Azure MonitorAzure Data-Store (ADX cluster) provisioning

Durante il periodo di accesso anticipato della funzionalità, il provisioning del cluster ADX viene eseguito manualmente dal team del prodotto una volta completati i passaggi precedenti. Usa il tuo canale Microsoft per il provisioning fornendo la risposta alle risorse *cluster.* 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Copiare e salvare la risposta poiché questi dettagli saranno necessari nei passaggi successivi

**Response**
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
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

>[!Important]
> Il provisioning del cluster ADX non è in grado di eseguire alcuni minuti. Il valore *provisioningState* indica il relativo stato, è *ProvisioningAccount* durante il provisioning e "Succeeded" al termine del provisioning.
> Il GUID "principalId" viene generato dal servizio di identità gestita per la risorsa *Cluster.*

### <a name="grant-key-vault-permissions"></a>Concedere le autorizzazioni dell'insieme di chiavi

Aggiornare l'insieme di credenziali delle chiavi con un nuovo criterio di accesso che concede le autorizzazioni alla risorsa *cluster.* Queste autorizzazioni vengono usate dall'archiviazione di Monitoraggio di Azure underlay per la crittografia dei dati. Aprire l'insieme di credenziali delle chiavi nel portale di Azure e fare clic su "Criteri di accesso", quindi su "Aggiungi criteri di accesso" per creare un criterio con queste impostazioni:Open your Key Vault in Azure portal and click "Access Policies"" then "' Add Access Policy" per creare un criterio con queste impostazioni:

- Autorizzazioni chiave: selezionare le autorizzazioni 'Get', 'Wrap Key' e 'Unwrap Key'.
- Selezionare principal: immettere il valore principal-id restituito nella risposta nel passaggio precedente.

![concedere autorizzazioni dell'insieme di credenziali delle chiavi](media/customer-managed-keys/grant-key-vault-permissions.png)

L'autorizzazione Ottieni è necessaria per verificare che l'insieme di credenziali delle chiavi sia configurato come recuperabile per proteggere la chiave e l'accesso ai dati di Monitoraggio di Azure.The *Get* permission is required to verify that your Key Vault is configured as recoverable to protect your key and the access to your Azure Monitor data.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Aggiornare la risorsa cluster con i dettagli dell'identificatore di chiaveUpdate Cluster resource with Key identifier details

Questo passaggio si applica per gli aggiornamenti della versione chiave iniziale e futura nell'insieme di credenziali delle chiavi. Informa Archiviazione di Monitoraggio di Azure sulla nuova versione della chiave.

Per aggiornare la risorsa *Cluster* con i dettagli *dell'identificatore* della chiave Vault, selezionare la versione corrente della chiave in Archiviazione delle chiavi di Azure per ottenere i dettagli dell'identificatore di chiave.

![Concedere le autorizzazioni dell'insieme di chiavi](media/customer-managed-keys/key-identifier-8bit.png)

Aggiornare la risorsa *cluster* KeyVaultProperties con i dettagli dell'identificatore di chiave.

**Aggiornamento**

>[!Warning]
> È necessario fornire un corpo completo nell'aggiornamento delle risorse *del cluster* che includa *identity,* *sku,* *KeyVaultProperties* e *location*. Se mancano i dettagli *keyVaultProperties,* l'identificatore di chiave verrà rimosso dalla risorsa *cluster* e verrà [cautala dalla revoca della chiave](#cmk-kek-revocation).

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
   },
   "location":"<region-name>"
}
```
"KeyVaultProperties" contiene i dettagli dell'identificatore della chiave Vault.

**Response**

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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Associazione dell'area di lavoro alla risorsa *cluster*

Per la configurazione CMK di Application Insights, seguire il contenuto dell'Appendice per questo passaggio.

> [!IMPORTANT]
> Questo passaggio deve essere eseguito solo dopo il provisioning del cluster ADX. Se si associano aree di lavoro e si inserino dati prima del provisioning, i dati ingeriti verranno eliminati e non saranno recuperabili.
> Per verificare che venga eseguito il provisioning del cluster ADX, eseguire *Risorsa cluster* Ottenere l'API REST e verificare che il valore *provisioningState* sia *Succeeded*.

Per eseguire questa operazione, è necessario disporre delle autorizzazioni di scrittura sia per l'area di lavoro che per la risorsa *Cluster,* che includono le azioni seguenti:You need to have 'write' permissions to both workspace and Cluster resource to perform this operation, which include these actions:

- Nell'area di lavoro: Microsoft.OperationalInsights/workspaces/write
- Risorsa *cluster:* Microsoft.OperationalInsights/clusters/write

**Associare un'area di lavoro**
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**Response**

```json
{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
}
```

Dopo l'associazione delle aree di lavoro, i dati ingeriti nelle aree di lavoro vengono archiviati crittografati con la chiave gestita.

### <a name="workspace-association-verification"></a>Verifica associazione all'area di lavoro
È possibile verificare se un'area di lavoro è associata a una risorsa *Custer* esaminando la risposta [Aree di lavoro - Ottieni.](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) L'area di lavoro associata avrà una proprietà 'clusterResourceId' con l'ID risorsa *cluster.*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview
```

**Response**

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

È possibile revocare l'accesso ai dati disabilitando la chiave o eliminando i criteri di accesso alle risorse *del cluster* nell'insieme di credenziali delle chiavi. Archiviazione di Monitoraggio azure rispetterà sempre le modifiche apportate alle autorizzazioni chiave entro un'ora, normalmente prima, e Archiviazione non sarà più disponibile. Tutti i dati resittati nelle aree di lavoro associate alla risorsa *cluster* vengono eliminati e le query avranno esito negativo. I dati ingeriti in precedenza rimangono inaccessibili in Archiviazione di Monitoraggio di Azure finché la chiave viene revocata e le aree di lavoro non vengono eliminate. I dati inaccessibili sono regolati dai criteri di conservazione dei dati e verranno eliminati quando viene raggiunta la conservazione.

L'archiviazione eseguirà periodicamente il polling dell'insieme di credenziali delle chiavi per tentare di annullare il wrapping della chiave di crittografia e, una volta acceduto, l'inserimento dei dati e la ripresa delle query entro 30 minuti.

## <a name="cmk-kek-rotation"></a>Rotazione CMK (KEK)

La rotazione di CMK richiede l'aggiornamento esplicito della risorsa Cluster con la nuova versione della chiave in Archiviazione delle chiavi di Azure.Rotation of CMK requires explicit update of the *Cluster* resource with the new key version in Azure Key Vault. Per aggiornare Monitoraggio di Azure con la nuova versione della chiave, seguire le istruzioni nel passaggio "Aggiornare la risorsa *cluster* con i dettagli dell'identificatore di chiave". Se si aggiorna la versione della chiave nell'insieme di credenziali delle chiavi e non si aggiornano i dettagli del nuovo identificatore di chiave nella risorsa *Cluster,* Archiviazione di Monitoraggio di Azure continuerà a usare la chiave precedente.
Tutti i dati sono accessibili dopo l'operazione di rotazione della chiave, inclusi i dati ingeriti prima della rotazione e dopo di essa, poiché tutti i dati rimangono crittografati dalla chiave di crittografia dell'account (AEK) mentre ora vengono crittografati dalla nuova versione della chiave di crittografia della chiave (KEK).

## <a name="limitations-and-constraints"></a>Limitazioni e vincoli

- La funzionalità CMK è supportata a livello di cluster ADX e richiede un cluster ADX di Monitoraggio di Azure dedicato

- Il numero massimo di risorse *del cluster* per sottoscrizione è limitato a 2

- *L'associazione* delle risorse cluster all'area di lavoro deve essere eseguita SOLO dopo aver ricevuto una conferma dal gruppo di prodotti che il provisioning del cluster ADX è stato evaso. I dati inviati prima di questo provisioning verranno eliminati e non saranno recuperabili.

- La crittografia CMK si applica ai dati appena ingeriti dopo la configurazione CMK. I dati che sono stati ingeriti prima della configurazione CMK, rimangono crittografati con la chiave Microsoft. È possibile eseguire query sui dati prima e dopo la configurazione CMK senza problemi.

- Una volta che l'area di lavoro è associata a una risorsa *Cluster,* non può essere de-associata dalla risorsa *Cluster,* poiché i dati sono crittografati con la chiave e non sono accessibili senza la chiave KEK in Azure Key Vault.

- L'insieme di credenziali delle chiavi di Azure deve essere configurato come recuperabile. Queste proprietà non sono abilitate per impostazione predefinita e devono essere configurate tramite l'interfaccia della riga di comando e PowerShell:These properties aren't enabled by default and should be configured using CLI and PowerShell:

  - [L'opzione Eliminazione temporanea](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve essere attivata
  - [La protezione dall'eliminazione](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) deve essere attivata per evitare la cancellazione forzata del segreto/vault anche dopo l'eliminazione temporanea

- Application Insights e Log Analytics richiedono risorse *cluster* separate. Il tipo della risorsa *Cluster* viene definito al momento della creazione impostando la proprietà "clusterType" su 'LogAnalytics' o 'ApplicationInsights'. Il tipo di risorsa *Cluster* non può essere modificato.

- *Lo* spostamento di risorse cluster a un altro gruppo di risorse o sottoscrizione non è attualmente supportato.

- L'insieme di credenziali delle chiavi di Azure, la risorsa *cluster* e le aree di lavoro associate devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse.

- L'associazione dell'area di lavoro alla risorsa *cluster* avrà esito negativo se è associata a un'altra risorsa *cluster*

## <a name="troubleshooting-and-management"></a>Risoluzione dei problemi e gestione

- Disponibilità dell'insieme di chiaviKey Vault availability
    - Nel normale funzionamento, Storage memorizza nella cache AEK per brevi periodi di tempo periodicamente torna all'insieme di credenziali delle chiavi per annullare il wrapping.
    
    - Errori di connessione temporanei. L'archiviazione gestisce gli errori temporanei (timeout, errori di connessione, problemi DNS) consentendo alle chiavi di rimanere nella cache per un breve periodo di tempo e questo supera eventuali piccoli blips nella disponibilità. Le funzionalità di query e inserimento continuano senza interruzioni.
    
    - Sito in tempo reale, l'indisponibilità di circa 30 minuti causerà l'account di archiviazione non sarà più disponibile. La funzionalità di query non è disponibile e i dati ingeriti vengono memorizzati nella cache per diverse ore utilizzando la chiave Microsoft per evitare la perdita di dati. Quando viene ripristinato l'accesso all'insieme di credenziali delle chiavi, la query diventa disponibile e i dati temporanei memorizzati nella cache vengono ingeriti nell'archivio dati e crittografati con CMK.

- Se si crea una risorsa *Cluster* e si specifica immediatamente KeyVaultProperties, l'operazione potrebbe non riuscire poiché i criteri di accesso non possono essere definiti fino a quando l'identità del sistema non viene assegnata alla risorsa *cluster.*

- Se si aggiorna la risorsa *Cluster* esistente con KeyVaultProperties e criteri di accesso chiave 'Get' non è presente nell'insieme di credenziali delle chiavi, l'operazione avrà esito negativo.

- Se si tenta di eliminare una risorsa *cluster* associata a un'area di lavoro, l'operazione di eliminazione avrà esito negativo.

- Ottenere tutte le risorse cluster per un gruppo di risorse:Get all *Cluster* resources for a resource group:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Response**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "properties": {
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- Ottenere tutte le risorse *del cluster* per una sottoscrizione:Get all Cluster resources for a subscription:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Response**
    
  La stessa risposta di *'Risorse cluster* per un gruppo di risorse', ma nell'ambito della sottoscrizione.
    
- Eliminare una risorsa *cluster:* viene eseguita un'operazione di eliminazione temporanea per consentire il ripristino della risorsa *cluster,* i dati e le aree di lavoro associate entro 14 giorni, indipendentemente dal fatto che l'eliminazione sia stata accidentale o intenzionale. Dopo il periodo di eliminazione temporanea, *la* risorsa cluster e i dati non sono recuperabili. Il nome della risorsa *cluster* rimane riservato durante il periodo di eliminazione temporanea e non è possibile creare un nuovo cluster con tale nome.

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **Response**

  200 - OK

- Ripristinare la risorsa *cluster* e i dati, durante il periodo di eliminazione temporanea, creare una risorsa *cluster* con lo stesso nome e nella stessa sottoscrizione, gruppo di risorse e area. Seguire il passaggio **Crea *risorsa cluster* ** per ripristinare la risorsa *cluster.*


## <a name="appendix"></a>Appendice

È supportata anche la chiave gestita dal cliente (CMK, Customer Managed Key) del cliente di Application Insights, anche se è necessario considerare la modifica seguente per pianificare la distribuzione di CMK per i componenti di Application Insight.

Log Analytics e Application Insights usano la stessa piattaforma di archiviazione dati e lo stesso motore di query. Stiamo portando questi due negozi insieme tramite l'integrazione di Application Insights in Log Analytics per fornire un singolo archivio di log unificato in Monitoraggio di Azure entro il secondo trimestre
2020. Questa modifica consentirà di inserire i dati di Application Insight nelle aree di lavoro di Log Analytics e di rendere possibili query, informazioni dettagliate e altri miglioramenti mentre la configurazione di CMK nell'area di lavoro verrà applicata anche ai dati di Application Insights.

> [!NOTE]
> Se non è necessario distribuire CMK per i dati di Application Insight prima dell'integrazione, è consigliabile attendere con Application Insights CMK poiché tali distribuzioni verranno interrotte dall'integrazione e sarà necessario riconfigurare CMK dopo la migrazione a Log Area di lavoro Analisi. Il minimo di 1 TB al giorno si applica a livello di cluster e fino al completamento del consolidamento durante il secondo trimestre, Application Insights e Log Analytics richiedono cluster separati.

## <a name="application-insights-cmk-configuration"></a>Configurazione CMK di Application Insights

La configurazione di Application Insights CMK è identica al processo illustrato in questo articolo, inclusi i vincoli e la risoluzione dei problemi, ad eccezione di questi passaggi:

- Creare una risorsa *clusterCreate a Cluster* resource
- Associare un componente a una risorsa *cluster*

Quando si configura CMK per Application Insights, utilizzare questa procedura anziché quelle elencate in precedenza.

### <a name="create-a-cluster-resource"></a>Creare una risorsa *clusterCreate a Cluster* resource

Questa risorsa viene utilizzata come connessione di identità intermedia tra key Vault e i componenti. Dopo aver ricevuto una conferma che le sottoscrizioni sono state inserite nella whitelist, creare una risorsa *Cluster* di Log Analytics nell'area in cui si trovano i componenti. Il tipo della risorsa *Cluster* viene definito al momento della creazione impostando la proprietà *clusterType* su *LogAnalytics*o *ApplicationInsights*. Deve essere *ApplicationInsights* per Application Insights CMK. L'impostazione *clusterType* non può essere modificata dopo la configurazione.

**Crea**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

**Response**

L'identità viene assegnata alla risorsa *cluster* al momento della creazione.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id" 
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```
"principio-id" è un GUID generato dal servizio di identità gestita.

> [!IMPORTANT]
> Copiare e mantenere il valore "principio-id" poiché sarà necessario nei passaggi successivi.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Associare un componente a una risorsa *cluster* usando componenti [- Crea o aggiorna](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) API

Per eseguire questa operazione, è necessario disporre delle autorizzazioni di scrittura sia per il componente che per la risorsa *Cluster,* che includono le azioni seguenti:You need to have 'write' permissions on both your component and Cluster resource to perform this operation, which include these actions:

- Nel componente: Microsoft.Insights/component/write
- Risorsa *cluster:* Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Questo passaggio deve essere eseguito solo dopo il provisioning del cluster ADX. Se si associano componenti e si inseriscono dati prima del provisioning, i dati ingeriti verranno eliminati e non saranno recuperabili.
> Per verificare che venga eseguito il provisioning del cluster ADX, eseguire *Risorsa cluster* Ottenere l'API REST e verificare che il valore *provisioningState* sia *Succeeded*.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

**Response**
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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

**Associare un componente**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
"clusterDefinitionId" è il valore "clusterId" fornito nella risposta dal passaggio precedente.
"tipo" dell'esempio è "web".

**Response**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
"clusterDefinitionId" è l'ID risorsa *cluster* associato a questo componente.

Dopo l'associazione, i dati inviati ai componenti vengono archiviati crittografati con la chiave gestita.
