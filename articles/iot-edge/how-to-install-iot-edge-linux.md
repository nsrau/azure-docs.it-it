---
title: Installare Azure IoT Edge in Linux | Microsoft Docs
description: Istruzioni per l'installazione di Azure IoT Edge in dispositivi AMD64 Linux con Ubuntu
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 822efe2534d49c0995a672232107cc322e547989
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227499"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Installare il runtime di Azure IoT Edge in Linux (x64)

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud.

Per altre informazioni, vedere [comprendere il runtime di Azure IOT Edge e la relativa architettura](iot-edge-runtime.md).

Questo articolo elenca i passaggi per installare il runtime di Azure IoT Edge sul dispositivo Ubuntu Linux x64 (Intel/AMD) IoT Edge. Per un elenco dei sistemi operativi AMD64 supportati, vedere [Azure IOT Edge sistemi supportati](support.md#operating-systems) .

> [!NOTE]
> I pacchetti disponibili nei repository di software Linux sono soggetti alle condizioni di licenza indicate in ogni pacchetto (/usr/share/doc/*nome-pacchetto*). Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

## <a name="install-the-latest-version"></a>Installare la versione più recente

Usare le sezioni seguenti per installare la versione più recente del servizio Azure IoT Edge sui dispositivi. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registrare la chiave e il feed del repository software Microsoft

Preparare il dispositivo per l'installazione di IoT Edge Runtime.


Installare la configurazione del repository. Scegliere il frammento di codice **16,04** o **18,04** nel modo appropriato per il rilascio di Ubuntu:

> [!NOTE]
> Assicurarsi di scegliere il frammento di codice dalla casella di codice corretta per la versione di Ubuntu.

* Per **Ubuntu 16,04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   ```

* Per **Ubuntu 18,04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
   ```
   
Copiare l'elenco generato.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installare la chiave pubblica di Microsoft GPG

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Installare il runtime per contenitori

Azure IoT Edge si basa su un runtime del contenitore [compatibile con OCI](https://www.opencontainers.org/). Per gli scenari di produzione, è consigliabile usare il motore [basato su Moby](https://mobyproject.org/) fornito di seguito. È l'unico motore di contenitore supportato ufficialmente con Azure IoT Edge. Le immagini del contenitore Docker CE/EE sono compatibili con il runtime di Moby.

Eseguire l'aggiornamento apt.

   ```bash
   sudo apt-get update
   ```

Installare il motore Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Installare l'interfaccia della riga di comando di Moby. L'interfaccia della riga di comando è utile per lo sviluppo, ma è facoltativa per le distribuzioni di produzione.

   ```bash
   sudo apt-get install moby-cli
   ```

#### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verificare il kernel Linux per la compatibilità con Moby

Molti produttori di dispositivi incorporati spediscono immagini del dispositivo contenenti kernel Linux personalizzati che potrebbero mancare funzionalità necessarie per la compatibilità del runtime del contenitore. Se si verificano problemi durante l'installazione del runtime del contenitore [Moby](https://github.com/moby/moby) consigliato, potrebbe essere possibile risolvere i problemi relativi alla configurazione del kernel Linux usando lo script [Check-Config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) fornito nel [repository GitHub](https://github.com/moby/moby) ufficiale di Moby di eseguire i comandi seguenti nel dispositivo.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Verrà fornito un output dettagliato che contiene lo stato delle funzionalità del kernel utilizzate dal runtime di Moby. È necessario assicurarsi che tutti gli elementi in `Generally Necessary` e `Network Drivers` siano abilitati per garantire che il kernel sia completamente compatibile con il runtime di Moby.  Se sono state identificate le funzionalità mancanti, è possibile abilitarle ricompilando il kernel dall'origine e selezionando i moduli associati da includere nel file kernel. config appropriato.  Analogamente, se si usa un generatore di configurazione kernel come defconfig o menuconfig, sarà necessario trovare e abilitare le rispettive funzionalità e ricompilare il kernel di conseguenza.  Dopo aver distribuito il kernel appena modificato, eseguire di nuovo lo script Check-config per verificare che le funzionalità identificate siano state abilitate correttamente.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Installare il daemon di sicurezza di Azure IoT Edge

Il **daemon di sicurezza di IOT Edge** fornisce e gestisce gli standard di sicurezza sul dispositivo IOT Edge. Il daemon viene avviato a ogni avvio del dispositivo e ne esegue il bootstrap avviando la parte restante del runtime IoT Edge.

Il comando di installazione installa anche la versione standard del **libiothsm** , se non è già presente.

Eseguire l'aggiornamento apt.

   ```bash
   sudo apt-get update
   ```

Installare il daemon di sicurezza. Il pacchetto è installato in `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Una volta completata l'installazione di IoT Edge, l'output richiederà di aggiornare il file di configurazione. Seguire i passaggi nella sezione [configurare il daemon di sicurezza Azure IOT Edge](#configure-the-azure-iot-edge-security-daemon) per completare il provisioning del dispositivo. 

## <a name="install-a-specific-version"></a>Installare una versione specifica

Se si vuole installare una versione specifica di Azure IoT Edge, è possibile indirizzare i file dei componenti direttamente dal repository di GitHub IoT Edge. Usare la procedura seguente per ottenere tutti i componenti IoT Edge nel dispositivo: il motore di Moby e l'interfaccia della riga di comando, il libiothsm e infine il daemon di sicurezza IoT Edge.

1. Passare alla [Azure IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases)e individuare la versione di rilascio di destinazione. 

2. Espandere la sezione **Asset** per la versione.

3. È possibile che non siano presenti aggiornamenti al motore Moby in una determinata versione. Se vengono visualizzati i file che iniziano con **Moby-Engine** e **Moby-CLI**, usare i comandi seguenti per aggiornare tali componenti. Se non viene visualizzato alcun file Moby e non è già installato Moby nel dispositivo, tornare indietro tra gli asset di versione precedenti fino a quando non vengono trovati. 

   1. Trovare il file del **motore di Moby** che corrisponde all'architettura del dispositivo IOT Edge. Fare clic con il pulsante destro del mouse sul collegamento file e copiare l'indirizzo del collegamento.

   2. Usare il collegamento copiato nel comando seguente per installare la versione del motore di Moby: 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Trovare il file **Moby-CLI** corrispondente all'architettura del dispositivo IOT Edge. L'interfaccia della riga di comando di Moby è un componente facoltativo, ma può essere utile durante lo sviluppo. Fare clic con il pulsante destro del mouse sul collegamento file e copiare l'indirizzo del collegamento. 

   4. Usare il collegamento copiato nel comando seguente per installare la versione dell'interfaccia della riga di comando di Moby: 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Ogni versione deve avere nuovi file per il daemon di sicurezza IoT Edge e hsmlib. Usare i comandi seguenti per aggiornare tali componenti. 

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

Una volta completata l'installazione di IoT Edge, l'output richiederà di aggiornare il file di configurazione. Seguire i passaggi della sezione successiva per completare il provisioning del dispositivo. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurare il daemon di sicurezza di Azure IoT Edge

Configurare il runtime di IoT Edge per collegare il dispositivo fisico con un'identità del dispositivo esistente in un hub IoT di Azure.

Il daemon può essere configurato usando il file di configurazione in `/etc/iotedge/config.yaml`. Il file è protetto da scrittura per impostazione predefinita e possono essere necessarie autorizzazioni elevate per modificarlo.

È possibile effettuare il provisioning di un singolo dispositivo IoT Edge manualmente usando una stringa di connessione dispositivo fornita dall'hub IoT. In alternativa, è possibile usare il servizio di provisioning di dispositivi per effettuare il provisioning di dispositivi automaticamente. Ciò è utile quando si dispone di molti dispositivi di cui effettuare il provisioning. A seconda del provisioning, scegliere lo script di installazione appropriato.

### <a name="option-1-manual-provisioning"></a>Opzione 1: provisioning manuale

Per eseguire manualmente il provisioning di un dispositivo, è necessario fornire una [stringa di connessione del dispositivo](how-to-register-device-portal.md) che è possibile creare tramite la registrazione di un nuovo dispositivo nell'hub IoT.

Aprire il file di configurazione.

```bash
sudo nano /etc/iotedge/config.yaml
```

Trovare le configurazioni del provisioning del file e rimuovere il commento dalla sezione di configurazione del provisioning **manuale** . Aggiornare il valore di **device_connection_string** con la stringa di connessione del dispositivo IoT Edge. Assicurarsi che tutte le altre sezioni di provisioning siano impostate come commento.

   ```yaml
   # Manual provisioning configuration
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   # provisioning:
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   attestation:
   #     method: "tpm"
   #     registration_id: "{registration_id}"
```

Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opzione 2: provisioning automatico

Per eseguire il provisioning automatico di un dispositivo, [configurare il servizio Device Provisioning e recuperare l'ID di registrazione del dispositivo](how-to-auto-provision-simulated-device-linux.md). Esistono diversi meccanismi di attestazione supportati da IoT Edge quando si usa il provisioning automatico, ma anche i requisiti hardware influiscano sulle scelte effettuate. I dispositivi Raspberry Pi, ad esempio, non sono dotati di un chip Trusted Platform Module (TPM) per impostazione predefinita.

Aprire il file di configurazione.

```bash
sudo nano /etc/iotedge/config.yaml
```

Trovare le configurazioni del provisioning del file e rimuovere il commento dalla sezione appropriata per il meccanismo di attestazione. Quando si usa l'attestazione TPM, ad esempio, aggiornare i valori di **scope_id** e **registration_id** con i valori del servizio Device provisioning in hub Internet e del dispositivo di IOT Edge con TPM, rispettivamente.

   ```yaml
   # Manual provisioning configuration
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     attestation:
       method: "tpm"
       registration_id: "{registration_id}"
   ```

Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se è stata usata la **configurazione manuale** nella sezione precedente, il runtime IoT Edge deve essere correttamente sottoposto a provisioning e in esecuzione nel dispositivo. Se è stata usata la **configurazione automatica**, è necessario completare alcuni passaggi aggiuntivi in modo che il runtime possa registrare il dispositivo con l'hub IoT per conto dell'utente. Per i passaggi successivi, vedere [creare ed effettuare il provisioning di un TPM simulato IOT Edge dispositivo in una macchina virtuale Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

È possibile verificare lo stato del daemon IoT Edge con il comando seguente:

```bash
systemctl status iotedge
```

Esaminare i log del daemon con il comando seguente:

```bash
journalctl -u iotedge --no-pager --no-full
```

Elencare infine i moduli in esecuzione con il comando seguente:

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>Suggerimenti

Per eseguire comandi `iotedge` sono necessari privilegi elevati. Dopo aver installato il runtime, scollegarsi e ricollegarsi al computer per aggiornare automaticamente le autorizzazioni. Fino ad allora, usare **sudo** davanti ai comandi `iotedge`.

Nei dispositivi con risorse vincolate si consiglia vivamente di impostare la variabile di ambiente *OptimizeForPerformance* su *false*, come indicato nelle istruzioni della [Guida alla risoluzione dei problemi](troubleshoot.md).

Se la rete è dotata di un server proxy, seguire i passaggi descritti in [Configurare il dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

## <a name="uninstall-iot-edge"></a>Disinstallazione di IoT Edge

Per rimuovere IoT Edge da un dispositivo Linux, usare i comandi seguenti dalla riga di comando.

Rimuovere il runtime IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Quando il runtime IoT Edge viene rimosso, i contenitori creati vengono arrestati, ma rimangono sul dispositivo. Verificare tutti i contenitori per vedere quali vengono conservati.

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

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

Se si verificano problemi con il runtime di IoT Edge installato correttamente, consultare la pagina [risoluzione dei problemi](troubleshoot.md) .

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).
