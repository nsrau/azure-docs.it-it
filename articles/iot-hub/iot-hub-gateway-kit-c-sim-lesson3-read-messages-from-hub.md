---
title: 'Dispositivo simulato e gateway Azure IoT: lezione 3: Leggere i messaggi | Documentazione Microsoft'
description: Eseguire un codice di esempio sul computer host per leggere i messaggi dall&quot;hub IoT.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: dati nel cloud, raccolta dati nel cloud, servizio cloud iot, dati iot
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 5a6ec9c1-d83c-41c1-beaf-7c0d3395d77f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 08ccd35c6e6a8e77f0fd4637f14a1f27730560d6
ms.lasthandoff: 01/25/2017


---

# <a name="read-messages-from-your-iot-hub"></a>Leggere i messaggi dall'hub IoT

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Eseguire il codice di esempio sul computer host per leggere i messaggi dall'hub IoT.

In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione

Come usare lo strumento gulp per leggere i messaggi dall'hub IoT.

## <a name="what-you-need"></a>Elementi necessari

- Esempio di dispositivo simulato in [Configurare ed eseguire un'applicazione di esempio per il caricamento nel cloud del dispositivo simulato](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md).

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Ottenere le stringhe di connessione dell'hub IoT e del dispositivo

La stringa di connessione del dispositivo viene usata dal dispositivo simulato per la connessione all'hub IoT. La stringa di connessione dell'hub IoT viene usata per connettersi al Registro di sistema relativo alle identità nell'hub IoT per gestire i dispositivi che possono connettersi all'hub IoT.

- Elencare tutti gli hub IoT nel gruppo di risorse eseguendo questo comando:

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   Usare `iot-gateway` come valore di `{resource group name}` se non è stato modificato.
- Ottenere la stringa di connessione dell'hub IoT eseguendo questo comando:

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}` è il nome specificato nella lezione 2.

## <a name="configure-the-device-connection-for-the-sample-code"></a>Configurare la connessione del dispositivo per il codice di esempio

Aggiornare le configurazioni di connessione del dispositivo e dell'hub IoT in `config-azure.json` tramite i passaggi seguenti:

1. Aprire `config-azure.json` in Visual Studio Code eseguendo questo comando in una finestra della console:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. Sostituire i valori seguenti nel file `config-azure.json`:

   ![schermata di config azure](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   Sostituire `[IoT hub connection string]` con la stringa di connessione dell'hub IoT.

## <a name="read-messages-from-your-iot-hub"></a>Leggere i messaggi dall'hub IoT

Eseguire l'applicazione di esempio del dispositivo simulato e leggere i messaggi dell'hub IoT con il comando seguente:

```bash
gulp run --iot-hub
```

Il comando esegue l'applicazione che invia messaggi all'hub IoT ogni 2 secondi e genera un processo figlio per ricevere il messaggio.

Tutti i messaggi inviati e ricevuti vengono visualizzati immediatamente nella stessa finestra della console nel computer host. L'applicazione verrà chiusa entro 40 secondi.

![Applicazione di esempio simulata con messaggi inviati e ricevuti](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub_simudev.png)

## <a name="summary"></a>Riepilogo

È stata eseguita l'applicazione di esempio per inviare dati all'hub IoT con un dispositivo simulato. Sono stati inoltre letti i messaggi inviati all'hub IoT.

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per le funzioni di Azure e un account di Archiviazione di Azure](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)



