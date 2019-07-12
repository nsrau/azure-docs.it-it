---
title: Esaminare i case con Sentinel anteprima di Azure | Microsoft Docs
description: Usare questa esercitazione per imparare a esaminare i case con Sentinel di Azure.
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
ms.openlocfilehash: 82fac23fc2d718aa908f6291241abaa2aedb8815
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621179"
---
# <a name="tutorial-investigate-cases-with-azure-sentinel-preview"></a>Esercitazione: Esaminare i case con Sentinel anteprima di Azure

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa esercitazione consente di rilevare le minacce con Sentinel di Azure.

Dopo aver [le origini dati connesse](quickstart-onboard.md) Sentinel di Azure, si desidera ricevere una notifica quando succede qualcosa di sospetto. Per consentire a tale scopo, Azure Sentinel consente di che creare avanzato e le regole di avviso, che generano i casi in cui è possibile assegnare per analizzare completamente le anomalie e le minacce nell'ambiente in uso. 

> [!div class="checklist"]
> * Creare i case
> * Analizzare i casi
> * Rispondere alle minacce

## <a name="investigate-cases"></a>Analizzare i casi

Un caso può includere più avvisi. È un'aggregazione di tutti gli elementi di prova per un'analisi specifiche. Un caso viene creato in avvisi definiti in base il **Analitica** pagina. Vengono impostate le proprietà correlate agli avvisi, ad esempio stato e la gravità al livello del case. Dopo che si lascia che Azure Sentinel sapere quali tipi di minacce che si sta cercando e metodi per individuarli, è possibile monitorare le minacce rilevate con l'analisi dei casi. 

1. Selezionare **casi**. Il **casi** pagina consente di sapere quanti case si dispone, quanti sono aprire, quanti è stata impostata su **In corso**, e quanti sono chiusi. Per ogni caso, è possibile visualizzare l'ora in cui che si è verificato e lo stato del case. Esaminare il livello di gravità a decidere come gestire prima. Nel **casi** pagina, fare clic sui **avvisi** scheda per visualizzare tutti gli avvisi correlati a un case. Le entità che è stata mappata in precedenza come parte del case può essere visualizzati nei **entità** scheda.  È possibile filtrare i case in base alle esigenze, ad esempio per lo stato o gravità. Quando si esamina il **casi** scheda, si noterà open case che contengono gli avvisi attivati per le regole di rilevamento nella **Analitica**. Nella parte superiore si noterà i casi attivi, i nuovi casi e, in casi lo stato di avanzamento. È anche possibile visualizzare una panoramica di tutti i case in base alla gravità.

   ![Dashboard degli avvisi](./media/tutorial-investigate-cases/cases.png)

2. Per avviare un'indagine, fare clic su un caso specifico. A destra, è possibile visualizzare informazioni dettagliate per il case, inclusi la gravità, riepilogo del numero di entità coinvolte (basata su un mapping). Ogni case ha un ID univoco. La gravità del case è determinata in base all'avviso più grave incluso nel caso.  

1. Per visualizzare altri dettagli sulle entità e gli avvisi nel caso, fare clic su **visualizzare i dettagli completi** nel caso di pagina e analizzare le schede rilevanti che riepilogano le informazioni del caso.  Vista completa case consolida tutte le prove nell'avviso, agli avvisi associati e le entità.

1. Nel **avvisi** scheda, esaminare l'avviso, quando è stata attivata e da quanto ha superato le soglie impostate. È possibile visualizzare tutte le informazioni pertinenti relative all'avviso: la query che ha attivato l'avviso, il numero di risultati restituiti per ogni query e la possibilità di eseguire Playbook per gli avvisi. Eseguire il drill-verso il basso, ulteriormente nel case, fare clic sul numero di riscontri. Verrà visualizzata la query che ha generato i risultati e i risultati che ha attivato l'avviso in Log Analitica.

3. Nel **entità** scheda, è possibile visualizzare tutte le entità che è stata mappata come parte della definizione della regola di avviso. 

4. Se si sta attivamente esaminando un case, è consigliabile impostare lo stato del caso **In corso** fino a quando non si chiude. È anche possibile chiudere il caso in cui **chiuso risolto** è lo stato per i casi che indicano che un evento imprevisto è stato gestito, mentre **chiuso dismissed** è lo stato per i casi che non richiedono una gestione. Le spiegazioni sono necessari che spiega i motivi per un caso di chiusura.

5. Casi possono essere assegnati a un utente specifico. Per ogni caso è possibile assegnare un proprietario, impostando il case **proprietario** campo. Iniziano tutti casi come non assegnati. È possibile analizzare i casi e filtrare per il nome per visualizzare tutti i case che si è proprietari. 

5. Fare clic su **ricerca causa** per visualizzare la mappa dell'indagine e l'ambito della violazione con i passaggi correttivi. 



## <a name="respond-to-threats"></a>Rispondere alle minacce

Sentinel Azure offre due opzioni principali per rispondere alle minacce tramite Playbook. È possibile impostare un playbook da eseguire automaticamente quando viene attivato un avviso oppure è possibile eseguire manualmente un playbook in risposta a un avviso.

- È possibile impostare un playbook da eseguire automaticamente quando viene attivato un avviso quando si configura il playbook. 

- È possibile eseguire manualmente un playbook all'interno dell'avviso, facendo clic **Visualizza i Playbook** e quindi selezionando un playbook per l'esecuzione.




## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come iniziare a usare analisi di casi usando Azure Sentinel. Continuare con l'esercitazione [come rispondere alle minacce tramite Playbook automatizzati](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Rispondere alle minacce](tutorial-respond-threats-playbook.md) per automatizzare le risposte alle minacce.

