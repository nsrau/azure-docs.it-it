---
title: Raccogliere i log delle risorse di Azure nell'area di lavoro Log Analytics
description: Informazioni su come trasmettere i log delle risorse di Azure a un'area di lavoro Log Analytics in monitoraggio di Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 92de47041791c8b6c540844adb62391268b81c34
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200510"
---
# <a name="collect-azure-resource-logs-in-log-analytics-workspace-in-azure-monitor"></a>Raccogliere i log delle risorse di Azure nell'area di lavoro Log Analytics in monitoraggio di Azure
I [log delle risorse](resource-logs-overview.md) in Azure forniscono dati avanzati e frequenti sul funzionamento interno di una risorsa di Azure. Questo articolo descrive come raccogliere i log delle risorse in un'area di lavoro Log Analytics che consente di analizzarli con altri dati di monitoraggio raccolti nei log di monitoraggio di Azure con potenti query di log e anche per sfruttare altre funzionalità di monitoraggio di Azure, ad esempio gli avvisi e Visualizzazioni. 


## <a name="what-you-can-do-with-resource-logs-in-a-workspace"></a>Operazioni possibili con i log delle risorse in un'area di lavoro
Raccogliendo i log delle risorse in un'area di lavoro Log Analytics è possibile analizzare i log di tutte le risorse di Azure insieme e sfruttare tutte le funzionalità disponibili per i [log di monitoraggio di Azure](data-platform-logs.md) , inclusi i seguenti:

* **Query di log** : è possibile creare [query di log](../log-query/log-query-overview.md) usando un linguaggio di query avanzato per analizzare rapidamente e ottenere informazioni dettagliate sui dati di diagnostica e per analizzarli con i dati raccolti da altre origini in monitoraggio di Azure.
* **Avvisi: ottenere** una notifica proattiva delle condizioni critiche e dei modelli identificati nei log delle risorse usando gli [avvisi del log in monitoraggio di Azure](alerts-log.md).
* **Visualizzazioni** : aggiungere i risultati di una query di log a un dashboard di Azure o includerli in una cartella di lavoro come parte di un report interattivo.

## <a name="prerequisites"></a>Prerequisiti
Se non si dispone già di una nuova area di lavoro, è necessario [crearne una nuova](../learn/quick-create-workspace.md) . Non è necessario che l'area di lavoro si trovi nella stessa sottoscrizione della risorsa che invia log, purché l'utente che configura l'impostazione disponga dell'accesso RBAC appropriato a entrambe le sottoscrizioni.

