---
title: Architettura di riferimento degli acceleratori di soluzione IoT - Azure | Microsoft Docs
description: Informazioni sull'architettura di riferimento degli acceleratori di soluzione Azure IoT. Gli acceleratori di soluzioni esistenti sfruttano questa architettura di riferimento. È anche possibile usare l'architettura di riferimento durante la compilazione di soluzioni IoT personalizzate.
author: dominicbetts
ms.author: dobett
ms.date: 12/04/2018
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: philmea
ms.openlocfilehash: ba5eb50dcf800c186124db348ac584ff6f55cebb
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467316"
---
# <a name="introduction-to-the-azure-iot-reference-architecture"></a>Introduzione all'architettura di riferimento di Azure IoT

Questo articolo presenta l'[architettura di riferimento per Azure IoT](https://aka.ms/iotrefarchitecture) e fornisce esempi di come gli [acceleratori di soluzioni di Azure IoT](about-iot-accelerators.md) ne seguono le indicazioni.

Gli acceleratori open source di soluzioni di [monitoraggio remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md) e [fabbrica connessa](iot-accelerators-connected-factory-sample-walkthrough.md) seguono buona parte delle indicazioni dell'architettura di riferimento. È possibile usare gli acceleratori di soluzioni come punto iniziale per una soluzione IoT personalizzata o come strumenti di apprendimento.

## <a name="overview"></a>Panoramica

L'architettura di riferimento descrive gli elementi di una soluzione IoT, come i principi tecnologici, la composizione dei servizi di Azure IoT e i dispositivi. L'architettura offre inoltre consigli relativi alle tecnologie di implementazione.

Al livello più alto, è possibile visualizzare una soluzione IoT come costituita da:

* Elementi che generano e inviano dati di telemetria, ad esempio misure ed eventi. Nell'acceleratore di soluzione di monitoraggio remoto, dispositivi come camion o montacarichi sono elementi che inviano dati di telemetria.
* Informazioni dettagliate dei dati di telemetria inviati dagli elementi. Nell'acceleratore di soluzione di monitoraggio remoto, una regola genera un'informazione dettagliata. Ad esempio, una regola consente di identificare quando la temperatura in un motore raggiunge una determinata soglia.
* Azioni, basate su informazioni dettagliate, che consentono di migliorare un operazione commerciale o un processo. Nell'acceleratore di soluzione di monitoraggio remoto, un'azione di posta elettronica può inviare una notifica a un operatore relativa a un potenziale problema a un motore.

L'[architettura di riferimento per Azure IoT](https://aka.ms/iotrefarchitecture) è un documento in continuo cambiamento che si aggiorna con l'evolversi della tecnologia.

## <a name="core-subsystems"></a>Sottosistemi principali

L'architettura di riferimento identifica i sottosistemi principali illustrati nel diagramma seguente:

![Sottosistemi principali](media/iot-accelerators-architecture-overview/CoreSubsystems.png)

Le sezioni seguenti descrivono come i componenti dell'acceleratore di soluzione di monitoraggio remoto eseguono il mapping dei sottosistemi principali.

### <a name="iot-devices"></a>Dispositivi IoT

Una soluzione IoT deve consentire una comunicazione sicura, efficiente e affidabile tra praticamente qualsiasi tipo di dispositivo e un gateway cloud. I dispositivi sono asset aziendali che variano dai semplici sensori di temperatura alle complesse linee di produzione industriale che comprendono centinaia di componenti e sensori.

Un gateway sul campo, o dispositivo perimetrale, è un software specializzato per dispositivi e appliance o un software di uso generico che può fungere da:

* Abilitatore di comunicazione per gestire la conversione di protocollo.
* Sistema di controllo del dispositivo locale e hub di elaborazione dei dati di telemetria dei dispositivi. Un dispositivo perimetrale può elaborare i dati di telemetria in locale per controllare i dispositivi senza comunicare con il cloud. È anche possibile filtrare o aggregare i dati di telemetria del dispositivo per ridurre la quantità di dati di telemetria trasferita nel cloud.

Nella soluzione di monitoraggio remoto, i dispositivi si connettono a un hub IoT e inviano i dati di telemetria per l'elaborazione. La soluzione di monitoraggio remoto consente inoltre agli operatori di gestire i dispositivi tramite i processi o la gestione automatica dei dispositivi. È possibile usare Azure IoT SDK per dispositivi per implementarli.

La soluzione di monitoraggio remoto può distribuire e gestire i dispositivi IoT Edge. L'elaborazione ai margini consente di ridurre il volume dei dati di telemetria inviati al cloud e accelerare le risposte agli eventi del dispositivo.

### <a name="cloud-gateway"></a>Gateway cloud

Un gateway cloud abilita la comunicazione da e verso i dispositivi e i dispositivi perimetrali. Questi dispositivi possono trovarsi in diversi siti remoti.

