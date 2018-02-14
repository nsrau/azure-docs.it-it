---
title: Esperimenti di Azure AI Gallery | Microsoft Docs
description: Scoprire e condividere esperimenti in Azure AI Gallery.
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: f4248922-c961-4d3a-9e1b-aec743210166
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: garye
ms.openlocfilehash: 80c2ab0cd97ce5afef7e38bad9f0fa0e4f3f363c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="discover-experiments-in-azure-ai-gallery"></a>Scoprire e condividere esperimenti in Azure AI Gallery
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="experiments-for-machine-learning-studio"></a>Esperimenti per Machine Learning Studio
Cortana Intelligence Gallery contiene un'ampia gamma di [esperimenti](https://gallery.cortanaintelligence.com/experiments) sviluppati in [Azure Machine Learning Studio](https://studio.azureml.net), da veloci esperimenti di prova che illustrano una tecnica di apprendimento automatico specifica a soluzioni complete per la risoluzione di problemi di apprendimento automatico complessi.

> [!NOTE]
> Un ***esperimento*** è un'area di disegno in Machine Learning Studio da usare per costruire un modello di analisi predittiva. Il modello viene creato collegando i dati da vari moduli di analisi. È possibile provare idee diverse, effettuare esecuzioni di prova e alla fine distribuire il modello come servizio Web in Azure. Per un esempio di creazione di un esperimento semplice, vedere [Esercitazione di Machine Learning: Creare il primo esperimento in Azure Machine Learning Studio](create-experiment.md). Per una procedura dettagliata più completa della creazione di una soluzione di analisi predittiva, vedere [Procedura dettagliata: Sviluppare una soluzione di analisi predittiva per la valutazione del rischio di credito in Azure Machine Learning](walkthrough-develop-predictive-solution.md).
>
>

