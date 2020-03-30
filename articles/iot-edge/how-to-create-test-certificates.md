---
title: Creare certificati di test - Azure IoT Edge Documenti Microsoft
description: Creare certificati di test e scoprire come installarli in un dispositivo Azure IoT Edge per preparare la distribuzione di produzione.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5afb9b7a6ba1ffb99df064c9f92780dc820b2e8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535987"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Creare certificati dimostrativi per testare le funzionalità dei dispositivi perimetrali IoTCreate demo certificates to test IoT Edge device features

I dispositivi IoT Edge richiedono certificati per la comunicazione sicura tra il runtime, i moduli e tutti i dispositivi downstream.
Se non si dispone di un'autorità di certificazione per creare i certificati necessari, è possibile usare i certificati dimostrativi per provare le funzionalità di IoT Edge nell'ambiente di test.
In questo articolo viene descritta la funzionalità degli script di generazione dei certificati che IoT Edge fornisce per il test.

Questi certificati scadono tra 30 giorni e non devono essere utilizzati in alcuno scenario di produzione.

È possibile creare certificati su qualsiasi computer e quindi copiarli nel dispositivo IoT Edge.You can create certificates on any machine, and then copy them over to your IoT Edge device.
È più facile usare il computer primario per creare i certificati anziché generarli sul dispositivo IoT Edge stesso.
Utilizzando il computer primario, è possibile configurare gli script una sola volta e quindi ripetere il processo per creare certificati per più dispositivi.

Seguire questi passaggi per creare certificati dimostrativi per testare lo scenario IoT Edge:Follow these steps to create demo certificates for testing your IoT Edge scenario:

