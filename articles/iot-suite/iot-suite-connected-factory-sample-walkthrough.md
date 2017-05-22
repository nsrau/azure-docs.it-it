---
title: Procedura dettagliata per la soluzione di connected factory di Azure IoT Suite | Microsoft Docs
description: Descrizione della soluzione preconfigurata di connected factory di Azure IoT e della relativa architettura.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: fd5e1083c65068c7f6b019838586b1bc7e37aa9f
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="connected-factory-preconfigured-solution-walkthrough"></a>Procedura dettagliata per la soluzione preconfigurata di connected factory

La [soluzione preconfigurata ][lnk-preconfigured-solutions] di connected factory di IoT Suite rappresenta l'implementazione di una soluzione industriale end-to-end che:

* Si connette sia a dispositivi industriali simulati che eseguono server OPC UA in linee di produzione simulate degli stabilimenti, sia a dispositivi server OPC UA reali.
* Visualizza KPI e OEE di tali dispositivi e delle linee di produzione.
* Dimostra come usare un'applicazione basata sul cloud per interagire con i sistemi server OPC UA.
* Consente di connettere i propri dispositivi server OPC UA.
* Consente di esplorare e modificare i dati dei server OPC UA.
* Si integra con il servizio Azure Time Series Insights (TSI) per fornire visualizzazioni personalizzate dei dati dei server OPC UA.

È possibile usare la soluzione come punto di partenza per l'implementazione e [personalizzarla][lnk-customize] in base ai requisiti aziendali specifici.

Questo articolo illustra alcuni degli elementi principali della soluzione di connected factory per comprenderne il funzionamento. Queste informazioni consentono di:

* Risolvere i problemi nella soluzione.
* Pianificare come personalizzare la soluzione per soddisfare requisiti specifici.
* Progettare la propria soluzione IoT che usa i servizi di Azure.

## <a name="logical-architecture"></a>Architettura logica

Il diagramma seguente illustra i componenti logici della soluzione preconfigurata:

![Architettura logica di connected factory][connected-factory-logical]

## <a name="simulation"></a>Simulazione

Le postazioni simulate e i sistemi MES (Manufacturing Execution Systems) simulati costituiscono una linea di produzione dello stabilimento. I dispositivi simulati e il modulo di pubblicazione OPC si basano sullo [standard OPC UA .NET][lnk-OPC-UA-NET-Standard] pubblicato dalla OPC Foundation.

Il proxy OPC e il modulo di pubblicazione OPC vengono implementati come moduli basati su [Azure IoT Edge][lnk-Azure-IoT-Gateway]. Ogni linea di produzione simulata ha un gateway designato collegato.

Tutti i componenti di simulazione vengono eseguiti in contenitori Docker ospitati in una macchina virtuale Linux di Azure. Per impostazione predefinita, la simulazione è configurata per l'esecuzione di otto linee di produzione simulate.

## <a name="simulated-production-line"></a>Linea di produzione simulata

Una linea di produzione produce parti. È composta da postazioni diverse, ovvero montaggio, collaudo e imballaggio.

La simulazione viene eseguita e aggiorna i dati esposti tramite i nodi OPC UA. Tutte le postazioni della linea di produzione simulata sono coordinate dal sistema MES tramite OPC UA.

## <a name="simulated-manufacturing-execution-system"></a>MES (Manufacturing Execution System) simulato

Il MES monitora ogni postazione nella linea di produzione tramite OPC UA per rilevare le modifiche di stato della postazione stessa. Chiama metodi OPC UA per controllare le postazioni e trasferisce un prodotto da una postazione all'altra finché non è completo.

## <a name="gateway-opc-publisher-module"></a>Modulo di pubblicazione OPC del gateway

Il modulo di pubblicazione OPC si connette ai server OPC UA della postazione ed effettua la sottoscrizione ai nodi OPC da pubblicare. Il modulo converte i dati del nodo in formato JSON, li crittografa e li invia all'hub IoT come messaggi di pubblicazione/sottoscrizione OPC UA.

