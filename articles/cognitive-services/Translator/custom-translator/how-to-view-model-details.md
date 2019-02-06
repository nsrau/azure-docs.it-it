---
title: Visualizzare i dettagli del modello - Custom Translator
titleSuffix: Azure Cognitive Services
description: La scheda Models (Modelli) in qualsiasi progetto illustra i dettagli di ogni modello, ad esempio nome del modello, stato del modello, punteggio BLEU, training, ottimizzazione, conteggio frase di test.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: d82d41a254164c635f4488db63766b39d922f002
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219112"
---
# <a name="view-model-details"></a>Visualizzare i dettagli del modello

La scheda Models (Modelli) nel progetto illustra tutti i modelli del progetto. Tutti i modelli sottoposti a training per tale progetto sono elencati in questa scheda.

Per ogni modello nel progetto, vengono visualizzati i dettagli del modello seguenti.

1.  Nome del modello: indica il nome di un modello specificato.

2.  Stato: indica lo stato di un modello specificato. I nuovi elementi sottoposti a training avranno lo stato Submitted (Inviato) fino a quando non vengono accettati. Lo stato verrà modificato in Data processing (Elaborazione dati) durante la valutazione del contenuto dei documenti da parte del servizio. Al termine della valutazione dei documenti lo stato verrà modificato in Running (In esecuzione) e sarà possibile visualizzare il numero di frasi che fanno parte del training, tra cui i set di ottimizzazione e test creati automaticamente. Di seguito è riportato un elenco, con relativa descrizione, degli stati del modello.

    -  Submitted (Inviato): specifica che il back-end sta elaborando i documenti per il modello.

    -  TrainingQueued (Training accodato): specifica che il training viene accodato al sistema di traduzione automatica per il modello.

    -  Running (In esecuzione): specifica che il training è in esecuzione nel sistema di traduzione automatica per il modello.

    -  Succeeded (Esito positivo): specifica che il training ha avuto esito positivo nel sistema di traduzione automatica e un modello è disponibile. In questo stato, viene visualizzato un punteggio BLEU per il modello.

    -  Deployed (Distribuito): specifica che il modello con training positivo viene inviato al sistema di traduzione automatica per la distribuzione.

    -  Undeploying (Annullamento distribuzione): specifica che viene annullata la distribuzione del modello distribuito.

    -  Undeployed (Distribuzione annullata): specifica che il processo di annullamento della distribuzione di un modello è stato completato correttamente.

    -  Training Failed (Training non riuscito): specifica che il training ha avuto esito negativo. Se si verifica un errore di training, ripetere il processo di training. Se l'errore persiste, contattare Microsoft. Non eliminare il modello non riuscito.

    - DataProcessingFailed (Elaborazione dati non riuscita): specifica che l'elaborazione dei dati ha avuto esito negativo per uno o più documenti appartenenti al modello.

    - DeploymentFailed (Distribuzione non riuscita): specifica che la distribuzione del modello ha avuto esito negativo.

    - MigratedDraft (Migrazione bozza completata): specifica che il modello è in stato di bozza dopo la migrazione da hub a traduttore personalizzato.

4.  BLEU Score (Punteggio BLEU): visualizza il punteggio BLEU (Bilingual Evaluation Understudy) del modello, indicante la qualità del sistema di traduzione. Questo punteggio indica quanto le traduzioni effettuate dal sistema di traduzione risultante da questo training corrispondono alle frasi di riferimento nel set di dati di test. Il punteggio BLEU viene visualizzato se il training è stato completato correttamente. Se il training non è stato completo o ha avuto esito negativo, non verrà visualizzato alcun punteggio BLEU.

5.  Training Sentence count (Conteggio frasi di training): indica il numero totale delle frasi usate come set di training.

6.  Tuning Sentence count (Conteggio frasi di ottimizzazione): indica il numero totale delle frasi usate come set di ottimizzazione.

7.  Training Sentence count (Conteggio frasi di training): indica il numero totale delle frasi usate come set di testing.

8.  Mono Sentence count (Conteggio frasi mono): indica il numero totale delle frasi usate come set mono.

9.  Pulsante di azione Deploy (Distribuisci): per un modello correttamente sottoposto a training, è disponibile il pulsante "Deploy" (Distribuisci) se non è ancora stato distribuito. Se un modello è stato distribuito, viene visualizzato un pulsante Undeploy (Annulla distribuzione).

10. Delete (Elimina): se si vuole eliminare il modello, è possibile usare questo pulsante. L'eliminazione di un modello non elimina alcun documento usato per la creazione di tale modello.

    ![Visualizzare i dettagli del modello](media/how-to/how-to-view-model-details.png)

>[!Note]
>Per confrontare training consecutivi per gli stessi sistemi, è importante mantenere costanti il set di ottimizzazione e il set di test.

## <a name="view-model-training-details"></a>Visualizzare i dettagli del training del modello

Dopo aver completato il training, è possibile esaminare i dettagli relativi al training nella pagina dei dettagli. Selezionare un progetto, individuare e selezionare la scheda Models (Modelli) e scegliere un modello.

La pagina del modello dispone di due schede: Training details (Dettagli training) e Test.

1.  **Training Details (Dettagli training):** questa scheda mostra l'elenco dei documenti usati nel training:

    -  Nome del documento: questo campo indica il nome del documento

    -  Tipo di documento: questo campo indica se il documento è parallelo o mono.

    -  Conteggio frasi nella lingua di origine: questo campo indica il numero delle frasi presenti nella lingua di origine.

    -  Conteggio frasi nella lingua di destinazione: questo campo indica il numero delle frasi presenti nella lingua di destinazione.

    -  Aligned Sentences (Frasi allineate): questo campo indica il numero di frasi allineate dal traduttore personalizzato durante il processo di allineamento.

    -  Used Sentences (Frasi usate): questo campo indica il numero di frasi usate dal traduttore personalizzato durante il training.

    ![Dettagli del training del modello](media/how-to/how-to-model-training-details.png)

2.  **Test:** questa scheda mostra i dettagli del test per un training con esito positivo.

## <a name="next-steps"></a>Passaggi successivi

- Vedere i [risultati del test](how-to-view-system-test-results.md) e analizzare i risultati del training.
