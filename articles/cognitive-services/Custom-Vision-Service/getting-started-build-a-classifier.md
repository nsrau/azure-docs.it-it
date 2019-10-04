---
title: 'Avvio rapido: Creare un classificatore - Servizio Visione personalizzata'
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido descrive come usare il sito Web di Visione personalizzata per creare un modello di classificazione di immagini.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: anroth
ms.openlocfilehash: dfc137375e35d0d13a34ff45b0c6639bcf6784df
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561062"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>Guida introduttiva: Come creare un classificatore con Visione personalizzata

Questo argomento di avvio rapido descrive come creare un classificatore tramite il sito Web di Visione personalizzata. Dopo aver creato un modello di classificatore, è possibile usare il servizio Visione personalizzata per la classificazione delle immagini.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- Un set di immagini con cui eseguire il training del classificatore. Per suggerimenti sulla scelta delle immagini, vedere di seguito.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Creare risorse di Visione personalizzata nel portale di Azure

Per usare il servizio Visione personalizzata, è necessario creare le risorse Training visione personalizzata e Previsioni visione personalizzata nel portale di Azure. Per creare tali risorse, completare la finestra di dialogo nella pagina [Create Custom Vision](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) (Crea visione personalizzata). 

## <a name="create-a-new-project"></a>Creare un nuovo progetto

