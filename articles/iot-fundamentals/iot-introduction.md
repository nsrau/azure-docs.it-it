---
title: Introduzione ad Azure IoT (Internet of Things)
description: Introduzione che illustra i concetti fondamentali di Azure IoT e dei servizi IoT, inclusi esempi utili relativi all'uso di IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: c79f18669e1b13f79491e98658107221b43f3ff5
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046183"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Informazioni su Azure IoT (Internet of Things)

Azure IoT (Internet of Things, Internet delle cose) è una raccolta di servizi cloud gestiti da Microsoft per la connessione, il monitoraggio e il controllo di miliardi di asset IoT. Semplificando, una soluzione IoT è costituita da uno o più dispositivi IoT che comunicano con uno o più servizi back-end ospitati nel cloud. 

## <a name="iot-devices"></a>Dispositivi IoT

Un dispositivo IoT è in genere costituito da un circuito stampato con sensori collegati che usano il Wi-Fi per connettersi a Internet. Ad esempio:

* Sensore di pressione in una pompa dell'olio remota.
* Sensori di temperatura e umidità in un condizionatore.
* Accelerometro in un ascensore.
* Sensori di presenza in una stanza.

Per creare la soluzione, è disponibile un'ampia gamma di dispositivi realizzati da produttori diversi. Per un elenco dei dispositivi certificati compatibili con l'hub IoT di Azure IoT, vedere [Catalogo dei dispositivi Azure Certified per IoT](https://catalog.azureiotsolutions.com/alldevices). Per la realizzazione di prototipi, è possibile usare dispositivi come [MXChip DevKit](https://microsoft.github.io/azure-iot-developer-kit/) o [Raspberry Pi](https://www.raspberrypi.org/). Il dispositivo Devkit include sensori per temperatura, pressione e umidità, oltre a un giroscopio, un accelerometro e un magnetometro. Il dispositivo Raspberry Pi consente di collegare numerosi tipi di sensori. 

Microsoft offre [SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md) open source che è possibile usare per creare app eseguibili nei dispositivi. Questi [SDK consentono di semplificare e velocizzare](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) lo sviluppo di soluzioni IoT.

## <a name="communication"></a>Comunicazione

In genere, i dispositivi Internet inviano i dati di telemetria acquisiti dai sensori a servizi back-end nel cloud. Sono tuttavia possibili altri tipi di comunicazione, ad esempio un servizio back-end che invia comandi ai dispositivi. Ecco alcuni esempi di comunicazione da dispositivo a cloud e da cloud a dispositivo:

* Un camion frigorifero in movimento invia ogni cinque minuti i dati relativi alla temperatura a un hub IoT. 

* Il servizio back-end invia un comando a un dispositivo per modificare la frequenza di invio dei dati di telemetria per facilitare la diagnosi di un problema. 

* Il dispositivo invia avvisi in base ai valori acquisiti dai rispettivi sensori. Un dispositivo che monitora un reattore chimico di tipo batch in uno stabilimento chimico invia, ad esempio, un avviso quando la temperatura supera un determinato valore.

* I dispositivi inviano le informazioni da visualizzare in un dashboard accessibile a operatori umani. Ad esempio, una sala di controllo in una raffineria può visualizzare i volumi relativi a temperatura, pressione e flusso di ogni conduttura, consentendo agli operatori di monitorare la struttura. 

Gli [SDK per dispositivi IoT](../iot-hub/iot-hub-devguide-sdks.md) e l'hub IoT supportano [protocolli di comunicazione](../iot-hub/iot-hub-devguide-protocols.md) comuni, quali HTTP, MQTT e AMQP.

I dispositivi IoT presentano caratteristiche diverse da quelle di altri client, ad esempio browser e app per dispositivi mobili. Gli SDK per dispositivi consentono di affrontare i problemi correlati alla connessione sicura e affidabile dei dispositivi al servizio back-end.  I dispositivi IoT in particolare:

* Sono spesso sistemi incorporati senza operatore umano (diversamente da un telefono).
* Possono essere distribuiti in località remote, dove l'accesso fisico è costoso.
* Possono essere raggiungibili solo tramite il back-end della soluzione.
* Possono avere risorse di alimentazione e di elaborazione limitate.
* Possono disporre di una connettività di rete intermittente, lenta o costosa.
* Possono richiedere protocolli di applicazioni proprietari, personalizzati o specifici del settore.

## <a name="back-end-services"></a>Servizi back-end 

In una soluzione IoT il servizio back-end offre funzionalità quali:

* Ricezione dei dati di telemetria su larga scala dai dispositivi e possibilità di scegliere come elaborare e archiviare tali dati.
* Analisi dei dati di telemetria per fornire informazioni dettagliate, in tempo reale o dopo un evento.
* Invio di comandi dal cloud a un dispositivo specifico. 
* Provisioning di dispositivi e possibilità di controllare i dispositivi autorizzati a connettersi all'infrastruttura.
* Controllo dello stato e monitoraggio delle attività dei dispositivi.
* Gestione del firmware installato nei dispositivi.

Ad esempio, in una soluzione di monitoraggio remoto per un impianto di estrazione del petrolio il back-end cloud usa i dati di telemetria acquisiti dalle pompe per identificare comportamenti anomali. Quando il servizio back-end identifica un'anomalia, può inviare automaticamente un comando al dispositivo per intraprendere un'azione correttiva. Questo processo genera un ciclo di feedback automatizzato tra il dispositivo e il cloud che accresce notevolmente l'efficienza della soluzione.

## <a name="azure-iot-examples"></a>Esempi di Azure IoT

Per esempi concreti relativi all'uso di Azure IoT in ambito aziendale, vedere [i case study tecnici Microsoft per IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="next-steps"></a>Passaggi successivi

Per casi aziendali effettivi e per informazioni sull'architettura usata, vedere [Microsoft Azure IoT Technical Case Studies](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured) (Case study tecnici di Microsoft Azure IoT).

Per alcuni progetti di esempio che possono essere usati con un DevKit di IoT, vedere [IoT DevKit Project Catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) (Catalogo di progetti per DevKit IoT). 

Per una spiegazione più completa dei diversi servizi e del modo in cui vengono usati, vedere [Servizi e tecnologie di Azure IoT](iot-services-and-technologies.md).

Per un'analisi approfondita dell'architettura IoT, vedere [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Architettura di riferimento di Microsoft Azure IoT).
