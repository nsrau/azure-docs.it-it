---
title: Eseguire automaticamente il provisioning dei dispositivi con DPS usando i certificati X.509 - Azure IoT Edge Documenti Microsoft
description: Usare i certificati X.509 per testare il provisioning automatico dei dispositivi per Azure IoT Edge con il servizio di provisioning dei dispositiviUse X.509 certificates to test automatic device provisioning for Azure IoT Edge with Device Provisioning Service
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 48c8179af4a4b69924fb943ac98918b48d3a2008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537362"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Creare ed eseguire il provisioning di un dispositivo IoT Edge usando i certificati X.509Create and provision an IoT Edge device using X.509 certificates

Con il [servizio Diprovisioning dispositivi hub IoT (DPS) di Azure IoT](../iot-dps/index.yml)è possibile eseguire automaticamente il provisioning dei dispositivi IoT Edge usando i certificati X.509.With the Azure IoT Hub Device Provisioning Service (DPS) , you can automatically provision IoT Edge devices using X.509 certificates. Se non si ha familiarità con il processo di provisioning automatico, vedere [Concetti relativi al provisioning automatico](../iot-dps/concepts-auto-provisioning.md) prima di continuare.

Questo articolo illustra come creare una registrazione del servizio Device Provisioning usando i certificati X.509 in un dispositivo Edge IoT con la procedura seguente:This article shows you how to create a Device Provisioning Service enrollment using X.509 certificates on an IoT Edge device with the following steps:

* Generare certificati e chiavi.
* Creare una registrazione singola per un dispositivo o una registrazione di gruppo per un set di dispositivi.
* Installare il runtime IoT Edge e registrare il dispositivo con l'hub IoT.

L'uso di certificati X.509 come meccanismo di attestazione è un ottimo approccio per ridimensionare la produzione e semplificare il provisioning di dispositivi. In genere, i certificati X.509 sono disposti in una catena di certificati di attendibilità. A partire da un certificato radice autofirmato o attendibile, ogni certificato nella catena firma il certificato inferiore successivo. Questo modello crea una catena di attendibilità delegata dal certificato radice verso il basso attraverso ogni certificato intermedio fino al certificato "foglia" finale installato in un dispositivo.

## <a name="prerequisites"></a>Prerequisiti

* Un hub IoT attivo.
* Un dispositivo fisico o virtuale per il dispositivo IoT Edge.
* Installata l'ultima versione di [Git.](https://git-scm.com/download/)
* Un'istanza del servizio di provisioning dei dispositivi dell'hub IoT in Azure, collegata all'hub IoT.
  * Se non si dispone di un'istanza del servizio Device Provisioning, seguire le istruzioni in [Configurare l'hub IoT DPS](../iot-dps/quick-setup-auto-provision.md).
  * Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="generate-device-identity-certificates"></a>Generare certificati di identità del dispositivo

Il certificato di identità del dispositivo è un certificato foglia che si connette tramite una catena di certificati di attendibilità al certificato dell'autorità di certificazione (CA) X.509 superiore. Il certificato di identità del dispositivo deve avere un nome comune (CN) impostato sull'ID dispositivo che si desidera assegnare al dispositivo nell'hub IoT.

Device identity certificates are only used for provisioning the IoT Edge device and authenticating the device with Azure IoT Hub. Non firmano certificati, a differenza dei certificati CA che il dispositivo IoT Edge presenta ai moduli o ai dispositivi foglia per la verifica. Per altre informazioni, vedere Dettagli sull'utilizzo dei certificati di [Azure IoT Edge.](iot-edge-certs.md)

Dopo aver creato il certificato di identità del dispositivo, è necessario disporre di due file: un file con estensione cer o pem contenente la parte pubblica del certificato e un file con estensione cer o pem con la chiave privata del certificato. Se si prevede di utilizzare la registrazione di gruppo in DPS, è necessario anche la parte pubblica di un certificato CA intermedio o radice nella stessa catena di certificati di attendibilità.

### <a name="use-test-certificates"></a>Usare i certificati di testUse test certificates

Se non si dispone di un'autorità di certificazione disponibile per creare nuovi certificati di identità e si vuole provare questo scenario, il repository git di Azure IoT Edge contiene script che è possibile usare per generare certificati di test. Questi certificati sono progettati solo per i test di sviluppo e non devono essere utilizzati nell'ambiente di produzione.

