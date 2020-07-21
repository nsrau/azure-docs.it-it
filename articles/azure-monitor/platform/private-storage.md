---
title: Account di archiviazione di proprietà del cliente per l'inserimento dei log
description: Usare il proprio account di archiviazione per l'inserimento dei dati di log in un'area di lavoro Log Analytics in Monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2020
ms.openlocfilehash: da9ec0fc421f0cb2f2a1e6fa65d8c936cfd5a3c7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515428"
---
# <a name="customer-owned-storage-accounts-for-log-ingestion-in-azure-monitor"></a>Account di archiviazione di proprietà del cliente per l'inserimento dei log in Monitoraggio di Azure

Monitoraggio di Azure usa gli account di archiviazione nel processo di inserimento di alcuni tipi di dati, ad esempio [log personalizzati](data-sources-custom-logs.md) e alcuni [log di Azure](./diagnostics-extension-logs.md). Durante il processo di inserimento, i log vengono prima inviati a un account di archiviazione e successivamente inseriti in Log Analytics o in Application Insights. Se si vuole controllare i dati durante l'inserimento, è possibile usare i propri account di archiviazione anziché l'archiviazione gestita dal servizio. L'uso del proprio account di archiviazione consente di controllare l'accesso, il contenuto, la crittografia e la conservazione dei log durante l'inserimento. Questa operazione viene indicata come Bring Your Own Storage, o BYOS. 

Uno scenario che richiede BYOS è l'isolamento di rete tramite collegamenti privati. Quando si usa una rete virtuale, l'isolamento rete è spesso un requisito e l'accesso alla rete Internet pubblica è limitato. In questi casi, l'accesso all'archiviazione del servizio di Monitoraggio di Azure per l'inserimento dei log è completamente bloccato o considerato una procedura non valida. Al contrario, i log devono essere inseriti tramite un account di archiviazione di proprietà del cliente all'interno della VNet o facilmente accessibili da tale account.

Un altro scenario è la crittografia dei log con chiavi gestite dal cliente (CMK). I clienti possono crittografare i dati registrati usando CMK nei cluster che archiviano i log. La stessa chiave può essere usata anche per crittografare i log durante il processo di inserimento.

## <a name="data-types-supported"></a>Tipi di dati supportati

Di seguito sono riportati i tipi di dati inseriti da un account di archiviazione. Per altre informazioni sull'inserimento di questi tipi di dati, vedere [Raccogliere i dati dall'estensione diagnostica di Azure ai log di monitoraggio di Azure](./diagnostics-extension-logs.md).

| Type | Informazioni tabella |
|:-----|:------------------|
| Log di IIS | Blob: wad-iis-logfiles|
|Log eventi di Windows | Tabella: WADWindowsEventLogsTable |
| syslog | Tabella: LinuxsyslogVer2v0 |
| Log di Windows ETW | Tabella: WADETWEventTable|
| Service Fabric | Tabella: WADServiceFabricSystemEventTable <br/> WADServiceFabricReliableActorEventTable<br/> WADServiceFabricReliableServicEventTable |
| Log personalizzati | n/d |
| File dump del Centro sicurezza di Azure Watson | n/d|  

## <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione 
L'account di archiviazione deve soddisfare i requisiti indicati di seguito.

- Accessibile alle risorse nella VNet che scrivono i log nell'archiviazione.
- Deve trovarsi nella stessa area geografica dell'area di lavoro a cui è collegato.
- Consenti accesso a monitoraggio di Azure: se si sceglie di limitare l'accesso dell'account di archiviazione per selezionare le reti, assicurarsi di consentire questa eccezione: *Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione*.

## <a name="process-to-configure-customer-owned-storage"></a>Processo di configurazione dell'archiviazione di proprietà del cliente
Il processo di base dell'uso dell'account di archiviazione per l'inserimento è il seguente:

1. creare un account di archiviazione o selezionarne uno esistente.
2. Collegare l'account di archiviazione a un'area di lavoro Log Analytics.
3. Gestire l'archiviazione esaminando il carico e la conservazione per assicurarsi che funzioni come previsto.

L'unico metodo disponibile per la creazione e la rimozione dei collegamenti è tramite l'API REST. Le sezioni seguenti forniscono informazioni dettagliate sulla richiesta API specifica necessaria per ogni processo.

## <a name="command-line-and-rest-api"></a>Riga di comando e API REST

### <a name="command-line"></a>Riga di comando
Per creare e gestire gli account di archiviazione collegati, usare [AZ monitor log-Analytics Workspace Linked-storage](/cli/azure/monitor/log-analytics/workspace/linked-storage). Questo comando può collegare e scollegare gli account di archiviazione da un'area di lavoro ed elencare gli account di archiviazione collegati.

### <a name="request-and-cli-values"></a>Valori di richiesta e CLI

#### <a name="datasourcetype"></a>dataSourceType 

- AzureWatson: usare questo valore per i file di dump di Azure Watson del Centro sicurezza di Azure.
- CustomLogs: usare questo valore per i tipi di dati seguenti:
  - Log personalizzati
  - Log IIS
  - Eventi (Windows)
  - Syslog (Linux)
  - Log ETW
  - Eventi di Service Fabric
  - Dati di valutazione  

