---
title: Gestire le impostazioni di modello e apprendimento-personalizzatore
description: Il modello di apprendimento automatico e le impostazioni di apprendimento possono essere esportate per il backup nel sistema di controllo del codice sorgente.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c544d058019c8d507f65dc6bfd854376b364abd1
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624297"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Come gestire le impostazioni di modellazione e apprendimento

Il modello di apprendimento automatico e le impostazioni di apprendimento possono essere esportate per il backup nel sistema di controllo del codice sorgente.

## <a name="export-the-personalizer-model"></a>Esportare il modello di Personalizza esperienze

Dalla sezione relativa alla gestione delle risorse per **le impostazioni relative a modelli e apprendimento**esaminare la creazione del modello e la data dell'ultimo aggiornamento ed esportare il modello corrente. È possibile usare il portale di Azure o le API di Personalizza esperienze per esportare un file di modello a scopo di archiviazione.

![Esportare il modello corrente di Personalizza esperienze](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Cancellare i dati per il ciclo di apprendimento

1. Nel portale di Azure, per la risorsa di personalizzazione, nella pagina **Impostazioni modello e apprendimento** selezionare **Cancella dati**.
1. Per cancellare tutti i dati e reimpostare il ciclo di apprendimento sullo stato originale, selezionare tutte e tre le caselle di controllo.

    ![In portale di Azure cancellare i dati dalla risorsa di personalizzazione.](./media/settings/clear-data-from-personalizer-resource.png)

    |valore|Scopo|
    |--|--|
    |Personalizzazione e dati di ricompensa registrati.|I dati di registrazione vengono usati nelle valutazioni offline. Cancellare i dati se si sta reimpostando la risorsa.|
    |Reimpostare il modello di personalizzazione.|Questo modello viene modificato a ogni ripetizione del training. Questa frequenza di training è specificata in **caricare la frequenza del modello** nella pagina di **configurazione** . |
    |Impostare i criteri di apprendimento su predefinito.|Se sono stati modificati i criteri di apprendimento come parte di una valutazione offline, questo Reimposta i criteri di apprendimento originali.|

1. Selezionare **Cancella dati selezionati** per avviare il processo di cancellazione. Lo stato viene segnalato nelle notifiche di Azure, nella barra di spostamento in alto a destra.

## <a name="import-a-new-learning-policy"></a>Importa un nuovo criterio di apprendimento

Le impostazioni dei [criteri di apprendimento](concept-active-learning.md#understand-learning-policy-settings) determinano gli _iperparametri_ del training del modello. Eseguire una [valutazione offline](how-to-offline-evaluation.md) per trovare un nuovo criterio di formazione.

1. Aprire il [portale di Azure](https://portal.azure.com)e selezionare la risorsa di personalizzazione.
1. Selezionare **modello e impostazioni di apprendimento** nella sezione **Gestione risorse** .
1. Per le **impostazioni di importazione di apprendimento** selezionare il file creato con il formato JSON specificato in precedenza, quindi selezionare il pulsante **carica** .

    Attendere la notifica che il criterio di apprendimento è stato caricato correttamente.

## <a name="export-a-learning-policy"></a>Esportare un criterio di formazione

1. Aprire il [portale di Azure](https://portal.azure.com)e selezionare la risorsa di personalizzazione.
1. Selezionare **modello e impostazioni di apprendimento** nella sezione **Gestione risorse** .
1. Per **Import Learning Settings** selezionare il pulsante **Export Learning Settings** . In questo modo il file di `json` viene salvato nel computer locale.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come gestire un criterio di formazione](how-to-manage-model.md)
