---
title: Creare un dispositivo gateway trasparente - Azure IoT Edge | Microsoft Docs
description: Usare un dispositivo Azure IoT Edge per creare un gateway trasparente in grado di elaborare informazioni da dispositivi downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 722ee6197b467454818026c960e1ce0e5b39efb4
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766312"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurare un dispositivo IoT Edge come gateway trasparente

Questo articolo contiene istruzioni dettagliate per la configurazione di dispositivi IoT Edge in grado di operare come gateway trasparente per consentire la comunicazione di altri dispositivi con l'hub IoT. In questo articolo il termine *gateway IoT Edge* si riferisce a un dispositivo IoT Edge usato come gateway trasparente. Per altre informazioni, vedere [dispositivo la modalità di IoT Edge può essere usato come gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>Al momento:
> * Se il gateway è disconnesso dall'hub IoT, i dispositivi downstream non possono eseguire l'autenticazione tramite gateway.
> * I dispositivi abilitati per Edge non possono connettersi ai gateway IoT Edge. 
> * I dispositivi downstream non possono usare il caricamento dei file.

Perché possa operare come gateway, un dispositivo deve essere in grado di connettersi in modo sicuro a dispositivi downstream. Azure IoT Edge permette di usare un'infrastruttura a chiave pubblica (PKI) per configurare connessioni sicure tra dispositivi. Nel caso illustrato si consente la connessione di un dispositivo downstream a un dispositivo IoT Edge che funge da gateway trasparente. Per mantenere la protezione, il dispositivo downstream deve verificare l'identità del dispositivo IoT Edge. Si vuole che i dispositivi che si connettono a solo i gateway, non tutti i gateway potenzialmente dannosi.

Un dispositivo downstream può essere qualsiasi applicazione o piattaforma la cui identità sia stata creata con il servizio cloud [hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub). In molti casi, queste applicazioni usano [Azure IoT SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md). Per scopi pratici, un dispositivo downstream può anche essere un'applicazione in esecuzione nel dispositivo gateway IoT Edge stesso. 

È possibile creare qualsiasi infrastruttura di certificati che abilita la relazione di trust necessaria per la topologia dispositivo-gateway. In questo articolo si presuppone la stessa configurazione di certificato che utilizzerebbe per abilitare [sicurezza CA X.509](../iot-hub/iot-hub-x509ca-overview.md) nell'IoT Hub, che implica un certificato della CA X.509 associato a un hub IoT specifico (la proprietaria dell'hub IoT CA) e una serie di certificati, firma con questa autorità di certificazione e un'autorità di certificazione per il dispositivo IoT Edge.

