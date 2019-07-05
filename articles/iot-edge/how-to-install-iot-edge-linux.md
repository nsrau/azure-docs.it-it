---
title: Installare Azure IoT Edge in Linux | Microsoft Docs
description: Istruzioni per l'installazione di Azure IoT Edge in dispositivi AMD64 Linux con Ubuntu
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: bbab0d8d0947c18cf8e6c178d12fdbd7b335d2b6
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485889"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Installare il runtime di Azure IoT Edge in Linux (x64)

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud.

Per altre informazioni, vedere [informazioni sul runtime di Azure IoT Edge e la relativa architettura](iot-edge-runtime.md).

Questo articolo elenca i passaggi per installare il runtime di Azure IoT Edge in Ubuntu Linux x64 (Intel/AMD) dispositivo IoT Edge. Fare riferimento a [sistemi supportati di Azure IoT Edge](support.md#operating-systems) per un elenco di sistemi operativi AMD64.

> [!NOTE]
> I pacchetti disponibili nei repository di software Linux sono soggetti alle condizioni di licenza indicate in ogni pacchetto (/usr/share/doc/*nome-pacchetto*). Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

## <a name="install-the-latest-version"></a>Installare la versione più recente

Usare le sezioni seguenti per installare la versione più recente del servizio Azure IoT Edge nel dispositivo. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registrare la chiave e il feed del repository software Microsoft

Preparare il dispositivo di IoT Edge installazione del runtime.


Installare la configurazione del repository. Scegliere il **16.04** oppure **18.04** frammento di codice come appropriato per il rilascio di Ubuntu:

> [!NOTE]
> Assicurarsi di che scegliere il frammento di codice nella finestra di codice corretto per la versione di Ubuntu.

* Per la **Ubuntu 16.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   ```

* Per la **Ubuntu 18.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
   ```
   
Copiare l'elenco generato.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installare Microsoft GPG chiave pubblica

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Installare il runtime per contenitori

Azure IoT Edge si basa su un runtime del contenitore [compatibile con OCI](https://www.opencontainers.org/). Per gli scenari di produzione, è consigliabile usare la [basato su Moby](https://mobyproject.org/) motore riportati di seguito. È l'unico motore di contenitore supportato ufficialmente con Azure IoT Edge. Le immagini del contenitore Docker CE/EE sono compatibili con il runtime di Moby.

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

#### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verificare il kernel di Linux per la compatibilità Moby

Molti produttori di dispositivi incorporati spedire immagini per i dispositivi che contengono i kernel Linux personalizzati che è possono che manchino le funzionalità necessarie per la compatibilità di runtime contenitore. Se si verificano problemi quando si installa l'elemento consigliato [Moby](https://github.com/moby/moby) runtime del contenitore, potrebbe essere in grado di risolvere i problemi di configurazione il kernel Linux tramite il [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) script fornito nel ufficiale [repository Moby Github](https://github.com/moby/moby) eseguendo i comandi seguenti nel dispositivo.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Ciò fornirà un output dettagliato che contiene lo stato delle funzionalità di kernel che vengono utilizzate dal runtime Moby. È opportuno verificare che tutti gli elementi sotto `Generally Necessary` e `Network Drivers` sono abilitati per garantire che il kernel sia completamente compatibile con il balena runtime.  Se sono state identificate le funzionalità mancante, è possibile abilitare la ricompilazione del kernel dall'origine e selezionando i moduli associati per l'inclusione nel file config kernel appropriato.  Analogamente, se si usa un generatore di configurazione di kernel come defconfig o menuconfig, sarà necessario trovare e abilitare le rispettive funzionalità e ricompilare il kernel di conseguenza.  Dopo aver distribuito il kernel appena modificati, eseguire lo script di configurazione di controllo per verificare che la funzionalità identificata sono state abilitate correttamente.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Installare il daemon di sicurezza di Azure IoT Edge

Il **daemon sicurezza IoT Edge** fornisce e gestisce gli standard di sicurezza nel dispositivo IoT Edge. Il daemon viene avviato a ogni avvio del dispositivo e ne esegue il bootstrap avviando la parte restante del runtime IoT Edge.

Il comando di installazione installa anche la versione standard del **libiothsm** se non è già presente.

Eseguire l'aggiornamento apt.

   ```bash
   sudo apt-get update
   ```

Installare il daemon di sicurezza. Il pacchetto è installato in `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Una volta IoT Edge è installato correttamente, l'output verrà richiesto di aggiornare il file di configurazione. Seguire i passaggi nel [configurare il daemon di sicurezza di Azure IoT Edge](#configure-the-azure-iot-edge-security-daemon) sezione per completare il provisioning del dispositivo. 

## <a name="install-a-specific-version"></a>Installare una versione specifica

Se si vuole installare una versione specifica di Azure IoT Edge, è possibile scegliere i file dei componenti direttamente dal repository GitHub di IoT Edge. Usare la procedura seguente per ottenere tutte di IoT Edge componenti nel dispositivo: il motore Moby e della riga di comando, il libiothsm e infine il daemon di sicurezza IoT Edge.

1. Passare al [Azure IoT Edge rilascia](https://github.com/Azure/azure-iotedge/releases)e trovare la versione di rilascio che si desidera di destinazione. 

2. Espandere la **asset** sezione per tale versione.

3. Potrebbe esistere o non sia gli aggiornamenti per il motore Moby in qualsiasi versione specificata. Se vengono visualizzati file che iniziano con **moby-engine** e **moby-cli**, usare i comandi seguenti per aggiornare tali componenti. Se qualsiasi file Moby non sono visibili e non è già installato nel dispositivo Moby, ripercorrere gli asset versione precedenti fino a quando non si trovarli. 

   1. Trovare il **moby motore** file corrispondente architettura del dispositivo IoT Edge. Fare clic sul collegamento per il file e copiare l'indirizzo del collegamento.

   2. Usare il collegamento copiato nel comando seguente per installare la versione specifica del motore di Moby: 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Trovare il **moby-cli** file corrispondente architettura del dispositivo IoT Edge. CLI Moby è un componente facoltativo, ma può essere utile durante lo sviluppo. Fare clic sul collegamento per il file e copiare l'indirizzo del collegamento. 

   4. Usare il collegamento copiato nel comando seguente per installare tale versione della CLI Moby: 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. In ogni nuova versione deve avere i nuovi file per il daemon di sicurezza IoT Edge e il hsmlib. Usare i comandi seguenti per aggiornare tali componenti. 

   1. Trovare il **libiothsm-std** file corrispondente architettura del dispositivo IoT Edge. Fare clic sul collegamento per il file e copiare l'indirizzo del collegamento. 

   2. Usare il collegamento copiato nel comando seguente per installare la versione specifica del hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. Trovare il **iotedge** file corrispondente architettura del dispositivo IoT Edge. Fare clic sul collegamento per il file e copiare l'indirizzo del collegamento. 

   4. Usare il collegamento copiato nel comando seguente per installare la versione specifica del daemon di sicurezza IoT Edge. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Una volta IoT Edge è installato correttamente, l'output verrà richiesto di aggiornare il file di configurazione. Seguire i passaggi nella sezione successiva per completare il provisioning del dispositivo. 

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

Individuare la sezione provisioning del file. Rimuovere il commento il **manuale** modalità di provisioning e verificare che il servizio Device provisioning in modalità di provisioning viene impostata come commento. Aggiornare il valore di **device_connection_string** con la stringa di connessione del dispositivo IoT Edge.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning:
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opzione 2: provisioning automatico

Per eseguire il provisioning automatico di un dispositivo, [configurare il servizio Device Provisioning e recuperare l'ID di registrazione del dispositivo](how-to-auto-provision-simulated-device-linux.md). Il provisioning automatico funziona solo con i dispositivi che dispongono di un chip Trusted Platform Module (TPM). Ad esempio, i dispositivi Raspberry Pi non sono dotati di TPM per impostazione predefinita.

Aprire il file di configurazione.

```bash
sudo nano /etc/iotedge/config.yaml
```

Individuare la sezione provisioning del file. Rimuovere il commento il **dps** commento la sezione manuale e la modalità di provisioning. Aggiornare i valori di **scope_id** e **registration_id** con i valori del servizio Device Provisioning in hub IoT e aggiornare il dispositivo IoT Edge con TPM.

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se è stata usata la **configurazione manuale** nella sezione precedente, il runtime IoT Edge deve essere correttamente sottoposto a provisioning e in esecuzione nel dispositivo. Se è stata usata la **configurazione automatica**, è necessario completare alcuni passaggi aggiuntivi in modo che il runtime possa registrare il dispositivo con l'hub IoT per conto dell'utente. Per i passaggi successivi, vedere [crea ed effettuare il provisioning di un dispositivo TPM IoT Edge simulato in una macchina virtuale Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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

Se si sono verificati problemi con il runtime di IoT Edge correttamente l'installazione, consultare il [risoluzione dei problemi](troubleshoot.md) pagina.

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).
