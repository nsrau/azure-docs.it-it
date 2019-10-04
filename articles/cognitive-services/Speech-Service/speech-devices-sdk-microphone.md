---
title: Suggerimenti per i dispositivi vocali SDK per array microfoni-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Suggerimenti per i dispositivi vocali SDK per array di microfoni. Le seguenti geometrie di matrice sono consigliate per l'uso con lo stack audio Microsoft. Il percorso delle origini audio e il rifiuto del rumore di ambiente sono migliorati con un numero maggiore di microfoni con dipendenze da applicazioni specifiche, scenari utente e il fattore di forma del dispositivo.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: b110e9ddc42d07c8356eb25b9ea2294e470d5fdc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558957"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Suggerimenti per i dispositivi vocali SDK per array microfoni

In questo articolo si apprenderà come progettare un array di microfoni per i dispositivi vocali SDK.

L'SDK per dispositivi vocali funziona meglio con una matrice di microfoni progettata in base alle linee guida seguenti, tra cui la geometria del microfono e la selezione dei componenti. Il materiale sussidiario viene fornito anche per considerazioni sull'integrazione e sull'elettricità.

## <a name="microphone-geometry"></a>Geometria microfono

Le seguenti geometrie di matrice sono consigliate per l'uso con lo stack audio Microsoft. Il percorso delle origini audio e il rifiuto del rumore di ambiente sono migliorati con un numero maggiore di microfoni con dipendenze da applicazioni specifiche, scenari utente e il fattore di forma del dispositivo.

|          | Matrice circolare    |       |  Matrice lineare              |                |
|----------|-------------------|-------------------|----------------|----------------|
|          |<img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/>|<img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/>|
| \#Microfoni  | 7                 | 4                 | 4              | 2              |
| Geometria | 6 esterno, 1 centro, raggio = 42,5 mm, spazio uniforme| 3 esterno, 1 centro, raggio = 42,5 mm, spazio uniforme | Lunghezza = 120 mm, spaziatura = 40 mm | Spaziatura = 40 mm |

I canali del microfono devono essere ordinati in base alla numerazione raffigurata per ogni matrice precedente, aumentando da 0.  Lo stack audio Microsoft richiederà un flusso di riferimento aggiuntivo per la riproduzione audio per eseguire l'annullamento Echo.

## <a name="component-selection"></a>Selezione componenti

È necessario selezionare i componenti del microfono per riprodurre accuratamente un segnale senza rumore e distorsione.

Le proprietà consigliate quando si selezionano i microfoni sono:

| Parametro                         | Consigliato                       |
|-----------------------------------|-----------------------------------|
| SNR                               | \>= 65 dB (1 kHz Signal 94 dBSPL, A-weighted Noise)   |
| Corrispondenza dell'ampiezza                | ± 1 dB a 1 kHz                     |
| Corrispondenza fase                    | ± 2 ° @ 1 kHz                       |
| Punto di sovraccarico acustico (AOP)     | \>= 120 dBSPL (THD = 10%)          |
| Velocità in bit                          | Minimo 24 bit                    |
| Frequenza di campionamento                     | Minimo 16 kHz\*                   |
| Risposta frequenza                | ± 3 dB, 200-8000 Hz a maschera mobile\*|
| Affidabilità                       | Intervallo di temperatura di archiviazione: da 40 a 70 ° c<br />Intervallo temperatura operativa-da 20 a 55 ° c  |

*\*È possibile che siano necessarie frequenze di campionamento maggiori o intervalli di frequenza "più ampi" per le applicazioni di comunicazione di alta qualità (VoIP)*

Per evitare di compromettere le prestazioni dei componenti usati, è necessario associare una selezione di componenti ottimali con una corretta integrazione con elettroacustica. I casi di utilizzo univoci possono richiedere requisiti aggiuntivi (ad esempio, intervalli di temperatura operativi).

## <a name="microphone-array-integration"></a>Integrazione di array di microfoni

Le prestazioni dell'array di microfoni quando integrato in un dispositivo si differenziano dalla specifica del componente. È importante assicurarsi che i microfoni siano abbinati correttamente dopo l'integrazione. Pertanto, le prestazioni del dispositivo misurate dopo qualsiasi guadagno fisso o EQ devono soddisfare le raccomandazioni seguenti:

|  Parametro        |    Consigliato |
|--------------------|----------------------------------------------------|
|  SNR                 | \>63 dB (1 kHz Signal 94 dBSPL, A-weighted Noise) |
|  Sensibilità output  | -26 dBFS/PA a 1 kHz (scelta consigliata) |
|  Corrispondenza dell'ampiezza  | ± 2 dB, 200-8000 Hz |
| THD\*                 | ≤ 1%, 200-8000 Hz, 94 dBSPL, 5a ordine |
|  Risposta frequenza  | ± 6 dB, 200-8000 Hz a maschera mobile\*\* |

