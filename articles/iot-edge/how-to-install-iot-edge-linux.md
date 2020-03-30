---
title: Installare Azure IoT Edge in Linux | Microsoft Docs
description: Azure IoT Edge installation instructions on Linux devices running Ubuntu or Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: kgremban
ms.openlocfilehash: fb86ee9ce956917f8da44146e58a4775e0ba639f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535902"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Installare il runtime Azure IoT Edge in sistemi Linux basati su Debian

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud. Per altre informazioni, vedere Informazioni sul runtime di [Azure IoT Edge e sulla relativa architettura.](iot-edge-runtime.md)

Questo articolo elenca i passaggi per installare il runtime di Azure IoT Edge in un dispositivo Linux X64, ARM32 o ARM64.This article lists the steps to install the Azure IoT Edge runtime on an X64, ARM32, or ARM64 Linux device. Forniamo pacchetti di installazione per Ubuntu Server 16.04, Ubuntu Server 18.04 e Raspbian Stretch. Fare riferimento [ai sistemi supportati da Azure IoT Edge](support.md#operating-systems) per un elenco delle architetture e dei sistemi operativi Linux supportati.

> [!NOTE]
> I pacchetti disponibili nei repository di software Linux sono soggetti alle condizioni di licenza indicate in ogni pacchetto (/usr/share/doc/*nome-pacchetto*). Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

## <a name="install-the-latest-runtime-version"></a>Installare la versione di runtime più recente

Usare le sezioni seguenti per installare la versione più recente del runtime di Azure IoT Edge nel dispositivo.

>[!NOTE]
>Il supporto per i dispositivi ARM64 è in [anteprima pubblica.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registrare la chiave e il feed del repository software Microsoft

Preparare il dispositivo per l'installazione di runtime di IoT Edge.Prepare your device for the IoT Edge runtime installation.

Installare la configurazione del repository. Scegliere il comando **16.04** o **18.04** corrispondente al sistema operativo del dispositivo:

* **Ubuntu Server 16.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Tratto Raspbia**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copiare l'elenco generato.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installare la chiave pubblica Microsoft GPG

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Installare il runtime per contenitori

Azure IoT Edge si basa su un runtime per contenitori [compatibile con OCI](https://www.opencontainers.org/). Per gli scenari di produzione, è consigliabile utilizzare il motore [basato su Moby](https://mobyproject.org/) fornito di seguito. The Moby engine is the only container engine officially supported with Azure IoT Edge. Le immagini del contenitore Docker CE/EE sono compatibili con il runtime di Moby.

Aggiornare gli elenchi dei pacchetti nel dispositivo.

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

Se si verificano errori durante l'installazione del runtime del contenitore Moby, attenersi alla procedura per [verificare il kernel Linux per la compatibilità Moby](#verify-your-linux-kernel-for-moby-compatibility), fornito più avanti in questo articolo.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Installare il daemon di sicurezza di Azure IoT Edge

Il **daemon di sicurezza IoT Edge** fornisce e mantiene gli standard di sicurezza sul dispositivo IoT Edge. Il daemon viene avviato a ogni avvio del dispositivo e ne esegue il bootstrap avviando la parte restante del runtime IoT Edge.

Il comando di installazione installa anche la versione standard del **libiotsm** se non è già presente.

Aggiornare gli elenchi dei pacchetti nel dispositivo.

   ```bash
   sudo apt-get update
   ```

Installare il daemon di sicurezza. Il pacchetto è installato in `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Una volta installato Correttamente IoT Edge, l'output richiederà di aggiornare il file di configurazione. Seguire i passaggi nella sezione [Configurare il daemon](#configure-the-security-daemon) di sicurezza per completare il provisioning dei dispositivi.

## <a name="install-a-specific-runtime-version"></a>Installare una versione di runtime specificaInstall a specific runtime version

Se si vuole installare una versione specifica di Moby e il runtime di Azure IoT Edge invece di usare le versioni più recenti, è possibile utilizzare i file dei componenti direttamente dal repository Di GitHub di IoT Edge. Utilizzare la procedura seguente per ottenere tutti i componenti IoT Edge sul dispositivo: il motore Moby e CLI, il libiothsm e infine il daemon di sicurezza IoT Edge. Passare alla sezione [successiva, Configurare il daemon](#configure-the-security-daemon)di sicurezza, se non si desidera passare a una versione di runtime specifica.

1. Passare alle versioni di [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)e trovare la versione di rilascio di destinazione.

2. Espandere la sezione **Risorse** per tale versione.

3. Potrebbero essere aggiornati o meno aggiornamenti al motore Moby in una determinata versione. Se vengono visualizzati file che iniziano con **moby-engine** e **moby-cli**, utilizzare i comandi seguenti per aggiornare tali componenti. Se non vedi alcun file Moby, torna attraverso le risorse di rilascio precedenti fino a trovare la versione più recente.

   1. Trova il file **moby-engine** che corrisponde all'architettura del tuo dispositivo IoT Edge. Fare clic con il pulsante destro del mouse sul collegamento del file e copiare l'indirizzo del collegamento.

   2. Utilizzare il collegamento copiato nel comando seguente per installare tale versione del motore Moby:

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Trova il file **moby-cli** che corrisponde all'architettura del tuo dispositivo IoT Edge. L'interfaccia della riga di comando di Moby è un componente facoltativo, ma può essere utile durante lo sviluppo. Fare clic con il pulsante destro del mouse sul collegamento del file e copiare l'indirizzo del collegamento.

   4. Utilizzare il collegamento copiato nel comando seguente per installare tale versione dell'interfaccia della riga di comando di Moby:

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Ogni versione dovrebbe avere nuovi file per il daemon di sicurezza IoT Edge e hsmlib. Utilizzare i comandi seguenti per aggiornare tali componenti.

   1. Trova il file **libiothsm-std** che corrisponde all'architettura del tuo dispositivo IoT Edge. Fare clic con il pulsante destro del mouse sul collegamento del file e copiare l'indirizzo del collegamento.

   2. Utilizzare il collegamento copiato nel comando seguente per installare tale versione di hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Trova il file **iotedge** che corrisponde all'architettura del tuo dispositivo IoT Edge. Fare clic con il pulsante destro del mouse sul collegamento del file e copiare l'indirizzo del collegamento.

   4. Utilizzare il collegamento copiato nel comando seguente per installare tale versione del daemon di sicurezza IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Una volta installato Correttamente IoT Edge, l'output richiederà di aggiornare il file di configurazione. Seguire i passaggi nella sezione successiva per completare il provisioning dei dispositivi.

## <a name="configure-the-security-daemon"></a>Configurare il daemon di sicurezza

Configurare il runtime di IoT Edge per collegare il dispositivo fisico con un'identità del dispositivo esistente in un hub IoT di Azure.

Il daemon può essere configurato usando il file di configurazione in `/etc/iotedge/config.yaml`. Il file è protetto da scrittura per impostazione predefinita e possono essere necessarie autorizzazioni elevate per modificarlo.

È possibile effettuare il provisioning di un singolo dispositivo IoT Edge manualmente usando una stringa di connessione dispositivo fornita dall'hub IoT. In alternativa, è possibile usare il servizio di provisioning di dispositivi per effettuare il provisioning di dispositivi automaticamente. Ciò è utile quando si dispone di molti dispositivi di cui effettuare il provisioning. A seconda del provisioning, scegliere lo script di installazione appropriato.

### <a name="option-1-manual-provisioning"></a>Opzione 1: Provisioning manuale

Per eseguire manualmente il provisioning di un dispositivo, è necessario fornire una [stringa di connessione del dispositivo](how-to-register-device.md#register-in-the-azure-portal) che è possibile creare tramite la registrazione di un nuovo dispositivo nell'hub IoT.

Aprire il file di configurazione.

```bash
sudo nano /etc/iotedge/config.yaml
```

Individuare le configurazioni di provisioning del file e rimuovere il commento nella sezione **Configurazione del provisioning manuale.** Aggiornare il valore di **device_connection_string** con la stringa di connessione del dispositivo IoT Edge. Assicurati che tutte le altre sezioni di provisioning siano commentate. Assicurati che la riga **provisioning:** non contengono spazi vuoti precedenti e che gli elementi nidificati siano rientrati di due spazi.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Per incollare il `Shift+Right Click` contenuto `Shift+Insert`degli Appunti in Nano o premere .

Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opzione 2: Provisioning automatico

È possibile eseguire automaticamente il provisioning dei dispositivi IoT Edge usando il servizio Di provisioning dei dispositivi [Hub (DPS) di Azure IoT.](../iot-dps/index.yml) Attualmente, IoT Edge supporta due meccanismi di attestazione quando si utilizza il provisioning automatico, ma i requisiti hardware possono influire sulle scelte. Ad esempio, i dispositivi Raspberry Pi non sono dotati di un chip Trusted Platform Module (TPM) per impostazione predefinita. Per altre informazioni, vedere gli articoli seguenti:

* [Creare ed eseguire il provisioning di un dispositivo IoT Edge con un TPM virtuale in una macchina virtuale LinuxCreate and provision an IoT Edge device with a virtual TPM on a Linux VM](how-to-auto-provision-simulated-device-linux.md)
* [Creare ed eseguire il provisioning di un dispositivo IoT Edge usando i certificati X.509Create and provision an IoT Edge device using X.509 certificates](how-to-auto-provision-x509-certs.md)
* [Creare ed eseguire il provisioning di un dispositivo IoT Edge usando l'attestazione a chiave simmetricaCreate and provision an IoT Edge device using symmetric key attestation](how-to-auto-provision-symmetric-keys.md)

Questi articoli illustrano come impostare le registrazioni in DPS e generare i certificati o le chiavi appropriati per l'attestazione. Indipendentemente dal meccanismo di attestazione scelto, le informazioni di provisioning vengono aggiunte al file di configurazione IoT Edge nel dispositivo IoT Edge.

Aprire il file di configurazione.

```bash
sudo nano /etc/iotedge/config.yaml
```

Individuare le configurazioni di provisioning del file e rimuovere il commento dalla sezione appropriata per il meccanismo di attestazione. Assicurati che tutte le altre sezioni di provisioning siano commentate. Il **provisioning:** riga non deve avere spazi vuoti precedenti e gli elementi nidificati devono essere rientrati di due spazi. Aggiornare il valore di **scope_id** con il valore dell'istanza del servizio di provisioning dei dispositivi dell'hub IoT e fornire i valori appropriati per i campi di attestazione.

Attestazione TPM:

```yml
# DPS TPM provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "tpm"
    registration_id: "<REGISTRATION_ID>"
```

Attestazione X.509:

```yml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

Attestazione a chiave simmetrica:

```yml
# DPS symmetric key provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "symmetric_key"
    registration_id: "<REGISTRATION_ID>"
    symmetric_key: "<SYMMETRIC_KEY>"
```

Per incollare il `Shift+Right Click` contenuto `Shift+Insert`degli Appunti in Nano o premere .

Salvare e chiudere il file. `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se è stata usata la **configurazione manuale** nella sezione precedente, il runtime IoT Edge deve essere correttamente sottoposto a provisioning e in esecuzione nel dispositivo. Se è stata usata la **configurazione automatica**, è necessario completare alcuni passaggi aggiuntivi in modo che il runtime possa registrare il dispositivo con l'hub IoT per conto dell'utente. Per i passaggi successivi, vedere Creare ed eseguire il provisioning di [un dispositivo TPM IoT Edge simulato in una macchina virtuale Linux.For](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)next steps, see Create and provision a simulated TPM IoT Edge device on a Linux virtual machine .

È possibile controllare lo stato del daemon Edge IoT:

```bash
systemctl status iotedge
```

Esaminare i registri di daemon:

```bash
journalctl -u iotedge --no-pager --no-full
```

Eseguire un controllo automatico per individuare gli errori di configurazione e di rete più comuni:Run an automated check for the most common configuration and networking errors:

```bash
sudo iotedge check
```

Fino a quando non si distribuisce il primo modulo in IoT Edge nel dispositivo, il modulo di sistema **$edgeHub** non verrà distribuito nel dispositivo. Di conseguenza, il controllo automatico restituirà `Edge Hub can bind to ports on host` un errore per il controllo di connettività. Questo errore può essere ignorato a meno che non si verifichi dopo la distribuzione di un modulo nel dispositivo.

Infine, elencare i moduli in esecuzione:

```bash
sudo iotedge list
```

Dopo aver installato IoT Edge sul dispositivo, l'unico modulo che dovrebbe essere visualizzato in esecuzione è **edgeAgent**. Una volta creata la prima distribuzione, anche l'altro **modulo di** sistema $edgeHub verrà avviato sul dispositivo. Per ulteriori informazioni, consultate [Distribuire moduli IoT Edge.](how-to-deploy-modules-portal.md)

## <a name="tips-and-troubleshooting"></a>Suggerimenti e risoluzione dei problemi

Per eseguire comandi `iotedge` sono necessari privilegi elevati. Dopo aver installato il runtime, scollegarsi e ricollegarsi al computer per aggiornare automaticamente le autorizzazioni. Fino ad allora, usare **sudo** davanti ai comandi `iotedge`.

Nei dispositivi con risorse vincolate, si consiglia vivamente di impostare la variabile di ambiente *OptimizeForPerformance* su *false*, come indicato nelle istruzioni della [Guida alla risoluzione dei problemi](troubleshoot.md).

Se la rete è dotata di un server proxy, seguire i passaggi descritti in [Configurare il dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verificare la compatibilità del kernel Linux per la compatibilità con Moby

Molti produttori di dispositivi embedded spediscono immagini di dispositivi che contengono kernel Linux personalizzati senza le funzionalità necessarie per la compatibilità del runtime del contenitore. Se si verificano problemi durante l'installazione del runtime del contenitore Moby consigliato, è possibile risolvere i problemi di configurazione del kernel Linux utilizzando lo script [di configurazione](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) di controllo dal repository ufficiale [Moby GitHub](https://github.com/moby/moby). Eseguire i seguenti comandi sul dispositivo per verificare la configurazione del kernel:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Questo comando fornisce un output dettagliato che contiene lo stato delle funzionalità del kernel utilizzate dal runtime di Moby. È necessario assicurarsi che `Generally Necessary` tutti `Network Drivers` gli elementi inclusi e siano abilitati per garantire che il kernel sia completamente compatibile con il runtime di Moby.  Se sono state identificate funzionalità mancanti, abilitarle ricompilando il kernel dall'origine e selezionando i moduli associati per l'inclusione nel file config del kernel appropriato.  Allo stesso modo, se si utilizza `defconfig` `menuconfig`un generatore di configurazione del kernel come o , trovare e abilitare le rispettive funzionalità e ricostruire il kernel di conseguenza.  Dopo aver distribuito il kernel appena modificato, eseguire nuovamente lo script di configurazione di controllo per verificare che tutte le funzionalità necessarie siano state abilitate correttamente.

## <a name="uninstall-iot-edge"></a>Disinstallare IoT Edge

Per rimuovere IoT Edge da un dispositivo Linux, usare i comandi seguenti dalla riga di comando.

Rimuovere il runtime IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Quando il runtime IoT Edge viene rimosso, i contenitori creati vengono arrestati ma ancora presenti nel dispositivo. Verificare tutti i contenitori per vedere quali vengono conservati.

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

Se si verificano problemi con l'installazione corretta del runtime di IoT Edge, vedere la pagina di [risoluzione dei problemi.](troubleshoot.md)

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).
