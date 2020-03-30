---
title: Usare i segnalibri di ricerca per le indagini sui dati in Azure SentinelUse hunting bookmarks for data investigations in Azure Sentinel
description: Questo articolo descrive come usare i segnalibri di ricerca di Azure Sentinel per tenere traccia dei dati.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: eacc104bf7157732f91507f631bcda2cd166bfb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588689"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Tenere traccia dei dati durante la caccia con Azure Sentinel

La caccia alle minacce richiede in genere la revisione di montagne di dati di log alla ricerca di prove di un comportamento dannoso. Durante questo processo, gli investigatori trovano gli eventi che vogliono ricordare, rivisitare e analizzare come parte della convalida di potenziali ipotesi e la comprensione della storia completa di un compromesso.

La ricerca di segnalibri in Azure Sentinel consente di eseguire questa operazione, conservando le query eseguite in **Azure Sentinel - Registri,** insieme ai risultati della query ritenuti pertinenti. Puoi anche registrare le tue osservazioni contestuali e fare riferimento ai tuoi risultati aggiungendo note e tag. I dati con segnalibri sono visibili a te e ai tuoi compagni di squadra per una facile collaborazione.

È possibile rivedere i dati preferiti in qualsiasi momento nella scheda **Segnalibri** del riquadro **di caccia.** È possibile utilizzare le opzioni di filtro e ricerca per trovare rapidamente dati specifici per l'indagine corrente. In alternativa, è possibile visualizzare i dati preferiti direttamente nella tabella **HuntingBookmark** nell'area di lavoro di Log Analytics. Ad esempio:

> [!div class="mx-imgBorder"]
> ![visualizzare la tabella HuntingBookmark](./media/bookmarks/bookmark-table.png)

La visualizzazione dei segnalibri dalla tabella consente di filtrare, riepilogare e unire i dati preferiti con altre origini dati, semplificando la ricerca di prove di corroborazione.

Attualmente in anteprima, se si trova qualcosa che deve essere risolto con urgenza durante la caccia nei log, in un paio di clic, è possibile creare un segnalibro e promuoverlo a un incidente, o aggiungere il segnalibro a un evento imprevisto esistente. Per altre informazioni sugli eventi imprevisti, vedere [Esercitazione: esaminare gli eventi imprevisti con Azure Sentinel.For more information about incidents, see Tutorial: Investigate incidents with Azure Sentinel](tutorial-investigate-cases.md). 

Anche in anteprima, è possibile visualizzare i dati preferiti, facendo clic su **Indaga** dai dettagli del segnalibro. In questo modo viene avviata l'esperienza di indagine in cui è possibile visualizzare, analizzare e comunicare visivamente i risultati utilizzando un diagramma e una sequenza temporale interattivi del diagramma di entità.

## <a name="add-a-bookmark"></a>Aggiungere un segnalibro

1. Nel portale di Azure passare a **Sentinel** > **Threat management** > **Hunting** per eseguire query per comportamenti sospetti e anomali.

2. Selezionare una delle query di ricerca e, a destra, nei dettagli della query di ricerca, selezionare **Esegui query**. 

3. Selezionare **Visualizza risultati query**. Ad esempio:
    
    > [!div class="mx-imgBorder"]
    > ![visualizzare i risultati delle query dalla ricerca di Azure SentinelView query results from Azure Sentinel hunting](./media/bookmarks/new-processes-observed-example.png)
    
    Questa azione consente di aprire i risultati della query nel riquadro **Registri.**

4. Nell'elenco dei risultati della query del log utilizzare le caselle di controllo per selezionare una o più righe contenenti le informazioni individuate interessanti.

5. Selezionare **Aggiungi segnalibro**:
    
    > [!div class="mx-imgBorder"]
    > ![Aggiungere un segnalibro di ricerca alla query](./media/bookmarks/add-hunting-bookmark.png)

6. A destra, nel riquadro **Aggiungi segnalibro,** facoltativamente, aggiornare il nome del segnalibro, aggiungere tag e note per identificare ciò che era interessante per l'elemento.