*\*\*È necessario un altoparlante a bassa distorsione per misurare THD, ad esempio Neumann KH120*

*\*\*Gli intervalli di frequenza "più ampi" possono essere necessari per le applicazioni di comunicazione di alta qualità (VoIP)*

## <a name="speaker-integration-recommendations"></a>Suggerimenti per l'integrazione di speaker

Poiché l'annullamento Echo è necessario per i dispositivi di riconoscimento vocale che contengono altoparlanti, vengono fornite indicazioni aggiuntive per la selezione e l'integrazione del relatore.

| Parametro                         | Consigliato                       |
|-----------------------------------|-----------------------------------|
| Considerazioni sulla linearità          | Nessuna elaborazione non lineare dopo il riferimento del relatore; in caso contrario, è necessario un flusso di riferimento loopback basato su hardware  |
| Loopback altoparlante                  | Fornito tramite WASAPI, le API private, il plug-in ALSA personalizzato (Linux) o fornito tramite il canale del firmware      |
| THD                              | 3 bande di ottave, minimo 5 ordine, 70 dBA riproduzione @ 0,8 m ≤ 6,3%, 315-500 Hz ≤ 5%, 630-5000 Hz                 |
| Accoppiamento Echo ai microfoni      | \>-10 TCLw di database usando il metodo ITU-T G. 122 Annex B. 4, normalizzato a livello MIC<br />TCLw = TCLwmeasured \+ (misura sensibile al livello di output di destinazione)<br />TCLw = TCLwmeasured \+ (livello misurato-(-26)) |

## <a name="integration-design-architecture"></a>Architettura di progettazione dell'integrazione

Le linee guida seguenti per l'architettura sono necessarie quando si integrano i microfoni in un dispositivo:

| Parametro                         | Recommendation                    |
|-----------------------------------|-----------------------------------|
| Somiglianza porta MIC               | Tutte le porte del microfono hanno la stessa lunghezza nella matrice    |
| Dimensioni porta MIC               | Dimensioni porta da Ø 0.8 a 1,0 mm. Lunghezza porta/diametro \< porta 2              |
| Sealing MIC                       | Guarnizioni sealing implementate in modo uniforme nello stack. Consiglia \> il rapporto di compressione del 70% per le guarnizioni di schiuma     |
| Affidabilità MIC                   | Mesh deve essere usato per evitare la polvere e il traffico in ingresso (tra i PCB per i microfoni con porta inferiore e la guarnizione di chiusura/copertura superiore)  |
| Isolamento MIC                     | Guarnizioni di gomma e separazione delle vibrazioni attraverso la struttura, in particolare per isolare i percorsi di vibrazione a causa di altoparlanti integrati      |
| Clock di campionamento                    | L'audio del dispositivo deve essere privo di jitter e drop-out con una bassa deviazione    |
| Funzionalità di registrazione                 | Il dispositivo deve essere in grado di registrare i singoli flussi RAW del canale simultaneamente |
| USB                               | Tutti i dispositivi di input audio USB devono impostare i descrittori in base alla [specifica rev3 dispositivi audio USB](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Geometria microfono               | I driver devono implementare correttamente i descrittori di [geometria della matrice microfonica](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry)  |
| Individuazione                   | I dispositivi non devono disporre di hardware, firmware o algoritmi di elaborazione audio non lineari basati su software di terze parti per/dal dispositivo|
| Formato di acquisizione                    | I formati di acquisizione devono usare una frequenza di campionamento minima di 16 kHz e la profondità consigliata a 24 bit      |

## <a name="electrical-architecture-considerations"></a>Considerazioni sull'architettura elettrica

Laddove applicabile, le matrici possono essere connesse a un host USB (ad esempio un SoC che esegue lo stack audio Microsoft) e le interfacce ai servizi vocali o ad altre applicazioni.

I componenti hardware, ad esempio la conversione da PDM a TDM, devono garantire che l'intervallo dinamico e l'SNR dei microfoni vengano conservati nei ricampionatori.

La classe audio USB ad alta velocità 2,0 dovrebbe essere supportata all'interno di qualsiasi MCU audio per fornire la larghezza di banda necessaria per un massimo di sette canali a frequenze di campionamento superiori e profondità dei bit.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sull'SDK dei dispositivi vocali](speech-devices-sdk.md)
