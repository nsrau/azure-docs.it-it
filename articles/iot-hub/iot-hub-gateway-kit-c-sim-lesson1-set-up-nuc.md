---
title: Configurare NUC Intel come gateway IoT di Azure | Microsoft Docs
description: Impostare Intel NUC come gateway IoT tra un sensore e un IoT Hub di Azure per raccogliere informazioni sul sensore e inviarlo a IoT Hub.
services: iot-hub
documentationcenter: 
author: shizn
manager: yjianfeng
tags: 
keywords: gateway IOT, intel nuc, nuc computer, DE3815TYKE
ms.assetid: f41d6b2e-9b00-40df-90eb-17d824bea883
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 1c4f44787a7200a1c3634b258df32d30152daa90
ms.openlocfilehash: 7725f49d71cb77dd6ff7ae075cc7449e568c21d7


---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>Configurare Intel NUC come gateway IoT

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Configurare Intel NUC come gateway IoT.
- Installare il pacchetto Azure IoT Gateway SDK in Intel NUC.
- Eseguire un'applicazione di esempio "hello_world" su Intel NUC per verificare la funzionalità del gateway.
In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione

In questa lezione si apprenderà:

- Come connettere Intel NUC con le periferiche.
- Come installare e aggiornare i pacchetti richiesti in Intel NUC tramite Gestione pacchetti Smart.
- Eseguire un'applicazione di esempio "hello_world" per verificare la funzionalità del gateway.

## <a name="what-you-need"></a>Elementi necessari

- Un Kit DE3815TYKE Intel NUC con la Suite di Software Gateway IoT Intel (Wind River Linux * 7.0.0.13) preinstallato.
- Un cavo Ethernet.
- Una tastiera.
- Un cavo VGA o HDMI.
- Un monitor con una porta HDMI o VGA.

![Kit gateway](media/iot-hub-gateway-kit-lessons/lesson1/kit_without_sensortag.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>Come connettere Intel NUC con le periferiche

Nell'immagine seguente è riportato un esempio di Intel NUC connesso con diverse periferiche:

1. Connesso a una tastiera.
2. Connesso al monitor con un cavo VGA o HDMI.
3. Connesso a una rete cablata tramite un cavo Ethernet.
4. Connesso con un cavo di alimentazione alla presa.

![Intel NUC connesso alle periferiche](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>Connettersi al sistema Intel NUC dal computer host tramite Secure Shell (SSH)

Qui occorrono tastiera e monitor per ottenere l'indirizzo IP del dispositivo NUC. Se già si conosce l'indirizzo IP, è possibile procedere al passaggio 3 di questa sezione.

1. Attivare Intel NUC premendo il pulsante di alimentazione e di log nel sistema.

   Il nome utente e la password predefiniti sono entrambi `root`.

2. Ottenere l'indirizzo IP del NUC eseguendo il comando `ifconfig`. Questo passaggio viene eseguito sul dispositivo NUC.

   Di seguito è riportato un esempio di output del comando.

   ![output ifconfig con NUC IP](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   In questo esempio, il valore che segue `inet addr:` è l'indirizzo IP necessario se si prevede di connettersi in remoto da un computer host a Intel NUC.

3. Utilizzare uno dei seguenti client SSH dalla macchina host per connettersi a Intel NUC.

   - [PuTTY](http://www.putty.org/) per Windows.
   - Il client SSH incorporato in Ubuntu o macOS.

   È più efficace e produttivo operare su Intel NUC da un computer host. Per la connessione del NUC tramite client SSH sono necessari indirizzo IP, nome utente e password. Di seguito è riportato un esempio di client SSH con MacOS.
   ![Client SSH in esecuzione su MacOS](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-gateway-sdk-package"></a>Installare il pacchetto Azure IoT Gateway SDK

Il pacchetto Azure IoT Gateway SDK contiene i file binari precompilati di SDK e le relative dipendenze. Questi file binari sono Azure IoT Gateway SDK, Azure IoT SDK e gli strumenti corrispondenti. Il pacchetto contiene anche un'applicazione di esempio "hello_world" utilizzata per convalidare la funzionalità del gateway. L'SDK è la parte principale del gateway. Per installare il pacchetto, seguire questi passaggi:

1. Aggiungere l'archivio cloud IoT eseguendo i comandi sotto riportati in una finestra del terminale:

   ```bash
   rpm --import http://iotdk.intel.com/misc/iot_pub.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   ```

   Il comando `rpm` importa la chiave rpm. Il comando `smart channel` aggiunge il canale rpm a Smart Package Manager. Prima dell'esecuzione del comando `smart update`, viene visualizzato un output come quello seguente.

   ![Output di comandi del canale smart e rpm](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

   ```bash
   smart update
   ```

2. Installare il pacchetto eseguendo il comando seguente:

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure` è il nome del pacchetto. Il comando `smart install` viene utilizzato per installare il pacchetto.

   Dopo l'installazione del pacchetto, Intel NUC dovrebbe funzionare come gateway.

## <a name="run-the-azure-iot-gateway-sdk-helloworld-sample-application"></a>Eseguire l'applicazione di esempio "hello_world" Gateway IoT di Azure SDK

Passare a `azureiotgatewaysdk/samples` ed eseguire l'applicazione di esempio "hello_world". Questa applicazione di esempio crea un gateway dal file `hello_world.json` e utilizza i componenti fondamentali dell'architettura Gateway IoT Azure SDK per registrare il messaggio hello world in un file ogni 5 secondi.

È possibile eseguire l'applicazione di esempio "hello_world" con questo comando:

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

Se la funzionalità di gateway funziona correttamente, l'applicazione di esempio produce il seguente output:

![output dell'applicazione](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="summary"></a>Riepilogo

Congratulazioni. La configurazione di Intel NUC come gateway è terminata. È ora possibile passare alla lezione successiva per configurare il computer host, creare un hub IoT di Azure e registrare il dispositivo logico hub IoT di Azure.

## <a name="next-steps"></a>Passaggi successivi
[Preparare il computer host e l'hub IoT di Azure](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)



<!--HONumber=Dec16_HO3-->


