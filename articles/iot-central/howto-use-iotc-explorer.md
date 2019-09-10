---
title: Monitorare la connettività dei dispositivi con Azure IoT Central Explorer
description: Monitorare i messaggi dei dispositivi e osservare le modifiche del dispositivo gemello usando l'interfaccia della riga di comando di IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 38cbe43e9038a47c4e222fd4744f0b844f9ddb4e
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845677"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitorare la connettività dei dispositivi con Azure IoT Central Explorer

*Questo argomento è rivolto ai responsabili di compilazione e amministrazione.*

Usare l'interfaccia della riga di comando di IoT Central Explorer per visualizzare i messaggi che i dispositivi inviano a IoT Central e osservare le modifiche nel dispositivo gemello dell'hub IoT. Questo strumento open source consente di ottenere informazioni più approfondite sullo stato delle connessioni dei dispositivi e di diagnosticare i problemi nel caso in cui i messaggi inviati dai dispositivi non raggiungano il cloud o i dispositivi non rispondano alle modifiche apportate ai dispositivi gemelli.

[Visitare il repository di IOTC-Explorer in GitHub.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Prerequisiti

+ Node.js versione 8.x o successiva - https://nodejs.org
+ Un amministratore dell'applicazione deve generare un token di accesso da usare in IOTC-Explorer

## <a name="install-iotc-explorer"></a>Installare IOTC-Explorer

Eseguire il seguente comando dalla riga di comando per l'installazione:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> In genere è necessario eseguire il comando install con `sudo` negli ambienti di tipo UNIX.

## <a name="run-iotc-explorer"></a>Eseguire IOTC-Explorer

Nelle sezioni seguenti vengono descritti i comandi e le opzioni comuni che è possibile utilizzare `iotc-explorer`quando si esegue. Per visualizzare il set completo di comandi e opzioni, passare `--help` a `iotc-explorer` o a uno dei relativi sottocomandi.

### <a name="login"></a>Accedi

Prima di procedere è necessario chiedere a un amministratore dell'applicazione IoT Central di ottenere un token di accesso da usare. L'amministratore esegue le operazioni seguenti:

1. Passare ad **Amministrazione** e quindi **accedere ai token**.
1. Selezionare **genera token**.
    ![Schermata della pagina dei token di accesso](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Immettere un nome di token, fare clic su **Avanti**e quindi su **copia**.
    > [!NOTE]
    > Il valore del token viene visualizzato una sola volta, quindi deve essere copiato prima di chiudere la finestra di dialogo. Una volta chiusa, la finestra di dialogo non viene mai visualizzata.

    ![Schermata con la finestra di dialogo di copia del token di accesso](media/howto-use-iotc-explorer/copyaccesstoken.png)

È possibile usare il token per accedere all'interfaccia della riga di comando nel modo seguente:

```cmd/sh
iotc-explorer login "<Token value>"
```

Se si preferisce che il token non sia stato salvato in modo permanente nella cronologia della shell, è possibile lasciare il token e fornirlo quando richiesto:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorare i messaggi del dispositivo

È possibile controllare i messaggi provenienti da un dispositivo specifico o da tutti i dispositivi nell'applicazione usando il comando `monitor-messages`. Questo comando avvia un Watcher che restituisce continuamente i nuovi messaggi man mano che arrivano:

Per controllare tutti i dispositivi all'interno dell'applicazione, eseguire il comando seguente:

```cmd/sh
iotc-explorer monitor-messages
```

Output:

![output di comando monitor-messages](media/howto-use-iotc-explorer/monitormessages.png)

Per controllare un dispositivo specifico, è sufficiente aggiungere l'ID del dispositivo alla fine del comando:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

È anche possibile restituire un formato più adatto per il computer aggiungendo l' `--raw` opzione al comando:

```cmd/sh
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

Ora che si è appreso come usare Esplora IoT Central, il passaggio successivo suggerito consiste nell'esplorazione della [gestione dei dispositivi IOT Central](howto-manage-devices.md).
