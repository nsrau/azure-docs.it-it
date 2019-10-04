---
title: Automatizzare i processi di Azure Application Insights con Microsoft Flow
description: Informazioni su come usare Microsoft Flow per automatizzare in poco tempo i processi ripetibili usando il connettore di Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mbullwin
ms.openlocfilehash: 541d5b70ee56d62831f0947e64b9522e17a07dd9
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195031"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizzare i processi di Azure Application Insights con il connettore per Microsoft Flow

Ci si trova spesso a eseguire ripetutamente le stesse query sui dati di telemetria per verificare il corretto funzionamento del servizio? Si vuole automatizzare queste query per trovare tendenze e anomalie e creare quindi flussi di lavoro basati su queste informazioni? Il connettore applicazione Azure Insights per Microsoft Flow è lo strumento ideale per questi scopi.

Con questa integrazione è ora possibile automatizzare numerosi processi senza dover scrivere codice. Dopo avere creato un flusso usando un'azione di Application Insights, il flusso esegue automaticamente la query di Application Insights - Analisi.

È possibile aggiungere anche altre azioni. In Microsoft Flow sono disponibili centinaia di azioni. Ad esempio, è possibile usare Microsoft Flow per inviare automaticamente una notifica di posta elettronica o per creare un bug in Azure DevOps. È anche possibile usare uno dei numerosi [modelli](https://ms.flow.microsoft.com/connectors/shared_applicationinsights/?slug=azure-application-insights) disponibili per il connettore per Microsoft Flow. Questi modelli velocizzano il processo di creazione di un flusso.

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). -->

## <a name="create-a-flow-for-application-insights"></a>Creare un flusso per Application Insights

In questa esercitazione si apprenderà come creare un flusso che usa l'algoritmo di cluster automatico di analisi per raggruppare gli attributi dei dati per un'applicazione Web. Il flusso invia automaticamente i risultati tramite posta elettronica. Questo è solo un esempio di uso congiunto di Microsoft Flow e Application Insights - Analisi.

### <a name="step-1-create-a-flow"></a>Passaggio 1: Creare un flusso

1. Accedere a [Microsoft Flow](https://flow.microsoft.com) e quindi selezionare **Flussi personali**.
2. Fare clic su **nuovo** e quindi su **pianificato, da zero**.

    ![Crea nuovo flusso da spazio pianificato](./media/automate-with-flow/1-create.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Passaggio 2: Creare un trigger per il flusso

1. Nella finestra popup **creare un flusso pianificato**, compilare il nome del flusso e la frequenza con cui si vuole eseguire il flusso.

    ![Impostare la ricorrenza della pianificazione con l'immissione di frequenza e intervallo](./media/automate-with-flow/2-schedule.png)

1. Fare clic su **Create**(Crea).

### <a name="step-3-add-an-application-insights-action"></a>Passaggio 3: aggiungere un'azione di Application Insights

1. Cercare **Application Insights**.
2. Fare clic su **applicazione Azure Insights-Visualizza query di analisi**.

    ![Scegliere un'azione: Query di visualizzazione di applicazione Azure Insights](./media/automate-with-flow/3-visualize.png)

3. Selezionare **Nuovo passaggio**.

### <a name="step-4-connect-to-an-application-insights-resource"></a>Passaggio 4: Connettersi a una risorsa di Application Insights

Per completare questo passaggio, sono necessari un ID applicazione e una chiave API per la risorsa. È possibile recuperare queste informazioni dal portale di Azure, come illustrato nell'immagine seguente:

![ID applicazione nel portale di Azure](./media/automate-with-flow/5apiaccess.png)

![Chiave API nella portale di Azure](./media/automate-with-flow/6apikey.png)

Specificare un nome per la connessione, con l'ID applicazione e la chiave API.

   ![Finestra di connessione di Microsoft Flow](./media/automate-with-flow/4-connection.png)

Se la casella connessione non viene visualizzata immediatamente e passa direttamente all'immissione della query, fare clic sui puntini di sospensione nella parte superiore destra della casella. Selezionare quindi connessioni personali o utilizzarne una esistente.

Fare clic su **Create**(Crea).

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Passaggio 5: Specificare la query e il tipo di grafico di Analytics
Questa query di esempio seleziona le richieste non riuscite entro l'ultimo giorno e le correla alle eccezioni che si sono verificate durante l'operazione. La correlazione eseguita da Analisi si basa sull'identificatore operation_Id. La query segmenta quindi i risultati usando l'algoritmo di cluster automatico.

Quando si creano query, verificare che funzionino correttamente in Analisi prima di aggiungerle al flusso.

- Aggiungere la query di analisi seguente e selezionare il tipo di grafico di tabella HTML. Quindi selezionare **nuovo passaggio**.

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
    
    ![Finestra di configurazione della query di Analisi](./media/automate-with-flow/5-query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Passaggio 6: Configurare il flusso per l'invio di un messaggio di posta elettronica

1. Cercare **Office 365 Outlook**.
2. Fare clic su **Office 365 Outlook - Send an email** (Office 365 Outlook - Invia un messaggio di posta elettronica).

    ![Finestra di selezione di Office 365 Outlook](./media/automate-with-flow/6-outlook.png)

1. Nella finestra **Invia un messaggio di posta elettronica** :

   a. Digitare l'indirizzo e-mail del destinatario.

   b. Digitare l'oggetto del messaggio di posta elettronica.

   c. Fare clic in un punto qualsiasi della casella **Corpo** e scegliere **Corpo** dal menu di contenuto dinamico che viene visualizzato a destra.

   e. Selezionare **Mostra opzioni avanzate**

1. Nel menu contenuto dinamico:

    a. Selezionare **Nome allegato**.

    b. Selezionare **Contenuto allegato**.
    
    c. Nella casella **HTML** selezionare **Sì**.

    ![Configurazione di Office 365 Outlook](./media/automate-with-flow/7-email.png)

### <a name="step-7-save-and-test-your-flow"></a>Passaggio 7: Salvare e testare il flusso

Fare clic su **Save**.

È possibile attendere che il trigger esegua l'azione oppure fare clic sul ![ **test** dell'icona](./media/automate-with-flow/testicon.png) del test del becher nella parte superiore.

Dopo aver selezionato il **test**:

1. Selezionare **eseguirò l'azione trigger**.
2. Selezionare **Esegui flusso**.

Quando viene eseguito il flusso, i destinatari specificati nell'elenco di posta elettronica ricevono un messaggio di posta elettronica simile a quello riportato di seguito.

![Esempio di messaggio di posta elettronica](./media/automate-with-flow/flow9.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla creazione di [query di Analisi](../../azure-monitor/log-query/get-started-queries.md).
- Altre informazioni su [Microsoft Flow](https://ms.flow.microsoft.com).

<!--Link references-->