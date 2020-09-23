---
title: Aggiungere tag alle immagini in un progetto di etichettatura
title.suffix: Azure Machine Learning
description: Informazioni su come usare gli strumenti di aggiunta dei tag ai dati in un progetto di etichettatura di Azure Machine Learning.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: e34fa4af08be898785acbc6f00aa735c1412ec47
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897564"
---
# <a name="tag-images-in-a-labeling-project"></a>Aggiungere tag alle immagini in un progetto di etichettatura 

Dopo che l'amministratore di progetto ha [creato un progetto di etichettatura](https://docs.microsoft.com/azure/machine-learning/how-to-create-labeling-projects#create-a-labeling-project) in Azure Machine Learning, è possibile usare lo strumento di etichettatura (anteprima pubblica) per preparare rapidamente i dati per un progetto di Machine Learning. L'articolo illustra:

> [!div class="checklist"]
> * Come accedere ai progetti di etichettatura
> * Gli strumenti di etichettatura
> * Come usare gli strumenti per attività di etichettatura specifiche

## <a name="prerequisites"></a>Prerequisiti

* Un [account Microsoft](https://account.microsoft.com/account) o un account Azure Active Directory per l'organizzazione e il progetto
* Accesso a livello di collaboratore all'area di lavoro che contiene il progetto di etichettatura.

## <a name="sign-in-to-the-workspace"></a>Accedere all'area di lavoro

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com).

1. Selezionare la sottoscrizione e l'area di lavoro che contiene il progetto di etichettatura.  Ottenere queste informazioni dall'amministratore di progetto.

1. Selezionare **Etichettatura dei dati** sul lato sinistro per trovare il progetto.  

## <a name="understand-the-labeling-task"></a>Informazioni sull'attività di etichettatura

Nella tabella dei progetti di etichettatura dei dati selezionare **Collegamento etichetta** per il progetto.

Vengono visualizzate le istruzioni specifiche per il progetto. Descrivono il tipo di dati che verrà usato, come prendere le decisioni e includono altre informazioni pertinenti. Dopo aver letto le informazioni, nella parte superiore della pagina selezionare **Attività**.  In alternativa, nella parte inferiore della pagina selezionare **Avvia etichettatura**.

## <a name="common-features-of-the-labeling-task"></a>Funzionalità comuni dell'attività di etichettatura

In tutte le attività di etichettatura scegliere uno o più tag appropriati nel set specificato dall'amministratore del progetto. È possibile selezionare i primi nove tag usando i tasti numerici della tastiera.  

Nelle attività di classificazione delle immagini è possibile scegliere di visualizzare più immagini simultaneamente. Usare le icone sopra l'area dell'immagine per selezionare il layout. 

Per selezionare tutte le immagini visualizzate simultaneamente, usare **Seleziona tutto**. Per selezionare singole immagini, usare il pulsante di selezione circolare nell'angolo in alto a destra dell'immagine. È necessario selezionare almeno un'immagine per applicare un tag. Se si selezionano più immagini, il tag selezionato verrà applicato a tutte.

In questo caso è stato scelto un layout 2x2 e si sta per applicare il tag "Mammal" alle immagini dell'orso e dell'orca. All'immagine con lo squalo è già stato aggiunto il tag "Pesce cartilagineo", mentre per l'iguana non è stato ancora aggiunto alcun tag.

![Layout e selezione di più immagini](./media/how-to-label-images/layouts.png)

> [!Important] 
> Cambiare il layout solo quando si apre una nuova pagina di dati non etichettati. Quando si passa da un layout all'altro, l'attività di aggiunta dei tag in corso della pagina viene cancellata.

Azure abilita il pulsante **Invia** solo dopo aver completato l'aggiunta dei tag per tutte le immagini nella pagina. Selezionare **Invia** per salvare il lavoro.

Dopo aver inviato i tag per i dati a disposizione, Azure aggiornerà la pagina con un nuovo set di immagini dalla coda di lavoro.

### <a name="assisted-machine-learning-preview"></a>Machine Learning assistito (anteprima) 

> [!IMPORTANT]
> Il servizio Machine Learning assistito è attualmente disponibile in anteprima pubblica.
> La versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Gli algoritmi di Machine Learning possono essere attivati. Se nel progetto sono abilitati questi algoritmi, è possibile che vengano visualizzati gli elementi seguenti:

