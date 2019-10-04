---
title: Tenere traccia dei dati durante la ricerca in Azure Sentinel con i segnalibri di caccia | Microsoft Docs
description: Questo articolo descrive come usare i segnalibri di ricerca di Azure Sentinel per tenere traccia dei dati.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: aa414e37470cc11b7dc83e7416590aa2babf6818
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240261"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Tenere traccia dei dati durante la ricerca con Azure Sentinel

Per la ricerca di minacce è in genere necessario esaminare le montagne dei dati di log per verificare la presenza di comportamenti dannosi. Durante questo processo, gli investigatori trovano gli eventi che vogliono ricordare, rivedere e analizzare come parte della convalida di potenziali ipotesi e la comprensione della storia completa di un compromesso.

I segnalibri di Hunting in Azure Sentinel consentono di eseguire questa operazione, conservando le query eseguite in Log Analytics, insieme ai risultati della query ritenuti rilevanti. È anche possibile registrare le osservazioni contestuali e fare riferimento ai risultati aggiungendo note e tag. I dati con segnalibro sono visibili all'utente e ai colleghi per una facile collaborazione.

È possibile rivedere i dati con segnalibro in qualsiasi momento nella scheda **segnalibro** del riquadro di **ricerca** . È possibile utilizzare le opzioni di filtro e di ricerca per trovare rapidamente dati specifici per l'analisi corrente. In alternativa, è possibile visualizzare i dati con segnalibro direttamente nella tabella **HuntingBookmark** di monitoraggio di Azure. In questo modo è possibile filtrare, riepilogare e unire i dati con segnalibro con altre origini dati, semplificando la ricerca di prove corroboranti.

Attualmente in anteprima, se si trova un elemento che deve essere risolto in modo urgente durante la ricerca nei log, in un paio di clic è possibile creare un segnalibro e innalzarlo a un evento imprevisto oppure aggiungere il segnalibro a un evento imprevisto esistente. Per ulteriori informazioni sugli eventi imprevisti, [vedere Esercitazione: Esaminare gli eventi imprevisti con](tutorial-investigate-cases.md)Sentinel di Azure. 

Inoltre, in anteprima, è possibile visualizzare i dati con segnalibro, facendo clic **su analizza** nei dettagli del segnalibro. In questo modo viene avviata l'esperienza di analisi in cui è possibile visualizzare, analizzare e comunicare visivamente le conclusioni usando un diagramma e una sequenza temporale interattive.

## <a name="add-a-bookmark"></a>Aggiungi un segnalibro

1. Nella portale di Azure passare a **Sentinel** > **Threat Management** > **Hunting** per eseguire query per comportamenti sospetti e anomali.

2. Selezionare una delle query di caccia e a destra nei dettagli della query di caccia selezionare **Esegui query**. 

3. Selezionare **Visualizza risultati query**. Esempio:
    
    > [!div class="mx-imgBorder"]
    > ![visualizzare i risultati delle query da Azure Sentinel Hunting](./media/bookmarks/new-processes-observed-example.png)
    
    Questa azione consente di aprire i risultati della query nel riquadro **log** .

4. Dall'elenco dei risultati della query di log espandere la riga che contiene le informazioni che si trovano interessanti.

5. Selezionare i puntini di sospensione (...) a sinistra e quindi selezionare **Aggiungi segnalibro di ricerca**:
    
    > [!div class="mx-imgBorder"]
    > ![Aggiungere un segnalibro di ricerca alla query](./media/bookmarks/add-hunting-bookmark.png)

6. A destra, nel riquadro **Aggiungi segnalibro di caccia** , facoltativamente, aggiornare il nome del segnalibro, aggiungere i tag e le note per facilitare l'identificazione dell'elemento interessante.

7. Nella sezione **informazioni query** utilizzare le caselle a discesa per estrarre informazioni dai risultati della query per i tipi di entità **account**, **host**e **indirizzo IP** . Questa azione esegue il mapping del tipo di entità selezionato a una colonna specifica dal risultato della query. Esempio:
    
    > [!div class="mx-imgBorder"]
    > ![Mapping dei tipi di entità per il segnalibro Hunting](./media/bookmarks/map-entity-types-bookmark.png)
    
    Per visualizzare il segnalibro nel grafico di analisi (attualmente in anteprima), è necessario eseguire il mapping di almeno un tipo di entità che sia **account**, **host**o **indirizzo IP**. 

5. Fare clic su **Aggiungi** per eseguire il commit delle modifiche e aggiungere il segnalibro. Tutti i dati con segnalibro sono condivisi con altri investigatori ed è il primo passo verso un'esperienza di analisi collaborativa.

 
> [!NOTE]
> I risultati della query di log supportano i segnalibri ogni volta che questo riquadro viene aperto da Azure Sentinel. Ad esempio, si selezionano i**log** **generali** > dalla barra di spostamento, si selezionano i collegamenti degli eventi nel grafico indagini oppure si seleziona un ID avviso tra i dettagli completi di un evento imprevisto (attualmente in anteprima). Non è possibile creare segnalibri quando il riquadro **log** è aperto da altri percorsi, ad esempio direttamente da monitoraggio di Azure.

## <a name="view-and-update-bookmarks"></a>Visualizzare e aggiornare i segnalibri 

