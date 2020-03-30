---
title: Utilizzare il cambio temporale e le uscite dal vivo per creare la riproduzione video su richiesta
titleSuffix: Azure Media Services
description: Questo articolo descrive come utilizzare time-shifting e Live Outputs per registrare flussi live e creare la riproduzione su richiesta.
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
ms.openlocfilehash: 4c7618b60e5fd86a9b8b3f22fb3333c00cfdfa61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899791"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Utilizzare il cambio temporale e le uscite dal vivo per creare la riproduzione video su richiesta

In Servizi multimediali di Azure, un oggetto [Live Output](https://docs.microsoft.com/rest/api/media/liveoutputs) è come un registratore video digitale che intercetta e registra il flusso live in una risorsa nell'account di Servizi multimediali. Il contenuto registrato viene salvato in modo permanente nel contenitore definito dalla risorsa [Asset](https://docs.microsoft.com/rest/api/media/assets) (il contenitore si trova nell'account di archiviazione di Azure collegato all'account). L'output live consente inoltre di controllare alcune proprietà del live streaming in uscita, ad esempio la quantità di streaming viene mantenuta nella registrazione dell'archivio (ad esempio, la capacità del DVR cloud) o quando gli spettatori possono iniziare a guardare il live streaming. L'archivio su disco è una "finestra" di archivio circolare che contiene solo la quantità di contenuto specificata nella proprietà **archiveWindowLength** dell'output live. Il contenuto che non rientra in questa finestra viene automaticamente eliminato dal contenitore di archiviazione e non è recuperabile. Il valore archiveWindowLength rappresenta una durata dell'intervallo di tempo ISO-8601 (ad esempio, PTHH:MM:SS), che specifica la capacità del DVR. Il valore può essere impostato da un minimo di tre minuti a un massimo di 25 ore.

La relazione tra un Live Event e le sue Live Outputs è simile alla trasmissione televisiva tradizionale, in quanto un canale (Live Event) rappresenta un flusso costante di video e una registrazione (Live Output) ha come ambito un segmento di tempo specifico (ad esempio, notizie serali da dalle 18:30 alle 19:00). Dopo aver creato il flusso dello streaming nell'evento live, puoi iniziare l'evento di streaming creando un asset, Live Output e streaming locator. L'output live archivierà il flusso e lo renderà disponibile agli utenti tramite l'[endpoint di streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints). È possibile creare più output live (tre al massimo) su un evento live con lunghezze e impostazioni di archivio diverse. Per informazioni sul flusso di lavoro di live streaming, vedere la sezione [Passaggi generali.](live-streaming-overview.md#general-steps)

## <a name="using-a-dvr-during-an-event"></a>Utilizzo di un DVR durante un evento

In questa sezione viene illustrato come utilizzare un DVR durante un evento per controllare quali parti del flusso sono disponibili per il "riavvolgimento".

Il `archiveWindowLength` valore determina quanto indietro nel tempo un visualizzatore può andare dalla posizione live corrente. Il `archiveWindowLength` valore determina anche per quanto tempo i manifesti client possono crescere.

Supponiamo che tu stia trasmettendo una `ArchiveWindowLength` partita di calcio e che abbia solo 30 minuti. Un visualizzatore che inizia a guardare l’evento 45 minuti dopo l’inizio della partita può tornare indietro al massimo fino all’indicatore di 15 minuti. Le uscite dal vivo per il gioco continueranno fino all'arresto dell'evento live. Il contenuto che non rientra in archiveWindowLength viene continuamente eliminato dall'archiviazione e non è recuperabile. In questo esempio, il video tra l'inizio dell'evento e il contrassegno di 15 minuti sarebbe stato eliminato dal DVR e dal contenitore nell'archivio BLOB per l'asset. L'archivio non è recuperabile e viene rimosso dal contenitore nell'archiviazione BLOB di Azure.The archive isn't recoverable and is removed from the container in Azure blob storage.

Un evento dal vivo supporta fino a tre uscite dal vivo contemporaneamente (è possibile creare al massimo 3 registrazioni/archivi da un live streaming contemporaneamente). Questo supporto consente di pubblicare e archiviare diverse parti di un evento in base alle esigenze. Ad esempio, immaginiamo di dover trasmettere un feed lineare live 24x7 e di creare "registrazioni" dei diversi programmi nel corso della giornata da offrire ai clienti come contenuto visualizzabile su richiesta. Per questo scenario, devi prima creare un Live Output primario con una breve finestra di archiviazione di 1 ora o meno, questo è il flusso live principale in cui gli spettatori si sintonizzano. È necessario creare un localizzatore di streaming per questo live output e pubblicarlo nell'app o nel sito Web come feed "Live". Mentre l'evento live è in esecuzione, è possibile creare a livello di codice un secondo output live simultaneo all'inizio di un programma (o 5 minuti prima per fornire alcuni handle per il trimming successivo). Questo secondo output live può essere eliminato 5 minuti dopo la fine del programma. Con questo secondo asset, puoi creare un nuovo localizzatore di streaming per pubblicare questo programma come asset su richiesta nel catalogo dell'app. È possibile ripetere questo processo più volte per altri limiti del programma o elementi di rilievo da condividere come video su richiesta, mentre il feed "Live" dal primo output live continua a trasmettere il feed lineare.

## <a name="creating-an-archive-for-on-demand-playback"></a>Creazione di un archivio per la riproduzione su richiesta

L'asset in cui viene eseguito l'archiviazione di Live Output diventa automaticamente un asset su richiesta quando viene eliminato l'output live. È necessario eliminare tutti gli output live prima di poter arrestare un evento live. È possibile utilizzare un flag facoltativo [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) per rimuovere automaticamente gli output live all'arresto.

Anche dopo aver interrotto ed eliminato l'evento, gli utenti possono trasmettere i contenuti archiviati come video su richiesta, purché non elimini la risorsa. Una risorsa non deve essere eliminata se viene usata da un evento; l'evento deve essere eliminato per primo.

Se hai pubblicato la risorsa del tuo Live Output utilizzando un localizzatore di streaming, l'evento live (fino alla lunghezza della finestra DVR) continuerà a essere visibile fino alla scadenza o all'eliminazione del localizzatore, a seconda dell'evento che si verifica per primo.

Per altre informazioni, vedere:

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)

> [!NOTE]
> Quando elimini l'Output live, non elimini l'asset sottostante e il contenuto nell'asset.

## <a name="next-steps"></a>Passaggi successivi

* [Ritagliare i video](subclip-video-rest-howto.md).
* [Definire i filtri per le risorse](filters-dynamic-manifest-rest-howto.md).
