---
title: Soluzione preconfigurata di manutenzione predittiva | Documentazione Microsoft
description: Una descrizione della soluzione preconfigurata di manutenzione predittiva di Azure IoT Suite.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: b370b3d7-2ce5-4906-9818-3aeedd471ee3
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: 8bad198488c4940a83eb32ec02122a91d47ca86c
ms.contentlocale: it-it
ms.lasthandoff: 07/24/2017

---
# <a name="predictive-maintenance-preconfigured-solution-overview"></a>Panoramica della soluzione preconfigurata di manutenzione predittiva

La soluzione preconfigurata di *manutenzione predittiva* è una delle [soluzioni preconfigurate][lnk_preconfigured_solutions] di [Microsoft Azure IoT Suite][lnk_iot_suite]. Questa soluzione integra una raccolta di dati di telemetria in tempo reale dei dispositivi con un modello predittivo creato utilizzando [Azure Machine Learning][lnk-machine-learning].

Con Azure IoT Suite, è possibile connettersi agli asset e monitorarli, nonché analizzare i dati di telemetria in tempo reale nei dashboard e nelle visualizzazioni in modo semplice e rapido. Nella soluzione di manutenzione predittiva i dashboard e le visualizzazioni forniscono nuove informazioni con cui è possibile promuovere l'efficienza e aumentare i flussi di profitti.

## <a name="the-scenario"></a>Scenario

Fabrikam è una compagnia aerea locale che si concentra sulle esperienze eccezionali dei clienti a prezzi competitivi. Una causa di ritardi dei voli sono i problemi di manutenzione e la manutenzione dei motori degli aeromobili è particolarmente complessa. Fabrikam deve evitare a tutti i costi i guasti dei motori durante il volo, quindi controlla regolarmente i motori e pianifica la manutenzione in base a un programma. Tuttavia, i motori non sempre si usurano allo stesso modo. Sui motori viene eseguita manutenzione superflua. Ancora più importante, si verificano problemi che possono forzare a terra un aereo fino a quando non viene eseguita la manutenzione. Se un aereo si trova in un luogo in cui tecnici esperti o parti di ricambio non sono disponibili, questi problemi possono essere particolarmente costosi.

I motori degli aeromobili Fabrikam sono instrumentati con sensori che controllano le condizioni del motore durante il volo. Fabrikam usa la soluzione di manutenzione predittiva per raccogliere i dati dei sensori raccolti durante il volo. Dopo aver accumulato anni di dati operativi e sui guasti dei motori, i ricercatori di dati di Fabrikam hanno modellato un modo per prevedere la vita utile rimanente (RUL) di un motore di aeromobile. Il modello usa una correlazione tra i dati provenienti da quattro dei sensori del motore e l'usura del motore che conduce a un eventuale guasto. Mentre Fabrikam continua a eseguire controlli periodici per garantire la sicurezza, ora può usare i modelli per calcolare il valore RUL per ogni motore dopo ogni volo. Il modello usa i dati di telemetria raccolti dai motori durante il volo. Fabrikam può prevedere i futuri punti di malfunzionamento e pianificare la manutenzione e la riparazione in anticipo.

> [!NOTE]
> Il modello di soluzione usa dati di usura del motore effettivi.

Prevedendo il momento in cui sarà necessaria la manutenzione, Fabrikam può ottimizzare le proprie operazioni per ridurre i costi.

I coordinatori della manutenzione usano utilità di pianificazione per:

- Pianificare l'esecuzione della manutenzione quando un velivolo si ferma in una determinata località.
- Assicurarsi che il velivolo rimanga fuori servizio per un periodo di tempo senza causare interruzioni della pianificazione.
- Per pianificare i tecnici di conseguenza e garantire così che i velivoli siano gestiti in modo efficiente senza tempi di attesa.

I responsabili del controllo di inventario ricevono i piani di manutenzione, pertanto possono ottimizzare il processo di ordinazione e l'inventario delle parti di ricambio.

Queste attività consentono a Fabrikam di ridurre al minimo i tempo di fermo a terra dei velivoli e di ridurre i costi operativi, garantendo la sicurezza dei passeggeri e dell'equipaggio.

Per comprendere come [Azure IoT Suite][lnk_iot_suite] fornisce ai clienti le funzionalità necessarie a realizzare il potenziale della manutenzione predittiva, vedere questa [infografica][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-is-built"></a>Creazione della soluzione di manutenzione predittiva

Per visualizzare queste funzionalità partendo dai dati di telemetria del dispositivo raccolti attraverso i servizi di IoT Suite, la soluzione usa un modello esistente di Azure Machine Learning disponibile. Microsoft ha creato un [modello di regressione][lnk_regression_model] basato su dati pubblici<sup>\[[1]\]</sup> e le istruzioni dettagliate su come usare il modello.

La soluzione di manutenzione predittiva di Azure IoT usa il modello di regressione creato da questo modello. Il modello viene distribuito nella sottoscrizione di Azure ed esposto tramite un'API generata automaticamente. La soluzione include un subset di dati di test che rappresenta 4 (su un totale di 100) motori e 4 (su un totale di 21) flussi di dati dei sensori. Questi dati sono sufficienti per fornire un risultato esatto dal modello con training.

*\[1\] A. Saxena and K. Goebel (2008). "Turbofan Engine Degradation Simulation Data Set", NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## <a name="get-started-with-predictive-maintenance"></a>Introduzione alla manutenzione predittiva

Questa esercitazione illustra come effettuare il provisioning della soluzione di manutenzione predittiva. Ne descrive anche le funzionalità di base. È possibile accedere a molte di queste funzionalità tramite il dashboard distribuito con la soluzione preconfigurata.

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure attiva.

> [!NOTE]
> Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk_free_trial].

