---
title: Creare un dispositivo gateway trasparente - Azure IoT Edge | Microsoft Docs
description: Usare un dispositivo Azure IoT Edge per creare un gateway trasparente in grado di elaborare informazioni da dispositivi downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5881adb7e2fc0d52cc2037d3d4a9e986b3e29d74
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058367"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurare un dispositivo IoT Edge come gateway trasparente

Questo articolo fornisce istruzioni dettagliate per la configurazione di un dispositivo IoT Edge per funzionare come gateway trasparente per gli altri dispositivi di comunicare con l'IoT Hub. In questo articolo il termine *gateway IoT Edge* si riferisce a un dispositivo IoT Edge usato come gateway trasparente. Per altre informazioni, vedere [dispositivo la modalità di IoT Edge può essere usato come gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>Al momento:
> * I dispositivi abilitati per Edge non possono connettersi ai gateway IoT Edge. 
> * I dispositivi downstream non possono usare il caricamento dei file.

Esistono tre passaggi generali per configurare una connessione gateway trasparente ha esito positivo. Questo articolo illustra il primo passaggio:

1. **Il dispositivo gateway deve essere in grado di connettersi ai dispositivi downstream in modo sicuro, ricevere le comunicazioni dai dispositivi downstream e indirizzare i messaggi alla destinazione appropriata.**
2. Il dispositivo downstream deve avere un'identità del dispositivo sia in grado di eseguire l'autenticazione con l'IoT Hub e in grado di comunicare tramite il relativo dispositivo gateway. Per altre informazioni, vedere [autenticare un dispositivo all'IoT Hub di Azure a valle](how-to-authenticate-downstream-device.md).
3. Il dispositivo downstream deve essere in grado di connettere in modo sicuro il dispositivo gateway. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).


Per un dispositivo a funzionare come gateway, è necessario essere in grado di connettere in modo sicuro i dispositivi di downstream. Azure IoT Edge permette di usare un'infrastruttura a chiave pubblica (PKI) per configurare connessioni sicure tra dispositivi. Nel caso illustrato si consente la connessione di un dispositivo downstream a un dispositivo IoT Edge che funge da gateway trasparente. Per mantenere la protezione, il dispositivo downstream deve verificare l'identità del dispositivo gateway. Questo controllo di identità impedisce i dispositivi di connettersi ai gateway potenzialmente dannosi.

Un dispositivo downstream può essere qualsiasi applicazione o piattaforma la cui identità sia stata creata con il servizio cloud [hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub). In molti casi, queste applicazioni usano [Azure IoT SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md). Per scopi pratici, un dispositivo downstream può anche essere un'applicazione in esecuzione nel dispositivo gateway IoT Edge stesso. 

È possibile creare qualsiasi infrastruttura di certificati che abilita la relazione di trust necessaria per la topologia dispositivo-gateway. In questo articolo si presuppone la stessa configurazione di certificato che utilizzerebbe per abilitare [sicurezza CA X.509](../iot-hub/iot-hub-x509ca-overview.md) nell'IoT Hub, che implica un certificato della CA X.509 associato a un hub IoT specifico (IoT hub radice della CA), una serie di certificati firmati con questa autorità di certificazione e un'autorità di certificazione per il dispositivo IoT Edge.

