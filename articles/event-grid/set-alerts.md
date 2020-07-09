---
title: Impostare gli avvisi per le metriche di griglia di eventi di Azure e le operazioni del log attività
description: Questo articolo descrive come creare avvisi per le metriche di griglia di eventi di Azure e le operazioni del log attività.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 615ebef749be49822a09470eb0d47d8eb31b9a5a
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119141"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>Impostare gli avvisi per le metriche e i log attività di griglia di eventi di Azure
Questo articolo descrive come creare avvisi per le metriche di griglia di eventi di Azure e le operazioni del log attività. È possibile creare avvisi per le metriche di pubblicazione e recapito per le risorse di griglia di eventi di Azure (argomenti e domini). Per gli argomenti di sistema, [creare avvisi usando la pagina **metrica** ](#create-alerts-using-the-metrics-page).

## <a name="create-alerts-on-dead-lettered-events"></a>Crea avvisi per gli eventi con messaggi non recapitabili
La procedura seguente illustra come creare un avviso per la metrica **degli eventi non recapitabili** per un argomento personalizzato. In questo esempio viene inviato un messaggio di posta elettronica al proprietario del gruppo di risorse di Azure quando il numero di eventi non recapitabili per un argomento supera i 10. 

1. Nella pagina dell' **argomento griglia di eventi** per l'argomento selezionare **avvisi** nel menu a sinistra e quindi selezionare **+ nuova regola di avviso**. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Pagina Avvisi - Pulsante Nuova regola di avviso":::
2. Nella pagina **Crea regola di avviso** verificare che l'argomento sia selezionato per la risorsa. Quindi, fare clic su **Seleziona condizione**. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Pagina avvisi-Seleziona condizione":::    
3. Nella pagina **Configura logica di segnalazione** , attenersi alla seguente procedura:
    1. Selezionare una metrica o una voce di log attività. In questo esempio, **gli eventi con lettera non recapitabili** sono selezionati. 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Seleziona eventi non recapitabili":::        
    2. Selezionare dimensioni (facoltativo). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Configurare la logica dei segnali":::        

        > [!NOTE]
        > È possibile selezionare il **+** pulsante per **EventSubscriptionName** per specificare il nome di una sottoscrizione di eventi per filtrare gli eventi. 
    3. Scorrere verso il basso. Nella sezione **logica di avviso** selezionare un **operatore**, un **tipo di aggregazione**e immettere un **valore soglia**, quindi **fare**clic su fine. In questo esempio viene generato un avviso quando il numero totale di eventi con messaggi non recapitabili è maggiore di 10. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="Logica di avviso":::                
4. Tornare alla pagina **Crea regola di avviso** , fare clic su **Seleziona gruppo di azioni**.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="Pulsante Seleziona gruppo di azioni":::
5. Selezionare **Crea gruppo di azioni** sulla barra degli strumenti per creare un nuovo gruppo di azioni. È anche possibile selezionare un gruppo di azioni esistente.        
6. Nella pagina **Aggiungi gruppo di azione** , attenersi alla seguente procedura:
    1. Immettere un **nome per il gruppo di azioni**.
    1. Immettere un **nome breve** per il gruppo di azioni.
    1. Selezionare una **sottoscrizione di Azure** in cui si vuole creare il gruppo di azione.
    1. Selezionare il **gruppo di risorse di Azure** in cui si vuole creare il gruppo di azioni.
    1. Immettere un **nome per l'azione**. 
    1. Selezionare il **tipo di azione**. In questo esempio, il **ruolo di posta elettronica Azure Resource Manager** è selezionato, in particolare il ruolo **proprietari** . 
    1. Fare clic su **OK** per chiudere la pagina. 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="Pagina Aggiungi gruppo di azioni":::                   
7. Tornare alla pagina **Crea regola di avviso** , immettere un nome per la regola di avviso e quindi selezionare **Crea regola di avviso**.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="Nome regola di avviso":::  
8. A questo punto, nella pagina **avvisi** dell'argomento viene visualizzato un collegamento per gestire le regole di avviso se non sono ancora presenti avvisi. Se sono presenti avvisi, selezionare **Gestione regole di avviso** sulla barra degli strumenti.  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="Gestire gli avvisi":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>Creare avvisi per altre metriche o operazioni del log attività
Nella sezione precedente è stato illustrato come creare avvisi per gli eventi non recapitabili. I passaggi per creare avvisi per altre metriche o operazioni del log attività sono simili. 

Ad esempio, per creare un avviso in caso di errore di recapito, selezionare **recapito eventi non riusciti** nella pagina **Configura logica del segnale** . 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="Selezionare gli eventi di recapito non riusciti":::


## <a name="create-alerts-using-the-metrics-page"></a>Creare avvisi tramite la pagina metriche
È anche possibile creare avvisi tramite la pagina **metrica** . I passaggi sono simili. Per gli argomenti di sistema, è possibile usare la pagina **metriche** solo per creare avvisi, perché la pagina degli **avvisi** non è disponibile. 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Pagina metrica-pulsante Crea avviso":::   
    

> [!NOTE]
> Questo articolo non illustra tutti i diversi passaggi e combinazioni che è possibile usare per creare un avviso. Per una panoramica degli avvisi, vedere [Panoramica degli avvisi](../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul recapito di eventi e sui nuovi tentativi, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
