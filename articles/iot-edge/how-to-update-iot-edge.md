---
title: Versione di aggiornamento IoT Edge nei dispositivi - Azure IoT Edge | Microsoft Docs
description: Come aggiornare i dispositivi IoT Edge per eseguire le versioni più recenti del daemon di sicurezza e del runtime IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/07/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4a7c27beeb7208efcf6687e49193c8d3b68f5300
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186502"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aggiornare il daemon di sicurezza e il runtime di IoT Edge

Quando il servizio IoT Edge rilascia nuove versioni, è opportuno aggiornare i dispositivi IoT Edge per le funzionalità più recenti e i miglioramenti della sicurezza. Questo articolo fornisce informazioni su come aggiornare i dispositivi IoT Edge quando è disponibile una nuova versione.

Se si vuole passare a una versione più recente, devono essere aggiornati due componenti in un dispositivo IoT Edge. Il primo è il daemon di sicurezza, che viene eseguito nel dispositivo e avvia i moduli runtime quando il dispositivo viene avviato. Attualmente, il daemon di sicurezza può essere aggiornato solo dal dispositivo stesso. Il secondo componente è il runtime, costituito dai moduli dell'agente IoT Edge e dell'hub di IoT Edge. A seconda del modo in cui si struttura la distribuzione, il runtime può essere aggiornato dal dispositivo o in modalità remota.

