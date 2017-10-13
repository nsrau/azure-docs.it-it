---
title: Inviare avvisi da Azure Application Insights | Microsoft Docs
description: Esercitazione per l'invio di avvisi in risposta agli errori dell'applicazione con Azure Application Insights.
keywords: 
author: bwren
ms.author: bwren
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: ce297476cfdf80564c6c0cb835955a146f7a1c12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorare e inviare avvisi sullo stato di integrità dell'applicazione con Azure Application Insights

Azure Application Insights consente di monitorare l'applicazione e inviare avvisi quando l'applicazione non è disponibile, si verificano errori o si riscontrano problemi di prestazioni.  Questa esercitazione illustra il processo di creazione di test per controllare in modo continuativo la disponibilità dell'applicazione e per inviare diversi tipi di avvisi in risposta ai problemi rilevati.  Si apprenderà come:

> [!div class="checklist"]
> * Creare test di disponibilità per controllare in modo continuativo la risposta dell'applicazione
> * Inviare un messaggio e-mail agli amministratori quando si verifica un problema
> * Creare avvisi in base a metriche delle prestazioni 
> * Usare un'app per la logica per inviare dati di telemetria riepilogati in una pianificazione.


## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con i carichi di lavoro seguenti:
    - Sviluppo Web e ASP.NET
    - Sviluppo di Azure
    - Distribuire un'applicazione .NET in Azure e [abilitare Application Insights SDK](app-insights-asp-net.md). 


## <a name="log-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Creare un test di disponibilità
I test di disponibilità in Application Insights consentono di testare automaticamente l'applicazione da diverse località in tutto il mondo.   In questa esercitazione verrà eseguito un semplice test per verificare che l'applicazione sia disponibile.  È anche possibile creare una procedura dettagliata completa per verificarne il funzionamento dettagliato. 

1. Selezionare **Application Insights** e quindi selezionare la sottoscrizione in uso.  
1. Selezionare **Disponibilità** nel menu **Analisi** e quindi fare clic su **Aggiungi test**.
 
    ![Aggiungere un test di disponibilità](media/app-insights-tutorial-alert/add-test.png)

2. Digitare un nome per il test e lasciare le altre impostazioni predefinite.  Ciò richiede la home page dell'applicazione ogni 5 minuti da 5 aree geografiche diverse. 
3. Selezionare **Avvisi** per aprire il pannello **Avvisi**, in cui è possibile definire i dettagli sulla modalità di risposta in caso di esito negativo del test. Digitare un indirizzo e-mail a cui inviare gli avvisi quando vengono soddisfatti i criteri di avviso.  Se si vuole, è possibile digitare l'indirizzo di un webhook da chiamare quando vengono soddisfatti i criteri di avviso.

    ![Creare un test](media/app-insights-tutorial-alert/create-test.png)
 
4. Tornare al pannello del test dove, dopo pochi minuti, si dovrebbero iniziare a vedere i risultati del test di disponibilità.  Fare clic sul nome del test per visualizzare i dettagli da ogni località.  Il grafico a dispersione mostra l'esito positivo e la durata di ogni test.

    ![Dettagli del test](media/app-insights-tutorial-alert/test-details.png)

5.  È possibile eseguire il drill-down nei dettagli di qualsiasi test specifico facendo clic sul relativo punto nel grafico a dispersione.  L'esempio seguente mostra i dettagli per una richiesta non riuscita.

    ![Risultato del test](media/app-insights-tutorial-alert/test-result.png)
  
6. Se i criteri di avviso vengono soddisfatti, verrà inviato un messaggio e-mail simile al seguente all'indirizzo specificato.

    ![Messaggio e-mail di avviso](media/app-insights-tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>Creare un avviso dalle metriche
Oltre a inviare avvisi da un test di disponibilità, è possibile creare un avviso da qualsiasi metrica delle prestazioni raccolta per l'applicazione.

2. Selezionare **Avvisi** nel menu **Configura**.  Verrà aperto il pannello Avvisi di Azure.  Potrebbero essere presenti altre regole di avviso configurate per altri servizi.
3. Fare clic su **Aggiungi avviso per la metrica**.  Verrà aperto il pannello per la creazione di una nuova regola di avviso.

    ![Aggiungi avviso per la metrica](media/app-insights-tutorial-alert/add-metric-alert.png)

4. Digitare un **Nome** per la regola di avviso e selezionare l'applicazione nell'elenco a discesa **Risorsa**.
5. Selezionare una **Metrica** da campionare.  Verrà visualizzato un grafico per indicare il valore di questa richiesta nelle ultime 24 ore.  Ciò consente di impostare la condizione per la metrica.

    ![Aggiungi regola di avviso](media/app-insights-tutorial-alert/add-alert-01.png)

6. Specificare una **Condizione** e una **Soglia** per l'avviso, ovvero il numero di volte in cui la metrica deve essere superata perché venga creato un avviso. 
6. In **Notifica tramite** selezionare la casella di controllo **Invia messaggio a proprietari, collaboratori e lettori** per inviare un messaggio e-mail a questi utenti quando la condizione di avviso viene soddisfatta e aggiungere l'indirizzo e-mail di eventuali destinatari aggiuntivi.  È inoltre possibile specificare un webhook o un'app per la logica da eseguire quando viene soddisfatta la condizione,  che sarà possibile usare per tentare di attenuare il problema rilevato. 

    ![Aggiungi regola di avviso](media/app-insights-tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>Inviare informazioni in modo proattivo
Gli avvisi vengono creati in risposta a un particolare set di problemi identificati nell'applicazione e in genere si usano per le condizioni critiche che richiedono un'attenzione immediata.  È possibile ottenere informazioni sull'applicazione in modo proattivo con un'app per la logica che viene eseguita automaticamente in base a una pianificazione.  Ad esempio, è possibile fare in modo che gli amministratori ricevano quotidianamente un messaggio e-mail con informazioni di riepilogo che richiedono un'ulteriore valutazione.

Per maggiori dettagli sulla creazione di un'app per la logica con Application Insights, vedere [Automatizzare i processi di Application Insights con app per la logica](automate-with-logic-apps.md).

## <a name="next-steps"></a>Passaggi successivi
Dopo avere appreso come creare avvisi per gli eventuali problemi, passare all'esercitazione successiva per imparare ad analizzare il modo in cui gli utenti interagiscono con l'applicazione.

> [!div class="nextstepaction"]
> [Comprendere gli utenti](app-insights-tutorial-users.md)