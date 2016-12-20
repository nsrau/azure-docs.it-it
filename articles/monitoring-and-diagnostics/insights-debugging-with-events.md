---
title: "Visualizzare eventi e log di attività (controllo)"
description: Informazioni su come visualizzare tutti gli eventi che si verificano nella sottoscrizione di Azure.
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 60c38217-c3b5-4ef1-98ac-d045fdd5549b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/28/2015
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c767480f072feaa0fbb0b5bdaec992a6f2381040


---
# <a name="view-events-and-activity-logs"></a>Visualizzare eventi e log di attività
Tutte le operazioni eseguite sulle risorse di Azure sono interamente controllate da Gestione risorse di Azure, dalla creazione e dalle eliminazioni alla concessione e alla revoca dell'accesso. È possibile esplorare questi log nel portale di Azure e usare l'[API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) per accedere al set completo di eventi a livello di codice.

## <a name="browse-the-events-impacting-your-azure-subscription"></a>Esplorare gli eventi che hanno effetto sulla sottoscrizione di Azure
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Sfoglia** e selezionare **Log di controllo**.  
    ![Hub di esplorazione](./media/insights-debugging-with-events/Insights_Browse.png)
3. Si aprirà un pannello contenente tutti gli eventi che hanno avuto effetto sulle sottoscrizioni negli ultimi 7 giorni. In alto un grafico mostra i dati per livello e sotto c'è l'elenco completo dei log:  ![Tutti gli eventi](./media/insights-debugging-with-events/Insights_AllEvents.png)

> [!NOTE]
> Nel portale di Azure è possibile visualizzare solo i 500 eventi più recenti per una determinata sottoscrizione.
> 
> 

1. È possibile fare clic su qualsiasi voce di log per visualizzare gli eventi che la costituiscono. Ad esempio, quando si distribuisce qualcosa a un gruppo di risorse, potrebbero venire create o modificate molte risorse diverse. Per ogni voce è possibile visualizzare:
   
   * **Livello** dell'evento: ad esempio, potrebbe trattarsi solo di qualcosa di cui tenere traccia (**Informativo**) o qualcosa per cui si sono verificati problemi e che è necessario conoscere (**Errore**).
   * **Stato**: lo stato finale sarà in genere **Completato** o **Non riuscito**, ma potrebbe essere anche **Accettato** per le operazioni di lunga durata.
   * *Quando* si è verificato l'evento.
   * *Chi* ha eventualmente eseguito l'operazione. Non tutte le operazioni vengono eseguite dagli utenti, alcune vengono eseguite da servizi back-end e quindi non hanno un **Chiamante**.
   * **ID di correlazione** dell'evento: identificatore univoco per questo set di operazioni.
2. Da qui è possibile passare al pannello dei dettagli per vedere le specifiche dell'evento.
   
    ![Gruppi di risorse](./media/insights-debugging-with-events/Insights_EventDetails.png)
   
    Per gli eventi con stato **Non riuscito**, in questa pagina sono normalmente incluse le sezioni **SubStatus** e **Proprietà** che contengono informazioni dettagliate utili a scopo di debug.

## <a name="filter-to-specific-logs"></a>Filtrare registri specifici
Per visualizzare gli eventi che si applicano a una specifica entità o che sono di un tipo specifico, è possibile filtrare il pannello Log di controllo facendo clic sul comando **Filtro** . Il pannello Filtro si usa anche per modificare l' **Intervallo di tempo** del pannello Log di controllo.

Quando si fa clic su questo comando, verrà aperto un nuovo pannello:

![Filtro](./media/insights-debugging-with-events/Insights_EventFilter.png)

Esistono quattro tipi di filtri:

1. Per sottoscrizione
2. Per **gruppo di risorse**
3. Per **tipo di risorsa**
4. Per una determinata **risorsa** : è necessario incollare l' *ID risorsa* completo della risorsa a cui si è interessati

Si possono filtrare gli eventi anche in base a chi ha eseguito l'evento o in base al livello dell'evento.

Una volta scelto cosa si vuole visualizzare, fare clic sul pulsante **Aggiorna** in fondo al pannello.

## <a name="monitor-events-impacting-specific-resources"></a>Monitorare gli eventi che hanno effetto su risorse specifiche
1. Fare clic su **Sfoglia** per trovare la risorsa a cui si è interessati. Si possono visualizzare anche tutti i log per un intero **Gruppo di risorse**.
2. Nel pannello della risorsa scorrere fino al riquadro **Eventi** .  
    ![Riquadro Eventi](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. Fare clic sul riquadro per visualizzare gli eventi filtrati solo in base alla risorsa selezionata. È possibile usare il comando **Filtra** per modificare l'intervallo di tempo o applicare altri filtri specifici.

## <a name="next-steps"></a>Passaggi successivi
* [Ricevere notifiche di avviso](insights-receive-alert-notifications.md) ogni volta che si verifica un evento.
* [Monitorare le metriche del servizio](insights-how-to-customize-monitoring.md) per assicurarsi che il servizio sia disponibile e reattivo.
* [Tenere traccia dello stato del servizio](insights-service-health.md) per scoprire quando Azure ha subito un calo delle prestazioni o interruzioni del servizio.  




<!--HONumber=Nov16_HO3-->


