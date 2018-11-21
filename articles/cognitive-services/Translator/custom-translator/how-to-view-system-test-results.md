---
title: Visualizzare i risultati dei test di sistema e distribuzione - Custom Translator
titleSuffix: Azure Cognitive Services
description: Al termine del training, esaminare i test di sistema per analizzare i risultati del training. Se si è soddisfatti dei risultati del training, inviare una richiesta di distribuzione per il modello sottoposto a training.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 61d3869559d88e14c0b9a3c3e23cd8a1f9c2b6c4
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626991"
---
# <a name="view-system-test-results"></a>Visualizzare i risultati dei test di sistema

Al termine del training, esaminare i test di sistema per analizzare i risultati del training. Se si è soddisfatti dei risultati del training, inviare una richiesta di distribuzione per il modello sottoposto a training. 

## <a name="system-test-results-page"></a>Pagina dei risultati dei test di sistema

Selezionare un progetto, quindi selezionare la scheda Models (Modelli) di quel progetto, individuare il modello che si vuole usare e infine selezionare la scheda Test.

La scheda Test mostra quanto segue:

1.  System Test Results (Risultati test di sistema): risultato del processo di test nei training. Il processo di test produce il punteggio BLEU.

    **Sentence Count** (Numero di frasi): numero di frasi parallele usate nel set di test.

     **BLEU Score** (Punteggio BLEU): punteggio generato per un modello dopo il completamento del training.

    **Status** (Stato): indica se il processo di test è stato completato o è in corso.

    ![Risultati test di sistema](media/how-to/how-to-system-test-results.png)

2.  Fare clic su System Test Results (Risultati test di sistema). Si passerà alla pagina di dettagli dei risultati di test. Questa pagina mostra la traduzione automatica delle frasi che facevano parte del set di dati di test.

3.  La tabella nella pagina dei dettagli dei risultati di test include due colonne, una per ogni lingua nella coppia. La colonna per la lingua di origine mostra la frase da tradurre. La colonna per la lingua di destinazione contiene due frasi in ogni riga.

    **Ref** (Rif): questa frase è la traduzione di riferimento della frase di origine come specificato nel set di dati di test.

    **MT** (Traduzione automatica): questa frase è la traduzione automatica della frase di origine eseguita dal modello compilato dopo l'esecuzione del training.

    ![Confronto dei risultati dei test di sistema](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Scaricare il test

Fare clic sul collegamento Download Translations (Scarica traduzioni) per scaricare un file ZIP. Il file ZIP contiene la traduzione automatica delle frasi di origine nel set di dati di test.

![Scaricare il test](media/how-to/how-to-system-test-download.png)

Questo archivio ZIP scaricato contiene tre file.

1.  custom.mt.txt: questo file contiene le traduzioni automatiche delle frasi nella lingua di origine nella lingua di destinazione eseguita dal modello sottoposto a training con i dati dell'utente.

2.  ref.txt: questo file contiene le traduzioni fornite dall'utente delle frasi nella lingua di origine nella lingua di destinazione.

3.  source.txt: questo file contiene le frasi nella lingua di origine.

    ![Risultati dei test di sistema scaricati](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Distribuire un modello

Per richiedere una distribuzione:

1.  Selezionare un progetto e passare alla scheda Models (Modelli).

2. Per un modello correttamente sottoposto a training è disponibile il pulsante "Deploy" (Distribuisci), se non è ancora stato distribuito.

    ![Distribuzione del modello](media/how-to/how-to-deploy-model.png)

3.  Fare clic su Deploy (Distribuisci).
4.  Selezionare le aree in cui si vuole distribuire il modello e fare clic su Salva. È possibile selezionare più aree.

    ![Distribuzione del modello](media/how-to/how-to-deploy-model-regions.png)

5.  È possibile visualizzare lo stato del modello nella colonna "Status" (Stato).

>[!Note]
>Se un modello è già stato distribuito, per tale modello verrà visualizzato il pulsante "Undeploy" (Annulla distribuzione). Per annullare la distribuzione di un modello, fare clic su "Undeploy" (Annulla distribuzione).

## <a name="next-steps"></a>Passaggi successivi

- Iniziare a usare il modello di traduzione personalizzata distribuito tramite l'[API Traduzione testuale Microsoft V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Scoprire [come gestire le impostazioni](how-to-manage-settings.md) per condividere l'area di lavoro e gestire la chiave di sottoscrizione.
- Scoprire [come eseguire la migrazione dell'area di lavoro e del progetto](how-to-migrate.md) da [Hub di Microsoft Translator](https://hub.microsofttranslator.com)