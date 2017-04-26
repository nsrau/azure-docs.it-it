---
title: 'Dispositivo SensorTag e gateway Azure IoT: lezione 1: Configurare Intel NUC | Microsoft Docs'
description: Configurare Intel NUC come gateway IoT di Azure tra un sensore e l&quot;hub IoT di Azure, per raccogliere informazioni sul sensore e inviarle a IoT Hub.
services: iot-hub
documentationcenter: 
author: shizn
manager: yjianfeng
tags: 
keywords: gateway iot, intel nuc, computer nuc, DE3815TYKE
ms.assetid: 917090d6-35c2-495b-a620-ca6f9c02b317
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 4c6b70d793a7d734f5a29139e1f0b91f0d41e73a
ms.lasthandoff: 04/12/2017


---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>Configurare Intel NUC come gateway IoT di Azure
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Configurare Intel NUC come gateway IoT di Azure.
- Installare il pacchetto Azure IoT Gateway SDK in Intel NUC.
- Eseguire un'applicazione di esempio "hello_world" in Intel NUC per verificare la funzionalità del gateway.

  > In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione

In questa lezione si apprenderà:

- Come collegare Intel NUC alle periferiche.
- Come installare e aggiornare i pacchetti richiesti su Intel NUC tramite Smart Package Manager.
- Come eseguire l'applicazione di esempio "hello_world" per verificare la funzionalità del gateway.

## <a name="what-you-need"></a>Elementi necessari

