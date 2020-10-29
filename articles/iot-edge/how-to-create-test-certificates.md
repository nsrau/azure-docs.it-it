---
title: Creare certificati di test-Azure IoT Edge | Microsoft Docs
description: Creare certificati di test e apprendere come installarli in un dispositivo Azure IoT Edge per prepararsi alla distribuzione di produzione.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c5af77da0ed2c579a478c8ebaaa924882d9a15c6
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927703"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Creare certificati demo per testare le funzionalità del dispositivo IoT Edge

I dispositivi IoT Edge richiedono certificati per la comunicazione sicura tra il runtime, i moduli e qualsiasi dispositivo downstream.
Se non si dispone di un'autorità di certificazione per creare i certificati necessari, è possibile usare i certificati demo per provare le funzionalità di IoT Edge nell'ambiente di test.
Questo articolo descrive la funzionalità degli script di generazione dei certificati che IoT Edge fornisce per i test.

Questi certificati scadono entro 30 giorni e non devono essere usati in uno scenario di produzione.

È possibile creare certificati in qualsiasi computer e quindi copiarli nel dispositivo IoT Edge.
È più semplice usare il computer principale per creare i certificati anziché generarli sul dispositivo IoT Edge stesso.
Usando il computer principale, è possibile configurare gli script una sola volta e quindi usarli per creare certificati per più dispositivi.

Seguire questa procedura per creare i certificati demo per testare lo scenario di IoT Edge:

