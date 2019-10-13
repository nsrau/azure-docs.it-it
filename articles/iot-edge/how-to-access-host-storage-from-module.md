---
title: Usare IoT Edge archiviazione locale del dispositivo da un Azure IoT Edge di modulo | Microsoft Docs
description: Usare le variabili di ambiente e creare opzioni per abilitare l'accesso al modulo per IoT Edge archiviazione locale del dispositivo.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/12/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2526f33f0053b5805394a4a898af88d86187066c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301282"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Consentire ai moduli di accedere alla risorsa di archiviazione locale di un dispositivo

Oltre a archiviare i dati usando i servizi di archiviazione di Azure o nell'archiviazione contenitore del dispositivo, è anche possibile dedicare spazio di archiviazione nell'host IoT Edge dispositivo stesso per migliorare l'affidabilità, soprattutto quando si opera offline.

Per configurare l'archiviazione nel sistema host, creare una variabile di ambiente per il modulo che punta a una cartella di archiviazione nel contenitore. Usare quindi le opzioni di creazione per associare tale cartella di archiviazione a una cartella nel computer host.

Ad esempio, se si vuole abilitare l'hub IoT Edge per archiviare i messaggi nell'archiviazione locale del dispositivo e recuperarli in un secondo momento, è possibile configurare le variabili di ambiente e le opzioni di creazione nella portale di Azure nelle **Impostazioni avanzate di runtime di Edge** sezione.

1. Per entrambi IoT Edge Hub e IoT Edge Agent, aggiungere una variabile di ambiente denominata **StorageFolder** che punta a una directory del modulo.
1. Per IoT Edge Hub e IoT Edge Agent, aggiungere binding per connettere una directory locale nel computer host a una directory del modulo. Esempio:

   ![Aggiungere le opzioni di creazione e le variabili di ambiente per l'archiviazione locale](./media/how-to-access-host-storage-from-module/offline-storage.png)

In alternativa, è possibile configurare l'archiviazione locale direttamente nel manifesto della distribuzione. Esempio:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Sostituire `<HostStoragePath>` e `<ModuleStoragePath>` con il percorso di archiviazione dell'host e del modulo; entrambi i valori devono essere un percorso assoluto.

Ad esempio, in un sistema Linux, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` indica che la directory **/etc/iotedge/storage** nel sistema host è mappata alla directory **/iotedge/storage/** nel contenitore. In un sistema Windows, come altro esempio, `"Binds":["C:\\temp:C:\\contemp"]` indica che la directory **c: \\temp** nel sistema host è mappata alla directory **c: \\contemp** nel contenitore.

Inoltre, nei dispositivi Linux, verificare che il profilo utente per il modulo disponga delle autorizzazioni di lettura, scrittura ed esecuzione necessarie per la directory del sistema host. Tornando all'esempio precedente di abilitazione di IoT Edge Hub per archiviare i messaggi nell'archiviazione locale del dispositivo, è necessario concedere le autorizzazioni al relativo profilo utente, UID 1000. L'agente di IoT Edge funge da radice, quindi non necessita di autorizzazioni aggiuntive. Esistono diversi modi per gestire le autorizzazioni della directory nei sistemi Linux, incluso l'uso di `chown` per modificare il proprietario della directory e quindi `chmod` per modificare le autorizzazioni, ad esempio:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Per altre informazioni su come creare opzioni, vedere la [documentazione di Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).
