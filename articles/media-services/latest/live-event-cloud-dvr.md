---
title: Uso di servizi multimediali di Azure, spostamento in tempo reale e Live-to-VOD (video on demand) | Microsoft Docs
description: Questo articolo spiega cos'è un output live e come usare un DVR cloud.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: ffcd279830cb49b64ddbb58a888ad7d653918b1b
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338848"
---
# <a name="using-time-shifting-and-live-to-vod-video-on-demand"></a>Uso della funzionalità di spostamento temporale e della versione Live-to-VOD (video on demand)

In servizi multimediali di Azure un oggetto di [output attivo](https://docs.microsoft.com/rest/api/media/liveoutputs) è come un registratore video digitale che rileverà e registrerà il flusso Live in un asset nell'account di servizi multimediali. Il contenuto registrato viene reso permanente nel contenitore definito dalla risorsa [Asset](https://docs.microsoft.com/rest/api/media/assets) (il contenitore si trova nell'account di archiviazione di Azure collegato all'account). L'output in tempo reale consente anche di controllare alcune proprietà del flusso Live in uscita, ad esempio la quantità di flusso che viene mantenuta nella registrazione dell'archivio (ad esempio, la capacità del DVR del cloud) e se i visualizzatori possono iniziare a guardare il flusso live. L'archivio su disco è una finestra di archivio circolare che include solo la quantità di contenuto specificato nella proprietà **archiveWindowLength** dell'output Live. Il contenuto che rientra in questa finestra viene automaticamente rimosso dal contenitore di archiviazione e non è recuperabile. Il valore archiveWindowLength rappresenta una durata dell'intervallo di tempo ISO-8601 (ad esempio, PTHH: MM: SS), che specifica la capacità del DVR, e può essere impostata da un minimo di 3 minuti a un massimo di 25 ore.

La relazione tra un evento Live e i relativi output Live è simile alla trasmissione televisiva tradizionale, in cui un canale (evento Live) rappresenta un flusso costante di video e una registrazione (output Live) ha come ambito un segmento di tempo specifico, ad esempio la sera Notizie dalle 6.00 alle 7:00. Una volta che il flusso è stato trasmesso nell'evento Live, è possibile iniziare l'evento di streaming creando un asset, un output Live e un localizzatore di streaming. L'output in tempo reale archivia il flusso e lo rende disponibile per i visualizzatori tramite l' [endpoint di streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints). È possibile creare più output Live (fino a un massimo di tre) in un evento live con lunghezze e impostazioni di archiviazione diverse. Per informazioni sul flusso di lavoro di streaming live, vedere la sezione [passaggi generali](live-streaming-overview.md#general-steps) .

## <a name="using-a-dvr-during-an-event"></a>Uso di un DVR durante un evento 

Questa sezione illustra come usare un DVR durante un evento per controllare quali parti del flusso sono disponibili per "Rewind".

Il valore archiveWindowLength determina per quanto tempo un visualizzatore può eseguire ricerche dalla posizione Live corrente. Il valore di archiveWindowLength determina anche per quanto tempo i manifesti client possono crescere.

Si supponga di voler trasmettere una partita di calcio e di ArchiveWindowLength solo 30 minuti. Un visualizzatore che inizia a guardare l’evento 45 minuti dopo l’inizio della partita può tornare indietro al massimo fino all’indicatore di 15 minuti. Gli output Live per il gioco continueranno fino a quando non viene arrestato l'evento Live, ma il contenuto che si trova all'esterno di archiveWindowLength viene continuamente rimosso dalla risorsa di archiviazione ed è non recuperabile. In questo esempio il video tra l'inizio dell'evento e il contrassegno di 15 minuti è stato eliminato dal DVR e dal contenitore nell'archivio BLOB per l'asset. L’archivio non può essere recuperato e viene rimosso dal contenitore nell’archivio blob di Azure.

Un evento Live supporta fino a tre output Live in esecuzione simultanea (è possibile creare al massimo 3 registrazioni/archivi da un flusso live nello stesso momento). Questo consente di pubblicare e archiviare parti diverse di un evento a seconda delle necessità. Ad esempio, immaginiamo di dover trasmettere un feed lineare live 24x7 e di creare "registrazioni" dei diversi programmi nel corso della giornata da offrire ai clienti come contenuto visualizzabile su richiesta. In questo caso, per prima cosa si crea un output live primario, con un intervallo di archiviazione breve di 1 ora o meno: questo è il flusso live primario con il quale si sintonizzano i visualizzatori. È possibile creare un localizzatore di streaming per questo output Live e pubblicarlo nell'applicazione o nel sito Web come feed in tempo reale. Mentre l'evento Live è in esecuzione, è possibile creare a livello di codice un secondo output Live simultaneo all'inizio di un programma (o 5 minuti prima per fornire alcuni handle da tagliare in seguito). Questo secondo output Live può essere eliminato 5 minuti dopo il termine del programma. Con questo secondo asset è possibile creare un nuovo localizzatore di streaming per pubblicare il programma come asset su richiesta nel catalogo dell'applicazione. È possibile ripetere questo processo più volte per gli altri limiti del programma o per evidenziare che si vuole condividere come video su richiesta, il tutto mentre il feed "Live" del primo output Live continua a trasmettere il feed lineare. 

## <a name="creating-an-archive-for-on-demand-playback"></a>Creazione di un archivio per la riproduzione su richiesta

L'asset a cui viene archiviata l'output Live, diventa automaticamente un asset su richiesta quando viene eliminato l'output in tempo reale. È necessario eliminare tutti gli output Live prima che un evento live possa essere arrestato. È possibile usare un flag facoltativo [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) per rimuovere automaticamente gli output Live all'arresto. 

Anche dopo l'arresto e l'eliminazione dell'evento, gli utenti saranno in grado di trasmettere il contenuto archiviato come video su richiesta, purché l'asset non venga eliminato. Un asset non deve essere eliminato se è usato da un evento. per prima cosa è necessario eliminare l'evento.

Se l'asset dell'output Live è stato pubblicato usando un localizzatore di streaming, l'evento Live (fino alla lunghezza della finestra DVR) continuerà a essere visualizzabile fino alla scadenza o all'eliminazione del localizzatore di streaming, a seconda di quale si verifichi per primo.

Per altre informazioni, vedere:

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)

> [!NOTE]
> Quando si elimina l'output Live, non si elimina l'asset e il contenuto sottostanti nell'asset. 

## <a name="next-steps"></a>Passaggi successivi

* [Sottoclip dei video](subclip-video-rest-howto.md).
* [Definire i filtri per gli asset](filters-dynamic-manifest-rest-howto.md).
