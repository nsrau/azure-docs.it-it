---
title: Creare un dispositivo gateway trasparente - Azure IoT Edge | Microsoft Docs
description: Usare un dispositivo Azure IoT Edge per creare un gateway trasparente in grado di elaborare informazioni da dispositivi downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 467ec25bb9e41180da36f118094324e4fea48cf8
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266108"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurare un dispositivo IoT Edge come gateway trasparente

Questo articolo fornisce istruzioni dettagliate per la configurazione di un dispositivo IoT Edge per funzionare come gateway trasparente per la comunicazione di altri dispositivi con l'hub Internet. In questo articolo il termine *gateway IoT Edge* si riferisce a un dispositivo IoT Edge usato come gateway trasparente. Per altre informazioni, vedere [come è possibile usare un dispositivo IOT Edge come gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>Al momento:
> * I dispositivi abilitati per Edge non possono connettersi ai gateway IoT Edge. 
> * I dispositivi downstream non possono usare il caricamento dei file.

Sono disponibili tre passaggi generali per configurare una connessione del gateway trasparente corretta. Questo articolo illustra il primo passaggio:

1. **Il dispositivo gateway deve essere in grado di connettersi in modo sicuro ai dispositivi downstream, ricevere comunicazioni dai dispositivi downstream e indirizzare i messaggi alla destinazione appropriata.**
2. Il dispositivo downstream deve avere un'identità del dispositivo per poter eseguire l'autenticazione con l'hub Internet e conoscere la comunicazione tramite il dispositivo gateway. Per altre informazioni, vedere [autenticare un dispositivo downstream nell'hub Azure](how-to-authenticate-downstream-device.md).
3. Il dispositivo downstream deve essere in grado di connettersi in modo sicuro al dispositivo gateway. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).


Per il funzionamento di un dispositivo come gateway, è necessario che sia in grado di connettersi in modo sicuro ai dispositivi downstream. Azure IoT Edge permette di usare un'infrastruttura a chiave pubblica (PKI) per configurare connessioni sicure tra dispositivi. Nel caso illustrato si consente la connessione di un dispositivo downstream a un dispositivo IoT Edge che funge da gateway trasparente. Per garantire una ragionevole sicurezza, il dispositivo downstream deve confermare l'identità del dispositivo gateway. Questa verifica dell'identità impedisce ai dispositivi di connettersi a gateway potenzialmente dannosi.

