---
title: Installare Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge le istruzioni di installazione nei dispositivi Windows o Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kgremban
ms.openlocfilehash: 3a02459f5b92aa7d708c29c737ed9428ed14215a
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045687"
---
# <a name="install-or-uninstall-the-azure-iot-edge-runtime"></a>Installare o disinstallare il runtime di Azure IoT Edge

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Quando un dispositivo viene configurato con il runtime IoT Edge, è possibile iniziare a distribuirvi la logica di business dal cloud. Per altre informazioni, vedere [comprendere il runtime di Azure IOT Edge e la relativa architettura](iot-edge-runtime.md).

Per configurare un dispositivo di IoT Edge, è necessario eseguire due passaggi. Il primo passaggio consiste nell'installare il runtime e le relative dipendenze, come spiegato in questo articolo. Il secondo passaggio consiste nel connettere il dispositivo alla propria identità nel cloud e configurare l'autenticazione con l'hub Internet. Questi passaggi sono descritti negli articoli successivi.

Questo articolo elenca i passaggi per installare il runtime di Azure IoT Edge in dispositivi Linux o Windows. Per i dispositivi Windows, è possibile scegliere di usare i contenitori Linux o i contenitori di Windows. Attualmente, i contenitori di Windows in Windows sono consigliati per gli scenari di produzione. I contenitori Linux in Windows sono utili per gli scenari di sviluppo e test, soprattutto se si sviluppa in un PC Windows per la distribuzione nei dispositivi Linux.

## <a name="prerequisites"></a>Prerequisiti