Per trovare la versione più recente di Azure IoT Edge, fare riferimento alle [versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Aggiornare il daemon di sicurezza

Il daemon di sicurezza di IoT Edge è un componente nativo che deve essere aggiornato usando la gestione pacchetti nel dispositivo IoT Edge.

Controllare la versione del daemon di sicurezza in esecuzione nel dispositivo tramite il comando `iotedge version`.

### <a name="linux-devices"></a>Dispositivi Linux

Nei dispositivi Linux x64 usare apt-get o il gestore di pacchetti appropriato per aggiornare il daemon di sicurezza alla versione più recente.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Se si vuole eseguire l'aggiornamento a una versione specifica del daemon di sicurezza, trovare la versione di destinazione da [IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases). In tale versione individuare i file **libiothsm-STD** e **iotedge** appropriati per il dispositivo. Per ogni file, fare clic con il pulsante destro del mouse sul collegamento file e copiare l'indirizzo del collegamento. Utilizzare l'indirizzo del collegamento per installare le versioni specifiche di tali componenti:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Dispositivi Windows

Nei dispositivi Windows, usare lo script di PowerShell per aggiornare il daemon di sicurezza. Lo script esegue automaticamente il pull della versione più recente del daemon di sicurezza.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

L'esecuzione del comando Update-IoTEdge rimuove e aggiorna il daemon di sicurezza dal dispositivo, insieme alle due immagini del contenitore di Runtime. Il file config. YAML viene mantenuto nel dispositivo e i dati del motore di contenitori di Moby (se si usano i contenitori di Windows). Mantenendo le informazioni di configurazione significa che non è necessario fornire di nuovo la stringa di connessione o le informazioni sul servizio Device provisioning per il dispositivo durante il processo di aggiornamento.

Se si vuole eseguire l'aggiornamento a una versione specifica del daemon di sicurezza, trovare la versione di destinazione da [IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases). In tale versione, scaricare il file **Microsoft-Azure-IoTEdge. cab** . Usare quindi il parametro `-OfflineInstallationPath` per puntare al percorso del file locale. Ad esempio:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>Il `-OfflineInstallationPath` parametro Cerca un file denominato **Microsoft-Azure-IoTEdge. cab** nella directory specificata. A partire da IoT Edge versione 1.0.9-RC4, sono disponibili due file CAB da usare, uno per i dispositivi AMD64 e uno per ARM32. Scaricare il file corretto per il dispositivo, quindi rinominare il file per rimuovere il suffisso Architecture.

Per ulteriori informazioni sulle opzioni di aggiornamento, utilizzare il comando `Get-Help Update-IoTEdge -full` o fare riferimento a [tutti i parametri di installazione](how-to-install-iot-edge-windows.md#all-installation-parameters).

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

Se si usano tag specifici nella distribuzione (ad esempio, mcr.microsoft.com/azureiotedge-hub:**1.0.8**), è sufficiente aggiornare il tag nel manifesto di distribuzione e applicare le modifiche al dispositivo.

1. Nell'hub Internet delle cose del portale di Azure selezionare il dispositivo IoT Edge e selezionare **imposta moduli**.

1. Nella sezione **moduli di IOT Edge** selezionare **impostazioni di runtime**.

   ![Configurare le impostazioni di runtime](./media/how-to-update-iot-edge/configure-runtime.png)

1. In **impostazioni runtime**aggiornare il valore dell' **immagine** per **Hub Edge** con la versione desiderata. Non selezionare **Salva** ancora.

   ![Aggiornare la versione dell'immagine dell'Hub Edge](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Comprimere le impostazioni dell' **Hub perimetrale** o scorrere verso il basso e aggiornare il valore dell' **immagine** per l' **agente Edge** con la stessa versione desiderata.

   ![Aggiornare la versione dell'agente Hub Edge](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Selezionare **Salva**.

1. Selezionare **Verifica e crea**, esaminare la distribuzione e selezionare **Crea**.

## <a name="update-to-a-release-candidate-version"></a>Eseguire l'aggiornamento a una versione finale candidata

Azure IoT Edge rilascia periodicamente nuove versioni del servizio IoT Edge. Prima di ogni versione stabile, sono presenti una o più versioni di Release Candidate (RC). Le versioni RC includono tutte le funzionalità pianificate per la versione, ma sono ancora in fase di test e convalida. Se si vuole testare una nuova funzionalità in anticipo, è possibile installare una versione RC e inviare commenti e suggerimenti tramite GitHub.

Le versioni finali candidate seguono la stessa convenzione di numerazione delle versioni, ma hanno **-RC** più un numero incrementale aggiunto alla fine. È possibile visualizzare i candidati della versione nello stesso elenco di [Azure IOT Edge](https://github.com/Azure/azure-iotedge/releases) versioni stabili. Ad esempio, trovare **1.0.7-RC1** e **1.0.7-RC2**, i due candidati della versione precedenti a **1.0.7**. È anche possibile notare che le versioni RC sono contrassegnate con le etichette della **versione non definitiva** .

I moduli dell'agente IoT Edge e dell'Hub hanno versioni RC contrassegnate con la stessa convenzione. Ad esempio, **MCR.Microsoft.com/azureiotedge-Hub:1.0.7-RC2**.

Come anteprime, le versioni release candidate non sono incluse come ultima versione di destinazione dei programmi di installazione regolari. Al contrario, è necessario assegnare manualmente gli asset per la versione RC che si desidera testare. Nella maggior parte dei casi, l'installazione o l'aggiornamento a una versione RC è uguale alla destinazione di qualsiasi altra versione specifica di IoT Edge, ad eccezione di una differenza per i dispositivi Windows. 

In una versione finale candidata, lo script di PowerShell che consente di installare e gestire il daemon di sicurezza IoT Edge in un dispositivo Windows può avere funzionalità diverse rispetto alla versione disponibile a livello generale più recente. Oltre al download del file IoT Edge. cab per la versione RC, scaricare anche lo script **IotEdgeSecurityDaemon. ps1** . Usare l'origine dei [punteggi](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) per eseguire lo script scaricato nell'origine corrente. Ad esempio: 

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Usare le sezioni di questo articolo per informazioni su come aggiornare un dispositivo IoT Edge a una versione specifica del daemon di sicurezza o dei moduli di Runtime.

Se si sta installando IoT Edge in un nuovo computer, usare i collegamenti seguenti per informazioni su come installare una versione specifica a seconda del sistema operativo del dispositivo:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>Passaggi successivi

Visualizzare le [versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) più recenti.

Rimanere aggiornati grazie agli annunci e agli aggiornamenti recenti nel [blog su Internet delle cose](https://azure.microsoft.com/blog/topics/internet-of-things/).