![Installazione del certificato del gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Il termine "root CA" usato in questo articolo si riferisce il certificato pubblico dell'autorità in primo piano della catena di certificati PKI e non necessariamente la radice del certificato di un'autorità di certificazione diffuso tramite RSS. In molti casi, è effettivamente un certificato della CA intermedia pubblico. 

Il gateway presenta relativo IoT Edge certificato della CA del dispositivo nel dispositivo downstream durante l'avvio della connessione. Il dispositivo downstream verifica per assicurarsi che il certificato di autorità di certificazione del dispositivo IoT Edge viene firmato dal certificato dell'autorità di certificazione radice. Questo processo consente al dispositivo downstream confermare che il gateway proviene da una fonte attendibile.

I passaggi seguenti consentono di eseguire il processo di creazione dei certificati e installarli nelle posizioni appropriate nel gateway. È possibile usare qualsiasi computer per generare i certificati e quindi copiarli nel dispositivo IoT Edge. 

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge da configurare come gateway. Usare la procedura di installazione di IoT Edge per uno dei sistemi operativi seguenti:
  * [Windows](./how-to-install-iot-edge-windows.md)
  * [Linux x64](./how-to-install-iot-edge-linux.md)
  * [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

Questo articolo si riferisce al *hostname gateway* in momenti diversi. Il nome host di gateway è dichiarato nel **hostname** parametro del file config. yaml nel dispositivo gateway IoT Edge. È possibile creare i certificati in questo articolo e fa riferimento nella stringa di connessione dei dispositivi downstream. Il nome host di gateway deve essere risolvibile in un indirizzo IP, utilizzando DNS o una voce del file host.

## <a name="generate-certificates-with-windows"></a>Generare certificati con Windows

Usare i passaggi descritti in questa sezione per generare i certificati di test in Windows. È possibile usare un computer Windows per generare i certificati e quindi copiarli su qualsiasi dispositivo IoT Edge in esecuzione in qualsiasi sistema operativo supportato. 

I certificati generati in questa sezione devono essere usati solo a scopo di test. 

### <a name="install-openssl"></a>Installare OpenSSL

Installare OpenSSL per Windows nel computer usato per generare i certificati. Se si dispone già di OpenSSL installata sul dispositivo Windows, è possibile ignorare questo passaggio, ma assicurarsi che tale openssl.exe è disponibile nella variabile di ambiente PATH. 

È possibile installare OpenSSL in diversi modi:

* **Più semplice:** Scaricare e installare qualsiasi [i file binari OpenSSL di terze parti](https://wiki.openssl.org/index.php/Binaries), ad esempio, da [OpenSSL in SourceForge](https://sourceforge.net/projects/openssl/). Aggiungere il percorso completo di openssl.exe alla variabile di ambiente PATH. 
   
* **Consigliato:** Scaricare il codice sorgente OpenSSL e generare i file binari nel computer in uso in maniera autonoma o tramite [vcpkg](https://github.com/Microsoft/vcpkg). Nelle istruzioni elencate di seguito viene usato vcpkg per scaricare il codice sorgente, compilare e installare OpenSSL nel computer Windows con passaggi molto semplici.

   1. Passare alla directory in cui installare vcpkg, Questa directory è denominata *\<VCPKGDIR>* . Seguire le istruzioni per scaricare e installare [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Una volta vcpkg è installato, eseguire il comando seguente da un prompt di powershell per installare il pacchetto OpenSSL per Windows x64. Per completare l'installazione sono in genere necessari circa cinque minuti.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Aggiungere `<VCPKGDIR>\installed\x64-windows\tools\openssl` alla variabile di ambiente PATH in modo da rendere il file openssl.exe disponibile per la chiamata.

### <a name="prepare-creation-scripts"></a>Preparare gli script di creazione

Il repository git di Azure IoT Edge contiene script che è possibile usare per generare i certificati di test. In questa sezione si clona il repository di IoT Edge ed eseguire gli script. 

1. Aprire una finestra di PowerShell in modalità amministratore. 

2. Clonare il repository git che contiene gli script per generare certificati non di produzione. Questi script consentono di creare i certificati necessari per configurare un gateway trasparente. Usare il comando `git clone` o [scaricare il file ZIP](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Passare alla directory che si vuole usare, In questo articolo, lo chiamiamo questa directory  *\<WRKDIR >* . Tutti i certificati e chiavi verranno create nella directory di lavoro.

4. Copiare i file di configurazione e lo script dal repository clonato nella directory di lavoro. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Se è stato scaricato il repository come file ZIP, quindi il nome della cartella è `iotedge-master` e il resto del percorso è lo stesso. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Abilitare PowerShell per l'esecuzione degli script.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Portare le funzioni utilizzate dagli script nello spazio dei nomi globale di PowerShell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   Nella finestra di PowerShell verrà visualizzato un avviso che i certificati generati da questo script sono solo a scopo di test e non devono essere utilizzati negli scenari di produzione.

8. Verificare che sia stato installato correttamente OpenSSL e assicurarsi che non saranno conflitti di nomi con i certificati esistenti. In caso di problemi, lo script dovrebbe descrivere come correggerli nel sistema in uso.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Creare i certificati

In questa sezione verranno creati tre certificati, che verranno quindi connessi in una catena. L'inserimento dei certificati in una catena permette di installarli facilmente nel dispositivo gateway IoT Edge e in tutti i dispositivi downstream.  

1. Creare il certificato CA radice e averlo di firmare un certificato intermedio. Tutti i certificati vengono inseriti nella directory di lavoro.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Questo comando per script crea alcuni certificati e i file di chiave, ma dobbiamo fare riferimento a uno in particolare, più avanti in questo articolo:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Creare chiave privata e certificato CA dispositivo IoT Edge con il comando seguente. Specificare il nome host di gateway, che può essere trovato nel file iotedge\config.yaml nel dispositivo gateway. Il nome host di gateway viene usato per denominare i file e durante la generazione del certificato. 

   ```powershell
   New-CACertsEdgeDevice "<gateway hostname>"
   ```

   Questo comando per script crea alcuni certificati e file di chiave, inclusi due che andremo a fare riferimento a più avanti in questo articolo:
   * `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`

Dopo aver creato i certificati, andare direttamente al [installare i certificati nel gateway](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Generare certificati con Linux

Usare i passaggi in questa sezione per generare i certificati di prova in Linux. È possibile usare un computer Linux per generare i certificati e quindi copiarli su qualsiasi dispositivo IoT Edge in esecuzione in qualsiasi sistema operativo supportato. 

I certificati generati in questa sezione devono essere usati solo a scopo di test. 

### <a name="prepare-creation-scripts"></a>Preparare gli script di creazione

Il repository git di Azure IoT Edge contiene script che è possibile usare per generare i certificati di test. In questa sezione si clona il repository di IoT Edge ed eseguire gli script. 

1. Clonare il repository git che contiene gli script per generare certificati non di produzione. Questi script consentono di creare i certificati necessari per configurare un gateway trasparente. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Passare alla directory che si vuole usare, Si farà riferimento a questa directory in tutto l'articolo come  *\<WRKDIR >* . Tutti i file di certificato e la chiave verranno creati in questa directory.
  
3. Copiare i file di configurazione e lo script dal repository di IoT Edge clonato nella directory di lavoro.

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

### <a name="create-certificates"></a>Creare i certificati

In questa sezione verranno creati tre certificati, che verranno quindi connessi in una catena. L'inserimento dei certificati in un file di catena permette di installarli facilmente nel dispositivo gateway IoT Edge e in tutti i dispositivi downstream.  

1. Creare il certificato CA radice e un certificato intermedio. Questi certificati vengono inseriti in *\<WRKDIR*.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Lo script crea alcuni certificati e chiavi. Prendere nota di uno, quindi si farà riferimento nella sezione successiva:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Creare chiave privata e certificato CA dispositivo IoT Edge con il comando seguente. Specificare il nome host di gateway, che può essere trovato nel file iotedge/config.yaml nel dispositivo gateway. Il nome host di gateway viene usato per denominare i file e durante la generazione del certificato. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway hostname>"
   ```

   Lo script crea alcuni certificati e chiavi. Prendere nota di due, si farà riferimento nella sezione successiva: 
   * `<WRKDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<gateway hostname>.key.pem`

## <a name="install-certificates-on-the-gateway"></a>Installare i certificati nel gateway

Dopo aver creato una catena di certificati, è necessario installarla nel dispositivo gateway IoT Edge e configurare il runtime IoT Edge perché faccia riferimento ai nuovi certificati. 

1. Copiare i file seguenti da *\<WRKDIR>* . Salvarli in una posizione qualsiasi nel dispositivo IoT Edge. La directory di destinazione nel dispositivo IoT Edge è denominata *\<CERTDIR>* . 

   * Certificato della CA del dispositivo: `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * Chiave privata della CA del dispositivo: `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`
   * CA - radice `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   È possibile usare un servizio come [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) o una funzione, ad esempio [protocollo copia sicura](https://www.ssh.com/ssh/scp/) per spostare i file di certificato.  Se è stato generato i certificati nel dispositivo IoT Edge stesso, è possibile ignorare questo passaggio e usare il percorso alla directory di lavoro.

2. Aprire il file di configurazione del daemon di sicurezza di IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Impostare il **certificato** proprietà nel file config. yaml per il percorso completo per i file di certificato e la chiave nel dispositivo IoT Edge. Rimuovere il `#` carattere prima le proprietà del certificato per rimuovere il commento quattro righe. Tenere presente che rientri in yaml sono due spazi.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Nei dispositivi Linux, verificare che l'utente **iotedge** dispone di autorizzazioni di lettura per la directory che contiene i certificati. 

## <a name="deploy-edgehub-to-the-gateway"></a>Distribuire Hub Edge nel gateway

Quando si installa prima di tutto di IoT Edge in un dispositivo, il modulo di un solo sistema viene avviato automaticamente: l'agente di IoT Edge. Perché il dispositivo possa operare come gateway, sono necessari entrambi i moduli di sistema. Se non sono stati distribuiti tutti i moduli nel dispositivo gateway prima di creare una distribuzione iniziale per il dispositivo avviare il secondo modulo di sistema, l'hub di IoT Edge. La distribuzione risulterà vuota perché non si aggiungono tutti i moduli nella procedura guidata, ma garantirà che entrambi i moduli di sistema siano in esecuzione. 

È possibile controllare quali moduli sono in esecuzione in un dispositivo con il comando `iotedge list`. Se l'elenco restituisce solo il modulo **edgeAgent** senza **edgeHub**, procedere come segue:

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

I dispositivi IoT Edge standard non necessitano disponibile la connettività in ingresso alla funzione, perché tutte le comunicazioni con l'IoT Hub viene eseguita tramite le connessioni in uscita. Dispositivi gateway sono diversi perché devono ricevere messaggi dai dispositivi downstream. Se un firewall tra i dispositivi downstream e il dispositivo gateway, quindi la comunicazione deve essere consentito tramite il firewall anche.

Per uno scenario di gateway a funzionare, almeno uno dei protocolli supportati dell'hub IoT Edge deve essere aperto per il traffico in ingresso dai dispositivi downstream. I protocolli supportati sono MQTT, AMQP e HTTPS. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Instradare i messaggi da dispositivi downstream
Il runtime IoT Edge può instradare i messaggi inviati dai dispositivi downstream come i messaggi inviati dai moduli. Questa funzionalità consente di eseguire analitica in un modulo in esecuzione nel gateway prima di inviare dati al cloud. 

Attualmente, il modo per instradare i messaggi inviati dai dispositivi downstream consiste nel differenziarli dai messaggi inviati dai moduli. I messaggi inviati da tutti i moduli contengono una proprietà di sistema denominata **connectionModuleId** ma non i messaggi inviati dai dispositivi downstream. È possibile utilizzare la clausola WHERE della route da escludere eventuali messaggi che contengono tale proprietà di sistema. 

La route di seguito è riportato un esempio che invia messaggi da qualsiasi dispositivo downstream a un modulo denominato `ai_insights`e quindi dalla `ai_insights` all'IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Per altre informazioni sul routing dei messaggi, vedere [distribuire moduli e stabilire le route](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Abilitare l'operazione non in linea estesa

Inizia con la [rilascio la versione 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) del runtime di IoT Edge, il dispositivo gateway e i dispositivi downstream che si connettono a esso possono essere configurati per l'operazione non in linea estesa. 

Con questa funzionalità, moduli locali o i dispositivi downstream possono ripetere l'autenticazione con il dispositivo IoT Edge in base alle esigenze e comunicare tra loro usando i messaggi e metodi quando disconnesso dall'hub IoT. Per altre informazioni, vedere [Informazioni sulle funzionalità per periodi offline prolungati per i dispositivi IoT Edge, i moduli e i dispositivi figlio](offline-capabilities.md).

Per abilitare le funzionalità offline estese, si stabilisce una relazione padre-figlio tra un dispositivo gateway IoT Edge e i dispositivi downstream che si connetteranno ad esso. Tali passaggi sono illustrati più dettagliatamente [autenticare un dispositivo all'IoT Hub di Azure a valle](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un dispositivo IoT Edge che opera come gateway trasparente, è necessario configurare i dispositivi downstream in modo da ritenere attendibile il gateway e inviarvi messaggi. Per altre informazioni, vedere [connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md) e [autenticare un dispositivo all'IoT Hub di Azure a valle](how-to-authenticate-downstream-device.md).
