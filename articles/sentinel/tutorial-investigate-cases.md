---
title: Esaminare gli eventi imprevisti con anteprima di Azure Sentinel | Microsoft Docs
description: Usare questa esercitazione per informazioni su come analizzare gli eventi imprevisti con Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: bad3fddd6caf7e6eb455e59280f181c787b95a4e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780389"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel-preview"></a>Esercitazione: Esaminare gli eventi imprevisti con anteprima di Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa esercitazione consente di rilevare le minacce con Azure Sentinel.

Dopo aver [connesso le origini dati](quickstart-onboard.md) ad Azure Sentinel, è necessario ricevere una notifica quando si verifica un evento sospetto. Per consentire l'esecuzione di questa operazione, Azure Sentinel consente di creare regole di avviso avanzate che generano eventi imprevisti che è possibile assegnare e usare per analizzare in modo approfondito le anomalie e le minacce nell'ambiente. 

> [!div class="checklist"]
> * Creazione di eventi imprevisti
> * Analizzare gli eventi imprevisti
> * Rispondere alle minacce

## <a name="investigate-incidents"></a>Analizzare gli eventi imprevisti

un evento imprevisto può includere più avvisi. Si tratta di un'aggregazione di tutte le evidenze rilevanti per un'indagine specifica. viene creato un evento imprevisto in base agli avvisi definiti nella pagina di **analisi** . Le proprietà correlate agli avvisi, ad esempio gravità e stato, vengono impostate a livello di evento imprevisto. Quando si lascia che Azure Sentinel conosca quali tipi di minacce si sta cercando e come trovarli, è possibile monitorare le minacce rilevate esaminando gli eventi imprevisti. 

1. Selezionare **eventi imprevisti**. Nella pagina **eventi imprevisti** è possibile verificare il numero di eventi imprevisti, il numero di eventi aperti, il numero di eventi impostati **in corso**e il numero di eventi chiusi. Per ogni evento imprevisto, è possibile visualizzare l'ora in cui si è verificata e lo stato dell'evento imprevisto. Esaminare la gravità per decidere cosa gestire prima. Nella pagina **eventi imprevisti** fare clic sulla scheda **avvisi** per visualizzare tutti gli avvisi correlati a un evento imprevisto. Le entità di cui è stato eseguito il mapping in precedenza come parte dell'evento imprevisto possono essere visualizzate nella scheda **entità** .  È possibile filtrare gli eventi imprevisti in base alle esigenze, ad esempio in base allo stato o alla gravità. Quando si esamina la scheda **eventi imprevisti** , verranno visualizzati gli eventi imprevisti aperti che contengono gli avvisi generati dalle regole di rilevamento definite in **Analytics**. Nella parte superiore verranno visualizzati gli eventi imprevisti attivi, i nuovi eventi imprevisti e gli eventi imprevisti in corso. È anche possibile visualizzare una panoramica di tutti gli eventi imprevisti in base alla gravità.

   ![Dashboard degli avvisi](./media/tutorial-investigate-cases/cases.png)

2. Per iniziare un'indagine, fare clic su un evento imprevisto specifico. A destra è possibile visualizzare informazioni dettagliate per l'evento imprevisto, inclusa la gravità, il riepilogo del numero di entità in uso (in base al mapping). Ogni evento imprevisto ha un ID univoco. La gravità dell'evento imprevisto viene determinata in base all'avviso più grave incluso nell'evento imprevisto.  

1. Per visualizzare ulteriori dettagli sugli avvisi e le entità nell'evento imprevisto, fare clic su **Visualizza dettagli completi** nella pagina evento imprevisto ed esaminare le schede pertinenti che riepilogano le informazioni sull'evento imprevisto.  La vista eventi imprevisti completa consolida tutte le evidenze nell'avviso, gli avvisi associati e le entità.

1. Nella scheda **avvisi** esaminare l'avviso stesso, quando è stato attivato e in base a quanto superato le soglie impostate. È possibile visualizzare tutte le informazioni rilevanti relative all'avviso, ovvero la query che ha attivato l'avviso, il numero di risultati restituiti per ogni query e la possibilità di eseguire PlayBook negli avvisi. Per eseguire il drill-down ancora più a lungo nell'evento imprevisto, fare clic sul numero di riscontri. Verrà visualizzata la query che ha generato i risultati e i risultati che hanno attivato l'avviso in Log Analytics.

3. Nella scheda **entità** è possibile visualizzare tutte le entità di cui è stato eseguito il mapping come parte della definizione della regola di avviso. 

4. Se si sta esaminando attivamente un evento imprevisto, è consigliabile impostare lo stato dell'evento su **in corso** fino a quando non lo si chiude. È anche possibile chiudere l'evento imprevisto, in cui **Closed risolto** è lo stato degli eventi imprevisti che indicano che un evento imprevisto è stato gestito, mentre **Closed chiuso** è lo stato degli eventi imprevisti che non richiedono la gestione. Sono necessarie spiegazioni che spiegano il motivo della chiusura di un evento imprevisto.

5. Gli eventi imprevisti possono essere assegnati a un utente specifico. Per ogni evento imprevisto è possibile assegnare un proprietario impostando il campo **proprietario** evento imprevisto. Tutti gli eventi imprevisti iniziano come non assegnati. È possibile esaminare gli eventi imprevisti e filtrare in base al nome per visualizzare tutti gli eventi imprevisti di cui si è proprietari. 

5. Fare clic su verifica per visualizzare la mappa di indagine e l'ambito della violazione con i passaggi correttivi. 



## <a name="respond-to-threats"></a>Rispondere alle minacce

Azure Sentinel offre due opzioni principali per rispondere alle minacce usando i PlayBook. È possibile impostare un PlayBook per l'esecuzione automatica quando viene attivato un avviso oppure è possibile eseguire manualmente un playbook in risposta a un avviso.

- È possibile impostare un PlayBook per l'esecuzione automatica quando viene attivato un avviso quando si configura il PlayBook. 

- È possibile eseguire manualmente un PlayBook dall'interno dell'avviso, facendo clic su **Visualizza PlayBook** e selezionando un PlayBook da eseguire.




## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come iniziare a esaminare gli eventi imprevisti usando Sentinel di Azure. Continuare con l'esercitazione su [come rispondere alle minacce usando i PlayBook automatici](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Rispondere alle minacce](tutorial-respond-threats-playbook.md) per automatizzare le risposte alle minacce.

