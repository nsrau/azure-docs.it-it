---
title: Panoramica del log attività di Azure
description: Informazioni sul log attività di Azure e su come usarlo per comprendere gli eventi che si verificano all'interno della sottoscrizione di Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 6fc00bf0dfb83f349da91989a579f31be2027ff0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071676"
---
# <a name="overview-of-azure-activity-log"></a>Panoramica del log attività di Azure

Il **Log attività di Azure** fornisce approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Ciò include un intervallo di dati che vanno dai dati operativi di Azure Resource Manager agli aggiornamenti sugli eventi di integrità del servizio. Il Log attività era noto in precedenza come _log di controllo_ oppure _log operativi_, dal momento che la categoria amministrativa segnala eventi del piano di controllo per le sottoscrizioni. 

Usare il Log attività, per determinare il _cosa_, _chi_, e _quando_ per qualsiasi operazione (PUT, POST, DELETE) eseguite sulle risorse nella sottoscrizione di scrittura. Consente inoltre di comprendere lo stato dell'operazione e altre proprietà specifiche. 

Il Log attività non include operazioni di lettura (GET) o quelle per le risorse che usano il modello classico/RDFE.

## <a name="comparison-to-diagnostic-logs"></a>Confronto con i log di diagnostica
È un singolo registro attività per ogni sottoscrizione di Azure. Fornisce i dati relativi alle operazioni su una risorsa esterna (il "piano di controllo"). [I log di diagnostica](diagnostic-logs-overview.md) vengono generati da una risorsa e contengono informazioni sul funzionamento di tale risorsa (il "piano dati"). È necessario abilitare le impostazioni di diagnostica per ogni risorsa.