Per informazioni aggiornate sui sistemi operativi attualmente supportati per gli scenari di produzione, vedere [Azure IOT Edge sistemi supportati](support.md#operating-systems)

# <a name="linux"></a>[Linux](#tab/linux)

Avere un dispositivo Linux x64, ARM32 o ARM64. Microsoft fornisce i pacchetti di installazione per Ubuntu Server 16,04, Ubuntu server 18,04 e i sistemi operativi Raspbian stretch.

>[!NOTE]
>Il supporto per i dispositivi ARM64 è in versione di [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Preparare il dispositivo per l'accesso ai pacchetti di installazione Microsoft.

1. Installare la configurazione del repository corrispondente al sistema operativo del dispositivo.

   * **Server Ubuntu 16,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Server Ubuntu 18,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Estensione Raspbian**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. Copiare l'elenco generato nella directory Sources. list. d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Installare la chiave pubblica GPG Microsoft.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

# <a name="windows"></a>[Windows](#tab/windows)

### <a name="windows-version"></a>Versione di Windows

IoT Edge con i contenitori di Windows richiede Windows versione 1809/Build 17762, ovvero la build più recente per il [supporto a lungo termine di Windows](/windows/release-information/). Per gli scenari di sviluppo e test, è possibile usare qualsiasi SKU (Pro, Enterprise, server e così via) che supporta la funzionalità dei contenitori. Tuttavia, assicurarsi di rivedere l'elenco dei sistemi supportati prima di passare alla produzione.

IoT Edge con i contenitori Linux possono essere eseguiti in qualsiasi versione di Windows che soddisfi i [requisiti per il desktop Docker](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

### <a name="container-engine-requirements"></a>Requisiti del motore del contenitore

Azure IoT Edge si basa su un motore di contenitori [compatibile con OCI](https://www.opencontainers.org/) . Verificare che il dispositivo sia in grado di supportare i contenitori.

Se si sta installando IoT Edge in una macchina virtuale, abilitare la virtualizzazione annidata e allocare almeno 2 GB di memoria. Per Hyper-V, per le macchine virtuali di seconda generazione la virtualizzazione nidificata è abilitata per impostazione predefinita. Per VMware, è disponibile un interruttore per abilitare la funzionalità nella macchina virtuale.

---

I pacchetti software Azure IoT Edge sono soggetti alle condizioni di licenza presenti in ogni pacchetto ( `usr/share/doc/{package-name}` o `LICENSE` Directory). Leggere le condizioni di licenza prima di utilizzare un pacchetto. L'installazione e l'utilizzo di un pacchetto costituiscono l'accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non utilizzare il pacchetto.

## <a name="install-a-container-engine"></a>Installare un motore di contenitori

Azure IoT Edge si basa su un runtime per contenitori compatibile con OCI. Per gli scenari di produzione, è consigliabile usare il motore basato su Moby. Il motore Moby è l'unico motore di contenitori supportato ufficialmente con Azure IoT Edge. Le immagini del contenitore Docker CE/EE sono compatibili con il runtime di Moby.

# <a name="linux"></a>[Linux](#tab/linux)

Aggiornare gli elenchi di pacchetti nel dispositivo.

   ```bash
   sudo apt-get update
   ```

Installare il motore Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Se si verificano errori durante l'installazione del motore di contenitori di Moby, verificare il kernel Linux per la compatibilità con Moby. Alcuni produttori di dispositivi incorporati spediscono immagini del dispositivo che contengono kernel Linux personalizzati senza le funzionalità richieste per la compatibilità del motore del contenitore. Eseguire il comando seguente, che usa lo [script Check-Config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) fornito da Moby per verificare la configurazione del kernel:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Nell'output dello script verificare che tutti gli elementi in `Generally Necessary` e `Network Drivers` siano abilitati. Se mancano funzionalità, abilitarle ricompilando il kernel dall'origine e selezionando i moduli associati da includere nel file kernel. config appropriato. Analogamente, se si usa un generatore di configurazione kernel come `defconfig` o `menuconfig` , trovare e abilitare le rispettive funzionalità e ricompilare il kernel di conseguenza. Dopo aver distribuito il kernel appena modificato, eseguire di nuovo lo script Check-config per verificare che tutte le funzionalità necessarie siano state abilitate correttamente.

# <a name="windows"></a>[Windows](#tab/windows)

Per gli scenari di produzione, usare il motore basato su Moby incluso nello script di installazione. Non sono previsti passaggi aggiuntivi per installare il motore di.

Per IoT Edge con i contenitori Linux, è necessario fornire il proprio runtime del contenitore. Installare [Docker desktop](https://docs.docker.com/docker-for-windows/install/) nel dispositivo e configurarlo per l' [uso di contenitori Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) prima di continuare.

---

## <a name="install-the-iot-edge-security-daemon"></a>Installare il daemon di sicurezza di IoT Edge

Il daemon di sicurezza di IoT Edge fornisce e gestisce gli standard di sicurezza sul dispositivo IoT Edge. Il daemon viene avviato a ogni avvio del dispositivo e ne esegue il bootstrap avviando la parte restante del runtime IoT Edge.

I passaggi descritti in questa sezione rappresentano il processo tipico per installare la versione più recente in un dispositivo con connessione a Internet. Se è necessario installare una versione specifica, ad esempio una versione non definitiva o se è necessario installarla mentre è offline, seguire i passaggi di [installazione della versione offline o specifica](#offline-or-specific-version-installation) nella sezione successiva.

# <a name="linux"></a>[Linux](#tab/linux)

Aggiornare gli elenchi di pacchetti nel dispositivo.

   ```bash
   sudo apt-get update
   ```

Verificare quali versioni di IoT Edge sono disponibili.

   ```bash
   apt list -a iotedge
   ```

Se si vuole installare la versione più recente del daemon di sicurezza, usare il comando seguente che installa anche la versione più recente del pacchetto **libiothsm-STD** :

   ```bash
   sudo apt-get install iotedge
   ```

Se si vuole installare una versione specifica del daemon di sicurezza, specificare la versione dall'output dell'elenco apt. Specificare anche la stessa versione per il pacchetto **libiothsm-STD** , che altrimenti installerebbe la versione più recente. Ad esempio, il comando seguente installa la versione più recente della versione di 1.0.8:

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

Se la versione che si vuole installare non è elencata, seguire la procedura di [installazione offline o specifica della versione](#offline-or-specific-version-installation) nella sezione successiva. In questa sezione viene illustrato come specificare come destinazione qualsiasi versione precedente del daemon di sicurezza IoT Edge o versione finale candidata.

# <a name="windows"></a>[Windows](#tab/windows)

1. Eseguire PowerShell come amministratore.

   Usare una sessione AMD64 di PowerShell, non PowerShell (x86). Se non si è certi del tipo di sessione in uso, eseguire il comando seguente:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Eseguire il comando [deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) , che esegue le attività seguenti:

   * Verifica che il computer Windows sia in una versione supportata.
   * Attiva la funzionalità dei contenitori.
   * Scarica il motore Moby e il IoT Edge Runtime.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   `Deploy-IoTEdge`Per impostazione predefinita, il comando usa i contenitori di Windows. Se si vogliono usare i contenitori Linux, aggiungere il `ContainerOs` parametro:

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. A questo punto, l'output potrebbe richiedere il riavvio. In tal caso, riavviare il dispositivo ora.

Quando si installa IoT Edge in un dispositivo, è possibile usare parametri aggiuntivi per modificare il processo, tra cui:

* Indirizzare il traffico attraverso un server proxy
* Puntare il programma di installazione a una directory locale per l'installazione offline.

Per ulteriori informazioni su questi parametri aggiuntivi, vedere [script di PowerShell per IOT Edge in Windows](reference-windows-scripts.md).

---

Ora che il motore del contenitore e il runtime di IoT Edge sono installati nel dispositivo, si è pronti per il passaggio successivo, ovvero per registrare il dispositivo con l'hub Internet e configurare il dispositivo con le informazioni di autenticazione e identità del cloud.

Scegliere il prossimo articolo in base al tipo di autenticazione che si vuole usare:

* [L'autenticazione con chiave simmetrica](how-to-manual-provision-symmetric-key.md) è più veloce per iniziare.
* [L'autenticazione del certificato X. 509](how-to-manual-provision-x509.md) è più sicura per gli scenari di produzione.

## <a name="offline-or-specific-version-installation"></a>Installazione di versioni offline o specifiche

I passaggi descritti in questa sezione si riferiscono a scenari non inclusi nei passaggi di installazione standard. Questo può includere:

* Installare IoT Edge offline
* Installare una versione finale candidata
* In Windows installare una versione diversa da quella più recente

# <a name="linux"></a>[Linux](#tab/linux)

Usare i passaggi in questa sezione se si vuole installare una versione specifica del runtime di Azure IoT Edge che non è disponibile tramite `apt-get install` . L'elenco di pacchetti Microsoft contiene solo un set limitato di versioni recenti e le relative versioni secondarie, quindi questi passaggi sono destinati a chiunque voglia installare una versione precedente o una versione finale candidata.

Usando i comandi CURL, è possibile indirizzare i file dei componenti direttamente dal repository GitHub IoT Edge.

<!-- TODO: Offline installation? -->

1. Passare alla [Azure IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases)e individuare la versione di rilascio di destinazione.

2. Espandere la sezione **Asset** per la versione.

3. Ogni versione deve avere nuovi file per il daemon di sicurezza IoT Edge e hsmlib. Usare i comandi seguenti per aggiornare tali componenti.

   1. Trovare il file **libiothsm-STD** che corrisponde all'architettura del dispositivo IOT Edge. Fare clic con il pulsante destro del mouse sul collegamento file e copiare l'indirizzo del collegamento.

   2. Usare il collegamento copiato nel comando seguente per installare la versione di hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Trovare il file **iotedge** corrispondente all'architettura del dispositivo IOT Edge. Fare clic con il pulsante destro del mouse sul collegamento file e copiare l'indirizzo del collegamento.

   4. Usare il collegamento copiato nel comando seguente per installare la versione del daemon di sicurezza IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

# <a name="windows"></a>[Windows](#tab/windows)

Durante l'installazione vengono scaricati tre file:

* Uno script di PowerShell che contiene le istruzioni di installazione
* Microsoft Azure IoT Edge CAB, che contiene il daemon di sicurezza IoT Edge (iotedged), il motore di contenitori di Moby e l'interfaccia della riga di comando di Moby
* Programma di installazione di Visual C++ Redistributable Package (runtime VC)

Se il dispositivo sarà offline durante l'installazione o se si vuole installare una versione specifica di IoT Edge, è possibile scaricare questi file in anticipo nel dispositivo. Al termine dell'installazione, puntare lo script di installazione alla directory che contiene i file scaricati. Il programma di installazione controlla prima di tutto la directory e quindi Scarica solo i componenti che non sono stati trovati. Se tutti i file sono disponibili offline, è possibile eseguire l'installazione senza connessione Internet.

1. Per i file di installazione IoT Edge più recenti insieme alle versioni precedenti, vedere [Azure IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases).

2. Trovare la versione che si vuole installare e scaricare i file seguenti dalla sezione **Asset** delle note sulla versione sul dispositivo Internet:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab da releases 1.0.9 o versione successiva o Microsoft-Azure-IoTEdge.cab dalle versioni 1.0.8 e precedenti.

   Microsoft-Azure-IotEdge-arm32.cab è disponibile anche a partire da 1.0.9 solo a scopo di test. IoT Edge non è attualmente supportata nei dispositivi Windows ARM32.

   È importante usare lo script di PowerShell della stessa versione del file con estensione cab usato perché la funzionalità Cambia per supportare le funzionalità in ogni versione.

3. Se il file con estensione cab scaricato contiene un suffisso di architettura, rinominare il file semplicemente **Microsoft-Azure-IoTEdge.cab**.

4. Facoltativamente, scaricare un programma di installazione per Visual C++ ridistribuibile. Ad esempio, lo script di PowerShell usa questa versione: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Salvare il programma di installazione nella stessa cartella del dispositivo Internet dell'utente come file di IoT Edge.

5. Per eseguire l'installazione con i componenti offline, il [punto di origine](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) è la copia locale dello script di PowerShell. 

6. Eseguire il comando [deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) con il `-OfflineInstallationPath` parametro. Consente di specificare il percorso assoluto della directory di file. Ad esempio,

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Il comando di distribuzione userà tutti i componenti trovati nella directory di file locale specificata. Se il file con estensione cab o il programma di installazione Visual C++ è mancante, tenterà di scaricarli.

---

Ora che il motore del contenitore e il runtime di IoT Edge sono installati nel dispositivo, si è pronti per il passaggio successivo, ovvero per [autenticare un dispositivo IOT Edge nell'hub](how-to-manual-provision-symmetric-key.md)Internet.

## <a name="uninstall-iot-edge"></a>Disinstallare IoT Edge

Se si vuole rimuovere l'installazione IoT Edge dal dispositivo, usare i comandi seguenti.

# <a name="linux"></a>[Linux](#tab/linux)

Rimuovere il runtime IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Quando il runtime di IoT Edge viene rimosso, tutti i contenitori creati vengono arrestati, ma ancora presenti nel dispositivo. Verificare tutti i contenitori per vedere quali vengono conservati.

```bash
sudo docker ps -a
```

Eliminare i contenitori dal dispositivo, inclusi i due contenitori di runtime.

```bash
sudo docker rm -f <container name>
```

Infine, rimuovere il runtime del contenitore dal dispositivo.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

# <a name="windows"></a>[Windows](#tab/windows)

Se si desidera rimuovere l'installazione di IoT Edge dal dispositivo Windows, utilizzare il comando [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) da una finestra di PowerShell amministrativa. Questo comando rimuove il runtime di IoT Edge, insieme alla configurazione esistente e ai dati del motore Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Per ulteriori informazioni sulle opzioni di disinstallazione, utilizzare il comando `Get-Help Uninstall-IoTEdge -full` .

---

## <a name="next-steps"></a>Passaggi successivi

Dopo l'installazione del runtime di IoT Edge, configurare il dispositivo per la connessione con l'hub Internet. Gli articoli seguenti illustrano come registrare un nuovo dispositivo nel cloud e quindi fornire al dispositivo le informazioni di autenticazione e identità.

Scegliere il prossimo articolo in base al tipo di autenticazione che si vuole usare:

* **Chiave simmetrica**: l'hub Internet e il dispositivo di IOT Edge hanno una copia di una chiave protetta. Quando il dispositivo si connette all'hub Internet, verifica che le chiavi corrispondano. Questo metodo di autenticazione è più veloce per iniziare, ma non come protetto.

  [Configurare un dispositivo di Azure IoT Edge con l'autenticazione con chiave simmetrica](how-to-manual-provision-symmetric-key.md)

* **X. 509 autofirmato**: il dispositivo IOT Edge dispone di certificati di identità x. 509 e l'identificazione personale dei certificati è fornita dall'hub. Quando il dispositivo si connette all'hub Internet, confronta il certificato con l'identificazione personale. Questo metodo di autenticazione è più sicuro e consigliato per gli scenari di produzione.

  [Configurare un dispositivo di Azure IoT Edge con l'autenticazione del certificato X. 509](how-to-manual-provision-x509.md)