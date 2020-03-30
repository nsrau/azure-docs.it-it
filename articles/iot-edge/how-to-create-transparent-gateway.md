---
title: Creare un dispositivo gateway trasparente - Azure IoT Edge | Microsoft Docs
description: Usare un dispositivo Azure IoT Edge per creare un gateway trasparente in grado di elaborare informazioni da dispositivi downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6069e0782f69d0dfb73d9be2998cbb11d59d7d22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529170"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurare un dispositivo IoT Edge come gateway trasparente

Questo articolo fornisce istruzioni dettagliate per configurare un dispositivo IoT Edge in modo che funzioni come gateway trasparente per la comunicazione di altri dispositivi con l'hub IoT. In questo articolo viene utilizzato il termine *gateway Edge IoT* per fare riferimento a un dispositivo IoT Edge configurato come gateway trasparente. Per ulteriori informazioni, vedere [Come utilizzare un dispositivo Edge IoT come gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>Al momento:
>
> * I dispositivi abilitati per Edge non possono connettersi ai gateway IoT Edge.
> * I dispositivi downstream non possono usare il caricamento dei file.

Esistono tre passaggi generali per configurare una connessione gateway trasparente di successo. In questo articolo viene illustrato il primo passaggio:This article covers the first step:

1. **Il dispositivo gateway deve essere in grado di connettersi in modo sicuro ai dispositivi downstream, ricevere comunicazioni dai dispositivi downstream e instradare i messaggi alla destinazione appropriata.**
2. Il dispositivo downstream deve avere un'identità del dispositivo per poter eseguire l'autenticazione con l'hub IoT e sapere per comunicare tramite il dispositivo gateway. Per altre informazioni, vedere Autenticare un dispositivo downstream nell'hub IoT di Azure.For more information, see [Authenticate a downstream device to Azure IoT Hub.](how-to-authenticate-downstream-device.md)
3. Il dispositivo downstream deve connettersi al dispositivo gateway in modo sicuro. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).

Affinché un dispositivo funzioni come gateway, deve essere in grado di connettersi in modo sicuro ai propri dispositivi downstream. Azure IoT Edge permette di usare un'infrastruttura a chiave pubblica (PKI) per configurare connessioni sicure tra dispositivi. Nel caso illustrato si consente la connessione di un dispositivo downstream a un dispositivo IoT Edge che funge da gateway trasparente. Per mantenere una sicurezza ragionevole, il dispositivo downstream deve confermare l'identità del dispositivo gateway. Questo controllo dell'identità impedisce ai dispositivi di connettersi a gateway potenzialmente dannosi.

Un dispositivo downstream in uno scenario gateway trasparente può essere qualsiasi applicazione o piattaforma con un'identità creata con il servizio cloud Hub IoT di Azure.A downstream device in a transparent gateway scenario can be any application or platform that has an identity created with the [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) cloud service. In molti casi, queste applicazioni usano [Azure IoT SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md). Per scopi pratici, un dispositivo downstream può anche essere un'applicazione in esecuzione nel dispositivo gateway IoT Edge stesso. Tuttavia, un dispositivo IoT Edge non può essere a valle di un gateway IoT Edge.However, an IoT Edge device cannot be downstream of an IoT Edge gateway.

È possibile creare qualsiasi infrastruttura di certificati che abilita la relazione di trust necessaria per la topologia dispositivo-gateway. In questo articolo si presuppone la stessa configurazione di certificato che si utilizzerebbe per abilitare la sicurezza della [CA X.509](../iot-hub/iot-hub-x509ca-overview.md) nell'hub IoT, che coinvolge un certificato ca X.509 associato a un hub IoT specifico (la CA radice dell'hub IoT), una serie di certificati firmati con questa CA e una CA per il dispositivo Edge IoT.

