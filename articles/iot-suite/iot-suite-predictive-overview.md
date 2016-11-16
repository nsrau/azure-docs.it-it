---
title: Soluzione preconfigurata di manutenzione predittiva | Documentazione Microsoft
description: Una descrizione della soluzione preconfigurata di manutenzione predittiva di Azure IoT.
services: 
suite: iot-suite
documentationcenter: 
author: stevehob
manager: timlt
editor: 
ms.assetid: b370b3d7-2ce5-4906-9818-3aeedd471ee3
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2016
ms.author: araguila
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2d44af03b8e16a2bd936fc805ed4f0c4e6c5fbfc


---
# <a name="predictive-maintenance-preconfigured-solution-overview"></a>Panoramica della soluzione preconfigurata di manutenzione predittiva
La soluzione di *manutenzione predittiva* è una delle [soluzioni preconfigurate][lnk_preconfigured_solutions] rilasciate come parte di [Microsoft Azure IoT Suite][lnk_iot_suite]. Questa soluzione integra una raccolta di dati di telemetria in tempo reale dei dispositivi con un modello predittivo creato mediante [Azure Machine Learning][lnk_machine_learning].

Con Azure IoT Suite, le aziende possono connettersi agli asset e monitorarli, nonché analizzare i dati in tempo reale in modo semplice e rapido. La soluzione preconfigurata di manutenzione predittiva preleva i dati e utilizza dashboard e visualizzazioni completi per fornire alle aziende nuove informazioni con cui è possibile promuovere l'efficienza e aumentare i flussi di profitti.

## <a name="the-scenario"></a>Scenario
Fabrikam è una compagnia aerea locale che si concentra sulle esperienze eccezionali dei clienti a prezzi competitivi. Una causa di ritardi dei voli sono i problemi di manutenzione e la manutenzione dei motori degli aeromobili è particolarmente complessa. Guasti dei motori durante il volo devono essere evitati a tutti i costi, quindi Fabrikam controlla regolarmente i motori ed è conforme a un programma di manutenzione pianificata. Tuttavia, i motori non sempre si usurano allo stesso modo. Sui motori viene eseguita manutenzione superflua. Ancora più importante, si verificano problemi che possono forzare a terra un aereo fino a quando non viene eseguita la manutenzione. Questo causa ritardi costosi, specialmente se un aereo si trova in un luogo in cui tecnici esperti o parti di ricambio non sono disponibili.

I motori degli aeromobili Fabrikam sono instrumentati con sensori che controllano le condizioni del motore durante il volo. Fabrikam utilizza la Suite IoT di Azure per raccogliere i dati dei sensori raccolti durante il volo. Dopo aver accumulato anni di dati operativi e sui guasti dei motori, i ricercatori di dati di Fabrikam hanno modellato un modo per prevedere la vita utile rimanente (RUL) di un motore di aeromobile. Ciò che hanno riscontrato è una correlazione tra i dati provenienti da quattro dei sensori del motore e l'usura del motore che conduce a un eventuale guasto. Mentre Fabrikam continua a eseguire controlli periodici per garantire la sicurezza, ora può utilizzare i modelli per calcolare il RUL per ogni motore dopo ogni volo utilizzando i dati di telemetria raccolti dai motori durante il volo. Fabrikam può prevedere i futuri punti di malfunzionamento e pianificare la manutenzione e la riparazione in anticipo.

> [!NOTE]
> Il modello di soluzione usa dati di usura del motore effettivi.
> 
> 

Prevedendo il momento in cui sarà necessaria la manutenzione, Fabrikam può ottimizzare le proprie operazioni per ridurre i costi. I coordinatori della manutenzione lavorano con utilità di pianificazione per pianificare la manutenzione in coincidenza con la sosta di un aereo in un determinato luogo e garantire un tempo sufficiente in cui l’aeromobile sarà fuori servizio senza causare interruzioni della programmazione. Fabrikam può pianificare i tecnici di conseguenza, garantendo che gli aeromobili siano gestiti in modo efficiente senza tempi di attesa. I responsabili del controllo di inventario ricevono i piani di manutenzione, pertanto possono ottimizzare il processo di ordinazione e l'inventario delle parti di ricambio. Tutto ciò consente a Fabrikam di ridurre al minimo i tempo di fermo a terra degli aeromobili e di ridurre i costi operativi, garantendo la sicurezza dei passeggeri e dell'equipaggio.

Per comprendere in che modo [Azure IoT Suite][lnk_iot_suite] fornisce ai clienti le funzionalità necessarie per sfruttare le potenzialità della manutenzione predittiva, consultare questa [infografica][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-is-built"></a>Creazione della soluzione di manutenzione predittiva
Per visualizzare queste funzionalità partendo dai dati di telemetria del dispositivo raccolti attraverso i servizi di IoT Suite, la soluzione usa un modello esistente di Azure Machine Learning disponibile. Microsoft ha creato un [modello di regressione][lnk_regression_model] di un motore di aereo e ha pubblicato il modello completo, i dati<sup>\[1\]</sup> e le istruzioni dettagliate su come usare tale modello.

La soluzione preconfigurata di manutenzione predittiva di Azure IoT usa il modello di regressione creato da questo modello, viene distribuita nella sottoscrizione di Azure e viene esposta tramite un'API generata automaticamente. La soluzione include un subset di dati di test che rappresenta 4 (su un totale di 100) motori e 4 (su un totale di 21) flussi di dati dei sensori che forniscono un risultato esatto dal modello addestrato.

*\[1\] A. Saxena and K. Goebel (2008). "Turbofan Engine Degradation Simulation Data Set", NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli scenari di manutenzione predittiva basati su Azure IoT, leggere [Capture value from the Internet of Things][lnk_capture_value] (Acquisire valore dall'Internet delle cose).

Esaminare una [procedura dettagliata][lnk-predictive-walkthrough] della soluzione preconfigurata di manutenzione predittiva.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

È anche possibile esplorare alcune altre funzionalità delle soluzioni preconfigurate di IoT Suite:

* [Domande frequenti su IoT Suite][lnk-faq]
* [Sicurezza dell'Internet of Things sin dall'inizio][lnk-security-groundup]

[lnk-faq]: iot-suite-faq.md
[file lnk-security-groundup]: securing-iot-ground-up.md



<!--HONumber=Nov16_HO2-->


