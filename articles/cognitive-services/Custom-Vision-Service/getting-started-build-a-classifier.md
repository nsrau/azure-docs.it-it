---
title: Creare un classificatore - Servizio visione artificiale personalizzato
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare il sito Web di Visione personalizzata per creare un modello di classificazione immagini.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: d0f0f3b120187a7538989f219876a8c10569a98e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59051474"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Come creare un classificatore con Visione personalizzata

Per usare il Servizio visione artificiale personalizzato per la classificazione immagini, è prima di tutto necessario creare un modello di classificatore. In questa guida si apprenderà come creare un classificatore tramite il sito Web di Visione personalizzata.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure valida. [Creare un account](https://azure.microsoft.com/free/) gratuitamente.
- Un set di immagini con cui eseguire il training del classificatore. Per suggerimenti sulla scelta delle immagini, vedere di seguito.


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Creare le risorse di visione artificiale personalizzato nel portale di Azure
Per usare il servizio Visione personalizzata, è necessario creare risorse di Training visione personalizzata e Stima nel [portale di Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). Verrà creata una risorsa di training e stima. 

## <a name="create-a-new-project"></a>Creare un nuovo progetto

Nel Web browser passare alla [pagina web Visione personalizzata](https://customvision.ai) e selezionare __Accedi__. Accedere con lo stesso account usato per accedere al portale di Azure.

![Immagine della pagina di accesso](./media/browser-home.png)


1. Per creare il primo progetto, selezionare **New Project** (Nuovo progetto). Verrà visualizzata la finestra di dialogo **Crea nuovo progetto**.

    ![La finestra di dialogo New Project (Nuovo progetto) include campi per nome, descrizione e domini.](./media/getting-started-build-a-classifier/new-project.png)

1. Immettere un nome e una descrizione per il progetto. Selezionare quindi un gruppo di risorse. Se l'account connesso è associato a un account Azure, l'elenco a discesa Gruppo di risorse visualizzerà tutti i gruppi di risorse di Azure che includono una risorsa Servizio visione artificiale personalizzato. 

   > [!NOTE]
   > Se nessun gruppo di risorse è disponibile, per confermare di aver effettuato [customvision.ai](https://customvision.ai) con lo stesso account è usato per accedere al [portale di Azure](https://portal.azure.com/). Verificare inoltre di avere selezionato nel portale del servizio Visione personalizzata la stessa "Directory" di quella del portale di Azure in cui si trovano le risorse di Visione personalizzata. In entrambi i siti è possibile selezionare la directory nel menu a discesa dell'account nell'angolo superiore destro dello schermo. 

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

Come minimo, è consigliabile usare almeno 30 immagini per ogni tag nel set di training iniziale. È anche opportuno raccogliere alcune immagini aggiuntive per testare il modello dopo che ne è stato eseguito il training.

Per eseguire il training del modello in modo efficace, usare le immagini con diversi oggetti visivi. Selezionare immagini diverse per:
* angolazione
* illuminazione
* background
* stile visivo
* soggetti singoli/raggruppati
* size
* type

Assicurarsi anche che tutte le immagini di training soddisfino i criteri seguenti:
* formato JPG, PNG o BMP
* dimensioni massime pari a 6 MB (4 MB per le immagini per la previsione)
* almeno 256 pixel sul bordo più corto. Le immagini più piccole verranno automaticamente ingrandite dal Servizio visione artificiale personalizzato

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

Si noti il dispositivo di scorrimento **Probability Threshold** (Soglia probabilità) nel riquadro sinistro della scheda **Prestazioni**. Si tratta della soglia per considerare corretta una probabilità prevista quando si calcolano precisione e recupero.

L'interpretazione di chiamate di previsione con una soglia di probabilità elevata tende a restituire risultati con precisione elevata a scapito del recupero: le classificazioni trovate sono corrette, ma molte non vengono trovate. Con una soglia di probabilità bassa accade il contrario: viene trovata la maggior parte delle classificazioni effettive, ma in tale set sono presenti falsi positivi. Tenendo presente questo aspetto, è consigliabile impostare la soglia di probabilità in base alle esigenze specifiche del progetto. In seguito è consigliabile usare lo stesso valore di soglia di probabilità sul lato client come filtro quando si ricevono i risultati della previsione dal modello.

## <a name="manage-training-iterations"></a>Gestire le iterazioni di training

Ogni volta che si esegue il training del classificatore, si crea una nuova _iterazione_ con le metriche delle prestazioni aggiornate. È possibile visualizzare tutte le iterazioni nel riquadro sinistro della scheda **Prestazioni**. Nel riquadro a sinistra è presente anche il pulsante **Elimina**, che consente di eliminare un'iterazione obsoleta. Quando si elimina un'iterazione, vengono eliminate anche tutte le immagini associate in modo univoco a tale iterazione.

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato descritto come creare ed eseguire il training di un modello di classificazione immagini usando il sito Web di Visione personalizzata. È ora possibile ottenere altre informazioni sul processo iterativo per migliorare il modello.

[Testare un modello e ripeterne il training](test-your-model.md)

