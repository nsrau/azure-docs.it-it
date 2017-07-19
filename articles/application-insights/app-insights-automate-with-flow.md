---
title: Automatizzare i processi di Azure Application Insights con Microsoft Flow
description: Informazioni su come usare Microsoft Flow per automatizzare in poco tempo i processi ripetibili usando Connettore di Application Insights per Flow.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 7e17fde55097a8e852be33bac66940a05fdd9df6
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizzare i processi di Application Insights con il Connettore per Microsoft Flow

Ci si trova spesso a eseguire ripetutamente le stesse query sui dati di telemetria per verificare il corretto funzionamento del servizio? Si desidera automatizzare queste query per individuare tendenze e anomalie e creare dei flussi di lavoro basati su queste informazioni? Connettore di Application Insights (anteprima) per Microsoft Flow serve proprio a questo.
Con questa integrazione è ora possibile automatizzare numerosi processi senza dover scrivere codice. Dopo avere creato un flusso usando un'azione di Application Insights, viene eseguita automaticamente la query di Application Insights - Analisi. È possibile aggiungere anche altre azioni. In Flow sono disponibili centinaia di azioni. È ad esempio possibile usare Flow per inviare automaticamente una notifica di posta elettronica o creare un bug in Visual Studio Team Services. È inoltre possibile usare uno dei numerosi [modelli](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) disponibili per il Connettore per Microsoft Flow. Questi modelli velocizzano il processo di creazione di un flusso. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="tutorial-for-creating-a-flow-for-application-insights"></a>Esercitazione per la creazione di un flusso per Application Insights

Questa esercitazione illustra come creare un flusso che usa l'algoritmo di cluster automatico di Analisi per raggruppare gli attributi dei dati per un'applicazione Web. Il flusso invia automaticamente i risultati tramite posta elettronica. Questo è solo un esempio di utilizzo congiunto di Microsoft Flow e Application Insights - Analisi. 

### <a name="step-1-create-a-flow"></a>Passaggio 1: Creare un flusso
1. Accedere a: http://flow.microsoft.com e selezionare **Flussi personali**.
2. Fare clic su **Crea un flusso da un modello vuoto**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>Passaggio 2: Creare un trigger per il flusso
1. Scegliere **Pianificazione** e quindi **Pianificazione - Ricorrenza**.
2. Impostare **Frequenza** su Giorno con un **Intervallo** di 1.

![Finestra di dialogo del trigger del flusso](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-azure-application-insights-action"></a>Passaggio 3: Aggiungere un'azione di Azure Application Insights
1. Fare clic su **Nuovo passaggio** e quindi su **Aggiungi un'azione**.
2. Cercare Azure Application Insights.
3. Fare clic su Application Insights - Visualize Analytics query Preview (Application Insights - Visualizza query di Analisi Anteprima).

![Schermata di esecuzione della query di Analisi](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Passaggio 4: Connettersi a una risorsa di Application Insightss

**Prerequisito**

Per completare questo passaggio, è necessario disporre di un ID applicazione e di una chiave API per la risorsa. È possibile recuperare queste informazioni dal portale di Azure, come illustrato nel diagramma seguente:

![ID applicazione nel portale di Azure](./media/app-insights-automate-with-flow/appid.png) 

- Specificare un nome per la connessione insieme all'ID applicazione e alla chiave API.

![Schermata di connessione di Flow](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Passaggio 5: Specificare la query e il tipo di grafico di Analisi
In questo esempio si selezionano le richieste non riuscite entro l'ultimo giorno e le si correla alle eccezioni che si sono verificate durante l'operazione. La correlazione eseguita da Analisi si basa sull'identificatore operation_Id. La query segmenta quindi i risultati usando l'algoritmo di cluster automatico. Quando si creano query, assicurarsi di verificare che funzionino correttamente in Analisi prima di aggiungerle al flusso.

- Aggiungere la query di Analisi seguente e selezionare il tipo di grafico di tabella Html. 

```
requests
| where timestamp > ago(1d)
| where success == "False"
| project name, operation_Id
| join ( exceptions
    | project problemId, outerMessage, operation_Id
) on operation_Id
| evaluate autocluster()
```
![Schermata di configurazione della query di Analisi](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Passaggio 6: Configurare il flusso per l'invio tramite posta elettronica

1. Fare clic su **Nuovo passaggio** e quindi selezionare **Aggiungi un'azione**.
2. Cercare Office 365 Outlook.
3. Fare clic su Office 365 Outlook – Send an email (Office 365 Outlook: invia un messaggio di posta elettronica).
![Selezione di Office 365 Outlook](./media/app-insights-automate-with-flow/flow2b.png)

4. Nell'azione di invio di posta elettronica aggiungere i dati seguenti:
 - Specificare l'indirizzo di posta elettronica del destinatario.
 - Specificare l'oggetto del messaggio di posta elettronica.
 - Posizionare il cursore nel campo **Corpo** e dal menu di contenuto dinamico che viene visualizzato a destra selezionare **Corpo**.
 - Fare clic su **Mostra opzioni avanzate**.

 ![Configurazione di Office 365 Outlook](./media/app-insights-automate-with-flow/flow5.png)

5. Dal menu di contenuto dinamico eseguire le operazioni seguenti:
- Selezionare **Nome allegato**.
- Selezionare **Contenuto allegato**.
- Selezionare **Sì** nel campo **Is HTML** (HTML).

![Schermata di configurazione della posta elettronica di Office 365](./media/app-insights-automate-with-flow/flow7.png)
### <a name="step-7-save-and-test-your-flow"></a>Passaggio 7: Salvare e testare il flusso
- Assegnare un nome al flusso e fare clic su **Crea flusso**.

![Schermata di creazione di Microsoft Flow](./media/app-insights-automate-with-flow/flow8.png)

È possibile attendere che il trigger esegua l'azione oppure è possibile eseguire il flusso immediatamente [eseguendo il trigger su richiesta](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Quando il flusso è in esecuzione, i destinatari specificati nell'elenco di posta elettronica ricevono un messaggio simile al seguente:

![Esempio di messaggio di posta elettronica](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla creazione di [query di Analisi](app-insights-analytics-using.md).
- Altre informazioni su [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->






