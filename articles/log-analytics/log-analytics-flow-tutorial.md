---
title: Automatizzare i processi di Azure Log Analytics con Microsoft Flow
description: Informazioni su come usare Microsoft Flow per automatizzare in poco tempo i processi ripetibili usando il connettore di Azure Log Analytics.
services: log-analytics
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: log-analytics
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 4955f90de06cd720d78c5bb60c0adcd7dc633245
ms.contentlocale: it-it
ms.lasthandoff: 08/10/2017

---

# <a name="automate-log-analytics-processes-with-the-connector-for-microsoft-flow"></a>Automatizzare i processi di Log Analytics con il connettore per Microsoft Flow
[Microsoft Flow](https://ms.flow.microsoft.com) consente di creare flussi di lavoro automatizzati che includono centinaia di azioni per un'ampia gamma di servizi. L'output di un'azione può essere usato come input per un'altra. È così possibile integrare servizi diversi.  Il connettore di Azure Log Analytics per Microsoft Flow consente di creare flussi di lavoro che includono dati recuperati da ricerche log in Log Analytics.

È ad esempio possibile usare Microsoft Flow per inserire i dati di Log Analytics in una notifica di posta elettronica da Office 365, creare un bug in Visual Studio Team Services o inviare un messaggio Slack.  È possibile attivare un flusso di lavoro da una semplice pianificazione o con un'azione in un servizio connesso, ad esempio quando viene ricevuto un messaggio di posta elettronica o un tweet.  


> [!NOTE]
> Il connettore di Azure Log Analytics per Microsoft Flow richiede che l'area di lavoro in uso sia aggiornata al nuovo linguaggio di query di Log Analytics. In [Aggiornare l'area di lavoro di Azure Log Analytics alla nuova ricerca log](log-analytics-log-search-upgrade.md) sono disponibili altre informazioni sul nuovo linguaggio e istruzioni per l'aggiornamento dell'area di lavoro.  

L'esercitazione descritta in questo articolo mostra come creare un flusso che invia automaticamente i risultati di una ricerca log di Log Analytics tramite posta elettronica. Si tratta di un esempio fra tanti del possibile utilizzo di Log Analytics in Microsoft Flow. 


## <a name="step-1-create-a-flow"></a>Passaggio 1: Creare un flusso
1. Accedere a [Microsoft Flow](http://flow.microsoft.com) e quindi selezionare **Flussi personali**.
2. Fare clic su **+ Crea da zero**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Passaggio 2: Creare un trigger per il flusso
1. Fare clic su **Search hundreds of connectors and triggers** (Cerca centinaia di connettori e trigger).
2. Digitare **Pianificazione** nella casella di ricerca.
3. Selezionare **Pianificazione** e quindi selezionare **Pianificazione - Ricorrenza**.
4. Nella casella **Frequenza** selezionare **Giorno** e nella casella **Intervallo** immettere **1**.<br><br>![Finestra di dialogo del trigger di Microsoft Flow](media/log-analytics-flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Passaggio 3: Aggiungere un'azione di Log Analytics
1. Fare clic su **+ Nuovo passaggio** e quindi su **Aggiungi un'azione**.
2. Cercare **Log Analytics**.
3. Fare clic su **Azure Log Analytics - Run query and visualize results** (Eseguire una query e visualizzare i risultati).<br><br>![Finestra di esecuzione query di Log Analytics](media/log-analytics-flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Passaggio 4: Configurare l'azione di Log Analytics

1. Specificare i dettagli dell'area di lavoro, inclusi l'ID sottoscrizione, il gruppo di risorse e il nome dell'area di lavoro.
2. Nella casella **Query** aggiungere la query di Log Analytics seguente.  Questa è una semplice query di esempio che può essere sostituita con una qualsiasi altra query che restituisce dati.
```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computerindow. 
```

2. Selezionare **HTML Table** (Tabella HTML) per **Chart Type** (Tipo di grafico).<br><br>![Azione di Log Analytics](media/log-analytics-flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Passaggio 5: Configurare il flusso per l'invio di un messaggio di posta elettronica

1. Fare clic su **Nuovo passaggio** e quindi su **+ Aggiungi un'azione**.
2. Cercare **Office 365 Outlook**.
3. Fare clic su **Office 365 Outlook - Send an email** (Office 365 Outlook: invia un messaggio di posta elettronica).<br><br>![Finestra di selezione di Office 365 Outlook](media/log-analytics-flow-tutorial/flow04.png)

4. Specificare l'indirizzo di posta elettronica del destinatario nella casella **A** e l'oggetto del messaggio nella casella **Oggetto**.
5. Fare clic nella casella **Corpo**.  Verrà aperta la finestra **Contenuto dinamico** con i valori delle azioni precedenti.  
6. Selezionare **Corpo**.  Questi sono i risultati della query nell'azione di Log Analytics.
6. Fare clic su **Mostra opzioni avanzate**.
7. Nella casella **HTML** selezionare **Sì**.<br><br>![Finestra di configurazione della posta elettronica di Office 365](media/log-analytics-flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Passaggio 6: Salvare e testare il flusso
1. Nella casella **Nome flusso** aggiungere un nome per il flusso e quindi fare clic su **Crea flusso**.<br><br>![Salva flusso](media/log-analytics-flow-tutorial/flow06.png)
2. Il flusso è ora creato e verrà eseguito dopo un giorno come da pianificazione. 
3. Per testare subito il flusso, fare clic su **Esegui ora** e quindi su **Esegui flusso**.<br><br>![Esegui flusso](media/log-analytics-flow-tutorial/flow07.png)
3. Dopo che il flusso è completato, controllare la posta elettronica del destinatario specificato.  Dovrebbe essere presente un messaggio di posta elettronica con un corpo simile al seguente:<br><br>![Esempio di messaggio di posta elettronica](media/log-analytics-flow-tutorial/flow08.png)


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Ricerche log di Log Analytics](log-analytics-log-search-new.md).
- Altre informazioni su [Microsoft Flow](https://ms.flow.microsoft.com).




