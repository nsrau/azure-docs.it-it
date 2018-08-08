---
title: Criteri di errore di output in Analisi di flusso di Azure
description: Informazioni sui criteri di gestione degli errori di output disponibili in Analisi di flusso di Azure.
services: stream-analytics
author: sidram
ms.author: sidram
manager: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: f854e8ce35ac9d0a99b4a7dc1cdc66724114a5c4
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392568"
---
# <a name="output-error-policy"></a>Criteri di gestione degli errori di output
Questo articolo descrive i criteri di gestione degli errori per i dati di output che possono essere configurati in Analisi di flusso di Azure.

I criteri di gestione degli errori per i dati di output si applicano solo agli errori di conversione dei dati che si verificano quando l'evento di output prodotto da un processo di Analisi di flusso di Azure non è conforme allo schema del sink di destinazione. È possibile configurare questo criterio scegliendo tra **Riprova** oppure **Rilascia**. Nel portale di Azure, all'interno di un processo di Analisi di flusso, sotto **Configura**, selezionare **Criteri di errore** per effettuare una selezione.

![Criteri di errore - percorso](./media/stream-analytics-error-policy/stream-analytics-error-policy-locate.PNG)


## <a name="retry"></a>Retry
Quando si verifica un errore, Analisi di flusso di Azure prova a scrivere l'evento a tempo indeterminato fino a quando la scrittura non ha esito positivo. Non è impostato alcun timeout per i tentativi. Alla fine, l'elaborazione di tutti gli eventi successivi viene bloccata dall'evento in fase di esecuzione. Questa opzione è il criterio di gestione degli errori di output predefinito.

## <a name="drop"></a>Rilascia
Analisi di flusso di Azure rilascerà qualsiasi evento di output che restituisca un errore di conversione dei dati. Gli eventi rilasciati non possono essere recuperati per essere rielaborati in seguito.


Tutti gli errori temporanei (ad esempio, gli errori di rete) vengono ritentati indipendentemente dalla configurazione dei criteri di gestione degli errori di output.


## <a name="next-steps"></a>Passaggi successivi
[Guida per la risoluzione dei problemi di Analisi di flusso di Azure](stream-analytics-troubleshooting-guide.md)