Un dispositivo downstream in uno scenario di gateway trasparente può essere qualsiasi applicazione o piattaforma con un'identità creata con il servizio cloud dell' [Hub Azure](https://docs.microsoft.com/azure/iot-hub) . In molti casi, queste applicazioni usano [Azure IoT SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md). Per scopi pratici, un dispositivo downstream può anche essere un'applicazione in esecuzione nel dispositivo gateway IoT Edge stesso. Tuttavia, un dispositivo IoT Edge non può essere a valle di un gateway di IoT Edge. 

È possibile creare qualsiasi infrastruttura di certificati che abilita la relazione di trust necessaria per la topologia dispositivo-gateway. In questo articolo si presuppone la stessa configurazione del certificato usata per abilitare la sicurezza dell' [autorità di certificazione x. 509](../iot-hub/iot-hub-x509ca-overview.md) nell'hub di tutto, che include un certificato della CA x. 509 associato a un hub di cose specifico (la CA radice dell'hub Internet), una serie di certificati firmati con questa CA e una CA per il dispositivo IoT Edge.

![Installazione del certificato del gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Il termine "CA radice" usato in questo articolo si riferisce al certificato pubblico dell'autorità in primo piano della catena di certificati PKI e non necessariamente alla radice del certificato di un'autorità di certificazione del sindacato. In molti casi, si tratta in realtà di un certificato pubblico CA intermedio. 

Il gateway presenta il certificato della CA del dispositivo IoT Edge al dispositivo downstream durante l'avvio della connessione. Il dispositivo downstream verifica che il certificato della CA del dispositivo IoT Edge sia firmato dal certificato CA radice. Questo processo consente al dispositivo downstream di verificare che il gateway provenga da una fonte attendibile.

I passaggi seguenti illustrano il processo di creazione dei certificati e di installazione nei punti giusti del gateway. È possibile usare qualsiasi computer per generare i certificati e quindi copiarli nel dispositivo IoT Edge. 

## <a name="prerequisites"></a>Prerequisiti

* Un computer di sviluppo per la creazione di certificati. 
* Un dispositivo Azure IoT Edge da configurare come gateway. Usare la procedura di installazione di IoT Edge per uno dei sistemi operativi seguenti:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>Generare certificati con Windows

Usare la procedura descritta in questa sezione per generare certificati di test in Windows. È possibile usare un computer Windows per generare i certificati, quindi copiarli in qualsiasi dispositivo IoT Edge in esecuzione su qualsiasi sistema operativo supportato. 

I certificati generati in questa sezione devono essere usati solo a scopo di test. 

### <a name="install-openssl"></a>Installare OpenSSL

Installare OpenSSL per Windows nel computer usato per generare i certificati. Se è già installato OpenSSL nel dispositivo Windows, è possibile ignorare questo passaggio, ma assicurarsi che OpenSSL. exe sia disponibile nella variabile di ambiente PATH. 

Sono disponibili diversi modi per installare OpenSSL, tra cui:

* **Più semplice:** Scaricare e installare i [file binari OpenSSL di terze parti](https://wiki.openssl.org/index.php/Binaries), ad esempio, da [openssl su SourceForge](https://sourceforge.net/projects/openssl/). Aggiungere il percorso completo di openssl.exe alla variabile di ambiente PATH. 
   
* **Consigliato:** Scaricare il codice sorgente OpenSSL e generare i file binari nel computer in uso in maniera autonoma o tramite [vcpkg](https://github.com/Microsoft/vcpkg). Nelle istruzioni elencate di seguito viene usato vcpkg per scaricare il codice sorgente, compilare e installare OpenSSL nel computer Windows con passaggi molto semplici.

   1. Passare alla directory in cui installare vcpkg, Questa directory è denominata *\<VCPKGDIR>* . Seguire le istruzioni per scaricare e installare [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Dopo l'installazione di vcpkg, eseguire il comando seguente da un prompt di PowerShell per installare il pacchetto OpenSSL per Windows x64. Per completare l'installazione sono in genere necessari circa cinque minuti.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Aggiungere `<VCPKGDIR>\installed\x64-windows\tools\openssl` alla variabile di ambiente PATH in modo da rendere il file openssl.exe disponibile per la chiamata.

### <a name="prepare-creation-scripts"></a>Preparare gli script di creazione

Il repository git Azure IoT Edge contiene gli script che è possibile usare per generare i certificati di test. In questa sezione si clona il repository IoT Edge ed eseguire gli script. 

1. Aprire una finestra di PowerShell in modalità amministratore. 

2. Clonare il repository git che contiene gli script per generare certificati non di produzione. Questi script consentono di creare i certificati necessari per configurare un gateway trasparente. Usare il comando `git clone` o [scaricare il file ZIP](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Passare alla directory che si vuole usare, In questo articolo verrà chiamata la directory  *\<WRKDIR >* . Tutti i certificati e le chiavi verranno creati in questa directory di lavoro.

4. Copiare i file di configurazione e script dal repository clonato alla directory di lavoro. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Se il repository è stato scaricato come zip, il nome della cartella è `iotedge-master` e il resto del percorso è lo stesso. 
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

7. Portare le funzioni usate dagli script nello spazio dei nomi globale di PowerShell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   Nella finestra di PowerShell verrà visualizzato un avviso indicante che i certificati generati da questo script sono solo a scopo di test e non devono essere utilizzati in scenari di produzione.

8. Verificare che OpenSSL sia stato installato correttamente e assicurarsi che non siano presenti conflitti di nome con i certificati esistenti. In caso di problemi, lo script dovrebbe descrivere come correggerli nel sistema in uso.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Creare i certificati

In questa sezione verranno creati tre certificati, che verranno quindi connessi in una catena. L'inserimento dei certificati in una catena permette di installarli facilmente nel dispositivo gateway IoT Edge e in tutti i dispositivi downstream.  

1. Creare il certificato CA radice e firmare un certificato intermedio. Tutti i certificati vengono inseriti nella directory di lavoro.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Questo comando script crea diversi file di certificato e di chiave, ma ne verrà fatto riferimento a uno in particolare più avanti in questo articolo:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Creare il certificato della CA del dispositivo IoT Edge e la chiave privata con il comando seguente. Specificare un nome per il certificato della CA, ad esempio **MyEdgeDeviceCA**. Il nome viene usato per assegnare un nome ai file e durante la generazione del certificato. 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   Questo comando script crea diversi file di certificato e chiave, inclusi i due che verranno riferiti più avanti in questo articolo:
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Se si specifica un nome diverso da **MyEdgeDeviceCA**, i certificati e le chiavi creati da questo comando rifletteranno tale nome. 

Ora che sono disponibili i certificati, passare a [installare i certificati sul gateway](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Generare certificati con Linux

Usare la procedura descritta in questa sezione per generare certificati di test in Linux. È possibile usare un computer Linux per generare i certificati e quindi copiarli in qualsiasi dispositivo IoT Edge eseguito in qualsiasi sistema operativo supportato. 

I certificati generati in questa sezione devono essere usati solo a scopo di test. 

### <a name="prepare-creation-scripts"></a>Preparare gli script di creazione

Il repository git Azure IoT Edge contiene gli script che è possibile usare per generare i certificati di test. In questa sezione si clona il repository IoT Edge ed eseguire gli script. 

1. Clonare il repository git che contiene gli script per generare certificati non di produzione. Questi script consentono di creare i certificati necessari per configurare un gateway trasparente. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Passare alla directory che si vuole usare, Si farà riferimento a questa directory in tutto l'articolo come  *\<WRKDIR >* . Tutti i file di certificato e di chiave verranno creati in questa directory.
  
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

### <a name="create-certificates"></a>Creare i certificati

In questa sezione verranno creati tre certificati, che verranno quindi connessi in una catena. L'inserimento dei certificati in un file di catena permette di installarli facilmente nel dispositivo gateway IoT Edge e in tutti i dispositivi downstream.  

1. Creare il certificato CA radice e un certificato intermedio. Questi certificati vengono inseriti in *\<WRKDIR*.

   Se sono già stati creati certificati radice e intermedi in questa directory di lavoro, non eseguire di nuovo lo script. Se si esegue nuovamente lo script, i certificati esistenti vengono sovrascritti. Al contrario, procedere al passaggio successivo. 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Lo script crea diversi certificati e chiavi. Prendere nota di un, a cui verrà fatto riferimento nella sezione successiva:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Creare il certificato della CA del dispositivo IoT Edge e la chiave privata con il comando seguente. Specificare un nome per il certificato della CA, ad esempio **MyEdgeDeviceCA**. Il nome viene usato per assegnare un nome ai file e durante la generazione del certificato. 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   Lo script crea diversi certificati e chiavi. Prendere nota di due, a cui si farà riferimento nella sezione successiva: 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Se si specifica un nome diverso da **MyEdgeDeviceCA**, i certificati e le chiavi creati da questo comando rifletteranno tale nome. 

## <a name="install-certificates-on-the-gateway"></a>Installare i certificati nel gateway

Dopo aver creato una catena di certificati, è necessario installarla nel dispositivo gateway IoT Edge e configurare il runtime IoT Edge perché faccia riferimento ai nuovi certificati. 

1. Copiare i file seguenti da *\<WRKDIR>* . Salvarli in una posizione qualsiasi nel dispositivo IoT Edge. La directory di destinazione nel dispositivo IoT Edge è denominata *\<CERTDIR>* . 

   * Certificato della CA del dispositivo: `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * Chiave privata della CA del dispositivo: `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * CA radice-`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   È possibile usare un servizio come [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) o una funzione come [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) per spostare i file del certificato.  Se i certificati sono stati generati nel dispositivo IoT Edge stesso, è possibile ignorare questo passaggio e usare il percorso della directory di lavoro.

2. Aprire il file di configurazione del daemon di sicurezza di IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Impostare le proprietà del **certificato** nel file config. YAML sul percorso completo dei file di certificato e di chiave nel dispositivo IOT Edge. Rimuovere il `#` carattere prima delle proprietà del certificato per rimuovere il commento dalle quattro righe. Tenere presente che i rientri in YAML sono due spazi.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Nei dispositivi Linux, assicurarsi che l'utente **iotedge** disponga delle autorizzazioni di lettura per la directory che contiene i certificati. 

## <a name="deploy-edgehub-to-the-gateway"></a>Distribuire Hub Edge nel gateway

Quando si installa per la prima volta IoT Edge in un dispositivo, viene avviato automaticamente un solo modulo di sistema, ovvero l'agente di IoT Edge. Perché il dispositivo possa operare come gateway, sono necessari entrambi i moduli di sistema. Se non sono mai stati distribuiti moduli nel dispositivo gateway, creare una distribuzione iniziale per il dispositivo per avviare il secondo modulo di sistema, l'hub IoT Edge. La distribuzione sarà vuota perché non vengono aggiunti moduli nella procedura guidata, ma si assicurerà che entrambi i moduli di sistema siano in esecuzione. 

È possibile controllare quali moduli sono in esecuzione in un dispositivo con il comando `iotedge list`. Se l'elenco restituisce solo il modulo **edgeAgent** senza **edgeHub**, attenersi alla procedura seguente:

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

## <a name="open-ports-on-gateway-device"></a>Aprire le porte nel dispositivo gateway

I dispositivi IoT Edge standard non richiedono la connettività in ingresso per il funzionamento, perché tutte le comunicazioni con l'hub Internet viene eseguita tramite le connessioni in uscita. I dispositivi gateway sono diversi perché devono ricevere messaggi dai dispositivi downstream. Se un firewall è tra i dispositivi downstream e il dispositivo gateway, è necessario che anche la comunicazione sia possibile tramite il firewall.

Per il funzionamento di uno scenario del gateway, è necessario che almeno uno dei protocolli supportati dell'hub IoT Edge sia aperto per il traffico in ingresso dai dispositivi downstream. I protocolli supportati sono MQTT, AMQP e HTTPS. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Instradare i messaggi da dispositivi downstream
Il runtime IoT Edge può instradare i messaggi inviati dai dispositivi downstream come i messaggi inviati dai moduli. Questa funzionalità consente di eseguire analisi in un modulo in esecuzione sul gateway prima di inviare dati al cloud. 

Attualmente, il modo per instradare i messaggi inviati dai dispositivi downstream consiste nel differenziarli dai messaggi inviati dai moduli. I messaggi inviati da tutti i moduli contengono una proprietà di sistema denominata **connectionModuleId** ma non i messaggi inviati dai dispositivi downstream. È possibile utilizzare la clausola WHERE della route da escludere eventuali messaggi che contengono tale proprietà di sistema. 

La route seguente è un esempio che consente di inviare messaggi da qualsiasi dispositivo downstream a un modulo `ai_insights`denominato e quindi da `ai_insights` a hub Internet.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Per altre informazioni sul routing dei messaggi, vedere [distribuire moduli e stabilire le route](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Abilita operazione offline estesa

A partire dalla [versione 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) del runtime di IOT Edge, è possibile configurare il dispositivo gateway e i dispositivi downstream che vi si connettono per l'operazione offline estesa. 

Con questa funzionalità, i moduli locali o i dispositivi downstream possono eseguire di nuovo l'autenticazione con il dispositivo IoT Edge in base alle esigenze e comunicare tra loro usando messaggi e metodi anche quando sono disconnessi dall'hub. Per altre informazioni, vedere [Informazioni sulle funzionalità per periodi offline prolungati per i dispositivi IoT Edge, i moduli e i dispositivi figlio](offline-capabilities.md).

Per abilitare le funzionalità estese offline, è necessario stabilire una relazione padre-figlio tra un dispositivo gateway IoT Edge e i dispositivi downstream a cui si connetterà. Questi passaggi sono illustrati in modo più dettagliato in [eseguire l'autenticazione di un dispositivo downstream nell'hub Azure](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un dispositivo IoT Edge che opera come gateway trasparente, è necessario configurare i dispositivi downstream in modo da ritenere attendibile il gateway e inviarvi messaggi. Continuare con l' [autenticazione di un dispositivo downstream nell'hub Azure](how-to-authenticate-downstream-device.md) Internet per i passaggi successivi per la configurazione dello scenario di gateway trasparente. 
