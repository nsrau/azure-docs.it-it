---
title: Monitorare la connettività dei dispositivi con Azure IoT Central Explorer
description: Monitorare i messaggi dei dispositivi e osservare le modifiche del dispositivo gemello usando l'interfaccia della riga di comando di IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: e510cfbd89ab8dcd8dccd9a8b95a49a057c9b54f
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55825129"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitorare la connettività dei dispositivi con Azure IoT Central Explorer

*Questo argomento è rivolto ai responsabili di compilazione e amministrazione.*

Usare l'interfaccia della riga di comando di IoT Central Explorer per visualizzare i messaggi che i dispositivi inviano a IoT Central e osservare le modifiche nel dispositivo gemello dell'hub IoT. Questo strumento open source consente di ottenere informazioni più approfondite sullo stato delle connessioni dei dispositivi e di diagnosticare i problemi nel caso in cui i messaggi inviati dai dispositivi non raggiungano il cloud o i dispositivi non rispondano alle modifiche apportate ai dispositivi gemelli.

## <a name="visit-the-iotc-explorer-repo-in-githubhttpsakamsiotciotcexplorercligithub"></a>[Visitare il repository iotc-explorer in GitHub](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Prerequisiti
+ Node.js versione 8.x o successiva - https://nodejs.org
+ È necessario chiedere a un amministratore dell'app di generare un token di accesso da usare in iotc-explorer

## <a name="installing-iotc-explorer"></a>Installazione di iotc-explorer

Eseguire il seguente comando dalla riga di comando per l'installazione:

```
npm install -g iotc-explorer
```

> [!NOTE]
> In genere è necessario eseguire il comando di installazione con `sudo` negli ambienti simili a Unix.

## <a name="running-iotc-explorer"></a>Esecuzione di iotc-explorer

Di seguito sono riportati alcuni comandi e opzioni comuni che è possibile eseguire quando si usa `iotc-explorer`. Per visualizzare il set completo di comandi e opzioni, è possibile passare `--help` a `iotc-explorer` o uno qualsiasi dei relativi sottocomandi.

### <a name="login"></a>Login

Prima di procedere è necessario chiedere a un amministratore dell'applicazione IoT Central di ottenere un token di accesso da usare. L'amministratore esegue le operazioni seguenti:
1. Accedere a **Administration/Access Tokens**. 
1. Fare clic su **Genera**.
    ![Schermata della pagina dei token di accesso](media/howto-use-iotc-explorer-experimental/accesstokenspage.png)

1. Immettere un nome di token, fare clic su **Avanti** e **copiare il valore del token**.
    > [!NOTE]
    > Il valore del token verrà visualizzato una sola volta, quindi è necessario copiarlo prima di chiudere la finestra di dialogo. Dopo avere chiuso la finestra di dialogo, non verrà più visualizzato.

    ![Schermata con la finestra di dialogo di copia del token di accesso](media/howto-use-iotc-explorer-experimental/copyaccesstoken.png)

È quindi possibile usare il token per accedere all'interfaccia della riga di comando eseguendo:

```sh
iotc-explorer login "<Token value>"
```

Per evitare che il token venga salvato in modo permanente nella cronologia della shell, è possibile escluderlo e specificarlo quando richiesto:

```
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorare i messaggi del dispositivo

È possibile controllare i messaggi provenienti da un dispositivo specifico o da tutti i dispositivi nell'applicazione usando il comando `monitor-messages`. Verrà avviato un watcher che genererà continuamente nuovi messaggi man mano che arrivano.

Per controllare tutti i dispositivi all'interno dell'applicazione, eseguire il comando seguente:

```
iotc-explorer monitor-messages
```

Output:

![output di comando monitor-messages](media/howto-use-iotc-explorer-experimental/monitormessages.png)

Per controllare un dispositivo specifico, è sufficiente aggiungere l'ID del dispositivo alla fine del comando:

```
iotc-explorer monitor-messages <your-device-id>
```

È anche possibile visualizzare l'output del comando in un formato più leggibile da computer aggiungendo l'opzione `--raw` al comando:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Recuperare il dispositivo gemello

È possibile usare il comando `get-twin` per ottenere il contenuto del dispositivo gemello di un dispositivo IoT Central. A tale scopo, eseguire il comando seguente:

```
iotc-explorer get-twin <your-device-id>
```

Output:

![output di comando get-twin](media/howto-use-iotc-explorer-experimental/getdevicetwin.png)

Come per `monitor-messages`, è possibile ottenere un output più leggibile da computer passando l'opzione `--raw`:

```
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come usare IoT Central Explorer, il passaggio successivo consigliato è esplorare la [gestione dei dispositivi con IoT Central](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
