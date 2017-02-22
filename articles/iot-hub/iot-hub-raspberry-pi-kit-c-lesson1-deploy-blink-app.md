---
title: 'Connettere Raspberry Pi (C) ad Azure IoT: lezione 1: Distribuire l&quot;app | Documentazione Microsoft'
description: Clonare l&quot;applicazione C di esempio da GitHub e usare gulp per distribuire l&quot;applicazione nella scheda Raspberry Pi 3. Questa applicazione di esempio fa lampeggiare il LED connesso alla scheda ogni due secondi.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: lampeggiamento led raspberry pi, far lampeggiare il led con raspberry pi
ms.assetid: f601d1e1-2bc3-4cc5-a6b1-0467e5304dcf
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 5b16230178fa82c7f227e3e3f8742fa16b00c719


---
# <a name="create-and-deploy-the-blink-application"></a>Creare e distribuire l'applicazione per il lampeggiamento
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Clonare l'applicazione C di esempio da GitHub e usare lo strumento gulp per distribuire l'applicazione di esempio in Raspberry Pi 3. L'applicazione di esempio fa lampeggiare il LED connesso alla scheda ogni due secondi. In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Contenuto dell'articolo:

* Come usare `device-discover-cli` per recuperare le informazioni di rete relative al dispositivo Pi.
* Come distribuire ed eseguire l'applicazione di esempio nel dispositivo Pi.
* Come effettuare la distribuzione e il debug di applicazioni eseguite in remoto in Pi.

## <a name="what-you-need"></a>Elementi necessari
È necessario aver completato le operazioni seguenti:

* [Configurare il dispositivo](iot-hub-raspberry-pi-kit-c-lesson1-configure-your-device.md)
* [Ottenere gli strumenti](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-win32.md)

## <a name="obtain-the-ip-address-and-host-name-of-pi"></a>Ottenere l'indirizzo IP e il nome host del dispositivo Pi
Aprire il prompt dei comandi in Windows o una finestra del terminale in macOS o Ubuntu e quindi eseguire questo comando:

```bash
devdisco list --eth
```

L'output visualizzato dovrebbe essere simile al seguente:

![Individuazione del dispositivo](media/iot-hub-raspberry-pi-lessons/lesson1/device_discovery.png)

Prendere nota di `IP address` e `hostname` del dispositivo Pi. Queste informazioni saranno necessarie più avanti in questo articolo.

> [!NOTE]
> Verificare che Pi sia connesso alla stessa rete del computer. Se il computer è connesso a una rete wireless mentre il dispositivo Pi è connesso a una rete cablata, ad esempio, l'indirizzo IP potrebbe non essere incluso nell'output di devdisco.

## <a name="open-the-sample-application"></a>Aprire l'applicazione di esempio
Per aprire l'applicazione di esempio, seguire questa procedura:

1. Clonare il repository di esempio da GitHub eseguendo questo comando:
   
    ```bash
    git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-getting-started.git
    ```
2. Aprire l'applicazione di esempio in Visual Studio Code usando i comandi seguenti:
   
    ```bash
    cd iot-hub-c-raspberrypi-getting-started
    cd Lesson1
    code .
    ```

![Struttura del repository](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-blink-c-mac.png)

Il file `main.c` nella sottocartella `app` è il file di origine chiave contenente il codice per controllare il LED.

### <a name="install-application-dependencies"></a>Installare le dipendenze dell'applicazione
Installare le librerie e gli altri moduli necessari per l'applicazione di esempio eseguendo questo comando:

```bash
npm install
```

## <a name="configure-the-device-connection"></a>Configurare la connessione del dispositivo
Per configurare la connessione al dispositivo, seguire questa procedura.

1. Generare il file di configurazione del dispositivo eseguendo questo comando:
   
   ```bash
   gulp init
   ```
   
   Il file di configurazione `config-raspberrypi.json` contiene le credenziali utente usate per accedere al dispositivo Pi. Per evitare la perdita delle credenziali utente, il file di configurazione viene generato nella sottocartella `.iot-hub-getting-started` della home directory del computer.

2. Aprire il file di configurazione del dispositivo in Visual Studio Code eseguendo questo comando:
   
   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```

3. Sostituire il segnaposto `[device hostname or IP address]` con l'indirizzo IP o il nome host ottenuto nella sezione "Ottenere l'indirizzo IP e il nome host del dispositivo Pi".
   
   ![Config.json](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-config-mac.png)

> [!NOTE]
> È possibile usare una chiave SSH al posto di nome utente e password quando ci si connette a Raspberry Pi. Per eseguire questa operazione è necessario generare la chiave tramite **ssh-keygen** e **ssh-copy-id pi@\<indirizzo dispositivo\>**.
>
> In Windows, questi comandi sono disponibili in **Git Bash**.
>
> In MacOS è necessario eseguire **brew install ssh-copy-id**.
>
> Dopo aver caricato correttamente la chiave in Raspberry Pi, sostituire **device_password** con la proprietà **device_key_path** in **config raspberrypi.json**.
>
> Le righe aggiornate saranno simili alle seguenti:
> ```javascript
> "device_user_name": "pi",
> "device_key_path": "id_rsa",
> ```

Congratulazioni. La creazione della prima applicazione di esempio per Pi è completata.

## <a name="deploy-and-run-the-sample-application"></a>Distribuire ed eseguire l'applicazione di esempio
### <a name="install-the-azure-iot-hub-sdk-on-pi"></a>Installare Azure IoT SDK per hub in Pi
Installare Azure IoT SDK per hub in Pi eseguendo questo comando:

```bash
gulp install-tools
```

Per completare questa attività, la prima potrebbero essere necessari alcuni minuti.

### <a name="deploy-and-run-the-sample-app"></a>Distribuire ed eseguire l'app di esempio
Distribuire ed eseguire l'applicazione di esempio eseguendo questo comando:

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>Verificare il funzionamento dell'app
L'applicazione di esempio termina automaticamente dopo 20 lampeggiamenti del LED. In caso contrario, vedere la [guida alla risoluzione dei problemi](iot-hub-raspberry-pi-kit-c-troubleshooting.md) per le soluzioni alle problematiche comuni.
![LED lampeggiante](media/iot-hub-raspberry-pi-lessons/lesson1/led_blinking.jpg)

## <a name="summary"></a>Riepilogo
Sono stati installati gli strumenti necessari per usare Pi ed è stata distribuita un'applicazione di esempio che consente al dispositivo Pi di far lampeggiare il LED. È ora possibile creare, distribuire ed eseguire un'altra applicazione di esempio che connette Pi all'hub IoT di Azure per inviare e ricevere messaggi.

## <a name="next-steps"></a>Passaggi successivi
[Ottenere gli strumenti di Azure](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-win32.md)




<!--HONumber=Jan17_HO4-->


