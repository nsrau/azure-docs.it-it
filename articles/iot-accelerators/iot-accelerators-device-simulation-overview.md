---
title: Panoramica di Simulazione dispositivi - Azure | Microsoft Docs
description: Descrizione dell'acceleratore di soluzione Simulazione dispositivi e delle sue funzionalità.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 173963470a77932186b3c51f17ccb406b32a13f3
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452225"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Panoramica dell'acceleratore di soluzione Simulazione dispositivi

In una soluzione IoT basata sul cloud i dispositivi si connettono a un endpoint cloud per inviare dati di telemetria come la temperatura, la posizione e lo stato. La soluzione utilizza questi dati di telemetria, consentendo all'utente di intraprendere le azioni appropriate o di ricavare informazioni dettagliate dai dati stessi.

La sperimentazione e il test sono componenti essenziali del processo di sviluppo di una soluzione IoT. La simulazione è uno strumento importante per l'intero processo. Con Simulazione dispositivi è possibile:

* Configurare rapidamente un prototipo e quindi eseguire l'iterazione adattando immediatamente il comportamento del dispositivo simulato. Questo processo consente di mettere alla prova la propria idea prima di investire in hardware costoso. È possibile creare dispositivi personalizzati tramite l'interfaccia utente Web per generare un prototipo di dispositivo in pochi secondi.
* Verificare che la soluzione funzioni come previsto dal dispositivo alla soluzione simulando i comportamenti dei dispositivi reali. È possibile creare script di comportamenti complessi del dispositivo usando JavaScript per generare dati di telemetria simulati in modo realistico.
* Testare la scalabilità della soluzione simulando condizioni di carico normali, di picco e oltre al picco. I test di scalabilità consentono anche di ridimensionare correttamente le risorse di Azure necessarie per l'esecuzione della soluzione.

![Esempio di simulazione di drone](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Con Simulazione dispositivi è possibile definire modelli di dispositivo per simulare i dispositivi reali in uso. Questo modello include formati di messaggio, proprietà dei dispositivi gemelli e metodi. È anche possibile simulare comportamenti complessi del dispositivo con JavaScript.

Le simulazioni possono essere eseguite per un solo dispositivo o per migliaia di dispositivi che si connettono a qualsiasi hub IoT. Per facilitare il testing, è possibile distribuire un hub IoT insieme a Simulazione dispositivi per un ambiente autonomo.

Simulazione dispositivi è una soluzione gratuita. Viene però distribuita nella sottoscrizione di Azure nel cloud e utilizza risorse di Azure. Se Simulazione dispositivi non soddisfa le proprie esigenze, il [codice sorgente è disponibile su GitHub](https://github.com/Azure/device-simulation-dotnet) e può essere copiato e modificato.

## <a name="sample-simulations"></a>Simulazioni di esempio

Quando si distribuisce Simulazione dispositivi, si ottengono alcuni dispositivi e simulazioni di esempio. È possibile usare questi esempi per imparare a usare Simulazione dispositivi. Per iniziare, eseguire una [simulazione di esempio che simula 10 veicoli](quickstart-device-simulation-deploy.md). È anche possibile [creare una simulazione personalizzata usando uno dei numerosi dispositivi di esempio disponibili](iot-accelerators-device-simulation-create-simulation.md).

![Configurazione della simulazione](media/iot-accelerators-device-simulation-overview/SampleSimulation.png)

## <a name="custom-simulated-devices"></a>Dispositivi simulati personalizzati

È possibile usare Simulazione dispositivi per [creare modelli di dispositivi personalizzati](iot-accelerators-device-simulation-create-custom-device.md) da usare nelle simulazioni. Si potrebbe ad esempio definire un nuovo modello di dispositivo frigorifero che invia dati di telemetria relativi alla temperatura e all'umidità. I dispositivi simulati personalizzati sono ideali per i comportamenti semplici con valori di telemetria casuali, incrementali o decrementali.

![Creare il modello di dispositivo](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Dispositivi simulati avanzati

Quando serve un maggiore controllo sui valori di telemetria inviati da un dispositivo, è possibile usare un modello di dispositivo avanzato. Questi modelli supportano JavaScript per la modifica dei valori di telemetria inviati. Si potrebbe ad esempio simulare la temperatura interna di un'auto parcheggiata in una calda giornata di sole: man mano che la temperatura esterna aumenta, la temperatura interna si alza in modo esponenziale.

I modelli di dispositivo avanzati consentono di [creare e caricare modelli di dispositivo personalizzati](iot-accelerators-device-simulation-advanced-device.md) costituiti da un file JSON di definizione di dispositivo e dai file JavaScript corrispondenti.

I modelli di dispositivo avanzati consentono di:

* Specificare il formato dei messaggi inviati dal dispositivo insieme ai tipi di telemetria.
* Usare script personalizzati che generano valori di telemetria che mantengono lo stato del dispositivo nel tempo.
* Usare script personalizzati per specificare la modalità di risposta del dispositivo simulato ai metodi.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati illustrati l'acceleratore di soluzione Simulazione dispositivi e le sue funzionalità. Per iniziare a usare l'acceleratore di soluzione, continuare con la guida di avvio rapido:

> [!div class="nextstepaction"]
> [Distribuire ed eseguire una soluzione di simulazione dei dispositivi IoT in Azure](quickstart-device-simulation-deploy.md)
