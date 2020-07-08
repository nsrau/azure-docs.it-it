---
title: Elenco di funzionalità Azure Media Player
description: Riferimento alla funzionalità per Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81727229"
---
# <a name="feature-list"></a>Elenco di funzionalità #
Di seguito è riportato l'elenco delle funzionalità testate e delle funzionalità non supportate:

|                                         | TESTATO | PARZIALMENTE TESTATO | NON testato | NON supportato | NOTE                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| Riproduzione                                |        |                  |          |             |                                                                                                                      |
| Riproduzione su richiesta di base                | X      |                  |          |             | Supporta solo flussi da servizi multimediali di Azure                                                                      |
| Riproduzione live di base                     | X      |                  |          |             | Supporta solo flussi da servizi multimediali di Azure                                                                      |
| AES                                     | X      |                  |          |             | Supporta il servizio di distribuzione delle chiavi di servizi multimediali di Azure                                                                   |
| DRM multiplo                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Supporta il servizio di distribuzione delle chiavi di servizi multimediali di Azure                                                                   |
| Widevine                                |        | X                |          |             | Supporta le caselle Widevine PSSH delineate nel manifesto                                                                    |
| FairPlay                                |        | X                |          |             | Supporta il servizio di distribuzione delle chiavi di servizi multimediali di Azure                                                                   |
| Tecnici                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Fallback Flash (Flash)                | X      |                  |          |             | Non tutte le funzionalità sono disponibili in questa tecnologia.                                                                         |
| Silverlight fallback Silverlight      | X      |                  |          |             | Non tutte le funzionalità sono disponibili in questa tecnologia.                                                                         |
| Pass-through HLS nativo (HTML5)         |        | X                |          |             | Non tutte le funzionalità sono disponibili in questa tecnologia a causa di restrizioni della piattaforma.                                            |
| Funzionalità                                |        |                  |          |             |                                                                                                                      |
| Supporto dell'API                             | X      |                  |          |             | Vedere l'elenco dei problemi noti                                                                                                |
| Interfaccia utente di base                                | X      |                  |          |                                                                                                                                    |
| Inizializzazione tramite JavaScript       | X      |                  |          |             |                                                                                                                      |
| Inizializzazione tramite tag video        |        | X                |          |             |                                                                                                                      |
| Indirizzamento del segmento-basato sul tempo         | X      |                  |          |             |                                                                                                                      |
| Indirizzamento del segmento-basato sull'indice        |        |                  |          | X           |                                                                                                                      |
| Indirizzamento segmento-basato su byte         |        |                  |          | X           |                                                                                                                      |
| Rewriter URL di servizi multimediali di Azure       |        | X                |          |             |                                                                                                                      |
| Accessibilità-didascalie e sottotitoli  |        | X                |          |             |  WebVTT supportato per su richiesta, Live CEA 708 parzialmente testato                                                       |
| Accessibilità-tasti di scelta rapida                 | X      |                  |          |             |                                                                                                                      |
| Accessibilità-Contrasto elevato           |        | X                |          |             |                                                                                                                      |
| Accessibilità-scheda stato attivo               |        | X                |          |             |                                                                                                                      |
| Messaggistica degli errori                         |        | X                |          |             | I messaggi di errore sono incoerenti tra le tecnologie                                                                         |
| Attivazione di eventi                        | X      |                  |          |             |                                                                                                                      |
| Diagnostica                             |        | X                |          |             | Le informazioni di diagnostica sono disponibili solo in AzureHtml5JS Tech e sono parzialmente disponibili su Silverlights Tech. |
| Ordine tecnico personalizzabile                 |        | X                |          |             |                                                                                                                      |
| Euristica-di base                      | X      |                  |          |             |                                                                                                                      |
| Euristica-personalizzazione              |        |                  | X        |             | La personalizzazione è disponibile solo con la tecnologia AzureHtml5JS.                                                          |
| Discontinuità                         | X      |                  |          |             |                                                                                                                      |
| Selezione velocità in bit                          | X      |                  |          |             | Questa API è disponibile solo per le Tech AzureHtml5JS e flashing.                                                    |
| Flusso multiaudio                      |        | X                |          |             | Il Commuter audio a livello di codice è supportato in AzureHtml5JS e i tecnici Flash ed è disponibile tramite la selezione dell'interfaccia utente in AzureHtml5JS, Flashing e HTML5 nativo (in Safari).  Per la maggior parte delle piattaforme sono necessari gli stessi dati privati dei codec per cambiare i flussi audio (stesso codec, canale, frequenza di campionamento e così via). |
| Localizzazione dell'interfaccia utente                         |        | X                |          |             |                                                                                                                      |
| Riproduzione a istanze diverse                 |        |                  |          | X           | Questo scenario può funzionare per alcuni tecnici ma non è attualmente supportato e non testato. Questa operazione può essere eseguita anche con gli iframe |
| Supporto per ADS                             |        | x                |          |             | AMP supporta l'inserimento di annunci lineari precedenti a metà e post-roll da server ad di grandi conformi per VOD in AzureHtml5JS Tech |
| Analytics                               |        | X                |          |             | AMP consente di ascoltare le analisi e gli eventi di diagnostica per inviare un back-end di analisi di propria scelta.  Tutti gli eventi e le proprietà non sono disponibili in tecnologie a causa di limitazioni della piattaforma.                                                                            |
| Interfacce personalizzate                            |        |                  | X        |             | Impostare i controlli su false in AMP e usando il codice HTML e CSS.           |
| Pulitura della barra di ricerca                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play                              |        |                  |          | X           |                                                                                                                      |
| Solo audio                              |        |                  |          | X           | Può funzionare in alcuni tech per lo streaming adattivo, ma non è attualmente supportato e non funziona in AzureHtml5JS. La riproduzione in formato MP3 progressivo può funzionare con la tecnologia HTML5 se supportata dalla piattaforma.                                                                                                        |
| Solo video                              |        |                  |          | X           | Può funzionare in alcuni tech per lo streaming adattivo, ma non è attualmente supportato e non funziona in AzureHtml5JS.      |
| Presentazione multiperiodo               |        |                  |          | X                                                                                                                                  |
| Più angoli della fotocamera                  |        |                  |          | X           |                                                                                                                      |
| Playback Speed                          |        | X                |          |             | La velocità di riproduzione è supportata nella maggior parte degli scenari, ad eccezione del caso dei dispositivi mobili a causa di un bug parziale in Chrome                 |

## <a name="next-steps"></a>Passaggi successivi ##
- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)