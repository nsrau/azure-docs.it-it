---
title: Creare un dispositivo gateway trasparente - Azure IoT Edge | Microsoft Docs
description: Usare un dispositivo Azure IoT Edge per creare un gateway trasparente in grado di elaborare informazioni da dispositivi downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 506f6a2025a61b4d9d16918b2a95de620171c46b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147853"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurare un dispositivo IoT Edge come gateway trasparente

Questo articolo fornisce istruzioni dettagliate per la configurazione di un dispositivo IoT Edge per funzionare come gateway trasparente per la comunicazione di altri dispositivi con l'hub Internet. Questo articolo usa il termine *IOT Edge Gateway* per fare riferimento a un dispositivo IOT Edge configurato come gateway trasparente. Per altre informazioni, vedere [come è possibile usare un dispositivo IOT Edge come gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>Al momento:
>
> * I dispositivi abilitati per Edge non possono connettersi ai gateway IoT Edge.
> * I dispositivi downstream non possono usare il caricamento dei file.

Ci sono tre passaggi generali per configurare correttamente una connessione gateway trasparente. Questo articolo illustra il primo passaggio:

1. **Configurare il dispositivo gateway come server in modo che i dispositivi downstream possano connettersi in modo sicuro. Configurare il gateway per ricevere messaggi dai dispositivi downstream e instradarli alla destinazione corretta.**
2. Creare un'identità del dispositivo downstream in modo che sia in grado di eseguire l'autenticazione con l'hub Internet. Configurare il dispositivo downstream per l'invio di messaggi tramite il dispositivo gateway. Per informazioni su questi passaggi, vedere [autenticare un dispositivo downstream nell'hub Azure](how-to-authenticate-downstream-device.md).
3. Connettere il dispositivo downstream al dispositivo gateway e iniziare a inviare messaggi. Per informazioni su questi passaggi, vedere [connettere un dispositivo downstream a un gateway Azure IOT Edge](how-to-connect-downstream-device.md).

Perché un dispositivo funga da gateway, deve connettersi in modo sicuro ai dispositivi downstream. Azure IoT Edge permette di usare un'infrastruttura a chiave pubblica (PKI) per configurare connessioni sicure tra dispositivi. In questo caso, un dispositivo downstream è in grado di connettersi a un dispositivo IoT Edge che funge da gateway trasparente. Per garantire una ragionevole sicurezza, il dispositivo downstream deve confermare l'identità del dispositivo gateway. Questa verifica dell'identità impedisce ai dispositivi di connettersi a gateway potenzialmente dannosi.

Un dispositivo downstream può essere qualsiasi applicazione o piattaforma la cui identità sia stata creata con il servizio cloud [hub IoT di Azure](../iot-hub/index.yml). Queste applicazioni usano spesso Azure Internet per [dispositivi SDK](../iot-hub/iot-hub-devguide-sdks.md). Un dispositivo downstream potrebbe anche essere un'applicazione in esecuzione sul dispositivo IoT Edge Gateway. Tuttavia, un dispositivo IoT Edge non può essere a valle di un gateway di IoT Edge.

È possibile creare qualsiasi infrastruttura di certificati che abilita la relazione di trust necessaria per la topologia dispositivo-gateway. In questo articolo si presuppone la stessa configurazione del certificato usata per abilitare la sicurezza dell' [autorità di certificazione x. 509](../iot-hub/iot-hub-x509ca-overview.md) nell'hub Internet, che include un certificato della CA x. 509 associato a un hub di tutto il tempo (CA radice dell'hub), una serie di certificati firmati con questa CA e una CA per il dispositivo IOT Edge.

>[!NOTE]
>Il termine *certificato CA radice* usato in questi articoli si riferisce al certificato pubblico dell'autorità in primo piano della catena di certificati PKI e non necessariamente alla radice del certificato di un'autorità di certificazione del sindacato. In molti casi, si tratta in realtà di un certificato pubblico CA intermedio.

I passaggi seguenti illustrano il processo di creazione dei certificati e di installazione nei punti giusti del gateway. È possibile usare qualsiasi computer per generare i certificati e quindi copiarli nel dispositivo IoT Edge.

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Linux o Windows con IoT Edge installato.

Se non si dispone di un dispositivo pronto, è possibile crearne uno in una macchina virtuale di Azure. Seguire la procedura descritta in [distribuire il primo modulo di IOT Edge in un dispositivo Linux virtuale](quickstart-linux.md) per creare un hub Internet delle cose, creare una macchina virtuale e configurare l'IOT Edge Runtime. 

## <a name="set-up-the-device-ca-certificate"></a>Configurare il certificato della CA del dispositivo

Per tutti i gateway IoT Edge è necessario che sia installato un certificato CA del dispositivo. Il daemon di sicurezza IoT Edge usa il certificato della CA del dispositivo IoT Edge per firmare un certificato CA del carico di lavoro, che a sua volta firma un certificato del server per IoT Edge Hub. Il gateway presenta il certificato server al dispositivo downstream durante l'avvio della connessione. Il dispositivo downstream verifica che il certificato del server faccia parte di una catena di certificati che esegue il rollup del certificato CA radice. Questo processo consente al dispositivo downstream di verificare che il gateway provenga da una fonte attendibile. Per altre informazioni, vedere informazioni su [come Azure IOT Edge usa i certificati](iot-edge-certs.md).

![Installazione del certificato del gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

Il certificato CA radice e il certificato della CA del dispositivo (con la relativa chiave privata) devono essere presenti sul dispositivo gateway IoT Edge e configurati nel file IoT Edge config. yaml. Tenere presente che, in questo caso, il *certificato CA radice* indica l'autorità di certificazione più in alto per questo scenario IOT Edge. Il certificato CA del dispositivo gateway e i certificati del dispositivo downstream devono eseguire il rollup allo stesso certificato CA radice.

>[!TIP]
>Il processo di installazione del certificato CA radice e del certificato della CA del dispositivo in un dispositivo IoT Edge viene anche illustrato in modo più dettagliato in [gestire i certificati in un dispositivo IOT Edge](how-to-manage-device-certificates.md).

Preparare i file seguenti:

* Certificato CA radice
* Certificato CA del dispositivo
* Chiave privata della CA del dispositivo

Per gli scenari di produzione, è necessario generare questi file con la propria autorità di certificazione. Per gli scenari di sviluppo e test, è possibile usare i certificati demo.

1. Se si usano i certificati demo, seguire le istruzioni riportate in [creare certificati demo per testare le funzionalità del dispositivo IOT Edge](how-to-create-test-certificates.md) per creare i file. In questa pagina è necessario eseguire i passaggi seguenti:

   1. Per iniziare, configurare gli script per la generazione di certificati nel dispositivo.
   2. Creare un certificato CA radice. Al termine di queste istruzioni, sarà presente un file di certificato CA radice:
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.
   3. Creare IoT Edge certificati della CA del dispositivo. Al termine di queste istruzioni, si disporrà di un certificato della CA del dispositivo e della relativa chiave privata:
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem` e
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

2. Se i certificati sono stati creati in un altro computer, copiarli sul dispositivo IoT Edge.

3. Nel dispositivo IoT Edge aprire il file di configurazione del daemon di sicurezza.
   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

4. Trovare la sezione delle **impostazioni del certificato** del file. Rimuovere il commento dalle quattro righe che iniziano con i **certificati:** e fornire gli URI dei file ai tre file come valori per le proprietà seguenti:
   * **device_ca_cert**: certificato CA del dispositivo
   * **device_ca_pk**: chiave privata della CA del dispositivo
   * **trusted_ca_certs**: certificato CA radice

   Verificare che non siano presenti spazi vuoti precedenti nella riga **Certificates:** e che le altre righe siano rientrate da due spazi.

5. Salvare e chiudere il file.

6. Riavviare IoT Edge.
   * Windows: `Restart-Service iotedge`
   * Linux: `sudo systemctl restart iotedge`

## <a name="deploy-edgehub-and-route-messages"></a>Distribuire edgeHub e indirizzare i messaggi

I dispositivi downstream inviano dati di telemetria e messaggi al dispositivo gateway, dove il modulo Hub IoT Edge è responsabile del routing delle informazioni ad altri moduli o all'hub Internet. Per preparare il dispositivo gateway per questa funzione, verificare che:

* Il modulo Hub IoT Edge viene distribuito nel dispositivo.

  Quando si installa per la prima volta IoT Edge in un dispositivo, viene avviato automaticamente un solo modulo di sistema, ovvero l'agente di IoT Edge. Una volta creata la prima distribuzione per un dispositivo, viene avviato anche il secondo modulo di sistema, l'hub IoT Edge. Se il modulo **edgeHub** non è in esecuzione nel dispositivo, creare una distribuzione per il dispositivo.

* Il modulo Hub IoT Edge dispone di route impostate per gestire i messaggi in ingresso dai dispositivi downstream.

  Il dispositivo gateway deve disporre di una route per gestire i messaggi provenienti da dispositivi downstream. in caso contrario, tali messaggi non verranno elaborati. È possibile inviare i messaggi ai moduli nel dispositivo gateway o direttamente nell'hub Internet.

Per distribuire il modulo Hub IoT Edge e configurarlo con le route per gestire i messaggi in ingresso dai dispositivi downstream, attenersi alla procedura seguente:

1. Nel portale di Azure passare all'hub IoT.

2. Passare a **IoT Edge** e selezionare il dispositivo IoT Edge da usare come gateway.

3. Selezionare **imposta moduli**.

4. Nella pagina **moduli** è possibile aggiungere tutti i moduli che si vuole distribuire nel dispositivo gateway. Ai fini di questo articolo si è concentrati sulla configurazione e sulla distribuzione del modulo edgeHub, che non deve essere impostato in modo esplicito in questa pagina.

5. Selezionare **Avanti: Route**.

6. Nella pagina **Route** assicurarsi che esista una route per gestire i messaggi provenienti da dispositivi downstream. Ad esempio:

   * Una route che invia tutti i messaggi, sia da un modulo che da un dispositivo downstream, all'hub Internet:
       * **Nome**: `allMessagesToHub`
       * **Valore**: `FROM /messages/* INTO $upstream`

   * Una route che invia tutti i messaggi da tutti i dispositivi downstream all'hub Internet:
      * **Nome**: `allDownstreamToHub`
      * **Valore**: `FROM /messages/* WHERE NOT IS_DEFINED ($connectionModuleId) INTO $upstream`

      Questa route funziona perché, a differenza dei messaggi provenienti da moduli IoT Edge, ai messaggi dei dispositivi downstream non è associato alcun ID modulo. L'uso della clausola **where** della route consente di filtrare tutti i messaggi con tale proprietà di sistema.

      Per altre informazioni sul routing dei messaggi, vedere [distribuire moduli e stabilire le route](./module-composition.md#declare-routes).

7. Dopo aver creato la route o le route, selezionare **Verifica + crea**.

8. Nella pagina **Verifica e crea** selezionare **Crea**.

## <a name="open-ports-on-gateway-device"></a>Aprire le porte nel dispositivo gateway

I dispositivi IoT Edge standard non richiedono la connettività in ingresso per il funzionamento, perché tutte le comunicazioni con l'hub Internet viene eseguita tramite le connessioni in uscita. I dispositivi gateway sono diversi perché devono ricevere messaggi dai dispositivi downstream. Se un firewall è tra i dispositivi downstream e il dispositivo gateway, è necessario che anche la comunicazione sia possibile tramite il firewall.

Per il funzionamento di uno scenario del gateway, è necessario che almeno uno dei protocolli supportati dell'hub IoT Edge sia aperto per il traffico in ingresso dai dispositivi downstream. I protocolli supportati sono MQTT, AMQP, HTTPS, MQTT su WebSocket e AMQP su WebSocket.

| Porta | Protocollo |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato configurato un dispositivo IoT Edge come gateway trasparente, è necessario configurare i dispositivi downstream per considerare attendibile il gateway e inviare messaggi. Continuare con l' [autenticazione di un dispositivo downstream nell'hub Azure](how-to-authenticate-downstream-device.md) Internet per i passaggi successivi per la configurazione dello scenario di gateway trasparente.