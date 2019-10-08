---
title: Esaminare gli eventi imprevisti con Sentinel di Azure | Microsoft Docs
description: Usare questa esercitazione per informazioni su come analizzare gli eventi imprevisti con Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: fbab3eff3d7f742c2542e4264ef1b2124c5f15e5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024618"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Esercitazione: Esaminare gli eventi imprevisti con Sentinel di Azure

> [!IMPORTANT]
> Il grafico di analisi è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Questa esercitazione consente di esaminare gli eventi imprevisti con Sentinel di Azure. Dopo aver connesso le origini dati ad Azure Sentinel, è necessario ricevere una notifica quando si verifica un evento sospetto. Per consentire l'esecuzione di questa operazione, Azure Sentinel consente di creare regole di avviso avanzate che generano eventi imprevisti che è possibile assegnare ed esaminare.

Questo articolo illustra le attività seguenti:
> [!div class="checklist"]
> * Analizzare gli eventi imprevisti
> * Usare il grafico di analisi
> * Rispondere alle minacce

Un evento imprevisto può includere più avvisi. Si tratta di un'aggregazione di tutte le evidenze rilevanti per un'indagine specifica. Viene creato un evento imprevisto in base alle regole analitiche create nella pagina di **analisi** . Le proprietà correlate agli avvisi, ad esempio la gravità e lo stato, vengono impostate a livello di evento imprevisto. Quando si lascia che Azure Sentinel conosca quali tipi di minacce si sta cercando e come trovarli, è possibile monitorare le minacce rilevate esaminando gli eventi imprevisti.

## <a name="prerequisites"></a>Prerequisiti
È possibile esaminare l'evento imprevisto solo se sono stati usati i campi di mapping delle entità quando si configura la regola di analisi. Il grafico di indagine richiede che l'evento imprevisto originale includa entità.

## <a name="how-to-investigate-incidents"></a>Come analizzare gli eventi imprevisti

1. Selezionare **eventi imprevisti**. Nella pagina **eventi imprevisti** è possibile verificare il numero di eventi imprevisti, il numero di eventi aperti, il numero di eventi impostati **in corso**e il numero di eventi chiusi. Per ogni evento imprevisto, è possibile visualizzare l'ora in cui si è verificata e lo stato dell'evento imprevisto. Esaminare la gravità per decidere quali eventi imprevisti gestire per primi.

    ![Visualizza gravità evento imprevisto](media/tutorial-investigate-cases/incident-severity.png)

1. È possibile filtrare gli eventi imprevisti in base alle esigenze, ad esempio in base allo stato o alla gravità.

1. Per iniziare un'indagine, selezionare un evento imprevisto specifico. A destra è possibile visualizzare informazioni dettagliate per l'evento imprevisto, tra cui la gravità, il riepilogo del numero di entità interessati, gli eventi non elaborati che hanno attivato l'evento imprevisto e l'ID univoco dell'evento imprevisto.

1. Per visualizzare ulteriori dettagli sugli avvisi e le entità nell'evento imprevisto, selezionare **Visualizza dettagli completi** nella pagina evento imprevisto ed esaminare le schede pertinenti che riepilogano le informazioni sull'evento imprevisto. Nella scheda **avvisi** esaminare l'avviso stesso. È possibile visualizzare tutte le informazioni rilevanti relative all'avviso, ovvero la query che ha attivato l'avviso, il numero di risultati restituiti per ogni query e la possibilità di eseguire PlayBook negli avvisi. Per eseguire il drill-down anche ulteriormente nell'evento imprevisto, selezionare il numero di **eventi**. Verrà visualizzata la query che ha generato i risultati e gli eventi che hanno attivato l'avviso in Log Analytics. Nella scheda **entità** è possibile visualizzare tutte le entità di cui è stato eseguito il mapping come parte della definizione della regola di avviso.

    ![Visualizza dettagli avviso](media/tutorial-investigate-cases/alert-details.png)

1. Se si sta esaminando attivamente un evento imprevisto, è consigliabile impostare lo stato dell'evento imprevisto su **in corso** fino a quando non lo si chiude.

