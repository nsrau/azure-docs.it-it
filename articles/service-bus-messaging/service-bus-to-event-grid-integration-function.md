---
title: Gestire gli eventi del bus di servizio tramite Griglia di eventi con Funzioni di Azure
description: Questo articolo illustra la procedura per gestire gli eventi del bus di servizio tramite Griglia di eventi con Funzioni di Azure.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: afc0a5bf9b83363d1f4baab955b55148fe3a8498
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95818481"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>Esercitazione: Rispondere agli eventi del bus di servizio di Azure ricevuti tramite Griglia di eventi di Azure usando Funzioni di Azure
Questa esercitazione descrive come rispondere agli eventi del bus di servizio di Azure ricevuti tramite Griglia di eventi di Azure usando Funzioni di Azure e App per la logica di Azure. 

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Creare uno spazio dei nomi del bus di servizio
> * Preparare un'applicazione di esempio per inviare messaggi
> * Inviare messaggi all'argomento del bus di servizio
> * Ricevere messaggi usando App per la logica
> * Configurare una funzione di test in Azure
> * Connettere la funzione e lo spazio dei nomi tramite Griglia di eventi
> * Ricevere messaggi usando Funzioni di Azure

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>Prerequisiti aggiuntivi
Installare [Visual Studio 2019](https://www.visualstudio.com/vs) e includere il carico di lavoro **Sviluppo di Azure**. Questo carico di lavoro include gli **strumenti di Funzioni di Azure**, necessari per creare, compilare e distribuire progetti di Funzioni di Azure in Visual Studio. 

## <a name="deploy-the-function-app"></a>Distribuire l'app per le funzioni 

>[!NOTE]
> Per altre informazioni sulla creazione e sulla distribuzione di un'app di Funzioni di Azure, vedere [Sviluppare Funzioni di Azure con Visual Studio](../azure-functions/functions-develop-vs.md)

1. Aprire il file **ReceiveMessagesOnEvent.cs** dal progetto **FunctionApp1** della soluzione **SBEventGridIntegration.sln**. 
1. Sostituire `<SERCICE BUS NAMESPACE - CONNECTION STRING>` con la stringa di connessione allo spazio dei nomi del bus di servizio. Dovrebbe essere la stessa di quella usata nel file **Program.cs** del progetto **MessageSender** nella stessa soluzione. 
1. Fare clic con il pulsante destro del mouse su **FunctionApp1** e scegliere **Pubblica**. 
1. Nella pagina **Pubblica** selezionare **Avvia**. Questi passaggi possono essere diversi da quelli riscontrati, ma il processo di pubblicazione dovrebbe essere simili. 
1. Nella pagina **Destinazione** della procedura guidata **Pubblica** selezionare **Azure** per **Destinazione**. 
1. Nella pagina **Destinazione specifica** selezionare **App per le funzioni di Azure (Windows)** . 
1. Nella pagina **Istanza di Funzioni** selezionare **Crea una nuova istanza di Funzioni di Azure**. 
1. Nella pagina **App per le funzioni (Windows)** seguire questa procedura:
    1. Immettere un **nome** per l'app per le funzioni.
    1. Selezionare una **sottoscrizione** di Azure.
    1. Selezionare un **gruppo di risorse** esistente o crearne uno nuovo. Per questa esercitazione, selezionare il gruppo di risorse che include lo spazio dei nomi del bus di servizio. 
    1. Selezionare un **tipo di piano** per il servizio app.
    1. Selezionare una **posizione**. Selezionare la stessa località dello spazio dei nomi del bus di servizio. 
    1. Selezionare un account di **Archiviazione di Azure** esistente oppure selezionare **Nuovo** per crearne uno nuovo da usare con l'app per le funzioni. 
    1. Selezionare **Crea** per creare l'app per le funzioni. 
1. Nella pagina **Istanza di Funzioni** della procedura guidata **Pubblica** selezionare **Fine**. 
1. Nella pagina **Pubblica** in Visual Studio selezionare **Pubblica** per pubblicare l'app per le funzioni in Azure. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="Pubblicare l'app per le funzioni":::    
1. Nella finestra **Output** visualizzare i messaggi relativi a compilazione e pubblicazione e verificare che entrambe le operazioni siano riuscite. 
1. Ora nella pagina **Pubblica** selezionare **Gestisci nel portale di Azure**. 
1. Nella pagina **App per le funzioni** del portale di Azure selezionare **Funzioni** nel menu sinistro e verificare che vengano visualizzate due funzioni: 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="Due funzioni per il gestore del trigger di Griglia di eventi e del trigger HTTP":::

    > [!NOTE]
    > `EventGridTriggerFunction` usa un [trigger di Griglia di eventi](../azure-functions/functions-bindings-event-grid-trigger.md) e `HTTPTriggerFunction` usa un [trigger HTTP](../azure-functions/functions-bindings-http-webhook-trigger.md).
1. Selezionare **EventGridTriggerFunction** nell'elenco. È consigliabile usare il trigger di Griglia di eventi in Funzioni di Azure, perché offre alcuni vantaggi in più rispetto al trigger HTTP. Per i dettagli, vedere [Funzione di Azure come gestore degli eventi di Griglia di eventi](../event-grid/handler-functions.md).
1. Nella pagina **Funzione** per **EventGridTriggerFunction** selezionare **Monitoraggio** nel menu sinistro. 
1. Selezionare **Configura** per configurare Application Insights in modo da acquisire il log di chiamate. Usare questa pagina per monitorare le esecuzioni della funzione dopo aver ricevuto eventi del bus di servizio da Griglia di eventi. 
1. Nella pagina **Application Insights** immettere un nome per la risorsa, selezionare una **località** e quindi selezionare **OK**. 
1. Selezionare la funzione **EventGridTriggerFunction** in alto (menu della barra di navigazione) per tornare nella pagina **Funzione**. 
1. Verificare che sia aperta la pagina **Monitoraggio**. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="Pagina Monitoraggio per la funzione prima delle chiamate":::
    
    Mantenere aperta questa pagina in una scheda del Web browser. Verrà aggiornata in seguito per vedere le chiamate per questa funzione.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Connettere la funzione e lo spazio dei nomi tramite Griglia di eventi
In questa sezione la funzione e lo spazio dei nomi del bus di servizio vengono collegati tra loro tramite il portale di Azure. 

Per creare una sottoscrizione di Griglia di eventi di Azure, seguire questa procedura:

1. Nel portale di Azure passare allo spazio dei nomi e quindi, nel riquadro sinistro, selezionare **Eventi**. Verrà aperta la finestra dello spazio dei nomi, con due sottoscrizioni di Griglia di eventi visualizzate nel riquadro destro. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="Bus di servizio - pagina Eventi":::
2. Selezionare **+ Sottoscrizione di eventi** sulla barra degli strumenti. 
3. Nella pagina **Crea sottoscrizione di eventi** seguire questa procedura:
    1. Immettere un **nome** per la sottoscrizione. 
    2. Immettere un **nome** per l'**argomento di sistema**. Gli argomenti di sistema sono argomenti creati per le risorse di Azure, ad esempio l'account di archiviazione di Azure e il bus di servizio di Azure. Per altre informazioni sugli argomenti di sistema, vedere [Panoramica degli argomenti di sistema](../event-grid/system-topics.md).
    2. Selezionare **Funzione di Azure** per **Tipo di endpoint** e fare clic su **Seleziona endpoint**. 

        ![Bus di servizio - sottoscrizione di Griglia di eventi](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Nella pagina **Seleziona funzione di Azure** selezionare la sottoscrizione, il gruppo di risorse, l'app per le funzioni, lo slot e la funzione, quindi selezionare **Conferma selezione**. 

        ![Funzione - selezione dell'endpoint](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Nella pagina **Crea sottoscrizione di eventi** passare alla scheda **Filtri** ed eseguire queste attività:
        1. Selezionare **Abilita filtro per l'oggetto**
        2. Immettere il nome della sottoscrizione dell'argomento del bus di servizio (**S1**) creato in precedenza.
        3. Selezionare il pulsante **Crea**. 

            ![Filtro della sottoscrizione di eventi](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Passare alla scheda **Sottoscrizione di eventi** della pagina **Eventi** e verificare che la sottoscrizione di eventi sia presente nell'elenco.

    ![Sottoscrizione di eventi nell'elenco](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>Monitorare l'app per le funzioni
I messaggi inviati in precedenza all'argomento del bus di servizio vengono inoltrati alla sottoscrizione (S1). Griglia di eventi inoltra i messaggi presenti nella sottoscrizione alla funzione di Azure. In questo passaggio dell'esercitazione verificare che la funzione sia stata chiamata e visualizzare i messaggi informativi registrati. 

1. Nella pagina dell'app per le funzioni di Azure passare alla scheda **Monitoraggio** se non è già attiva. Si dovrebbe vedere una voce per ogni messaggio pubblicato nell'argomento del bus di servizio. Se non si vedono, aggiornare la pagina dopo aver atteso alcuni minuti. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="Pagina Monitoraggio per la funzione prima dopo le chiamate":::
2. Selezionare la chiamata nell'elenco per visualizzare i dettagli. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="Dettagli della chiamata alla funzione" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    È anche possibile usare la scheda **Log** della pagina **Monitoraggio** per visualizzare le informazioni di registrazione durante l'invio dei messaggi. Potrebbe verificarsi un ritardo, quindi attendere alcuni minuti per visualizzare i messaggi registrati. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="Log della funzione" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>Risolvere problemi
Se dopo aver atteso qualche minuto e aggiornato la pagina non vengono visualizzate chiamate alla funzione, procedere come segue: 

1. Verificare che il messaggio abbia raggiunto l'argomento del bus di servizio. Vedere il contatore di **messaggi in ingresso** nella pagina **Argomento del bus di servizio**. In questo caso, l'applicazione **MessageSender** è stata eseguita due volte, quindi vengono visualizzati 10 messaggi (5 per ogni esecuzione).

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="Pagina Argomento del bus di servizio - messaggi in ingresso":::    
1. Verificare che nella sottoscrizione del bus di servizio **non siano presenti messaggi attivi**. 
    Se in questa pagina non vengono visualizzati eventi, verificare che la pagina **Sottoscrizione del bus di servizio** non contenga valori in **Numero di messaggi attivi**. Se il numero di questo contatore è maggiore di zero, significa che per qualche motivo i messaggi nella sottoscrizione non vengono inoltrati alla funzione del gestore di sottoscrizione di eventi. Verificare di aver configurato correttamente la sottoscrizione di eventi. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="Numero di messaggi attivi nella sottoscrizione del bus di servizio":::    
1. Vengono inoltre visualizzati gli **eventi recapitati** nella pagina **Eventi** dello spazio dei nomi del bus di servizio. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="Pagina Eventi - eventi recapitati" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. È anche possibile verificare che gli eventi siano stati recapitati nella pagina **Sottoscrizione di eventi**. Per aprire questa pagina, selezionare la sottoscrizione di eventi nella pagina **Eventi**. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="Pagina Sottoscrizione di eventi - eventi recapitati":::
    


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Griglia di eventi di Azure](../event-grid/index.yml).
* Altre informazioni su [Funzioni di Azure](../azure-functions/index.yml).
* Altre informazioni sulla [funzionalità App per la logica del Servizio app di Azure](../logic-apps/index.yml).
* Altre informazioni sul [bus di servizio di Azure](/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png