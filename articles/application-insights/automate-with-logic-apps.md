---
title: Automatizzare i processi di Azure Application Insights con un'app per la logica di Azure.
description: Informazioni su come automatizzare in poco tempo i processi ripetibili aggiungendo Connettore di Application Insights all'app per la logica di Azure.
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
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 571a76253fa62a89f325e0c295e9a7e98e201079
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-an-azure-logic-app"></a>Automatizzare i processi di Application Insights con un'app per la logica di Azure

Ci si trova spesso a eseguire ripetutamente le stesse query sui dati di telemetria per verificare il corretto funzionamento del servizio? Si desidera automatizzare queste query per individuare tendenze e anomalie e creare dei flussi di lavoro basati su queste informazioni? Connettore di Application Insights (anteprima) per App per la logica di Azure serve proprio a questo.
Con questa integrazione è ora possibile automatizzare numerosi processi senza dover scrivere codice. È possibile creare l'app per la logica con Connettore di Application Insights per automatizzare rapidamente qualsiasi processo di Application Insights. È possibile aggiungere anche altre azioni. In App per la logica sono disponibili centinaia di azioni. È ad esempio possibile inviare automaticamente una notifica di posta elettronica o creare un bug in Visual Studio Team Services come parte dell'app per la logica. È inoltre possibile usare uno dei numerosi [modelli](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) disponibili per App per la logica. Questi modelli velocizzano il processo di creazione dell'app per la logica. 

## <a name="tutorial-for-creating-an-azure-logic-app-for-application-insights"></a>Esercitazione per la creazione di un'app per la logica di Azure per Application Insights

Questa esercitazione illustra come creare un'app per la logica di Azure che usa l'algoritmo di cluster automatico di Analisi per raggruppare gli attributi dei dati per un'applicazione Web. Il flusso invia automaticamente i risultati tramite posta elettronica. Questo è solo un esempio di utilizzo congiunto di Application Insights - Analisi e App per la logica di Azure. 

### <a name="step-1-create-a-logic-app"></a>Passaggio 1: Creare un'app per la logica
1. Accedere a https://portal.azure.com.
2. Creare una nuova app per la logica dal menu Nuovo/Web e dispositivi mobili.

![Schermata della nuova app per la logica](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Passaggio 2: Creare un trigger per l'app per la logica
1.  In Progettazione app per la logica, in Inizia con un trigger comune scegliere Ricorrenza.
2.  Impostare la Frequenza su Giorno con un Intervallo di 1.

![Finestra di dialogo Trigger dell'app per la logica](./media/automate-with-logic-apps/logicapp2.png)

![Finestra di dialogo della frequenza dell'app per la logica](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-azure-application-insights-action"></a>Passaggio 3: Aggiungere un'azione di Azure Application Insights
1. Fare clic su **Nuovo passaggio** e quindi su **Aggiungi un'azione**.
2. Cercare Azure Application Insights.
3. Fare clic su Application Insights - Visualize Analytics query Preview (Application Insights - Visualizza query di Analisi Anteprima).

![Schermata di esecuzione della query di Analisi](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Passaggio 4: Connettersi a una risorsa di Application Insightss

**Prerequisito**

Per completare questo passaggio, è necessario disporre di un ID applicazione e di una chiave API per la risorsa. È possibile recuperare queste informazioni dal portale di Azure, come illustrato nel diagramma seguente:

![ID applicazione nel portale di Azure](./media/automate-with-logic-apps/appid.png) 

- Specificare un nome per la connessione insieme all'ID applicazione e alla chiave API.

![Schermata di connessione di Flow](./media/automate-with-logic-apps/flow3.png)

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
![Schermata di configurazione della query di Analisi](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-app-to-send-email"></a>Passaggio 6: Configurare l'app per l'invio tramite posta elettronica

1. Fare clic su **Nuovo passaggio** e quindi selezionare **Aggiungi un'azione**.
2. Cercare Office 365 Outlook.
3. Fare clic su Office 365 Outlook – Send an email (Office 365 Outlook: invia un messaggio di posta elettronica).
![Selezione di Office 365 Outlook](./media/automate-with-logic-apps/flow2b.png)

4. Nell'azione di invio di posta elettronica aggiungere i dati seguenti:
 - Specificare l'indirizzo di posta elettronica del destinatario.
 - Specificare l'oggetto del messaggio di posta elettronica.
 - Posizionare il cursore nel campo **Corpo** e dal menu di contenuto dinamico che viene visualizzato a destra selezionare **Corpo**.
 - Fare clic su **Mostra opzioni avanzate**.

 ![Configurazione di Office 365 Outlook](./media/automate-with-logic-apps/flow5.png)

5. Dal menu di contenuto dinamico eseguire le operazioni seguenti:
- Selezionare **Nome allegato**.
- Selezionare **Contenuto allegato**.
- Selezionare **Sì** nel campo **Is HTML** (HTML).

![Schermata di configurazione della posta elettronica di Office 365](./media/automate-with-logic-apps/flow7.png)
### <a name="step-7-save-and-test-your-logic-app"></a>Passaggio 7: Salvare e testare l'app per la logica
1. Fare clic su **Salva** per salvare le modifiche.
1. È possibile attendere che il trigger esegua l'app per la logica oppure è possibile eseguirla immediatamente scegliendo **Esegui**.

![Schermata di creazione dell'app per la logica](./media/automate-with-logic-apps/step7.png)

Quando l'app per la logica è in esecuzione, i destinatari specificati nell'elenco di posta elettronica ricevono un messaggio simile al seguente:

![Messaggio di posta elettronica dell'app per la logica](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla creazione di [query di Analisi](app-insights-analytics-using.md).
- Altre informazioni su [App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->






