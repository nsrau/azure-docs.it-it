---
title: Registrazione video-Azure
description: Nel contesto di un sistema di gestione video per fotocamere CCTV, la registrazione video si riferisce al processo di acquisizione di video dalle fotocamere e alla relativa registrazione per la visualizzazione tramite app per dispositivi mobili e browser. La registrazione video può essere categorizzata come registrazione video continua e registrazione video basata su eventi.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c03472f577e67d5b2705a2ebe3f53e85e0849819
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260194"
---
# <a name="video-recording"></a>Registrazione di video

Nel contesto di un sistema di gestione video per fotocamere CCTV, la registrazione video si riferisce al processo di acquisizione di video dalle fotocamere e alla relativa registrazione per la visualizzazione tramite app per dispositivi mobili e browser. La registrazione video può essere categorizzata come registrazione video continua e registrazione video basata su eventi. 

## <a name="continuous-video-recording"></a>Registrazione continua di video  

La registrazione video continua (CVR) si riferisce al processo di registrazione continua di tutti i video acquisiti da un'origine video. CVR garantisce che tutto il video sia disponibile (dettato dal [criterio di registrazione](#recording-policy)) per analizzare e/o controllare in un secondo momento.

## <a name="event-based-video-recording"></a>Registrazione di video basata su eventi  

Per registrazione video basata su eventi (EVR) si intende il processo di registrazione video attivato da un evento. L'evento in questione potrebbe avere origine a causa dell'elaborazione del segnale video stesso o può provenire da un'origine indipendente (ad esempio, da un sensore della porta). La registrazione video basata su eventi (EVR) può comportare un risparmio di spazio di archiviazione, ma richiede componenti aggiuntivi che generano gli eventi e attivano la registrazione video (per un criterio predefinito). In altre parole, EVR richiede decisioni aggiuntive riguardanti gli eventi che devono attivare la registrazione video e i criteri associati alla registrazione video, nota anche come criterio di registrazione. Un esempio di criterio potrebbe essere simile al seguente: registrare il video per 2 minuti a partire da 30 secondi prima dell'ora dell'evento. Gli eventi in questione potrebbero avere origine a causa dell'elaborazione video sulla fotocamera stessa. Ad esempio, diverse fotocamere supportano la generazione di un evento del segnale di rilevamento del movimento quando viene rilevato un movimento all'interno di una zona preconfigurata di interesse nel viewport della fotocamera. Gli eventi possono essere generati anche elaborando il video in un altro dispositivo che acquisisce il video dalla fotocamera e lo analizza usando semplici tecniche di elaborazione di immagini o modelli di apprendimento automatico sofisticati. 

## <a name="choosing-recording-modes"></a>Scelta delle modalità di registrazione  

La scelta di utilizzare CVR o EVR dipende dagli obiettivi aziendali. Analisi video in tempo reale su IoT Edge offre funzionalità della piattaforma sia per CVR che per EVR. Per altre informazioni, vedere gli articoli dello scenario [CVR](continuous-video-recording-concept.md) e [EVR](event-based-video-recording-concept.md) .

## <a name="recording-policy"></a>Criteri di registrazione  

I criteri di registrazione fanno riferimento ai criteri che stabiliscono l'ora di avvio/arresto della registrazione (in caso di EVR), la durata della registrazione e l'eliminazione della registrazione. I criteri di registrazione consentono di bilanciare i costi di archiviazione con i requisiti aziendali. I criteri di registrazione sono diversi tra CVR e EVR. Per CVR, i criteri di registrazione definiscono il numero di giorni di archiviazione del video (ad esempio, gli ultimi 7 giorni). Per EVR, i criteri di registrazione definiscono quando deve iniziare e terminare la registrazione, insieme alla durata del video. Per altre informazioni, vedere gli articoli dello scenario [CVR](continuous-video-recording-concept.md) e [EVR](event-based-video-recording-concept.md) .

## <a name="next-steps"></a>Passaggi successivi

* [Rilevare il movimento, registrare videoclip in Servizi multimediali di Azure](detect-motion-record-video-clips-media-services-quickstart.md)
* [Rilevare il movimento, registrare i clip video nei dispositivi perimetrali](detect-motion-record-video-clips-edge-devices-quickstart.md)

