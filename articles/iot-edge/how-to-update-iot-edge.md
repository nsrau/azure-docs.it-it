---
title: Versione di aggiornamento IoT Edge nei dispositivi - Azure IoT Edge | Microsoft Docs
description: Come aggiornare i dispositivi IoT Edge per eseguire le versioni più recenti del daemon di sicurezza e del runtime IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/22/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3916855a62e506b12f72de713ccb56e89f846938
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96171807"
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

Ottenere la configurazione più recente del repository da Microsoft:

* **Server Ubuntu 16,04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Server Ubuntu 18,04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Estensione del sistema operativo Raspberry Pi**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copiare l'elenco generato.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installare la chiave pubblica di Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Aggiornare apt.

   ```bash
   sudo apt-get update
   ```

Verificare quali versioni di IoT Edge sono disponibili.

   ```bash
   apt list -a iotedge
   ```

Se si vuole eseguire l'aggiornamento alla versione più recente del daemon di sicurezza, usare il comando seguente che aggiorna anche **libiothsm-STD** alla versione più recente:

   ```bash
   sudo apt-get install iotedge
   ```

Se si vuole eseguire l'aggiornamento a una versione specifica del daemon di sicurezza, specificare la versione dall'output dell'elenco di apt. Ogni volta che **iotedge** viene aggiornato, tenta automaticamente di aggiornare il pacchetto **libiothsm-STD** alla versione più recente, che può causare un conflitto di dipendenza. Se non si passa alla versione più recente, assicurarsi di fare riferimento a entrambi i pacchetti per la stessa versione. Ad esempio, il comando seguente installa una versione specifica della versione di 1.0.9:

   ```bash
   sudo apt-get install iotedge=1.0.9-1 libiothsm-std=1.0.9-1
   ```