1. [Configurare gli script](#set-up-scripts) per la generazione di certificati nel dispositivo.
2. [Creare il certificato CA radice](#create-root-ca-certificate) usato per firmare tutti gli altri certificati per lo scenario.
3. Generare i certificati necessari per lo scenario che si desidera testare:
   * [Creare IOT Edge certificati di identità del dispositivo](#create-iot-edge-device-identity-certificates) per il provisioning automatico con il servizio Device provisioning in hub Internet.
   * [Creare certificati della CA del dispositivo IOT Edge](#create-iot-edge-device-ca-certificates) per IOT Edge dispositivi in scenari gateway.
   * [Creare certificati di dispositivo downstream](#create-downstream-device-certificates) per l'autenticazione di dispositivi downstream in uno scenario di gateway.

## <a name="prerequisites"></a>Prerequisiti

Un computer di sviluppo con git installato.

## <a name="set-up-scripts"></a>Configurare gli script

Il repository IoT Edge su GitHub include script di generazione dei certificati che è possibile usare per creare certificati demo.
In questa sezione vengono fornite le istruzioni per preparare gli script da eseguire nel computer, in Windows o Linux.
Se ci si trova in un computer Linux, passare alla pagina [configurare in Linux](#set-up-on-linux).

### <a name="set-up-on-windows"></a>Configurare in Windows

Per creare certificati demo in un dispositivo Windows, è necessario installare OpenSSL, quindi clonare gli script di generazione e impostarli per l'esecuzione in locale in PowerShell.

#### <a name="install-openssl"></a>Installare OpenSSL

Installare OpenSSL per Windows nel computer usato per generare i certificati.
Se è già installato OpenSSL nel dispositivo Windows, assicurarsi che openssl.exe sia disponibile nella variabile di ambiente PATH.

Sono disponibili diversi modi per installare OpenSSL, incluse le opzioni seguenti:

* **Più semplice:** Scaricare e installare i [file binari OpenSSL di terze parti](https://wiki.openssl.org/index.php/Binaries), ad esempio, da [openssl su SourceForge](https://sourceforge.net/projects/openssl/). Aggiungere il percorso completo di openssl.exe alla variabile di ambiente PATH.

* **Metodo consigliato:** scaricare il codice sorgente di OpenSSL source code e creare i file binari nel computer personalmente o tramite [vcpkg](https://github.com/Microsoft/vcpkg). Nelle istruzioni elencate di seguito viene usato vcpkg per scaricare il codice sorgente, compilare e installare OpenSSL nel computer Windows con passaggi molto semplici.

   1. Passare alla directory in cui installare vcpkg, Seguire le istruzioni per scaricare e installare [vcpkg](https://github.com/Microsoft/vcpkg).

   2. Dopo l'installazione di vcpkg, eseguire il comando seguente da un prompt di PowerShell per installare il pacchetto OpenSSL per Windows x64. Il completamento dell'installazione richiede in genere circa 5 minuti.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Aggiungere `<vcpkg path>\installed\x64-windows\tools\openssl` alla variabile di ambiente PATH in modo da rendere il file openssl.exe disponibile per la chiamata.

#### <a name="prepare-scripts-in-powershell"></a>Preparare gli script in PowerShell

Il repository git Azure IoT Edge contiene gli script che è possibile usare per generare i certificati di test.
In questa sezione si clona il repository IoT Edge ed eseguire gli script.

1. Aprire una finestra di PowerShell in modalità amministratore.

2. Clonare il repository git IoT Edge, che contiene gli script per generare i certificati demo. Usare il comando `git clone` o [scaricare il file ZIP](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Passare alla directory che si vuole usare, In questo articolo verrà chiamata questa directory *\<WRKDIR>* . Tutti i certificati e le chiavi verranno creati in questa directory di lavoro.

4. Copiare i file di configurazione e script dal repository clonato alla directory di lavoro.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Se il repository è stato scaricato come ZIP, il nome della cartella è `iotedge-master` e il resto del percorso è lo stesso.

5. Abilitare PowerShell per l'esecuzione degli script.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Portare le funzioni usate dagli script nello spazio dei nomi globale di PowerShell.

   ```powershell
   . .\ca-certs.ps1
   ```

   Nella finestra di PowerShell verrà visualizzato un avviso indicante che i certificati generati da questo script sono solo a scopo di test e non devono essere utilizzati in scenari di produzione.

7. Verificare che OpenSSL sia stato installato correttamente e assicurarsi che non siano presenti conflitti di nome con i certificati esistenti. Se si verificano problemi, l'output dello script dovrebbe descrivere come risolverli nel sistema.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Configurare in Linux

Per creare certificati demo in un dispositivo Windows, è necessario clonare gli script di generazione e configurarli per l'esecuzione localmente in bash.

1. Clonare il repository git IoT Edge, che contiene gli script per generare i certificati demo.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Passare alla directory che si vuole usare, In questo articolo verrà fatto riferimento a questa directory come *\<WRKDIR>* . Tutti i file di certificato e di chiave verranno creati in questa directory.
  
3. Copiare i file di configurazione e script dal repository IoT Edge clonato nella directory di lavoro.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>Crea certificato CA radice

Il certificato CA radice viene usato per creare tutti gli altri certificati demo per il test di uno scenario di IoT Edge.
È possibile usare lo stesso certificato CA radice per creare certificati demo per più dispositivi IoT Edge o downstream.

Se si dispone già di un certificato CA radice nella cartella di lavoro, non crearne uno nuovo.
Il nuovo certificato CA radice sovrascriverà il vecchio e tutti i certificati downstream ricavati da quello precedente smetteranno di funzionare.
Se si desiderano più certificati CA radice, assicurarsi di gestirli in cartelle separate.

Prima di procedere con i passaggi descritti in questa sezione, seguire i passaggi della sezione [configurare gli script](#set-up-scripts) per preparare una directory di lavoro con gli script di generazione dei certificati demo.

### <a name="windows"></a>Windows

1. Passare alla directory di lavoro in cui sono stati inseriti gli script di generazione del certificato.

1. Creare il certificato CA radice e firmare un certificato intermedio. Tutti i certificati vengono inseriti nella directory di lavoro.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Questo comando script crea diversi file di certificato e di chiave, ma quando gli articoli richiedono il **certificato CA radice** , usare il file seguente:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Passare alla directory di lavoro in cui sono stati inseriti gli script di generazione del certificato.

1. Creare il certificato CA radice e un certificato intermedio.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Questo comando script crea diversi file di certificato e di chiave, ma quando gli articoli richiedono il **certificato CA radice** , usare il file seguente:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-identity-certificates"></a>Creare certificati di identità del dispositivo IoT Edge

I certificati di identità dei dispositivi vengono usati per effettuare il provisioning di dispositivi IoT Edge tramite il servizio Device provisioning in hub Azure.

I certificati di identità del dispositivo vengono inseriti nella sezione **provisioning** del file config. YAML sul dispositivo IOT Edge.

Prima di procedere con i passaggi descritti in questa sezione, seguire i passaggi descritti nelle sezioni [configurare gli script](#set-up-scripts) e [creare il certificato CA radice](#create-root-ca-certificate) .

### <a name="windows"></a>Windows

Creare il certificato di identità del dispositivo IoT Edge e la chiave privata con il comando seguente:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

Il nome passato a questo comando sarà l'ID del dispositivo per il dispositivo IoT Edge nell'hub Internet.

Il nuovo comando Device Identity crea diversi file di certificato e chiave, inclusi tre che verranno usati per la creazione di una registrazione singola in DPS e l'installazione del IoT Edge Runtime:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Creare il certificato di identità del dispositivo IoT Edge e la chiave privata con il comando seguente:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

Il nome passato a questo comando sarà l'ID del dispositivo per il dispositivo IoT Edge nell'hub Internet.

Lo script crea diversi file di certificato e chiave, inclusi tre che verranno usati durante la creazione di una registrazione singola in DPS e l'installazione del runtime di IoT Edge:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-iot-edge-device-ca-certificates"></a>Creare certificati CA del dispositivo IoT Edge

Ogni dispositivo IoT Edge per l'ambiente di produzione necessita di un certificato CA del dispositivo a cui viene fatto riferimento dal file config. yaml.
Il certificato della CA del dispositivo è responsabile della creazione di certificati per i moduli in esecuzione nel dispositivo.
È anche necessario per gli scenari di gateway, perché il certificato della CA del dispositivo è il modo in cui il dispositivo IoT Edge verifica la propria identità nei dispositivi downstream.

I certificati della CA del dispositivo vengono inseriti nella sezione **certificato** del file config. YAML sul dispositivo IOT Edge.

Prima di procedere con i passaggi descritti in questa sezione, seguire i passaggi descritti nelle sezioni [configurare gli script](#set-up-scripts) e [creare il certificato CA radice](#create-root-ca-certificate) .

### <a name="windows"></a>Windows

1. Passare alla directory di lavoro con gli script di generazione del certificato e il certificato CA radice.

2. Creare il certificato della CA del dispositivo IoT Edge e la chiave privata con il comando seguente. Specificare un nome per il certificato della CA.

   ```powershell
   New-CACertsEdgeDevice "<CA cert name>"
   ```

   Questo comando crea diversi file di certificato e chiave. Il certificato e la coppia di chiavi seguenti devono essere copiati in un dispositivo IoT Edge e a cui viene fatto riferimento nel file config. YAML:

   * `<WRKDIR>\certs\iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<CA cert name>.key.pem`

Il nome passato al comando **New-CACertsEdgeDevice** non deve corrispondere al parametro hostname in config. YAML o all'ID del dispositivo nell'hub Internet.

### <a name="linux"></a>Linux

1. Passare alla directory di lavoro con gli script di generazione del certificato e il certificato CA radice.

2. Creare il certificato della CA del dispositivo IoT Edge e la chiave privata con il comando seguente. Specificare un nome per il certificato della CA.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "<CA cert name>"
   ```

   Questo comando script crea diversi file di certificato e chiave. Il certificato e la coppia di chiavi seguenti devono essere copiati in un dispositivo IoT Edge e a cui viene fatto riferimento nel file config. YAML:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Il nome passato al comando **create_edge_device_ca_certificate** non deve corrispondere al parametro hostname in config. YAML o all'ID del dispositivo nell'hub Internet.

## <a name="create-downstream-device-certificates"></a>Creare certificati downstream per i dispositivi

Se si sta configurando un dispositivo a valle per uno scenario di gateway e si vuole usare l'autenticazione X. 509, è possibile generare i certificati demo per il dispositivo downstream.
Se si vuole usare l'autenticazione con chiave simmetrica, non è necessario creare certificati aggiuntivi per il dispositivo downstream.
Esistono due modi per autenticare un dispositivo Internet delle cose usando i certificati X. 509: usando i certificati autofirmati o i certificati firmati dell'autorità di certificazione (CA).
Per l'opzione di autenticazione X.509 autofirmato, detta anche autenticazione tramite identificazione personale, è necessario creare nuovi certificati da inserire nel dispositivo IoT.
Questi certificati hanno un'identificazione personale che viene condivisa con l'hub IoT per l'autenticazione.
Per l'opzione di autenticazione X.509 firmato dall'Autorità di certificazione, è necessario un certificato CA radice registrato nell'hub IoT che si usa per firmare i certificati per il dispositivo IoT.
Qualsiasi dispositivo che usa un certificato emesso dal certificato CA radice o da qualsiasi certificato intermedio sarà autorizzato a eseguire l'autenticazione.

Gli script di generazione del certificato consentono di creare certificati demo per testare uno di questi scenari di autenticazione.

Prima di procedere con i passaggi descritti in questa sezione, seguire i passaggi descritti nelle sezioni [configurare gli script](#set-up-scripts) e [creare il certificato CA radice](#create-root-ca-certificate) .

### <a name="self-signed-certificates"></a>Certificati autofirmati

Quando si autentica un dispositivo Internet con certificati autofirmati, è necessario creare i certificati del dispositivo in base al certificato della CA radice per la soluzione.
Viene quindi recuperata una "impronta digitale" esadecimale dai certificati da fornire all'hub Internet.
Il dispositivo Internet delle cose necessita anche di una copia dei certificati del dispositivo in modo che possa autenticarsi con l'hub Internet.

#### <a name="windows"></a>Windows

1. Passare alla directory di lavoro con gli script di generazione del certificato e il certificato CA radice.

2. Creare due certificati (primario e secondario) per il dispositivo downstream. Una convenzione di denominazione semplice da usare consiste nel creare i certificati con il nome del dispositivo Internet delle cose e quindi con l'etichetta primaria o secondaria. Esempio:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Questo comando script crea diversi file di certificato e chiave. Il certificato e le coppie di chiavi seguenti devono essere copiati nel dispositivo degli elementi figlio downstream e a cui viene fatto riferimento nelle applicazioni che si connettono all'hub Internet:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Recuperare l'impronta digitale SHA1 (denominata identificazione personale nei contesti dell'hub degli stessi) da ogni certificato. L'impronta digitale è una stringa di caratteri esadecimale 40. Usare il comando OpenSSL seguente per visualizzare il certificato e trovare l'impronta digitale:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint
   ```

   Eseguire questo comando due volte, una volta per il certificato primario e una per il certificato secondario. Si forniscono le impronte digitali per entrambi i certificati quando si registra un nuovo dispositivo IoT usando certificati X.509 autofirmati.

#### <a name="linux"></a>Linux

1. Passare alla directory di lavoro con gli script di generazione del certificato e il certificato CA radice.

2. Creare due certificati (primario e secondario) per il dispositivo downstream. Una convenzione di denominazione semplice da usare consiste nel creare i certificati con il nome del dispositivo Internet delle cose e quindi con l'etichetta primaria o secondaria. Esempio:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Questo comando script crea diversi file di certificato e chiave. Il certificato e le coppie di chiavi seguenti devono essere copiati nel dispositivo degli elementi figlio downstream e a cui viene fatto riferimento nelle applicazioni che si connettono all'hub Internet:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Recuperare l'impronta digitale SHA1 (denominata identificazione personale nei contesti dell'hub degli stessi) da ogni certificato. L'impronta digitale è una stringa di caratteri esadecimale 40. Usare il comando OpenSSL seguente per visualizzare il certificato e trovare l'impronta digitale:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Si forniscono l'impronta digitale primaria e secondaria quando si registra un nuovo dispositivo Internet con certificati X. 509 autofirmati.

### <a name="ca-signed-certificates"></a>Certificati firmati dall'autorità di certificazione

Quando si autentica un dispositivo Internet con certificati autofirmati, è necessario caricare il certificato della CA radice per la soluzione nell'hub Internet.
Quindi, si esegue una verifica per dimostrare all'hub tutto il proprietario del certificato CA radice.
Infine, si usa lo stesso certificato CA radice per creare i certificati del dispositivo da inserire nel dispositivo Internet delle cose, in modo che sia possibile eseguire l'autenticazione con l'hub Internet.

I certificati in questa sezione sono relativi alla procedura di [configurazione della sicurezza X. 509 nell'hub Azure](../iot-hub/iot-hub-security-x509-get-started.md).

#### <a name="windows"></a>Windows

1. Caricare il file del certificato CA radice dalla directory di lavoro, `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` , all'hub Internet.

2. Usare il codice fornito nella portale di Azure per verificare che si è proprietari del certificato CA radice.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Creare una catena di certificati per il dispositivo downstream. Usare lo stesso ID dispositivo con cui è registrato il dispositivo nell'hub Internet.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Questo comando script crea diversi file di certificato e chiave. Il certificato e le coppie di chiavi seguenti devono essere copiati nel dispositivo degli elementi figlio downstream e a cui viene fatto riferimento nelle applicazioni che si connettono all'hub Internet:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Caricare il file del certificato CA radice dalla directory di lavoro, `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` , all'hub Internet.

2. Usare il codice fornito nella portale di Azure per verificare che si è proprietari del certificato CA radice.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Creare una catena di certificati per il dispositivo downstream. Usare lo stesso ID dispositivo con cui è registrato il dispositivo nell'hub Internet.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Questo comando script crea diversi file di certificato e chiave. Il certificato e le coppie di chiavi seguenti devono essere copiati nel dispositivo degli elementi figlio downstream e a cui viene fatto riferimento nelle applicazioni che si connettono all'hub Internet:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
