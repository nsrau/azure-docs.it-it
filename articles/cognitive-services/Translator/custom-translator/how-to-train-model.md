---
title: Eseguire il training di un modello - Custom Translator
titleSuffix: Azure Cognitive Services
description: Il training di un modello è un passaggio importante quando si compila un modello di traduzione. Il training viene eseguito in base ai documenti selezionati per questa operazione.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 824516a327d45feb5b6a084a113633bd3a486abe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88508362"
---
# <a name="train-a-model"></a>Eseguire il training di un modello

Il training di un modello è il primo e il passaggio più importante per la creazione di un modello di traduzione. in caso contrario, non è possibile compilare il modello. Il training viene eseguiti in base ai documenti selezionati per questa operazione. Quando si selezionano i documenti del tipo di documento "Training", tenere presente il requisito minimo 10.000 delle frasi parallele. Quando si selezionano i documenti, viene visualizzato il numero totale di frasi di training per la guida. Questo requisito non si applica quando si selezionano solo i documenti del tipo di documento del dizionario per eseguire il training di un modello.

Per eseguire il training di un modello:

1. Selezionare il progetto in cui si desidera compilare un modello.

2. Nella scheda Data (Dati) per il progetto vengono visualizzati tutti i documenti rilevanti per la coppia di lingue del progetto. Selezionare manualmente i documenti in cui che si desidera usare per il training del modello. È possibile selezionare i documenti per il training, l'ottimizzazione e il test in questa schermata. È anche possibile selezionare solo il set di training e consentire a Custom Translator di creare automaticamente i set di ottimizzazione e test.

    - Document name (Nome documento): nome del documento.

    - Pairing (Associazione): indica se il documento è un documento monolingua o parallelo. I documenti monolingua non sono attualmente supportati per il training.

    - Document type (Tipo di documento): il documento può essere destinato al training, all'ottimizzazione, al test o essere un dizionario.

    - Language pair (Coppia di lingue): vengono visualizzate la lingua di origine e la lingua di destinazione per il progetto.

    - Frasi di origine: Mostra il numero di frasi estratte dal file di origine.

    - Frasi di destinazione: Mostra il numero di frasi estratte dal file di destinazione.

    ![Eseguire il training del modello](media/how-to/how-to-train-model.png)

3. Fare clic sul pulsante "Crea modello".

4. Nella finestra di dialogo specificare il nome del modello. Per impostazione predefinita, viene selezionato "Training immediato" per avviare la pipeline di training quando si fa clic sul pulsante "Crea modello". È possibile selezionare "Salva come bozza" per creare i metadati del modello e inserire il modello in uno stato di bozza, ma il training del modello non verrà avviato. In un secondo momento, è necessario selezionare manualmente i modelli in stato bozza per eseguire il training.

5. Fare clic sul pulsante "Crea modello".

    ![Finestra di dialogo Train model (Esegui il training del modello)](media/how-to/how-to-train-model-2.png)

6. Il convertitore personalizzato invierà il training e visualizzerà lo stato della formazione nella scheda modelli.

    ![Pagina Train model (Esegui il training del modello)](media/how-to/how-to-train-model-3.png)

>[!Note]
>Il convertitore personalizzato supporta 10 Training simultanei in un'area di lavoro in qualsiasi momento.

## <a name="modify-a-model-name"></a>Modificare il nome di un modello

È possibile modificare il nome di un modello dalla pagina dei dettagli del modello.

1. Nella pagina progetti, fare clic sul nome del progetto in cui è presente il modello.
2. Fare clic sulla scheda modello.
3. Fare clic sul nome del modello per visualizzare i dettagli del modello.
4. Fare clic sull'icona della matita.

    ![Modificare il modello](media/how-to/how-to-edit-model.png)

5. Nella finestra di dialogo modificare il nome del modello e assegnare un nome significativo al modello.

    ![Finestra di dialogo Edit Model (Modifica modello)](media/how-to/how-to-edit-model-dialog.png)

6. Fare clic su Salva.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [come visualizzare i dettagli del modello](how-to-view-model-details.md).
