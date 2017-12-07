---
title: Creare un dispositivo gateway trasparente con Azure IoT Edge | Microsoft Docs
description: "Usare Azure IoT Edge per creare un dispositivo gateway trasparente in grado di elaborare informazioni per più dispositivi"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/27/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: eaabf360eec48fad6bb1b8b889f30d746520eef0
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>Creare un dispositivo IoT Edge come gateway trasparente - anteprima

L'articolo [Come usare un dispositivo IoT Edge come gateway] [ lnk-edge-as-gateway] illustra i concetti di base relativi all'uso di un dispositivo IoT Edge come gateway trasparente o come gateway con conversione di protocollo o identità. Questo articolo contiene istruzioni dettagliate per usare un dispositivo IoT Edge come gateway trasparente. Nella parte restante di questo articolo il termine *gateway IoT Edge* si riferisce a un dispositivo IoT Edge usato come gateway trasparente.

>[!NOTE]
>Al momento:
> * i dispositivi downstream non sono in grado di eseguire l'autenticazione tramite gateway quando quest'ultimo è disconnesso dall'hub IoT;
> * i dispositivi IoT Edge non possono connettersi ai gateway IoT Edge.

## <a name="use-the-azure-iot-device-sdk"></a>Usare Azure IoT SDK per dispositivi


L'hub Edge installato in tutti i dispositivi IoT Edge espone ai dispositivi downstream le primitive seguenti:

* messaggi da dispositivo a cloud e da cloud a dispositivo;
* metodi diretti;
* operazioni di dispositivo gemello.

>[!NOTE]
>I dispositivi downstream non sono attualmente in grado di usare il caricamento di file durante la connessione tramite un gateway IoT Edge.

Quando si connettono i dispositivi a un gateway IoT Edge tramite Azure IoT SDK per dispositivi, è necessario:

* Configurare il dispositivo downstream con una stringa di connessione che fa riferimento al nome host del dispositivo gateway;
* Verificare che il dispositivo downstream consideri attendibile il certificato usato dal dispositivo gateway per accettare la connessione.

Quando si installa il runtime di Azure IoT Edge usando lo script di controllo, viene creato un certificato per l'hub Edge, come descritto nell'esercitazione Installare IoT Edge in un dispositivo simulato in [Windows] [ lnk-tutorial1-win] e [Linux][lnk-tutorial1-lin]. Questo certificato viene usato dall'hub Edge per accettare le connessioni TLS in ingresso e deve essere considerato attendibile dal dispositivo downstream quando ci si connette al dispositivo gateway.

È possibile creare qualsiasi infrastruttura di certificati che abilita la relazione di trust necessaria per la topologia dispositivo-gateway. Questo articolo presuppone la stessa configurazione di certificato usata per abilitare la [sicurezza CA X.509] [ lnk-iothub-x509] nell'hub IoT, che implica un certificato CA X.509 associato a un hub IoT specifico (la *CA proprietaria dell'hub IoT*) e una serie di certificati, firmati da tale CA, installati nei dispositivi IoT Edge.

>[!IMPORTANT]
>Per l'autenticazione con l'hub IoT, i dispositivi IoT Edge e quelli downstream possono attualmente usare solo [token SAS] [ lnk-iothub-tokens]. I certificati vengono usati solo per convalidare la connessione TLS tra il dispositivo gateway e il livello foglia.

La configurazione usa la **CA proprietaria dell'hub IoT** come:
* un certificato di firma per la configurazione del runtime di IoT Edge su tutti i dispositivi IoT Edge;
* un certificato di chiave pubblica installato nei dispositivi downstream.

Ne risulta una soluzione che consente a tutti i dispositivi di usare qualsiasi dispositivo IoT Edge come gateway, a condizione che siano connessi allo stesso hub IoT.

### <a name="create-the-certificates-for-test-scenarios"></a>Creare i certificati per gli scenari di test

È possibile usare gli script di esempio di Powershell e Bash descritti in [Managing CA Certificate Sample] [ lnk-ca-scripts] (Esempio di gestione di un certificato della CA) per generare un certificato autofirmato dalla **CA proprietaria dell'hub IoT** e certificati del dispositivo firmati con questo.

1. Eseguire il passaggio 1 di [Managing CA Certificate Sample] [ lnk-ca-scripts] (Esempio di gestione di un certificato della CA) per installare gli script. Assicurarsi di eseguire la clonazione dal ramo `modules-preview`:
                
                git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
2. Eseguire il passaggio 2 per generare la **CA proprietaria dell'hub IoT** ; questo file verrà usato dai dispositivi downstream per convalidare la connessione.

Usare le istruzioni seguenti per generare un certificato per il dispositivo gateway.

#### <a name="bash"></a>Bash

