---
title: 'Dispositivo simulato e gateway Azure IoT: lezione 1: Configurare NUC | Documentazione Microsoft'
description: Impostare Intel NUC come un gateway IoT di Azure tra un sensore e l'hub IoT di Azure, per raccogliere informazioni sul sensore e inviarle a IoT Hub.
services: iot-hub
documentationcenter: 
author: shizn
manager: yjianfeng
tags: 
keywords: gateway iot, intel nuc, computer nuc, DE3815TYKE
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: f41d6b2e-9b00-40df-90eb-17d824bea883
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b87974be9570f7d03fe84ae0a1d1fa7e346ff189
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
<a id="set-up-intel-nuc-as-an-iot-gateway" class="xliff"></a>

# Configurare Intel NUC come gateway IoT di Azure

<a id="what-you-will-do" class="xliff"></a>

## Contenuto dell'esercitazione

- Configurare Intel NUC come gateway IoT di Azure.
- Installare il pacchetto Azure IoT Edge in Intel NUC.
- Eseguire un'applicazione di esempio "hello_world" in Intel NUC per verificare la funzionalità del gateway.
In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-sim-troubleshooting.md).

<a id="what-you-will-learn" class="xliff"></a>

## Contenuto dell'esercitazione

In questa lezione si apprenderà:

- Come collegare Intel NUC alle periferiche.
- Come installare e aggiornare i pacchetti richiesti su Intel NUC tramite Smart Package Manager.
- Come eseguire l'applicazione di esempio "hello_world" per verificare la funzionalità del gateway.

<a id="what-you-need" class="xliff"></a>

## Elementi necessari

- Intel NUC Kit DE3815TYKE con Intel IoT Gateway Software Suite (Wind River Linux *7.0.0.13) preinstallata.
- Un cavo Ethernet.
- Una tastiera.
- Un cavo HDMI o VGA.
- Un monitor con porta HDMI o VGA.

![Kit gateway](media/iot-hub-gateway-kit-lessons/lesson1/kit_without_sensortag.png)

<a id="connect-intel-nuc-with-the-peripherals" class="xliff"></a>

## Come connettere Intel NUC con le periferiche

Nell'immagine seguente è riportato un esempio di dispositivo Intel NUC collegato a diverse periferiche:

1. A una tastiera.
2. A un monitor tramite un cavo VGA o HDMI.
3. A una rete cablata tramite cavo Ethernet.
4. All'alimentatore tramite un cavo di alimentazione.

![Dispositivo Intel NUC collegato a periferiche](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

<a id="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh" class="xliff"></a>

## Connettere il sistema Intel NUC dal computer host tramite Secure Shell (SSH)

In questo caso sono necessari una tastiera e un monitor per ottenere l'indirizzo IP del dispositivo NUC. Se si conosce già l'indirizzo IP, è possibile andare al passaggio 3 di questa sezione.

1. Accendere il dispositivo Intel NUC premendo l'apposito tasto e accedere al sistema.

   Il nome utente e la password predefiniti sono entrambi `root`.

2. Ottenere l'indirizzo IP del dispositivo NUC eseguendo il comando `ifconfig`. Questo passaggio viene eseguito sul dispositivo NUC.

   Di seguito è riportato un esempio dell'output del comando.

   ![Output di ifconfig che indica l'IP NUC](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   In questo esempio, il valore che segue `inet addr:` è l'indirizzo IP necessario se si prevede di connettersi in remoto da un computer host a Intel NUC.

3. Per connettersi a Intel NUC, usare uno dei client SSH seguenti dal computer host.

   - [PuTTY](http://www.putty.org/) per Windows.
   - Il client SSH incorporato in Ubuntu o macOS.

   È più efficiente e produttivo lavorare su un dispositivo Intel NUC da un computer host. Per connettersi al dispositivo NUC tramite il client SSH, sono necessari indirizzo IP, nome utente e password. Di seguito è riportato un esempio di utilizzo del client SSH su macOS.
   ![Client SSH eseguito su macOS](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

<a id="install-the-azure-iot-edge-package" class="xliff"></a>

## Installare il pacchetto Azure IoT Edge

Il pacchetto Azure IoT Edge contiene i file binari precompilati dell'SDK e le relative dipendenze. I file binari sono costituiti da Azure IoT Edge, Azure IoT SDK e gli strumenti corrispondenti. Il pacchetto contiene anche un'applicazione di esempio "hello_world" usata per convalidare la funzionalità del gateway. IoT Edge è la parte principale del gateway. Per installare il pacchetto, seguire questa procedura:

1. Aggiungere il repository cloud IoT eseguendo questi comandi nella finestra del terminale:

   ```bash
   rpm --import http://iotdk.intel.com/misc/iot_pub.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   ```

   Il comando `rpm` consente di importare la chiave rpm. Il comando `smart channel` consente di aggiungere il canale rpm a Smart Package Manager. Prima dell'esecuzione del comando `smart update`, viene visualizzato un output come quello seguente.

   ![output dei comandi di canale rpm e smart](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

   ```bash
   smart update
   ```

2. Installare il pacchetto eseguendo questo comando:

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure` è il nome del pacchetto. Il comando `smart install` consente di installare il pacchetto.

   Dopo l'installazione del pacchetto, Intel NUC dovrebbe funzionare come gateway.

<a id="run-the-azure-iot-edge-helloworld-sample-application" class="xliff"></a>

## Eseguire l'applicazione di esempio "hello_world" di Azure IoT Edge.

Passare a `azureiotgatewaysdk/samples` ed eseguire l'applicazione di esempio "hello_world" che crea un gateway dal file `hello_world.json` e usa i componenti principali dell'architettura di Azure IoT Edge per registrare un messaggio di "hello world" in un file ogni 5 secondi.

È possibile eseguire l'applicazione di esempio "hello_world" con questo comando:

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

L'applicazione di esempio restituisce l'output seguente se la funzionalità del gateway viene eseguita correttamente:

![output dell'applicazione](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-troubleshooting.md).

<a id="summary" class="xliff"></a>

## Riepilogo

Congratulazioni. La configurazione di Intel NUC come gateway è completata. È ora possibile passare alla lezione successiva per configurare il computer host, creare un hub IoT di Azure e registrare il dispositivo logico dell'hub IoT di Azure.

<a id="next-steps" class="xliff"></a>

## Passaggi successivi
[Preparare il computer host e l'hub IoT di Azure](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)

