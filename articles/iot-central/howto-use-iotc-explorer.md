---
title: Monitorare la connettività dei dispositivi con Azure IoT Central Explorer
description: Monitorare i messaggi dei dispositivi e osservare le modifiche del dispositivo gemello usando l'interfaccia della riga di comando di IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 16cb27ab330118d1bb59cf4f3d782bf55fa28d43
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779743"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitorare la connettività dei dispositivi con Azure IoT Central Explorer

*Questo argomento è rivolto ai responsabili di compilazione e amministrazione.*

Usare l'interfaccia della riga di comando di IoT Central Explorer per visualizzare i messaggi che i dispositivi inviano a IoT Central e osservare le modifiche nel dispositivo gemello dell'hub IoT. Questo strumento open source consente di ottenere informazioni più approfondite sullo stato delle connessioni dei dispositivi e di diagnosticare i problemi nel caso in cui i messaggi inviati dai dispositivi non raggiungano il cloud o i dispositivi non rispondano alle modifiche apportate ai dispositivi gemelli.

[Visitare il repository iotc-explorer in GitHub.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Prerequisiti

+ Node.js versione 8.x o successiva - https://nodejs.org
+ Un amministratore dell'applicazione deve generare un token di accesso per l'utilizzo in iotc-explorer

## <a name="install-iotc-explorer"></a>Installare iotc-explorer

Eseguire il seguente comando dalla riga di comando per l'installazione:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> È in genere necessario eseguire il comando di installazione con `sudo` in ambienti simili a Unix.

## <a name="run-iotc-explorer"></a>Eseguire iotc-explorer

Le sezioni seguenti descrivono i comandi e opzioni che è possibile usare quando si esegue comuni `iotc-explorer`. Per visualizzare il set completo di comandi e opzioni, passare `--help` a `iotc-explorer` o uno qualsiasi dei relativi sottocomandi.

### <a name="login"></a>Login

Prima di procedere è necessario chiedere a un amministratore dell'applicazione IoT Central di ottenere un token di accesso da usare. L'amministratore esegue le operazioni seguenti:

1. Passare a **Administration** quindi **i token di accesso**.
1. Selezionare **generare Token**.
    ![Schermata della pagina dei token di accesso](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Immettere un nome di Token, selezionare **successivo**e quindi **copia**.
    > [!NOTE]
    > Il valore del token viene visualizzato solo una volta, pertanto è necessario copiarlo prima di chiudere la finestra di dialogo. Dopo avere chiuso la finestra di dialogo, non viene mai visualizzata di nuovo.

    ![Schermata con la finestra di dialogo di copia del token di accesso](media/howto-use-iotc-explorer/copyaccesstoken.png)

È possibile usare il token per accedere al comando come indicato di seguito:

```cmd/sh
iotc-explorer login "<Token value>"
```

Se si preferisce non che il token persistente nella cronologia della shell, è possibile lasciare il token out e invece specificarla quando richiesto:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorare i messaggi del dispositivo

È possibile controllare i messaggi provenienti da un dispositivo specifico o da tutti i dispositivi nell'applicazione usando il comando `monitor-messages`. Questo comando avvia un controllo che genera in modo continuativo i nuovi messaggi man mano che arrivano:

Per controllare tutti i dispositivi all'interno dell'applicazione, eseguire il comando seguente:

```cmd/sh
iotc-explorer monitor-messages
```

Output:

![output di comando monitor-messages](media/howto-use-iotc-explorer/monitormessages.png)

Per guardare un dispositivo specifico, è sufficiente aggiungere l'id del dispositivo alla fine del comando:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

È anche possibile inviare un formato più leggibile tramite computer aggiungendo il `--raw` opzione per il comando:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Recuperare il dispositivo gemello

È possibile usare il comando `get-twin` per ottenere il contenuto del dispositivo gemello di un dispositivo IoT Central. A tale scopo, eseguire il comando seguente:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Output:

![output di comando get-twin](media/howto-use-iotc-explorer/getdevicetwin.png)

Come per `monitor-messages`, è possibile ottenere un output più leggibile da computer passando l'opzione `--raw`:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare Esplora IoT Central, il passaggio successivo consigliato è esplorare [la gestione dei dispositivi IoT Central](howto-manage-devices.md).
