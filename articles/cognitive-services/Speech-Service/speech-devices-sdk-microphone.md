---
title: Consigli per l'array di microfoni di Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Consigli per l'array di microfoni di Speech Devices SDK. Queste geometrie di matrice sono consigliate per l'utilizzo con Microsoft Audio Stack.These array geometries are recommended for use with the Microsoft Audio Stack.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: a87bdd7a55036e8b70f0bc5816d2b587c1569202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168134"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Suggerimenti per l'array di microfoni di Speech Devices SDK

In questo articolo imparerai a progettare un array di microfoni per l'SDK dei dispositivi di riconoscimento vocale.

L'SDK dei dispositivi di riconoscimento vocale funziona meglio con un array di microfoni progettato in base alle linee guida seguenti, tra cui la geometria del microfono e la selezione dei componenti. Vengono inoltre fornite indicazioni sull'integrazione e considerazioni elettriche.

## <a name="microphone-geometry"></a>Geometria del microfono

Le geometrie di matrice seguenti sono consigliate per l'utilizzo con Microsoft Audio Stack. La posizione delle sorgenti audio e il rifiuto del rumore ambientale sono migliorati con un maggior numero di microfoni con dipendenze da applicazioni specifiche, scenari utente e il fattore di forma del dispositivo.

|     | Serie circolare |     | Serie lineare |     |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \#Microfoni | 7 | 4 | 4 | 2 |
| Geometry | 6 Esterno, 1 Centro, Raggio - 42,5 mm, Spaziati uniformemente | 3 Esterno, 1 Centro, Raggio - 42,5 mm, Spaziato uniformemente | Lunghezza : 120 mm, Spaziatura - 40 mm | Spaziatura: 40 mm |

I canali del microfono devono essere ordinati in base alla numerazione raffigurata per ogni array precedente, aumentando da 0. Lo stack audio Microsoft richiederà un flusso di riferimento aggiuntivo di riproduzione audio per eseguire l'annullamento dell'eco.

## <a name="component-selection"></a>Selezione dei componenti

I componenti del microfono devono essere selezionati per riprodurre con precisione un segnale privo di rumore e distorsione.

Le proprietà consigliate per la selezione dei microfoni sono:

| Parametro | Consigliato |
| --------- | ----------- |
| Snr | \>65 dB (1 kHz segnale 94 dBSPL, rumore ponderato A) |
| Corrispondenza ampiezza | 1 dB - 1 kHz |
| Corrispondenza di fase | 2o 1 kHz |
| Punto di sovraccarico acustico (AOP) | \>120 dBSPL (THD - 10%) |
| Velocità in bit | Minimo 24 bit |
| Frequenza di campionamento | Minimo 16 kHz\* |
| Risposta di frequenza | - Maschera mobile da 3 dB, 200-8000 Hz\* |
| Affidabilità | Intervallo di temperatura di immagazzinamento da -40 a 70 gradi centigradi<br />Gamma di temperatura di funzionamento da -20 a 55 gradi centigradi |

\*_Frequenze di campionamento più elevate o intervalli di frequenza "più larghi" possono essere necessari per le applicazioni di comunicazione di alta qualità (VoIP)_

Una buona selezione dei componenti deve essere abbinata a una buona integrazione elettroacustica per evitare di compromettere le prestazioni dei componenti utilizzati. Casi d'uso unici possono anche richiedere requisiti aggiuntivi (ad esempio: intervalli di temperatura di funzionamento).

## <a name="microphone-array-integration"></a>Integrazione dell'array del microfono

Le prestazioni dell'array di microfoni quando integrato in un dispositivo sarà diverso da quello del componente. È importante assicurarsi che i microfoni siano ben abbinati dopo l'integrazione. Pertanto le prestazioni del dispositivo misurate dopo qualsiasi guadagno fisso o EQ devono soddisfare le seguenti raccomandazioni:

| Parametro          | Consigliato                                        |
| ------------------ | -------------------------------------------------- |
| Snr                | \>63 dB (1 kHz segnale 94 dBSPL, rumore ponderato A) |
| Sensibilità all'uscita | -26 dBFS/Pa - 1 kHz (consigliato)                  |
| Corrispondenza ampiezza | 2 dB, 200-8000 Hz                                |
| THD%\*             | 1%, 200-8000 Hz, 94 dBSPL, 5o Ordine             |
| Risposta di frequenza | - Maschera mobile da 6 dB, 200-8000 Hz\*\*              |

