---
title: Log delle risorse di Azure
description: Informazioni su come trasmettere i log delle risorse di Azure a un'area di lavoro Log Analytics in monitoraggio di Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: ccf470abadb28919e4fca3c4862b71946a5bb204
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87800501"
---
# <a name="azure-resource-logs"></a>Log delle risorse di Azure
I log delle risorse di Azure sono [log di piattaforma](platform-logs-overview.md) che forniscono informazioni dettagliate sulle operazioni eseguite all'interno di una risorsa di Azure. Il contenuto dei log delle risorse varia in base al servizio di Azure e al tipo di risorsa. I log delle risorse non vengono raccolti per impostazione predefinita. È necessario creare un'impostazione di diagnostica per ogni risorsa di Azure per inviare i log delle risorse a un'area di lavoro Log Analytics da usare con i [log di monitoraggio di Azure](data-platform-logs.md), Hub eventi di Azure da inoltrare all'esterno di Azure o ad archiviazione di Azure per l'archiviazione.

Vedere [creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse](diagnostic-settings.md) per informazioni dettagliate sulla creazione di un'impostazione di diagnostica e sulla [distribuzione di monitoraggio di Azure su larga scala usando criteri](../deploy-scale.md) di Azure per informazioni dettagliate sull'uso di criteri di Azure per creare automaticamente un'impostazione di diagnostica per ogni risorsa di Azure creata.

## <a name="send-to-log-analytics-workspace"></a>Inviare all'area di lavoro Log Analytics
 Inviare i log delle risorse a un'area di lavoro di Log Analytics per abilitare le funzionalità dei [log di monitoraggio di Azure](data-platform-logs.md) , inclusi i seguenti:

- Correlare i dati del log delle risorse con altri dati di monitoraggio raccolti da monitoraggio di Azure.
- Consolidare le voci di log da più risorse, sottoscrizioni e tenant di Azure in un'unica posizione per l'analisi insieme.
- Usare query di log per eseguire analisi complesse e ottenere informazioni approfondite sui dati del log.
- Usare gli avvisi del log con una logica di avviso complessa.

