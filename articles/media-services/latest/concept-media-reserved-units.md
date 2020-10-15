---
title: Media reserved Unit-Azure | Microsoft Docs
description: Media reserved Unit consentono di ridimensionare il processo multimediale e di determinare la velocità delle attività di elaborazione di contenuti multimediali.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: df691f5b9e74432f683e52fbb896dd4d44efe2aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91622087"
---
# <a name="media-reserved-units"></a>Unità riservate multimediali

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Servizi multimediali di Azure consente di ridimensionare l'elaborazione multimediale gestendo media reserved Unit (MRU). Un MRU fornisce capacità di calcolo aggiuntiva necessaria per la codifica dei supporti. Il numero di MRU determina la velocità con cui le attività multimediali vengono elaborate e il numero di attività multimediali che possono essere elaborate contemporaneamente in un account. Se, ad esempio, l'account ha cinque MRU e sono presenti attività da elaborare, è possibile eseguire simultaneamente cinque attività multimediali. Tutte le attività rimanenti verranno accodate e possono essere prelevate per l'elaborazione in sequenza al termine di un'attività in esecuzione. Ogni MRU di cui si esegue il provisioning comporta una prenotazione di capacità ma non fornisce risorse dedicate. Durante i periodi di richiesta estremamente elevata, è possibile che l'elaborazione non venga avviata immediatamente da tutti i MRU.

Un'attività è una singola operazione di lavoro su un asset, ad esempio la codifica del flusso adattivo. Quando si invia un processo, servizi multimediali si occuperà di suddividere il processo in singole operazioni, ad esempio le attività, che verranno quindi associate a MRU separati.

## <a name="choosing-between-different-reserved-unit-types"></a>Scelta tra i tipi diversi di unità riservata

La tabella seguente consente di scegliere tra diverse velocità di codifica.  Mostra la durata della codifica per un video di 7 minuti a 1080p, a seconda dell'uso di MRU.

|Tipo di unità richiesta|Scenario|Risultati di esempio per il video di 7 min con risoluzione 1080p |
|---|---|---|
| **S1**|Codifica con velocità in bit singola. <br/>File SD o con risoluzione inferiore, non dipendenti dall'ora, a basso costo.|La codifica in un file MP4 di risoluzione SD a bitrate singolo con "H264 Single bitrate SD 16x9" richiede circa 7 minuti.|
| **S2**|Codifica con velocità in bit singola e multipla.<br/>Uso normale per la codifica SD e HD.|La codifica con il set di impostazioni "H264 a bitrate singolo 720p" richiede circa 6 minuti.<br/><br/>La codifica con il set di impostazioni "H264 a bitrate multipli 720p" richiede circa 12 minuti.|
| **S3**|Codifica con velocità in bit singola e multipla.<br/>Video Full HD e con risoluzione 4K. Codifica dipendente dall'ora con completamento più rapido.|La codifica con il set di impostazioni "H264 Single bitrate 1080p" richiede circa 3 minuti.<br/><br/>La codifica con "Codec video H.264 a bitrate multiplo con risoluzione 1080p" preconfigurato richiede circa 8 minuti.|

> [!NOTE]
> Se non si esegue il provisioning di MRU per il proprio account, le attività multimediali verranno elaborate con le prestazioni di un file con estensione S1 e le attività verranno prelevate in sequenza. Nessuna capacità di elaborazione è riservata, quindi il tempo di attesa tra un completamento delle attività e il successivo avvio dipende dalla disponibilità delle risorse nel sistema.

## <a name="considerations"></a>Considerazioni

* Per i processi di analisi audio e analisi video attivati da Media Services V3 o Video Indexer, è consigliabile eseguire il provisioning dell'account con dieci unità S3. Se sono necessarie più di 10 MRU S3, aprire un ticket di supporto dal [portale di Azure](https://portal.azure.com/).
* Per le attività di codifica senza MRU, non esiste alcun limite superiore al tempo che le attività possono trascorrere nello stato in coda e al massimo una sola attività verrà eseguita alla volta.

## <a name="billing"></a>Fatturazione

L'addebito è basato sul numero di minuti in cui viene effettuato il provisioning di media reserved Unit nel tuo account, indipendentemente dal fatto che siano in esecuzione processi. Per una spiegazione dettagliata, vedere la sezione delle domande frequenti nella pagina dei [prezzi dei Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-step"></a>Passaggio successivo
[Ridimensionare media reserved Unit con CLI](media-reserved-units-cli-how-to.md) 
 [Analizzare i video](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Vedere anche

* [Quote e limiti](limits-quotas-constraints.md)