* Dopo aver etichettato una certa quantità di immagini, è possibile visualizzare **Attività raggruppate** nella parte superiore della schermata accanto al nome del progetto.  Ciò significa che le immagini vengono raggruppate per presentare immagini simili nella stessa pagina.  In tal caso, passare a una delle visualizzazioni di più immagini per sfruttare i vantaggi del raggruppamento.  

* In un secondo momento, è possibile visualizzare **Attività pre-etichettate** accanto al nome del progetto.  Le immagini verranno quindi visualizzate con un'etichetta suggerita che deriva da un modello di classificazione di Machine Learning. Nessun modello di Machine Learning ha una precisione del 100%. Sebbene vengano usate solo le immagini per cui il modello è attendibile, è possibile che le immagini siano ancora pre-etichettate in modo errato.  Quando vengono visualizzate queste etichette, correggere eventuali etichette errate prima di inviare la pagina.  

* Per i modelli di rilevamento degli oggetti, è possibile che siano già presenti riquadri delimitatori ed etichette.  Correggere quelli non corretti prima di inviare la pagina.

Soprattutto all'inizio di un progetto di etichettatura, il modello di Machine Learning può essere sufficientemente accurato da pre-etichettare solo un piccolo subset di immagini. Una volta etichettate queste immagini, il progetto di etichettatura tornerà all'etichettatura manuale per raccogliere più dati per il ciclo successivo di training del modello. Con il tempo, il modello diventerà più affidabile rispetto a una proporzione più elevata di immagini, ottenendo in seguito più attività di pre-etichettatura nel progetto.

## <a name="tag-images-for-multi-class-classification"></a>Aggiunta di tag alle immagini per la classificazione multi-classe

Se il progetto è di tipo "Classificazione delle immagini multi-classe", verrà assegnato un singolo tag all'intera immagine. Per esaminare le istruzioni in qualsiasi momento, passare alla pagina **Istruzioni** e selezionare **Visualizza istruzioni dettagliate**.

Se si ritiene di aver commesso un errore dopo l'assegnazione di un tag a un'immagine, è possibile correggerlo. Selezionare "**X**" nell'etichetta visualizzata sotto l'immagine per cancellare il tag. In alternativa, selezionare l'immagine e scegliere un'altra classe. Il valore appena selezionato sostituirà il tag applicato in precedenza.

## <a name="tag-images-for-multi-label-classification"></a>Aggiunta dei tag alle immagini per la classificazione multi-etichetta

Se si sta lavorando a un progetto di tipo "Classificazione delle immagini multi-etichetta", sarà necessario applicare uno *o più* tag a un'immagine. Per visualizzare le istruzioni specifiche del progetto, selezionare **Istruzioni** e passare a **Visualizza istruzioni dettagliate**.

Selezionare l'immagine da etichettare e quindi selezionare il tag. Il tag viene applicato a tutte le immagini selezionate, che quindi vengono deselezionate. Per applicare più tag, è necessario selezionare nuovamente le immagini. L'animazione seguente mostra l'assegnazione di tag multi-etichetta:

1. Viene usata l'opzione **Seleziona tutto** per applicare il tag "Ocean".
1. Viene selezionata una singola immagine per l'applicazione del tag "Closeup".
1. Vengono selezionate tre immagini per l'applicazione del tag "Wide angle".

![Animazione che mostra il flusso multi-etichetta](./media/how-to-label-images/multilabel.gif)

Per correggere un errore, è possibile fare clic sulla "**X**" per cancellare singoli tag oppure selezionare le immagini e quindi il tag, cancellandolo così da tutte le immagini selezionate. Questo scenario è illustrato di seguito. Se si fa clic su "Land", il tag verrà cancellato dalle due immagini selezionate.

![Screenshot che mostra più opzioni deselezionate](./media/how-to-label-images/multiple-deselection.png)

Azure abiliterà il pulsante **Invia** solo dopo aver applicato almeno un tag a ogni immagine. Selezionare **Invia** per salvare il lavoro.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Assegnazione di tag alle immagini e aggiunta di rettangoli di selezione per il rilevamento di oggetti