1. Nella portale di Azure passare a **Sentinel** > **Threat Management** > **Hunting**. 

2. Selezionare la scheda **segnalibri** per visualizzare l'elenco dei segnalibri.

3. Per individuare un segnalibro specifico, utilizzare la casella di ricerca o le opzioni di filtro.

4. Selezionare i singoli segnalibri e visualizzare i dettagli del segnalibro nel riquadro dei dettagli a destra.

5. Apportare le modifiche necessarie, che vengono salvate automaticamente.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Esplorazione dei segnalibri nel grafico di analisi

> [!IMPORTANT]
> L'esplorazione dei segnalibri nel grafico di analisi e il grafico di indagine sono attualmente in anteprima pubblica.
> Queste funzionalità vengono fornite senza un contratto di servizio e non sono consigliate per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Nella portale di Azure passare alla scheda **Sentinel** > **Threat Management** > **Hunting** > **Bookmarks** e selezionare il segnalibro o i segnalibri che si desidera analizzare.

2. Nei dettagli del segnalibro verificare che sia stato eseguito il mapping di almeno un'entità. Per le **entità**, ad esempio, vengono visualizzate le voci relative a **IP**, **computer**o **account**.

3. Fare **clic su** verifica per visualizzare il segnalibro nel grafico di analisi.

Per istruzioni sull'uso del grafico di indagine, vedere [usare il grafico di indagine per approfondire](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)la procedura.

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Aggiungere segnalibri a un evento imprevisto nuovo o esistente

> [!IMPORTANT]
> L'aggiunta di segnalibri a un evento imprevisto nuovo o esistente è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Nella portale di Azure passare alla scheda **Sentinel** > **Threat Management** > **Hunting** > **Bookmarks** e selezionare il segnalibro o i segnalibri che si desidera aggiungere a un evento imprevisto.

2. Selezionare **azioni evento imprevisto (anteprima)** dalla barra dei comandi:
    
    > [!div class="mx-imgBorder"]
    > ![Aggiungi segnalibri a evento imprevisto](./media/bookmarks/incident-actions.png)

3. Selezionare **Crea nuovo evento imprevisto** o **Aggiungi a evento imprevisto esistente**, a seconda delle esigenze. Quindi:
    
    - Per un nuovo evento imprevisto: Facoltativamente, aggiornare i dettagli dell'evento imprevisto, quindi selezionare **Crea**.
    - Per aggiungere un segnalibro a un evento imprevisto esistente: Selezionare un evento imprevisto, quindi selezionare **Aggiungi**. 

Per visualizzare il segnalibro nell'evento imprevisto: Passare a **Sentinel** > **Threat Management** > **Incidents** e selezionare l'evento imprevisto con il segnalibro. Selezionare **Visualizza dettagli completi**, quindi selezionare la scheda **segnalibri** .

## <a name="view-bookmarked-data-in-logs"></a>Visualizzare i dati con segnalibro nei log

Per visualizzare le query con segnalibro, i risultati o la cronologia, selezionare il segnalibro dalla scheda**segnalibri** **caccia** > e usare i collegamenti disponibili nel riquadro dei dettagli: 

- Consente di **visualizzare** la query di origine per visualizzare la query di origine nel riquadro **log** .

- **Visualizzare i log dei segnalibri** per visualizzare tutti i metadati dei segnalibri, che includono chi ha eseguito l'aggiornamento, i valori aggiornati e l'ora in cui si è verificato l'aggiornamento.

È anche possibile visualizzare i dati dei segnalibri non elaborati per tutti i segnalibri selezionando i **log dei segnalibri** nella barra dei comandi nella scheda**segnalibri** di **caccia** > :

> [!div class="mx-imgBorder"]
> ![Segnalibri-log](./media/bookmarks/bookmark-logs.png)

Questa visualizzazione Mostra tutti i segnalibri con i metadati associati. È possibile usare le query KQL ( [Keyword Query Language](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) ) per filtrare la versione più recente del segnalibro specifico che si sta cercando.

> [!NOTE]
> È possibile che si verifichi un ritardo significativo (espresso in minuti) tra il momento in cui si crea un segnalibro e quando questo viene visualizzato nella scheda **segnalibri** .

## <a name="delete-a-bookmark"></a>Elimina un segnalibro
 
1.  Nella portale di Azure passare alla scheda **Sentinel** > **Threat Management** > **Hunting** > **Bookmarks** e selezionare il segnalibro o i segnalibri che si desidera eliminare. 

2. Selezionare i puntini di sospensione (...) alla fine della riga e selezionare **Elimina segnalibro**.
    
Eliminando il segnalibro, il segnalibro verrà rimosso dall'elenco nella scheda **segnalibro** . La tabella Log Analytics **HuntingBookmark** continuerà a contenere le voci di segnalibro precedenti, ma la voce più recente modificherà il valore **SoftDelete** in true, semplificando la filtrazione dei segnalibri obsoleti. L'eliminazione di un segnalibro non comporta la rimozione di entità dall'esperienza di analisi associate ad altri segnalibri o avvisi. 


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come eseguire un'indagine di caccia usando i segnalibri in Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:


- [Cercare in modo proattivo le minacce](hunting.md)
- [Usare i notebook per eseguire campagne di caccia automatiche](notebooks.md)
