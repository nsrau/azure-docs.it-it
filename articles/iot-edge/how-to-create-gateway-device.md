---
title: Creare un dispositivo gateway con Azure IoT Edge | Microsoft Docs
description: "Usare IoT Edge di Azure per creare un dispositivo gateway in grado di elaborare informazioni per più dispositivi"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e1337ddf5ed84a06a62e2faa198f3e8fb49bc3bd
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-iot-edge-gateway-device-to-process-data-from-other-iot-devices---preview"></a>Creare un dispositivo gateway IoT Edge per elaborare i dati provenienti da altri dispositivi IoT - anteprima

Esistono due modi per usare i dispositivi IoT Edge come gateway:

* Connettere i dispositivi downstream che usano [Azure IoT SDK per dispositivi][lnk-devicesdk].
* Connettere i dispositivi che non usano il protocollo Hub IoT.

Dall'impiego di un dispositivo IoT Edge come gateway si ottengono i vantaggi seguenti:

* **Connessione multiplexing**. Tutti i dispositivi connessi all'hub IoT tramite un dispositivo IoT Edge impiegheranno la stessa connessione sottostante.
* **Smorzamento del traffico** . Il dispositivo IoT Edge esegue automaticamente il backoff esponenziale in caso di limitazione dell'hub IoT, salvando al contempo i messaggi in locale. Ciò rende la soluzione resiliente ai picchi di traffico.
* **Supporto offline limitato**. Il dispositivo gateway archivia in locale i messaggi che non possono essere recapitati all'hub IoT.

In questo articolo viene denominato *gateway IoT Edge* un dispositivo IoT Edge usato come gateway.

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

Quando si connettono i dispositivi a un gateway IoT Edge tramite il dispositivo Azure IoT SDK per dispositivi, è necessario:

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

1. Eseguire il passaggio 1 di [Managing CA Certificate Sample] [ lnk-ca-scripts] (Esempio di gestione di un certificato della CA) per installare gli script.
2. Eseguire il passaggio 2 per generare la **CA proprietaria dell'hub IoT** ; questo file verrà usato dai dispositivi downstream per convalidare la connessione.

Usare le istruzioni seguenti per generare un certificato per il dispositivo gateway.

#### <a name="bash"></a>Bash

* Eseguire `./certGen.sh create_edge_device_certificate myGateway` per creare il nuovo certificato del dispositivo.  
  Vengono così creati i file .\certs\new-edge-device.* che contengono la chiave pubblica e il file PFX e .\private\new-edge-device.key.pem che contengono la chiave privata del dispositivo.  
* Eseguire `cat new-edge-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-edge-device-full-chain.cert.pem` per ottenere la chiave pubblica.
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

Per le applicazioni .NET, ad esempio, è possibile aggiungere il frammento di codice seguente per considerare attendibile un certificato in formato con estensione pem archiviato nel percorso `certPath`.

        using System.Security.Cryptography.X509Certificates;
        
        ...

        X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
        store.Open(OpenFlags.ReadWrite);
        store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
        store.Close();

Si noti che gli script di esempio citati in precedenza generano la chiave pubblica nel file `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) o `RootCA.pem` (Powershell).

L'esecuzione di questo passaggio al livello del sistema operativo è diversa nelle distribuzioni Windows e Linux.

Il secondo passaggio consiste nell'inizializzare l'SDK del dispositivo hub IoT con una stringa di connessione che fa riferimento al nome host del dispositivo gateway.
A tal fine, aggiungere la proprietà `GatewayHostName` alla stringa di connessione del dispositivo. Ad esempio, di seguito è riportato un esempio di stringa di connessione del dispositivo, alla quale è stata aggiunta la proprietà `GatewayHostName`:

        HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com

Questi due passaggi consentono all'applicazione del dispositivo di connettersi al dispositivo gateway.

## <a name="use-other-protocols"></a>Uso di altri protocolli

Una delle funzioni principali di un dispositivo gateway è l'interpretazione dei protocolli dei dispositivi downstream. Per connettere dispositivi che non usano il protocollo dell'hub IoT, è possibile sviluppare un modulo IoT Edge che esegue questa conversione e si connette all'hub IoT per conto del dispositivo downstream.

È possibile creare un gateway *trasparente* o *opaco*:

| &nbsp; | Gateway trasparente | Gateway opaco|
|--------|-------------|--------|
| Identità archiviate nel registro delle identità dell'hub IoT | Identità di tutti i dispositivi connessi | Solo l'identità del dispositivo gateway |
| Dispositivo gemello | Ogni dispositivo connesso ha il proprio dispositivo gemello | Solo il gateway ha dispositivi e moduli gemelli |
| Metodi diretti e messaggi da cloud a dispositivo | Il cloud può indirizzare ogni singolo dispositivo connesso | Il cloud può indirizzare solo il dispositivo gateway |
| [Quote e limitazioni dell'hub IoT][lnk-iothub-throttles-quotas] | Si applicano a ogni dispositivo | Si applicano al dispositivo gateway |

Quando si usa un modello di gateway opaco, tutti i dispositivi connessi tramite tale gateway condividono la stessa coda da cloud a dispositivo, che può contenere al massimo 50 messaggi. Ne consegue che il modello di gateway opaco va usato solo quando i dispositivi che si connettono tramite ogni gateway sul campo sono pochi e il relativo traffico da cloud a dispositivo è limitato.

Quando si implementa un gateway opaco, il modulo di conversione del protocollo usa la stringa di connessione del modulo.

Quando si implementa un gateway trasparente, il modulo crea più istanze del client del dispositivo hub IoT, usando le stringhe di connessione dei dispositivi downstream.

## <a name="next-steps"></a>Passaggi successivi

- [Understand the requirements and tools for developing IoT Edge modules][lnk-module-dev] (Informazioni sui requisiti e sugli strumenti per sviluppare moduli IoT Edge).

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/CACertToolEdge/tools/CACertificates/CACertificateOverview.md