* Eseguire `./certGen.sh create_edge_device_certificate myGateway` per creare il nuovo certificato del dispositivo.  
  Vengono così creati i file .\certs\new-edge-device.* che contengono la chiave pubblica e il file PFX e .\private\new-edge-device.key.pem che contengono la chiave privata del dispositivo.  
* Nella directory `certs` eseguire `cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem` per ottenere la catena completa della chiave pubblica del dispositivo.
* `./private/new-edge-device.cert.pem`contiene la chiave privata del dispositivo.

#### <a name="powershell"></a>PowerShell

* Eseguire `New-CACertsEdgeDevice myGateway` per creare il nuovo certificato del dispositivo.
  Viene così creato il file myEdgeDevice * che contiene la chiave pubblica, la chiave privata e il file PFX del certificato.  Quando, durante il processo di firma, viene richiesto di immettere una password, digitare "1234".


>[!IMPORTANT]
>Questo esempio ha solo finalità di verifica. Per gli scenari di produzione, vedere [Proteggere la distribuzione di IoT] [ lnk-iothub-secure-deployment] per le linee guida di Azure IoT su come proteggere la soluzione IoT ed eseguire il provisioning del certificato di conseguenza.

### <a name="configure-an-iot-edge-device-as-a-gateway"></a>Configurare un dispositivo IoT Edge come gateway

Per configurare il dispositivo IoT Edge come gateway è sufficiente configurare l'utilizzo del certificato del dispositivo creato nella sezione precedente.

Si presuppone l'impiego dei nomi di file dell'esempio precedente:

| Output | Script Bash | PowerShell |
| ------ | ----------- | ---------- |
| Certificato del dispositivo | `certs/new-edge-device.cert.pem` | `certs/new-edge-device.cert.pem` |
| Chiave privata del dispositivo | `private/new-edge-device.cert.pem` | `private/new-edge-device.cert.pem` |
| Catena di certificati del dispositivo | `certs/new-edge-device-full-chain.cert.pem` | `certs/new-edge-device-full-chain.cert.pem` |
| CA proprietaria dell'hub IoT | `certs/azure-iot-test-only.root.ca.cert.pem` | `RootCA.pem` |

 Analogamente all'installazione descritta in Distribuire Azure IoT Edge su un dispositivo simulato in [Windows] [ lnk-tutorial1-win] o [Linux][lnk-tutorial1-lin], è necessario fornire le informazioni indicate prima al runtime di IoT Edge. 
 
 Linux:

        sudo iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem

Windows:

        iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/RootCA.pem

Per impostazione predefinita gli script di esempio non impostano una passphrase per la chiave privata del dispositivo. Se si imposta una passphrase, aggiungere il parametro seguente:

        --device-ca-passphrase {passphrase}

Lo script chiederà di impostare una passphrase per il certificato dell'agente Edge.
È necessario riavviare il runtime di IoT Edge dopo l'invio di questo comando.

### <a name="configure-an-iot-hub-device-application-as-a-downstream-device"></a>Configurare un'applicazione del dispositivo hub IoT come dispositivo downstream

Qualsiasi applicazione che usa [Azure IoT SDK per dispositivi][lnk-devicesdk], come quella descritta in [Connettere il dispositivo all'hub IoT usando .NET] [ lnk-iothub-getstarted], può costituire un dispositivo downstream.

Innanzitutto, un'applicazione per dispositivi downstream deve considerare attendibile il certificato della **CA proprietaria dell'hub IoT** per convalidare le connessioni TLS dei dispositivi gateway. Due le modalità per ottenere ciò: a livello di sistema operativo o, per alcuni linguaggi, a livello di applicazione.

Per le applicazioni .NET, ad esempio, è possibile aggiungere il frammento di codice seguente per considerare attendibile un certificato in formato con estensione pem archiviato nel percorso `certPath`. Se si usa lo script precedente, il percorso farà riferimento a `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) o `RootCA.pem` (PowerShell).

        using System.Security.Cryptography.X509Certificates;
        
        ...

        X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
        store.Open(OpenFlags.ReadWrite);
        store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
        store.Close();

L'esecuzione di questo passaggio al livello del sistema operativo è diversa nelle distribuzioni Windows e Linux.

Il secondo passaggio consiste nell'inizializzare l'SDK del dispositivo hub IoT con una stringa di connessione che fa riferimento al nome host del dispositivo gateway.
A tal fine, aggiungere la proprietà `GatewayHostName` alla stringa di connessione del dispositivo. Ad esempio, di seguito è riportato un esempio di stringa di connessione del dispositivo, alla quale è stata aggiunta la proprietà `GatewayHostName`:

        HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com

Questi due passaggi consentono all'applicazione del dispositivo di connettersi al dispositivo gateway.

## <a name="next-steps"></a>Passaggi successivi
[Understand the requirements and tools for developing IoT Edge modules][lnk-module-dev] (Informazioni sui requisiti e sugli strumenti per sviluppare moduli IoT Edge).

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
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/modules-preview/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus