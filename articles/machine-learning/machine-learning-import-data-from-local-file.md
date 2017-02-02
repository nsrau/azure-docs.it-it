---
title: Importare dati in Machine Learning Studio da un file locale | Documentazione Microsoft
description: Come importare dati di training in Azure Machine Learning Studio da un file locale.
keywords: dati di importazione, formato dati, tipi di dati, origini dati, dati di training
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: c0dd9e90-23c4-4f64-8b8f-489ad79f047b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: garye;bradsev
translationtype: Human Translation
ms.sourcegitcommit: 844dafe79c71594212d966286c74aa7f916ba60e
ms.openlocfilehash: e17a3dd8b70f22ab0c68eecde50a7506167188b9


---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-a-local-file"></a>Importare dati di training in Azure Machine Learning Studio da un file locale
[!INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

Per usare i propri dati in Machine Learning Studio, è possibile caricare un file di dati in anticipo dal disco rigido locale per creare un modulo di set di dati nell'area di lavoro. 

## <a name="import-data-from-a-local-file"></a>Importare dati da un file locale
È possibile importare dati da un disco rigido locale effettuando le operazioni seguenti:

1. Fare clic su **+ NEW** nella parte inferiore della finestra di Machine Learning Studio.
2. Selezionare **DATASET** (SET DI DATI) e **FROM LOCAL FILE** (DA FILE LOCALE).
3. Nella finestra di dialogo **Caricare un nuovo set di dati** selezionare il file da caricare
4. Immettere un nome, identificare il tipo di dati e immettere facoltativamente una descrizione. Una descrizione è consigliabile perché consente di registrare tutte le caratteristiche relative ai dati da tenere presenti quando si useranno tali dati in futuro.
5. La casella di controllo **Questa è la nuova versione di un set di dati esistente** consente di aggiornare un set di dati esistente con nuovi dati. Fare clic su questa casella di controllo e quindi immettere il nome di un set di dati esistente.

Durante il caricamento, verrà visualizzato un messaggio che indica che il file è in fase di caricamento. Il tempo di caricamento dipende dalle dimensioni dei dati e dalla velocità della connessione al servizio.
Se è già noto che il file richiederà molto tempo, è possibile eseguire altre operazioni in Machine Learning Studio durante l'attesa. In caso di chiusura del browser, tuttavia, il caricamento dei dati avrà esito negativo.

Una volta caricati, i dati vengono archiviati in un modulo di set di dati e sono disponibili per eventuali esperimenti nell'area di lavoro.
Durante la modifica di un esperimento, è possibile individuare i set di dati creati nell'elenco **My Datasets** (Set di dati personali) presente nell'elenco **Saved Datasets** (Set di dati salvati) nella tavolozza dei moduli. È possibile trascinare il set di dati nell'area di disegno dell'esperimento per usarlo per l'ulteriore analisi e l'apprendimento automatico.




<!--HONumber=Dec16_HO3-->


