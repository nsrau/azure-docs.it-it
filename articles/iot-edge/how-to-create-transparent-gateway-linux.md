---
title: Creare un gateway trasparente con Azure IoT Edge - Linux | Microsoft Docs
description: Usare Azure IoT Edge per creare un gateway trasparente capace di elaborare informazioni per più dispositivi
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2b4e2a19b5d5f6491ff3db24489b361040a52280
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035575"
---
# <a name="create-a-linux-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Creare un dispositivo IoT Edge Linux come gateway trasparente

Questo articolo contiene istruzioni dettagliate per usare un dispositivo IoT Edge come gateway trasparente. Nella parte restante di questo articolo il termine *gateway IoT Edge* si riferisce a un dispositivo IoT Edge usato come gateway trasparente. Per altri dettagli, vedere [Come usare un dispositivo Azure IoT Edge come gateway - anteprima][lnk-edge-as-gateway], che offre una panoramica concettuale.

>[!NOTE]
>Al momento:
> * Se il gateway è disconnesso dall'hub IoT, i dispositivi downstream non possono eseguire l'autenticazione tramite gateway.
> * I dispositivi abilitati per Edge non possono connettersi ai gateway IoT Edge. 
> * I dispositivi downstream non possono usare il caricamento dei file.

La principale difficoltà correlata alla creazione di un gateway trasparente è costituita dalla necessità di connettere il gateway in modo sicuro ai dispositivi downstream. Azure IoT Edge consente di usare l'infrastruttura PKI per impostare connessioni TLS sicure tra questi dispositivi. Nel caso illustrato si consente la connessione di un dispositivo downstream a un dispositivo IoT Edge che funge da gateway trasparente.  Per garantire un ragionevole livello di sicurezza, il dispositivo downstream deve verificare l'identità del dispositivo Edge. L'obiettivo, infatti, è connettere i dispositivi ai gateway, non avere a disposizione un gateway potenzialmente dannoso.

È possibile creare qualsiasi infrastruttura di certificati che abilita la relazione di trust necessaria per la topologia dispositivo-gateway. Questo articolo presuppone la stessa configurazione di certificati usata per abilitare la [sicurezza CA X.509][lnk-iothub-x509] nell'hub IoT, che include un certificato della CA X.509 associato a un hub IoT specifico (la CA proprietaria dell'hub IoT), una serie di certificati firmati da tale CA e una CA per il dispositivo Edge.

![Configurazione del gateway][1]

Il gateway presenta il certificato della CA del dispositivo Edge al dispositivo downstream durante l'avvio della connessione. Il dispositivo downstream verifica che il certificato della CA del dispositivo Edge sia firmato dal certificato della CA proprietaria. Questo processo consente al dispositivo downstream di verificare che il gateway provenga da una fonte attendibile.

La procedura seguente illustra il processo di creazione e installazione dei certificati nelle posizioni corrette.

## <a name="prerequisites"></a>Prerequisiti
1.  Installare il runtime Azure IoT Edge in un dispositivo Linux che si vuole usare come gateway trasparente.
   * [Linux x64][lnk-install-linux-x64]
   * [Linux ARM32][lnk-install-linux-arm]

