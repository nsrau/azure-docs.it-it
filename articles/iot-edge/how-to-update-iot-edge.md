---
title: Versione di aggiornamento IoT Edge nei dispositivi - Azure IoT Edge | Microsoft Docs
description: Come aggiornare i dispositivi IoT Edge per eseguire le versioni più recenti del daemon di sicurezza e del runtime IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: b6b4cd38ce5f591d43f27f735a48993cc1a1ab63
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414461"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aggiornare il daemon di sicurezza e il runtime di IoT Edge

Quando il servizio IoT Edge rilascia nuove versioni, è opportuno aggiornare i dispositivi IoT Edge per le funzionalità più recenti e i miglioramenti della sicurezza. Questo articolo fornisce informazioni su come aggiornare i dispositivi IoT Edge quando è disponibile una nuova versione. 

Se si vuole passare a una versione più recente, devono essere aggiornati due componenti in un dispositivo IoT Edge. Il primo è il daemon di sicurezza, che viene eseguito nel dispositivo e avvia i moduli runtime quando il dispositivo viene avviato. Attualmente, il daemon di sicurezza può essere aggiornato solo dal dispositivo stesso. Il secondo componente è il runtime, costituito dai moduli dell'agente IoT Edge e dell'hub di IoT Edge. A seconda del modo in cui si struttura la distribuzione, il runtime può essere aggiornato dal dispositivo o in modalità remota. 

