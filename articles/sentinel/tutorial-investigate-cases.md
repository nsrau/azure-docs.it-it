---
title: Indagare sugli incidenti con Azure Sentinel Documenti Microsoft
description: Usare questa esercitazione per informazioni su come analizzare gli eventi imprevisti con Azure Sentinel.Use this tutorial to learn how to investigate incidents with Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: ecd8c508d05bfeb541a6cb5efbcdf2fffd3c78d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587193"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Esercitazione: Esaminare gli eventi imprevisti con Azure SentinelTutorial: Investigate incidents with Azure Sentinel

> [!IMPORTANT]
> Il grafico delle indagini è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Questa esercitazione consente di analizzare gli eventi imprevisti con Azure Sentinel.This tutorial helps you investigate incidents with Azure Sentinel. Dopo aver connesso le origini dati ad Azure Sentinel, si vuole ricevere una notifica quando si verifica qualcosa di sospetto. Per consentire questa operazione, Azure Sentinel consente di creare regole di avviso avanzate che generano eventi imprevisti che è possibile assegnare e analizzare.

Questo articolo riguarda:
> [!div class="checklist"]
> * Analizzare gli eventi imprevisti
> * Utilizzare il grafico delle indagini
> * Rispondere alle minacce

Un evento imprevisto può includere più avvisi. È un'aggregazione di tutte le prove rilevanti per un'indagine specifica. Viene creato un evento imprevisto in base alle regole analitiche create nella pagina **Analisi.** Le proprietà correlate agli avvisi, ad esempio la gravità e lo stato, vengono impostate a livello di evento imprevisto. Dopo aver comunicato ad Azure Sentinel quali tipi di minacce stai cercando e come trovarle, puoi monitorare le minacce rilevate analizzando gli incidenti.

## <a name="prerequisites"></a>Prerequisiti
Sarà possibile analizzare l'evento imprevisto solo se sono stati utilizzati i campi di mapping delle entità quando si imposta la regola analitica. Il grafico dell'indagine richiede che l'evento imprevisto originale includa entità.

## <a name="how-to-investigate-incidents"></a>Come analizzare gli eventi imprevisti

1. Selezionare **Incidenti**. La pagina **Operazioni non consentite** consente di sapere quanti eventi sono presenti, quanti sono aperti, quanti sono stati impostati su **In corso**e quanti sono chiusi. Per ogni evento imprevisto, è possibile visualizzare l'ora in cui si è verificato e lo stato dell'evento imprevisto. Esaminare la gravità per decidere quali eventi imprevisti gestire per primi.

    ![Visualizzare la gravità dell'evento imprevisto](media/tutorial-investigate-cases/incident-severity.png)

1. È possibile filtrare gli eventi imprevisti in base alle esigenze, ad esempio in base allo stato o alla gravità.

1. Per avviare un'indagine, selezionare un evento imprevisto specifico. Sulla destra, è possibile visualizzare informazioni dettagliate per l'evento imprevisto, tra cui la gravità, il riepilogo del numero di entità coinvolte, gli eventi non elaborati che hanno attivato l'evento imprevisto e l'ID univoco dell'evento imprevisto.

1. Per visualizzare ulteriori dettagli sugli avvisi e sulle entità nell'evento imprevisto, selezionare **Visualizza dettagli completi** nella pagina dell'evento imprevisto ed esaminare le schede pertinenti che riepilogano le informazioni sull'evento imprevisto. Nella scheda **Avvisi** esaminare l'avviso stesso. È possibile visualizzare tutte le informazioni rilevanti sull'avviso, ovvero la query che ha attivato l'avviso, il numero di risultati restituiti per ogni query e la possibilità di eseguire playbook sugli avvisi. Per eseguire ulteriormente il drill-down nell'evento imprevisto, selezionare il numero di **eventi**. Verrà visualizzata la query che ha generato i risultati e gli eventi che hanno attivato l'avviso in Log Analytics. Nella scheda **Entità** è possibile visualizzare tutte le entità di cui è stato eseguito il mapping come parte della definizione della regola di avviso.

    ![Visualizzare i dettagli relativi all'avviso](media/tutorial-investigate-cases/alert-details.png)

1. Se si sta indagando attivamente su un evento imprevisto, è consigliabile impostare lo stato dell'evento imprevisto **su In corso** fino a quando non viene chiuso.

1. Gli eventi imprevisti possono essere assegnati a un utente specifico. Per ogni evento imprevisto è possibile assegnare un proprietario, impostando il campo **Proprietario evento imprevisto.** Tutti gli eventi imprevisti iniziano come non assegnati. È inoltre possibile aggiungere commenti in modo che altri analisti saranno in grado di capire ciò che hai indagato e quali sono le vostre preoccupazioni intorno all'incidente.

    ![Assegnare un evento imprevisto all'utenteAssign incident to user](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Selezionare **Indaga** per visualizzare la mappa di indagine.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Usa il grafico dell'indagine per immergerti in profondità

Il grafico delle indagini consente agli analisti di porre le domande giuste per ogni indagine. Il grafico dell'indagine consente di comprendere l'ambito e identificare la causa principale di una potenziale minaccia alla sicurezza correlando i dati rilevanti con qualsiasi entità coinvolta. È possibile approfondire e analizzare qualsiasi entità presentata nel grafico selezionandola e scegliendo tra diverse opzioni di espansione.  
  
Il grafico delle indagini fornisce:

- **Contesto visivo dai dati non elaborati:** il grafico visivo in tempo reale visualizza le relazioni di entità estratte automaticamente dai dati non elaborati. In questo modo è possibile visualizzare facilmente le connessioni tra origini dati diverse.

- **Individuazione completa dell'ambito**di indagine : espandere l'ambito di indagine utilizzando query di esplorazione incorporate per esporre l'intero ambito di una violazione.

- Passaggi di **indagine**integrati : Utilizzare opzioni di esplorazione predefinite per assicurarsi che si stanno ponendo le domande giuste di fronte a una minaccia.

Per utilizzare il grafico delle indagini:

1. Selezionare un evento imprevisto, quindi selezionare **Esamina**. Questo ti porta al grafico delle indagini. Il grafico fornisce una mappa illustrativa delle entità direttamente connesse all'avviso e di ogni risorsa connessa ulteriormente.

   > [!IMPORTANT] 
   > Sarà possibile analizzare l'evento imprevisto solo se sono stati utilizzati i campi di mapping delle entità quando si imposta la regola analitica. Il grafico dell'indagine richiede che l'evento imprevisto originale includa entità.

   ![Visualizzare la mappa](media/tutorial-investigate-cases/map1.png)

1. Selezionare un'entità per aprire il riquadro **Entità** in modo da poter esaminare le informazioni su tale entità.

    ![Visualizzare le entità nella mappa](media/tutorial-investigate-cases/map-entities.png)
  
1. Espandi la tua indagine passando il mouse su ogni entità per rivelare un elenco di domande che è stato progettato dai nostri esperti di sicurezza e analisti per ogni tipo di entità per approfondire la tua indagine. Queste query di **esplorazione**delle opzioni vengono chiamate .

    ![Scopri maggiori dettagli](media/tutorial-investigate-cases/exploration-cases.png)

   Ad esempio, in un computer è possibile richiedere avvisi correlati. Se si seleziona una query di esplorazione, i diritti risultanti vengono aggiunti nuovamente al grafico. In questo esempio, selezionando Avvisi correlati sono stati restituiti gli avvisi seguenti nel grafico:In this example, selecting **Related alerts** returned the following alerts in the graph:

    ![Visualizzare gli avvisi correlati](media/tutorial-investigate-cases/related-alerts.png)

1. Per ogni query di esplorazione, è possibile selezionare l'opzione per aprire i risultati degli eventi non elaborati e la query utilizzata in Log Analytics selezionando **Eventi\>**.

1. Per comprendere l'incidente, il grafico fornisce una sequenza temporale parallela.

    ![Visualizzare la sequenza temporale nella mappa](media/tutorial-investigate-cases/map-timeline.png)

1. Passa il mouse sopra la sequenza temporale per vedere quali elementi del grafico si sono verificati in quale punto nel tempo.

    ![Usare la sequenza temporale nella mappa per analizzare gli avvisiUse timeline in map to investigate alerts](media/tutorial-investigate-cases/use-timeline.png)



## <a name="next-steps"></a>Passaggi successivi
In this tutorial, you learned how to get started investigating incidents using Azure Sentinel. Continuare con il tutorial su [come rispondere alle minacce utilizzando playbook automatizzati](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Rispondi alle minacce](tutorial-respond-threats-playbook.md) per automatizzare le tue risposte alle minacce.

