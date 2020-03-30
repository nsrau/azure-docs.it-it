---
title: Usare i log di Monitoraggio di Azure con le app per la logica di Azure e Power AutomateUse Azure Monitor Logs with Azure Logic Apps and Power Automate
description: Informazioni su come usare App per la logica di Azure e Power Automate per automatizzare rapidamente i processi ripetibili usando il connettore di Monitoraggio di Azure.Learn how you can use Azure Logic Apps and Power Automate to quickly automate repeatable processes by using the Azure Monitor connector.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 6961b7bd94c9b3fe70365055851c488efa2cbeca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480012"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-flow"></a>Azure Monitor Logs connector for Logic Apps and Flow
[Le app per](/azure/logic-apps/) la logica di Azure e [Power Automate](https://ms.flow.microsoft.com) consentono di creare flussi di lavoro automatizzati usando centinaia di azioni per un'ampia gamma di servizi. Il connettore Log di monitoraggio di Azure consente di creare flussi di lavoro che recuperano dati da un'area di lavoro di Log Analytics o da un'applicazione Di Application Insights in Monitoraggio di Azure.The Azure Monitor Logs connector allows you to build workflows that retrieve data from a Log Analytics workspace or an Application Insights application in Azure Monitor. In questo articolo vengono descritte le azioni incluse nel connettore e viene fornita una procedura dettagliata per compilare un flusso di lavoro utilizzando questi dati.

Ad esempio, è possibile creare un'app per la logica per usare i dati del log di Monitoraggio di Azure in una notifica tramite posta elettronica da Office 365, creare un bug in Azure DevOps o pubblicare un messaggio Slack.For example, you can create a logic app to use Azure Monitor log data in an email notification from Office 365, create a bug in Azure DevOps, or post a Slack message.  È possibile attivare un flusso di lavoro da una semplice pianificazione o con un'azione in un servizio connesso, ad esempio quando viene ricevuto un messaggio di posta elettronica o un tweet. 

## <a name="actions"></a>Azioni
Nella tabella seguente vengono descritte le azioni incluse con il connettore Log di Monitoraggio di Azure.The following table describes the actions included with the Azure Monitor Logs connector. Entrambi consentono di eseguire una query di log su un'area di lavoro di Log Analytics o un'applicazione di Application Insights. La differenza sta nel modo in cui vengono restituiti i dati.

> [!NOTE]
> Il connettore Log di Monitoraggio di Azure sostituisce il [connettore di Azure Log Analytics](https://docs.microsoft.com/connectors/azureloganalytics/) e il connettore di Azure Application [Insights.](https://docs.microsoft.com/connectors/applicationinsights/) Questo connettore fornisce la stessa funzionalità degli altri ed è il metodo preferito per eseguire una query su un'area di lavoro di Log Analytics o un'applicazione di Application Insights.This connector provides the same functionality as the others and is the preferred method for running a query against a Log Analytics workspace or an Application Insights application.


| Azione | Descrizione |
|:---|:---|
| [Eseguire query ed ed elencare i risultati](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-list-results) | Restituisce ogni riga come oggetto. Utilizzare questa azione quando si desidera utilizzare ogni riga separatamente nel resto del flusso di lavoro. L'azione è in genere seguita da un [For each activity](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop). |
| [Eseguire query e visualizzare i risultati](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Restituisce tutte le righe nel set di risultati come un singolo oggetto formattato. Utilizzare questa azione quando si desidera utilizzare il set di risultati insieme nel resto del flusso di lavoro, ad esempio l'invio dei risultati in un messaggio di posta elettronica.  |

## <a name="walkthroughs"></a>Procedure dettagliate
The following tutorials illustrate the use of the Azure Monitor connectors in Azure Logic Apps. È possibile eseguire questo stesso esempio con Power Automate, l'unica differenza è la modalità di creazione del flusso di lavoro iniziale ed eseguirla al termine dell'operazione. La configurazione del flusso di lavoro e delle azioni è la stessa tra entrambe. Per iniziare, vedere [Creare un flusso da un modello in Power Automate.See Create](https://docs.microsoft.com/power-automate/get-started-logic-template) a flow from a template in Power Automate to get started.


### <a name="create-a-logic-app"></a>Creare un'app per la logica

Passare ad **App per** la logica nel portale di Azure e fare clic su **Aggiungi**. Selezionare **una sottoscrizione**, un gruppo di **risorse**e un'area per archiviare la nuova app per la logica e assegnarle un nome univoco. **Region** È possibile attivare l'impostazione **Log Analytics** per raccogliere informazioni sui dati e sugli eventi di runtime, come descritto in Configurare i log di Monitoraggio di Azure e raccogliere dati di diagnostica per le app per la logica di Azure.You can turn on Log Analytics setting to collect information about runtime data and events as described in [Set up Azure Monitor logs and collect diagnostics data for Azure Logic Apps](../../logic-apps/monitor-logic-apps-log-analytics.md). Questa impostazione non è necessaria per l'uso del connettore Log di Monitoraggio di Azure.This setting isn't required for using the Azure Monitor Logs connector.

![Creare l'app per la logica](media/logicapp-flow-connector/create-logic-app.png)


Fare clic su **Revisione e quindi su Crea** e quindi su **Crea**. Al termine della distribuzione, fare clic su **Vai alla risorsa** per aprire **Progettazione app per la logica**.

### <a name="create-a-trigger-for-the-logic-app"></a>Creare un trigger per l'app per la logicaCreate a trigger for the logic app
In **Inizia con un trigger comune**selezionare **Ricorrenza**. In questo modo viene creata un'app per la logica che viene eseguita automaticamente a intervalli regolari. Nella casella **Frequenza** dell'azione selezionare **Giorno** e nella casella **Intervallo** immettere **1** per eseguire il flusso di lavoro una volta al giorno.

![Azione di ricorrenza](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Procedura dettagliata: Risultati visualizzati tramite posta elettronicaWalkthrough: Mail visualized results
L'esercitazione seguente illustra come creare un'app per la logica che invia i risultati di una query di log di Monitoraggio di Azure tramite posta elettronica. 

### <a name="add-azure-monitor-logs-action"></a>Azione Add Azure Monitor Logs
Fare clic su **Nuovo passaggio** per aggiungere un'azione che viene eseguita dopo l'azione di ricorrenza. In **Scegliere un'azione**digitare **monitoraggio di azure** e quindi selezionare Registri di Monitoraggio di **Azure**.

![Azione Log di Monitoraggio di AzureAzure Monitor Logs action](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Fare clic su **Azure Log Analytics - Run query and visualize results** (Eseguire una query e visualizzare i risultati).

![Eseguire query e visualizzare l'azione dei risultati](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Azione Add Azure Monitor Logs

Selezionare la sottoscrizione e il **gruppo di risorse** per l'area di lavoro di Log Analytics.Select the **Subscription** and Resource Group for your Log Analytics workspace. Selezionare *Area di lavoro log-analytics* per **Tipo di risorsa,** quindi selezionare il nome dell'area di lavoro in **Nome risorsa**.

Aggiungere la query di log seguente alla finestra **Query**.  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Selezionare *Imposta nella query* per l'intervallo di **tempo** e la **tabella HTML** per Tipo **di grafico**.
   
![Eseguire query e visualizzare l'azione dei risultati](media/logicapp-flow-connector/run-query-visualize-action.png)

La posta verrà inviata dall'account associato alla connessione corrente. È possibile specificare un altro account facendo clic su **Cambia connessione**.

### <a name="add-email-action"></a>Azione Aggiungi messaggio di posta elettronica

Fare clic su **Nuovo passaggio**, quindi fare clic su **Aggiungi un'azione**. In **Scegliere un'azione**digitare **outlook** e quindi selezionare **Office 365 Outlook**.

![Selezionare il connettore di Outlook](media/logicapp-flow-connector/select-outlook-connector.png)

Selezionare **Invia un messaggio di posta elettronica (V2)**.

![Finestra di selezione di Office 365 Outlook](media/logicapp-flow-connector/select-mail-action.png)

Fare clic in un punto qualsiasi della casella **Corpo** per aprire una finestra **Contenuto dinamico** con i valori delle azioni precedenti nell'app per la logica. Selezionare **Visualizza altro** e quindi **Corpo** che è il risultato della query nell'azione Log Analytics.

![Selezionare Corpo](media/logicapp-flow-connector/select-body.png)

Specificare l'indirizzo di posta elettronica del destinatario nella casella **A** e l'oggetto del messaggio nella casella **Oggetto**. 

![Azione di posta](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>Salvare e testare l'app per la logica
Fare clic su **Salva** e quindi **su Esegui** per eseguire un'esecuzione dei test dell'app per la logica.

![Salvare ed eseguire](media/logicapp-flow-connector/save-run.png)


Al termine dell'app per la logica, controllare la posta del destinatario specificato.  Dovrebbe essere presente un messaggio di posta elettronica con un corpo simile al seguente:

![Esempio di messaggio di posta elettronica](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni sulle [query di log in Monitoraggio di Azure](../log-query/log-query-overview.md).
- Ulteriori informazioni sulle [app per la logica](/azure/logic-apps/)
- Altre informazioni su [Microsoft Flow](https://ms.flow.microsoft.com).

