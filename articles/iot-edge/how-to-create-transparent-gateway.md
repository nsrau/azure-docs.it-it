---
title: Creare un dispositivo gateway trasparente con Azure IoT Edge | Microsoft Docs
description: "Usare Azure IoT Edge per creare un dispositivo gateway trasparente in grado di elaborare informazioni per più dispositivi"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/04/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c3621cb860339499089ebdf3c3581faf770f1fe3
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>Creare un dispositivo IoT Edge come gateway trasparente - anteprima

Questo articolo contiene istruzioni dettagliate per usare un dispositivo IoT Edge come gateway trasparente. Nella parte restante di questo articolo il termine *gateway IoT Edge* si riferisce a un dispositivo IoT Edge usato come gateway trasparente. Per altri dettagli, vedere [Come usare un dispositivo Azure IoT Edge come gateway - anteprima][lnk-edge-as-gateway], che offre una panoramica concettuale. 

>[!NOTE]
>Al momento:
> * Se il gateway è disconnesso dall'hub IoT, i dispositivi downstream non possono eseguire l'autenticazione tramite gateway.
> * i dispositivi IoT Edge non possono connettersi ai gateway IoT Edge.

## <a name="understand-the-azure-iot-device-sdk"></a>Comprendere Azure IoT SDK per dispositivi


L'hub Edge installato in tutti i dispositivi IoT Edge espone ai dispositivi downstream le primitive seguenti:

* messaggi da dispositivo a cloud e da cloud a dispositivo
* metodi diretti
* operazioni di dispositivo gemello

I dispositivi downstream non sono attualmente in grado di usare il caricamento di file durante la connessione tramite un gateway IoT Edge.

Quando si connettono i dispositivi a un gateway IoT Edge tramite Azure IoT SDK per dispositivi, è necessario:

* Configurare il dispositivo downstream con una stringa di connessione che fa riferimento al nome host del dispositivo gateway;
* Verificare che il dispositivo downstream consideri attendibile il certificato usato dal dispositivo gateway per accettare la connessione.

Quando si installa il runtime di Azure IoT Edge usando lo script di controllo, viene creato un certificato per l'hub Edge, come descritto nell'esercitazione [Installare IoT Edge in un dispositivo simulato in Windows][lnk-tutorial1-win] e [Linux][lnk-tutorial1-lin]. Questo certificato viene usato dall'hub Edge per accettare le connessioni TLS in ingresso e deve essere considerato attendibile dal dispositivo downstream quando ci si connette al dispositivo gateway.

È possibile creare qualsiasi infrastruttura di certificati che abilita la relazione di trust necessaria per la topologia dispositivo-gateway. Questo articolo presuppone la stessa configurazione di certificato usata per abilitare la [sicurezza CA X.509][lnk-iothub-x509] nell'hub IoT, che implica un certificato CA X.509 associato a un hub IoT specifico (la *CA proprietaria dell'hub IoT*) e una serie di certificati, firmati da tale CA, installati nei dispositivi IoT Edge.

>[!IMPORTANT]
>Per l'autenticazione con l'hub IoT, i dispositivi IoT Edge e quelli downstream possono attualmente usare solo [token SAS] [ lnk-iothub-tokens]. I certificati vengono usati solo per convalidare la connessione TLS tra il dispositivo gateway e il livello foglia.

La configurazione usa la **CA proprietaria dell'hub IoT** come:
* Un certificato di firma per la configurazione del runtime di IoT Edge su tutti i dispositivi IoT Edge; e
* Un certificato di chiave pubblica installato nei dispositivi downstream.

Ne risulta una soluzione che consente a tutti i dispositivi di usare qualsiasi dispositivo IoT Edge come gateway, a condizione che siano connessi allo stesso hub IoT.

## <a name="create-the-certificates-for-test-scenarios"></a>Creare i certificati per gli scenari di test

È possibile usare gli script di esempio di Powershell e Bash descritti in [Managing CA Certificate Sample] [ lnk-ca-scripts] (Esempio di gestione di un certificato della CA) per generare un certificato autofirmato dalla **CA proprietaria dell'hub IoT** e certificati del dispositivo firmati con questo.

>[!IMPORTANT]
>Questo esempio ha solo finalità di verifica. Per gli scenari di produzione, vedere [Proteggere la distribuzione di IoT] [ lnk-iothub-secure-deployment] per le linee guida di Azure IoT su come proteggere la soluzione IoT ed eseguire il provisioning del certificato di conseguenza.


1. Clonare SDK di Microsoft Azure IoT e librerie per C da GitHub:

   ```
   git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
   ```