\*\*_Per misurare il THD (ad esempio Neumann KH120) è necessario un altoparlante a bassa distorsione_

\*\*_Per le applicazioni di comunicazione di alta qualità (VoIP) possono essere necessarie intervalli di frequenza "più ampi"_

## <a name="speaker-integration-recommendations"></a>Raccomandazioni per l'integrazione degli oratori

Poiché l'annullamento dell'eco è necessario per i dispositivi di riconoscimento vocale che contengono altoparlanti, vengono forniti suggerimenti aggiuntivi per la selezione e l'integrazione degli altoparlanti.

| Parametro | Consigliato |
| --------- | ----------- |
| Considerazioni sulla linearità | Nessuna elaborazione non lineare dopo il riferimento dell'altoparlante, altrimenti è necessario un flusso di riferimento loopback basato su hardware |
| Loopback degli altoparlanti | Fornito tramite WASAPI, API private, plug-in ALSA personalizzato (Linux) o fornito tramite canale firmware |
| THD% | 3a Banda di ottava minima 5a, 70 dBA Riproduzione - 0,8 m - 6,3%, 315-500 Hz - 5%, 630-5000 Hz |
| Accoppiamento dell'eco ai microfoni | \>-10 dB TCLw con metodo ITU-T G.122 Annex B.4, normalizzato a livello del microfono<br />TCLw - TCLwmeasured (Livello misurato \+ - Target Output Sensitivity)<br />TCLw - TCLwmeasured \+ (Livello misurato - (-26)) |

## <a name="integration-design-architecture"></a>Architettura di progettazione dell'integrazione

Quando si integrano microfoni in un dispositivo, sono necessarie le seguenti linee guida per l'architettura:

| Parametro | Recommendation |
| --------- | -------------- |
| Somiglianza porta mic | Tutte le porte del microfono sono della stessa lunghezza nell'array |
| Dimensioni porta microfono | Dimensioni della porta: 0,8-1,0 mm. Lunghezza porta / \< Diemetro porta 2 |
| Sigillamento del microfono         | Guarnizioni di sigillamento uniformemente implementate in stack-up. Raccomandare \> il rapporto di compressione del 70% per le guarnizioni in schiuma |
| Affidabilità del microfono     | La rete deve essere utilizzata per prevenire la polvere e l'ingresso (tra PCB per microfoni con portamento inferiore e guarnizione guarnizione/copertura superiore) |
| Isolamento del microfono       | Guarnizioni in gomma e disaccoppiamento delle vibrazioni attraverso la struttura, in particolare per isolare eventuali percorsi di vibrazione grazie a altoparlanti integrati |
| Orologio di campionamento      | L'audio del dispositivo deve essere privo di jitter e drop-out con deriva bassa |
| Capacità di registrazione   | Il dispositivo deve essere in grado di registrare contemporaneamente i flussi raw dei singoli canali |
| USB                 | Tutti i dispositivi di input audio USB devono impostare i descrittori in base alle [specifiche Rev3](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) dei dispositivi audio USB |
| Geometria del microfono | I driver devono implementare correttamente i descrittori della [geometria dell'array di microfoni](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) |
| Individuabilità     | I dispositivi non devono avere hardware, firmware o algoritmi di elaborazione audio non lineari basati su software di terze parti da/verso il dispositivo |
| Formato di acquisizione      | I formati di acquisizione devono utilizzare una frequenza di campionamento minima di 16 kHz e una profondità di 24 bit consigliata |

## <a name="electrical-architecture-considerations"></a>Considerazioni sull'architettura elettrica

Se applicabile, gli array possono essere collegati a un host USB (ad esempio un SoC che esegue Microsoft Audio Stack) e interfacce ai servizi di riconoscimento vocale o ad altre applicazioni.

I componenti hardware come la conversione da PDM a TDM devono garantire che l'intervallo dinamico e la SNR dei microfoni siano conservati all'interno di re-campionatori.

La classe audio USB 2.0 ad alta velocità deve essere supportata all'interno di qualsiasi MCU audio al fine di fornire la larghezza di banda necessaria per un massimo di sette canali a velocità di campionamento e profondità di bit più elevate.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ulteriori informazioni sull'SDK dei dispositivi di riconoscimento vocale](speech-devices-sdk.md)