[Creare un'impostazione di diagnostica](diagnostic-settings.md) per inviare i log delle risorse a un'area di lavoro log Analytics. Questi dati vengono archiviati in tabelle come descritto in [struttura dei log di monitoraggio di Azure](../log-query/logs-structure.md). Le tabelle utilizzate dai log delle risorse dipendono dal tipo di raccolta utilizzata dalla risorsa:

- Diagnostica di Azure: tutti i dati scritti sono nella tabella _AzureDiagnostics_ .
- Specifica della risorsa: i dati vengono scritti in una singola tabella per ogni categoria della risorsa.

### <a name="azure-diagnostics-mode"></a>Modalità diagnostica di Azure 
In questa modalità, tutti i dati di qualsiasi impostazione di diagnostica verranno raccolti nella tabella _AzureDiagnostics_ . Questo è il metodo legacy attualmente usato dalla maggior parte dei servizi di Azure. Poiché più tipi di risorse inviano i dati alla stessa tabella, il relativo schema corrisponde al superset degli schemi di tutti i diversi tipi di dati raccolti.

Si consideri l'esempio seguente in cui le impostazioni di diagnostica vengono raccolte nella stessa area di lavoro per i tipi di dati seguenti:

- Log di controllo del servizio 1 (con schema composto da colonne A, B e C)  
- Log degli errori del servizio 1 (con schema composto da colonne D, E e F)  
- Log di controllo del servizio 2 (con uno schema composto da colonne G, H e I)  

La tabella AzureDiagnostics sarà simile alla seguente:  

| ResourceProvider    | Category     | Una  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft. Service1 | AuditLogs    | X1 | Y1 | Z1 |    |    |    |    |    |    |
| Microsoft. Service1 | ErrorLogs    |    |    |    | q1 | W1 | E1 |    |    |    |
| Microsoft. Service2 | AuditLogs    |    |    |    |    |    |    | J1 | k1 | L1 |
| Microsoft. Service1 | ErrorLogs    |    |    |    | q2 | W2 | E2 |    |    |    |
| Microsoft. Service2 | AuditLogs    |    |    |    |    |    |    | J3 | K3 | L3 |
| Microsoft. Service1 | AuditLogs    | X5 | Y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Specifico della risorsa
In questa modalità vengono create singole tabelle nell'area di lavoro selezionata per ogni categoria selezionata nell'impostazione di diagnostica. Questo metodo è consigliato perché rende molto più semplice lavorare con i dati nelle query di log, offre una migliore individuabilità degli schemi e della relativa struttura, migliora le prestazioni in base alla latenza di inserimento e ai tempi di query e alla possibilità di concedere diritti RBAC su una tabella specifica. Alla fine, tutti i servizi di Azure eseguiranno la migrazione alla modalità Resource-Specific. 

L'esempio precedente comporterebbe la creazione di tre tabelle:
 
- Tabella *Service1AuditLogs* come segue:

    | Provider di risorse | Category | Una | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | X1 | Y1 | Z1 |
    | Service1 | AuditLogs | X5 | Y5 | z5 |
    | ... |

- Tabella *Service1ErrorLogs* come segue:  

    | Provider di risorse | Category | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  q1 | W1 | E1 |
    | Service1 | ErrorLogs |  q2 | W2 | E2 |
    | ... |

- Tabella *Service2AuditLogs* come segue:  

    | Provider di risorse | Category | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | J1 | k1 | L1|
    | Service2 | AuditLogs | J3 | K3 | L3|
    | ... |



### <a name="select-the-collection-mode"></a>Selezionare la modalità di raccolta
La maggior parte delle risorse di Azure scriverà i dati nell'area di lavoro in modalità **diagnostica di Azure** o **specifica della risorsa** senza alcuna scelta. Per informazioni dettagliate sulla modalità di utilizzo, vedere la [documentazione relativa a ogni servizio](./resource-logs-schema.md) . Tutti i servizi di Azure utilizzeranno alla fine la modalità Resource-Specific. Come parte di questa transizione, alcune risorse consentiranno di selezionare una modalità nell'impostazione di diagnostica. Specificare la modalità specifica della risorsa per tutte le nuove impostazioni di diagnostica, in quanto ciò rende più semplice la gestione dei dati e può essere utile per evitare migrazioni complesse in un secondo momento.
  
   ![Selettore modalità impostazioni di diagnostica](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)

> [!NOTE]
> Per un esempio di impostazione della modalità di raccolta usando un modello di Resource Manager, vedere [Gestione risorse esempi di modelli per le impostazioni di diagnostica in monitoraggio di Azure](../samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-recovery-services-vault).


È possibile modificare un'impostazione di diagnostica esistente in modalità specifica della risorsa. In questo caso, i dati già raccolti rimarranno nella tabella _AzureDiagnostics_ fino a quando non vengono rimossi in base all'impostazione di conservazione per l'area di lavoro. I nuovi dati verranno raccolti nella tabella dedicata. Utilizzare l'operatore [Union](/azure/kusto/query/unionoperator) per eseguire query sui dati in entrambe le tabelle.

Continua a guardare il Blog sugli [aggiornamenti di Azure per gli](https://azure.microsoft.com/updates/) annunci relativi ai servizi di Azure che supportano la modalità Resource-Specific.

### <a name="column-limit-in-azurediagnostics"></a>Limite di colonne in AzureDiagnostics
È previsto un limite di proprietà di 500 per qualsiasi tabella nei log di monitoraggio di Azure. Una volta raggiunto questo limite, le righe contenenti dati con qualsiasi proprietà al di fuori della prima 500 verranno eliminate in fase di inserimento. La tabella *AzureDiagnostics* è particolarmente soggetta a questo limite, perché include le proprietà per tutti i servizi di Azure che scrivono.

Se si raccolgono i log delle risorse da più servizi, _AzureDiagnostics_ può superare questo limite e i dati verranno persi. Fino a quando tutti i servizi di Azure non supportano la modalità specifica della risorsa, è necessario configurare le risorse per la scrittura in più aree di lavoro per ridurre la possibilità di raggiungere il limite di 500 colonne.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory, a causa di un set dettagliato di log, è un servizio noto per la scrittura di un numero elevato di colonne e che può causare il superamento del limite di _AzureDiagnostics_ . Per tutte le impostazioni di diagnostica configurate prima dell'abilitazione della modalità specifica della risorsa, sarà presente una nuova colonna creata per ogni parametro utente denominato in modo univoco su qualsiasi attività. Verranno create altre colonne a causa della natura dettagliata degli input e degli output delle attività.
 
È necessario eseguire la migrazione dei log per usare la modalità specifica della risorsa appena possibile. Se non è possibile eseguire questa operazione immediatamente, un'alternativa provvisoria consiste nell'isolare i log Azure Data Factory nella propria area di lavoro per ridurre al minimo la possibilità che questi registri abbiano un effetto sugli altri tipi di log raccolti nelle aree di lavoro.


## <a name="send-to-azure-event-hubs"></a>Invia a hub eventi di Azure
Inviare i log delle risorse a un hub eventi per inviarli all'esterno di Azure, ad esempio a un SIEM di terze parti o ad altre soluzioni di log Analytics. I log delle risorse degli hub eventi vengono utilizzati in formato JSON con un `records` elemento contenente i record in ogni payload. Lo schema dipende dal tipo di risorsa, come descritto in [schema comune e specifico del servizio per i log delle risorse di Azure](resource-logs-schema.md).

Di seguito sono riportati i dati di output di esempio di hub eventi per un log delle risorse:

```json
{
    "records": [
        {
            "time": "2019-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2019-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

## <a name="send-to-azure-storage"></a>Inviare ad Archiviazione di Azure
Inviare i log delle risorse ad archiviazione di Azure per conservarli per l'archiviazione. Dopo aver creato l'impostazione di diagnostica, viene creato un contenitore di archiviazione nell'account di archiviazione non appena si verifica un evento in una delle categorie di log abilitate. I BLOB all'interno del contenitore usano la convenzione di denominazione seguente:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ad esempio, il BLOB per un gruppo di sicurezza di rete potrebbe avere un nome simile al seguente:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Ogni BLOB PT1H.json contiene un BLOB JSON di eventi che si sono verificati nell'ora specificata nell'URL BLOB (ad esempio, h=12). Durante l'ora attuale, gli eventi vengono aggiunti al file PT1H.json man mano che si verificano. Il valore dei minuti (m = 00) è sempre 00, perché gli eventi del log delle risorse sono suddivisi in singoli BLOB all'ora.

All'interno del PT1H.jsfile, ogni evento viene archiviato con il formato seguente. Verrà usato uno schema di primo livello comune, ma sarà univoco per ogni servizio di Azure, come descritto in [schema dei log delle risorse](./resource-logs-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> I log di piattaforma vengono scritti nell'archiviazione BLOB usando le [righe JSON](http://jsonlines.org/), dove ogni evento è una riga e il carattere di nuova riga indica un nuovo evento. Questo formato è stato implementato nel 2018 novembre. Prima di questa data, i log venivano scritti nell'archiviazione BLOB come una matrice di record JSON, come descritto in [preparare la modifica del formato ai log della piattaforma di monitoraggio di Azure archiviati in un account di archiviazione](resource-logs-blob-format.md).


## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui log delle risorse](platform-logs-overview.md).
* [Creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse](diagnostic-settings.md).
