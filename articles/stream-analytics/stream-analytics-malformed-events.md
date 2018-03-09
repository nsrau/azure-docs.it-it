---
title: Risoluzione dei problemi relativi agli eventi di input in formato non valido in Analisi di flusso di Azure | Microsoft Docs
description: Come stabilire quale evento nei dati di input causa problemi in un processo di Analisi di flusso
keywords: 
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/19/2018
ms.author: sngun
ms.openlocfilehash: 2b4f82bae20c3cb398848a89715bfdc1316e1ba1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2018
---
# <a name="troubleshoot-for-malformed-input-events"></a>Risoluzione dei problemi relativi agli eventi di input in formato non valido

Quando il flusso di input del processo di Analisi di flusso contiene messaggi in formato non valido, causa problemi di serializzazione. I messaggi in formato non valido includono errori di serializzazione, ad esempio parentesi mancanti in un oggetto JSON o formato del timestamp non corretto. Quando un processo di Analisi di flusso riceve un messaggio in formato non valido, elimina il messaggio e visualizza un messaggio di avviso per l'utente. Viene visualizzato un simbolo di avviso nel riquadro **Input** del processo di Analisi di flusso:

![Riquadro Input](media/stream-analytics-malformed-events/inputs_tile.png)

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

1. Passare al riquadro Input e fare clic per visualizzare gli avvisi.
2. Nel riquadro dei dettagli di input viene visualizzato un set di avvisi con i dettagli sul problema. Di seguito è riportato un messaggio di avviso di esempio, che mostra la partizione, l'offset e i numeri di sequenza dei dati JSON in formato non valido. 

   ![Messaggio di avviso con offset](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Per ottenere i dati JSON in formato non valido, eseguire il frammento di codice seguente. Questo blocco di codice legge l'ID della partizione, l'offset e stampa i dati. È possibile ottenere l'esempio completo dal [repository degli esempi di GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Dopo aver eseguito la lettura dei dati, è possibile analizzare e correggere il formato di serializzazione.

```csharp
static void PrintMessages(string partitionId, long offset, int numberOfEvents)
        {
            EventHubReceiver receiver;
            
            try
            {
                foreach (var e in receiver.Receive(numberOfEvents, TimeSpan.FromMinutes(1)))
                {
                    Console.WriteLine(Encoding.UTF8.GetString(e.GetBytes()));
                    Console.WriteLine("----");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.ToString());
                return;
            }

            receiver.Close();
        }
```
## <a name="next-steps"></a>Passaggi successivi

* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
