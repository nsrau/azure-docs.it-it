---
title: Come usare lo strumento di etichettatura dei dati di Azure Machine Learning
title.suffix: Azure Machine Learning
description: Questo articolo illustra come usare gli strumenti di aggiunta dei tag ai dati in un progetto di etichettatura di Azure Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 9cbebb905ba39dabaf905be2fa741702bd1a0088
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838949"
---
# <a name="how-to-tag-images-in-a-labeling-project"></a>Come aggiungere i tag alle immagini in un progetto di etichettatura

Dopo che l'amministratore del progetto ha creato un progetto di etichettatura in Azure Machine Learning, è possibile usare lo strumento di etichettatura per preparare rapidamente i dati per un progetto di Machine Learning. 

> [!div class="checklist"]
> * Come accedere ai progetti di etichettatura
> * Strumenti di etichettatura
> * Come usare gli strumenti per attività di etichettatura specifiche

## <a name="prerequisites"></a>Prerequisiti

* URL del portale di etichettatura per un progetto di etichettatura dei dati in esecuzione
* Un [account Microsoft](https://account.microsoft.com/account) o
* Un account Azure Active Directory per l'organizzazione e il progetto

> [!Note]
> L'amministratore del progetto può trovare l'URL del portale di etichettatura nella scheda **Dettagli** all'interno della pagina **Dettagli del progetto**. 

## <a name="logging-in-to-the-projects-labeling-portal"></a>Accesso al portale di etichettatura del progetto

Passare all'URL del portale di etichettatura fornito dall'amministratore del progetto. 

Eseguire l'accesso con l'account di posta elettronica usato dall'amministratore per aggiungere l'utente al team. Per la maggior parte degli utenti, l'accesso verrà eseguito con l'account Microsoft. Se il progetto di etichettatura usa Azure Active Directory, questa sarà la modalità di accesso. 

## <a name="understanding-the-labeling-task"></a>Informazioni sull'attività di etichettatura

Dopo aver eseguito l'accesso, verrà visualizzata la pagina Panoramica del progetto. 

La prima operazione da fare è **visualizzare le istruzioni dettagliate**. Queste istruzioni sono specifiche per il progetto e descrivono il tipo di dati che verrà usato, come prendere le decisioni e altre informazioni pertinenti. Al termine, tornare alla pagina del progetto e scegliere **Start labeling** (Avvia etichettatura).

## <a name="common-features-of-the-labeling-task"></a>Funzionalità comuni dell'attività di etichettatura

Tutte le attività di etichettatura delle immagini prevedono la scelta dei tag appropriati da un set specificato dall'amministratore del progetto. È possibile scegliere tra i primi nove tag usando i tasti numerici sulla tastiera.  

Le attività di classificazione delle immagini consentono di scegliere se presentare più immagini simultaneamente. I diversi layout disponibili possono essere selezionati tramite le icone sopra l'area delle immagini. È possibile selezionare tutte le immagini visualizzate simultaneamente premendo il pulsante **Seleziona tutto**. Selezionare le foto singole usando il pulsante di selezione circolare nella parte superiore destra dell'area dell'immagine. È necessario selezionare almeno un'immagine per applicare un tag. Se sono state selezionate più immagini, la selezione di un tag applicherà il tag in questione a ogni foto selezionata.

In questo caso è stato scelto un layout 2x2 e si sta per applicare il tag "mammifero" alle immagini dell'orso e dell'orca. All'immagine con lo squalo è già stato aggiunto il tag "pesce cartilagineo" e per l'iguana non è ancora stato aggiunto alcun tag.

![Layout e selezione di più immagini](media/how-to-label-images/layouts.png)

> [!Important] 
> Cambiare il layout solo quando è visualizzata una pagina nuova di dati non etichettati. Quando si passa da un layout all'altro, l'attività di aggiunta dei tag in corso della pagina viene cancellata. 

Azure abilita il pulsante **Invia** solo dopo aver completato l'aggiunta dei tag per tutte le immagini nella pagina. Premere **Invia** per salvare il lavoro. 

Dopo aver inviato i tag per i dati a disposizione, Azure aggiornerà la pagina con un nuovo set di immagini dalla coda di lavoro.  

## <a name="tagging-images-for-multi-class-classification"></a>Aggiunta dei tag alle immagini per la classificazione multi-classe

Se il progetto è di tipo "Classificazione delle immagini multi-classe", verrà assegnato un singolo tag all'intera immagine. In qualsiasi momento scegliere la pagina **Istruzioni**  e passare a **View detailed instructions** (Visualizza le istruzioni dettagliate) per visualizzare le indicazioni specifiche per il progetto. 

Come indicato in precedenza, è possibile scegliere tra vari layout di presentazione delle immagini. Se, dopo la selezione di un'immagine e l'assegnazione di un tag, ci si accorge di aver commesso un errore, è possibile correggerlo. Se, nell'etichetta mostrata sotto l'immagine, si fa clic sulla destinazione `X`, il tag verrà cancellato. In alternativa, se si seleziona l'immagine e si sceglie un'altra classe, il tag passerà al nuovo valore selezionato.

## <a name="tagging-images-for-multi-label-classification"></a>Aggiunta dei tag alle immagini per la classificazione multi-etichetta

Se si sta lavorando a un progetto di tipo "Classificazione delle immagini multi-etichetta", sarà necessario applicare uno _o più_ tag a un'immagine. In qualsiasi momento scegliere la pagina **Istruzioni**  e passare a **View detailed instructions** (Visualizza le istruzioni dettagliate) per visualizzare le indicazioni specifiche per il progetto. 

Selezionare l'immagine da etichettare e quindi fare clic sul tag. Quando si sceglie il tag, questo viene applicato a tutte le immagini selezionate e le deseleziona. Per applicare più tag, è necessario selezionare nuovamente le immagini. Questa animazione mostra una pagina di assegnazione di tag multi-etichetta:

* Viene usata l'opzione **Seleziona tutto** per applicare il tag "Ocean"
* Viene selezionata una singola immagine per l'applicazione del tag "Closeup"
* Vengono selezionate tre immagini per l'applicazione del tag "Wide angle"

![Animazione che mostra il flusso multi-etichetta](media/how-to-label-images/multilabel.gif)

Per correggere un errore, è possibile fare clic su `X` per cancellare singoli tag o selezionare le immagini e scegliere il tag, cancellando così il tag da tutte le immagini selezionate. Questo scenario è illustrato di seguito. Se si fa clic su "Land", il tag verrà cancellato dalle due immagini selezionate.

![Screenshot che mostra la deselezione multipla](media/how-to-label-images/multiple-deselection.png)

Azure abiliterà il pulsante **Invia** solo dopo aver applicato almeno un tag a ogni immagine. Premere **Invia** per salvare il lavoro.

## <a name="tagging-images-and-bounding-boxes-for-object-detection"></a>Assegnazione di tag alle immagini e rettangoli di selezione per il rilevamento di oggetti

Se il progetto è di tipo "identificazione oggetto (rettangolo di selezione)", è necessario specificare uno o più rettangoli di selezione nell'immagine e applicare un tag a tale rettangolo. Ogni immagine può contenere più rettangoli di selezione, ognuno con un singolo tag. Usare **View detailed instructions** (Visualizza le istruzioni dettagliate) per determinare se l'aggiunta di più rettangoli di selezione è appropriata per il progetto.

1. Selezionare un tag per il rettangolo di selezione da creare
1. Selezionare lo strumento **Rectangular box** (casella rettangolare) ![Rectangular box tool](media/how-to-label-images/rectangular-box-tool.png) (Strumento Casella rettangolare) o premere 'R' 
1. Fare clic e trascinare in diagonale nell'area di destinazione per creare un rettangolo di selezione approssimativo
    * Regolare il rettangolo di selezione facendo clic e trascinando i bordi o gli angoli del rettangolo

![Screenshot della creazione di un rettangolo di selezione di base](media/how-to-label-images/bounding-box-sequence.png)

Per eliminare il rettangolo di selezione, fare clic sulla destinazione a forma di X visualizzata accanto al rettangolo di selezione dopo la creazione.

Non è possibile riassegnare il tag di un rettangolo di selezione esistente. Se si commette un errore con l'assegnazione di tag, è necessario eliminare il rettangolo di selezione e crearne uno nuovo con il tag corretto.

Per impostazione predefinita, è possibile modificare i rettangoli di selezione esistenti. Questo comportamento è attivato/disattivato tramite lo strumento **Lock/unlock regions** (Blocca/Sblocca aree) ![Lock/unlock regions tool](media/how-to-label-images/lock-bounding-boxes-tool.png) (Strumento Blocca/Sblocca aree) o "L". Se le aree sono bloccate, è possibile modificare solo la forma o la posizione di un nuovo rettangolo di selezione.

Per modificare un rettangolo di selezione esistente, usare lo strumento **Regions manipulation** (Modifica aree) ![Regions manipulation tool](media/how-to-label-images/regions-tool.png) (Strumento Modifica aree) o "M". È possibile fare clic e trascinare i bordi o gli angoli per regolare la forma. Se si fa clic all'interno, è possibile trascinare l'intero rettangolo di selezione. Se non è possibile modificare un'area, è probabile che sia stato attivato o disattivato lo strumento **Lock/unlock regions** (Blocca/Sblocca aree). 

Usare lo strumento **Template-based box** (Rettangolo basato su modello) ![Template-box tool](media/how-to-label-images/template-box-tool.png) (Strumento Rettangolo basato su modello) o "T" per creare più rettangoli di selezione delle stesse dimensioni. Se l'immagine non contiene rettangoli di selezione e si attivano i rettangoli basati su modello, lo strumento genererà rettangoli da 50x50 pixel. Se è stato creato un rettangolo di selezione e quindi si attivano i rettangoli basati su modello, i nuovi rettangoli di selezione avranno le stesse dimensioni dell'ultimo rettangolo creato. È possibile ridimensionare i rettangoli basati su modello dopo il posizionamento. Il ridimensionamento di un rettangolo basato su modello ridimensiona solo il rettangolo specifico. 

Per eliminare _tutti_ i rettangoli di selezione nell'immagine corrente, è possibile scegliere lo strumento **Delete All Regions** (Elimina tutte le aree) ![Delete regions tool](media/how-to-label-images/delete-regions-tool.png) (Strumento Elimina aree). 

Una volta creati i rettangoli di selezione per l'immagine, fare clic su **Invia** per salvare il lavoro. Il lavoro in corso non verrà salvato se non si fa clic sul pulsante **Invia**. 

## <a name="finishing-up"></a>Completamento dell'operazione 

Quando si invia una pagina di dati con tag, Azure assegna nuovi dati non etichettati da una coda di lavoro. Se non sono più presenti dati non etichettati, verrà visualizzato un messaggio informativo con un collegamento alla pagina iniziale del portale. 

Se si è certi di non svolgere altre attività di etichettatura, selezionare il proprio nome nell'angolo superiore destro del portale di etichettatura e scegliere **Disconnetti**. Se non si esegue la disconnessione, la sessione di Azure dell'utente raggiungerà il "timeout" e i dati verranno assegnati a un altro etichettatore. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [eseguire il training di modelli di classificazione delle immagini in Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
* Informazioni sul [rilevamento di oggetti con Azure e la tecnica Faster R-CNN](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)