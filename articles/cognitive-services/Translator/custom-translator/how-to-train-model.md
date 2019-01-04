---
title: Eseguire il training di un modello - Custom Translator
titleSuffix: Azure Cognitive Services
description: Il training di un modello è un passaggio importante quando si compila un modello di traduzione. Il training viene eseguito in base ai documenti selezionati per questa operazione.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 60e0485c28d90050a6ff775db41f8696a09fe033
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165759"
---
# <a name="train-a-model"></a>Eseguire il training di un modello

Il training di un modello è il passaggio fondamentale per la compilazione di un modello di traduzione in quanto senza di esso il modello non può essere compilato. Il training viene eseguiti in base ai documenti selezionati per questa operazione.

Per eseguire il training di un modello:

1.  Selezionare il progetto in cui si desidera compilare un modello.

2.  Nella scheda Data (Dati) per il progetto vengono visualizzati tutti i documenti rilevanti per la coppia di lingue del progetto. Selezionare manualmente i documenti in cui che si desidera usare per il training del modello. È possibile selezionare i documenti per il training, l'ottimizzazione e il test in questa schermata. È anche possibile selezionare solo il set di training e consentire a Custom Translator di creare automaticamente i set di ottimizzazione e test.

    -  Document name (Nome documento): nome del documento.

    -  Pairing (Associazione): indica se il documento è monolingua o parallelo. I documenti monolingua non sono attualmente supportati per il training.

    -  Document type (Tipo di documento): può essere destinato al training, all'ottimizzazione o al test oppure può essere un dizionario.

    -  Language pair (Coppia di lingue): mostra la lingua di origine e quella di destinazione per il progetto.

    -  Source sentences (Frasi di origine): mostra il numero di frasi estratte dal file di origine.

    -  Target sentences (Frasi di destinazione): mostra il numero di frasi estratte dal file di destinazione.

    ![Eseguire il training del modello](media/how-to/how-to-train-model.png)

3.  Fare clic sul pulsante Train (Esegui il training).

4.  Nella finestra di dialogo, specificare un nome per il modello.

5.  Fare clic su Train model (Esegui il training del modello).

    ![Finestra di dialogo Train model (Esegui il training del modello)](media/how-to/how-to-train-model-2.png)

6.  Custom Translator avvierà il training e visualizzerà lo stato dell'operazione nella scheda Models (Modelli).

    ![Pagina Train model (Esegui il training del modello)](media/how-to/how-to-train-model-3.png)


## <a name="edit-a-model"></a>Modificare un modello

È possibile modificare un modello usando il collegamento Edit (Modifica) nella pagina di modifica nella pagina Model Details (Dettagli modello).

1.  Fare clic sull'icona della matita.

    ![Modificare il modello](media/how-to/how-to-edit-model.png)

2.  Nella finestra di dialogo modificare i valori seguenti:

    1.  Model Name (Nome modello) (obbligatorio): assegnare un nome significativo al modello.

        ![Finestra di dialogo Edit Model (Modifica modello)](media/how-to/how-to-edit-model-dialog.png)

3.  Fare clic su Save (Salva).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [come visualizzare i dettagli del modello](how-to-view-model-details.md).