2.  Ottenere gli script per generare i certificati non di produzione richiesti con il comando seguente. Questi script consentono di creare i certificati necessari per configurare un gateway trasparente. 

   ```cmd
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Per generare i certificati necessari questi script usano OpenSSL, che richiede alcune operazioni di configurazione.
   
   1. Passare alla directory che si vuole usare, che da ora in poi sarà denominata $WRKDIR.  Tutti i file verranno creati in questa directory.

      cd $WRKDIR
   
   1. Copiare i file di configurazione e script nella directory di lavoro.

      ```cmd
      cp azure-iot-sdk-c/tools/CACertificates/*.cnf .
      cp azure-iot-sdk-c/tools/CACertificates/certGen.sh .
      chmod 700 certGen.sh 
      ```

## <a name="certificate-creation"></a>Creazione di certificati
1.  Creare il certificato della CA proprietaria e un certificato intermedio. Questi certificati vengono tutti posizionati in `$WRKDIR`.

   ```cmd
   ./certGen.sh create_root_and_intermediate
   ```

   L'esecuzione degli script genera i certificati e le chiavi seguenti:
   * Certificati
      * `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`
      * `$WRKDIR/certs/azure-iot-test-only.intermediate.cert.pem`
   * Chiavi
      * `$WRKDIR/private/azure-iot-test-only.root.ca.key.pem`
      * `$WRKDIR/private/azure-iot-test-only.intermediate.key.pem`

2.  Creare la chiave privata e il certificato della CA del dispositivo Edge con il comando seguente.

   >[!NOTE]
   > **NON** usare un nome corrispondente al nome host DNS del gateway, altrimenti la certificazione client a fronte di questi certificati avrà esito negativo.

      ```cmd
      ./certGen.sh create_edge_device_certificate "<gateway device name>"
      ```

   L'esecuzione degli script genera la chiave e i certificati seguenti:
   * `$WRKDIR/certs/new-edge-device.*`
   * `$WRKDIR/private/new-edge-device.key.pem`

## <a name="certificate-chain-creation"></a>Creazione di catene di certificati
Con il comando seguente è possibile creare una catena di certificati partendo dal certificato della CA proprietaria, dal certificato intermedio e dal certificato della CA del dispositivo Edge. L'inserimento in un file apposito consente di installare la catena con facilità nel dispositivo Edge che funge da gateway trasparente.

   ```cmd
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="installation-on-the-gateway"></a>Installazione nel gateway
1.  Copiare i file seguenti da $WRKDIR in una posizione qualsiasi del dispositivo Edge, che verrà denominata $CERTDIR. Ignorare questo passaggio se i certificati sono stati generati nel dispositivo Edge.

   * Certificato della CA del dispositivo: `$WRKDIR/certs/new-edge-device-full-chain.cert.pem`
   * Chiave privata della CA del dispositivo: `$WRKDIR/private/new-edge-device.key.pem`
   * CA proprietaria: `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`

2.  Impostare le proprietà `certificate` del file YAML di configurazione del daemon di sicurezza sul percorso in cui sono stati inseriti i file di chiave e di certificato.

```yaml
certificates:
  device_ca_cert: "$CERTDIR/certs/new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR/private/new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem"
```

## <a name="deploy-edgehub-to-the-gateway"></a>Distribuire Hub Edge nel gateway
Una delle principali funzionalità di Azure IoT Edge è la possibilità di distribuire i moduli nei dispositivi IoT Edge dal cloud. Questa sezione consente di creare una distribuzione apparentemente vuota. Hub Edge, tuttavia, viene automaticamente aggiunto a tutte le distribuzioni anche se non sono presenti altri moduli. Hub Edge è l'unico modulo necessario per consentire a un dispositivo Edge di svolgere la funzione di gateway trasparente. È quindi sufficiente creare una distribuzione vuota. 
1. Nel portale di Azure passare all'hub IoT.
2. Passare a **IoT Edge** e selezionare il dispositivo IoT Edge da usare come gateway.
3. Selezionare **Set Modules** (Configura i moduli).
4. Selezionare **Avanti**.
5. Nel passaggio **Specifica route** deve essere presente una route predefinita che invia tutti i messaggi di tutti i moduli all'hub IoT. In caso contrario, aggiungere il codice seguente e quindi selezionare **Avanti**.
   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```
6. Nel passaggio Rivedi modello selezionare **Invia**.

## <a name="installation-on-the-downstream-device"></a>Installazione nel dispositivo downstream
Qualsiasi applicazione che usa [Azure IoT SDK per dispositivi][lnk-devicesdk], come quella descritta in [Connettere il dispositivo all'hub IoT usando .NET] [ lnk-iothub-getstarted], può costituire un dispositivo downstream. Per convalidare le connessioni TLS ai dispositivi gateway, un dispositivo downstream deve considerare attendibile il certificato della **CA proprietaria**. Due le modalità per ottenere ciò: a livello di sistema operativo o, per alcuni linguaggi, a livello di applicazione.

### <a name="os-level"></a>Livello sistema operativo
L'installazione di questo certificato nell'archivio certificati del sistema operativo consentirà a tutte le applicazioni di usare il certificato della CA proprietaria come certificato attendibile.

* Ubuntu: l'esempio seguente illustra come installare un certificato della CA in un host Ubuntu.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Verrà visualizzato un messaggio per confermare l'avvenuto aggiornamento dei certificati in /etc/ssl/certs (1 aggiunto, 0 rimossi).

* Windows: l'esempio seguente illustra come installare un certificato della CA in un host Windows.
  * Nel menu Start digitare Gestisci i certificati computer. Verrà visualizzata un'utilità denominata `certlm`.
  * Passare a Certificati - Computer locale --> Autorità di certificazione radice attendibile --> Certificati --> fare clic con il pulsante destro del mouse --> Tutte le attività --> Importa per avviare la procedura guidata per l'importazione del certificato.
  * Seguire i passaggi come indicato e importare il file di certificato $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem.
  * Al termine, verrà visualizzato il messaggio "Importazione completata".

### <a name="application-level"></a>Livello applicazione
Per le applicazioni .NET è possibile aggiungere il frammento di codice seguente per poter considerare attendibile un certificato in formato PEM. Inizializzare la variabile `certPath` con `$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Connettere il dispositivo downstream al gateway
È necessario inizializzare l'SDK del dispositivo hub IoT con una stringa di connessione che faccia riferimento al nome host del dispositivo gateway. A tal fine, aggiungere la proprietà `GatewayHostName` alla stringa di connessione del dispositivo. Ad esempio, di seguito è riportato un esempio di stringa di connessione del dispositivo, alla quale è stata aggiunta la proprietà `GatewayHostName`:

   ```
   HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com
   ```

   >[!NOTE]
   >Di seguito è riportato un comando di esempio che consente di verificare che tutto sia stato configurato correttamente. Verrà visualizzato un messaggio di conferma della verifica.
   >
   >openssl s_client -connect mygateway.contoso.com:8883 -CAfile $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem -showcerts

## <a name="routing-messages-from-downstream-devices"></a>Routing dei messaggi da dispositivi downstream
Il runtime IoT Edge può instradare i messaggi inviati dai dispositivi downstream come i messaggi inviati dai moduli. In questo modo è possibile eseguire analisi in un modulo in esecuzione nel gateway prima di inviare dati al cloud. La route seguente viene usata per inviare messaggi da un dispositivo downstream denominato `sensor` a un modulo denominato `ai_insights`.

   ```json
   { "routes":{ "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" } }
   ```

Per altri dettagli sul routing dei messaggi, vedere l'[articolo sulla composizione dei moduli][lnk-module-composition].

## <a name="next-steps"></a>Passaggi successivi
[Understand the requirements and tools for developing IoT Edge modules][lnk-module-dev] (Informazioni sui requisiti e sugli strumenti per sviluppare moduli IoT Edge).

<!-- Images -->
[1]: ./media/how-to-create-transparent-gateway/gateway-setup.png

<!-- Links -->
[lnk-install-linux-x64]: ./how-to-install-iot-edge-linux.md
[lnk-install-linux-arm]: ./how-to-install-iot-edge-linux-arm.md
[lnk-module-composition]: ./module-composition.md
[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