## <a name="discover"></a>Scoprire
Per cercare gli esperimenti disponibili in Gallery, aprire [Gallery](http://gallery.cortanaintelligence.com) e selezionare **Esperimenti** nella parte alta della home page di Gallery.

Nella pagina **[Esperimenti](https://gallery.cortanaintelligence.com/experiments)** è disponibile l'elenco degli ultimi esperimenti inseriti e quello degli esperimenti più visualizzati. Per visualizzare tutti gli esperimenti, selezionare il pulsante **Visualizza tutto**. Per cercare un esperimento specifico, selezionare **Visualizza tutto** e quindi selezionare i criteri di filtro. È anche possibile immettere i termini di ricerca nella casella **Ricerca** nella parte superiore della pagina di Gallery.

È possibile ottenere altre informazioni su un esperimento nella pagina di dettagli dell'esperimento. Per aprire una pagina di dettagli dell'esperimento, selezionare l'esperimento. Nella pagina dei dettagli di un esperimento, è possibile fornire commenti e suggerimenti oppure fare domande sull'esperimento nella sezione **Commenti**. È anche possibile condividere l'esperimento con amici o colleghi su Twitter o LinkedIn e invitare altri utenti a visualizzare la pagina di dettagli dell'esperimento inviando loro un collegamento tramite posta elettronica.

![Condividere l'elemento con amici](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Aggiungere un commento](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="download"></a>Download
È possibile scaricare nella propria area di lavoro di Machine Learning Studio una copia di un qualsiasi esperimento presente in Gallery. A quel punto è possibile modificare la copia per creare soluzioni personalizzate.

Azure AI Gallery offre due modi per importare la copia di un esperimento:

* **Da Gallery**. Se si trova un esperimento di proprio interesse in Gallery, è possibile scaricarne e aprirne una copia nella propria area di lavoro di Machine Learning Studio.
* **Da Machine Learning Studio**. In Machine Learning Studio è possibile usare qualsiasi esperimento presente in Gallery come modello per creare un nuovo esperimento.

### <a name="from-the-gallery"></a>Da Gallery

1. Aprire la pagina dei dettagli dell'esperimento in Gallery.
2. Selezionare **Apri in Studio**.

    ![Aprire un esperimento da Gallery](./media/gallery-experiments/open-experiment-from-gallery.png)

Quando si seleziona **Apri in Studio**, l'esperimento viene aperto nell'area di lavoro di Machine Learning Studio. Se non è già stato effettuato l'accesso a Machine Learning Studio, sarà necessario accedere con l'account Microsoft.

### <a name="from-within-machine-learning-studio"></a>Da Machine Learning Studio

1. In Machine Learning Studio selezionare **NUOVO**.
2. Selezionare **Esperimento**. È possibile scegliere da un elenco degli esperimenti di Gallery o trovare un esperimento specifico usando la casella **Ricerca**.
3. Spostare il mouse sopra un esperimento e quindi selezionare **Apri in Studio**. Per ottenere informazioni sull'esperimento, selezionare **Visualizza in Gallery**. Verrà aperta la pagina di dettagli dell'esperimento in Gallery.

    ![Aprire un esperimento di Gallery da Machine Learning Studio](./media/gallery-experiments/open-experiment-from-studio.png)

È possibile personalizzare, ripetere e distribuire un esperimento scaricato come qualsiasi altro esperimento creato in Machine Learning Studio.

![Esperimento aperto in Studio](./media/gallery-experiments/experiment-open-in-studio.png)

## <a name="contribute"></a>Contribuire
Quando si esegue l'accesso a Gallery si diventa membri della community di Gallery. I membri della community contribuire con i propri esperimenti, in modo che altri utenti possano trarre vantaggio dalle soluzioni individuate.

### <a name="publish-your-experiment-to-the-gallery"></a>Pubblicare un esperimento in Gallery

1. Accedere a Machine Learning Studio con il proprio account Microsoft.
2. Creare l'esperimento ed eseguirlo.
3. Quando l'esperimento è pronto per essere pubblicato in Gallery, selezionare **Publish to Gallery** (Pubblica in Gallery) nell'elenco di azioni sotto l'area di disegno dell'esperimento.

    ![Selezionare "Publish to Gallery" (Pubblica in Gallery)](./media/gallery-experiments/publish-experiment-to-gallery.png)
4. Nella pagina **Experiment Description** (Descrizione esperimento), immettere un titolo e i tag, possibilmente descrittivi. Evidenziare le tecniche utilizzate o i problemi reali risolvibili che l'esperimento punta a risolvere. Un esempio di titolo descrittivo per un esperimento è "Classificazione binaria: analisi del sentiment su Twitter".

    ![Immettere titolo e tag per la pubblicazione](./media/gallery-experiments/experiment-description.png)
5. Nella casella **RIEPILOGO** immettere un riepilogo dell'esperimento. Descrivere brevemente il problema che viene risolto con l'esperimento e com'è stato affrontato.
6. Nella casella **DESCRIZIONE DETTAGLIATA** descrivere i passaggi in ogni parte dell'esperimento. Ecco alcuni elementi utili da includere:
   * Screenshot del grafico dell'esperimento
   * Origini dati e spiegazione
   * Elaborazione dei dati
   * Progettazione delle funzioni
   * Descrizione modello
   * Risultati e valutazione delle prestazioni del modello

   È possibile usare la sintassi markdown per formattare la descrizione. Per visualizzare l'aspetto delle voci nella pagina di descrizione dell'esperimento una volta pubblicato, selezionare **Anteprima**.

   ![Selezionare "Anteprima" per visualizzare l'aspetto del testo](./media/gallery-experiments/preview-markdown-text.png)

   > [!TIP]
   > Le caselle di testo per l'anteprima e la modifica markdown sono molto piccole. È consigliabile scrivere la documentazione dell'esperimento in un editor markdown, copiarla e incollarla nella casella di testo in Gallery una volta completa. Dopo aver pubblicato l'esperimento, è possibile apportare correzioni usando strumenti standard basati su Web che usano la sintassi markdown per le modifiche e l'anteprima.

7. Nella pagina **Selezione immagine** scegliere un'immagine di anteprima per l'esperimento. L'immagine di anteprima viene visualizzata nella parte superiore della pagina dei dettagli dell'esperimento e nel riquadro dell'esperimento. Gli altri utenti vedranno un'immagine di anteprima mentre esplorano Gallery. È possibile caricare un'immagine dal computer oppure selezionarne una da Gallery.
    </br>
    ![Caricare o selezionare un'immagine per Gallery](./media/gallery-experiments/select-gallery-image.png)
8. Nella pagina **Impostazioni**, in **Visibilità**, scegliere se pubblicare il contenuto con l'impostazione **Public** (Pubblico) o se renderlo accessibile solo agli utenti dotati di un collegamento alla pagina con l'impostazione **Unlisted** (Non in elenco).

    ![Scelta tra pubblicazione pubblica o come applicazione non in elenco](./media/gallery-experiments/choose-public-or-unlisted.png)

    <!-- -->

   > [!TIP]
   > Per assicurarsi che la documentazione sia corretta prima di pubblicarla, è possibile usare l'opzione **Unlisted** (Non in elenco) prima di rendere pubblico l'esperimento. In un secondo momento sarà possibile modificare l'impostazione di visibilità su **Public** (Pubblico) nella pagina dei dettagli dell'esperimento.
   >
   >
9. Selezionare il segno di spunta **OK** per pubblicare l'esperimento in Gallery.

    ![Selezionare il segno di spunta OK per pubblicare l'esperimento](./media/gallery-experiments/ok-checkmark.png)

Per suggerimenti su come pubblicare un esperimento di alta qualità in Gallery, vedere [Suggerimenti per documentare e pubblicare un esperimento](#tips-for-documenting-and-publishing-your-experiment).

La procedura è terminata.

Ora è possibile visualizzare l'esperimento in Cortana Intelligence Gallery e condividere il collegamento con altri utenti. Se l'esperimento è stato pubblicato con l'impostazione di visibilità **Public** (Pubblico), verrà visualizzato in Gallery e nei relativi risultati di ricerca. È possibile modificare la documentazione dell'esperimento nella relativa pagina dei dettagli in qualsiasi momento dopo aver effettuato l'accesso a Gallery.

Per visualizzare l'elenco dei propri contributi, selezionare l'immagine nell'angolo superiore destro delle pagine della raccolta. Quindi selezionare il proprio nome per aprire la pagina dell'account.

![Selezionare il nome del proprio account](./media/gallery-experiments/click-account-name.png)

### <a name="update-your-experiment"></a>Aggiornare l'esperimento
Se si desidera, è possibile apportare modifiche al flusso di lavoro (moduli, parametri e così via) in un esperimento pubblicato in Gallery. In Machine Learning Studio, apportare le modifiche desiderate all'esperimento e quindi pubblicarlo di nuovo. L'esperimento pubblicato verrà aggiornato con le modifiche apportate.

È possibile modificare le informazioni seguenti dell'esperimento direttamente in Gallery:

* Nome dell'esperimento
* Riepilogo o descrizione
* Tag
* Image
* Impostazione di visibilità, tra **Public** (Pubblico) e **Unlisted** (Non in elenco)

È anche possibile eliminare l'esperimento da Gallery.

Queste modifiche o eliminazioni possono essere apportate in Gallery dalla pagina dei dettagli dell'esperimento o dalla pagina del profilo personale.


#### <a name="from-the-experiment-details-page"></a>Dalla pagina dei dettagli dell'esperimento
Nella pagina dei dettagli dell'esperimento selezionare **Modifica** per modificare i dettagli dell'esperimento.

![Selezionare Modifica per modificare l'esperimento](./media/gallery-experiments/edit-button.png)

La pagina dei dettagli passa alla modalità di modifica. Per apportare le modifiche, selezionare **Modifica** accanto al nome dell'esperimento, al riepilogo o ai tag. Dopo aver apportato tutte le modifiche desiderate, selezionare **Fine**.

![Selezionare "Modifica" per modificare i dettagli e quindi "Fine" al termine dell'operazione](./media/gallery-experiments/edit-details-page.png)

Per modificare le impostazioni di visibilità per l'esperimento tra **Public** (Pubblico) e **Unlisted** (Non in elenco) o per eliminare l'esperimento da Gallery, selezionare l'icona **Impostazioni**.

![Selezionare "Impostazioni" modificare la visibilità o eliminare l'esperimento](./media/gallery-experiments/settings-button.png)

#### <a name="from-your-profile-page"></a>Dalla pagina del profilo personale
Nella pagina del profilo personale è possibile selezionare la freccia verso il basso in corrispondenza dell'esperimento e selezionare **Modifica**. Viene visualizzata la pagina dei dettagli dell'esperimento in modalità di modifica. Dopo aver apportato tutte le modifiche desiderate, selezionare **Fine**.

È anche possibile eliminare l'esperimento da Gallery selezionando **Elimina**.

![Selezionare "Modifica" o "Elimina"](./media/gallery-experiments/edit-delete-buttons.png)

### <a name="tips-for-documenting-and-publishing-your-experiment"></a>Suggerimenti per documentare e pubblicare un esperimento
* Si può presupporre che il lettore disponga di esperienze precedenti nell'analisi scientifica dei dati, ma è comunque consigliabile usare un linguaggio semplice. Si consiglia di spiegare in dettaglio i vari elementi, se possibile.
* Cortana Intelligence Suite è una soluzione relativamente nuova, pertanto non tutti i lettori hanno familiarità nell'usarla. È quindi opportuno fornire informazioni esaurienti e spiegazioni dettagliate per consentire a tutti di svolgere correttamente l'esperimento.
* Gli elementi visivi possono essere utili per aiutare i lettori a interpretare e usare correttamente la documentazione dell'esperimento. Possono includere grafici e screenshot dei dati dell'esperimento. Per altre informazioni su come includere immagini nella documentazione dell'esperimento, vedere [Publishing Guidelines and Examples collection](https://gallery.cortanaintelligence.com/Collection/Publishing-Guidelines-and-Examples-1) (Linee guida per la pubblicazione e raccolta di esempi).
* Se si include un set di dati nell'esperimento, vale a dire se non si importa il set di dati tramite il modulo di importazione, questo farà parte dell'esperimento e verrà pubblicato in Gallery. Assicurarsi che i dati pubblicati dispongano di termini di licenza che autorizzino la condivisione e il download da parte degli utenti. I contributi pubblicati in Cortana Intelligence Gallery sono coperti dalle [Condizioni per l'utilizzo](https://azure.microsoft.com/support/legal/website-terms-of-use/)di Azure.

## <a name="frequently-asked-questions"></a>Domande frequenti
**Quali sono i requisiti a cui attenersi quando si invia o si modifica l'immagine associata a un esperimento?**

Le immagini inviate insieme all'esperimento vengono usate per creare il riquadro dell'esperimento con informazioni sull'autore che ha inserito il proprio contributo. È consigliabile che le immagini siano inferiori a 500 KB, con proporzioni di 3:2 e una risoluzione di 960 &#215; 640.

**Come vengono impiegati i set di dati usati nell'esperimento? Il set di dati viene pubblicato anche in Gallery?**

Se il set di dati non è stato importato tramite il modulo di importazione dati ma fa parte dell'esperimento, viene pubblicato in Gallery contestualmente all'esperimento. Assicurarsi che il set di dati pubblicato con l'esperimento disponga di termini di licenza appropriati. I termini di licenza devono consentire a chiunque di condividere e scaricare i dati.

**Per la realizzazione di un esperimento ho usato le mie credenziali per estrarre i dati da Azure HDInsight o SQL Server tramite un modulo di importazione dati. È possibile pubblicare questo tipo di esperimento? Come posso essere certo che le mie credenziali non saranno condivise?**

Al momento non è possibile pubblicare in Gallery un esperimento che usa le credenziali.

**Come posso inserire più tag?**

Dopo aver inserito un tag, premere il tasto TAB per inserirne un altro.

**[Passare a Gallery](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