1. [Configurare gli script](#set-up-scripts) per la generazione di certificati nel dispositivo.
2. [Creare il certificato della CA radice](#create-root-ca-certificate) utilizzato per firmare tutti gli altri certificati per lo scenario.
3. Generare i certificati necessari per lo scenario che si desidera testare:
   * Creare certificati di [identità del dispositivo IoT Edge](#create-iot-edge-device-identity-certificates) per testare il provisioning automatico con il servizio di provisioning dei dispositivi Hub IoT.Create IoT Edge device identity certificates to test automatic provisioning with the IoT Hub Device Provisioning Service.
   * [Creare certificati CA del dispositivo IoT Edge](#create-iot-edge-device-ca-certificates) per testare scenari di produzione o scenari gateway.
   * [Creare certificati di dispositivo downstream](#create-downstream-device-certificates) per testare l'autenticazione dei dispositivi downstream nell'hub IoT in uno scenario gateway.

## <a name="prerequisites"></a>Prerequisiti

Un computer di sviluppo con Git installato.

## <a name="set-up-scripts"></a>Configurare gli script

Il repository IoT Edge in GitHub include script di generazione dei certificati che è possibile usare per creare certificati dimostrativi.
In questa sezione vengono fornite istruzioni per la preparazione degli script per l'esecuzione nel computer, in Windows o Linux.This section provides instructions for preparing the scripts to run on your computer, either on Windows or Linux.
Se si è su un computer Linux, saltare avanti per [Impostare su Linux](#set-up-on-linux).

### <a name="set-up-on-windows"></a>Configurazione in Windows

Per creare certificati dimostrativi in un dispositivo Windows, è necessario installare OpenSSL e quindi clonare gli script di generazione e configurarli per l'esecuzione in locale in PowerShell.To create demo certificates on a Windows device, you need to install OpenSSL and then clone the generation scripts and set up to run locally in PowerShell.

#### <a name="install-openssl"></a>Installare OpenSSL

Installare OpenSSL per Windows nel computer usato per generare i certificati.
Se OpenSSL è già installato nel dispositivo Windows, è possibile ignorare questo passaggio, ma assicurarsi che openssl.exe sia disponibile nella variabile di ambiente PATH.

Esistono diversi modi per installare OpenSSL, incluse le seguenti opzioni:

* **Più facile:** Scaricare e installare eventuali [file binari OpenSSL](https://wiki.openssl.org/index.php/Binaries)di terze parti, ad esempio da [OpenSSL su SourceForge](https://sourceforge.net/projects/openssl/). Aggiungere il percorso completo di openssl.exe alla variabile di ambiente PATH.

* **Metodo consigliato:** scaricare il codice sorgente di OpenSSL source code e creare i file binari nel computer personalmente o tramite [vcpkg](https://github.com/Microsoft/vcpkg). Nelle istruzioni elencate di seguito viene usato vcpkg per scaricare il codice sorgente, compilare e installare OpenSSL nel computer Windows con passaggi molto semplici.

   1. Passare alla directory in cui installare vcpkg, Seguire le istruzioni per scaricare e installare [vcpkg](https://github.com/Microsoft/vcpkg).

   2. Una volta installato vcpkg, eseguire il comando seguente da un prompt di PowerShell per installare il pacchetto OpenSSL per Windows x64. Il completamento dell'installazione richiede in genere circa 5 minuti.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Aggiungere `<vcpkg path>\installed\x64-windows\tools\openssl` alla variabile di ambiente PATH in modo da rendere il file openssl.exe disponibile per la chiamata.

#### <a name="prepare-scripts-in-powershell"></a>Preparare gli script in PowerShellPrepare scripts in PowerShell

Il repository git di Azure IoT Edge contiene script che è possibile usare per generare certificati di test.
In questa sezione si clona il repository IoT Edge ed si eseguono gli script.

1. Aprire una finestra di PowerShell in modalità amministratore.

2. Clonare il repository git IoT Edge, che contiene script per generare certificati dimostrativi. Usare il comando `git clone` o [scaricare il file ZIP](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Passare alla directory che si vuole usare, In questo articolo, chiameremo questa directory * \<WRKDIR>*. Tutti i certificati e le chiavi verranno creati in questa directory di lavoro.

4. Copiare i file di configurazione e di script dal repository clonato nella directory di lavoro.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Se il repository è stato scaricato come cap, il nome della cartella è `iotedge-master` e il resto del percorso è lo stesso.

5. Abilitare PowerShell per l'esecuzione degli script.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Portare le funzioni utilizzate dagli script nello spazio dei nomi globale di PowerShell.Bring the functions used by the scripts into PowerShell's global namespace.

   ```powershell
   . .\ca-certs.ps1
   ```

   Nella finestra di PowerShell verrà visualizzato un avviso che i certificati generati da questo script sono solo a scopo di test e non devono essere utilizzati negli scenari di produzione.

7. Verificare che OpenSSL sia stato installato correttamente e assicurarsi che non si verifichino conflitti di nomi con i certificati esistenti. In caso di problemi, l'output dello script deve descrivere come risolverli nel sistema.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Configurazione su Linux

Per creare certificati dimostrativi in un dispositivo Windows, è necessario clonare gli script di generazione e configurarli per l'esecuzione in locale in bash.

1. Clonare il repository git IoT Edge, che contiene script per generare certificati dimostrativi.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Passare alla directory che si vuole usare, Faremo riferimento a questa directory in tutto l'articolo come * \<WRKDIR>*. Tutti i file di certificato e di chiave verranno creati in questa directory.
  
3. Copiare i file di configurazione e di script dal repository IoT Edge clonato nella directory di lavoro.

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

## <a name="create-root-ca-certificate"></a>Creare un certificato CA radice

Il certificato della CA radice viene utilizzato per creare tutti gli altri certificati demo per il test di uno scenario IoT Edge.The root CA certificate is used to make all the other demo certificates for testing an IoT Edge scenario.
È possibile continuare a usare lo stesso certificato della CA radice per creare certificati dimostrativi per più dispositivi IoT Edge o downstream.

Se nella cartella di lavoro è già presente un certificato CA radice, non crearne uno nuovo.
Il nuovo certificato della CA radice sovrascriverà quello precedente e tutti i certificati downstream effettuati da quello precedente smetteranno di funzionare.
Se si desidera più certificati CA radice, assicurarsi di gestirli in cartelle separate.

Prima di procedere con i passaggi descritti in questa sezione, seguire i passaggi nella sezione [Configurare gli script](#set-up-scripts) per preparare una directory di lavoro con gli script demo di generazione dei certificati.

### <a name="windows"></a>WINDOWS

1. Passare alla directory di lavoro in cui sono stati inseriti gli script di generazione dei certificati.

1. Creare il certificato della CA radice e fare in modo che firmi un certificato intermedio. I certificati vengono tutti inseriti nella directory di lavoro.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Questo comando di script crea diversi file di certificato e di chiave, ma quando gli articoli chiedono il **certificato della CA principale**, utilizzare il file seguente:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Passare alla directory di lavoro in cui sono stati inseriti gli script di generazione dei certificati.

1. Creare il certificato della CA radice e un certificato intermedio.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Questo comando di script crea diversi file di certificato e di chiave, ma quando gli articoli chiedono il **certificato della CA principale**, utilizzare il file seguente:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>Creare certificati CA per dispositivi IoT Edge

Ogni dispositivo IoT Edge che va all'ambiente di produzione richiede un certificato CA del dispositivo a cui viene fatto riferimento dal file config.yaml.
Il certificato CA del dispositivo è responsabile della creazione di certificati per i moduli in esecuzione nel dispositivo.
È anche il modo in cui il dispositivo IoT Edge verifica la propria identità quando si connette ai dispositivi downstream.

I certificati CA per dispositivi vengono visualizzati nella sezione **Certificate** del file config.yaml nel dispositivo IoT Edge.

Prima di procedere con i passaggi descritti in questa sezione, seguire i passaggi descritti nelle sezioni [Configurare gli script](#set-up-scripts) e Creare il [certificato della CA radice.](#create-root-ca-certificate)

### <a name="windows"></a>WINDOWS

1. Passare alla directory di lavoro contenente gli script di generazione del certificato e il certificato della CA radice.

2. Creare il certificato CA del dispositivo IoT Edge e la chiave privata con il comando seguente. Specificare un nome per il certificato CA, ad esempio **MyEdgeDeviceCA**, utilizzato per denominare i file di output.

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   Questo comando di script crea diversi file di certificato e di chiave. La seguente coppia di certificati e chiavi deve essere copiata in un dispositivo IoT Edge e a cui viene fatto riferimento nel file config.yaml:

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

Il nome del dispositivo gateway passato in tali script non deve essere lo stesso del parametro "hostname" in config.yaml o dell'ID del dispositivo nell'hub IoT.
Gli script consentono di evitare eventuali problemi aggiungendo la stringa ".ca" al nome del dispositivo gateway per evitare un conflitto di nomi nel caso in cui l'utente configuri IoT Edge con lo stesso nome per entrambi.
Tuttavia, è buona norma evitare di usare lo stesso nome.

### <a name="linux"></a>Linux

1. Passare alla directory di lavoro contenente gli script di generazione del certificato e il certificato della CA radice.

2. Creare il certificato CA del dispositivo IoT Edge e la chiave privata con il comando seguente. Specificare un nome per il certificato CA, ad esempio **MyEdgeDeviceCA**, utilizzato per denominare i file di output.

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   Questo comando di script crea diversi file di certificato e di chiave. La seguente coppia di certificati e chiavi deve essere copiata in un dispositivo IoT Edge e a cui viene fatto riferimento nel file config.yaml:

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

Il nome del dispositivo gateway passato in tali script non deve essere lo stesso del parametro "hostname" in config.yaml o dell'ID del dispositivo nell'hub IoT.
Gli script consentono di evitare eventuali problemi aggiungendo la stringa ".ca" al nome del dispositivo gateway per evitare un conflitto di nomi nel caso in cui l'utente configuri IoT Edge con lo stesso nome per entrambi.
Tuttavia, è buona norma evitare di usare lo stesso nome.

## <a name="create-iot-edge-device-identity-certificates"></a>Creare certificati di identità dei dispositivi IoT EdgeCreate IoT Edge device identity certificates

I certificati di identità del dispositivo vengono usati per eseguire il provisioning dei dispositivi IoT Edge tramite il servizio Di provisioning dispositivi [Hub-IoT (DPS) di Azure IoT.](../iot-dps/index.yml)

I certificati di identità del dispositivo vengono visualizzati nella sezione **Provisioning** del file config.yaml nel dispositivo IoT Edge.

Prima di procedere con i passaggi descritti in questa sezione, seguire i passaggi descritti nelle sezioni [Configurare gli script](#set-up-scripts) e Creare il [certificato della CA radice.](#create-root-ca-certificate)

### <a name="windows"></a>WINDOWS

Creare il certificato di identità del dispositivo IoT Edge e la chiave privata con il comando seguente:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

Il nome passato a questo comando sarà l'ID del dispositivo IoT Edge nell'hub IoT.

Il comando new device identity crea diversi file di certificati e chiavi, tra cui due che verranno utilizzati durante la creazione di una singola registrazione in DPS e l'installazione del runtime di IoT Edge:The new device identity command creates several certificate and key files, including two that you'll use when creating an individual enrollment in DPS and installing the IoT Edge runtime:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Creare il certificato di identità del dispositivo IoT Edge e la chiave privata con il comando seguente:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

Il nome passato a questo comando sarà l'ID del dispositivo IoT Edge nell'hub IoT.

Lo script crea diversi file di certificati e di chiave, inclusi due che verranno utilizzati durante la creazione di una singola registrazione in DPS e l'installazione del runtime di IoT Edge:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-downstream-device-certificates"></a>Creare certificati di dispositivo downstreamCreate downstream device certificates

Se si configura un dispositivo IoT downstream per uno scenario gateway, è possibile generare certificati dimostrativi per l'autenticazione X.509.If you're setting up a downstream IoT device for a gateway scenario, you can generate demo certificates for X.509 authentication.
Esistono due modi per autenticare un dispositivo IoT usando certificati X.509: utilizzando certificati autofirmati o certificati firmati dall'autorità di certificazione (CA).
Per l'autenticazione autofirmata X.509, talvolta definita autenticazione di identificazione personale, è necessario creare nuovi certificati da inserire nel dispositivo IoT.For X.509 self-signed authentication, sometimes referred to as thumbprint authentication, you need to create new certificates to place on your IoT device.
Questi certificati contesto di identificazione personale che si condividono con l'hub IoT per l'autenticazione.
Per l'autenticazione firmata dall'autorità di certificazione (CA) X.509, è necessario un certificato CA radice registrato nell'hub IoT usato per firmare i certificati per il dispositivo IoT.For X.509 certificate authority (CA) signed authentication, you need a root CA certificate registered in IoT Hub that you use to sign certificates for your IoT device.
Qualsiasi dispositivo che utilizza un certificato emesso dal certificato della CA radice o da uno dei relativi certificati intermedi sarà autorizzato ad eseguire l'autenticazione.

Gli script di generazione dei certificati consentono di creare certificati dimostrativi per testare uno di questi scenari di autenticazione.

Prima di procedere con i passaggi descritti in questa sezione, seguire i passaggi descritti nelle sezioni [Configurare gli script](#set-up-scripts) e Creare il [certificato della CA radice.](#create-root-ca-certificate)

### <a name="self-signed-certificates"></a>Certificati autofirmati

Quando si autentica un dispositivo IoT con certificati autofirmati, è necessario creare certificati del dispositivo in base al certificato della CA radice per la soluzione.
Quindi, si recupera un'impronta digitale esadecimale dai certificati da fornire all'hub IoT.
Il dispositivo IoT necessita anche di una copia dei relativi certificati del dispositivo in modo che possa eseguire l'autenticazione con l'hub IoT.Your IoT device also needs a copy of its device certificates so that it can authenticate with IoT Hub.

#### <a name="windows"></a>WINDOWS

1. Passare alla directory di lavoro contenente gli script di generazione del certificato e il certificato della CA radice.

2. Creare due certificati (primario e secondario) per il dispositivo downstream. Una convenzione di denominazione semplice da usare consiste nel creare i certificati con il nome del dispositivo IoT e quindi l'etichetta primaria o secondaria. Ad esempio:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Questo comando di script crea diversi file di certificato e di chiave. The following certificate and key pairs needs to be copied over to the downstream IoT device and referenced in the applications that connect to IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Recuperare l'impronta digitale SHA1 (denominata identificazione personale nei contesti dell'hub IoT) da ogni certificato. L'impronta digitale è una stringa di 40 caratteri esadecimali. Utilizzare il seguente comando openssl per visualizzare il certificato e trovare l'impronta digitale:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Eseguire questo comando due volte, una volta per il certificato primario e una per il certificato secondario. Le impronte digitali vengono fornite per entrambi i certificati quando si registra un nuovo dispositivo IoT utilizzando certificati X.509 autofirmati.

#### <a name="linux"></a>Linux

1. Passare alla directory di lavoro contenente gli script di generazione del certificato e il certificato della CA radice.

2. Creare due certificati (primario e secondario) per il dispositivo downstream. Una convenzione di denominazione semplice da usare consiste nel creare i certificati con il nome del dispositivo IoT e quindi l'etichetta primaria o secondaria. Ad esempio:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Questo comando di script crea diversi file di certificato e di chiave. The following certificate and key pairs needs to be copied over to the downstream IoT device and referenced in the applications that connect to IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Recuperare l'impronta digitale SHA1 (denominata identificazione personale nei contesti dell'hub IoT) da ogni certificato. L'impronta digitale è una stringa di 40 caratteri esadecimali. Utilizzare il seguente comando openssl per visualizzare il certificato e trovare l'impronta digitale:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Specificare l'impronta digitale primaria e secondaria quando si registra un nuovo dispositivo IoT utilizzando certificati X.509 autofirmati.

### <a name="ca-signed-certificates"></a>Certificati firmati dall'autorità di certificazione

Quando si autentica un dispositivo IoT con certificati autofirmati, è necessario caricare il certificato della CA radice per la soluzione nell'hub IoT.When you authenticate an IoT device with self-signed certificates, you need to upload the root CA certificate for your solution to IoT Hub.
Quindi, si esegue una verifica per dimostrare all'hub IoT che si è proprietari del certificato della CA radice.
Infine, si usa lo stesso certificato CA radice per creare certificati del dispositivo da inserire nel dispositivo IoT in modo che possa eseguire l'autenticazione con l'hub IoT.Finally, you use the same root CA certificate to create device certificates to put on your IoT device so that it can authenticate with IoT Hub.

I certificati in questa sezione sono per la procedura descritta in Configurare la [sicurezza X.509 nell'hub IoT](../iot-hub/iot-hub-security-x509-get-started.md)di Azure.

#### <a name="windows"></a>WINDOWS

1. Caricare il file del certificato della `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`CA radice dalla directory di lavoro, , nell'hub IoT.

2. Usare il codice fornito nel portale di Azure per verificare che si è proprietari del certificato della CA radice.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Creare una catena di certificati per il dispositivo downstream. Usa lo stesso ID dispositivo con cui è registrato il dispositivo nell'hub IoT.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Questo comando di script crea diversi file di certificato e di chiave. The following certificate and key pairs needs to be copied over to the downstream IoT device and referenced in the applications that connect to IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Caricare il file del certificato della `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`CA radice dalla directory di lavoro, , nell'hub IoT.

2. Usare il codice fornito nel portale di Azure per verificare che si è proprietari del certificato della CA radice.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Creare una catena di certificati per il dispositivo downstream. Usa lo stesso ID dispositivo con cui è registrato il dispositivo nell'hub IoT.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Questo comando di script crea diversi file di certificato e di chiave. The following certificate and key pairs needs to be copied over to the downstream IoT device and referenced in the applications that connect to IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