![Installazione del certificato del gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

Il gateway presenta relativo IoT Edge certificato della CA del dispositivo nel dispositivo downstream durante l'avvio della connessione. Il dispositivo downstream verifica per assicurarsi che il certificato di autorità di certificazione del dispositivo IoT Edge viene firmato dal certificato dell'autorità di certificazione proprietario. Questo processo consente al dispositivo downstream di verificare che il gateway provenga da una fonte attendibile.

La procedura seguente illustra il processo di creazione e installazione dei certificati nelle posizioni corrette.

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge da configurare come gateway. Usare la procedura di installazione di IoT Edge per i sistemi operativi seguenti:
* [Windows](./how-to-install-iot-edge-windows.md)
* [Linux x64](./how-to-install-iot-edge-linux.md)
* [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

È possibile usare qualsiasi computer per generare i certificati e quindi copiarli nel dispositivo IoT Edge.

>[!NOTE]
>Il "nome del gateway" utilizzato per creare i certificati in questa istruzione, deve essere lo stesso nome usato come nome host nel file config. yaml di IoT Edge e come GatewayHostName nella stringa di connessione del dispositivo downstream. Deve essere risolvibile in un indirizzo IP, DNS o una voce del file host utilizzando il nome del gateway"". Comunicazione basata sul protocollo usato (MQTTS:8883 / AMQPS:5671 / HTTPS:433) deve essere possibile tra dispositivi downstream e trasparente IoT Edge. Se un firewall è compreso tra, la rispettiva porta deve essere aperta.

## <a name="generate-certificates-with-windows"></a>Generare certificati con Windows

Usare i passaggi descritti in questa sezione per generare certificati di test in un dispositivo Windows. È possibile usare questi passaggi per generare i certificati in un dispositivo Windows IoT Edge. In alternativa, è possibile generare i certificati nel computer di sviluppo Windows e quindi copiarli in qualsiasi dispositivo IoT Edge. 

I certificati generati in questa sezione devono essere usati solo a scopo di test. 

### <a name="install-openssl"></a>Installare OpenSSL

Installare OpenSSL per Windows nel computer usato per generare i certificati. È possibile installare OpenSSL in diversi modi:

   >[!NOTE]
   >Se OpenSSL è già installato nel dispositivo Windows, è possibile ignorare questo passaggio, ma assicurarsi che openssl.exe sia disponibile nella variabile di ambiente PATH.

* **Più semplice:** Scaricare e installare qualsiasi [file binario OpenSSL di terze parti](https://wiki.openssl.org/index.php/Binaries), ad esempio da [questo progetto in SourceForge](https://sourceforge.net/projects/openssl/). Aggiungere il percorso completo di openssl.exe alla variabile di ambiente PATH. 
   
* **Consigliato:** Scaricare il codice sorgente OpenSSL e generare i file binari nel computer in uso in maniera autonoma o tramite [vcpkg](https://github.com/Microsoft/vcpkg). Nelle istruzioni elencate di seguito viene usato vcpkg per scaricare il codice sorgente, compilare e installare OpenSSL nel computer Windows con passaggi molto semplici.

   1. Passare alla directory in cui installare vcpkg, Questa directory è denominata *\<VCPKGDIR>*. Seguire le istruzioni per scaricare e installare [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Dopo aver installato vcpkg, da un prompt di PowerShell, eseguire il comando seguente per installare il pacchetto OpenSSL per Windows x64. Per completare l'installazione sono in genere necessari circa cinque minuti.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Aggiungere `<VCPKGDIR>\installed\x64-windows\tools\openssl` alla variabile di ambiente PATH in modo da rendere il file openssl.exe disponibile per la chiamata.

### <a name="prepare-creation-scripts"></a>Preparare gli script di creazione

Azure IoT SDK per dispositivi per C contiene script che è possibile usare per generare i certificati di test. In questa sezione si clonerà l'SDK e si configurerà PowerShell.

1. Aprire una finestra di PowerShell in modalità amministratore. 

2. Clonare il repository git che contiene gli script per generare certificati non di produzione. Questi script consentono di creare i certificati necessari per configurare un gateway trasparente. Usare il comando `git clone` o [scaricare il file ZIP](https://github.com/Azure/azure-iot-sdk-c/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Passare alla directory che si vuole usare, Questa directory è denominata *\<WRKDIR>*.  Tutti i file verranno creati in questa directory.

4. Copiare i file script e di configurazione nella directory di lavoro. 

   ```powershell
   copy <path>\azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy <path>\azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

   Se l'SDK è stato scaricato come file ZIP, il nome della cartella è `azure-iot-sdk-c-master` e il resto del percorso è identico. 

5. Impostare la variabile di ambiente OPENSSL_CONF in modo da usare il file di configurazione openssl_root_ca.cnf.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```

6. Abilitare PowerShell per l'esecuzione degli script.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Includere le funzioni usate dagli script nello spazio dei nomi globale di PowerShell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

8. Verificare che OpenSSL sia stato installato correttamente e assicurarsi che non vi siano conflitti di nomi con i certificati esistenti. In caso di problemi, lo script dovrebbe descrivere come correggerli nel sistema in uso.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Creare i certificati

In questa sezione verranno creati tre certificati, che verranno quindi connessi in una catena. L'inserimento dei certificati in una catena permette di installarli facilmente nel dispositivo gateway IoT Edge e in tutti i dispositivi downstream.  

1. Creare il certificato della CA proprietaria e fare in modo che firmi un certificato intermedio. I certificati vengono tutti inseriti in *\<WRKDIR>*.

      ```powershell
      New-CACertsCertChain rsa
      ```

2. Creare chiave privata e certificato CA dispositivo IoT Edge con il comando seguente. Specificare un nome per il dispositivo gateway, che verrà usato per denominare i file e durante la generazione del certificato. 

   ```powershell
   New-CACertsEdgeDevice "<gateway name>"
   ```

3. Creare una catena di certificati dal certificato della CA di proprietario, certificati intermedi e certificato della CA del dispositivo IoT Edge con il comando seguente. 

   ```powershell
   Write-CACertsCertificatesForEdgeDevice "<gateway name>"
   ```

   Lo script crea la chiave e i certificati seguenti:
   * `<WRKDIR>\certs\new-edge-device.*`
   * `<WRKDIR>\private\new-edge-device.key.pem`
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="generate-certificates-with-linux"></a>Generare certificati con Linux

Usare i passaggi descritti in questa sezione per generare certificati di test in un dispositivo Linux. È possibile generare i certificati nel dispositivo IoT Edge stesso oppure usare un computer separato e copiare i certificati finali in qualsiasi dispositivo IoT Edge che esegue tutti i sistemi operativi supportati. 

### <a name="prepare-creation-scripts"></a>Preparare gli script di creazione

1. Clonare il repository git che contiene gli script per generare certificati non di produzione. Questi script consentono di creare i certificati necessari per configurare un gateway trasparente. 

   ```bash
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

2. Passare alla directory che si vuole usare, Questa directory è denominata *\<WRKDIR>*.  Tutti i file verranno creati in questa directory.
  
3. Copiare i file di configurazione e script nella directory di lavoro.

   ```bash
   cp <path>/azure-iot-sdk-c/tools/CACertificates/*.cnf .
   cp <path>/azure-iot-sdk-c/tools/CACertificates/certGen.sh .
   ```

4. Configurare OpenSSL per generare i certificati usando lo script fornito. 

   ```bash
   chmod 700 certGen.sh 
   ```

### <a name="create-certificates"></a>Creare i certificati

In questa sezione verranno creati tre certificati, che verranno quindi connessi in una catena. L'inserimento dei certificati in un file di catena permette di installarli facilmente nel dispositivo gateway IoT Edge e in tutti i dispositivi downstream.  

1. Creare il certificato della CA proprietaria e un certificato intermedio. Questi certificati vengono inseriti in *\<WRKDIR*.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Lo script crea i certificati e le chiavi seguenti:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>/certs/azure-iot-test-only.intermediate.cert.pem`
   * `<WRKDIR>/private/azure-iot-test-only.root.ca.key.pem`
   * `<WRKDIR>/private/azure-iot-test-only.intermediate.key.pem`

2. Creare chiave privata e certificato CA dispositivo IoT Edge con il comando seguente. Specificare un nome per il dispositivo gateway, che verrà usato per denominare i file e durante la generazione del certificato. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway name>"
   ```

   Lo script crea la chiave e i certificati seguenti:
   * `<WRKDIR>/certs/new-edge-device.*`
   * `<WRKDIR>/private/new-edge-device.key.pem`

3. Creare una catena di certificati chiamata **nuovo-edge-device-full-chain.cert.pem** dal certificato CA proprietaria, certificati intermedi e certificato di autorità di certificazione del dispositivo IoT Edge.

   ```bash
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="install-certificates-on-the-gateway"></a>Installare i certificati nel gateway

Dopo aver creato una catena di certificati, è necessario installarla nel dispositivo gateway IoT Edge e configurare il runtime IoT Edge perché faccia riferimento ai nuovi certificati. 

1. Copiare i file seguenti da *\<WRKDIR>*. Salvarli in una posizione qualsiasi nel dispositivo IoT Edge. La directory di destinazione nel dispositivo IoT Edge è denominata *\<CERTDIR>*. 

   Se è stato generato i certificati nel dispositivo IoT Edge stesso, è possibile ignorare questo passaggio e usare il percorso alla directory di lavoro.

   * Certificato della CA del dispositivo: `<WRKDIR>\certs\new-edge-device-full-chain.cert.pem`
   * Chiave privata della CA del dispositivo: `<WRKDIR>\private\new-edge-device.key.pem`
   * CA proprietaria: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Aprire il file di configurazione del daemon di sicurezza di IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Impostare le proprietà **certificate** nel file config.yaml sul percorso in cui sono stati salvati i file di certificato e di chiave nel dispositivo IoT Edge.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\new-edge-device-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\new-edge-device.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/new-edge-device-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/new-edge-device.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Nei dispositivi Linux, verificare che l'utente **iotedge** dispone di autorizzazioni di lettura per la directory che contiene i certificati. 

## <a name="deploy-edgehub-to-the-gateway"></a>Distribuire Hub Edge nel gateway

Quando si installa prima di tutto di IoT Edge in un dispositivo, il modulo di un solo sistema viene avviato automaticamente: l'agente di IoT Edge. Perché il dispositivo possa operare come gateway, sono necessari entrambi i moduli di sistema. Se non sono stati distribuiti tutti i moduli nel dispositivo gateway prima di creare una distribuzione per il dispositivo avviare il secondo modulo di sistema, l'hub di IoT Edge. La distribuzione apparirà come vuota perché non è stato aggiunto alcun modulo durante la procedura guidata, ma distribuirà entrambi i moduli di sistema. 

È possibile controllare quali moduli sono in esecuzione in un dispositivo con il comando `iotedge list`.

1. Nel portale di Azure passare all'hub IoT.

2. Passare a **IoT Edge** e selezionare il dispositivo IoT Edge da usare come gateway.

3. Selezionare **Set Modules** (Configura i moduli).

4. Selezionare **Avanti**.

5. Nel passaggio **Specifica route** deve essere indicata una route predefinita che invia tutti i messaggi da tutti i moduli all'hub IoT. In caso contrario, aggiungere il codice seguente e quindi selezionare **Avanti**.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. Nel passaggio **Rivedi modello** selezionare **Invia**.

## <a name="open-ports-on-gateway-device"></a>Porte aperte sul dispositivo gateway

I dispositivi IoT Edge standard non necessitano disponibile la connettività in ingresso alla funzione, perché tutte le comunicazioni con l'IoT Hub viene eseguita tramite le connessioni in uscita. Tuttavia, i dispositivi gateway sono diversi perché devono essere in grado di ricevere messaggi dai dispositivi downstream.

Per uno scenario di gateway a funzionare, almeno uno dei protocolli supportati dell'hub IoT Edge deve essere aperto per il traffico in ingresso dai dispositivi downstream. I protocolli supportati sono MQTT, AMQP e HTTPS.

| Porta | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Instradare i messaggi da dispositivi downstream
Il runtime IoT Edge può instradare i messaggi inviati dai dispositivi downstream come i messaggi inviati dai moduli. Questa funzionalità consente di eseguire analitica in un modulo in esecuzione nel gateway prima di inviare dati al cloud. 

Attualmente, il modo per instradare i messaggi inviati dai dispositivi downstream consiste nel differenziarli dai messaggi inviati dai moduli. I messaggi inviati da tutti i moduli contengono una proprietà di sistema denominata **connectionModuleId** ma non i messaggi inviati dai dispositivi downstream. È possibile utilizzare la clausola WHERE della route da escludere eventuali messaggi che contengono tale proprietà di sistema. 

La route seguente viene usata per inviare messaggi da un dispositivo downstream a un modulo denominato `ai_insights`.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Per altre informazioni sul routing dei messaggi, vedere [distribuire moduli e stabilire le route](./module-composition.md#declare-routes).

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un dispositivo IoT Edge che opera come gateway trasparente, è necessario configurare i dispositivi downstream in modo da ritenere attendibile il gateway e inviarvi messaggi. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).