Per creare certificati di prova, seguire i passaggi descritti in [Creare certificati dimostrativi per testare le funzionalità dei dispositivi Perimetrali IoT](how-to-create-test-certificates.md). Completare le due sezioni necessarie per configurare gli script di generazione dei certificati e creare un certificato CA radice. Quindi, seguire i passaggi per creare un certificato di identità del dispositivo. Al termine, è necessario disporre della catena di certificati e della coppia di chiavi seguenti:When you're finished, you should have the following certificate chain and key pair:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Sono necessari entrambi questi certificati nel dispositivo IoT Edge.You need both certificates on the IoT Edge device. Se si intende utilizzare la registrazione individuale in DPS, verrà caricato il file .cert.pem. Se si intende utilizzare la registrazione di gruppo in DPS, è necessario anche un certificato CA intermedio o radice nella stessa catena di certificati di attendibilità per caricare. Se si usano certificati dimostrativi, usare il certificato per la `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` registrazione di gruppo.

## <a name="create-a-dps-individual-enrollment"></a>Creare una registrazione individuale DPSCreate a DPS individual enrollment

Usare i certificati e le chiavi generati per creare una singola registrazione in DPS per un singolo dispositivo IoT Edge.Use your generated certificates and keys to create an individual enrollment in DPS for a single IoT Edge device. Le singole registrazioni accettano la parte pubblica del certificato di identità di un dispositivo e lo abbinano al certificato nel dispositivo.

Se si desidera eseguire il provisioning di più dispositivi IoT Edge, seguire i passaggi nella sezione [successiva, Creare una registrazione](#create-a-dps-group-enrollment)di gruppo DPS .

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-monitor.md).

Per ulteriori informazioni sulle registrazioni nel servizio Device Provisioning, vedere [Come gestire le registrazioni dei dispositivi](../iot-dps/how-to-manage-enrollments.md).