Se la versione che si vuole installare non è disponibile tramite apt-get, è possibile usare curl per avere come destinazione qualsiasi versione dal repository [IOT Edge releases](https://github.com/Azure/azure-iotedge/releases) . Per qualsiasi versione che si vuole installare, individuare i file **libiothsm-STD** e **iotedge** appropriati per il dispositivo. Per ogni file, fare clic con il pulsante destro del mouse sul collegamento file e copiare l'indirizzo del collegamento. Utilizzare l'indirizzo del collegamento per installare le versioni specifiche di tali componenti:

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

Se si vuole eseguire l'aggiornamento a una versione specifica del daemon di sicurezza, trovare la versione di destinazione da [IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases). In tale versione, scaricare il file di **Microsoft-Azure-IoTEdge.cab** . Quindi, usare il `-OfflineInstallationPath` parametro per puntare al percorso del file locale. Ad esempio:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>Il `-OfflineInstallationPath` parametro Cerca un file denominato **Microsoft-Azure-IoTEdge.cab** nella directory specificata. A partire da IoT Edge versione 1.0.9-RC4, sono disponibili due file CAB da usare, uno per i dispositivi AMD64 e uno per ARM32. Scaricare il file corretto per il dispositivo, quindi rinominare il file per rimuovere il suffisso Architecture.

Per ulteriori informazioni sulle opzioni di aggiornamento, utilizzare il comando `Get-Help Update-IoTEdge -full` o fare riferimento allo [script di PowerShell per IOT Edge in Windows](reference-windows-scripts.md).

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

1. In **impostazioni runtime** aggiornare il valore dell' **immagine** per **Hub Edge** con la versione desiderata. Non selezionare **Salva** ancora.

   ![Aggiornare la versione dell'immagine dell'Hub Edge](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Comprimere le impostazioni dell' **Hub perimetrale** o scorrere verso il basso e aggiornare il valore dell' **immagine** per l' **agente Edge** con la stessa versione desiderata.

   ![Aggiornare la versione dell'agente Hub Edge](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Selezionare **Salva**.

1. Selezionare **Verifica e crea**, esaminare la distribuzione e selezionare **Crea**.

## <a name="update-offline-or-to-a-specific-version"></a>Aggiornare offline o a una versione specifica

Se si vuole aggiornare un dispositivo offline o eseguire l'aggiornamento a una versione specifica di IoT Edge piuttosto che alla versione più recente, è possibile usare il `-OfflineInstallationPath` parametro.

Per aggiornare un dispositivo IoT Edge vengono usati due componenti:

* Uno script di PowerShell che contiene le istruzioni di installazione
* Microsoft Azure IoT Edge CAB, che contiene il daemon di sicurezza IoT Edge (iotedged), il motore di contenitori di Moby e l'interfaccia della riga di comando di Moby

1. Per i file di installazione IoT Edge più recenti insieme alle versioni precedenti, vedere [Azure IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases).

2. Trovare la versione che si vuole installare e scaricare i file seguenti dalla sezione **Asset** delle note sulla versione sul dispositivo Internet:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab da releases 1.0.9 o versione successiva o Microsoft-Azure-IoTEdge.cab dalle versioni 1.0.8 e precedenti.

   Microsoft-Azure-IotEdge-arm32.cab è disponibile anche a partire da 1.0.9 solo a scopo di test. IoT Edge non è attualmente supportata nei dispositivi Windows ARM32.

   È importante usare lo script di PowerShell della stessa versione del file con estensione cab usato perché la funzionalità Cambia per supportare le funzionalità in ogni versione.

3. Se il file con estensione cab scaricato contiene un suffisso di architettura, rinominare il file semplicemente **Microsoft-Azure-IoTEdge.cab**.

4. Per eseguire l'aggiornamento con i componenti offline, il [punto di origine](/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) è la copia locale dello script di PowerShell. Usare quindi il `-OfflineInstallationPath` parametro come parte del `Update-IoTEdge` comando e fornire il percorso assoluto alla directory di file. Ad esempio,

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Update-IoTEdge -OfflineInstallationPath <path>
   ```

## <a name="update-to-a-release-candidate-version"></a>Eseguire l'aggiornamento a una versione finale candidata

Azure IoT Edge rilascia periodicamente nuove versioni del servizio IoT Edge. Prima di ogni versione stabile, sono presenti una o più versioni di Release Candidate (RC). Le versioni RC includono tutte le funzionalità pianificate per la versione, ma sono ancora in fase di test e convalida. Se si vuole testare una nuova funzionalità in anticipo, è possibile installare una versione RC e inviare commenti e suggerimenti tramite GitHub.

Le versioni finali candidate seguono la stessa convenzione di numerazione delle versioni, ma hanno **-RC** più un numero incrementale aggiunto alla fine. È possibile visualizzare i candidati della versione nello stesso elenco di [Azure IOT Edge](https://github.com/Azure/azure-iotedge/releases) versioni stabili. Trovare, ad esempio, **1.0.9-RC5** e **1.0.9-RC6**, due dei candidati alla versione precedenti a **1.0.9**. È anche possibile notare che le versioni RC sono contrassegnate con le etichette della **versione non definitiva** .

I moduli dell'agente IoT Edge e dell'Hub hanno versioni RC contrassegnate con la stessa convenzione. Ad esempio, **MCR.Microsoft.com/azureiotedge-Hub:1.0.9-RC6**.

Come anteprime, le versioni release candidate non sono incluse come ultima versione di destinazione dei programmi di installazione regolari. Al contrario, è necessario assegnare manualmente gli asset per la versione RC che si desidera testare. Nella maggior parte dei casi, l'installazione o l'aggiornamento a una versione RC equivale alla definizione di una versione specifica di IoT Edge.

Usare le sezioni di questo articolo per informazioni su come aggiornare un dispositivo IoT Edge a una versione specifica del daemon di sicurezza o dei moduli di Runtime.

Se si sta installando IoT Edge, anziché eseguire l'aggiornamento di un'installazione esistente, attenersi alla procedura descritta in [installazione di versioni offline o specifiche](how-to-install-iot-edge.md#offline-or-specific-version-installation).

## <a name="next-steps"></a>Passaggi successivi

Visualizzare le [versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) più recenti.

Per rimanere sempre aggiornati sui recenti aggiornamenti e annunci nel [blog Internet delle cose](https://azure.microsoft.com/blog/topics/internet-of-things/)