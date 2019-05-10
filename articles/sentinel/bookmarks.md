---
title: Tenere traccia dei dati durante la ricerca nell'anteprima di Azure Sentinel utilizzando i segnalibri caccia | Microsoft Docs
description: Questo articolo descrive come usare i segnalibri di caccia Azure Sentinel per tenere traccia dei dati.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b1a438b9645dbb37d852eb0092355850d816872d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65207451"
---
# <a name="keep-track-of-data-during-hunting"></a>Tenere traccia dei dati durante la ricerca

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Ricerca delle minacce richiede in genere esaminato montagne di dati di log alla ricerca di prove di comportamenti dannosi. Durante questo processo, addetti trovare gli eventi che si desiderano tenere presente, visitare di nuovo e analizzare come parte della convalida le ipotesi potenziali e comprendere la storia completa di una compromissione.
Segnalibri di ricerca consentono di eseguire questa operazione, mantenendo le query eseguite nel Log Analitica, insieme ai risultati della query che ritengono rilevanti. È anche possibile registrare le osservazioni contestuali e fanno riferimento i risultati ottenuti tramite l'aggiunta di tag e le note. I dati con segnalibro sono visibili all'utente e i colleghi del team per la collaborazione.   

È possibile modificare i dati con segnalibro in qualsiasi momento sul **segnalibro** scheda della finestra di **caccia** pagina. È possibile usare il filtro e cercare opzioni di trovare rapidamente i dati specifici per l'analisi corrente. In alternativa, è possibile visualizzare i dati con segnalibro direttamente nella **HuntingBookmark** tabella nel Log Analitica. In questo modo è possibile filtrare, riepilogare e unire i dati con segnalibro con altre origini dati, rendendo più semplice cercare che conferma la prova.

È anche possibile visualizzare i dati con segnalibro, facendo clic **ricerca causa**. Verrà avviata l'esperienza di analisi in cui è possibile visualizzare, analizzare e visivamente comunicare i risultati ottenuti utilizzando un diagramma grafico di entità interattivo e sequenza temporale.


## <a name="run-a-log-analytics-query-from-azure-sentinel"></a>Eseguire una query di Log Analitica da Azure Sentinel

1. Nel portale di Azure Sentinel, fare clic su **caccia** per eseguire query per il comportamento sospetto e anomala.

1. Per eseguire una campagna di ricerca, selezionare una delle query di ricerca e nella revisione a sinistra, i risultati. 

1. Fare clic su **visualizzare i risultati della query** nella query caccia **dettagli** risultati della pagina per visualizzare la query in Log Analitica. Di seguito è riportato un esempio di ciò che viene visualizzato se è stato eseguito una query di attacco bruteforce SSH personalizzata.
  
   ![Mostra risultati](./media/bookmarks/ssh-bruteforce-example.png)

## <a name="add-a-bookmark"></a>Aggiungere un segnalibro

1. Nell'elenco dei risultati della query di Log Analitica, espandere la riga contenente le informazioni che si trova interessante.

4. Selezionare i puntini di sospensione (...) alla fine della riga e selezionare **aggiungere segnalibri caccia**.
5. A destra, nelle **dettagli** pagina, aggiornare il nome e aggiungere i tag e le note per semplificare l'identificazione Qual era interessante sull'elemento.
6. Fare clic su **salvare** per eseguire il commit delle modifiche. Tutti i dati con segnalibro è condiviso con altri ricercatori ed è il primo passo verso un'esperienza di analisi collaborativa.

   ![Mostra risultati](./media/bookmarks/add-bookmark-la.png)

 
> [!NOTE]
> È anche possibile usare i segnalibri con query di Log Analitica arbitrarie avviata dalla pagina Sentinel Log Analitica i log di Azure o le query create sul tempo reale dalla pagina Log Analitica e aperto dalla pagina di ricerca. Non sarà in grado di aggiungere un segnalibro, se si avvia Log Analitica all'esterno di Azure Sentinel. 

## <a name="view-and-update-bookmarks"></a>Visualizzare e aggiornare i segnalibri 

1. Nel portale di Azure Sentinel, fare clic su **caccia**. 
2. Scegliere il **segnalibri** scheda al centro della pagina per visualizzare l'elenco dei segnalibri.
3. Usare le opzioni di finestra o un filtro di ricerca per trovare un segnalibro specifico.
4. Selezionare singoli segnalibri nella griglia sotto per visualizzare i dettagli di segnalibro nel riquadro dei dettagli a destra.
5. Per aggiornare i tag e le note, fare clic su caselle di testo modificabile e fare clic su **salvare** per mantenere le modifiche.

   ![Mostra risultati](./media/bookmarks/view-update-bookmarks.png)

## <a name="view-bookmarked-data-in-log-analytics"></a>Visualizzare i dati con segnalibro nel Log Analitica 

Esistono diverse opzioni per la visualizzazione dei dati con segnalibro nel Log Analitica. 

Il modo più semplice per visualizzare le query con segnalibro, i risultati o cronologia è selezionando il segnalibro desiderato nella **segnalibri** di tabella e usare i collegamenti riportati nel riquadro dei dettagli. Le opzioni includono: 
- Fare clic su **query della vista** per visualizzare la query di origine nel Log Analitica.  
- Fare clic su **visualizzare la cronologia di segnalibro** per visualizzare tutti i segnalibri dei metadati, comprese: chi ha apportato l'aggiornamento, i valori aggiornati e l'ora in cui si è verificato l'aggiornamento. 

- È anche possibile visualizzare i dati di segnalibro non elaborati per tutti i segnalibri facendo clic su **segnalibro log** sopra la griglia di segnalibro. Questa visualizzazione saranno inclusi tutti i segnalibri nella tabella di segnalibro caccia con i metadati associati. È possibile utilizzare le query KQL per filtrare la versione più recente del segnalibro specifico che si sta cercando.  


> [!NOTE]
> Tra le creazione di un segnalibro e quando viene visualizzato può essere notevole ritardo, espresso in minuti, il **HuntingBookmark** tabella. È consigliabile creare prima di tutto i segnalibri e quindi analizzarli dopo che i dati vengono inseriti. 

## <a name="delete-a-bookmark"></a>Eliminare un segnalibro
Se si desidera eliminare una segnalibro eseguire le operazioni seguenti: 
1.  Aprire th **segnalibro caccia** scheda. 
2.  Selezionare il segnalibro di destinazione.
3.  Selezionare i puntini di sospensione (...) alla fine della riga e selezionare **segnalibro Delete**.
    
Eliminare il segnalibro rimuove il segnalibro dall'elenco nella **segnalibro** scheda.  Il Log Analitica "HuntingBookmark" della tabella continueranno a contenere le voci di segnalibro precedente, ma l'ultima voce verrà modificato il **SoftDelete** valore su true, rendendo più semplice filtrare i vecchi segnalibri.  L'eliminazione di un segnalibro non rimuove tutte le entità dall'esperienza di analisi che sono associati altri segnalibri o avvisi. 


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si appreso come eseguire un'analisi di ricerca utilizzando i segnalibri in Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:


- [Cercare in modo proattivo le minacce](hunting.md)
- [Usare i notebook per eseguire campagne caccia automatizzati](notebooks.md)