#### <a name="storage_account_resource_id"></a>storage_account_resource_id
Questo valore usa la struttura seguente:

```
subscriptions/{subscriptionId}/resourcesGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName1}
```


### <a name="get-linked-storage-accounts-for-all-data-source-types"></a>Ottenere gli account di archiviazione collegati per tutti i tipi di origine dati

#### <a name="api-request"></a>Richiesta API

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Risposta

```json
{
    [
        {
            "properties":
            {
                "dataSourceType": "CustomLogs",
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_1>",
                    "<storage_account_resource_id_2>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
            "name": "CustomLogs",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        },
        {
            "properties":
            {
                "dataSourceType": " AzureWatson "
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_3>",
                    "<storage_account_resource_id_4>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/AzureWatson",
            "name": "AzureWatson",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        }
    ]
}
```


### <a name="get-linked-storage-accounts-for-a-specific-data-source-type"></a>Ottenere gli account di archiviazione collegati per un tipo di origine dati specifico

#### <a name="api-request"></a>Richiesta API

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Risposta 

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs",
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
    "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
    "name": "CustomLogs",
    "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```

## <a name="create-or-modify-a-link"></a>Creare o modificare un collegamento

Quando si collega un account di archiviazione a un'area di lavoro, Log Analytics inizia a usarlo al posto dell'account di archiviazione di proprietà del servizio. È possibile registrare contemporaneamente un elenco di account di archiviazione ed è possibile usare lo stesso account di archiviazione per più aree di lavoro.

Se l'area di lavoro gestisce sia risorse VNet che risorse esterne a una VNet, è necessario assicurarsi che non venga rifiutato il traffico proveniente da Internet. Lo spazio di archiviazione deve avere le stesse impostazioni dell'area di lavoro ed essere reso disponibile per le risorse all'esterno della VNet. 

### <a name="api-request"></a>Richiesta API

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

### <a name="payload"></a>Payload

```json
{
    "properties":
    {
        "storageAccountIds  " : 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    }
}
```

### <a name="response"></a>Risposta

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs"
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
"id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
"name": "CustomLogs",
"type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```


## <a name="unlink-a-storage-account"></a>Scollegare un account di archiviazione
Se si decide di interrompere l'uso di un account di archiviazione per l'inserimento o di sostituire l'area di lavoro in uso, è necessario scollegare lo spazio di archiviazione dall'area di lavoro.

Lo scollegamento di tutti gli account di archiviazione da un'area di lavoro significa che l'inserimento tenterà di utilizzare gli account di archiviazione gestiti dal servizio. Se gli agenti vengono eseguiti in una rete virtuale con accesso limitato a Internet, l'inserimento dovrebbe avere esito negativo. L'area di lavoro deve avere un account di archiviazione collegato raggiungibile dalle risorse monitorate.

Prima di eliminare un account di archiviazione, è necessario assicurarsi che tutti i dati in esso contenuti siano stati inseriti nell'area di lavoro. Per precauzione, tenere disponibile l'account di archiviazione dopo avere collegato una risorsa di archiviazione alternativa. Eliminarlo solo dopo che tutto il contenuto è stato inserito ed è possibile vedere che i nuovi dati vengono scritti nell'account di archiviazione appena connesso.


### <a name="api-request"></a>Richiesta API
```
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

## <a name="replace-a-storage-account"></a>Sostituire un account di archiviazione

Per sostituire un account di archiviazione usato per l'inserimento, creare prima di tutto un collegamento per un nuovo account di archiviazione. Gli agenti di registrazione otterranno la configurazione aggiornata e inizieranno a inviare i dati anche alla nuova risorsa di archiviazione.

Scollegare quindi il vecchio account di archiviazione in modo che gli agenti smettano di scrivere nell'account rimosso. Il processo di inserimento continua a leggere i dati da questo account fino a quando non vengono inseriti tutti. Non eliminare l'account di archiviazione finché non vengono visualizzati tutti i log inseriti.

La configurazione dell'agente verrà aggiornata dopo alcuni minuti e passerà alla nuova risorsa di archiviazione.

## <a name="manage-storage-account"></a>Gestire un account di archiviazione

### <a name="load"></a>Caricamento

Gli account di archiviazione possono gestire un determinato carico di richieste di lettura e scrittura prima di avviare la limitazione delle richieste. Tale limitazione influisce sul tempo necessario per inserire i log e può causare la perdita di dati. Se lo spazio di archiviazione è sovraccarico, registrare gli account di archiviazione aggiuntivi e suddividere il carico tra di loro. 

### <a name="related-charges"></a>Addebiti correlati

Gli account di archiviazione vengono addebitati in base al volume dei dati archiviati, ai tipi di archiviazione e al tipo di ridondanza. Per informazioni dettagliate, vedere [Prezzi per i BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/) e [Prezzi di Archiviazione tabelle di Azure](https://azure.microsoft.com/pricing/details/storage/tables/).

Se l'account di archiviazione registrato dell'area di lavoro si trova in un'altra area geografica, verranno addebitati i costi per l'uscita in base a tali [dati sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).



## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla configurazione di un collegamento privato, vedere [usare il collegamento privato di Azure per connettere in modo sicuro le reti a monitoraggio di Azure](private-link-security.md)
