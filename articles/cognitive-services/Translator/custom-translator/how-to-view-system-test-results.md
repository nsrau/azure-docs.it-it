---
title: Visualizzare i risultati dei test di sistema e distribuzione - Custom Translator
titleSuffix: Azure Cognitive Services
description: Al termine del training, esaminare i test di sistema per analizzare i risultati del training. Se si è soddisfatti dei risultati del training, inviare una richiesta di distribuzione per il modello sottoposto a training.
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: ec15851ae7ff59a752fbf0d823d87aa6e68f10e9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442488"
---
# <a name="view-system-test-results"></a>Visualizzare i risultati dei test di sistema

Al termine del training, esaminare i test di sistema per analizzare i risultati del training. Se si è soddisfatti dei risultati del training, inviare una richiesta di distribuzione per il modello sottoposto a training.

## <a name="system-test-results-page"></a>Pagina dei risultati dei test di sistema

Selezionare un progetto, quindi selezionare la scheda Models (Modelli) di quel progetto, individuare il modello che si vuole usare e infine selezionare la scheda Test.

La scheda Test mostra quanto segue:

1.  **Risultati Test di sistema:** Il risultato del processo di test nei corsi di formazione. Il processo di test produce il punteggio BLEU.

    **Sentence Count** (Numero di frasi): il numero di frasi parallele utilizzate nel set di test.

     **BLEU Score** (Punteggio BLEU): il punteggio generato per un modello dopo il completamento del training.

    **Stato:** indica se il processo di test è stato completato o è in corso.

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

1.  **custom.mt.txt:** questo file contiene le traduzioni automatiche delle frasi nella lingua di origine nella lingua di destinazione eseguita dal modello sottoposto a training con i dati dell'utente.

2.  **ref.txt:** questo file contiene le traduzioni fornite dall'utente delle frasi nella lingua di origine nella lingua di destinazione.

3.  **source.txt:** questo file contiene le frasi nella lingua di origine.

    ![Risultati dei test di sistema scaricati](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Distribuire un modello

Per richiedere una distribuzione:

1.  Selezionare un progetto e passare alla scheda Models (Modelli).

2. Per un modello correttamente sottoposto a training è disponibile il pulsante "Deploy" (Distribuisci), se non è ancora stato distribuito.

    ![Distribuzione del modello](media/how-to/how-to-deploy-model.png)

3.  Fare clic su Deploy (Distribuisci).
4.  Selezionare **Deployed** (Distribuito) per le aree in cui si vuole distribuire il modello e fare clic su Salva. È possibile selezionare **Deployed** (Distribuito) per più aree.

    ![Distribuzione del modello](media/how-to/how-to-deploy-model-regions.png)

5.  È possibile visualizzare lo stato del modello nella colonna "Status" (Stato).

>[!Note]
>Microsoft Translator personalizzato supporta 10 modelli distribuiti all'interno di un'area di lavoro in qualsiasi punto nel tempo.

## <a name="update-deployment-settings"></a>Aggiornare le impostazioni di distribuzione

Per aggiornare le impostazioni di distribuzione:

1.  Selezionare un progetto e passare alla scheda **Models** (Modelli).

2. Per un modello distribuito correttamente, è mostrato il pulsante **Update** (Aggiorna).

    ![Distribuzione del modello](media/how-to/how-to-update-undeploy-model.png)

3.  Selezionare **Aggiorna**.
4.  Selezionare **Deployed** (Distribuito) o **Undeployed** (Non distribuito) per le aree in cui si vuole che il modello sia distribuito o non distribuito e fare clic su **Salva**.

    ![Distribuzione del modello](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Se si seleziona **Undeployed** (Non distribuito) per tutte le aree, la distribuzione del modello viene annullata in tutte le aree e il modello viene messo nello stato non distribuito. Ora è disponibile per l'utilizzo.

## <a name="next-steps"></a>Passaggi successivi

- Iniziare a usare il modello di traduzione personalizzata distribuito tramite l'[API Traduzione testuale Microsoft V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Scoprire [come gestire le impostazioni](how-to-manage-settings.md) per condividere l'area di lavoro e gestire la chiave di sottoscrizione.
- Scoprire [come eseguire la migrazione dell'area di lavoro e del progetto](how-to-migrate.md) da [Hub di Microsoft Translator](https://hub.microsofttranslator.com)
