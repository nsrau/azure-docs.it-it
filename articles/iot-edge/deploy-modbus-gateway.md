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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76511145"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Connettere dispositivi Modbus TCP tramite un dispositivo gateway IoT Edge

Se si vuole connettere i dispositivi Internet che usano protocolli Modbus TCP o RTU a un hub Azure, è possibile usare un dispositivo IoT Edge come gateway. Il dispositivo gateway legge i dati dai dispositivi Modbus, quindi li comunica al cloud usando un protocollo supportato.

![I dispositivi Modbus si connettono all'hub Internet tramite IoT Edge Gateway](./media/deploy-modbus-gateway/diagram.png)

Questo articolo descrive come creare un'immagine del contenitore personalizzata per un modulo Modbus (è anche possibile usare un esempio predefinito) e quindi distribuirla nel dispositivo IoT Edge che fungerà da gateway.

Questo articolo presuppone l'uso del protocollo Modbus TCP. Per altre informazioni su come configurare il modulo per supportare il protocollo Modbus RTU, vedere il progetto [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) (Modulo Modbus per Azure IoT Edge) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

* Un dispositivo Azure IoT Edge. Per una procedura dettagliata su come configurarne uno, vedere [distribuire Azure IOT Edge in Windows](quickstart.md) o [Linux](quickstart-linux.md).
* La chiave primaria della stringa di connessione del dispositivo IoT Edge.
* Un dispositivo Modbus fisico o simulato che supporta il protocollo Modbus TCP. Sarà necessario conoscerne l'indirizzo IPv4.

## <a name="prepare-a-modbus-container"></a>Preparare un contenitore Modbus

Se si vuole testare la funzionalità del gateway Modbus, Microsoft offre un modulo di esempio che può essere usato a questo scopo. È possibile accedere al modulo da Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)o con l'URI dell'immagine `mcr.microsoft.com/azureiotedge/modbus:1.0`.

Se si vuole creare un modulo e quindi personalizzarlo per l'ambiente, è disponibile un [modulo Modbus per Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) open source in GitHub. Seguire le indicazioni nel progetto per creare l'immagine del contenitore personalizzata. Per creare un'immagine del contenitore, vedere [sviluppare moduli C# in Visual Studio](how-to-visual-studio-develop-csharp-module.md) o [sviluppare moduli in Visual Studio Code](how-to-vs-code-develop-module.md). Questi articoli contengono istruzioni per la creazione di nuovi moduli e la pubblicazione di immagini del contenitore in un registro.

## <a name="try-the-solution"></a>Prova la soluzione

Questa sezione illustra la distribuzione del modulo Modbus di esempio di Microsoft nel dispositivo IoT Edge.

1. Nel [portale di Azure](https://portal.azure.com/) passare all'hub IoT.

2. Passare a **IoT Edge** e fare clic sul dispositivo IoT Edge.

3. Selezionare **imposta moduli**.

4. Nella sezione **moduli IOT Edge** aggiungere il modulo Modbus:

   1. Fare clic sull'elenco a discesa **Aggiungi** e selezionare **modulo Marketplace**.
   2. Cercare `Modbus` e selezionare il **modulo Modbus TCP** da Microsoft.
   3. Il modulo viene configurato automaticamente per l'hub Internet e viene visualizzato nell'elenco dei moduli IoT Edge. Anche le route vengono configurate automaticamente. Selezionare **Rivedi e crea**.
   4. Esaminare il manifesto della distribuzione e selezionare **Crea**.

5. Selezionare il modulo Modbus, `ModbusTCPModule`, nell'elenco e selezionare la scheda **impostazioni del dispositivo gemello del modulo** . Il codice JSON necessario per le proprietà desiderate del modulo gemello viene popolato automaticamente.

6. Cercare la proprietà **SlaveConnection** in JSON e impostarne il valore sull'indirizzo IPv4 del dispositivo Modbus.

7. Selezionare **Aggiorna**.

8. Selezionare **Verifica e crea**, esaminare la distribuzione e quindi selezionare **Crea**.

9. Tornare alla pagina dei dettagli del dispositivo e selezionare **Aggiorna**. Verrà visualizzato il nuovo `ModbusTCPModule` modulo in esecuzione insieme al runtime di IOT Edge.

## <a name="view-data"></a>Visualizzare i dati

Visualizzare i dati in arrivo tramite il modulo Modbus:

```cmd/sh
iotedge logs modbus
```

È anche possibile visualizzare i dati di telemetria inviati dal dispositivo usando l' [estensione dell'hub Azure Internet per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (in precedenza l'estensione Toolkit di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul modo in cui i dispositivi IoT Edge possono fungere da gateway, vedere [creare un dispositivo IOT Edge che funga da gateway trasparente](./how-to-create-transparent-gateway.md).
* Per ulteriori informazioni sul funzionamento dei moduli IoT Edge, vedere informazioni sui [moduli di Azure IOT Edge](iot-edge-modules.md).
