---
title: Criteri di errore di output in Analisi di flusso di Azure
description: Informazioni sui criteri di gestione degli errori di output disponibili in Analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75431616"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Criteri di errore di output in Analisi di flusso di Azure
Questo articolo descrive i criteri di gestione degli errori per i dati di output che possono essere configurati in Analisi di flusso di Azure.

I criteri di gestione degli errori per i dati di output si applicano solo agli errori di conversione dei dati che si verificano quando l'evento di output prodotto da un processo di Analisi di flusso di Azure non è conforme allo schema del sink di destinazione. È possibile configurare questo criterio scegliendo tra **Riprova** oppure **Rilascia**. Nel portale di Azure, all'interno di un processo di Analisi di flusso, sotto **Configura**, selezionare **Criteri di errore** per effettuare una selezione.

![Posizione dei criteri di errore di output in Analisi di flusso di Azure](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Riprova
Quando si verifica un errore, Analisi di flusso di Azure prova a scrivere l'evento a tempo indeterminato fino a quando la scrittura non ha esito positivo. Non è impostato alcun timeout per i tentativi. Alla fine, l'elaborazione di tutti gli eventi successivi viene bloccata dall'evento in fase di esecuzione. Questa opzione è il criterio di gestione degli errori di output predefinito.

## <a name="drop"></a>Rilascia
Analisi di flusso di Azure rilascerà qualsiasi evento di output che restituisca un errore di conversione dei dati. Gli eventi rilasciati non possono essere recuperati per essere rielaborati in seguito.


Tutti gli errori temporanei (ad esempio, gli errori di rete) vengono ritentati indipendentemente dalla configurazione dei criteri di gestione degli errori di output.


## <a name="next-steps"></a>Passaggi successivi
[Guida per la risoluzione dei problemi di Analisi di flusso di Azure](stream-analytics-troubleshooting-guide.md)
