---
title: Come caricare un documento - Custom Translator
titleSuffix: Azure Cognitive Services
description: Usando la funzionalità di caricamento dei documenti è possibile caricare documenti paralleli per il training. I documenti paralleli sono coppie di documenti in cui uno è la traduzione dell'altro. Un documento della coppia contiene frasi nella lingua di origine, mentre l'altro contiene le stesse frasi tradotte nella lingua di destinazione.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f2bd6103c27d455265ee967554fb27513f78a472
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595638"
---
# <a name="upload-a-document"></a>Caricare un documento

In [Custom Translator](https://portal.customtranslator.azure.ai) è possibile caricare documenti paralleli per eseguire il training dei modelli di traduzione. I [documenti paralleli](what-are-parallel-documents.md) sono coppie di documenti in cui uno è una traduzione dell'altro. Un documento della coppia contiene frasi nella lingua di origine, mentre l'altro contiene le stesse frasi tradotte nella lingua di destinazione.

Prima di caricare i documenti, consultare la [guida ai formati dei documenti e alle convenzioni di denominazione](document-formats-naming-convention.md) per assicurarsi che il formato del file sia supportato da Custom Translator.

## <a name="how-to-upload-document"></a>Come caricare i documenti?

Dal portale di [Custom Translator](https://portal.customtranslator.azure.ai) fare clic sulla scheda "Documents" (Documenti) per passare alla pagina dei documenti.

![Collegamento per il caricamento del documento](media/how-to/how-to-upload-1.png)


1.  Fare clic sul pulsante Upload files (Carica file) nella pagina dei documenti.

    ![Pagina di caricamento del documento](media/how-to/how-to-upload-2.png)

2.  Nella finestra di dialogo specificare le informazioni seguenti:

    a.  Document type (Tipo di documento):

    -  Training: questi documenti verranno usati per il set di training.
    -  Tuning (Ottimizzazione): questi documenti verranno usati per il set di ottimizzazione.
    -  Testing: questi documenti verranno usati per il set di test.
    -  Phrase Dictionary (Dizionario di espressioni): questi documenti verranno usati per il dizionario di espressioni.
    -  Sentence Dictionary (Dizionario di frasi): questi documenti verranno usati per il dizionario di frasi.

    b.  Language pair (Coppia di lingue)

    c.  Override document if exists (Sostituisci il documento se esistente): selezionare questa casella di controllo se si vogliono sovrascrivere gli eventuali documenti esistenti con lo stesso nome.

    d.  Compilare la sezione pertinente per i dati paralleli o i dati combinati.

    -  Parallel data (Dati paralleli):
        -  Source file (File di origine): selezionare il file nella lingua di origine nel computer locale.
        -  Target file (File di destinazione): selezionare il file nella lingua di destinazione nel computer locale.
        -  Document name (Nome documento): usato solo per il caricamento di file paralleli.

    - Combo data (Dati combinati):
        -  Combo File (File combinato): selezionare il file combinato nel computer locale. Il file combinato include sia le frasi nella lingua di origine che quelle nella lingua di destinazione. La [convenzione di denominazione](document-formats-naming-convention.md) è importante per i file combinati.

    e.  Fare clic su Upload (Carica).

    ![Finestra di dialogo di caricamento del documento](media/how-to/how-to-upload-dialog.png)

3.  A questo punto, sono in corso l'elaborazione dei documenti e il tentativo di estrarre le frasi. È possibile fare clic su "View upload Progress" (Visualizza stato di caricamento) per controllare lo stato dei documenti durante l'elaborazione.

    ![Finestra di dialogo di elaborazione del caricamento dei documenti](media/how-to/how-to-upload-processing-dialog.png)

4.  In questa pagina verranno visualizzati lo stato e gli eventuali errori per ogni file nel caricamento. È possibile visualizzare lo stato dei caricamenti precedenti in qualsiasi momento facendo clic sulla scheda "Upload history" (Carica cronologia).

    ![Finestra di dialogo della cronologia dei caricamenti dei documenti](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Visualizzare la cronologia dei caricamenti

Nella pagina della cronologia dei caricamenti è possibile visualizzare la cronologia di tutti i caricamenti di documenti con dettagli come il tipo di documento, la coppia di lingue, lo stato di caricamento e così via.

1. Dal portale di [Custom Translator](https://portal.customtranslator.azure.ai) fare clic sulla scheda Upload History (Cronologia caricamenti) per visualizzare la cronologia.

    ![Scheda Upload History (Cronologia caricamenti)](media/how-to/how-to-upload-history-1.png)

2. Questa pagina mostra lo stato di tutti i caricamenti precedenti. I caricamenti sono visualizzati in ordine dal più recente al meno recente. Per ogni caricamento sono indicati il nome del documento, lo stato di caricamento, la data di caricamento, il numero di file caricati, il tipo di file caricato e la coppia di lingue del file.

    ![Pagina della cronologia dei caricamenti](media/how-to/how-to-document-history-2.png)

3. Fare clic su uno dei record nella cronologia dei caricamenti. Nella pagina dei dettagli della cronologia dei caricamenti è possibile visualizzare i file caricati come parte del processo di caricamento, lo stato di caricamento del file, la lingua del file ed eventuali messaggi di errore in caso di errori durante il caricamento.

## <a name="next-steps"></a>Passaggi successivi

- Usare la [pagina dei dettagli dei documenti](how-to-view-document-details.md) per esaminare l'elenco delle frasi estratte.
- [Come eseguire il training di un modello](how-to-train-model.md).
