---
title: Informazioni su Azure IoT Edge | Microsoft Docs
description: Panoramica del servizio Azure IoT Edge
services: iot-Edge
documentationcenter: 
author: kgremban
manager: timlt
editor: chipalost
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: kgremban
ms.custom: 
ms.openlocfilehash: 5f69041572729d1458a22a855128639056d61586
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-azure-iot-edge---preview"></a>Informazioni su Azure IoT Edge - anteprima

Azure IoT Edge sposta nei dispositivi l'analisi cloud e la logica di business personalizzata consentendo all'organizzazione di concentrarsi sulle analisi approfondite di livello aziendale invece che sulla gestione dati. È possibile ottenere una reale scalabilità per la soluzione configurando il software IoT, distribuendola nei dispositivi tramite contenitori standard e monitorandola dal cloud.

L'analisi incrementa il valore aggiunto nelle soluzioni IoT, ma non tutte le operazioni di analisi devono essere eseguite nel cloud. Per fare in modo che un dispositivo risponda alle emergenze il più rapidamente possibile, è possibile eseguire il rilevamento anomalie sul dispositivo stesso. Analogamente, per ridurre i costi della larghezza di banda ed evitare il trasferimento di terabyte di dati non elaborati, è possibile eseguire in locale la pulizia e l'aggregazione dei dati e quindi inviare le informazioni al cloud. 

Azure IoT Edge è costituito da tra componenti:
* I moduli di IoT Edge sono contenitori che eseguono servizi di Azure, servizi di terze parti o il codice dell'utente. Vengono distribuiti nei dispositivi IoT Edge ed eseguiti in locale su tali dispositivi. 
* Il runtime di IoT Edge viene eseguito su ogni dispositivo IoT Edge e gestisce i moduli distribuiti in ogni dispositivo. 
* Un'interfaccia basata sul cloud consente di monitorare e gestire in remoto i dispositivi IoT Edge.

## <a name="iot-edge-modules"></a>Moduli di IoT Edge

I moduli di IoT Edge sono unità di esecuzione, attualmente implementati come contenitori compatibili con Docker, che eseguono la logica di business nella rete perimetrale. Si possono configurare più moduli in modo che comunichino tra di essi, creando una pipeline di elaborazione dati. È possibile sviluppare moduli personalizzati o inserire determinati servizi di Azure nei moduli che contengono informazioni offline e nella rete perimetrale. 

### <a name="artificial-intelligence-on-the-edge"></a>Intelligenza artificiale nella rete perimetrale

Azure IoT Edge consente di distribuire funzionalità di intelligenza artificiale di valore elevato, come elaborazione di eventi complessi, apprendimento automatico, riconoscimento delle immagini e altre, senza scriverle internamente. Tutti i servizi di Azure, ad esempio Funzioni di Azure, Analisi di flusso di Azure e Azure Machine Learning, possono essere eseguiti in locale tramite Azure IoT Edge. Questa possibilità non è tuttavia limitata ai servizi di Azure. Chiunque può creare moduli di intelligenza artificiale e renderli disponibili alla community. 

### <a name="bring-your-own-code"></a>Usare un codice personalizzato

Azure IoT Edge consente anche di distribuire il proprio codice nei dispositivi. Azure IoT Edge segue lo stesso modello di programmazione degli altri servizi di Azure IoT. Lo stesso codice può essere eseguito in un dispositivo o nel cloud. Poiché Azure IoT Edge supporta sia Linux che Windows, è possibile scrivere codice per la piattaforma preferita. Supporta Java, .NET Core 2.0, Node.js, C e Python, quindi gli sviluppatori possono scrivere codice in un linguaggio già conosciuto e usare la logica di business esistente senza scriverla da zero.

## <a name="iot-edge-runtime"></a>Runtime di IoT Edge

Il runtime di Azure IoT Edge consente la logica personalizzata e cloud sui dispositivi IoT Edge. Fa parte del dispositivo IoT Edge ed esegue operazioni di gestione e di comunicazione. Il runtime esegue diverse funzioni:

* Installa e aggiorna i carichi di lavoro nel dispositivo.
* Mantiene gli standard di sicurezza di Azure IoT Edge sul dispositivo.
* Assicura che i moduli di IoT Edge siano sempre in esecuzione.
* Segnala l'integrità dei moduli al cloud per il monitoraggio remoto.
* Facilita la comunicazione tra i dispositivi foglia downstream e il dispositivo IoT Edge.
* Facilita la comunicazione tra i moduli sul dispositivo IoT Edge.
* Facilita la comunicazione tra il dispositivo IoT Edge e il cloud.

![Il runtime di IoT Edge invia informazioni dettagliate e report all'hub IoT][1]

È possibile usare un dispositivo Azure IoT Edge nel modo preferito. Il runtime viene spesso usato per distribuire l'intelligenza artificiale nei gateway che aggregano ed elaborano dati provenienti da diversi altri dispositivi locali, ma questa è solo un'opzione. I dispositivi foglia possono anche essere dispositivi Azure IoT Edge, indipendentemente dal fatto che siano connessi a un gateway o direttamente al cloud.

Il runtime di Azure IoT Edge viene eseguito in un set completo di dispositivi IoT per consentire l'uso del runtime in svariati modi. Supporta sistemi operativi sia Linux che Windows ed estrae i dettagli sull'hardware. Usare un dispositivo inferiore a Raspberry Pi 3 se non si elaborano molti dati o non si passa a un server industrializzato per eseguire carichi di lavoro con un utilizzo intensivo delle risorse.

## <a name="iot-edge-cloud-interface"></a>Interfaccia cloud di IoT Edge

Gestire il ciclo di vita del software per i dispositivi aziendali è complesso. Gestire il ciclo di vita del software per milioni di dispositivi IoT diversi è ancora più difficile. È necessario creare e configurare carichi di lavoro per un particolare tipo di dispositivo, distribuirli su vasta scala in milioni di dispositivi della soluzione e monitorarli per individuare eventuali dispositivi non correttamente funzionanti. Queste attività non possono essere eseguite per ogni singolo dispositivo, ma devono essere eseguite su vasta scala.

Azure IoT Edge si integra facilmente con Azure IoT Suite offrendo un piano di controllo per le esigenze della soluzione. I servizi cloud consentono agli utenti di:

* Creare e configurare un carico di lavoro da eseguire in un tipo specifico di dispositivo.
* Inviare un carico di lavoro a un set di dispositivi.
* Monitorare sul campo i carichi di lavoro in esecuzione sui dispositivi.

![Dati di telemetria, informazioni dettagliate e azioni dei dispositivi vengono coordinati nel cloud][2]

## <a name="next-steps"></a>Passaggi successivi

Provare ad applicare questi concetti [distribuendo IoT Edge in un dispositivo simulato][lnk-quickstart].

<!-- Images -->
[1]: ./media/how-iot-edge-works/runtime.png
[2]: ./media/how-iot-edge-works/cloud-interface.png

<!-- Links -->
[lnk-quickstart]: quickstart.md