---
title: 'Avvio rapido: Creare un rilevatore di oggetti - Servizio Visione personalizzata'
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido descrive come usare il sito Web di Visione personalizzata per creare un modello di classificazione di immagini.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: anroth
ms.openlocfilehash: 0d9c175db1370fe07b3278a46d910c59d81df860
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969922"
---
# <a name="quickstart-how-to-build-an-object-detector-with-custom-vision"></a>Guida introduttiva: Come creare un rilevatore di oggetti con Visione personalizzata

In questa esercitazione dell'avvio rapido, si apprenderà a creare un rilevatore di oggetti tramite il sito Web di Visione personalizzata. Dopo aver creato un modello di rilevatore, è possibile usare il servizio Visione personalizzata per il rilevamento oggetti.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- Un set di immagini con cui eseguire il training del modello di rilevatore. È possibile usare il set di [immagini di esempio](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) in GitHub. In alternativa, è possibile scegliere immagini personalizzate usando i suggerimenti seguenti.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Creare risorse di Visione personalizzata nel portale di Azure

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Creare un nuovo progetto

Nel Web browser passare alla [pagina web Visione personalizzata](https://customvision.ai) e selezionare __Accedi__. Accedere con lo stesso account usato per il portale di Azure.

![Immagine della pagina di accesso](./media/browser-home.png)


1. Per creare il primo progetto, selezionare **New Project** (Nuovo progetto). Verrà visualizzata la finestra di dialogo **Crea nuovo progetto**.

    ![La finestra di dialogo New Project (Nuovo progetto) include campi per nome, descrizione e domini.](./media/get-started-build-detector/new-project.png)

1. Immettere un nome e una descrizione per il progetto. Selezionare quindi un gruppo di risorse. Se l'account connesso è associato a un account Azure, l'elenco a discesa Gruppo di risorse visualizzerà tutti i gruppi di risorse di Azure che includono una risorsa Servizio visione artificiale personalizzato. 

   > [!NOTE]
   > Se non sono disponibili gruppi di risorse, verificare di aver eseguito l'accesso a [customvision.ai](https://customvision.ai) con lo stesso account usato per il [portale di Azure](https://portal.azure.com/). Verificare inoltre di avere selezionato nel portale del servizio Visione personalizzata la stessa "Directory" corrispondente alla directory del portale di Azure in cui si trovano le risorse di Visione personalizzata. In entrambi i siti è possibile selezionare la directory nel menu a discesa dell'account nell'angolo superiore destro dello schermo. 

1. In __Tipi di progetto__ selezionare __Rilevamento oggetti__.

1. Selezionare quindi uno dei domini disponibili. Ogni dominio ottimizza il rilevatore per tipi specifici di immagini, come descritto nella tabella seguente. Sarà possibile modificare il dominio in un secondo momento, se necessario.

    |Domain|Scopo|
    |---|---|
    |__Generale__| Ottimizzato per un'ampia gamma di attività di rilevamento oggetti. Se nessuno degli altri domini risulta appropriato o si è in dubbio sul dominio da scegliere, selezionare il dominio generico. |
    |__Logo__|Ottimizzato per il rilevamento di logo dei marchi nelle immagini.|
    |__Domini compatti__| Ottimizzati per i vincoli di rilevamento in tempo reale su dispositivi mobili. I modelli generati da domini compatti possono essere esportati per l'esecuzione in locale.|

1. Selezionare infine __Crea progetto__.

## <a name="choose-training-images"></a>Scegliere le immagini di training

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

In questa sezione si caricheranno e si aggiungeranno manualmente i tag alle immagini per eseguire il training del rilevatore. 

1. Per aggiungere immagini, fare clic sul pulsante __Aggiungi immagini__ e quindi selezionare __Esplora file locali__. Selezionare __Apri__ per caricare le immagini.

    ![Il controllo per l'aggiunta di immagini è visualizzato in alto a sinistra e come pulsante in basso al centro.](./media/get-started-build-detector/add-images.png)

1. Le immagini caricate verranno visualizzate nella sezione **Senza tag** dell'interfaccia utente. Il passaggio successivo prevede l'aggiunta manuale dei tag agli oggetti che il rilevatore dovrà riconoscere. Fare clic sulla prima immagine per aprire la finestra di dialogo per l'aggiunta dei tag. 

    ![Immagini caricate nella sezione Senza tag](./media/get-started-build-detector/images-untagged.png)

1. Fare clic e trascinare un rettangolo intorno all'oggetto nell'immagine. Immettere quindi un nuovo nome di tag con il pulsante **+** oppure selezionare un tag esistente dall'elenco a discesa. È molto importante aggiungere i tag a ogni istanza degli oggetti che si desidera rilevare, perché il rilevatore usa l'area di sfondo senza tag come esempio negativo durante il training. Dopo aver aggiunto i tag, fare clic sulla freccia a destra per salvarli e per passare all'immagine successiva.

    ![Aggiunta di tag a un oggetto con una selezione rettangolare](./media/get-started-build-detector/image-tagging.png)

Per caricare un altro set di immagini, tornare all'inizio di questa sezione e ripetere i passaggi.

## <a name="train-the-detector"></a>Eseguire il training del rilevatore

Per eseguire il training del modello di rilevatore, selezionare il pulsante **Esegui il training**. Il rilevatore usa tutte le immagini correnti e i relativi tag per creare un modello in grado di identificare ogni oggetto con tag.

![Pulsante train (Esegui il training) in alto a destra nella barra degli strumenti dell'intestazione della pagina Web](./media/getting-started-build-a-classifier/train01.png)

Il processo di training dovrebbe richiedere solo alcuni minuti. Durante questo periodo, vengono visualizzate informazioni sul processo di training nella scheda **Prestazioni**.

![Finestra del browser con una finestra di dialogo di training nella sezione principale](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>Valutare il rilevatore

Al termine del training, le prestazioni del modello vengono calcolate e visualizzate. Il servizio Visione personalizzata usa le immagini inviate per il training per calcolare precisione e recupero e precisione media. Precisione e recupero sono due misure diverse dell'efficacia di un rilevatore:

- La **precisione** indica la frazione delle classificazioni identificate corrette. Se ad esempio il modello identificasse 100 immagini come cani e 99 di essi fossero effettivamente cani, la precisione sarebbe del 99%.
- Il **recupero** indica la frazione delle classificazioni effettive identificate correttamente. Se ad esempio fossero effettivamente presenti 100 immagini di mele e il modello ne identificasse 80 come mele, il recupero sarebbe dell'80%.

![I risultati del training mostrano la precisione e il recupero complessivi e la precisione media.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Soglia di probabilità

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Gestire le iterazioni di training

Ogni volta che si esegue il training del rilevatore, si crea una nuova _iterazione_ con le relative metriche delle prestazioni aggiornate. È possibile visualizzare tutte le iterazioni nel riquadro sinistro della scheda **Prestazioni**. Nel riquadro a sinistra è presente anche il pulsante **Elimina**, che consente di eliminare un'iterazione obsoleta. Quando si elimina un'iterazione, vengono eliminate anche tutte le immagini associate in modo univoco a tale iterazione.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione dell'avvio rapido si è appreso a creare ed eseguire il training di un modello di rilevamento oggetti tramite il sito Web di Visione personalizzata. È ora possibile ottenere altre informazioni sul processo iterativo per migliorare il modello.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](test-your-model.md)

