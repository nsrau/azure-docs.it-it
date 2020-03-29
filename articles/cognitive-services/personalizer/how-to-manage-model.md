---
title: Gestire le impostazioni di modello e apprendimento - Personalizer
description: Il modello appreso dalla macchina e le impostazioni di apprendimento possono essere esportati per il backup nel proprio sistema di controllo del codice sorgente.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c544d058019c8d507f65dc6bfd854376b364abd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624297"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Come gestire le impostazioni di modello e apprendimento

Il modello appreso dalla macchina e le impostazioni di apprendimento possono essere esportati per il backup nel proprio sistema di controllo del codice sorgente.

## <a name="export-the-personalizer-model"></a>Esportare il modello di Personalizza esperienze

Nella sezione Gestione risorse per **Impostazioni modello e apprendimento,** esaminare la data di creazione del modello e l'ultimo aggiornamento ed esportare il modello corrente. È possibile usare il portale di Azure o le API di Personalizza esperienze per esportare un file di modello a scopo di archiviazione.

![Esportare il modello corrente di Personalizza esperienze](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Cancella i dati per il tuo ciclo di apprendimento

1. Nel portale di Azure, per la risorsa Personalizer, nella pagina **Impostazioni modello e apprendimento** selezionare Cancella **dati**.
1. Per cancellare tutti i dati e ripristinare lo stato originale del ciclo di apprendimento, selezionare tutte e 3 le caselle di controllo.

    ![Nel portale di Azure cancellare i dati dalla risorsa Personalizer.In Azure portal, clear data from Personalizer resource.](./media/settings/clear-data-from-personalizer-resource.png)

    |valore|Scopo|
    |--|--|
    |Dati di personalizzazione e ricompensa registrati.|Questi dati di registrazione vengono utilizzati nelle valutazioni offline. Cancellare i dati se si sta reimpostando la risorsa.|
    |Reimpostare il modello Personalizer.|Questo modello cambia ad ogni riqualificazione. Questa frequenza di training è specificata nella **frequenza** del modello di caricamento nella pagina **Configurazione.This** frequency of training is specified in upload model frequency on the Configuration page. |
    |Impostare i criteri di apprendimento su default.|Se i criteri di apprendimento sono stati modificati come parte di una valutazione offline, il criterio di apprendimento originale viene reimpostato sul criterio di apprendimento originale.|

1. Selezionare **Cancella i dati selezionati** per avviare il processo di compensazione. Lo stato viene segnalato nelle notifiche di Azure nella barra di spostamento in alto a destra.

## <a name="import-a-new-learning-policy"></a>Importare una nuova politica di apprendimento

Le impostazioni [dei criteri](concept-active-learning.md#understand-learning-policy-settings) di apprendimento determinano gli _iperparametri_ del training del modello. Eseguire una [valutazione offline](how-to-offline-evaluation.md) per trovare un nuovo criterio di apprendimento.

1. Aprire il portale di Azure e selezionare la risorsa Personalizer.Open the [Azure portal](https://portal.azure.com), and select your Personalizer resource.
1. Selezionare **Impostazioni modello e apprendimento** nella sezione Gestione **risorse.**
1. Per le **impostazioni di apprendimento Import** selezionare il file creato con il formato JSON specificato in precedenza, quindi selezionare il pulsante **Carica.**

    Attendere la notifica che il criterio di apprendimento è stato caricato correttamente.

## <a name="export-a-learning-policy"></a>Esportare una politica di apprendimento

1. Aprire il portale di Azure e selezionare la risorsa Personalizer.Open the [Azure portal](https://portal.azure.com), and select your Personalizer resource.
1. Selezionare **Impostazioni modello e apprendimento** nella sezione Gestione **risorse.**
1. Per le **impostazioni di apprendimento Importa,** selezionare il pulsante **Esporta impostazioni di apprendimento.** In questo `json` modo il file viene salvato nel computer locale.

## <a name="next-steps"></a>Passaggi successivi

[Scopri come gestire una politica di apprendimento](how-to-manage-model.md)