Nel Web browser passare alla [pagina web Visione personalizzata](https://customvision.ai) e selezionare __Accedi__. Accedere con lo stesso account usato per il portale di Azure.

![Immagine della pagina di accesso](./media/browser-home.png)


1. Per creare il primo progetto, selezionare **New Project** (Nuovo progetto). Verrà visualizzata la finestra di dialogo **Crea nuovo progetto**.

    ![La finestra di dialogo New Project (Nuovo progetto) include campi per nome, descrizione e domini.](./media/getting-started-build-a-classifier/new-project.png)

1. Immettere un nome e una descrizione per il progetto. Selezionare quindi un gruppo di risorse. Se l'account connesso è associato a un account Azure, l'elenco a discesa Gruppo di risorse visualizzerà tutti i gruppi di risorse di Azure che includono una risorsa Servizio visione artificiale personalizzato. 

   > [!NOTE]
   > Se non sono disponibili gruppi di risorse, verificare di aver eseguito l'accesso a [customvision.ai](https://customvision.ai) con lo stesso account usato per il [portale di Azure](https://portal.azure.com/). Verificare inoltre di avere selezionato nel portale del servizio Visione personalizzata la stessa "Directory" di quella del portale di Azure in cui si trovano le risorse di Visione personalizzata. In entrambi i siti è possibile selezionare la directory nel menu a discesa dell'account nell'angolo superiore destro dello schermo. 

1. Selezionare __Classificazione__ in __Project Types__ (Tipi di progetto). In __Classification Types__ (Tipi di classificazione) scegliere quindi **Multilabel** (Multietichetta) o **Multiclass** (Multiclasse), a seconda del caso d'uso. La classificazione multietichetta applica un numero qualsiasi di tag a un'immagine (zero o più), mentre la classificazione multiclasse ordina le immagini in categorie singole (ogni immagine inviata verrà ordinata nel tag più probabile). Sarà possibile modificare il tipo di classificazione in un secondo momento, se necessario.

1. Selezionare quindi uno dei domini disponibili. Ogni dominio ottimizza il classificatore per tipi specifici di immagini, come descritto nella tabella seguente. Sarà possibile modificare il dominio in un secondo momento, se necessario.

    |Domain|Scopo|
    |---|---|
    |__Domande generiche__| Ottimizzato per un'ampia gamma di attività di classificazione di immagini. Se nessuno degli altri domini risulta appropriato o si è in dubbio sul dominio da scegliere, selezionare il dominio generico. |
    |__Food__ (Cibo)|Ottimizzato per fotografie di piatti come nel menù di un ristorante. Se si vogliono classificare fotografie di singoli frutti o verdure, usare il dominio Food (Cibo).|
    |__Landmarks__ (Luoghi di interesse)|Ottimizzato per i luoghi di interesse riconoscibili, sia naturali che artificiali. Il dominio offre i migliori risultati quando il luogo di interesse è chiaramente visibile nella fotografia. Il dominio è efficace anche se il luogo è leggermente nascosto da utenti posti davanti.|
    |__Retail__ (Vendita)|Ottimizzato per le immagini che si trovano in un catalogo di vendita o in un sito Web di vendita. Se si vogliono classificare con alta precisione vestiti, pantaloni e magliette o camicie, usare questo dominio.|
    |__Domini compatti__| Ottimizzati per i vincoli di classificazione in tempo reale su dispositivi mobili. I modelli generati da domini compatti possono essere esportati per l'esecuzione in locale.|

1. Selezionare infine __Crea progetto__.

## <a name="choose-training-images"></a>Scegliere le immagini di training

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

In questa sezione si caricheranno e si contrassegneranno manualmente le immagini per eseguire il training del classificatore. 

1. Per aggiungere immagini, fare clic sul pulsante __Aggiungi immagini__ e quindi selezionare __Esplora file locali__. Selezionare __Open__ (Apri) per passare all'assegnazione di tag. La selezione di tag verrà applicata all'intero gruppo di immagini selezionate per il caricamento, quindi è più facile caricare le immagini in gruppi separati in base ai tag desiderati. È anche possibile modificare i tag per le singole immagini dopo averle caricate.

    ![Il controllo per l'aggiunta di immagini è visualizzato in alto a sinistra e come pulsante in basso al centro.](./media/getting-started-build-a-classifier/add-images01.png)


1. Per creare un tag, immettere il testo nel campo __My Tags__ (Tag personali) e premere INVIO. Se il tag esiste già, verrà visualizzato in un menu a discesa. In un progetto multietichetta è possibile aggiungere più di un tag alle immagini, ma in un progetto multiclasse è possibile aggiungerne solo uno. Per completare il caricamento delle immagini e contrassegnarle, usare il pulsante __Upload [number] files__ (Carica [numero] file). 

    ![Immagine della pagina per l'aggiunta dei tag e il caricamento](./media/getting-started-build-a-classifier/add-images03.png)

1. Selezionare __Done__ (Fine) al termine del caricamento delle immagini.

    ![L'indicatore di stato mostra tutte le attività completate.](./media/getting-started-build-a-classifier/add-images04.png)

Per caricare un altro set di immagini, tornare all'inizio di questa sezione e ripetere i passaggi.

## <a name="train-the-classifier"></a>Training del classificatore

Per eseguire il training del classificatore, selezionare il pulsante **Train** (Esegui training). Il classificatore usa tutte le immagini correnti per creare un modello che identifica le qualità visive di ogni tag.

![Pulsante train (Esegui il training) in alto a destra nella barra degli strumenti dell'intestazione della pagina Web](./media/getting-started-build-a-classifier/train01.png)

Il processo di training dovrebbe richiedere solo alcuni minuti. Durante questo periodo, vengono visualizzate informazioni sul processo di training nella scheda **Prestazioni**.

![Finestra del browser con una finestra di dialogo di training nella sezione principale](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Valutare il classificatore

Al termine del training, le prestazioni del modello vengono stimate e visualizzate. Il Servizio visione artificiale personalizzato usa le immagini inviate per il training per calcolare precisione e recupero, con un processo noto come [convalida incrociata k-fold](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Precisione e recupero sono due misure diverse dell'efficacia di un classificatore:

- La **precisione** indica la frazione delle classificazioni identificate corrette. Se ad esempio il modello identificasse 100 immagini come cani e 99 di essi fossero effettivamente cani, la precisione sarebbe del 99%.
- Il **recupero** indica la frazione delle classificazioni effettive identificate correttamente. Se ad esempio fossero effettivamente presenti 100 immagini di mele e il modello ne identificasse 80 come mele, il recupero sarebbe dell'80%.

![I risultati di training mostrano la precisione e il richiamo complessivi, oltre alla precisione e al richiamo per ogni tag nella funzione di classificazione.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Soglia di probabilità

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Gestire le iterazioni di training

Ogni volta che si esegue il training del classificatore, si crea una nuova _iterazione_ con le metriche delle prestazioni aggiornate. È possibile visualizzare tutte le iterazioni nel riquadro sinistro della scheda **Prestazioni**. Nel riquadro a sinistra è presente anche il pulsante **Elimina**, che consente di eliminare un'iterazione obsoleta. Quando si elimina un'iterazione, vengono eliminate anche tutte le immagini associate in modo univoco a tale iterazione.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato descritto come creare ed eseguire il training di un modello di classificazione di immagini tramite il sito Web di Visione personalizzata. È ora possibile ottenere altre informazioni sul processo iterativo per migliorare il modello.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](test-your-model.md)