1. Nel [portale di Azure](https://portal.azure.com)passare all'istanza del servizio di provisioning dei dispositivi dell'hub IoT.In the Azure portal , navigate to your instance of IoT Hub Device Provisioning Service.

1. In **le impostazioni** selezionare **Gestisci registrazioni**.

1. Selezionare **Aggiungi registrazione singola**, quindi completare la procedura seguente per configurare la registrazione:  

   * **Meccanismo**: Selezionare **X.509**.

   * **Certificato primario .pem o .cer file**: Caricare il file pubblico dal certificato di identità del dispositivo. Se sono stati utilizzati gli script per generare un certificato di prova, scegliere il file seguente:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`

   * **IoT Hub Device ID**: Fornire un ID per il dispositivo, se lo si desidera. È possibile usare gli ID dispositivo per identificare come destinazione un singolo dispositivo per la distribuzione di moduli. Se non si specifica un ID dispositivo, viene utilizzato il nome comune (CN) nel certificato X.509.

   * **Dispositivo IoT Edge:** selezionare True per dichiarare che la registrazione è per un dispositivo IoT Edge.IoT Edge device : Select **True** to declare that the enrollment is for an IoT Edge device.

   * **Selezionare gli hub IoT a cui è possibile assegnare questo dispositivo:** scegliere l'hub IoT collegato a cui si vuole connettere il dispositivo. È possibile scegliere più hub e il dispositivo verrà assegnato a uno di essi in base ai criteri di allocazione selezionati.

   * **Stato iniziale del dispositivo gemello**: Aggiungere un valore di tag da aggiungere al dispositivo gemello se lo si desidera. È possibile utilizzare i tag per scegliere come destinazione gruppi di dispositivi per la distribuzione automatica. Ad esempio:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Selezionare **Salva**.

Ora che esiste una registrazione per questo dispositivo, il runtime di IoT Edge può eseguire automaticamente il provisioning del dispositivo durante l'installazione. Passare alla sezione [Installare il runtime di IoT Edge](#install-the-iot-edge-runtime) per configurare il dispositivo IoT Edge.

## <a name="create-a-dps-group-enrollment"></a>Creare una registrazione di gruppo DPSCreate a DPS group enrollment

Usare i certificati e le chiavi generati per creare una registrazione di gruppo in DPS per più dispositivi IoT Edge.Use your generated certificates and keys to create a group enrollment in DPS for multiple IoT Edge devices. Le registrazioni di gruppo usano un certificato CA intermedio o radice dalla catena di certificati di attendibilità usata per generare i singoli certificati di identità del dispositivo.

Se invece si desidera eseguire il provisioning di un singolo dispositivo IoT Edge, seguire i passaggi nella sezione [precedente, Creare una registrazione individuale DPS.](#create-a-dps-individual-enrollment)

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-monitor.md).

### <a name="verify-your-root-certificate"></a>Verificare il certificato radice

Quando si crea un gruppo di registrazione, è possibile utilizzare un certificato verificato. È possibile verificare un certificato con DPS dimostrando di essere proprietari del certificato radice. Per ulteriori informazioni, vedere Come eseguire la [verifica del possesso dei certificati della CA X.509](../iot-dps/how-to-verify-certificates.md).

1. Nel [portale di Azure](https://portal.azure.com)passare all'istanza del servizio di provisioning dei dispositivi dell'hub IoT.In the Azure portal , navigate to your instance of IoT Hub Device Provisioning Service.

1. Selezionare **Certificati** dal menu a sinistra.

1. Selezionare **Aggiungi** per aggiungere un nuovo certificato.

1. Immettere un nome descrittivo per il certificato, quindi individuare il file con estensione cer o pem che rappresenta la parte pubblica del certificato X.509.

   Se si usano i certificati demo, caricare il `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` certificato.

1. Selezionare **Salva**.

1. Il certificato dovrebbe ora essere elencato nella pagina **Certificati.** Selezionarlo per aprire i dettagli del certificato.

1. Selezionare **Genera codice di verifica,** quindi copiare il codice generato.

1. Sia che si abbia portato un certificato CA personalizzato o si utilizzino i certificati demo, è possibile utilizzare lo strumento di verifica fornito nel repository IoT Edge per verificare la prova di possesso. Lo strumento di verifica utilizza il certificato CA per firmare un nuovo certificato con il codice di verifica fornito come nome del soggetto.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Nella stessa pagina dei dettagli del certificato nel portale di Azure caricare il certificato di verifica appena generato.

1. Selezionare **Verifica**.

### <a name="create-enrollment-group"></a>Creare un gruppo di registrazioneCreate enrollment group

Per ulteriori informazioni sulle registrazioni nel servizio Device Provisioning, vedere [Come gestire le registrazioni dei dispositivi](../iot-dps/how-to-manage-enrollments.md).

1. Nel [portale di Azure](https://portal.azure.com)passare all'istanza del servizio di provisioning dei dispositivi dell'hub IoT.In the Azure portal , navigate to your instance of IoT Hub Device Provisioning Service.

1. In **le impostazioni** selezionare **Gestisci registrazioni**.

1. Selezionare Aggiungi gruppo di **registrazione,** quindi completare i passaggi seguenti per configurare la registrazione:Select Add enrollment group then complete the following steps to configure the enrollment:

   * **Nome gruppo**: Specificare un nome memorabile per questa registrazione di gruppo.

   * **Tipo di attestazione**: Selezionare **Certificato**.

   * **Dispositivo IoT Edge**: selezionare **True**. Per una registrazione di gruppo, tutti i dispositivi devono essere dispositivi IoT Edge o nessuno di essi può essere.

   * **Tipo di certificato**: Selezionare **Certificato CA** se si dispone di un certificato CA verificato archiviato con DPS o **Certificato intermedio** se si desidera caricare un nuovo file solo per questa registrazione.

   * **Certificato primario:** se nell'ultima sezione è stato scelto Certificato CA, scegliere il certificato dall'elenco a discesa. Se si sceglie il certificato intermedio, caricare il file pubblico da un certificato CA nella catena di certificati di attendibilità utilizzata per generare i certificati di identità del dispositivo.

   * **Selezionare gli hub IoT a cui è possibile assegnare questo dispositivo:** scegliere l'hub IoT collegato a cui si vuole connettere il dispositivo. È possibile scegliere più hub e il dispositivo verrà assegnato a uno di essi in base ai criteri di allocazione selezionati.

   * **Stato iniziale del dispositivo gemello**: Aggiungere un valore di tag da aggiungere al dispositivo gemello se lo si desidera. È possibile utilizzare i tag per scegliere come destinazione gruppi di dispositivi per la distribuzione automatica. Ad esempio:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Selezionare **Salva**.

Ora che esiste una registrazione per questo dispositivo, il runtime di IoT Edge può eseguire automaticamente il provisioning del dispositivo durante l'installazione. Passare alla sezione successiva per configurare il dispositivo IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale.

Il provisioning X.509 con DPS è supportato solo in IoT Edge versione 1.0.9 o successiva.

Quando si esegue il provisioning del dispositivo, sono necessarie le informazioni seguenti:You'll need the following information when provisioning your device:

* Valore **dell'ambito ID** DPS. È possibile recuperare questo valore dalla pagina di panoramica dell'istanza DPS nel portale di Azure.You can retrieve this value from the overview page of your DPS instance in the Azure portal.
* File del certificato di identità del dispositivo nel dispositivo.
* File di chiave dell'identità del dispositivo nel dispositivo.
* Un ID di registrazione facoltativo (estratto dal nome comune nel certificato di identità del dispositivo se non fornito).

### <a name="linux-device"></a>Dispositivo Linux

Usare il collegamento seguente per installare il runtime di Azure IoT Edge nel dispositivo usando i comandi appropriati per l'architettura del dispositivo. Quando si arriva alla sezione sulla configurazione del daemon di sicurezza, configurare il runtime IoT Edge per il provisioning automatico, non manuale, non manuale. È necessario disporre di tutte le informazioni e i file di certificato necessari dopo aver completato le sezioni precedenti di questo articolo.

[Installare il runtime di Azure IoT Edge in LinuxInstall the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md)

Quando si aggiungono il certificato X.509 e le informazioni sulla chiave al file config.yaml, i percorsi devono essere forniti come URI di file. Ad esempio:

* `file:///<path>/identity_certificate.pem`
* `file:///<path>/identity_key.pem`

La sezione nel file di configurazione per il provisioning automatico X.509 è simile alla seguente:The section in the configuration file for X.509 automatic provisioning looks like this:

```yaml
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

Sostituire i valori `scope_id` `identity_cert`segnaposto `identity_pk` per , , con l'ID ambito dell'istanza DPS e gli URI nei percorsi dei file di chiave e del certificato nel dispositivo. Fornire `registration_id` un per il dispositivo, se lo si desidera, o lasciare questa riga commentata per registrare il dispositivo con il nome CN del certificato di identità.

Riavviare sempre il daemon di sicurezza dopo l'aggiornamento del file config.yaml.

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Dispositivo Windows

Installare il runtime Di IoT Edge nel dispositivo per il quale sono stati generati il certificato di identità e la chiave di identità. Il runtime Di IoT Edge verrà configurato per il provisioning automatico, non manuale.

Per informazioni più dettagliate sull'installazione di Edge In Windows, inclusi prerequisiti e istruzioni per attività quali la gestione dei contenitori e l'aggiornamento di IoT Edge, vedere Installare il runtime di [Azure IoT Edge in Windows.](how-to-install-iot-edge-windows.md)

1. Aprire una finestra di PowerShell in modalità amministratore. Assicurarsi di usare una sessione AMD64 di PowerShell durante l'installazione di IoT Edge, non powerShell (x86).

1. Il comando **Deploy-IoTEdge** controlla che il computer Windows sia in una versione supportata, attiva la funzionalità dei contenitori e quindi scarica il runtime moby e il runtime di IoT Edge. Il comando utilizza per impostazione predefinita i contenitori di Windows.The default command defaults to using Windows containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. A questo punto, i dispositivi IoT Core potrebbero riavviarsi automaticamente. Altri dispositivi Windows 10 o Windows Server potrebbero richiedere il riavvio. In tal caso, riavviare il dispositivo ora. Quando il dispositivo è pronto, eseguire di nuovo PowerShell come amministratore.

1. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. Il comando per impostazione predefinita `-Dps` è il provisioning manuale a meno che non si utilizzi il flag per utilizzare il provisioning automatico.

   Sostituire i valori `{scope_id}` `{identity cert path}`segnaposto `{identity key path}` per , e con i valori appropriati dell'istanza DPS e i percorsi dei file nel dispositivo. Se si desidera specificare l'ID di registrazione, includere `-RegistrationId {registration_id}` pure, sostituendo il segnaposto come appropriato.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Il file config.yaml archivia il certificato e le informazioni sulla chiave come URI di file. Tuttavia, il comando Initialize-IoTEdge gestisce automaticamente questo passaggio di formattazione, in modo da poter fornire il percorso assoluto del certificato e dei file di chiave nel dispositivo.

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se il runtime è stato avviato correttamente, è possibile passare all'hub IoT e iniziare la distribuzione di moduli IoT Edge nel dispositivo.

È possibile verificare che sia stata utilizzata la singola registrazione creata in Device Provisioning Service. Passare all'istanza del servizio Device Provisioning nel portale di Azure.Navigate to your Device Provisioning Service instance in the Azure portal. Aprire i dettagli della registrazione per la singola registrazione creata. Si noti che lo stato della registrazione è **assegnato** e l'ID dispositivo è elencato.

Usare i comandi seguenti sul dispositivo per verificare che il runtime sia stato installato e avviato correttamente.

### <a name="linux-device"></a>Dispositivo Linux

Verificare lo stato del servizio IoT Edge.

```cmd/sh
systemctl status iotedge
```

Esaminare i registri dei servizi.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Elencare i moduli in esecuzione.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Dispositivo Windows

Verificare lo stato del servizio IoT Edge.

```powershell
Get-Service iotedge
```

Esaminare i registri dei servizi.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Elencare i moduli in esecuzione.

```powershell
iotedge list
```

## <a name="next-steps"></a>Passaggi successivi

Il processo di registrazione del servizio Device Provisioning consente di impostare l'ID dispositivo e i tag del dispositivo gemello mentre si effettua il provisioning del nuovo dispositivo. È possibile usare questi valori per identificare come destinazione singoli dispositivi o gruppi di dispositivi usando la gestione automatica dei dispositivi. Informazioni su come distribuire e monitorare i [moduli di IoT Edge su larga scala usando il portale](how-to-deploy-monitor.md) di Azure o l'interfaccia della riga di comando di [Azure.](how-to-deploy-monitor-cli.md)
