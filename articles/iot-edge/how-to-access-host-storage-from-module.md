---
title: Usare l'archiviazione locale del dispositivo IoT Edge da un modulo - Azure IoT Edge Documenti Microsoft
description: Usare le variabili di ambiente e creare opzioni per abilitare l'accesso ai moduli all'archiviazione locale del dispositivo Edge IoT.Use environment variables and create options to enable module access to IoT Edge device local storage.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079d5845917e63fadcf0466e5a744ed637d704ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434529"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Consentire ai moduli di accedere all'archiviazione locale di un dispositivo

Oltre a archiviare i dati usando i servizi di archiviazione di Azure o nella memoria del contenitore del dispositivo, è anche possibile dedicare l'archiviazione nel dispositivo IoT Edge host stesso per una maggiore affidabilità, soprattutto quando si opera offline.

## <a name="link-module-storage-to-device-storage"></a>Collegare l'archiviazione del modulo all'archiviazione del dispositivo

Per abilitare un collegamento dall'archiviazione del modulo all'archiviazione nel sistema host, creare una variabile di ambiente per il modulo che punti a una cartella di archiviazione nel contenitore. Usare quindi le opzioni di creazione per associare tale cartella di archiviazione a una cartella nel computer host.

Ad esempio, se si desidera abilitare l'hub IoT Edge per archiviare i messaggi nell'archiviazione locale del dispositivo e recuperarli in un secondo momento, è possibile configurare le variabili di ambiente e le opzioni di creazione nel portale di Azure nella sezione **Impostazioni di runtime.**

1. Per l'hub IoT Edge e l'agente IoT Edge, aggiungere una variabile di ambiente denominata **storageFolder** che punta a una directory nel modulo.
1. Per l'hub IoT Edge e l'agente IoT Edge, aggiungere associazioni per connettere una directory locale nel computer host a una directory nel modulo. Ad esempio:

   ![Aggiungere opzioni di creazione e variabili di ambiente per l'archiviazione localeAdd create options and environment variables for local storage](./media/how-to-access-host-storage-from-module/offline-storage.png)

In alternativa, è possibile configurare l'archiviazione locale direttamente nel manifesto di distribuzione. Ad esempio:

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

Sostituire `<HostStoragePath>` `<ModuleStoragePath>` e con il percorso di archiviazione dell'host e del modulo; entrambi i valori devono essere un percorso assoluto.

Ad esempio, in un `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` sistema Linux, significa che la directory **/etc/iotedge/storage** sul sistema host è mappata alla directory **/iotedge/storage/** nel contenitore. In un sistema Windows, `"Binds":["C:\\temp:C:\\contemp"]` come altro esempio, significa che la directory **\\C: temp** sul sistema host è mappata alla directory **C:\\contemp** nel contenitore.

Inoltre, nei dispositivi Linux, assicurarsi che il profilo utente per il modulo disponga delle autorizzazioni di lettura, scrittura ed esecuzione necessarie per la directory del sistema host. Tornando all'esempio precedente di abilitazione dell'hub IoT Edge per archiviare i messaggi nell'archivio locale del dispositivo, è necessario concedere autorizzazioni al relativo profilo utente, UID 1000. L'agente IoT Edge funziona come root, pertanto non necessita di autorizzazioni aggiuntive. Esistono diversi modi per gestire le autorizzazioni `chown` della directory nei sistemi `chmod` Linux, tra cui l'utilizzo per modificare il proprietario della directory e quindi per modificare le autorizzazioni, ad esempio:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

È possibile trovare ulteriori dettagli sulle opzioni di creazione da [docker docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Passaggi successivi

Per un altro esempio di accesso all'archiviazione host da un modulo, vedere [Archiviare dati sul perimetro con Archiviazione BLOB](how-to-store-data-blob.md)di Azure in Edge Edge .