1. Accedere a [azureiotsuite.com][lnk-azureiotsuite] con le credenziali dell'account Azure e fare clic su **+** per creare una soluzione.
1. **Selezionare** il riquadro **Manutenzione predittiva**.
1. Immettere un valore in **Nome soluzione** per la soluzione preconfigurata di manutenzione predittiva.
1. Selezionare l'**area** e la **sottoscrizione** che si desidera usare per il provisioning della soluzione.
1. Fare clic su **Crea soluzione** per iniziare il processo di provisioning. In genere il processo richiede alcuni minuti.

### <a name="wait-for-the-provisioning-process-to-complete"></a>Attendere il completamento del processo di provisioning.

1. Fare clic sul riquadro della soluzione con stato **Provisioning**.
1. Notare gli stati **Provisioning** man mano che i servizi di Azure vengono distribuiti nella sottoscrizione di Azure.
1. Al termine del provisioning, lo stato cambierà in **Pronto**.
1. Fare clic sul riquadro per visualizzare i dettagli della soluzione nel riquadro di destra. Da questo riquadro è possibile avviare il dashboard della soluzione e accedere all'area di lavoro di Machine Learning.

> [!NOTE]
> In caso di problemi di distribuzione della soluzione preconfigurata, vedere [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions] e le [domande frequenti][lnk-faq]. Se i problemi persistono, creare un ticket di servizio nel [portale][lnk-portal].

Se ci sono dettagli importanti non elencati per la soluzione, è possibile inviare suggerimenti sulle funzionalità usando i [suggerimenti degli utenti](https://feedback.azure.com/forums/321918-azure-iot).

## <a name="view-the-solution"></a>Visualizzare la soluzione

Questa sezione descrive l'interfaccia utente della soluzione.

### <a name="predictive-maintenance-dashboard"></a>Dashboard di manutenzione predittiva

Questa pagina dell'applicazione Web usa i controlli JavaScript di Power BI (vedere il [repository di oggetti visivi di Power BI][lnk-powerbi]) per visualizzare:

* I dati di output dei processi di Analisi di flusso nell'archivio BLOB.
* La vita utile rimanente Il conteggio dei cicli per motore di aereo.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Osservare il comportamento della soluzione cloud

Nel portale di Azure passare al gruppo di risorse con il nome della soluzione scelto per visualizzare le risorse di cui è stato effettuato il provisioning.

![][img-resource-group]

Quando si esegue il provisioning della soluzione preconfigurata, viene visualizzato un messaggio di posta elettronica con un collegamento all'area di lavoro di Machine Learning. È anche possibile passare all'area di lavoro di Machine Learning dalla pagina [azureiotsuite.com][lnk-azureiotsuite] per la soluzione di cui è stato effettuato il provisioning. Quando lo stato della soluzione è **Ready** (Pronto), in questa pagina è disponibile un riquadro.

![][img-machine-learning]

Nel portale della soluzione si noterà che l'esempio include quattro dispositivi simulati per rappresentare due aerei con due motori per aereo, ciascuno con quattro sensori. Quando si accede per la prima volta al portale della soluzione, la simulazione viene arrestata.

![][img-simulation-stopped]

Fare clic su **Avvia simulazione** per iniziare la simulazione. Il dashboard viene popolato con cronologia del sensore, vita utile rimanente, cicli e cronologia della vita utile rimanente.

![][img-simulation-running]

Quando la vita utile rimanente è inferiore a 160, una soglia arbitraria scelta a scopo dimostrativo, il portale della soluzione mostra un simbolo di avviso accanto alla visualizzazione della vita utile rimanente. Il portale della soluzione evidenzia anche in giallo il motore dell'aereo. Si noti come i valori della vita utile rimanente abbiano complessivamente una tendenza generale al ribasso, ma tendano a oscillare in alto o in basso. Questo comportamento dovuto alle diverse lunghezze dei cicli e all'accuratezza del modello.

![][img-simulation-warning]

La simulazione completa richiede circa 35 minuti per completare 148 cicli. La soglia di 160 per la vita utile rimanente viene raggiunta per la prima volta dopo circa 5 minuti ed entrambi i motori la raggiungono a circa 8 minuti.

La simulazione viene eseguita sul set di dati completo per 148 cicli e si stabilizza sui valori finali dei cicli e della vita utile rimanente.

È possibile arrestare la simulazione in qualsiasi punto, ma facendo clic su **Start Simulation** viene riprodotta la simulazione dall'inizio del set di dati.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su come Azure IoT consente scenari di manutenzione predittiva, leggere [Capture value from the Internet of Things][lnk_capture_value] (Acquisire valore da Internet delle cose).

Esaminare una [procedura dettagliata][lnk-predictive-walkthrough] della soluzione di manutenzione predittiva.

È anche possibile esplorare alcune altre funzionalità delle soluzioni preconfigurate di IoT Suite:

* [Domande frequenti su IoT Suite][lnk-faq]
* [Sicurezza IoT sin dall'inizio][lnk-security-groundup]

[img-resource-group]: media/iot-suite-predictive-overview/resource-group.png
[img-simulation-stopped]: media/iot-suite-predictive-overview/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-overview/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-overview/simulation-warning.png
[img-machine-learning]: media/iot-suite-predictive-overview/machine-learning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3

[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-permissions]: iot-suite-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