![Installazione del certificato del gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Il termine "CA radice" utilizzato in questo articolo si riferisce al certificato pubblico dell'autorità superiore della catena di certificati PKI e non necessariamente alla radice del certificato di un'autorità di certificazione su diversi termini. In molti casi, si tratta in realtà di un certificato pubblico CA intermedio.

Il gateway presenta il certificato CA del dispositivo IoT Edge al dispositivo downstream durante l'avvio della connessione. Il dispositivo downstream verifica che il certificato CA del dispositivo IoT Edge sia firmato dal certificato della CA radice. Questo processo consente al dispositivo downstream di verificare che il gateway provenga da una fonte attendibile.

I passaggi seguenti illustrano il processo di creazione e installazione dei certificati nelle posizioni corrette nel gateway. È possibile usare qualsiasi computer per generare i certificati e quindi copiarli nel dispositivo IoT Edge.

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge, configurato con certificati di [produzione.](how-to-manage-device-certificates.md)

## <a name="deploy-edgehub-to-the-gateway"></a>Distribuire edgeHub nel gatewayDeploy edgeHub to the gateway

Quando si installa IoT Edge per la prima volta in un dispositivo, viene avviato automaticamente un solo modulo di sistema: l'agente IoT Edge. Dopo aver creato la prima distribuzione, viene avviato anche il secondo modulo di sistema, l'hub IoT Edge.

L'hub IoT Edge è responsabile della ricezione dei messaggi in arrivo dai dispositivi downstream e del routing alla destinazione successiva. Se il modulo **edgeHub** non è in esecuzione nel dispositivo, creare una distribuzione iniziale per il dispositivo. La distribuzione sarà vuota perché non si aggiungono moduli, ma si assicurerà che entrambi i moduli di sistema siano in esecuzione.

È possibile verificare quali moduli sono in esecuzione in un dispositivo controllando i dettagli del dispositivo nel portale `iotedge list` di Azure, visualizzando lo stato del dispositivo in Visual Studio o Visual Studio Code o eseguendo il comando nel dispositivo stesso.

Se il modulo **edgeAgent** è in esecuzione senza il modulo **edgeHub,** attenersi alla seguente procedura:

1. Nel portale di Azure passare all'hub IoT.

2. Passare a **IoT Edge** e selezionare il dispositivo IoT Edge da usare come gateway.

3. Selezionare **Set Modules** (Configura i moduli).

4. Fare clic su **Avanti**.

5. Nel passaggio **Specifica route** deve essere indicata una route predefinita che invia tutti i messaggi da tutti i moduli all'hub IoT. In caso contrario, aggiungere il codice seguente e quindi selezionare **Avanti**.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. Nel passaggio **Rivedi modello** selezionare **Invia**.

## <a name="open-ports-on-gateway-device"></a>Aprire le porte sul dispositivo gateway

I dispositivi IoT Edge standard non richiedono alcuna connettività in ingresso per funzionare, perché tutte le comunicazioni con l'hub IoT vengono eseguite tramite connessioni in uscita. I dispositivi gateway sono diversi perché devono ricevere messaggi dai dispositivi downstream. Se un firewall si trova tra i dispositivi downstream e il dispositivo gateway, la comunicazione deve essere possibile anche attraverso il firewall.

Affinché uno scenario gateway funzioni, almeno uno dei protocolli supportati dall'hub IoT Edge deve essere aperto per il traffico in ingresso dai dispositivi downstream. I protocolli supportati sono MQTT, AMQP, HTTPS, MQTT su WebSockets e AMQP su WebSockets.

| Porta | Protocollo |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT -WS <br> AMQP-WS (AZIONE DI UNQP) |

## <a name="route-messages-from-downstream-devices"></a>Instradare i messaggi da dispositivi downstream

Il runtime IoT Edge può instradare i messaggi inviati dai dispositivi downstream come i messaggi inviati dai moduli. Questa funzionalità consente di eseguire analisi in un modulo in esecuzione sul gateway prima di inviare dati al cloud.

Attualmente, il modo per instradare i messaggi inviati dai dispositivi downstream consiste nel differenziarli dai messaggi inviati dai moduli. I messaggi inviati da tutti i moduli contengono una proprietà di sistema denominata **connectionModuleId** ma non i messaggi inviati dai dispositivi downstream. È possibile utilizzare la clausola WHERE della route da escludere eventuali messaggi che contengono tale proprietà di sistema.

La route seguente è un esempio che invia messaggi da `ai_insights`qualsiasi dispositivo `ai_insights` downstream a un modulo denominato , quindi da all'hub IoT.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Per altre informazioni sul routing dei messaggi, vedere [distribuire moduli e stabilire le route](./module-composition.md#declare-routes).

## <a name="enable-extended-offline-operation"></a>Abilita funzionamento offline esteso

A partire dalla [versione v1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) del runtime IoT Edge, il dispositivo gateway e i dispositivi downstream che si connettono ad esso possono essere configurati per il funzionamento offline esteso.

Con questa funzionalità, i moduli locali o i dispositivi downstream possono eseguire nuovamente l'autenticazione con il dispositivo IoT Edge in base alle esigenze e comunicare tra loro usando messaggi e metodi anche quando si è disconnessi dall'hub IoT. Per altre informazioni, vedere [Informazioni sulle funzionalità per periodi offline prolungati per i dispositivi IoT Edge, i moduli e i dispositivi figlio](offline-capabilities.md).

Per abilitare le funzionalità offline estese, si stabilisce una relazione padre-figlio tra un dispositivo gateway IoT Edge e dispositivi downstream che si connetteranno a esso. Questi passaggi sono illustrati in modo più dettagliato in Autenticare un dispositivo downstream nell'hub IoT di Azure.Those steps are explained in more detail in [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un dispositivo IoT Edge che opera come gateway trasparente, è necessario configurare i dispositivi downstream in modo da ritenere attendibile il gateway e inviarvi messaggi. Continuare con [L'autenticazione di un dispositivo downstream nell'hub IoT](how-to-authenticate-downstream-device.md) di Azure per i passaggi successivi nella configurazione dello scenario del gateway trasparente.
