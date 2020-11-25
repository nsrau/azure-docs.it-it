---
title: Azure IoT Edge e IoT Central di Azure | Microsoft Docs
description: Informazioni su come usare Azure IoT Edge con un'applicazione IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: 4c4248246be845aa8608aac8b8a3ce761ea1ef86
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004773"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Connettere un dispositivo Azure IoT Edge a un'applicazione Azure IoT Central

*Le informazioni di questo articolo sono destinate a generatori di soluzioni e sviluppatori di dispositivi.*

IoT Edge è costituito da tra componenti:

* I **moduli di IoT Edge** sono contenitori che eseguono servizi di Azure, servizi partner o il codice dell'utente. Vengono distribuiti nei dispositivi IoT Edge ed eseguiti in locale in tali dispositivi.
* Il **runtime di IOT Edge** viene eseguito in ogni dispositivo IOT Edge e gestisce i moduli distribuiti in ogni dispositivo.
* Un'**interfaccia basata sul cloud** consente di monitorare e gestire in remoto i dispositivi IoT Edge. IoT Central è l'interfaccia cloud.

Un dispositivo **Azure IoT Edge** può essere un dispositivo gateway con dispositivi downstream che si connettono al dispositivo IoT Edge. Questo articolo condivide altre informazioni sui modelli di connettività dei dispositivi downstream.

Un **modello di dispositivo** definisce le funzionalità del dispositivo e dei moduli IoT Edge. Le funzionalità includono i dati di telemetria inviati dal modulo, le proprietà del modulo e i comandi a cui un modulo risponde.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relazioni dei dispositivi downstream con il gateway e i moduli

I dispositivi downstream possono connettersi al dispositivo gateway IoT Edge tramite il modulo `$edgeHub`. Questo dispositivo IoT Edge diventa un gateway trasparente in questo scenario.

![Diagramma del gateway trasparente](./media/concepts-iot-edge/gateway-transparent.png)

I dispositivi downstream possono anche connettersi al dispositivo gateway IoT Edge tramite un modulo personalizzato. Nello scenario seguente i dispositivi downstream si connettono tramite un modulo personalizzato Modbus.

![Diagramma della connessione del modulo personalizzato](./media/concepts-iot-edge/gateway-module.png)

Il diagramma seguente mostra la connessione a un dispositivo gateway IoT Edge tramite entrambi i tipi di moduli (personalizzato e `$edgeHub`).  

![Diagramma della connessione tramite entrambi i moduli di connessione](./media/concepts-iot-edge/gateway-module-transparent.png)

Infine, i dispositivi downstream possono connettersi al dispositivo gateway IoT Edge tramite più moduli personalizzati. Il diagramma seguente mostra i dispositivi downstream che si connettono tramite un modulo personalizzato Modbus, un modulo personalizzato BLE (Bluetooth a basso consumo) e il modulo `$edgeHub`. 

![Diagramma della connessione tramite più moduli personalizzati](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Manifesti di distribuzione e modelli di dispositivo

IoT Edge consente di distribuire e gestire la logica di business sotto forma di moduli. I moduli di IoT Edge sono l'unità di calcolo più piccola gestita da IoT Edge e possono contenere i servizi di Azure, ad esempio Analisi di flusso di Azure, o il codice specifico della soluzione. Per comprendere come vengono sviluppati, distribuiti e gestiti i moduli, vedere [Moduli di IoT Edge](../../iot-edge/iot-edge-modules.md).

A livello generale, un manifesto della distribuzione è un elenco di moduli gemelli configurati con le relative proprietà desiderate. Un manifesto della distribuzione indica a un dispositivo IoT Edge (o a un gruppo di dispositivi) quali moduli installare e come configurarli. I manifesti della distribuzione includono le proprietà desiderate per ogni modulo gemello. I dispositivi IoT Edge riportano le proprietà segnalate per ogni modulo.

Usare Visual Studio Code per creare un manifesto della distribuzione. Per altre informazioni, vedere [Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

In IoT Central di Azure è possibile importare un manifesto di distribuzione per creare un modello di dispositivo. Il diagramma di flusso seguente mostra il ciclo di vita del manifesto della distribuzione in IoT Central.

![Diagramma di flusso del ciclo di vita del manifesto della distribuzione](./media/concepts-iot-edge/dmflow.png)

IoT Central modella un dispositivo IoT Edge come segue:

* Ogni modello di dispositivo IoT Edge dispone di un modello di dispositivo.
* Per ogni modulo personalizzato elencato nel manifesto della distribuzione viene generato un modello di funzionalità del modulo.
* Viene stabilita una relazione tra ogni modello di capacità del modulo e un modello di dispositivo.
* Il modello di funzionalità del modulo implementa le interfacce del modulo.
* Ogni interfaccia del modulo contiene i dati di telemetria, le proprietà e i comandi.

![Diagramma della creazione del modello IoT Edge](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>Dispositivi IoT Edge Gateway

Se è stato selezionato un dispositivo IoT Edge come dispositivo gateway, è possibile aggiungere relazioni downstream ai modelli di dispositivo per i dispositivi che si desidera connettere al dispositivo gateway.

## <a name="next-steps"></a>Passaggi successivi

Se si è uno sviluppatore di dispositivi, un passaggio successivo suggerito consiste nell'apprendere i [tipi di dispositivi gateway in IOT Central](./tutorial-define-gateway-device-type.md).
