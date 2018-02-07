---
title: Usare gli strumenti forniti con gli SDK del servizio Device Provisioning in hub IoT di Azure per semplificare lo sviluppo
description: Questo documento illustra gli strumenti di sviluppo disponibili negli SDK del servizio Device Provisioning in hub IoT di Azure
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 01/18/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 76c6f64dea202f661691fafaa78a6d77b4a40f14
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Come usare gli strumenti disponibili negli SDK per semplificare lo sviluppo per il provisioning
Il servizio Device Provisioning in hub IoT semplifica il processo di provisioning supportando il provisioning JIT automatico in modo sicuro e scalabile.  È necessaria un'attestazione di sicurezza sotto forma di certificato X.509 o modulo TPM (Trusted Platform).  Microsoft sta inoltre collaborando con [altri produttori di hardware per la sicurezza](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) per migliorare il livello di protezione della distribuzione di IoT. Comprendere i requisiti di sicurezza hardware può risultare difficile per gli sviluppatori. Per questo motivo, viene fornito un set di SDK del servizio di provisioning di IoT di Azure in modo che gli sviluppatori possano usufruire di un livello dedicato per creare client in grado di comunicare con il servizio di provisioning. Gli SDK offrono anche esempi di scenari comuni, nonché un set di strumenti in grado di semplificare l'attestazione di sicurezza in fase di sviluppo.

## <a name="trusted-platform-module-tpm-simulator"></a>Simulatore TPM (Trusted Platform Module)
Il concetto di [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security#trusted-platform-module-tpm) può fare riferimento a uno standard per archiviare in modo sicuro le chiavi usate per autenticare la piattaforma oppure all'interfaccia di I/O usata per interagire con i moduli che implementano lo standard. Il TPM può essere un componente hardware distinto o integrato e può essere basato su firmware o su software.  Nell'ambiente di produzione, il TPM si trova sul dispositivo come componente hardware distinto o integrato e può essere basato sul firmware. Durante la fase di test, invece, agli sviluppatori viene fornito un simulatore TPM basato sul software.  Per il momento, il simulatore è disponibile solo per attività di sviluppo su piattaforme Windows.

Per usare il simulatore TPM, seguire questa procedura:
1. [Preparare l'ambiente di sviluppo](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment) e clonare il repository GitHub.
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Passare alla cartella del simulatore TPM in ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Eseguire Simulator.exe prima di eseguire qualsiasi applicazione client per il dispositivo di provisioning.
4. Mantenere l'esecuzione del simulatore in background durante l'intero processo di provisioning per ottenere l'ID di registrazione e la chiave di verifica dell'autenticità.  Entrambi i valori sono validi solo per un'istanza dell'esecuzione.

## <a name="x509-certificate-generator"></a>Generatore di certificati X.509
I [certificati X.509](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) possono essere usati come meccanismo di attestazione per ridimensionare la produzione e semplificare il provisioning dei dispositivi.  Esistono [diversi modi](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) per ottenere un certificato X.509:
* Per un ambiente di produzione, è consigliabile acquistare un certificato della CA X.509 da una CA radice pubblica.
* Per un ambiente di test, è possibile generare un certificato X.509 radice o una catena di certificati X.509 usando uno degli strumenti seguenti.
    * OpenSSL: la presente guida fornisce [procedure](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-create-certificates) e istruzioni dettagliate che usano script PowerShell di esempio basati su [OpenSSL](https://www.openssl.org/) per creare e firmare certificati X.509.  Per la generazione dei certificati è possibile anche usare script in altri linguaggi:
        * [Node.JS](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Emulatore DICE (Device Identity Composition Engine): DICE consente di ottenere l'identità crittografica dei dispositivi e l'attestazione basata sul protocollo TLS e certificati client X.509.  [Altre informazioni](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) sull'identità dei dispositivi con DICE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Utilizzo del generatore di certificati X.509 con l'emulatore DICE
Gli SDK offrono un generatore di certificati X.509 con emulatore DICE, disponibile in [Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator),  che può essere usato su più piattaforme.  Il certificato generato, inoltre, può essere usato per attività di sviluppo in altri linguaggi.

Sebbene l'emulatore DICE generi attualmente un certificato radice, un certificato intermedio, un certificato foglia e la relativa chiave privata,  il certificato radice e quello intermedio non possono essere usati per firmare un certificato foglia separato.  Se si vuole testare uno scenario di registrazione di gruppo in cui viene usato un certificato di firma per firmare i certificati foglia di più dispositivi, è possibile usare OpenSSL per produrre una catena di certificati.

Per generare un certificato X.509 usando questo generatore:
1. [Preparare l'ambiente di sviluppo](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment) e clonare il repository GitHub.
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Modificare la radice in azure-iot-sdk-java.
3. Eseguire ```mvn install -DskipTests=true``` per scaricare tutti i pacchetti richiesti e compilare l'SDK
4. Passare alla cartella radice per il generatore di certificati X.509 in ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. Eseguire la compilazione con ```mvn clean install```
6. Eseguire lo strumento con questi comandi:
```
cd target
java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
```
7. Quando richiesto, è facoltativamente possibile immettere un _nome comune_ per i certificati.
8. Lo strumento genera in locale un **certificato client**, la **chiave privata del certificato client**, un **certificato intermedio** e il **certificato radice**.

Il **certificato client** è il certificato foglia sul dispositivo.  Nel client del dispositivo devono essere presenti il **certificato client** e la relativa **chiave privata del certificato client**. A seconda del linguaggio scelto, il meccanismo usato per inserirli nell'applicazione client può essere diverso.  Per altre informazioni, vedere le [guide introduttive](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) sulla creazione di un dispositivo simulato mediante certificati X.509.

È possibile usare il certificato radice o quello intermedio per creare un gruppo di registrazione o una registrazione individuale a [livello di programmazione](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) o tramite il [portale](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Passaggi successivi
* Sviluppare usando [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) per hub IoT di Azure e il servizio Device Provisioning in hub IoT di Azure