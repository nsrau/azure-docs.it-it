---
title: Ottenere la ridondanza geografica per i processi di analisi di flusso di Azure
description: Questo articolo descrive come ottenere la ridondanza geografica dei processi di analisi di flusso di Azure anziché il failover geografico.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/26/2020
ms.openlocfilehash: 44b0394773485f8054eddc94c7d7e9b91baebc6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88951093"
---
# <a name="achieve-geo-redundancy-for-azure-stream-analytics-jobs"></a>Ottenere la ridondanza geografica per i processi di analisi di flusso di Azure

Analisi di flusso di Azure non fornisce il failover geografico automatico, ma è possibile ottenere la ridondanza geografica distribuendo processi di analisi di flusso identici in più aree di Azure. Ogni processo si connette a un input locale e a origini di output locali. È responsabilità dell'applicazione inviare i dati di input nei due input di area e riconciliare tra i due output regionali. I processi di analisi di flusso sono due entità separate.

Il diagramma seguente illustra una distribuzione del processo di analisi di flusso con ridondanza geografica di esempio con l'input dell'hub eventi e l'output del database di Azure.

:::image type="content" source="media/geo-redundancy/geo-redundant-jobs.png" alt-text="diagramma dei processi di analisi di flusso con ridondanza geografica":::

## <a name="primarysecondary-strategy"></a>Strategia primaria/secondaria

L'applicazione deve gestire il database di output dell'area considerata come primaria e che viene considerato secondario. In caso di errore dell'area primaria, l'applicazione passa al database secondario e avvia la lettura degli aggiornamenti da tale database. Il meccanismo effettivo che consente di ridurre al minimo le letture duplicate dipende dall'applicazione.Per semplificare questo processo, è possibile scrivere informazioni aggiuntive nell'output. Ad esempio, è possibile aggiungere un timestamp o un ID di sequenza a ogni output per evitare che le righe duplicate vengano ignorate un'operazione semplice. Una volta ripristinata l'area primaria, viene rilevata con il database secondario usando meccanismi analoghi.

Sebbene i diversi tipi di input e output consentano diverse opzioni di replica geografica, è consigliabile usare il modello descritto in questo articolo per ottenere la ridondanza geografica, in quanto fornisce flessibilità e controllo per i produttori di eventi e i consumer di eventi.

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare e gestire processi di Analisi di flusso di Azure con PowerShell](stream-analytics-monitor-and-manage-jobs-use-powershell.md)
* [Debug guidato dai dati in Analisi di flusso di Azure](stream-analytics-job-diagram-with-metrics.md)