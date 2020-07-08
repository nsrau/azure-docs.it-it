---
title: Analisi di video in tempo reale su IoT Edge domande frequenti-Azure
description: Questo argomento fornisce le risposte alle analisi video in tempo reale su IoT Edge domande frequenti.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 0a6c1c0f26116227454fa0968264644ea7a43178
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260734"
---
# <a name="frequently-asked-questions-faqs"></a>Domande frequenti (FAQ)

Questo argomento fornisce le risposte alle analisi video in tempo reale su IoT Edge domande frequenti.

## <a name="general"></a>Generale

Quali sono le variabili di sistema che è possibile usare nella definizione della topologia Graph?

|Variabile   |Descrizione|
|---|---|
|[System.DateTime](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Rappresenta un istante di tempo, in genere espresso come data e ora del giorno.|
|System. GraphTopologyName   |Rappresenta una topologia del grafico multimediale, che include il progetto di un grafico.|
|System. GraphInstanceName|  Rappresenta un'istanza del grafico multimediale, include i valori dei parametri e fa riferimento alla topologia.|

## <a name="configuration-and-deployment"></a>Configurazione e distribuzione

È possibile distribuire il modulo Media Edge in un dispositivo Windows 10?
    * Sì. Vedere l'articolo sui [contenitori Linux in Windows 10](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Acquisisci dalla fotocamera IP e dalle impostazioni RTSP

* È necessario usare un SDK speciale sul dispositivo per inviare un flusso video?
    * No. Analisi video in tempo reale su IoT Edge supporta l'acquisizione di supporti tramite il protocollo di streaming video RTSP (supportato nella maggior parte delle fotocamere IP).
* È possibile eseguire il push di contenuti multimediali in analisi video in tempo reale su IoT Edge usando RTMP o Smooth (come un evento live di servizi multimediali)?
    * No. LVA supporta solo RTSP per l'acquisizione di video da fotocamere IP.
    * Qualsiasi fotocamera che supporta lo streaming RTSP su TCP/HTTP dovrebbe funzionare. 
* È possibile reimpostare o aggiornare l'URL dell'origine RTSP in un'istanza del grafo?
    * Sì, quando l'istanza del grafo è in stato inattivo.  
* È disponibile un simulatore RTSP da usare durante i test e lo sviluppo?
    * Sì. Per supportare il processo di apprendimento è disponibile un modulo [RTSP Simulator](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge disponibile per l'uso nelle guide introduttive e nelle esercitazioni. Questo modulo viene fornito come miglior tentativo e potrebbe non essere sempre disponibile. Si consiglia vivamente di non usare questa operazione per più di alcune ore. È consigliabile investire nei test con l'origine RTSP effettiva prima di creare piani per una distribuzione di produzione.
* L'individuazione ONVIF di fotocamere IP in rete perimetrale è supportata?
    * No, non è previsto alcun supporto per l'individuazione ONVIF dei dispositivi perimetrali.

## <a name="streaming-and-playback"></a>Streaming e riproduzione

* Le risorse registrate in AMS dal dispositivo perimetrale possono essere riprodotte usando tecnologie di streaming di servizi multimediali come HLS o DASH?
    * Sì. Gli asset registrati possono essere trasmessi come qualsiasi altro asset in servizi multimediali di Azure. Per eseguire lo streaming del contenuto, è necessario che sia stato creato un endpoint di streaming e che si trovi nello stato in esecuzione. L'uso del processo di creazione del localizzatore di streaming standard consentirà di accedere a un manifesto HLS o DASH per lo streaming a qualsiasi framework di Player idoneo. Per informazioni dettagliate sulla creazione di manifesti HLS o DASH, vedere Creazione [dinamica dei pacchetti](../latest/dynamic-packaging-overview.md).
* È possibile usare le funzionalità di protezione del contenuto standard e DRM di servizi multimediali in un asset archiviato?
    * Sì. Tutte le funzionalità di protezione del contenuto della crittografia dinamica standard e DRM sono disponibili per l'uso negli asset registrati da un grafico multimediale.
* Quali giocatori è possibile usare per visualizzare il contenuto degli asset registrati?
   * Sono supportati tutti i lettori standard che supportano Apple HTTP Live Streaming (HLS) versione 3 o 4. Inoltre, è supportato anche qualsiasi lettore in grado di riprodurre MPEG-DASH compatibile.
    I giocatori consigliati per i test includono:

    * [Azure Media Player](../latest/use-azure-media-player.md)
    * [HLS.js](https://hls-js.netlify.app/demo/)
    * [Video.js](https://videojs.com/)
    * [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
    * [Lettore Shaka](https://github.com/google/shaka-player)
    * [ExoPlayer](https://github.com/google/ExoPlayer)
    * [HTTP Live Streaming nativo Apple](https://developer.apple.com/streaming/)
    * Microsoft Edge, Chrome o Safari incorporato nel lettore video HTML5
    * Lettori commerciali che supportano la riproduzione HLS o DASH
* Quali sono i limiti per lo streaming di un asset del grafico multimediale?
    * Il flusso di un asset attivo o registrato da un grafico multimediale usa lo stesso endpoint di streaming e infrastruttura a scalabilità elevata supportato da servizi multimediali per streaming on demand e Live per i clienti di media & Entertainment, OTT e broadcast. Ciò significa che è possibile abilitare in modo rapido e semplice la rete CDN di Azure, Verizon o Akamai per distribuire il contenuto a un pubblico con un numero ridotto di utenti o fino a milioni a seconda dello scenario.

    Il contenuto può essere recapitato usando Apple HTTP Live Streaming (HLS) o MPEG-DASH.

## <a name="monitoring-and-metrics"></a>Monitoraggio e metriche

* È possibile monitorare il grafico multimediale sul perimetro usando griglia di eventi?
    * No. Griglia di eventi attualmente non supportata.
* È possibile usare monitoraggio di Azure per visualizzare l'integrità, le metriche e le prestazioni dei grafici multimediali nel cloud o sul perimetro?
    * No.
* Sono disponibili strumenti che consentono di monitorare più facilmente il modulo IoT Edge di servizi multimediali?
    * Visual Studio Code supporta l'estensione "Azure Internet per gli strumenti", che consente di monitorare facilmente gli endpoint del modulo LVAEdge. È possibile usare questo strumento per avviare rapidamente il monitoraggio dell'endpoint predefinito dell'hub Internet per gli "eventi" e visualizzare i messaggi di inferenza indirizzati dal dispositivo perimetrale al cloud. 

    Inoltre, è possibile usare questa estensione per modificare il modulo gemello per il modulo LVAEdge per modificare le impostazioni del grafico multimediale.

Per ulteriori informazioni, vedere l'articolo [monitoraggio e registrazione](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Fatturazione e disponibilità

* Come viene fatturato LiveVideo Analytics in IoT Edge?
    * Per informazioni dettagliate, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/) .

## <a name="next-steps"></a>Passaggi successivi

[Avvio rapido: Introduzione: analisi di video live in IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
