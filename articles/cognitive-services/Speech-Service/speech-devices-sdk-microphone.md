---
title: Riconoscimento vocale Devices SDK microfono array le raccomandazioni - servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Indicazioni di matrice di riconoscimento vocale Devices SDK microfono. Le geometrie di matrice seguenti sono consigliate per l'uso con lo Stack Audio di Microsoft. Percorso delle origini audio e rifiuto dei rumore ambientale risultano migliorate con un numero maggiore di microfoni con dipendenze su applicazioni specifiche, gli scenari utente e il form factor del dispositivo.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 63dd64e900cf68e708032569ca75ac2e8b221491
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236997"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Consigli di vocale dispositivi SDK microfono array

In questo articolo descrive come progettare una matrice di microfono per il SDK di dispositivi di riconoscimento vocale.

il SDK di dispositivi di riconoscimento vocale funziona meglio con una matrice di microfono è stata progettata in base alle linee guida seguenti, tra cui la selezione di geometria e componente microfono. Si forniscono indicazioni anche su considerazioni elettriche e di integrazione.

## <a name="microphone-geometry"></a>Geometria microfono

Le geometrie di matrice seguenti sono consigliate per l'uso con lo Stack Audio di Microsoft. Percorso delle origini audio e rifiuto dei rumore ambientale risultano migliorate con un numero maggiore di microfoni con dipendenze su applicazioni specifiche, gli scenari utente e il form factor del dispositivo.

|          | Matrice circolare    |       |  Matrice lineare              |                |
|----------|-------------------|-------------------|----------------|----------------|
|          |<img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/>|<img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/>|
| \# Microfoni  | 7                 | 4                 | 4              | 2              |
| Geometria | 6 1 outer, centro, raggio = mm 42,5, uniformemente distribuite| 3 1 outer, centro, raggio = mm 42,5, uniformemente distribuite | Lunghezza = 120 mm, spaziatura = 40 mm | Spaziatura = 40 mm |

I canali di microfono devono essere ordinati in base alla numerazione descritto per ciascuno di sopra di matrice, crescenti da 0.  Lo Stack Audio Microsoft richiederà un flusso di riferimento aggiuntivo di riproduzione audio per eseguire l'annullamento echo.

## <a name="component-selection"></a>Selezione dei componenti

È necessario selezionare i componenti di microfono per riprodurre con precisione un segnale prive di rumore e una distorsione.

Le proprietà consigliate quando si seleziona microfoni sono:

| Parametro                         | Consigliati                       |
|-----------------------------------|-----------------------------------|
| SNR                               | \> 65 dB (1 kHz segnale 94 dBSPL, rumore ponderato)   |
| Corrispondenza di Amplitude                | + 1 dB @ 1 kHz                     |
| Fase di corrispondenza                    | ± 2° @ 1 kHz                       |
| Punto di Overload acustici (programmazione orientata ad aspetti)     | \> 120 dBSPL (THD = 10%)          |
| Velocità in bit                          | Almeno 24 bit                    |
| Frequenza di campionamento                     | Minimo kHz 16\*                   |
| Direzione                       | Omnidirezionale                   |
| Risposta in frequenza                | + 3 dB, 200 8000 Hz mobile maschera\*|
| Affidabilità                       | Intervallo di temperatura archiviazione-40 ° C a 70 ° C<br />Temperatura operativa compresa tra 20 ° C-55 ° C  |

*\*La velocità di campionamento o intervalli di frequenza "larghi" potrebbero essere necessari per le applicazioni di alta qualità delle comunicazioni (VoIP)*

Selezione componenti ottimale deve essere abbinato con una buona integrazione electroacoustic per evitare compromettono di conseguenza le prestazioni dei componenti usati. Casi d'uso univoco può anche richiedere requisiti aggiuntivi (ad esempio: operativo intervalli temperatura).

## <a name="microphone-array-integration"></a>Integrazione di microfono array

Le prestazioni delle matrici quando integrate in un dispositivo e dopo qualsiasi miglioramento fisso o EQ devono soddisfare i requisiti seguenti:

|  Parametro        |    Consigliati |
|--------------------|----------------------------------------------------|
|  SNR                 | \> 65 dB (1 kHz segnale 94 dBSPL, rumore ponderato) |
|  Sensibilità di output  | -26 dBFS/Pa @ 1 kHz (scelta consigliata) |
|  Corrispondenza di Amplitude  | ± 2 dB, 200-8000 Hz |
|  Fase di corrispondenza      | + 5°, 200 8000 Hz |
| THD%                 | ≤ 1%, 200 8000 Hz, dBSPL 94, 5th ordine |
|  Risposta in frequenza  | + 6 dB, 200 8000 Hz mobile maschera\* |