7. Nella sezione **Informazioni query** utilizzare le caselle di riepilogo a discesa per estrarre informazioni dai risultati della query per i tipi di entità **Account**, **Host**e **Indirizzo IP.** Questa azione esegue il mapping del tipo di entità selezionato a una colonna specifica dal risultato della query. Ad esempio:
    
    > [!div class="mx-imgBorder"]
    > ![Tipi di entità mappa per la ricerca di un segnalibro](./media/bookmarks/map-entity-types-bookmark.png)
    
    Per visualizzare il segnalibro nel grafico di indagine (attualmente in anteprima), è necessario eseguire il mapping di almeno un tipo di entità **Account**, **Host**o **Indirizzo IP**. 

5. Fare clic su **Salva** per eseguire il commit delle modifiche e aggiungere il segnalibro. Tutti i dati preferiti sono condivisi con altri ricercatori ed è un primo passo verso un'esperienza di indagine collaborativa.

 
> [!NOTE]
> I risultati della query di log supportano i segnalibri ogni volta che questo riquadro viene aperto da Azure Sentinel.The log query results support bookmarks whenever this pane is opened from Azure Sentinel. Ad esempio, si seleziona**Registri** **generali** > dalla barra di spostamento, si selezionano i collegamenti agli eventi nel grafico delle indagini o si seleziona un ID avviso dai dettagli completi di un evento imprevisto (attualmente in anteprima). Non è possibile creare segnalibri quando il riquadro Log viene aperto da altre posizioni, ad esempio direttamente da Monitoraggio di Azure.You can't create bookmarks when the **Logs** pane is opened from other locations, such as directly from Azure Monitor.

## <a name="view-and-update-bookmarks"></a>Visualizzare e aggiornare i segnalibri 

1. Nel portale di Azure passare a **Sentinel** > **Threat management** > **Hunting**. 

2. Selezionare la scheda **Segnalibri** per visualizzare l'elenco dei segnalibri.

3. Per trovare un segnalibro specifico, utilizzare la casella di ricerca o le opzioni di filtro.

4. Selezionare singoli segnalibri e visualizzare i dettagli del segnalibro nel riquadro dei dettagli a destra.

5. Apportare le modifiche necessarie, che vengono salvate automaticamente.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Esplorazione dei segnalibri nel grafico delle indagini

> [!IMPORTANT]
> L'esplorazione dei segnalibri nel grafico delle indagini e il grafico delle indagini stesso sono attualmente in anteprima pubblica.
> Queste funzionalità vengono fornite senza un contratto di servizio e non consigliate per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Nel portale di Azure passare alla scheda **Sentinel** > **Threat management** > **Hunting** > **Bookmarks** e selezionare il segnalibro o i segnalibri da analizzare.

2. Nei dettagli del segnalibro verificare che sia mappata almeno un'entità. Ad esempio, per **ENTITIES**, vengono visualizzate le voci per **IP**, **Computer**o **Account**.

3. Fare clic su **Indaga** per visualizzare il segnalibro nel grafico dell'indagine.

Per istruzioni sull'utilizzo del grafico delle indagini, vedere [Usare il grafico dell'indagine per approfondire](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)la visualizzazione.

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Aggiungere segnalibri a un evento imprevisto nuovo o esistente

> [!IMPORTANT]
> L'aggiunta di segnalibri a un evento imprevisto nuovo o esistente è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Nel portale di Azure passare alla scheda **Sentinel** > **Threat management** > **Hunting** > **Bookmarks** e selezionare il segnalibro o i segnalibri che si desidera aggiungere a un evento imprevisto.

