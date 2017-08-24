---
title: Automatizzare i processi di Azure Application Insights con app per la logica.
description: Informazioni su come automatizzare in poco tempo i processi ripetibili aggiungendo il connettore di Application Insights all'app per la logica.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 06bfb75a07b7902fcf245271def5d40e9941a89e
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---

# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatizzare i processi di Application Insights con app per la logica

Ci si trova spesso a eseguire ripetutamente le stesse query sui dati di telemetria per verificare il corretto funzionamento del servizio? Si vuole automatizzare queste query per trovare tendenze e anomalie e creare quindi flussi di lavoro basati su queste informazioni? Il connettore di Azure Application Insights (anteprima) per le app per la logica è lo strumento ideale.

Con questa integrazione è possibile automatizzare numerosi processi senza dover scrivere una sola riga di codice. È possibile creare un'app per la logica con il connettore di Application Insights per automatizzare rapidamente qualsiasi processo di Application Insights. 

È possibile aggiungere anche altre azioni. App per la logica è una funzione del Servizio app di Azure che offre centinaia di azioni. Usando un'app per la logica, è ad esempio possibile inviare automaticamente una notifica di posta elettronica o creare un bug in Visual Studio Team Services. È anche possibile usare uno dei numerosi [modelli](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) disponibili per velocizzare il processo di creazione dell'app per la logica. 

## <a name="create-a-logic-app-for-application-insights"></a>Creare un app per la logica per Application Insights

Questa esercitazione illustra come creare un'app per la logica che usa l'algoritmo di cluster automatico di Analisi per raggruppare gli attributi dei dati per un'applicazione Web. Il flusso invia automaticamente i risultati tramite posta elettronica. Questo è solo un esempio di uso congiunto di Application Insights - Analisi e App per la logica. 

### <a name="step-1-create-a-logic-app"></a>Passaggio 1: Creare un'app per la logica
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro **Nuovo** selezionare **Web e dispositivi mobili** e scegliere **App per la logica**.

    ![Finestra della nuova app per la logica](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Passaggio 2: Creare un trigger per l'app per la logica
1. In **Inizia con un trigger comune** nella finestra **Progettazione app per la logica** scegliere **Ricorrenza**.

    ![Finestra di progettazione di app per la logica](./media/automate-with-logic-apps/logicapp2.png)

2. Nella casella **Frequenza** selezionare **Giorno** e nella casella **Intervallo** digitare **1**.

    !["Ricorrenza" nella finestra Progettazione app per la logica](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-application-insights-action"></a>Passaggio 3: Aggiungere un'azione di Application Insights
1. Fare clic su **Nuovo passaggio** e quindi su **Aggiungi un'azione**.

2. Nella casella di ricerca **Scegliere un'azione** digitare **Azure Application Insights**.

3. Fare clic su **Application Insights - Visualize Analytics query Preview** (Application Insights - Visualizza query di Analisi Anteprima) in **Azioni**.

    !["Scegliere un'azione" nella finestra Progettazione app per la logica](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Passaggio 4: Connettersi a una risorsa di Application Insights

Per completare questo passaggio, sono necessari un ID applicazione e una chiave API per la risorsa. È possibile recuperare queste informazioni dal portale di Azure, come illustrato nell'immagine seguente:

![ID applicazione nel portale di Azure](./media/automate-with-logic-apps/appid.png) 

Specificare un nome per la connessione, l'ID applicazione e la chiave API.

![Connessione per il flusso nella finestra Progettazione app per la logica](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Passaggio 5: Specificare la query e il tipo di grafico di Analisi
In questo esempio la query seleziona le richieste non riuscite entro l'ultimo giorno e le correla alle eccezioni che si sono verificate durante l'operazione. La correlazione delle richieste non riuscite eseguita da Analisi si basa sull'identificatore operation_Id. La query segmenta quindi i risultati usando l'algoritmo di cluster automatico. 

Quando si creano query, verificare che funzionino correttamente in Analisi prima di aggiungerle al flusso.

1. Nella casella **Query** aggiungere la query di Analisi seguente: 

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

2. Nella casella **Tipo di grafico** selezionare **Tabella HTML**.

    ![Finestra di configurazione della query di Analisi](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Passaggio 6: Configurare l'app per la logica per l'invio tramite posta elettronica

1. Fare clic su **Nuovo passaggio** e selezionare **Aggiungi un'azione**.

2. Nella casella di ricerca digitare **Office 365 Outlook**.

3. Fare clic su **Office 365 Outlook - Send an email** (Office 365 Outlook: invia un messaggio di posta elettronica).

    ![Selezione di Office 365 Outlook](./media/automate-with-logic-apps/flow2b.png)

4. Nella finestra **Invia un messaggio di posta elettronica** seguire questa procedura:

   a. Digitare l'indirizzo e-mail del destinatario.

   b. Digitare l'oggetto del messaggio di posta elettronica.

   c. Fare clic in un punto qualsiasi della casella **Corpo** e scegliere **Corpo** dal menu di contenuto dinamico che viene visualizzato a destra.

   d. Fare clic su **Mostra opzioni avanzate**.

      ![Configurazione di Office 365 Outlook](./media/automate-with-logic-apps/flow5.png)

5. Nel menu di contenuto dinamico seguire questa procedura:

    a. Selezionare **Nome allegato**.

    b. Selezionare **Contenuto allegato**.
    
    c. Nella casella **HTML** selezionare **Sì**.

      ![Schermata di configurazione della posta elettronica di Office 365](./media/automate-with-logic-apps/flow7.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Passaggio 7: Salvare e testare l'app per la logica
* Fare clic su **Salva** per salvare le modifiche.

È possibile attendere che il trigger esegua l'app per la logica oppure è possibile eseguirla immediatamente scegliendo **Esegui**.

![Schermata di creazione dell'app per la logica](./media/automate-with-logic-apps/step7.png)

Quando l'app per la logica è in esecuzione, i destinatari specificati nell'elenco di posta elettronica ricevono un messaggio simile al seguente:

![Messaggio di posta elettronica dell'app per la logica](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla creazione di [query di Analisi](app-insights-analytics-using.md).
- Altre informazioni su [App per la logica](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->