![Log di attività rispetto al log di diagnostica](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Il log attività di Azure è destinato principalmente alle attività che si verificano in Azure Resource Manager. Non tiene traccia delle risorse che usano il modello classico/RDFE. Alcuni tipi di risorse classiche dispongono di un provider di risorse proxy in Azure Resource Manager (ad esempio, Microsoft.ClassicCompute). Se un utente interagisce con un tipo di risorsa classica tramite Azure Resource Manager con questi provider di risorse di proxy, le operazioni verranno visualizzate nel log attività. Se un utente interagisce con un tipo di risorsa classica all'esterno dei proxy di Azure Resource Manager, le azioni dell'utente verranno registrate solo nel log delle operazioni. È possibile esaminare il log delle operazioni in una sezione distinta del portale.

## <a name="activity-log-retention"></a>Conservazione dei Log attività
Una volta creato, le voci di Log attività non modificate o eliminate dal sistema. È inoltre, non è possibile modificarli nell'interfaccia o a livello di codice. Eventi del Log attività vengono archiviati per 90 giorni. Per archiviare i dati per periodi più lunghi, [raccoglierli in Monitoraggio di Azure](activity-log-collect.md) oppure [esportarlo di archiviazione o hub eventi](activity-log-export.md).

## <a name="view-the-activity-log"></a>Visualizzare il Log attività
Visualizzare il Log attività per tutte le risorse dal **Monitor** menu nel portale di Azure. Visualizzare il Log attività per una determinata risorsa dal **Log attività** opzione nel menu della risorsa. È anche possibile recuperare i record del Log attività con PowerShell, CLI o l'API REST.  Visualizzare [visualizzazione e come recuperare le attività di Azure vengono registrati eventi](activity-log-view.md).

![Visualizzare il Log attività](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Raccogliere Log attività in Monitoraggio di Azure
Raccogliere Log attività in un'area di lavoro di Log Analitica in Monitoraggio di Azure per l'analisi con altri dati di monitoraggio e conservare i dati per più di 90 giorni. Visualizzare [raccogliere e analizzare i log attività di Azure nell'area di lavoro di Log Analitica in Monitoraggio di Azure](activity-log-collect.md).

![Log attività di query](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Esporta Log attività
Esportare il Log attività in archiviazione di Azure per l'archiviazione o trasmetterli a un Hub eventi per l'inserimento tramite un servizio di terze parti o una soluzione analitica personalizzato. Visualizzare [esportare il Log attività Azure](activity-log-export.md). È inoltre possibile analizzare gli eventi del Log attività in Power BI usando il [ **pacchetto di contenuto di Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

## <a name="alert-on-activity-log"></a>Avvisi sui Log attività
È possibile creare un avviso quando vengono creati determinati eventi nel Log attività con un [avviso del Log attività](activity-log-alerts.md). È anche possibile creare un avviso usando un [query di log](alerts-log-query.md) quando il Log attività è connesso a un'area di lavoro di Log Analitica, ma è previsto un costo per registrare gli avvisi di query. Non sono previsti costi per gli avvisi del Log attività.

## <a name="categories-in-the-activity-log"></a>Categorie nel log attività
Ogni evento nel Log attività dispone di una determinata categoria descritte nella tabella seguente. Per informazioni dettagliate sugli schemi di queste categorie, vedere [Schema degli eventi del log attività di Azure](activity-log-schema.md). 

| Category | Descrizione |
|:---|:---|
| Administrative | Contiene il record di tutti di creazione, le operazioni di aggiornamento, eliminazione e azione eseguite tramite Resource Manager. Esempi di eventi amministrativi _crea macchina virtuale_ e _eliminare il gruppo di sicurezza di rete_.<br><br>Ogni azione eseguita da un utente o l'applicazione usando Resource Manager viene modellato come un'operazione su un determinato tipo di risorsa. Se il tipo di operazione _scrivere_, _eliminare_, o _azione_, i record di avvio e riuscita o non riuscita di tale operazione viene registrato nella categoria amministrativa. Gli eventi amministrativi includano anche le modifiche apportate al controllo degli accessi in base al ruolo in una sottoscrizione. |
| Integrità del servizio | Contiene il record degli eventi imprevisti di integrità qualsiasi servizio che si sono verificati in Azure. Un esempio di un evento di integrità del servizio _di SQL Azure negli Stati Uniti orientali sta riscontrando tempi di inattività_. <br><br>Gli eventi di integrità del servizio sono disponibili in sei tipi: _Azione necessaria_, _recupero assistito_, _Incident_, _manutenzione_, _informazioni_, o  _Sicurezza_. Questi eventi vengono creati solo se si dispone di una risorsa nella sottoscrizione è interessata dall'evento.
| Integrità delle risorse | Contiene il record di tutti gli eventi di integrità risorse che si sono verificati per le risorse di Azure. È un esempio di un evento di integrità risorse _lo stato di integrità macchina virtuale diventa non disponibile_.<br><br>Gli eventi di integrità delle risorse possono rappresentare uno dei quattro stati di integrità: _Disponibile_, _non disponibile_, _danneggiato_, e _sconosciuto_. Inoltre, gli eventi di integrità delle risorse possono essere classificati come in corso _avviata dalla piattaforma_ oppure _avviata dall'utente_. |
| Avviso | Contiene il record di attivazioni per gli avvisi di Azure. È un esempio di un evento di avviso _CPU % on myVM è stata oltre 80 per gli ultimi 5 minuti_.|
| Autoscale | Contiene il record degli eventi correlati all'operazione del motore di scalabilità automatica basato su eventuali impostazioni di scalabilità automatica definite nella sottoscrizione. È un esempio di un evento di scalabilità automatica _scalabilità automatica di azione non è stato possibile_. |
| Recommendation | Contiene gli eventi di raccomandazione di Azure Advisor. |
| Security | Contiene il record degli avvisi generati dal Centro sicurezza di Azure. Un esempio di un evento di sicurezza rappresentato _file di estensione doppia sospetta eseguito_. |
| Policy | Contiene i record di tutte le operazioni di azioni effettive eseguite dai criteri di Azure. Esempi di eventi criteri _Audit_ e _Deny_. Ogni azione eseguita da Criteri viene modellata come operazione su una risorsa. |


## <a name="next-steps"></a>Fasi successive

* [Creare un profilo di log per esportare i Log attività di Azure](activity-log-export.md)
* [Trasmettere il log attività di Azure a Hub eventi](activity-logs-stream-event-hubs.md)
* [Archiviare il Log attività di Azure all'archiviazione](archive-activity-log.md)