Il modulo di pubblicazione OPC richiede solo una porta HTTPS in uscita (443) e può funzionare con l'infrastruttura aziendale esistente.

## <a name="gateway-opc-proxy-module"></a>Modulo proxy OPC del gateway

Il modulo proxy OPC UA del gateway effettua il tunnel dei messaggi di comando e controllo OPC UA binari e richiede solo una porta HTTPS in uscita (443). Può funzionare con l'infrastruttura aziendale esistente, inclusi i proxy Web.

Usa metodi di dispositivo hub IoT per trasferire pacchetti di dati TCP/IP a livello di applicazione e garantisce quindi l'attendibilità degli endpoint, la crittografia dei dati e l'integrità tramite SSL/TLS.

Il protocollo binario OPC UA inoltrato attraverso il proxy stesso usa la crittografia e l'autenticazione UA.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

Il modulo di pubblicazione OPC del gateway effettua la sottoscrizione ai nodi del server OPC UA per rilevare le modifiche ai valori dei dati. Se viene rilevata una modifica dei dati in uno dei nodi, questo modulo invia messaggi all'hub IoT di Azure.

L'hub IoT fornisce un'origine evento ad Azure Time Series Insights. Time Series Insights archivia i dati per 30 giorni in base ai timestamp associati ai messaggi. Questi dati includono:

* ApplicationUri OPC UA
* NodeId OPC UA
* Valore del nodo
* Timestamp di origine
* DisplayName OPC UA

Time Series Insights non consente attualmente ai clienti di definire il periodo di conservazione dei dati.

Time Series Insights esegue query sui dati del nodo usando una funzione SearchSpan (Time.From, Time.To) ed esegue l'aggregazione in base ad ApplicationUri OPC UA, NodeId OPC UA oppure DisplayName OPC UA.

Per recuperare i dati per i misuratori OEE e KPI e i grafici di serie temporali, i dati vengono aggregati per numero di eventi, Sum, Avg, Min e Max.

Le serie temporali vengono compilate usando un processo differente. OEE e KPI vengono calcolati dai dati di base della postazione e propagati alla topologia (linee di produzione, stabilimento, azienda) nell'applicazione.

La serie temporale per la topologia OEE e KPI viene calcolata nell'app ogni volta che un intervallo di tempo visualizzato è pronto, ad esempio la visualizzazione giornaliera viene aggiornata ogni ora.

La visualizzazione della serie temporale dei dati del nodo proviene direttamente da Time Series Insights tramite un'aggregazione per timespan.

## <a name="iot-hub"></a>Hub IoT
L'[hub IoT][lnk-IoT Hub] riceve i dati inviati dal modulo di pubblicazione OPC nel cloud e li mette a disposizione del servizio di Azure Time Series Insights. 

L'hub IoT nella soluzione esegue anche le operazioni seguenti:
- Gestisce un registro delle identità che archivia gli ID per tutti i moduli di pubblicazione OPC e tutti i moduli proxy OPC.
- Viene usato come canale di trasporto per la comunicazione bidirezionale del modulo proxy OPC.

## <a name="azure-storage"></a>Archiviazione di Azure
La soluzione usa Archiviazione BLOB di Azure come spazio di archiviazione su disco per la macchina virtuale e per i dati di distribuzione.

## <a name="web-app"></a>App Web
L'app Web distribuita nell'ambito della soluzione preconfigurata è costituita da un client OPC UA integrato, elaborazione degli avvisi e visualizzazione dei dati di telemetria.

## <a name="next-steps"></a>Passaggi successivi

È possibile proseguire con l'introduzione a IoT Suite vedendo gli articoli seguenti:

* [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions]

[connected-factory-logical]:media/iot-suite-connected-factory-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/azure-iot-gateway-sdk
[lnk-permissions]: iot-suite-permissions.md
