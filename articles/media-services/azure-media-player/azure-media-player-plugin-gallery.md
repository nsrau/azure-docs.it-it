---
title: Raccolta di plug-in di Azure Media Player
description: Questo articolo contiene un elenco di plug-in disponibili per Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 206200f3ba0757cd738439b58c8b94874cf5a938
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727498"
---
# <a name="azure-media-player-plugin-gallery"></a>Raccolta di plug-in di Azure Media Player #

## <a name="plugins"></a>Plug-in ##

| Nome del plug-in                         | URL demo                    | Codice sorgente                | Descrizione    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| Funzionalità aggiuntive                 | | | |
| **Novità** AMP360Video                | [Demo](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | Il plug-in consente di visualizzare video a 360 gradi in Azure Media Player, sul desktop o in dispositivi compatibili con la realtà virtuale. La documentazione completa è disponibile [qui](https://doc\.babylonjs\.com/extensions/amp360video): |
|  Sprite Tip                         | [Demo](http://www.skymedia.tv/asset/sprite)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Plug-in di Azure Media Player per il rendering della sequenza temporale di un'immagine di anteprima video sprite generata da Media Encoder Standard (MES) di Servizi multimediali di Azure. |
| Diagnostics Overlay                 | [Demo](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | Questo plug-in visualizza tutti i principali parametri, le statistiche del video, tutti gli eventi del ciclo di vita di riproduzione dei video e le informazioni sulla protezione DRM, se abilitata, ad esempio ID chiave e URL di acquisizione della licenza.                                                                                                                                                                      |
| Frame rate and Timecode calculator | [Demo](http://mconvertitest001.blob.core.windows.net/public/example.html)                        | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Questo plug-in calcola la frequenza dei fotogrammi del video in base ai moduli MP4 `tfhd`/`trun` del primo frammento di video MPEG-DASH, analizza il valore della scala temporale dal manifesto del client MPEG-DASH e fornisce anche un modo per generare il timecode per un determinato tempo assoluto del lettore (oltre a fornire il tempo assoluto del lettore in base al timecode) |
| <strike>Playback Speed</strike>                      | [Demo](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Questo plug-in consente agli utenti di controllare la velocità del video. *Si noti che questa funzionalità è automaticamente disponibile nella versione 2.0.0 e successive di Azure Media Player, ma è disabilitata per impostazione predefinita.* Per informazioni su come abilitarla, vedere gli esempi [qui](https://github.com/Azure-Samples/azure-media-player-samples) |
| Hover Time Tip                      | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Visualizza un suggerimento sul tempo al passaggio del mouse sull'indicatore di stato per la ricerca accurata del tempo. *Nota: questo plug-in è già integrato in Azure Media Player* ma se interessa è possibile vedere come è stato programmato.                                                                                                                       |
| Title Overlay                       | [Demo](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin/")                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Applica il titolo del video come sovraimpressione configurabile sullo schermo |
| Timeline Markers                    | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Questo plug-in sovrappone minuscoli contrassegni dell'ora sull'indicatore di stato in specifici momenti. |
| Analytics                           | | | |
| Application Insights                | [Blog Post](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Plug-in che tiene traccia delle metriche del lettore e le inserisce in Power BI per una rappresentazione grafica intuitiva dell'esperienza utente. |
| Google Analytics                    | N/D                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Plug-in Google Analytics per Azure Media Player |
| Diagnostica                         | | | |
| Diagnostics Output                  | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Questo plug-in genera una serie di dati diagnostici del lettore. Per vederlo in azione, seguire il collegamento della demo e aprire la console JavaScript. |
| Ease of Access                      | | | |
| Zoom avanti                             | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Questo plug-in visualizza una scala di zoom avanti trascinabile sullo schermo del lettore per consentire agli utenti di fare zoom avanti sul contenuto |
| Live Captions                       | [Post di blog di Azure](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/), [post diSubPly](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | N/D | *Vedere i post per altre informazioni.* Flusso di lavoro end-to-end progettato per il plug-in incorporato di sottotitoli in tempo reale per Azure Media Player. Per altre informazioni sulla soluzione, fare clic sul collegamento a sinistra per passare al sito di SubPly |
| Hot Keys                            | <strike>[Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | Il plug-in Hot Keys consente di controllare vari aspetti del lettore con combinazioni di plug-in generici, come F per lo schermo intero, M per disattivare l'audio e i tasti di direzione per il controllo indicatore di stato. *Nota: questo plug-in è già integrato in Azure Media Player, ma è possibile usarlo come risorsa* |
| Social media                              | | | |
| Condividi                               | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Questo plug-in aggiunge un pulsante di condivisione alla barra di controllo del lettore in modo che gli utenti possano condividere il video con i loro amici tramite Facebook, Twitter o LinkedIn. |

## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)