Per trovare la versione più recente di Azure IoT Edge, fare riferimento alle [versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

>[!IMPORTANT]
>Se si esegue Azure IoT Edge in un dispositivo Windows, non eseguire l'aggiornamento alla versione 1.0.5 se al dispositivo si applica una delle condizioni seguenti: 
>* Il dispositivo non è stato aggiornato alla build di Windows 17763. IoT Edge versione 1.0.5 non supporta build di Windows precedenti alla 17763.
>* Nel dispositivo Windows sono in esecuzione moduli Java o Node.js. Non eseguire l'aggiornamento alla versione 1.0.5 anche se il dispositivo Windows è stato aggiornato alla build più recente. 
>
>Per altre informazioni su IoT Edge versione 1.0.5, vedere le [note sulla versione 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Per ulteriori informazioni su come impedire agli strumenti di sviluppo di eseguire l'aggiornamento alla versione più recente, vedere [il Blog per sviluppatori](https://devblogs.microsoft.com/iotdev/).


## <a name="update-the-security-daemon"></a>Aggiornare il daemon di sicurezza

Il daemon di sicurezza di IoT Edge è un componente nativo che deve essere aggiornato usando la gestione pacchetti nel dispositivo IoT Edge. 

Controllare la versione del daemon di sicurezza in esecuzione nel dispositivo tramite il comando `iotedge version`. 

### <a name="linux-devices"></a>Dispositivi Linux

Nei dispositivi Linux x64 usare apt-get o il gestore di pacchetti appropriato per aggiornare il daemon di sicurezza. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

Nei dispositivi ARM32 Linux, usare la procedura descritta in [install Azure IOT Edge Runtime on Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) per installare la versione più recente del daemon di sicurezza. 

### <a name="windows-devices"></a>Dispositivi Windows

Nei dispositivi Windows, usare lo script di PowerShell per aggiornare il daemon di sicurezza. Lo script esegue automaticamente il pull della versione più recente del daemon di sicurezza. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

L'esecuzione del comando Update-IoTEdge rimuove il daemon di sicurezza dal dispositivo, insieme alle due immagini del contenitore di Runtime. Il file config. YAML viene mantenuto nel dispositivo e i dati del motore di contenitori di Moby (se si usano i contenitori di Windows). Mantenendo le informazioni di configurazione significa che non è necessario fornire di nuovo la stringa di connessione o le informazioni sul servizio Device provisioning per il dispositivo durante il processo di aggiornamento. 

Se si vuole installare una versione specifica del daemon di sicurezza, scaricare il file Microsoft-Azure-IoTEdge. CAB appropriato dalle [versioni IOT Edge](https://github.com/Azure/azure-iotedge/releases). Usare quindi il parametro `-OfflineInstallationPath` in modo che punti al percorso del file. Per altre informazioni, vedere [Installazione offline](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Aggiornare i contenitori del runtime

La modalità di aggiornamento dell'agente IoT Edge e dei contenitori di hub IoT Edge dipende dal fatto che si usino tag in sequenza (ad esempio 1,0) o tag specifici (ad esempio 1.0.7) nella distribuzione. 

Controllare la versione dei moduli dell'agente IoT Edge e dell'hub di IoT Edge attualmente presenti nel dispositivo usando i comandi `iotedge logs edgeAgent` o `iotedge logs edgeHub`. 

  ![Trovare la versione contenitore nei log](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Informazioni sui tag di IoT Edge

Le immagini dell'agente IoT Edge e dell'hub di IoT Edge vengono contrassegnate con la versione di IoT Edge a cui sono associate. Esistono due diversi modi per usare i tag con le immagini di runtime: 

* **Tag di versioni**. Vengono usati solo i primi due valori del numero di versione per ottenere l'immagine più recente corrispondente a tali cifre. Ad esempio, il tag 1.0 viene aggiornato ogni volta che è disponibile una nuova versione in modo da puntare alla versione 1.0.x più recente. Se il runtime del contenitore nel dispositivo IoT Edge esegue nuovamente il pull dell'immagine, i moduli del runtime vengono aggiornati alla versione più recente. Questo approccio è consigliato per finalità di sviluppo. Nelle distribuzioni dal portale di Azure vengono usati i tag di versioni per impostazione predefinita. 
* **Tag specifici**. Vengono usati tutti e tre i valori del numero di versione per impostare in modo esplicito la versione dell'immagine. Ad esempio, 1.0.7 non cambierà dopo la versione iniziale. Quando si è pronti per l'aggiornamento, è possibile dichiarare un nuovo numero di versione nel manifesto della distribuzione. Questo approccio è consigliato per finalità di produzione.

### <a name="update-a-rolling-tag-image"></a>Aggiornare un'immagine con tag di versioni

Se si usano i tag di versioni nella distribuzione (ad esempio, mcr.microsoft.com/azureiotedge-hub:**1.0**), è necessario forzare il runtime del contenitore nel dispositivo per eseguire il pull della versione più recente dell'immagine. 

Eliminare la versione locale dell'immagine dal dispositivo IoT Edge. Nei computer Windows la disinstallazione del daemon di sicurezza rimuove anche le immagini di runtime, pertanto non è necessario eseguire nuovamente questo passaggio. 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Potrebbe essere necessario usare il flag force `-f` per rimuovere le immagini. 

Il servizio IoT Edge eseguirà il pull delle versioni più recenti delle immagini di runtime e le riavvierà automaticamente nel dispositivo. 

### <a name="update-a-specific-tag-image"></a>Aggiornare un'immagine con tag specifici

Se si usano tag specifici nella distribuzione (ad esempio, mcr.microsoft.com/azureiotedge-hub:**1.0.7**), è sufficiente aggiornare il tag nel manifesto di distribuzione e applicare le modifiche al dispositivo. 

Nel portale di Azure le immagini di distribuzione del runtime vengono dichiarate nella sezione **Configura impostazioni avanzate per il runtime di IoT Edge**. 

![Configurare le impostazioni avanzate del runtime Edge](./media/how-to-update-iot-edge/configure-runtime.png)

In un manifesto della distribuzione JSON aggiornare le immagini dei moduli nella sezione **systemModules**. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.7",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.7",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="update-to-a-release-candidate-version"></a>Eseguire l'aggiornamento a una versione finale candidata

Azure IoT Edge rilascia periodicamente nuove versioni del servizio IoT Edge. Prima di ogni versione stabile, sono presenti una o più versioni di Release Candidate (RC). Le versioni RC includono tutte le funzionalità pianificate per la versione, ma sono ancora in corso i processi di test e convalida necessari per una versione stabile. Se si vuole testare una nuova funzionalità in anticipo, è possibile installare la versione RC e inviare commenti e suggerimenti tramite GitHub. 

Le versioni finali candidate seguono la stessa convenzione di numerazione delle versioni, ma hanno **-RC** più un numero incrementale aggiunto alla fine. È possibile visualizzare i candidati della versione nello stesso elenco di [Azure IOT Edge](https://github.com/Azure/azure-iotedge/releases) versioni stabili. Ad esempio, trovare **1.0.7-RC1** e **1.0.7-RC2**, i due candidati della versione precedenti a **1.0.7**. È anche possibile notare che le versioni RC sono contrassegnate con le etichette della **versione non definitiva** . 

Come anteprime, le versioni release candidate non sono incluse come ultima versione di destinazione dei programmi di installazione regolari. Al contrario, è necessario assegnare manualmente gli asset per la versione RC che si desidera testare. A seconda del sistema operativo del dispositivo IoT Edge, usare le sezioni seguenti per aggiornare IoT Edge a una versione specifica:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Passaggi successivi

Visualizzare le [versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) più recenti.

Rimanere aggiornati grazie agli annunci e agli aggiornamenti recenti nel [blog su Internet delle cose](https://azure.microsoft.com/blog/topics/internet-of-things/). 