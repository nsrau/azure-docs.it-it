---
title: Informazioni sui prezzi dell'hub IoT di Azure | Microsoft Docs
description: Guida per sviluppatori - Informazioni sulle misurazioni e sui prezzi nell'hub IoT ed esempi reali.
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: dbceca3daed53da2a383fd8a6b7a0bd1f225cf77
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="azure-iot-hub-pricing-information"></a>Informazioni sui prezzi dell'hub IoT di Azure

[Prezzi di Azure IoT Hub][lnk-pricing] fornisce informazioni generali su diversi SKU e sui prezzi per l'hub IoT. Questo articolo contiene dettagli aggiuntivi sul modo in cui le diverse funzionalità dell'hub IoT vengono misurate come messaggi dall'hub IoT.

## <a name="charges-per-operation"></a>Addebiti per ogni operazione

| Operazione | Informazioni di fatturazione | 
| --------- | ------------------- |
| Operazioni del registro delle identità <br/> (creazione, recupero, elenco, aggiornamento, eliminazione) | Nessun addebito. |
| Messaggi da dispositivo a cloud | Per i messaggi inviati correttamente è previsto un addebito in base a blocchi di 4 KB in ingresso nell'hub IoT. Un messaggio di 6 KB, ad esempio, viene addebitato come 2 messaggi. |
| Messaggi da cloud a dispositivo | Per i messaggi inviati correttamente è previsto un addebito in base a blocchi di 4 KB, ad esempio un messaggio di 6 KB viene addebitato come 2 messaggi. |
| Caricamenti di file | Il trasferimento di file in Archiviazione di Azure non viene misurato dall'hub IoT. I messaggi di avvio e di completamento del trasferimento di file vengono addebitati come messaggi misurati in incrementi di 4 KB. Il trasferimento di un file di 10 MB, ad esempio, viene addebitato come due messaggi, oltre al costo di Archiviazione di Azure. |
| Metodi diretti | Le richieste di metodo completate vengono addebitate in blocchi di 4 KB, le risposte con corpi non vuoti vengono addebitate in blocchi di 4 KB come messaggi aggiuntivi. Le richieste ai dispositivi disconnessi vengono addebitate come messaggi in blocchi di 4 KB. Un metodo con corpo da 6 KB che dà come risultato una risposta senza corpo dal dispositivo, ad esempio, viene addebitato come due messaggi. Un metodo con corpo da 6 KB che dà come risultato una risposta da 1 KB dal dispositivo viene addebitato come due messaggi per la richiesta, con in più un altro messaggio per la risposta. |
| Letture del dispositivo gemello | Le letture del dispositivo gemello dal dispositivo e dal back-end della soluzione vengono addebitate come messaggi in blocchi di 512 byte. La lettura di un messaggio di 6 KB del dispositivo gemello, ad esempio, viene addebitata come 12 messaggi. |
| Aggiornamenti del dispositivo gemello (tag e proprietà) | Gli aggiornamenti del dispositivo gemello dal dispositivo e dal back-end della soluzione vengono addebitati come messaggi in blocchi di 512 byte. La lettura di un messaggio di 6 KB del dispositivo gemello, ad esempio, viene addebitata come 12 messaggi. |
| Query del dispositivo gemello | Le query vengono addebitate come messaggi in base alle dimensioni del risultato in blocchi di 512 byte. |
| Operazioni dei processi <br/> (creazione, aggiornamento, elenco, eliminazione) | Nessun addebito. |
| Operazioni dei processi per ogni dispositivo | Le operazioni dei processi, ad esempio gli aggiornamenti del dispositivo gemello e i metodi, vengono addebitati come messaggi normali. Un processo che esegue 1000 chiamate di metodo con richieste di 1 KB e risposte con corpo vuoto, ad esempio, viene addebitato come 1000 messaggi. |

> [!NOTE]
> Tutte le dimensioni vengono calcolate considerando le dimensioni del payload in byte. Il frame del protocollo viene ignorato. Per i messaggi, che hanno proprietà e corpo, le dimensioni vengono calcolate in modo indipendente dal protocollo. Per altre informazioni, vedere [Guida per gli sviluppatori di messaggistica di hub IoT][lnk-message-size].

## <a name="example-1"></a>Esempio 1

Un dispositivo invia all'hub IoT un messaggio da 1 KB dal dispositivo al cloud al minuto e il messaggio viene quindi letto da Analisi di flusso di Azure. Il back-end della soluzione chiama un metodo con un payload di 512 byte per il dispositivo ogni 10 minuti per attivare un'azione specifica. Il dispositivo risponde al metodo con un risultato di 200 byte.

Il dispositivo usa:

* Un messaggio * 60 minuti * 24 ore = 1440 messaggi al giorno per i messaggi da dispositivo a cloud.
* Due richieste più la risposta * 6 volte all'ora * 24 ore = 288 messaggi per i metodi.

Il totale di questo calcolo corrisponde a 1728 messaggi al giorno.

## <a name="example-2"></a>Esempio n. 2

Un dispositivo invia un messaggio da dispositivo a cloud di 100 KB ogni ora. Il dispositivo aggiorna anche il proprio dispositivo gemello con payload di 1 KB ogni quattro ore. Il back-end della soluzione legge una volta al giorno il dispositivo gemello di 14 KB e lo aggiorna con payload di 512 byte per modificare le configurazioni.

Il dispositivo usa:

* 25 (100 KB/4 KB) messaggi * 24 ore per i messaggi da dispositivo a cloud.
* Due messaggi (1 KB/0,5 KB) * sei volte al giorno per gli aggiornamenti del dispositivo gemello.

Il totale di questo calcolo corrisponde a 612 messaggi al giorno.

Il back-end della soluzione usa 28 messaggi (14 KB/0,5 KB) per leggere il dispositivo gemello più un messaggio per aggiornarlo, per un totale di 29 messaggi.

In totale il dispositivo e il back-end della soluzione usano 641 messaggi al giorno.


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messages-construct.md