*\*Gli intervalli di frequenza "Larghi" potrebbero essere necessari per le applicazioni di alta qualità delle comunicazioni (VoIP)*

## <a name="speaker-integration-recommendations"></a>Consigli di integrazione del relatore

Eco è necessaria per i dispositivi di riconoscimento vocale che contengono gli altoparlanti, vengono forniti consigli aggiuntivi per l'integrazione e la selezione del relatore.

| Parametro                         | Consigliati                       |
|-----------------------------------|-----------------------------------|
| Considerazioni di linearità          | Non lineari dopo il riferimento di relatore, in caso contrario, un flusso di riferimento basati su hardware loopback è richiesto un'elaborazione  |
| Loopback dell'altoparlante                  | Fornito tramite WASAPI, privata API, personalizzato ALSA plug-in (Linux) o fornita tramite un canale del firmware      |
| THD%                              | 3 ° ottava minimo 5 ordine, 70 dBA riproduzione @ m 0,8 ≤ 6.3%, fino a 500 315 Hz ≤ 5%, 630 5000 Hz                 |
| Accoppiamento echo per microfoni      | \> dB-10 TCLw utilizzando il metodo ITU-T G.122 allegato B.4, normalizzato in base al livello mic<br />TCLw = TCLwmeasured \+ (misurazione - destinazione Output sensibilità)<br />TCLw = TCLwmeasured \+ (misurata livello --(26)) |

## <a name="integration-design-architecture"></a>Architettura di progettazione di integrazione

Le seguenti linee guida per l'architettura sono necessari quando si integrano microfoni in un dispositivo:

| Parametro                         | Recommendation                    |
|-----------------------------------|-----------------------------------|
| Somiglianza porta MIC               | Tutte le porte microfono siano la stessa lunghezza nella matrice    |
| Quote a MIC porta               | Porta dimensioni Ø0.8 1.0 mm. Porta lunghezza porta diametro / \< 2              |
| Sigillo MIC                       | Come rendere sealed giunti implementati in modo uniforme in stack di backup. È consigliabile \> rapporto di compressione 70% per giunti schiuma     |
| Affidabilità MIC                   | Mesh deve essere usato per impedire la polvere e traffico in ingresso (tra PCB per inferiore trasferita microfoni e sigillo cover guarnizione o superiore)  |
| Isolamento del MIC                     | Giunti elastica e il disaccoppiamento vibrazione nella struttura, in particolare per isolare i percorsi di vibrazione a causa di relatori integrate      |
| Orologio di campionamento                    | Dispositivo audio deve avere prive di instabilità e drop-out con sfasamento bassa    |
| Funzionalità di record                 | Il dispositivo deve essere in grado di registrare i flussi non elaborati di singolo canale contemporaneamente |
| USB                               | Tutti i dispositivi di input audio USB necessario impostare i descrittori in base al [specifica di Rev3 dispositivi Audio USB](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Geometria microfono               | I driver devono implementare [microfono Array Geometry descrittori](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) correttamente  |
| Individuabilità                   | Dispositivi non devono contenere qualsiasi hardware non controllabile o non rilevabili, il firmware o 3rd algoritmi di elaborazione audio non lineare basata su software di terze parti da e verso il dispositivo|
| Acquisizione di formato                    | Formati di acquisizione devono usare una frequenza di campionamento minima di kHz 16 e la profondità di 24 bit consigliata      |

## <a name="electrical-architecture-considerations"></a>Considerazioni sull'architettura elettrica

Dove applicabile, le matrici possono essere connesso a un host USB (ad esempio un SoC che viene eseguito lo Stack Audio Microsoft) e le interfacce da altre applicazioni o servizi di riconoscimento vocale.

Componenti hardware, ad esempio la conversione da PDM-TDM devono verificare che l'intervallo dinamico e SNR dei microfoni venga mantenuta all'interno di campionatori nuovamente.

Ad alta velocità USB 2.0 classe Audio deve essere supportato all'interno di qualsiasi MCU audio per fornire la larghezza di banda necessaria per un massimo di sette canali frequenze di campionamento e intensità del bit.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sui dispositivi Speech SDK](speech-devices-sdk.md)
