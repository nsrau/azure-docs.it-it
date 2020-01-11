---
title: Configurazione della chiave gestita dal cliente di monitoraggio di Azure
description: Informazioni e procedure per configurare la chiave gestita dal cliente (CMK) per crittografare i dati nelle aree di lavoro Log Analytics usando una chiave di Azure Key Vault.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/07/2020
ms.openlocfilehash: 7a3749f61e6e656f750059ee76881a2e3f3b7912
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75865038"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Configurazione della chiave gestita dal cliente di monitoraggio di Azure 

Questo articolo fornisce informazioni generali e procedure per configurare le chiavi gestite dal cliente (CMK) e le aree di lavoro Log Analytics e i componenti Application Insights. Una volta configurata, tutti i dati inviati alle aree di lavoro vengono crittografati con la chiave Azure Key Vault.

Prima della configurazione è consigliabile esaminare [limitazioni e vincoli](#Limitations and constraints) .

## <a name="disclaimers"></a>Dichiarazioni di non responsabilità

- Monitoraggio di Azure CMK è una funzionalità di accesso anticipato e abilitata per le sottoscrizioni registrate

- La distribuzione di CMK descritta in questo articolo viene fornita in qualità di produzione e supportata come tale, sebbene si tratta di una funzionalità di accesso anticipato.

- La funzionalità CMK viene distribuita in un cluster di archiviazione dati dedicato, un cluster Azure Esplora dati (ADX) e adatto per i clienti che inviano 1 TB al giorno. 

- Il modello di determinazione prezzi CMK non è attualmente disponibile e non è trattato in questo articolo. Nel secondo trimestre dell'anno di calendario (CY) 2020 si prevede un modello di determinazione dei prezzi per il cluster di archiviazione dati dedicato, che verrà applicato a tutte le distribuzioni di CMK esistenti.

- Questo articolo descrive la configurazione di CMK per le aree di lavoro Log Analytics. CMK per i componenti Application Insights è supportato anche in questo articolo, mentre le differenze sono elencate nell'appendice.

> [!NOTE]
> Log Analytics e Application Insights utilizzano la stessa piattaforma di archivio dati e il motore di query.
> Questi due archivi vengono raggruppati insieme tramite l'integrazione di Application Insights in Log Analytics per creare un singolo archivio di log unificato in monitoraggio di Azure. Questa modifica è prevista per il secondo trimestre dell'anno di calendario 2020. Se non è necessario distribuire CMK per i dati di Application Insights, è consigliabile attendere il completamento del consolidamento poiché tali distribuzioni verranno interrotte dal consolidamento e sarà necessario riconfigurare CMK dopo la migrazione a log Area di lavoro Analytics. Il valore minimo di 1 TB al giorno si applica a livello di cluster e fino al completamento del consolidamento durante il secondo trimestre Application Insights e Log Analytics richiedono cluster distinti.

## <a name="customer-managed-key-cmk-overview"></a>Panoramica della chiave gestita dal cliente (CMK)

La [crittografia di](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) dati inattivi è un requisito comune per la privacy e la sicurezza nelle organizzazioni. È possibile lasciare che Azure gestisca completamente la crittografia inattiva, mentre sono disponibili diverse opzioni per gestire le chiavi di crittografia o crittografia.

Archivio dati di monitoraggio di Azure garantisce che tutti i dati siano crittografati a riposo usando chiavi gestite da Azure, archiviate in archiviazione di Azure. Monitoraggio di Azure offre anche un'opzione per la crittografia dei dati usando la propria chiave archiviata in insiemi di credenziali delle [chiavi di Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview), a cui è possibile accedere tramite l'autenticazione dell' [identità gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) assegnata dal sistema. Questa chiave può essere [software o hardware-HSM protetto](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
L'uso della crittografia da parte di monitoraggio di Azure è identico a quello usato per la [crittografia di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) .

La frequenza con cui l'archiviazione di monitoraggio di Azure accede Key Vault per le operazioni di wrapping e unwrap è compresa tra 6 e 60 secondi. Archiviazione di monitoraggio di Azure  
rispetta sempre le modifiche apportate alle autorizzazioni chiave entro un'ora.

## <a name="how-cmk-works-in-azure-monitor"></a>Funzionamento di CMK in monitoraggio di Azure

Monitoraggio di Azure sfrutta l'identità gestita assegnata dal sistema per concedere l'accesso al Azure Key Vault. L'identità gestita assegnata dal sistema può essere associata solo a una singola risorsa di Azure. L'identità del cluster di archiviazione dati (ADX) di monitoraggio di Azure è supportata a livello di cluster e questo impone che la funzionalità CMK venga distribuita in un cluster ADX dedicato. Per supportare CMK su più aree di lavoro, una nuova risorsa Log Analytics (*cluster*) viene eseguita come connessione di identità intermedia tra la Key Vault e le aree di lavoro log Analytics. Questo concetto è conforme al vincolo di identità assegnato dal sistema e l'identità viene mantenuta tra il cluster ADX e la risorsa *cluster* log Analytics *,* mentre i dati di tutte le aree di lavoro associate sono protetti con la chiave di Key Vault. L'archiviazione del cluster ADX di sottosistema usa l'identità gestita che\'s associata alla risorsa *cluster* per l'autenticazione e l'accesso al Azure Key Vault tramite Azure Active Directory.

![Panoramica di CMK](media/customer-managed-keys/cmk-overview-8bit.png)

## <a name="encryption-keys-management"></a>Gestione delle chiavi di crittografia

La crittografia dei dati di archiviazione include tre tipi di chiavi:

- Chiave di crittografia **KEK** -key in Key Vault (CMK)
- **AEK** -chiave di crittografia dell'account
- Chiave di crittografia dei dati di **decrittografia**

Sono applicabili le regole seguenti:

- L'account di archiviazione ADX genera una chiave di crittografia univoca per ogni account di archiviazione, noto come AEK

- L'AEK viene usato per derivare chiavi DEK, ovvero le chiavi usate per crittografare ogni blocco di dati scritti su disco.

- Quando si configura la chiave in Key Vault e vi si fa riferimento nella risorsa *cluster* , archiviazione di Azure esegue il wrapping dell'AEK con la KEK in Azure Key Vault.

- La KEK non lascia mai il Key Vault e, nel caso di una chiave HSM, non lascia mai l'hardware.

- Archiviazione di Azure usa l'identità gestita associata alla risorsa *cluster* per l'autenticazione e l'accesso ai Azure Key Vault tramite Azure Active Directory.

- Per le operazioni di lettura/scrittura, archiviazione di Azure invia richieste a Azure Key Vault per eseguire il wrapping e annullare il wrapping dell'AEK per eseguire operazioni di crittografia e decrittografia.

## <a name="cmk-provisioning-procedure"></a>Procedura di provisioning CMK

Il processo di provisioning include i passaggi seguenti:

1. Elenco elementi consentiti per la sottoscrizione: questa funzionalità è necessaria per questa funzionalità di accesso anticipato
2. Creazione di Azure Key Vault e archiviazione della chiave
3. Creare una risorsa *cluster*
4. Concedere le autorizzazioni all'Key Vault
5. Provisioning di archivio dati (ADX) di monitoraggio di Azure
6. Associazione di aree di lavoro Log Analytics

La procedura non è attualmente supportata nell'interfaccia utente e il processo di provisioning viene eseguito tramite l'API REST.

> [!IMPORTANT]
> Qualsiasi richiesta API deve includere un token di autorizzazione di porta nell'intestazione della richiesta.

Ad esempio:

```rst
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2015-11-01-preview]
  authorization: Bearer eyJ0eXAiO....
```

dove *eyJ0eXAiO....* rappresenta il token di autorizzazione completo. 

È possibile acquisire il token usando uno di questi metodi:

1. Utilizzare [registrazioni app](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) metodo.

2. Nel portale di Azure
    1. Passare a portale di Azure in "strumento per sviluppatori (F12)
    1. Cercare la stringa di autorizzazione in "intestazioni richiesta" in una delle istanze "batch? API-Version". Ha un aspetto simile al seguente: "Authorization: Bearer \<token\>". 
    1. Copiarlo e aggiungerlo alla chiamata API per gli esempi riportati di seguito.

3. Passare al sito della documentazione REST di Azure. Premere "try it" su qualsiasi API e copiare il token di porta.

### <a name="subscription-whitelisting"></a>Elenco elementi consentiti per la sottoscrizione

La funzionalità CMK è una funzionalità di accesso anticipato. Le sottoscrizioni in cui si prevede di creare le risorse del *cluster* devono essere inserite nell'elenco elementi consentiti in anticipo dal gruppo di prodotti Azure. Usare i contatti in Microsoft per fornire gli ID delle sottoscrizioni.

> [!WARNING]
> La funzionalità CMK è a livello di area. Il Azure Key Vault, l'account di archiviazione, la risorsa *cluster* e le aree di lavoro associate log Analytics devono trovarsi nella stessa area, ma possono trovarsi in sottoscrizioni diverse.

### <a name="storing-encryption-key-kek"></a>Archiviazione della chiave di crittografia (KEK)

Creare una risorsa Azure Key Vault, quindi generare o importare una chiave da usare per la crittografia dei dati.

Il Azure Key Vault deve essere configurato come reversibile per proteggere la chiave e l'accesso ai dati di monitoraggio di Azure.

Per [attivare le opzioni di ripristino](https://docs.microsoft.com/azure/key-vault/key-vault-best-practices#turn-on-recovery-options):
- L' [eliminazione](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) temporanea deve essere attivata
- La protezione ripulisce deve essere attivata per prevenire l'eliminazione forzata del segreto/insieme di credenziali anche dopo l'eliminazione temporanea

### <a name="create-cluster-resource"></a>Crea risorsa *cluster*

Questa risorsa viene usata come connessione di identità intermedia tra il Key Vault e le aree di lavoro. Solo dopo aver ricevuto la conferma che le sottoscrizioni sono state inserite nell'elenco elementi consentiti, creare una risorsa *Cluster* log Analytics nell'area in cui si trovano le aree di lavoro.

**Creare**

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "region-name",
   "properties": {
      "clusterType": "LogAnalytics"
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```

**Risposta**

L'identità viene assegnata al *cluster* al momento della creazione.

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id" //A GUID that was generated by the managed identity service
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"   //A GUID that Log Analytics generates for the cluster
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}

```

Se si desidera eliminare la risorsa *cluster* per qualsiasi motivo (ad esempio, crearla con un nome diverso), usare questa chiamata API:

```
DELETE
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
```

### <a name="grant-key-vault-permissions"></a>Concedere autorizzazioni Key Vault

Aggiornare il Key Vault e aggiungere i criteri di accesso con le autorizzazioni ' Get ',' wrap Key ' è Unwrap Key ' per l'ID risorsa *cluster* o il nome della risorsa *cluster* . Queste autorizzazioni verranno propagate alla risorsa di archiviazione di monitoraggio di Azure.

![concedere autorizzazioni Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

L'autorizzazione *Get* è necessaria per verificare che il Key Vault sia configurato come reversibile per proteggere la chiave e l'accesso ai dati di monitoraggio di Azure.

Sono necessari alcuni minuti prima che la risorsa *cluster* venga propagata in Azure Resource Manager. Quando si configurano i criteri di accesso immediatamente dopo la creazione della risorsa *cluster* , potrebbe verificarsi un errore temporaneo. In questo caso, riprovare tra qualche minuto.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Aggiornare la risorsa cluster con i dettagli dell'identificatore di chiave

Quando si crea una nuova versione di una chiave, è necessario aggiornare la risorsa cluster con Azure Key Vault dettagli dell'identificatore di chiave, per consentire all'archiviazione di monitoraggio di Azure di usare la nuova versione. Per ottenere l'identificatore della chiave, selezionare la versione corrente della chiave in Azure Key Vault:

![Concedere autorizzazioni Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Aggiornare la risorsa *cluster* KeyVaultProperties con i dettagli dell'identificatore di chiave.

**Aggiornamento**

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
       "KeyVaultProperties": { //Key Vault key identifier
            KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
            KeyName: {key-name},
            KeyVersion: {current-version}
            },
   },
   "location":"region-name",
   "identity": { 
        "type": "systemAssigned" 
        }
}
```

**Risposta**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"    //A GUID that was generated by the managed identity service
  },
  "properties": {
       "KeyVaultProperties": {     // Key Vault key identifier
            KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
            KeyName: {key-name},
            KeyVersion: {current-version}
            },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name" //Example: Switzerland North
}
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Provisioning di archivio dati di monitoraggio di Azure (cluster ADX)

Durante il periodo di accesso iniziale della funzionalità, il provisioning del cluster ADX viene eseguito manualmente dal team del prodotto una volta completati i passaggi precedenti. Usare il canale con Microsoft per fornire i dettagli seguenti:

1. Conferma che i passaggi precedenti sono stati completati

2. L'ID di risorsa *cluster* ottenuto nella risposta è simile al seguente:

```
"id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
```

L'ID risorsa *cluster* può essere recuperato in qualsiasi momento tramite una chiamata all'API Get.

**Leggere l'ID risorsa *cluster***

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

**Risposta**
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-Id"
  },
  "properties": {
       "KeyVaultProperties": { // Key Vault key identifier
            KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
            KeyName: {key-name},
            KeyVersion: {current-version}
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

> [!NOTE]
> Questo passaggio deve essere eseguito **solo** dopo aver ricevuto la conferma dal gruppo di prodotti tramite il canale Microsoft che è stato completato il **provisioning di Azure Monitor Data-Store (cluster ADX)** . Se si associano le aree di lavoro e si inseriscono i dati prima del **provisioning**, i dati verranno eliminati e non saranno recuperabili.

**Associare un'area di lavoro a una risorsa *cluster***

```json
PUT https://management.azure.com.resources.windows-int.net/Customer.svc/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name} 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "source": "Azure",
    "customerId": {workspace-id}, //Available in Azure portal under Log Analytics workspace Overview section
    "features": {
      "clusterDefinitionId": "cluster-id" //The id of the Cluster resource
    }
  },
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name}",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

**Risposta**

```
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-id",
    "retentionInDays": value,
    "features": {
      "legacy": value,
      "searchVersion": value,
      "clusterDefinitionId": "cluster-id" //The id of the Cluster resource
    },
    "workspaceCapping": {
      "dailyQuotaGb": value,
      "quotaNextResetTime": "timeStamp",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name}",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}

```

Dopo l'associazione, i dati inviati alle aree di lavoro vengono archiviati crittografati con la chiave gestita.

## <a name="cmk-kek-revocation"></a>Revoca CMK (KEK)

L'archiviazione di monitoraggio di Azure rispetta sempre le modifiche delle autorizzazioni chiave entro un'ora, in genere prima e l'archiviazione non sarà più disponibile. tutti i dati inseriti nelle aree di lavoro associate alla risorsa *cluster* verranno eliminati e le query avranno esito negativo. I dati inseriti in precedenza rimangono inaccessibili nell'archiviazione di monitoraggio di Azure finché la chiave viene revocata e le aree di lavoro non vengono eliminate. I dati inaccessibili sono regolati dai criteri di conservazione dei dati e verranno eliminati al raggiungimento della conservazione.

Lo spazio di archiviazione esegue periodicamente il polling del Key Vault per tentare di annullare il wrapping della chiave di crittografia e, una volta eseguito l'accesso, l'inserimento e la ripresa dei dati entro 30

## <a name="cmk-kek-rotation"></a>Rotazione CMK (KEK)

La rotazione di CMK richiede un aggiornamento esplicito della risorsa cluster con la nuova versione della chiave Azure Key Vault. Per aggiornare monitoraggio di Azure con la nuova versione della chiave, seguire le istruzioni riportate nel passaggio "aggiornare la risorsa *cluster* con i dettagli dell'identificatore di chiave".

-   Se si ruota la chiave in Key Vault e non si aggiorna la nuova versione in monitoraggio di Azure subito dopo, la chiave non sarà accessibile dall'archiviazione di monitoraggio di Azure.

## <a name="limitations-and-constraints"></a>Limitazioni e vincoli

- La funzionalità CMK è supportata a livello di cluster ADX e richiede un cluster ADX di monitoraggio di Azure dedicato

- Il numero massimo di risorse *cluster* per ogni sottoscrizione è limitato a 5

- L'associazione di risorse *cluster* all'area di lavoro deve essere eseguita solo dopo aver ricevuto una conferma dal gruppo di prodotti in cui è stato completato il provisioning del cluster ADX. I dati inviati prima del provisioning verranno eliminati e non saranno recuperabili.

- La crittografia CMK si applica ai dati appena inseriti dopo la configurazione di CMK. I dati inseriti prima della configurazione di CMK sono rimasti crittografati con la chiave Microsoft. È possibile eseguire query sui dati prima e dopo la configurazione in modo uniforme.

- La funzionalità CMK è a livello di area: il Azure Key Vault, la risorsa *cluster* e le aree di lavoro associate devono trovarsi nella stessa area, ma possono trovarsi in sottoscrizioni diverse.

- Una volta che l'area di lavoro è associata a una risorsa *cluster* , non può essere deassociata dalla risorsa *cluster* , perché i dati vengono crittografati con la chiave e non sono accessibili senza la KEK in Azure Key Vault.

- Il Azure Key Vault deve essere configurato come reversibile. Queste proprietà non sono abilitate per impostazione predefinita:

  - L' [eliminazione](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) temporanea è attivata
  - ' Non ripulire ' è attivato per prevenire l'eliminazione forzata del segreto/insieme di credenziali anche dopo l'eliminazione temporanea

- Lo spostamento di risorse *cluster* in un altro gruppo di risorse o sottoscrizione non è attualmente supportato.

- L'associazione dell'area di lavoro alla risorsa *cluster* non riuscirà se la risorsa *cluster* si trova in un tenant diverso.

-   L'associazione dell'area di lavoro alla risorsa *cluster* non riuscirà se è associata a un'altra risorsa *cluster*

## <a name="troubleshooting-and-management"></a>Risoluzione dei problemi e gestione

- Disponibilità Key Vault
    - In condizioni normali, l'archiviazione memorizza nella cache l'AEK per brevi periodi di tempo che si riportano periodicamente a Key Vault per annullare il wrapping.
    
    - Errori di connessione temporanei. L'archiviazione gestisce gli errori temporanei (timeout, errori di connessione, problemi DNS) consentendo alle chiavi di rimanere nella cache per un breve periodo di tempo e questo supera le piccole quantità di messaggi di disponibilità. la funzionalità query di disponibilità è disponibile senza interruzioni.
            -L'inserimento continua senza interruzioni
    
    - La mancata disponibilità di un sito Live di circa 30 minuti comporterà l'indisponibilità dell'account di archiviazione.
            -   funzionalità di **query** non è **disponibile-inserimento: i** dati vengono memorizzati nella cache per diverse ore usando la chiave Microsoft per evitare la perdita di dati. Quando viene ripristinato l'accesso Key Vault, la query diventa disponibile e i dati temporanei memorizzati nella cache vengono inseriti nell'archivio dati e crittografati con CMK.

- Se si crea una risorsa *cluster* e si specifica immediatamente il KeyVaultProperties, l'operazione potrebbe non riuscire perché i criteri di accesso non possono essere definiti fino a quando non viene assegnata l'identità del sistema alla risorsa *cluster* .

- Se si aggiorna la risorsa *cluster* esistente con KeyVaultProperties e i criteri di accesso alla chiave ' Get ' mancano nell'Key Vault, l'operazione avrà esito negativo.

- Se si tenta di eliminare una risorsa *cluster* associata a un'area di lavoro, l'operazione di eliminazione avrà esito negativo.

- Ottenere tutti i cluster per un gruppo di risorse:

    ```
    GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
    ```
    
    *Risposta*

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
             "KeyVaultProperties": { // Key Vault key identifier
                KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
                KeyName: {key-name},
                KeyVersion: {current-version}
                },
            "provisioningState": "Succeeded",
            "clusterType": "LogAnalytics", 
            "clusterId": "cluster-id"
          },
          "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name}",
          "name": "cluster-name",
          "type": "Microsoft.OperationalInsights/clusters",
          "location": "region-name"
        }
      ]
    }
    ```

- Per ottenere tutti i cluster per una sottoscrizione

    ```
    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
    ```
    
    *Risposta*
    
    Uguale alla risposta da per ' tutti i cluster per un gruppo di risorse ', ma nell'ambito della sottoscrizione.
    
- Eliminare una risorsa *cluster* :

> È necessario eliminare tutte le aree di lavoro associate prima di poter eliminare la risorsa *cluster* :
>
> DELETE https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
>

Risposta

200 - OK

## <a name="appendix"></a>Appendice

Questo articolo si applica anche a Application Insights chiave gestita dal cliente (CMK), ma è opportuno prendere in considerazione la modifica imminente che consente di pianificare la distribuzione di CMK per i componenti di Application Insight.

Log Analytics e Application Insights usano la stessa piattaforma di archivio dati e il motore di query: questi due archivi vengono uniti tramite l'integrazione di Application Insights in Log Analytics per fornire un singolo archivio di log unificato in monitoraggio di Azure da parte del secondo trimestre di
2020. Questa modifica consente di visualizzare i dati dell'applicazione in Log Analytics aree di lavoro ed eseguire query, informazioni dettagliate e altri miglioramenti possibili durante la configurazione di CMK nell'area di lavoro, anche per i dati Application Insights.

> [!NOTE]
> Se non è necessario distribuire CMK sui dati dell'applicazione Insight prima del secondo trimestre di CY 2020, è consigliabile attendere il completamento del consolidamento poiché tali distribuzioni verranno interrotte dal consolidamento e sarà necessario riconfigurare CMK nel area di lavoro dopo l'it.

## <a name="application-insights-cmk-configuration"></a>Configurazione di Application Insights CMK

La configurazione di Application Insights CMK è identica alla procedura illustrata in questo articolo, inclusi i vincoli e la risoluzione dei problemi, eccetto i passaggi seguenti:

- Crea risorsa *cluster*

- Associare un componente a una risorsa *cluster*

Quando si configura CMK per Application Insights, usare questi passaggi anziché quelli elencati in precedenza.

### <a name="create-a-cluster-resource"></a>Creare una risorsa *cluster*

Questa risorsa viene utilizzata come connessione di identità intermedia tra il Key Vault e i componenti. Dopo aver ricevuto una conferma che le sottoscrizioni sono state inserite nell'elenco elementi consentiti, creare una risorsa cluster Log Analytics nell'area in cui si trovano i componenti. Il tipo della risorsa cluster viene definito al momento della creazione impostando la proprietà *clusterType* su *LogAnalytics*o *ApplicationInsights*. Deve essere *ApplicationInsights* per Application Insights CMK. L'impostazione *clusterType* non può essere modificata dopo la configurazione.

Crea:

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "region-name",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

Risposta:

L'identità viene assegnata al cluster al momento della creazione.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id" //A GUID that was generated by the managed identity service
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id"   //A GUID that Log Analytics generates for the cluster
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="associate-a-component-to-a-cluster-resource"></a>Associare un componente a una risorsa cluster

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Insights/components/{component-name}?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id" //The id of the cluster resource
  },
  "location": "region-name",
  "kind": "component-type",
}
```

Risposta

```json
{
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.insights/components/{component-name}",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id" //The id of the cluster resource
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

Dopo l'associazione, i dati inviati ai componenti vengono archiviati crittografati con la chiave gestita.