Se il progetto è di tipo "Identificazione oggetto (rettangolo di selezione)", è necessario specificare uno o più rettangoli di selezione nell'immagine e applicare un tag a ognuno. Per ogni immagine è possibile specificare più rettangoli di selezione, ognuno con un singolo tag. Usare **Visualizza istruzioni dettagliate** per determinare se nel progetto vengono usati più rettangoli di selezione.

1. Selezionare un tag per il rettangolo di selezione da creare.
1. Selezionare lo strumento **Rectangular box** (casella rettangolare) ![Rectangular box tool](./media/how-to-label-images/rectangular-box-tool.png) (Strumento Casella rettangolare) o premere "R".
3. Fare clic e trascinare in diagonale nell'area di destinazione per creare un rettangolo di selezione approssimativo. Per modificare il rettangolo di selezione, trascinare i bordi o gli angoli.

![Creazione del rettangolo di selezione](./media/how-to-label-images/bounding-box-sequence.png)

Per eliminare un rettangolo di selezione, fare clic sulla destinazione a forma di X visualizzata accanto ad esso dopo la creazione.

Non è possibile cambiare il tag di un rettangolo di selezione esistente. Se si commette un errore con l'assegnazione di tag, è necessario eliminare il rettangolo di selezione e crearne uno nuovo con il tag corretto.

Per impostazione predefinita, è possibile modificare i rettangoli di selezione esistenti. Questo comportamento è attivato/disattivato tramite lo strumento **Lock/unlock regions** (Blocca/Sblocca aree) ![Lock/unlock regions tool](./media/how-to-label-images/lock-bounding-boxes-tool.png) (Strumento Blocca/Sblocca aree) o "L". Se le aree sono bloccate, è possibile cambiare solo la forma o la posizione di un nuovo rettangolo di selezione.

Per modificare un rettangolo di selezione esistente, usare lo strumento **Regions manipulation** (Modifica aree) ![Regions manipulation tool](./media/how-to-label-images/regions-tool.png) (Strumento Modifica aree) o "M". Per modificare la forma, trascinare i bordi o gli angoli. Fare clic all'interno per trascinare l'intero rettangolo di selezione. Se non è possibile modificare un'area, è probabile che sia stato attivato o disattivato lo strumento **Lock/unlock regions** (Blocca/Sblocca aree).

Usare lo strumento **Template-based box** (Rettangolo basato su modello) ![Template-box tool](./media/how-to-label-images/template-box-tool.png) (Strumento Rettangolo basato su modello) o "T" per creare più rettangoli di selezione delle stesse dimensioni. Se l'immagine non contiene rettangoli di selezione e si attivano i rettangoli basati su modello, lo strumento genererà rettangoli da 50 x 50 pixel. Se si crea un rettangolo di selezione e quindi si attivano i rettangoli basati su modello, i nuovi rettangoli di selezione avranno le stesse dimensioni dell'ultimo creato. È possibile ridimensionare i rettangoli basati su modello dopo il posizionamento. Il ridimensionamento di un rettangolo basato su modello ha effetto solo sul rettangolo specifico.

Per eliminare *tutti* i rettangoli di selezione nell'immagine corrente, selezionare lo strumento **Delete All Regions** (Elimina tutte le aree) ![Delete regions tool](./media/how-to-label-images/delete-regions-tool.png) (Strumento Elimina aree).

Dopo aver creato i rettangoli di selezione per un'immagine, selezionare **Invia** per salvare il lavoro in corso, che altrimenti non verrà salvato.

## <a name="finish-up"></a>Completare la procedura

Quando si invia una pagina di dati con tag, Azure assegna all'utente nuovi dati non etichettati da una coda di lavoro. Se non sono disponibili altri dati senza etichetta, verrà visualizzato un messaggio con un collegamento alla home page del portale.

Al termine dell'etichettatura, selezionare il proprio nome nell'angolo in alto a destra del portale e quindi selezionare **Disconnetti**. Se non si esegue la disconnessione, la sessione di Azure dell'utente raggiungerà il "timeout" e i dati verranno assegnati a un altro etichettatore.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [eseguire il training di modelli di classificazione delle immagini in Azure](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)

