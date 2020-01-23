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
ms.openlocfilehash: bf60bfb41e48220845e9aa26dc26f20e6ed60d16
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510686"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurare un dispositivo IoT Edge come gateway trasparente

Questo articolo fornisce istruzioni dettagliate per la configurazione di un dispositivo IoT Edge per funzionare come gateway trasparente per la comunicazione di altri dispositivi con l'hub Internet. Questo articolo usa il termine *IOT Edge Gateway* per fare riferimento a un dispositivo IOT Edge configurato come gateway trasparente. Per altre informazioni, vedere [come è possibile usare un dispositivo IOT Edge come gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>Al momento:
>
> * I dispositivi abilitati per Edge non possono connettersi ai gateway IoT Edge.
> * I dispositivi downstream non possono usare il caricamento dei file.

Sono disponibili tre passaggi generali per configurare una connessione del gateway trasparente corretta. Questo articolo illustra il primo passaggio:

1. **Il dispositivo gateway deve essere in grado di connettersi in modo sicuro ai dispositivi downstream, ricevere comunicazioni dai dispositivi downstream e indirizzare i messaggi alla destinazione appropriata.**
2. Il dispositivo downstream deve avere un'identità del dispositivo per poter eseguire l'autenticazione con l'hub Internet e conoscere la comunicazione tramite il dispositivo gateway. Per altre informazioni, vedere [autenticare un dispositivo downstream nell'hub Azure](how-to-authenticate-downstream-device.md).
3. Il dispositivo downstream deve connettersi al dispositivo gateway in modo sicuro. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).

Per il funzionamento di un dispositivo come gateway, è necessario che sia in grado di connettersi in modo sicuro ai dispositivi downstream. Azure IoT Edge permette di usare un'infrastruttura a chiave pubblica (PKI) per configurare connessioni sicure tra dispositivi. Nel caso illustrato si consente la connessione di un dispositivo downstream a un dispositivo IoT Edge che funge da gateway trasparente. Per garantire una ragionevole sicurezza, il dispositivo downstream deve confermare l'identità del dispositivo gateway. Questa verifica dell'identità impedisce ai dispositivi di connettersi a gateway potenzialmente dannosi.