2. Per installare gli script di certificato, seguire le istruzioni in **Step 1 - Initial Setup** (Passaggio 1: Configurazione iniziale) di [Managing CA Certificate Sample][lnk-ca-scripts] (Esempio di gestione di un certificato della CA). 
3. Per generare la **CA proprietaria dell'hub IoT**, seguire le istruzioni in **Step 2 - Create the certificate chain** (Passaggio 2: creare la catena di certificati). Questo file viene usato per i dispositivi a valle per convalidare la connessione.
4. Per generare un certificato per il dispositivo gateway, attenersi alle istruzioni di PowerShell o Bash:

### <a name="bash"></a>Bash

Creare il nuovo certificato del dispositivo:

   ```
   ./certGen.sh create_edge_device_certificate myGateway
   ```

Vengono creati nuovi file: .\certs\new-edge-device.* che contiene la chiave pubblica e il file PFX e .\private\new-edge-device.key.pem che contiene la chiave privata del dispositivo.
 
Nella directory `certs` eseguire il comando seguente per ottenere la catena completa della chiave pubblica del dispositivo:

   ```
   cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem
   ```

### <a name="powershell"></a>PowerShell

Creare il nuovo certificato del dispositivo: 
   ```
   New-CACertsEdgeDevice myGateway
   ```

Vengono creati i file myEdgeDevice * che contengono la chiave pubblica, la chiave privata e il file PFX del certificato. 

Quando, durante il processo di firma, viene richiesto di immettere una password, digitare "1234".

## <a name="configure-a-gateway-device"></a>Creare un dispositivo gateway

Per configurare il dispositivo IoT Edge come gateway è sufficiente configurare l'utilizzo del certificato del dispositivo creato nella sezione precedente.

Si presuppone l'impiego dei nomi di file dell'esempio precedente:

| Output | Nome file |
| ------ | --------- |
| Certificato del dispositivo | `certs/new-edge-device.cert.pem` |
| Chiave privata del dispositivo | `private/new-edge-device.cert.pem` |
| Catena di certificati del dispositivo | `certs/new-edge-device-full-chain.cert.pem` |
| CA proprietaria dell'hub IoT | `certs/azure-iot-test-only.root.ca.cert.pem`  |

Fornire le informazioni del dispositivo e il certificato al runtime di IoT Edge. 
 
In Linux, usando l'output Bash:

   ```
   sudo iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem
   ```

In Windows, usando l'output di PowerShell:

   ```
   iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/RootCA.pem
   ```

Per impostazione predefinita gli script di esempio non impostano una passphrase per la chiave privata del dispositivo. Se si imposta una passphrase, aggiungere il parametro seguente:

   ```
   --device-ca-passphrase {passphrase}
   ```

Lo script chiederà di impostare una passphrase per il certificato dell'agente Edge. Riavviare il runtime di IoT Edge dopo l'invio di questo comando:

   ```
   iotedgectl restart
   ```

## <a name="configure-a-downstream-device"></a>Configurare un dispositivo a valle

Qualsiasi applicazione che usa [Azure IoT SDK per dispositivi][lnk-devicesdk], come quella descritta in [Connettere il dispositivo all'hub IoT usando .NET] [ lnk-iothub-getstarted], può costituire un dispositivo downstream.

Innanzitutto, un'applicazione per dispositivi downstream deve considerare attendibile il certificato della **CA proprietaria dell'hub IoT** per convalidare le connessioni TLS dei dispositivi gateway. Due le modalità per ottenere ciò: a livello di sistema operativo o, per alcuni linguaggi, a livello di applicazione.

Per le applicazioni .NET, ad esempio, è possibile aggiungere il frammento di codice seguente per considerare attendibile un certificato in formato con estensione pem archiviato nel percorso `certPath`. A seconda della versione dello script usata, il percorso si riferisce a `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) o a `RootCA.pem` (Powershell).

   ```
   using System.Security.Cryptography.X509Certificates;
   
   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

L'esecuzione di questo passaggio al livello del sistema operativo è diversa nelle distribuzioni Windows e Linux.

Il secondo passaggio consiste nell'inizializzare l'SDK del dispositivo hub IoT con una stringa di connessione che fa riferimento al nome host del dispositivo gateway.
A tal fine, aggiungere la proprietà `GatewayHostName` alla stringa di connessione del dispositivo. Ad esempio, di seguito è riportato un esempio di stringa di connessione del dispositivo, alla quale è stata aggiunta la proprietà `GatewayHostName`:

   ```
   HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com
   ```

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