- Intel NUC Kit DE3815TYKE con Intel IoT Gateway Software Suite (Wind River Linux *7.0.0.13) preinstallata. [Fare clic qui per acquistare il Kit gateway commerciale Grove IoT](https://www.seeedstudio.com/Grove-IoT-Commercial-Gateway-Kit-p-2724.html).
- Un cavo Ethernet.
- Una tastiera.
- Un cavo HDMI o VGA.
- Un monitor con porta HDMI o VGA.
- Facoltativo: [SensorTag di Texas Instruments (CC2650STK)](http://www.ti.com/tool/cc2650stk)

![Kit gateway](media/iot-hub-gateway-kit-lessons/lesson1/kit.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>Come connettere Intel NUC con le periferiche

Nell'immagine seguente è riportato un esempio di dispositivo Intel NUC collegato a diverse periferiche:

1. A una tastiera.
2. A un monitor tramite un cavo VGA o HDMI.
3. A una rete cablata tramite cavo Ethernet.
4. A un alimentatore tramite un cavo di alimentazione.

![Dispositivo Intel NUC collegato a periferiche](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>Connettere il sistema Intel NUC dal computer host tramite Secure Shell (SSH)

Sono necessari una tastiera e un monitor per ottenere l'indirizzo IP del dispositivo Intel NUC. Se si conosce già l'indirizzo IP, è possibile andare al passaggio 3 di questa sezione.

1. Accendere il dispositivo Intel NUC premendo l'apposito tasto ed eseguire l'accesso.

   Il nome utente e la password predefiniti sono entrambi `root`.

       > Hit the enter key on your keyboard if you see either of the following errors when you boot: 'A TPM error (7) occurred attempting to read a pcr value.' or 'Timeout, No TPM chip found, activating TPM-bypass!'

2. Ottenere l'indirizzo IP del dispositivo Intel NUC eseguendo da esso il comando `ifconfig`. 

   Di seguito è riportato un esempio dell'output del comando.

   ![Output di ifconfig che indica l'IP di Intel NUC](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   In questo esempio, il valore che segue `inet addr:` è l'indirizzo IP necessario per connettersi da un computer host a Intel NUC.

3. Per connettersi a Intel NUC, usare uno dei client SSH seguenti dal computer host.

    - [PuTTY](http://www.putty.org/) per Windows.
    - Il client SSH incorporato in Ubuntu o macOS.

   È più efficiente e produttivo lavorare su un dispositivo Intel NUC da un computer host. Per connettersi al dispositivo Intel NUC tramite il client SSH, sono necessari indirizzo IP, nome utente e password. Di seguito è riportato un esempio che usa un client SSH in macOS.
   ![Client SSH eseguito su macOS](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-gateway-sdk-package"></a>Installare il pacchetto Azure IoT Gateway SDK

Il pacchetto Azure IoT Gateway SDK contiene i file binari precompilati dell'SDK e le relative dipendenze. Tali file binari sono Azure IoT Gateway SDK, Azure IoT SDK e agli strumenti corrispondenti. Il pacchetto contiene anche un'applicazione di esempio "hello_world" utilizzata per convalidare la funzionalità del gateway. L'SDK è la parte principale del gateway. 

Per installare il pacchetto, seguire questa procedura.

1. Aggiungere il repository cloud IoT eseguendo questi comandi nella finestra del terminale:

   ```bash
   rpm --import https://iotdk.intel.com/misc/iot_pub2.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
   ```

   > Digitare "y", quando viene richiesto se includere questo canale.

   Il comando `rpm` consente di importare la chiave rpm. Il comando `smart channel` consente di aggiungere il canale rpm a Smart Package Manager. Prima dell'esecuzione del comando `smart update`, viene visualizzato un output come quello seguente.

   ![output dei comandi di canale rpm e smart](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

2. Eseguire il comando di aggiornamento smart:

   ```bash
   smart update
   ```

3. Installare il pacchetto del gateway IoT di Azure eseguendo questo comando:

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure` è il nome del pacchetto. Il comando `smart install` consente di installare il pacchetto.

    > Eseguire il comando seguente se viene visualizzato l'errore "public key not available" (chiave pubblica non disponibile)

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```
    > Riavviare Intel NUC se viene visualizzato l'errore: "no package provides util-linux-dev" (nessun pacchetto fornisce util-linux-dev)

   Dopo l'installazione del pacchetto, Intel NUC è pronto per funzionare come gateway.

## <a name="run-the-azure-iot-gateway-sdk-helloworld-sample-application"></a>Eseguire l'applicazione di esempio "hello_world" di Azure IoT Gateway SDK

che crea un gateway dal file `hello_world.json` e usa i componenti principali dell'architettura di Azure IoT Gateway SDK per registrare un messaggio di hello world in un file (log.txt) ogni 5 secondi.

È possibile eseguire l'esempio Hello World tramite i comandi seguenti:

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

Lasciare in esecuzione l'applicazione Hello World per alcuni minuti, quindi premere il tasto INVIO per arrestarla.
![output dell'applicazione](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

> È possibile ignorare eventuali errori "invalid argument handle(NULL)" (handle argomento non valido(NULL)" visualizzati dopo aver premuto INVIO. 

È possibile verificare l'esecuzione corretta del gateway aprendo il file log.txt che ora si trova nella cartella hello_world: ![vista della directory log.txt](media/iot-hub-gateway-kit-lessons/lesson1/logtxtdir.png)

Aprire il file log.txt con il comando seguente:

```bash
vim log.txt
```

Verranno visualizzati i contenuti del file log.txt, ovvero un output in formato JSON dei messaggi di log scritti ogni 5 secondi dal modulo Hello World del gateway.
![vista della directory log.txt](media/iot-hub-gateway-kit-lessons/lesson1/logtxtview.png)

In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="summary"></a>Riepilogo

Congratulazioni. La configurazione di Intel NUC come gateway è completata. È ora possibile passare alla lezione successiva per configurare il computer host, creare un hub IoT di Azure e registrare il dispositivo logico dell'hub IoT di Azure.

## <a name="next-steps"></a>Passaggi successivi
[Use an IoT gateway to connect a device to Azure IoT Hub](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md) (Usare un gateway IoT per connettere un dispositivo all'hub IoT di Azure)


