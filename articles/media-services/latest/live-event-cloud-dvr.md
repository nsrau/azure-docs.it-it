---
title: Usare gli output in tempo reale e di spostamento per creare la riproduzione video su richiesta
titleSuffix: Azure Media Services
description: Questo articolo descrive come usare gli output in tempo reale e di spostamento per registrare i flussi live e creare la riproduzione su richiesta.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 11485ebac449cbde0a4f31e2a099a153476577b6
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89289512"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Usare gli output in tempo reale e di spostamento per creare la riproduzione video su richiesta

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In servizi multimediali di Azure un oggetto di [output attivo](/rest/api/media/liveoutputs) è come un registratore video digitale che rileverà e registrerà il flusso Live in un asset nell'account di servizi multimediali. Il contenuto registrato viene reso permanente nel contenitore definito dalla risorsa [Asset](/rest/api/media/assets) (il contenitore si trova nell'account di archiviazione di Azure collegato all'account). L'output in tempo reale consente anche di controllare alcune proprietà del flusso Live in uscita, ad esempio la quantità di flusso che viene mantenuta nella registrazione dell'archivio (ad esempio, la capacità del DVR del cloud) o quando i visualizzatori possono iniziare a osservare il flusso live. L'archivio su disco è una finestra di archivio circolare che include solo la quantità di contenuto specificato nella proprietà **archiveWindowLength** dell'output Live. Il contenuto esterno a questa finestra viene eliminato automaticamente dal contenitore di archiviazione e non è ripristinabile. Il valore archiveWindowLength rappresenta una durata dell'intervallo di tempo ISO-8601 (ad esempio, PTHH: MM: SS), che specifica la capacità del DVR. Il valore può essere impostato da un minimo di un minuto a un massimo di 25 ore.

La relazione tra un evento Live e i relativi output Live è simile alla trasmissione TV tradizionale, in quanto un canale (evento Live) rappresenta un flusso costante di video e una registrazione (output Live) ha come ambito un segmento di tempo specifico (ad esempio, le notizie serali dalle 6.00 alle 7:00). Una volta che il flusso è stato trasmesso nell'evento Live, è possibile iniziare l'evento di streaming creando un asset, un output Live e un localizzatore di streaming. L'output live archivierà il flusso e lo renderà disponibile agli utenti tramite l'[endpoint di streaming](/rest/api/media/streamingendpoints). È possibile creare più output live (tre al massimo) su un evento live con lunghezze e impostazioni di archivio diverse. Per informazioni sul flusso di lavoro di streaming live, vedere la sezione [passaggi generali](live-streaming-overview.md#general-steps) .

## <a name="using-a-dvr-during-an-event"></a>Uso di un DVR durante un evento

Questa sezione illustra come usare un DVR durante un evento per controllare quali parti del flusso sono disponibili per "Rewind".

Il `archiveWindowLength` valore determina per quanto tempo un visualizzatore può passare dalla posizione Live corrente. Il `archiveWindowLength` valore determina anche per quanto tempo i manifesti client possono crescere.

Si supponga di trasmettere una partita di calcio e `ArchiveWindowLength` di avere solo 30 minuti. Un visualizzatore che inizia a guardare l’evento 45 minuti dopo l’inizio della partita può tornare indietro al massimo fino all’indicatore di 15 minuti. Gli output Live per il gioco continueranno fino a quando l'evento Live non verrà interrotto. Il contenuto che si trova all'esterno di archiveWindowLength viene continuamente rimosso dalla risorsa di archiviazione ed è non recuperabile. In questo esempio il video tra l'inizio dell'evento e il contrassegno di 15 minuti è stato eliminato dal DVR e dal contenitore nell'archivio BLOB per l'asset. L'archivio non è reversibile e viene rimosso dal contenitore nell'archivio BLOB di Azure.

Un evento Live supporta fino a tre output Live in esecuzione simultanea (è possibile creare al massimo 3 registrazioni/archivi da un flusso live nello stesso momento). Questo supporto consente di pubblicare e archiviare parti diverse di un evento in base alle esigenze. Ad esempio, immaginiamo di dover trasmettere un feed lineare live 24x7 e di creare "registrazioni" dei diversi programmi nel corso della giornata da offrire ai clienti come contenuto visualizzabile su richiesta. Per questo scenario, creare prima di tutto un output Live primario con una breve finestra di archiviazione di 1 ora o meno, ovvero il flusso Live primario a cui si consentiranno i visualizzatori. È possibile creare un localizzatore di streaming per questo output Live e pubblicarlo nell'app o nel sito Web come feed in tempo reale. Mentre l'evento live è in esecuzione, è possibile creare a livello di codice un secondo output live simultaneo all'inizio di un programma (o 5 minuti prima per fornire alcuni handle per il trimming successivo). Questo secondo output live può essere eliminato 5 minuti dopo la fine del programma. Con questo secondo asset è possibile creare un nuovo localizzatore di streaming per pubblicare il programma come asset su richiesta nel catalogo dell'app. È possibile ripetere questo processo più volte per altri limiti del programma o elementi di rilievo da condividere come video su richiesta, mentre il feed "Live" dal primo output live continua a trasmettere il feed lineare.

## <a name="creating-an-archive-for-on-demand-playback"></a>Creazione di un archivio per la riproduzione su richiesta

L'asset che l'output attivo sta archiviando automaticamente diventa un asset su richiesta quando viene eliminato l'output in tempo reale. È necessario eliminare tutti gli output Live prima che un evento live possa essere arrestato. È possibile usare un flag facoltativo [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body) per rimuovere automaticamente gli output Live all'arresto.

Anche dopo l'arresto e l'eliminazione dell'evento, gli utenti possono trasmettere il contenuto archiviato come video su richiesta, purché non venga eliminato l'asset. Una risorsa non deve essere eliminata se usata da un evento. per prima cosa è necessario eliminare l'evento.

Se la risorsa dell'output Live è stata pubblicata usando un localizzatore di streaming, l'evento Live (fino alla lunghezza della finestra DVR) continuerà a essere visualizzabile fino alla scadenza o all'eliminazione del localizzatore di streaming, a seconda di quale evento si verifica per primo.

Per altre informazioni, vedere:

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)

> [!NOTE]
> Quando si elimina l'output Live, l'asset e il contenuto sottostanti non vengono eliminati nell'asset.

## <a name="next-steps"></a>Passaggi successivi

* [Sottoclip dei video](subclip-video-rest-howto.md).
* [Definire i filtri per gli asset](filters-dynamic-manifest-rest-howto.md).
