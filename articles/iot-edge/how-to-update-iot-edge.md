---
title: Aggiornare i dispositivi alla versione più recente di Azure IoT Edge | Microsoft Docs
description: Come aggiornare i dispositivi IoT Edge per eseguire le versioni più recenti del daemon di sicurezza e del runtime IoT Edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b04f909d58e1555cad9f34b682f9062bbd96cd0e
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394732"
---
# <a name="update-the-iot-edge-runtime"></a>Aggiornare il runtime IoT Edge

Con la disponibilità di versioni sempre nuove del servizio IoT Edge, è opportuno aggiornare i dispositivi IoT Edge in modo da disporre delle funzionalità e dei miglioramenti della sicurezza più recenti. Questo articolo fornisce informazioni su come aggiornare i dispositivi IoT Edge quando è disponibile una nuova versione. 

Se si vuole passare a una versione più recente, devono essere aggiornati due componenti in un dispositivo IoT Edge. Il primo è il daemon di sicurezza che viene eseguito nel dispositivo e avvia il runtime quando il dispositivo viene avviato. Attualmente, il daemon di sicurezza può essere aggiornato solo dal dispositivo stesso. Il secondo componente è il runtime, costituito dai moduli dell'agente IoT Edge e dell'hub Edge. A seconda del modo in cui si struttura la distribuzione, il runtime può essere aggiornato dal dispositivo o in modalità remota. 

Per trovare la versione più recente di Azure IoT Edge, fare riferimento alle [versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Aggiornare il daemon di sicurezza

Il daemon di sicurezza di IoT Edge è un componente nativo che deve essere aggiornato usando la gestione pacchetti nel dispositivo IoT Edge. 

Controllare la versione del daemon di sicurezza in esecuzione nel dispositivo tramite il comando `iotedge version`. 

### <a name="linux-devices"></a>Dispositivi Linux

Nei dispositivi Linux usare apt-get o la gestione pacchetti appropriata per aggiornare il daemon di sicurezza. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

### <a name="windows-devices"></a>Dispositivi Windows

Nei dispositivi Windows usare lo script di PowerShell per disinstallare e quindi reinstallare il daemon di sicurezza. Lo script di installazione esegue automaticamente il pull della versione più recente del daemon di sicurezza. È necessario specificare nuovamente la stringa di connessione per il dispositivo durante il processo di installazione. 

Disinstallare il daemon di sicurezza in una sessione amministratore di PowerShell. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
UnInstall-SecurityDaemon
```

Reinstallare il daemon di sicurezza a seconda che il dispositivo IoT Edge usi contenitori Windows o contenitori Linux. Sostituire la frase **\<Windows o Linux\>** con uno dei sistemi operativi del contenitore. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOS <Windows or Linux>
```

## <a name="update-the-runtime-containers"></a>Aggiornare i contenitori del runtime

La modalità di aggiornamento dei contenitori dell'agente IoT Edge e dell'hub Edge dipende dal fatto che si usino tag di versioni (come 1.0) o tag specifici (come 1.0.2) nella distribuzione. 

Controllare la versione dei moduli dell'agente IoT Edge e dell'hub Edge attualmente presenti nel dispositivo usando i comandi `iotedge logs edgeAgent` o `iotedge logs edgeHub`. 

  ![Visualizzare la versione dei contenitori](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Informazioni sui tag di IoT Edge

Le immagini dell'agente IoT Edge e dell'hub Edge vengono contrassegnate con la versione di IoT Edge a cui sono associati. Esistono due diversi modi per usare i tag con le immagini di runtime: 

* **Tag di versioni**. Vengono usati solo i primi due valori del numero di versione per ottenere l'immagine più recente corrispondente a tali cifre. Ad esempio, il tag 1.0 viene aggiornato ogni volta che è disponibile una nuova versione in modo da puntare alla versione 1.0.x più recente. Se il runtime del contenitore nel dispositivo IoT Edge esegue nuovamente il pull dell'immagine, i moduli del runtime vengono aggiornati alla versione più recente. Questo approccio è consigliato per finalità di sviluppo. Nelle distribuzioni dal portale di Azure vengono usati i tag di versioni per impostazione predefinita. 
* **Tag specifici**. Vengono usati tutti e tre i valori del numero di versione per impostare in modo esplicito la versione dell'immagine. Ad esempio, il tag 1.0.2 non verrà modificato dopo il rilascio iniziale. Quando si è pronti per l'aggiornamento, è possibile dichiarare un nuovo numero di versione nel manifesto della distribuzione. Questo approccio è consigliato per finalità di produzione.

### <a name="update-a-rolling-tag-image"></a>Aggiornare un'immagine con tag di versioni

Se si usano i tag di versioni nella distribuzione (ad esempio, mcr.microsoft.com/azureiotedge-hub:**1.0**), è necessario forzare il runtime del contenitore nel dispositivo per eseguire il pull della versione più recente dell'immagine. 

Eliminare la versione locale dell'immagine dal dispositivo IoT Edge. 

```cmd/sh
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Potrebbe essere necessario usare il flag force `-f` per rimuovere le immagini. 

Il servizio IoT Edge eseguirà il pull delle versioni più recenti delle immagini di runtime e le riavvierà automaticamente nel dispositivo. 

### <a name="update-a-specific-tag-image"></a>Aggiornare un'immagine con tag specifici

Se si usano tag specifici nella distribuzione (ad esempio, mcr.microsoft.com/azureiotedge-hub:**1.0.2**), occorre semplicemente aggiornare il tag nel manifesto della distribuzione e applicare le modifiche al dispositivo. 

Nel portale di Azure le immagini di distribuzione del runtime vengono dichiarate nella sezione **Configura impostazioni avanzate per il runtime di IoT Edge**. 

[Configura impostazioni avanzate per il runtime di IoT Edge](./media/how-to-update-iot-edge/configure-runtime.png)

In un manifesto della distribuzione JSON aggiornare le immagini dei moduli nella sezione **systemModules**. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.2",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.2",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="next-steps"></a>Passaggi successivi

Visualizzare le [versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) più recenti.

Rimanere aggiornati grazie agli annunci e agli aggiornamenti recenti nel [blog su Internet delle cose](https://azure.microsoft.com/blog/topics/internet-of-things/). 