## <a name="create-a-diagnostic-setting"></a>Creare un'impostazione di diagnostica
I log delle risorse non vengono raccolti per impostazione predefinita. Raccoglierli in un'area di lavoro Log Analytics e altre destinazioni creando un'impostazione di diagnostica per una risorsa di Azure. Per informazioni dettagliate, vedere [creare un'impostazione di diagnostica per raccogliere log e metriche in Azure](diagnostic-settings.md) .

## <a name="collection-mode"></a>Modalità di raccolta
I dati raccolti in un'area di lavoro Log Analytics vengono archiviati in tabelle come descritto in [struttura dei log di monitoraggio di Azure](../log-query/logs-structure.md). Le tabelle utilizzate dai log delle risorse dipendono dal tipo di raccolta utilizzata dalla risorsa:

- Diagnostica di Azure: tutti i dati scritti sono nella tabella _AzureDiagnostics_ .
- Specifica della risorsa: i dati vengono scritti in una singola tabella per ogni categoria della risorsa.

### <a name="azure-diagnostics-mode"></a>Modalità Diagnostica di Azure 
In questa modalità, tutti i dati di qualsiasi [impostazione di diagnostica](diagnostic-settings.md) verranno raccolti nella tabella _AzureDiagnostics_ . Questo è il metodo legacy attualmente usato dalla maggior parte dei servizi di Azure.

Poiché più tipi di risorse inviano i dati alla stessa tabella, il relativo schema corrisponde al superset degli schemi di tutti i diversi tipi di dati raccolti.

Si consideri l'esempio seguente in cui le impostazioni di diagnostica vengono raccolte nella stessa area di lavoro per i tipi di dati seguenti:

- Log di controllo del servizio 1 (con schema composto da colonne A, B e C)  
- Log degli errori del servizio 1 (con schema composto da colonne D, E e F)  
- Log di controllo del servizio 2 (con uno schema composto da colonne G, H e I)  

La tabella AzureDiagnostics sarà simile alla seguente:  

| ResourceProvider    | Categoria     | A  | b  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft. Service1 | AuditLogs    | X1 | Y1 | Z1 |    |    |    |    |    |    |
| Microsoft. Service1 | ErrorLogs    |    |    |    | Q1 | W1 | E1 |    |    |    |
| Microsoft. Service2 | AuditLogs    |    |    |    |    |    |    | J1 | K1 | L1 |
| Microsoft. Service1 | ErrorLogs    |    |    |    | Q2 | W2 | E2 |    |    |    |
| Microsoft. Service2 | AuditLogs    |    |    |    |    |    |    | J3 | K3 | L3 |
| Microsoft. Service1 | AuditLogs    | X5 | Y5 | Z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Specifico della risorsa
In questa modalità vengono create singole tabelle nell'area di lavoro selezionata per ogni categoria selezionata nell'impostazione di diagnostica. Questo metodo è consigliato poiché rende molto più semplice lavorare con i dati nelle query di log, offre una migliore individuabilità degli schemi e della relativa struttura, migliora le prestazioni in base alla latenza di inserimento e ai tempi di query e la possibilità di concedere diritti RBAC su tabella specifica. Alla fine, tutti i servizi di Azure eseguiranno la migrazione alla modalità specifica della risorsa. 

L'esempio precedente comporterebbe la creazione di tre tabelle:
 
- Tabella *Service1AuditLogs* come segue:

    | Provider di risorse | Categoria | A | b | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | X1 | Y1 | Z1 |
    | Service1 | AuditLogs | X5 | Y5 | Z5 |
    | ... |

- Tabella *Service1ErrorLogs* come segue:  

    | Provider di risorse | Categoria | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  Q1 | W1 | E1 |
    | Service1 | ErrorLogs |  Q2 | W2 | E2 |
    | ... |

- Tabella *Service2AuditLogs* come segue:  

    | Provider di risorse | Categoria | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | J1 | K1 | L1|
    | Service2 | AuditLogs | J3 | K3 | L3|
    | ... |



### <a name="select-the-collection-mode"></a>Selezionare la modalità di raccolta
La maggior parte delle risorse di Azure scriverà i dati nell'area di lavoro in modalità **diagnostica di Azure** o **specifica della risorsa** senza alcuna scelta. Per informazioni dettagliate sulla modalità di utilizzo, vedere la [documentazione relativa a ogni servizio](diagnostic-logs-schema.md) . Tutti i servizi di Azure utilizzeranno la modalità specifica della risorsa. Come parte di questa transizione, alcune risorse consentiranno di selezionare una modalità nell'impostazione di diagnostica. È necessario specificare la modalità specifica della risorsa per le nuove impostazioni di diagnostica, in quanto ciò rende più semplice la gestione dei dati e può essere utile per evitare migrazioni complesse in un secondo momento.
  
   ![Selettore modalità impostazioni di diagnostica](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> Attualmente, la **diagnostica di Azure** e la modalità **specifica delle risorse** possono essere selezionate solo quando si configura l'impostazione di diagnostica nel portale di Azure. Se si configura l'impostazione usando l'interfaccia della riga di comando, PowerShell o l'API REST, per impostazione predefinita verrà usata la **diagnostica di Azure**.

È possibile modificare un'impostazione di diagnostica esistente in modalità specifica della risorsa. In questo caso, i dati già raccolti rimarranno nella tabella _AzureDiagnostics_ fino a quando non vengono rimossi in base all'impostazione di conservazione per l'area di lavoro. I nuovi dati verranno raccolti nella tabella dedicata. Utilizzare l'operatore [Union](https://docs.microsoft.com/azure/kusto/query/unionoperator) per eseguire query sui dati in entrambe le tabelle.

Continua a guardare il Blog sugli [aggiornamenti di Azure per gli](https://azure.microsoft.com/updates/) annunci relativi ai servizi di Azure che supportano la modalità specifica delle risorse.

### <a name="column-limit-in-azurediagnostics"></a>Limite di colonne in AzureDiagnostics
È previsto un limite di proprietà di 500 per qualsiasi tabella nei log di monitoraggio di Azure. Una volta raggiunto questo limite, le righe contenenti dati con qualsiasi proprietà al di fuori della prima 500 verranno eliminate in fase di inserimento. La tabella *AzureDiagnostics* è particolarmente soggetta a questo limite, perché include le proprietà per tutti i servizi di Azure che scrivono.

Se si raccolgono i log di diagnostica da più servizi, _AzureDiagnostics_ può superare questo limite e i dati verranno persi. Fino a quando tutti i servizi di Azure non supportano la modalità specifica della risorsa, è necessario configurare le risorse per la scrittura in più aree di lavoro per ridurre la possibilità di raggiungere il limite di 500 colonne.

### <a name="azure-data-factory"></a>Data factory di Azure
Azure Data Factory, a causa di un set di log molto dettagliato, è un servizio noto per la scrittura di un numero elevato di colonne e che può causare il superamento del limite di _AzureDiagnostics_ . Per tutte le impostazioni di diagnostica configurate prima che venisse abilitata la modalità specifica della risorsa, sarà presente una nuova colonna creata per ogni parametro utente con nome univoco per ogni attività. Verranno create altre colonne a causa della natura dettagliata degli input e degli output delle attività.
 
È necessario eseguire la migrazione dei log per usare la modalità specifica della risorsa appena possibile. Se non è possibile eseguire questa operazione immediatamente, un'alternativa provvisoria consiste nell'isolare i log Azure Data Factory nella propria area di lavoro per ridurre al minimo la possibilità che questi registri abbiano un effetto sugli altri tipi di log raccolti nelle aree di lavoro.


## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui log delle risorse di Azure, vedere [Panoramica dei log delle risorse di Azure](resource-logs-overview.md).
* Per creare un'impostazione di diagnostica per raccogliere i log delle risorse in un'area di lavoro Log Analytics, vedere [creare un'impostazione di diagnostica per raccogliere log e metriche in Azure](diagnostic-settings.md).
