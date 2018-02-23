---
title: Creazione guidata dell'origine dati di Azure per Azure Machine Learning | Microsoft Docs
description: Illustra la creazione guidata dell'origine dati di Azure Machine Learning Workbench
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: ff0159facd693b83230c731eb7e76f0a9495fdf2
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="data-source-wizard"></a>Creazione guidata dell'origine dati #

La creazione guidata dell'origine dati è un modo semplice e rapido per portare un set di dati in Azure Machine Learning Workbench senza codice. Consente anche di scegliere una strategia di esempio per il set di dati. 

## <a name="step-1-trigger-the-data-source-wizard"></a>Passaggio 1: Avviare la creazione guidata dell'origine dati ## 

Per spostare i dati in un progetto usando la creazione guidata dell'origine dati. Selezionare il pulsante **+** accanto alla casella di ricerca nella vista dati e scegliere Aggiungi origine dati. 

![aggiungere l'origine dati](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>Passaggio 2: Selezionare il percorso in cui archiviare i dati ##
Innanzitutto, specificare la provenienza dei dati. Possono essere archiviati in un file flat o in una directory, in file parquet, di Excel o in un database. Per altre informazioni, vedere [Archivi dati supportati](data-prep-appendix2-supported-data-sources.md).

![passaggio 1](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>Passaggio 3: Selezionare il file di dati ##
Per un file o una directory, specificare il percorso del file. Scegliere nell'elenco a discesa la posizione dei dati: si può scegliere un percorso file locale o Archiviazione BLOB di Azure. 

Specificare il percorso digitandolo oppure facendo clic sul pulsante **Sfoglia...** per cercarlo. È possibile cercare una directory oppure uno o più file.

Fare clic su **Fine** per accettare le impostazioni predefinite per gli altri passaggi o su Avanti per procedere al passaggio successivo.


![passaggio 4](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>Passaggio 4: Scegliere i parametri del file ##

La creazione guidata dell'origine dati può rilevare automaticamente il tipo di file tipo, i separatori e la codifica. Inoltre permettere di visualizzare un esempio di come apparirebbero i dati. È anche possibile cambiare questi parametri manualmente. 

![passaggio 5](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>Passaggio 5: Impostare i tipi di dati per le colonne ##

La creazione guidata dell'origine dati rileva automaticamente i tipi di dati delle colonne del set di dati. Se ne manca uno o si desidera applicare un tipo di dati, è possibile modificare manualmente il tipo di dati. La colonna **SAMPLE OUTPUT DATA** (DATI DI OUTPUT DI ESEMPIO) mostra gli esempi di come apparirebbero i dati.

![passaggio 6](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>Passaggio 6: Scegliere la strategia di campionamento per i dati ##

È possibile specificare una o più strategie di campionamento per il set di dati e sceglierne una come strategia attiva. Il valore predefinito prevede il caricamento delle prime 10.000 righe. È possibile modificare questo esempio facendo clic sul pulsante **Modifica** nella barra degli strumenti oppure aggiungere una nuova strategia facendo clic su +Nuovo. Le strategie che sono attualmente supportate sono

-     Numero superiore di righe
-     Numero casuale di righe
-     Percentuale casuale di righe
-     File completo

È possibile specificare tutte le strategie di campionamento desiderato, ma è possibile impostarne una sola come attiva durante la preparazione dei dati. È possibile impostare qualsiasi strategia come attiva selezionando la strategia e facendo clic su Imposta come attivo nella barra degli strumenti.

A seconda della provenienza dei dati, alcune strategie di esempio potrebbero non essere supportate. Per altre informazioni sul campionamento, consultare la sezione sul campionamento in [questo documento](data-prep-user-guide.md) 

![passaggio 6](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>Passaggio 7: Gestione delle colonne relative al percorso ##

Se il percorso del file include dati importanti, è possibile scegliere di includerlo come prima colonna del set di dati. Questo è utile se si importano più file. In caso contrario, è possibile scegliere di non includerlo.

![passaggio 7](media/data-source-wizard/step6.png)

Dopo aver fatto clic su Fine, verrà aggiunta al progetto una nuova origine dati. È possibile trovarla nel gruppo Origini dati in Visualizzazione Dati o come file .dsource in **Visualizzazione file**.
