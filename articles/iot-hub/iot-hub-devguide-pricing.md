---
title: Informazioni sui prezzi dell&quot;hub IoT di Azure | Microsoft Docs
description: Guida per sviluppatori - Informazioni sulle misurazioni e sui prezzi nell&quot;hub IoT ed esempi reali.
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
ms.date: 12/12/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 3598f490752fa275d7c9c5a7d924c315e92f5b33
ms.openlocfilehash: e1f1dc43af2096fb9affc1d12433f091dd28c69c
ms.lasthandoff: 12/13/2016


---

# <a name="azure-iot-hub-pricing-information"></a>Informazioni sui prezzi dell'hub IoT di Azure

[Prezzi di Azure IoT Hub][lnk-pricing] fornisce informazioni generali su diversi SKU e sui prezzi per l'hub IoT. Questo articolo contiene dettagli aggiuntivi sul modo in cui le diverse funzionalità dell'hub IoT vengono misurate come messaggi dall'hub IoT.

## <a name="charges-per-operation"></a>Addebiti per ogni operazione

| Operazione | Informazioni di fatturazione | 
| --------- | ------------------- |
| Operazioni del registro delle identità <br/> (creazione, recupero, elenco, aggiornamento, eliminazione) | Nessun addebito. |
| Messaggi da dispositivo a cloud | Per i messaggi inviati correttamente è previsto un addebito in base a blocchi di 4 KB in ingresso nell'hub IoT, ad esempio un messaggio di 6 KB viene addebitato come 2 messaggi. |
| Messaggi da cloud a dispositivo | Per i messaggi inviati correttamente è previsto un addebito in base a blocchi di 4 KB, ad esempio un messaggio di 6 KB viene addebitato come 2 messaggi. |
| Caricamenti di file | Il trasferimento di file in Archiviazione di Azure non viene misurato dall'hub IoT. I messaggi di avvio e di completamento del trasferimento di file vengono addebitati come messaggi misurati in incrementi di 4 KB. Ad esempio, il trasferimento di un file di 10 MB viene addebitato come due messaggi, oltre al costo di Archiviazione di Azure. |
| Metodi diretti | Le richieste di metodo completate vengono addebitate in blocchi di 4 KB, le richieste con corpi non vuoti vengono addebitate in blocchi di 4 KB, come messaggi aggiuntivi. Le richieste ai servizi disconnessi vengono addebitate come messaggi in blocchi di 4 KB. Ad esempio, un metodo con un corpo di 6 KB che produce una risposta senza corpo dal dispositivo viene addebitato come due messaggi. Un metodo con un corpo di 6 KB che produce una risposta di 1 KB dal dispositivo viene addebitato come due messaggi per la richiesta e un messaggio aggiuntivo per la risposta. |
| Letture del dispositivo gemello | Le letture del dispositivo gemello dal dispositivo e dal back-end della soluzione vengono addebitate come messaggi in blocchi di 512 byte. Ad esempio, la lettura di un messaggio di 6 KB del dispositivo gemello viene addebitata come 12 messaggi. |
| Aggiornamenti del dispositivo gemello (tag e proprietà) | Gli aggiornamenti del dispositivo gemello dal dispositivo e il dispositivo vengono addebitati come messaggi in blocchi di 512 byte. Ad esempio, la lettura di un messaggio di 6 KB del dispositivo gemello viene addebitata come 12 messaggi. |
| Query del dispositivo gemello | Le query vengono addebitate come messaggi in base alle dimensioni del risultato in blocchi di 512 byte. |
| Operazioni dei processi <br/> (creazione, aggiornamento, elenco, eliminazione) | Nessun addebito. |
| Operazioni dei processi per ogni dispositivo | Le operazioni dei processi, ad esempio gli aggiornamenti del dispositivo gemello e i metodi, vengono addebitati come messaggi normali. Ad esempio, un processo che provoca 1000 chiamate del metodo con richieste di 1 KB e risposte con corpo vuoto viene addebitato come 1000 messaggi. |

> [!NOTE]
> Tutte le dimensioni vengono calcolate considerando le dimensioni del payload in byte. Il frame del protocollo viene ignorato. Nel caso dei messaggi, che hanno proprietà e corpo, le dimensioni vengono calcolate in modo indipendente dal protocollo, come illustrato nella [Guida per gli sviluppatori della messaggistica dell'hub IoT][lnk-message-size].

## <a name="example-1"></a>Esempio 1

Un dispositivo invia all'hub IoT un messaggio da 1 KB dal dispositivo al cloud ogni minuto e il messaggio viene quindi letto da Analisi di flusso di Azure. Il back-end della soluzione chiama un metodo, con payload di 512 byte, sul dispositivo ogni dieci minuti per attivare un'azione specifica. Il dispositivo risponde al metodo con un risultato di 200 byte.

Il dispositivo utilizza 1 messaggio * 60 minuti * 24 ore = 1440 messaggi al giorno per i messaggi da dispositivo al cloud e 2 richieste più risposta * 6 volte all'ora * 24 ore = 288 messaggi per i metodi, per un totale di 1728 messaggi al giorno.

## <a name="example-2"></a>Esempio n. 2

Un dispositivo invia un messaggio da dispositivo a cloud di 100 KB ogni ora. Aggiorna anche il rispettivo dispositivo gemello con payload di 1 KB ogni 4 ore. Il back-end della soluzione legge una volta al giorno il dispositivo gemello di 14 KB e lo aggiorna con payload di 512 byte per modificare le configurazioni.

Il dispositivo utilizza 25 (100 KB/4 KB) messaggi * 24 ore per messaggi dal dispositivo al cloud, più 1 messaggio * 6 volte al giorno per gli aggiornamenti del dispositivo gemello, per un totale di 156 messaggi al giorno.
Il back-end della soluzione usa 28 messaggi (14 KB/0,5 KB) per leggere il dispositivo gemello più 1 messaggio per aggiornarlo, per un totale di 29 messaggi.

In totale il dispositivo e il back-end della soluzione usano 185 messaggi al giorno.


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messaging.md#message-size

