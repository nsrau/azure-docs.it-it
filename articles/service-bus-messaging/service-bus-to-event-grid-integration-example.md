---
title: Gestire gli eventi del bus di servizio tramite Griglia di eventi con App per la logica di Azure
description: Questo articolo illustra la procedura per gestire gli eventi del bus di servizio tramite Griglia di eventi con App per la logica di Azure.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/16/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 93375f6047fbe4eda2132e024dab0e067e83ccf1
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95999019"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>Esercitazione: Rispondere agli eventi del bus di servizio di Azure ricevuti tramite Griglia di eventi di Azure usando App per la logica di Azure
Questa esercitazione descrive come rispondere agli eventi del bus di servizio di Azure ricevuti tramite Griglia di eventi di Azure usando App per la logica di Azure. 

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>Ricevere messaggi usando App per la logica
In questo passaggio si crea un'app per la logica di Azure che riceve gli eventi del bus di servizio tramite Griglia di eventi di Azure. 

1. Creare un'app per la logica nel portale di Azure.
    1. Selezionare **+ Crea una risorsa**, selezionare **Integrazione** e quindi **App per la logica**. 
    2. Nella pagina **App per la logica - Crea** immettere un **nome** per l'app per la logica.
    3. Selezionare la **sottoscrizione** di Azure. 
    4. Selezionare **Usa esistente** per **Gruppo di risorse** e selezionare il gruppo di risorse usato per le altre risorse create in precedenza, ad esempio funzione di Azure o spazio dei nomi del bus di servizio. 
    5. Selezionare il **percorso** per l'app per la logica. 
    6. Selezionare **Rivedi e crea**. 
    1. Nella pagina **Rivedi e crea** selezionare **Crea** per creare l'app per la logica. 
1. Nella pagina **Progettazione app per la logica** selezionare **App per la logica vuota** in **Modelli**. 
1. Nella finestra di progettazione seguire questa procedura:
    1. Cercare **Griglia di eventi**. 
    2. Selezionare **Quando si verifica un evento della risorsa - Griglia di eventi di Azure**. 

        ![Progettazione app per la logica - selezione del trigger di Griglia di eventi](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Selezionare **Accedi**, immettere le credenziali di Azure e selezionare **Consenti accesso**. 
5. Nella pagina **Quando si verifica un evento della risorsa** seguire questa procedura:
    1. Selezionare la sottoscrizione di Azure. 
    2. Per **Tipo di risorsa**, selezionare **Microsoft.ServiceBus.Namespaces**. 
    3. Per **Nome risorsa** selezionare lo spazio dei nomi del bus di servizio. 
    4. Selezionare **Aggiungi nuovo parametro** e quindi **Filtro per suffisso**. 
    5. Per **Filtro per suffisso** immettere il nome della seconda sottoscrizione dell'argomento del bus di servizio. 
        ![Progettazione app per la logica - configurazione dell'evento](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Selezionare **+ Nuovo passaggio** nella finestra di progettazione e seguire la procedura seguente:
    1. Cercare **Bus di servizio**.
    2. Selezionare **Bus di servizio** nell'elenco. 
    3. Selezionare **Recupera messaggi** nell'elenco **Azioni**. 
    4. Selezionare **Recupera i messaggi dalla sottoscrizione dell'argomento (blocco visualizzazione)** . 

        ![Progettazione app per la logica - recupero dell'azione per i messaggi](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Immettere un **nome per la connessione**. Ad esempio: **Recuperare i messaggi della sottoscrizione dell'argomento** e selezionare lo spazio dei nomi del bus di servizio. 

        ![Progettazione app per la logica - selezione dello spazio dei nomi del bus di servizio](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Selezionare **RootManageSharedAccessKey** e quindi selezionare **Crea**.

        ![Progettazione app per la logica - selezione della chiave di accesso condiviso](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. Selezionare l'**argomento** e la **sottoscrizione**. 
    
        ![Screenshot che mostra dove selezionare l'argomento e la sottoscrizione.](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Selezionare **+ Nuovo passaggio** e seguire la procedura seguente: 
    1. Selezionare **Bus di servizio**.
    2. Selezionare **Completa il messaggio in una sottoscrizione dell'argomento** nell'elenco di azioni. 
    3. Selezionare l'**argomento** del bus di servizio.
    4. Selezionare la seconda **sottoscrizione** dell'argomento.
    5. Per **Token di blocco del messaggio** selezionare **Token di blocco** in **Contenuto dinamico**. 

        ![Finestra di progettazione di app per la logica - completare il messaggio](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Per salvare le app per la logica, selezionare **Salva** sulla barra degli strumenti di Progettazione app per la logica. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="Salvare l'app per la logica":::
1. Se non sono stati ancora inviati messaggi di test all'argomento, inviarli seguendo le istruzioni riportate nella sezione [Inviare messaggi all'argomento del bus di servizio](#send-messages-to-the-service-bus-topic). 
1. Passare alla pagina **Panoramica** dell'app per la logica. Le esecuzioni dell'app per la logica sono visualizzate nella **Cronologia esecuzioni** dei messaggi inviati. Possono essere necessari alcuni minuti prima che venga visualizzata l'app per la logica in esecuzione. Selezionare **Aggiorna** sulla barra degli strumenti per aggiornare la pagina. 

    ![Progettazione app per la logica - esecuzioni dell'app per la logica](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. Selezionare un'esecuzione dell'app per la logica per vedere i dettagli. Si noti che sono stati elaborati 5 messaggi nel ciclo For. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="Dettagli dell'esecuzione dell'app per la logica":::    

## <a name="troubleshoot"></a>Risolvere problemi
Se dopo aver atteso qualche minuto e aggiornato la pagina non vengono visualizzate chiamate, seguire questa procedura: 

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