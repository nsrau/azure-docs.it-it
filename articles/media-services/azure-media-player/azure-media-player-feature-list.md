---
title: Elenco delle funzionalità di Azure Media PlayerAzure Media Player feature list
description: Informazioni di riferimento sulle funzionalità per Azure Media Player.A feature reference for Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727229"
---
# <a name="feature-list"></a>Elenco delle funzionalità #
Ecco l'elenco delle funzionalità testate e non supportate:

|                                         | Testato | PARZIALMENTE TESTATO | Testate | Supportato | NOTE                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| Riproduzione                                |        |                  |          |             |                                                                                                                      |
| Riproduzione su richiesta di base                | X      |                  |          |             | Supporta solo flussi da Servizi multimediali di AzureSupports streams from Azure Media Services only                                                                      |
| Riproduzione live di base                     | X      |                  |          |             | Supporta solo flussi da Servizi multimediali di AzureSupports streams from Azure Media Services only                                                                      |
| AES                                     | X      |                  |          |             | Supporta il servizio di distribuzione delle chiavi di Servizi multimediali di AzureSupports Azure Media Services Key Delivery Service                                                                   |
| Multi-DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Supporta il servizio di distribuzione delle chiavi di Servizi multimediali di AzureSupports Azure Media Services Key Delivery Service                                                                   |
| Widevine                                |        | X                |          |             | Supporta le caselle Widevine PSSH delineate nel manifesto                                                                    |
| FairPlay                                |        | X                |          |             | Supporta il servizio di distribuzione delle chiavi di Servizi multimediali di AzureSupports Azure Media Services Key Delivery Service                                                                   |
| Tecnici                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Flash Fallback (FlashSS)                | X      |                  |          |             | Non tutte le funzioni sono disponibili su questa tecnologia.                                                                         |
| Silverlight Fallback SilverlightSS      | X      |                  |          |             | Non tutte le funzioni sono disponibili su questa tecnologia.                                                                         |
| Pass-through HLS nativo (Html5)         |        | X                |          |             | Non tutte le funzionalità sono disponibili su questa tecnologia a causa delle restrizioni della piattaforma.                                            |
| Funzionalità                                |        |                  |          |             |                                                                                                                      |
| Supporto dell'API                             | X      |                  |          |             | Vedere l'elenco dei problemi noti                                                                                                |
| Interfaccia utente di base                                | X      |                  |          |                                                                                                                                    |
| Inizializzazione tramite JavaScript       | X      |                  |          |             |                                                                                                                      |
| Inizializzazione tramite tag video        |        | X                |          |             |                                                                                                                      |
| Indirizzamento segmento - Basato sul tempo         | X      |                  |          |             |                                                                                                                      |
| Indirizzamento segmento - Basato su indice        |        |                  |          | X           |                                                                                                                      |
| Indirizzamento segmento - Basato su byte         |        |                  |          | X           |                                                                                                                      |
| Rewriter URL di Servizi multimediali di AzureAzure Media Services URL rewriter       |        | X                |          |             |                                                                                                                      |
| Accessibilità - Sottotitoli  |        | X                |          |             |  WebVTT supportato per il CEA 708 live parzialmente testato                                                       |
| Accessibilità - Tasti di scelta rapida                 | X      |                  |          |             |                                                                                                                      |
| Accessibilità - Contrasto elevato           |        | X                |          |             |                                                                                                                      |
| Accessibilità - Tab Focus               |        | X                |          |             |                                                                                                                      |
| Messaggi di errore                         |        | X                |          |             | I messaggi di errore sono incoerenti tra i tecnici                                                                         |
| Attivazione di eventi                        | X      |                  |          |             |                                                                                                                      |
| Diagnostica                             |        | X                |          |             | Le informazioni di diagnostica sono disponibili solo nella tecnologia AzureHtml5JS e parzialmente disponibili nella tecnologia SilverlightSS. |
| Ordine tecnico personalizzabile                 |        | X                |          |             |                                                                                                                      |
| Euristica - Base                      | X      |                  |          |             |                                                                                                                      |
| Euristica - Personalizzazione              |        |                  | X        |             | La personalizzazione è disponibile solo con la tecnologia AzureHtml5JS.Customization is only available with the AzureHtml5JS tech.                                                          |
| Discontinuità                         | X      |                  |          |             |                                                                                                                      |
| Selezionare Bitrate                          | X      |                  |          |             | Questa API è disponibile solo nei tecnici AzureHtml5JS e FlashSS.This API is only available on the AzureHtml5JS and FlashSS techs.                                                    |
| Flusso multi-audio                      |        | X                |          |             | L'opzione audio a livello di codice è supportata nei tecnici AzureHtml5JS e FlashSS ed è disponibile tramite la selezione dell'interfaccia utente in AzureHtml5JS, FlashSS e Html5 nativo (in Safari).  La maggior parte delle piattaforme richiede gli stessi dati privati del codec per passare da un flusso audio all'altro (stesso codec, canale, frequenza di campionamento e così via). |
| Localizzazione dell'interfaccia utenteUI Localization                         |        | X                |          |             |                                                                                                                      |
| Riproduzione a più istanze                 |        |                  |          | X           | Questo scenario può funzionare per alcuni tecnici, ma è attualmente non supportato e non testato. Si può anche ottenere questo per lavorare utilizzando iframe |
| Supporto per gli annunci                             |        | x                |          |             | AMP supporta l'inserimento di annunci lineari pre-medio e post-roll da server ad ad di tipo VAST compatibili con VOD nella tecnologia AzureHtml5JS |
| Analytics                               |        | X                |          |             | AMP offre la possibilità di ascoltare gli eventi di analisi e diagnostica per inviare a un back-end di Analytics di tua scelta.  Tutti gli eventi e le proprietà non sono disponibili in tutte le tecnologie a causa delle limitazioni della piattaforma.                                                                            |
| Skin personalizzati                            |        |                  | X        |             | Trasforma i controlli di impostazione su false in AMP e usa il tuo HTML e CSS.           |
| Cerca la barra di scorrimento                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play                              |        |                  |          | X           |                                                                                                                      |
| Solo audio                              |        |                  |          | X           | Potrebbe funzionare in alcuni tecnici per lo streaming adattivo, ma non è attualmente supportato e non funziona in AzureHtml5JS.May work in some techs for Adaptive Streaming but is currently not supported and does not work in AzureHtml5JS. La riproduzione MP3 progressiva può funzionare con la tecnologia HTML5 se la piattaforma lo supporta.                                                                                                        |
| Solo video                              |        |                  |          | X           | Potrebbe funzionare in alcuni tecnici per lo streaming adattivo, ma non è attualmente supportato e non funziona in AzureHtml5JS.May work in some techs for Adaptive Streaming but is currently not supported and does not work in AzureHtml5JS.      |
| Presentazione multiperiodo               |        |                  |          | X                                                                                                                                  |
| Angoli di ripresa multipli                  |        |                  |          | X           |                                                                                                                      |
| Velocità di riproduzione                          |        | X                |          |             | La velocità di riproduzione è supportata nella maggior parte degli scenari tranne che nel caso dei dispositivi mobili a causa di un bug parziale in Chrome                 |

## <a name="next-steps"></a>Passaggi successivi ##
- [Azure Media Player Quickstart](azure-media-player-quickstart.md)