1. Gli eventi imprevisti possono essere assegnati a un utente specifico. Per ogni evento imprevisto è possibile assegnare un proprietario impostando il campo **proprietario evento imprevisto** . Tutti gli eventi imprevisti iniziano come non assegnati. È anche possibile aggiungere commenti in modo che altri analisti siano in grado di comprendere cosa è stato esaminato e quali sono le preoccupazioni relative all'evento imprevisto.

    ![Assegna evento imprevisto all'utente](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Selezionare **esamina** per visualizzare la mappa di analisi.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Usare il grafico di indagine per approfondire

Il grafico di indagine consente agli analisti di porre le domande corrette per ogni indagine. Il grafico di analisi consente di comprendere l'ambito e identificare la causa principale di una potenziale minaccia per la sicurezza correlando i dati rilevanti con qualsiasi entità interessata. È possibile approfondire e analizzare le entità presentate nel grafico selezionandolo e scegliendo tra diverse opzioni di espansione.  
  
Il grafico di indagine fornisce:

- **Contesto visivo da dati non elaborati**: Il grafico visuale Live Visualizza le relazioni di entità estratte automaticamente dai dati non elaborati. In questo modo è possibile visualizzare facilmente le connessioni tra origini dati diverse.

- **Individuazione completa dell'ambito di analisi**: Espandere l'ambito di analisi usando le query di esplorazione predefinite per emergere l'ambito completo di una violazione.

- **Procedure di analisi predefinite**: Usare le opzioni di esplorazione predefinite per assicurarsi di porre le domande corrette sul fronte di una minaccia.

Per usare il grafico di analisi:

1. Selezionare un evento imprevisto, quindi fare clic **su**Cerca. In questo modo si passa al grafico di analisi. Il grafico fornisce una mappa illustrativa delle entità connesse direttamente all'avviso e ogni risorsa è connessa.

   > [!IMPORTANT] 
   > È possibile esaminare l'evento imprevisto solo se sono stati usati i campi di mapping delle entità quando si configura la regola di analisi. Il grafico di indagine richiede che l'evento imprevisto originale includa entità.

   ![Visualizzare la mappa](media/tutorial-investigate-cases/map1.png)

1. Selezionare un'entità per aprire il riquadro **entità** in modo da poter esaminare le informazioni relative a tale entità.

    ![Visualizza entità nella mappa](media/tutorial-investigate-cases/map-entities.png)
  
1. Espandi la tua indagine posizionando il puntatore del mouse su ogni entità per visualizzare un elenco di domande progettate dagli esperti di sicurezza e dagli analisti per tipo di entità per approfondire l'analisi. Queste query sono denominate opzioni di **esplorazione**.

    ![Esplora altri dettagli](media/tutorial-investigate-cases/exploration-cases.png)

   Ad esempio, in un computer è possibile richiedere avvisi correlati. Se si seleziona una query di esplorazione, i titoli risultanti vengono aggiunti di nuovo al grafico. In questo esempio, se si selezionano gli **avvisi correlati** , nel grafico vengono restituiti gli avvisi seguenti:

    ![Visualizza avvisi correlati](media/tutorial-investigate-cases/related-alerts.png)

1. Per ogni query di esplorazione, è possibile selezionare l'opzione per aprire i risultati degli eventi non elaborati e la query usata in Log Analytics, selezionando **Events @ no__t-1**.

1. Per comprendere l'evento imprevisto, il grafico fornisce una sequenza temporale parallela.

    ![Visualizza sequenza temporale nella mappa](media/tutorial-investigate-cases/map-timeline.png)

1. Passare il puntatore del mouse sulla sequenza temporale per vedere quali elementi del grafico si sono verificati in un determinato momento.

    ![Usare la sequenza temporale nella mappa per esaminare gli avvisi](media/tutorial-investigate-cases/use-timeline.png)



## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come iniziare a esaminare gli eventi imprevisti usando Sentinel di Azure. Continuare con l'esercitazione su [come rispondere alle minacce usando i PlayBook automatici](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Rispondere alle minacce](tutorial-respond-threats-playbook.md) per automatizzare le risposte alle minacce.

