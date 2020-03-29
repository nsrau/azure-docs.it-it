---
title: Convertire i protocolli Modbus con i gateway - Azure IoT Edge | Microsoft Docs
description: Consentire ai dispositivi che usano Modbus TCP di comunicare con l'hub IoT di Azure creando un dispositivo gateway IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 23fbbd87230ea0a0147dc9d90c77729f4d531e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511145"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Connettere dispositivi Modbus TCP tramite un dispositivo gateway IoT Edge

Se si vogliono connettere dispositivi IoT che usano protocolli Modbus TCP o RTU a un hub IoT di Azure, è possibile usare un dispositivo IoT Edge come gateway. Il dispositivo gateway legge i dati dai dispositivi Modbus, quindi li comunica al cloud usando un protocollo supportato.

![I dispositivi Modbus si connettono all'hub IoT tramite il gateway IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Questo articolo descrive come creare un'immagine del contenitore personalizzata per un modulo Modbus (è anche possibile usare un esempio predefinito) e quindi distribuirla nel dispositivo IoT Edge che fungerà da gateway.

Questo articolo presuppone l'uso del protocollo Modbus TCP. Per altre informazioni su come configurare il modulo per supportare il protocollo Modbus RTU, vedere il progetto [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) (Modulo Modbus per Azure IoT Edge) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

* Un dispositivo Azure IoT Edge. Per una procedura dettagliata su come configurarne uno, vedere [Distribuire Azure IoT Edge in Windows](quickstart.md) o [Linux.](quickstart-linux.md)
* La chiave primaria della stringa di connessione del dispositivo IoT Edge.
* Un dispositivo Modbus fisico o simulato che supporta il protocollo Modbus TCP. È necessario conoscere il relativo indirizzo IPv4.

## <a name="prepare-a-modbus-container"></a>Preparare un contenitore Modbus

Se si vuole testare la funzionalità del gateway Modbus, Microsoft offre un modulo di esempio che può essere usato a questo scopo. È possibile accedere al modulo da Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)o con l'URI dell'immagine, `mcr.microsoft.com/azureiotedge/modbus:1.0`.

Se si vuole creare un modulo e quindi personalizzarlo per l'ambiente, è disponibile un [modulo Modbus per Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) open source in GitHub. Seguire le indicazioni nel progetto per creare l'immagine del contenitore personalizzata. Per creare un'immagine contenitore, fare riferimento a [Sviluppare i moduli in Visual Studio o](how-to-visual-studio-develop-csharp-module.md) Sviluppare moduli in Visual Studio [Code](how-to-vs-code-develop-module.md). Questi articoli forniscono istruzioni sulla creazione di nuovi moduli e sulla pubblicazione di immagini del contenitore in un Registro di sistema.

## <a name="try-the-solution"></a>Prova la soluzione

Questa sezione illustra come distribuire il modulo Modbus di esempio di Microsoft nel dispositivo IoT Edge.This section walks through deploying Microsoft's sample Modbus module to your IoT Edge device.

1. Nel [portale di Azure](https://portal.azure.com/) passare all'hub IoT.

2. Passare a **IoT Edge** e fare clic sul dispositivo IoT Edge.

3. Selezionare **Imposta moduli**.

4. Nella sezione **Moduli Edge IoT** aggiungere il modulo Modbus:

   1. Fare clic sull'elenco a discesa **Aggiungi** e selezionare **Modulo Marketplace**.
   2. Cercare `Modbus` e selezionare il **modulo Tcp Modbus** di Microsoft.
   3. Il modulo viene configurato automaticamente per l'hub IoT e viene visualizzato nell'elenco dei moduli Edge IoT. Anche i percorsi vengono configurati automaticamente. Selezionare **Revisione e creazione**.
   4. Esaminare il manifesto di distribuzione e selezionare **Crea**.

5. Selezionare il modulo `ModbusTCPModule`Modbus , , nell'elenco e selezionare la scheda **Impostazioni modulo twin.** Il codice JSON richiesto per le proprietà desiderate del modulo gemello viene popolato automaticamente.

6. Cercare la proprietà **SlaveConnection** in JSON e impostarne il valore sull'indirizzo IPv4 del dispositivo Modbus.

7. Selezionare **Aggiorna**.

8. Selezionare **Revisione e creazione**, esaminare la distribuzione e quindi **selezionare Crea**.

9. Tornare alla pagina dei dettagli del dispositivo e selezionare **Aggiorna**. Verrà visualizzato il `ModbusTCPModule` nuovo modulo in esecuzione insieme al runtime IoT Edge.You should see the new module running along with the IoT Edge runtime.

## <a name="view-data"></a>Visualizzare i dati

Visualizzare i dati provenienti dal modulo Modbus:

```cmd/sh
iotedge logs modbus
```

È anche possibile visualizzare i dati di telemetria inviati dal dispositivo usando [l'estensione dell'hub IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) di Azure per il codice di Visual Studio (in precedenza estensione Azure IoT Toolkit).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come i dispositivi IoT Edge possono fungere da gateway, vedere [Creare un dispositivo IoT Edge che funga da gateway trasparente.](./how-to-create-transparent-gateway.md)
* Per altre informazioni sul funzionamento dei moduli IoT Edge, vedere [Informazioni sui moduli di Azure IoT Edge.](iot-edge-modules.md)