2. Selezionare **Azioni evento imprevisto (anteprima)** dalla barra dei comandi:Select Incident actions (Preview) from the command bar:
    
    > [!div class="mx-imgBorder"]
    > ![Aggiungere segnalibri all'evento imprevisto](./media/bookmarks/incident-actions.png)

3. Selezionare **Crea nuovo evento imprevisto** o Aggiungi a eventi **imprevisti esistenti**, in base alle esigenze. Quindi:
    
    - Per un nuovo evento imprevisto: facoltativamente aggiornare i dettagli per l'evento imprevisto e quindi selezionare **Crea**.
    - Per aggiungere un segnalibro a un evento imprevisto esistente: selezionare un evento imprevisto e quindi selezionare **Aggiungi**. 

Per visualizzare il segnalibro all'interno dell'evento imprevisto: passare a**Eventi imprevisti** di**gestione** > delle minacce **Sentinel** > e selezionare l'evento imprevisto con il segnalibro. Selezionare **Visualizza dettagli completi**, quindi selezionare la scheda **Segnalibri.**

> [!TIP]
> In alternativa all'opzione **Azioni evento imprevisto (anteprima)** nella barra dei comandi, è possibile utilizzare il menu di scelta rapida (**...**) per uno o più segnalibri per selezionare le opzioni Crea nuovo **evento imprevisto**, **Aggiungi a evento imprevisto esistente**e Rimuovi **dall'evento imprevisto**. 

## <a name="view-bookmarked-data-in-logs"></a>Visualizzare i dati preferiti nei log

Per visualizzare le query, i risultati o la relativa cronologia con**segnalibri,** selezionare il segnalibro dalla scheda Segnalibri di **caccia** > e utilizzare i collegamenti forniti nel riquadro dei dettagli: 

- **Visualizzare** la query di origine per visualizzare la query di origine nel riquadro **Registri.**

- **Visualizzare i registri dei segnalibri** per visualizzare tutti i metadati dei segnalibri, inclusi gli utenti che hanno eseguito l'aggiornamento, i valori aggiornati e l'ora in cui si è verificato l'aggiornamento.

È inoltre possibile visualizzare i dati dei segnalibri non elaborati per tutti i segnalibri selezionando **Registri segnalibri** dalla barra dei comandi nella scheda**Segnalibri** di **caccia:** > 

> [!div class="mx-imgBorder"]
> ![Registri dei segnalibri](./media/bookmarks/bookmark-logs.png)

Questa visualizzazione mostra tutti i segnalibri con i metadati associati. È possibile utilizzare le query [KQL (Keyword Query Language)](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) per filtrare fino alla versione più recente del segnalibro specifico che si sta cercando.

> [!NOTE]
> Può esserci un ritardo significativo (misurato in minuti) tra il momento in cui si crea un segnalibro e il momento in cui viene visualizzato nella scheda **Segnalibri.**

## <a name="delete-a-bookmark"></a>Eliminare un segnalibro
 
1.  Nel portale di Azure passare alla scheda **Sentinel** > **Threat management** > **Hunting** > **Bookmarks** e selezionare il segnalibro o i segnalibri da eliminare. 

2. Fare clic con il pulsante destro del mouse sulle selezioni e selezionare l'opzione per eliminare il segnalibro o i segnalibri. Ad esempio, **Elimina segnalibro** se è stato selezionato un solo segnalibro e **Elimina 2 segnalibri** se sono stati selezionati due segnalibri.
    
L'eliminazione del segnalibro comporta la rimozione del segnalibro dall'elenco nella scheda **Segnalibro.** La tabella **HuntingBookmark** per l'area di lavoro di Log Analytics continuerà a contenere voci di segnalibri precedenti, ma la voce più recente modificherà il valore **SoftDelete** su true, semplificando la filtrata dei vecchi segnalibri. L'eliminazione di un segnalibro non comporta la rimozione di entità dall'esperienza di indagine associata ad altri segnalibri o avvisi. 


## <a name="next-steps"></a>Passaggi successivi

In this article, you learned how to run a hunting investigation using bookmarks in Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:


- [Caccia proattivamente alle minacce](hunting.md)
- [Utilizzare i blocchi appunti per eseguire campagne di caccia automatizzate](notebooks.md)