Il gateway gestisce la comunicazione dei dispositivi, inclusa la gestione della connessione, la protezione del percorso di comunicazione, l'autenticazione e l'autorizzazione. Il gateway applica inoltre le quote di velocità effettiva e connessione e raccoglie i dati di telemetria usati per la fatturazione, la diagnostica e altre attività di monitoraggio.

La soluzione di monitoraggio remoto consente di distribuire un hub IoT per fornire un endpoint sicuro a cui i dispositivi possono inviare i dati di telemetria. L'hub IoT, inoltre:

* Include un archivio di identità del dispositivo per gestire i dispositivi che hanno l'autorizzazione a connettersi alla soluzione.
* Abilita la soluzione a inviare comandi ai dispositivi (ad esempio, aprire una valvola per rilasciare la pressione).
* Abilita la gestione dei dispositivi in blocco per eseguire, ad esempio, l'aggiornamento del firmware in un set di dispositivi.

### <a name="stream-processing"></a>Elaborazione del flusso

Poiché soluzione inserisce i dati di telemetria, è importante comprendere come il flusso di elaborazione dei dati di telemetria può variare. A seconda dello scenario, i record dei dati possono fluire attraverso diverse fasi:

* Archiviazione, ad esempio cache in memoria, code temporanee e archivi permanenti.

* Analisi, per eseguire dati di telemetria di input attraverso un set di condizioni. I record di dati di output prodotti possono essere diversi. Ad esempio, i dati di telemetria di input codificati in Avro possono restituire dati di telemetria di output codificati in JSON.

* I dati di telemetria di input originali e i record di analisi di output vengono in genere archiviati e resi disponibili per la visualizzazione. I dati di telemetria di input e i record di output possono anche attivare azioni, come messaggi di posta elettronica, ticket degli eventi imprevisti e comandi del dispositivo.

Il routing può inviare dati di telemetria a uno o più endpoint di archiviazione, processi di analisi e azioni. Una soluzione può combinare le fasi in ordine diverso ed elaborarle con attività simultanee in parallelo.

La soluzione di monitoraggio remoto usa [Analisi di flusso di Azure](/azure/stream-analytics/) per l'elaborazione del flusso. Il motore delle regole nella soluzione usa le query di Analisi di flusso per generare avvisi e azioni. Ad esempio, la soluzione può usare una query per identificare quando la temperatura del vano di carico di un camion scende sotto i 36 gradi nell'arco di cinque minuti.

### <a name="storage"></a>Archiviazione

Le soluzioni IoT possono generare grandi quantità di dati, spesso di serie temporali. Questi dati devono essere archiviati dove possono essere usati per la visualizzazione e la creazione di report. Una soluzione può anche dover accedere ai dati in un secondo momento per l'analisi o un'ulteriore elaborazione. È pratica comune suddividere i dati in archivi secondo la frequenza d'uso. Un archivio conterrà i dati più recenti per l'accesso a bassa latenza. In un altro archivio vengono solitamente archiviati i dati cronologici.

La soluzione di monitoraggio remoto usa [Azure Time Series Insights](/azure/time-series-insights/) come archivio dati più attivo e Cosmos DB come archivio meno attivo.

### <a name="ui-and-reporting-tools"></a>Interfaccia utente e strumenti di reporting

L'interfaccia utente della soluzione può fornire:

* Accesso e visualizzazione dei dati del dispositivo e risultati dell'analisi.
* Individuazione dei dispositivi tramite il registro.
* Comando e controllo dei dispositivi.
* Flussi di lavoro del provisioning di dispositivi.
* Notifiche e avvisi.
* Integrazione con dashboard interattive e in tempo reale per visualizzare i dati provenienti da un gran numero di dispositivo.  
* Servizi di localizzazione e rilevazione geografica.

La soluzione di monitoraggio remoto include un'interfaccia utente web per fornire questa funzionalità. L'interfaccia utente web include:

* Una mappa interattiva per mostrare la posizione dei dispositivi.
* Accesso all'explorer di Time Series Insights per eseguire query e analizzare i dati di telemetria.

### <a name="business-integration"></a>Integrazione aziendale

Il livello di integrazione aziendale gestisce l'integrazione della soluzione IoT con sistemi aziendali come CRM, ERP e applicazioni line-of-business. Alcuni esempi includono fatturazione di servizi, supporto tecnico, e fornitura di parti di ricambio.

La soluzione di monitoraggio remoto usa regole per inviare messaggi di posta elettronica quando viene soddisfatta una condizione. Ad esempio, la soluzione può notificare l'operatore quando la temperatura in un camion scende sotto i 36 gradi.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato l'architettura di riferimento di Azure IoT e mostrato alcuni esempi di come l'acceleratore di soluzione di monitoraggio remoto ne segue le indicazioni. Il passaggio successivo consiste nella lettura di [Architettura di riferimento di Microsoft Azure IoT](https://aka.ms/iotrefarchitecture).