---
title: Usare i log di monitoraggio di Azure con app per la logica di Azure e Power automatizzate
description: Informazioni su come usare le app per la logica di Azure e l'automazione dell'energia elettrica per automatizzare rapidamente i processi ripetibili usando il connettore di monitoraggio di Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 2ef7095d186902425adb5065c470325be1283023
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475737"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-power-automate"></a>Connettore dei log di monitoraggio di Azure per app per la logica e automazione dell'alimentazione
[App](../../logic-apps/index.yml) per la logica di Azure e [Power automatizzate](https://flow.microsoft.com) consentono di creare flussi di lavoro automatizzati usando centinaia di azioni per un'ampia gamma di servizi. Il connettore log di monitoraggio di Azure consente di creare flussi di lavoro che recuperano dati da un'area di lavoro Log Analytics o da un'applicazione Application Insights in monitoraggio di Azure. Questo articolo descrive le azioni incluse nel connettore e fornisce una procedura dettagliata per creare un flusso di lavoro usando questi dati.

Ad esempio, è possibile creare un'app per la logica per usare i dati di log di monitoraggio di Azure in una notifica di posta elettronica da Office 365, creare un bug in Azure DevOps o pubblicare un messaggio Slack.  È possibile attivare un flusso di lavoro da una semplice pianificazione o con un'azione in un servizio connesso, ad esempio quando viene ricevuto un messaggio di posta elettronica o un tweet. 

## <a name="connector-limits"></a>Limiti del connettore
Il connettore log di monitoraggio di Azure presenta questi limiti:
* Dimensioni massime dati: 16 MB
* Dimensioni massime risposta query 100 MB
* Numero massimo di record: 500.000
* Timeout max query 110 secondo.

A seconda delle dimensioni dei dati e della query usata, il connettore potrebbe raggiungere i limiti e non riuscire. È possibile ovviare a questi casi quando si modifica la ricorrenza del trigger in modo che venga eseguita più di frequente ed eseguire query su un minor numero È possibile utilizzare query che aggregano i dati per restituire meno record e colonne.

## <a name="actions"></a>Azioni
La tabella seguente descrive le azioni incluse nel connettore dei log di monitoraggio di Azure. Entrambi consentono di eseguire una query di log su un'area di lavoro Log Analytics o Application Insights applicazione. La differenza consiste nel modo in cui vengono restituiti i dati.

> [!NOTE]
> Il connettore log di monitoraggio di Azure sostituisce il [connettore azure log Analytics](/connectors/azureloganalytics/) e il connettore [applicazione Azure Insights](/connectors/applicationinsights/). Questo connettore fornisce le stesse funzionalità degli altri ed è il metodo preferito per l'esecuzione di una query su un'area di lavoro Log Analytics o un'applicazione Application Insights.


| Azione | Descrizione |
|:---|:---|
| [Eseguire query ed elencare i risultati](/connectors/azuremonitorlogs/#run-query-and-list-results) | Restituisce ogni riga come oggetto proprio. Utilizzare questa azione quando si desidera utilizzare ogni riga separatamente nel resto del flusso di lavoro. L'azione è in genere seguita da un [per ogni attività](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop). |
| [Esecuzione di query e visualizzazione dei risultati](/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Restituisce tutte le righe del set di risultati come singolo oggetto formattato. Utilizzare questa azione quando si desidera utilizzare il set di risultati insieme nel resto del flusso di lavoro, ad esempio l'invio dei risultati in un messaggio di posta elettronica.  |

## <a name="walkthroughs"></a>Procedure dettagliate
Le esercitazioni seguenti illustrano l'uso dei connettori di monitoraggio di Azure in app per la logica di Azure. È possibile eseguire lo stesso esempio con Power Automate, l'unica differenza consiste nel creare il flusso di lavoro iniziale ed eseguirlo al termine dell'operazione. La configurazione del flusso di lavoro e delle azioni è la stessa tra entrambi. Per iniziare, vedere [creare un flusso da un modello in Power automatici](/power-automate/get-started-logic-template) .


### <a name="create-a-logic-app"></a>Creare un'app per la logica

Passare a **app** per la logica nella portale di Azure e fare clic su **Aggiungi**. Selezionare una **sottoscrizione**, un **gruppo di risorse**e un' **area** per archiviare la nuova app per la logica e assegnarle un nome univoco. È possibile attivare **log Analytics** impostazione per raccogliere informazioni sui dati e gli eventi di runtime, come descritto in [configurare i log di monitoraggio di Azure e raccogliere i dati di diagnostica per le app per la logica di Azure](../../logic-apps/monitor-logic-apps-log-analytics.md). Questa impostazione non è obbligatoria per l'uso del connettore log di monitoraggio di Azure.

![Creare l'app per la logica](media/logicapp-flow-connector/create-logic-app.png)


Fare clic su **Verifica + crea** e quindi su **Crea**. Al termine della distribuzione, fare clic su **Vai alla risorsa** per aprire la **finestra di progettazione delle app**per la logica.

### <a name="create-a-trigger-for-the-logic-app"></a>Creare un trigger per l'app per la logica
In **inizia con un trigger comune**selezionare **ricorrenza**. Verrà creata un'app per la logica che viene eseguita automaticamente a intervalli regolari. Nella casella **frequenza** dell'azione selezionare **giorno** e nella casella **intervallo** immettere **1** per eseguire il flusso di lavoro una volta al giorno.

![Azione di ricorrenza](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Procedura dettagliata: visualizzare i risultati della posta
L'esercitazione seguente illustra come creare un'app per la logica che invia i risultati di una query di log di monitoraggio di Azure tramite posta elettronica. 

### <a name="add-azure-monitor-logs-action"></a>Azione Aggiungi log di monitoraggio di Azure
Fare clic su **+ nuovo passaggio** per aggiungere un'azione che viene eseguita dopo l'azione di ricorrenza. In **scegliere un'azione**Digitare **monitoraggio di Azure** e quindi selezionare **log di monitoraggio di Azure**.

![Azione log di monitoraggio di Azure](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Fare clic su **Azure Log Analytics - Run query and visualize results** (Eseguire una query e visualizzare i risultati).

![Esegui query e visualizza risultati azione](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Azione Aggiungi log di monitoraggio di Azure

Selezionare la **sottoscrizione** e il **gruppo di risorse** per l'area di lavoro log Analytics. Selezionare *log Analytics area di lavoro* per il **tipo di risorsa** e quindi selezionare il nome dell'area di lavoro in **nome risorsa**.

Aggiungere la query di log seguente alla finestra **Query**.  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Selezionare *imposta in query* per l' **intervallo di tempo** e la **tabella HTML** per il **tipo di grafico**.
   
![Esegui query e visualizza risultati azione](media/logicapp-flow-connector/run-query-visualize-action.png)

Il messaggio verrà inviato dall'account associato alla connessione corrente. È possibile specificare un altro account facendo clic su **Modifica connessione**.

### <a name="add-email-action"></a>Aggiungi azione di posta elettronica

Fare clic su **+ nuovo passaggio**e quindi su **+ Aggiungi un'azione**. In **scegliere un'azione**Digitare **Outlook** , quindi selezionare **Office 365 Outlook**.

![Seleziona connettore Outlook](media/logicapp-flow-connector/select-outlook-connector.png)

Selezionare **Invia un messaggio di posta elettronica (v2)**.

![Finestra di selezione di Office 365 Outlook](media/logicapp-flow-connector/select-mail-action.png)

Fare clic in un punto qualsiasi della casella **corpo** per aprire una finestra di **contenuto dinamica** con i valori delle azioni precedenti nell'app per la logica. Selezionare **Vedi altro** e quindi **Body** , ovvero i risultati della query nell'azione log Analytics.

![Selezionare Corpo](media/logicapp-flow-connector/select-body.png)

Specificare l'indirizzo di posta elettronica del destinatario nella casella **A** e l'oggetto del messaggio nella casella **Oggetto**. 

![Azione di posta elettronica](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>Salvare e testare l'app per la logica
Fare clic su **Save (Salva** ) e quindi **eseguire** per eseguire un'esecuzione dei test dell'app per la logica.

![Salvare ed eseguire](media/logicapp-flow-connector/save-run.png)


Al termine dell'app per la logica, controllare la posta del destinatario specificato.  Dovrebbe essere presente un messaggio di posta elettronica con un corpo simile al seguente:

![Esempio di messaggio di posta elettronica](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni sulle [query di log in Monitoraggio di Azure](../log-query/log-query-overview.md).
- Altre informazioni sulle [app](../../logic-apps/index.yml) per la logica
- Altre informazioni su [Power automatizzate](https://flow.microsoft.com).