Un dispositivo downstream in uno scenario di gateway trasparente può essere qualsiasi applicazione o piattaforma con un'identità creata con il servizio cloud dell' [Hub Azure](https://docs.microsoft.com/azure/iot-hub) . In molti casi, queste applicazioni usano [Azure IoT SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md). Per scopi pratici, un dispositivo downstream può anche essere un'applicazione in esecuzione nel dispositivo gateway IoT Edge stesso. Tuttavia, un dispositivo IoT Edge non può essere a valle di un gateway di IoT Edge.

È possibile creare qualsiasi infrastruttura di certificati che abilita la relazione di trust necessaria per la topologia dispositivo-gateway. In questo articolo si presuppone la stessa configurazione del certificato usata per abilitare la sicurezza dell' [autorità di certificazione x. 509](../iot-hub/iot-hub-x509ca-overview.md) nell'hub Internet, che include un certificato della CA x. 509 associato a un hub di tutto il tempo (CA radice dell'hub), una serie di certificati firmati con questa CA e una CA per il dispositivo IOT Edge.

![Installazione del certificato del gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Il termine "CA radice" usato in questo articolo si riferisce al certificato pubblico dell'autorità in primo piano della catena di certificati PKI e non necessariamente alla radice del certificato di un'autorità di certificazione del sindacato. In molti casi, si tratta in realtà di un certificato pubblico CA intermedio.

Il gateway presenta il certificato della CA del dispositivo IoT Edge al dispositivo downstream durante l'avvio della connessione. Il dispositivo downstream verifica che il certificato della CA del dispositivo IoT Edge sia firmato dal certificato CA radice. Questo processo consente al dispositivo downstream di verificare che il gateway provenga da una fonte attendibile.

I passaggi seguenti illustrano il processo di creazione dei certificati e di installazione nei punti giusti del gateway. È possibile usare qualsiasi computer per generare i certificati e quindi copiarli nel dispositivo IoT Edge.

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge, configurato con [certificati di produzione](how-to-install-production-certificates.md).

## <a name="deploy-edgehub-to-the-gateway"></a>Distribuire edgeHub nel gateway

Quando si installa per la prima volta IoT Edge in un dispositivo, viene avviato automaticamente un solo modulo di sistema, ovvero l'agente di IoT Edge. Una volta creata la prima distribuzione di un dispositivo, viene avviato anche il secondo modulo di sistema, l'hub IoT Edge.

L'hub IoT Edge è responsabile della ricezione dei messaggi in ingresso dai dispositivi downstream e del relativo routing alla destinazione successiva. Se il modulo **edgeHub** non è in esecuzione nel dispositivo, creare una distribuzione iniziale per il dispositivo. La distribuzione sarà vuota perché non vengono aggiunti moduli, ma si assicurerà che entrambi i moduli di sistema siano in esecuzione.

È possibile verificare quali moduli sono in esecuzione in un dispositivo controllando i dettagli del dispositivo nel portale di Azure, visualizzando lo stato del dispositivo in Visual Studio o Visual Studio Code oppure eseguendo il comando `iotedge list` sul dispositivo stesso.

Se il modulo **edgeAgent** è in esecuzione senza il modulo **edgeHub** , attenersi alla procedura seguente:

1. Nel portale di Azure passare all'hub IoT.

2. Passare a **IoT Edge** e selezionare il dispositivo IoT Edge da usare come gateway.

3. Selezionare **Set Modules** (Configura i moduli).

4. Selezionare **Avanti**.

5. Nel passaggio **Specifica route** deve essere indicata una route predefinita che invia tutti i messaggi da tutti i moduli all'hub IoT. In caso contrario, aggiungere il codice seguente e quindi selezionare **Avanti**.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. Nel passaggio **Rivedi modello** selezionare **Invia**.

## <a name="open-ports-on-gateway-device"></a>Aprire le porte nel dispositivo gateway

I dispositivi IoT Edge standard non richiedono la connettività in ingresso per il funzionamento, perché tutte le comunicazioni con l'hub Internet viene eseguita tramite le connessioni in uscita. I dispositivi gateway sono diversi perché devono ricevere messaggi dai dispositivi downstream. Se un firewall è tra i dispositivi downstream e il dispositivo gateway, è necessario che anche la comunicazione sia possibile tramite il firewall.

Per il funzionamento di uno scenario del gateway, è necessario che almeno uno dei protocolli supportati dell'hub IoT Edge sia aperto per il traffico in ingresso dai dispositivi downstream. I protocolli supportati sono MQTT, AMQP, HTTPS, MQTT su WebSocket e AMQP su WebSocket.

| Porta | Protocollo |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>Instradare i messaggi da dispositivi downstream

Il runtime IoT Edge può instradare i messaggi inviati dai dispositivi downstream come i messaggi inviati dai moduli. Questa funzionalità consente di eseguire analisi in un modulo in esecuzione sul gateway prima di inviare dati al cloud.

Attualmente, il modo per instradare i messaggi inviati dai dispositivi downstream consiste nel differenziarli dai messaggi inviati dai moduli. I messaggi inviati da tutti i moduli contengono una proprietà di sistema denominata **connectionModuleId** ma non i messaggi inviati dai dispositivi downstream. È possibile utilizzare la clausola WHERE della route da escludere eventuali messaggi che contengono tale proprietà di sistema.

La route seguente è un esempio che consente di inviare messaggi da qualsiasi dispositivo downstream a un modulo denominato `ai_insights`, quindi da `ai_insights` a hub Internet.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Per altre informazioni sul routing dei messaggi, vedere [distribuire moduli e stabilire le route](./module-composition.md#declare-routes).

## <a name="enable-extended-offline-operation"></a>Abilita operazione offline estesa

A partire dalla [versione 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) del runtime di IOT Edge, è possibile configurare il dispositivo gateway e i dispositivi downstream che vi si connettono per l'operazione offline estesa.

Con questa funzionalità, i moduli locali o i dispositivi downstream possono eseguire di nuovo l'autenticazione con il dispositivo IoT Edge in base alle esigenze e comunicare tra loro usando messaggi e metodi anche quando sono disconnessi dall'hub. Per altre informazioni, vedere [Informazioni sulle funzionalità per periodi offline prolungati per i dispositivi IoT Edge, i moduli e i dispositivi figlio](offline-capabilities.md).

Per abilitare le funzionalità estese offline, è necessario stabilire una relazione padre-figlio tra un dispositivo gateway IoT Edge e i dispositivi downstream a cui si connetterà. Questi passaggi sono illustrati in modo più dettagliato in [eseguire l'autenticazione di un dispositivo downstream nell'hub Azure](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un dispositivo IoT Edge che opera come gateway trasparente, è necessario configurare i dispositivi downstream in modo da ritenere attendibile il gateway e inviarvi messaggi. Continuare con l' [autenticazione di un dispositivo downstream nell'hub Azure](how-to-authenticate-downstream-device.md) Internet per i passaggi successivi per la configurazione dello scenario di gateway trasparente.
