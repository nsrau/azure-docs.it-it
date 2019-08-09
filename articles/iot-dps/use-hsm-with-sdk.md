---
title: Procedure di Azure - Come usare meccanismi di attestazione diversi con Device Provisioning Service Client SDK in Azure
description: Procedure di Azure - Come usare meccanismi di attestazione diversi con Device Provisioning Service Client SDK in Azure
author: robinsh
ms.author: robinsh
ms.date: 03/30/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 7dd93298c96842e4e5417a0b2ba023bb71a4e7ba
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884429"
---
# <a name="how-to-use-different-attestation-mechanisms-with-device-provisioning-service-client-sdk-for-c"></a>Come usare meccanismi di attestazione diversi con Device Provisioning Service Client SDK per C

Questo articolo illustra come usare diversi [meccanismi di attestazione](concepts-security.md#attestation-mechanism) con Device Provisioning Service Client SDK per C. È possibile usare un dispositivo fisico o un simulatore. Il servizio di provisioning supporta l'autenticazione per due tipi di meccanismi di attestazione: X. 509 e Trusted Platform Module (TPM).

## <a name="prerequisites"></a>Prerequisiti

Preparare l'ambiente di sviluppo in base alle indicazioni disponibili nella sezione "Preparare l'ambiente di sviluppo" nella Guida [Creare ed effettuare il provisioning di un dispositivo simulato](./quick-create-simulated-device.md).

### <a name="choose-an-attestation-mechanism"></a>Scegliere un meccanismo di attestazione

In qualità di produttore di dispositivi, è necessario prima di tutto scegliere un meccanismo di attestazione in base a uno dei tipi supportati. [Device Provisioning Service client SDK per C](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) supporta attualmente i meccanismi di attestazione seguenti: 

- [Trusted Platform Module (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module): Il modulo TPM è uno standard stabilito per la maggior parte delle piattaforme per dispositivi basati su Windows, nonché per alcuni dispositivi basati su Linux/Ubuntu. In qualità di produttore di dispositivi, è possibile scegliere questo meccanismo di attestazione se nei dispositivi è installato uno di questi sistemi operativi e si è alla ricerca di uno standard consolidato. Con i chip TPM è possibile registrare solo ogni dispositivo individualmente nel servizio Device Provisioning. Ai fini dello sviluppo è possibile usare il simulatore TPM nel computer di sviluppo Windows o Linux.

- [X.509](https://cryptography.io/en/latest/x509/): I certificati X. 509 possono essere archiviati in chip relativamente più recenti, detti [moduli di protezione hardware (HSM)](concepts-security.md#hardware-security-module). In Microsoft si sta lavorando inoltre sui chip RIoT o DICE, che implementano i certificati X.509. I chip X.509 consentono di eseguire registrazioni di dispositivi in blocco nel portale. Supportano anche determinati sistemi operativi diversi da Windows, ad esempio embedOS. Ai fini dello sviluppo, l'SDK client del servizio Device Provisioning supporta il simulatore di dispositivi X.509. 

Per altre informazioni, vedere i [concetti relativi alla sicurezza](concepts-security.md) e i [concetti relativi al provisioning automatico](/azure/iot-dps/concepts-auto-provisioning) del servizio Device Provisioning in hub IoT.

## <a name="enable-authentication-for-supported-attestation-mechanisms"></a>Abilitare l'autenticazione per i meccanismi di attestazione supportati

È necessario abilitare la modalità di autenticazione SDK (X **.** 509 o TPM) per il dispositivo fisico o il simulatore prima che ne sia consentita la registrazione nel portale di Azure. Per cominciare, passare alla cartella radice per azure-iot-sdk-c. Eseguire quindi il comando specificato, in base alla modalità di autenticazione scelta.

### <a name="use-x509-with-simulator"></a>Usare X **.** 509 con il simulatore

Il servizio di provisioning include un emulatore DICE (Device Identity Composition Engine) che genera un certificato X **.** 509 per l'autenticazione del dispositivo. Per abilitare l'autenticazione X **.** 509, eseguire il comando seguente: 

```
cmake -Ddps_auth_type=x509 ..
```

Per informazioni sull'hardware con DICE, vedere [qui](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/).

### <a name="use-x509-with-hardware"></a>Usare X **.** 509 con l'hardware

Il servizio di provisioning può essere usato con X **.** 509 su altro hardware. È necessaria un'interfaccia tra hardware e SDK per stabilire una connessione. Per informazioni sull'interfaccia, contattare il produttore del modulo di protezione hardware.

### <a name="use-tpm"></a>Usare TPM

Il servizio di provisioning può connettersi a chip TPM di hardware Windows e Linux con un token di firma di accesso condiviso. Per abilitare l'autenticazione TPM, eseguire il comando seguente:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>Usare TPM con un simulatore

Se non si ha alcun dispositivo con chip TPM, è possibile usare un simulatore per finalità di sviluppo nel sistema operativo Windows. Per abilitare l'autenticazione TPM ed eseguire il simulatore TPM, eseguire il comando seguente:

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>Compilare l'SDK
Compilare l'SDK prima di creare la registrazione del dispositivo.

### <a name="linux"></a>Linux
- Per compilare l'SDK in Linux:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- Per compilare file binari di debug, aggiungere l'opzione CMake corrispondente al comando di generazione del progetto, ad esempio:
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- Sono disponibili molte [opzioni di configurazione CMake](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html) per la compilazione dell'SDK. È ad esempio possibile disabilitare uno degli stack di protocolli disponibili aggiungendo un argomento al comando di generazione del progetto CMake:
    ```
    cmake -Duse_amqp=OFF ..
    ```
- È anche possibile compilare ed eseguire unit test:
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Per compilare l'SDK in Windows, seguire questa procedura per generare i file di progetto:
  - Aprire un "Prompt dei comandi per gli sviluppatori per VS2015"
  - Eseguire i comandi CMake seguenti dalla radice del repository:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake -G "Visual Studio 14 2015" ..
    ```
    Questo comando compila librerie x86. Per la compilazione per x64, modificare l'argomento del generatore cmake: 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- Se la generazione del progetto viene completata correttamente, dovrebbe essere visualizzato un file della soluzione Visual Studio (con estensione sln) nella cartella `cmake`. Per compilare l'SDK:
    - Aprire il file **cmake\azure_iot_sdks.sln** in Visual Studio e compilarlo **OPPURE**
    - Eseguire il comando seguente nel prompt dei comandi usato per generare i file di progetto:
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- Per compilare file binari di debug, usare l'argomento MSBuild corrispondente: 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- Sono disponibili molte opzioni di configurazione CMake per la compilazione dell'SDK. È ad esempio possibile disabilitare uno degli stack di protocolli disponibili aggiungendo un argomento al comando di generazione del progetto CMake:
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- È anche possibile compilare ed eseguire unit test:
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>Librerie da includere
- Queste librerie devono essere incluse nell'SDK:
    - Il servizio di provisioning: dps_http_transport, dps_client, dps_security_client
    - Sicurezza per l'hub IoT: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-device-provisioning-services"></a>Creare una voce di registrazione del dispositivo nei servizi Device Provisioning

### <a name="tpm"></a>TPM
Se si usa TPM, seguire le istruzioni disponibili in ["Creare ed effettuare il provisioning di un dispositivo simulato usando il servizio Device Provisioning in hub IoT"](./quick-create-simulated-device.md) per creare una voce di registrazione dei dispositivi nel servizio Device Provisioning e simulare il primo avvio.

### <a name="x509"></a>X **.** 509

1. Per registrare un dispositivo nel servizio di provisioning, è necessario annotare la chiave di verifica dell'autenticità e l'ID di registrazione per ogni dispositivo. Questi valori vengono visualizzati nello strumento di provisioning fornito dall'SDK client. Eseguire questo comando per stampare il certificato della CA radice (per i gruppi di registrazione) e il certificato foglia (per la registrazione singola):
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Accedere al portale di Azure, fare clic sul pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio Device Provisioning.
   - X **.** 509 registrazione singola: Nel pannello di riepilogo del servizio di provisioning selezionare **Gestisci registrazioni**. Selezionare la scheda **Individual Enrollments** (Registrazioni singole) e fare clic sul pulsante **Aggiungi** in alto. Selezionare **X**.**509** come *meccanismo* di attestazione dell'identità e caricare il certificato foglia come richiesto dal pannello. Al termine, fare clic sul pulsante **Save** (Salva). 
   - X **.** 509 registrazione di gruppo: Nel pannello di riepilogo del servizio di provisioning selezionare **Gestisci registrazioni**. Selezionare la scheda **Group Enrollments** (Registrazioni di gruppo) e fare clic sul pulsante **Aggiungi** in alto. Selezionare **X**.**509** come *meccanismo* di attestazione dell'identità, immettere un nome di gruppo e un nome di certificato e caricare il certificato della CA/intermedio come richiesto dal pannello. Al termine, fare clic sul pulsante **Save** (Salva). 

## <a name="enable-authentication-for-devices-using-a-custom-attestation-mechanism-optional"></a>Abilitare l'autenticazione per i dispositivi usando un meccanismo di attestazione personalizzato (facoltativo)

> [!NOTE]
> Questa sezione è applicabile solo ai dispositivi che richiedono il supporto per una piattaforma o un meccanismo di attestazione personalizzati, non attualmente supportati da Device Provisioning Service Client SDK per C. Si noti anche che l'SDK usa frequentemente il termine "HSM" come sostituto generico di "meccanismo di attestazione".

È prima di tutto necessario sviluppare un repository e una libreria per il meccanismo di attestazione personalizzato:

1. Sviluppare una libreria per accedere al meccanismo di attestazione. Questo progetto richiede che si produca una raccolta statica ad uso dell'SDK del servizio Device Provisioning.

2. Implementare le funzioni definite nel file di intestazione seguente nella libreria: 

    - Per un TPM personalizzato: implementare le funzioni definite in [HSM TPM API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api).  
    - Per un X.509 personalizzato: implementare le funzioni definite in [HSM X509 API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api). 

Dopo la creazione della libreria, questa dovrà essere integrata con Device Provisioning Service Client SDK tramite un collegamento. :

1. Specificare il repository GitHub personalizzato e la libreria nel comando `cmake` seguente:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. Aprire il file della soluzione Visual Studio creato da CMake (`\azure-iot-sdk-c\cmake\azure_iot_sdks.sln`) e compilarlo. 

    - Il processo di compilazione crea la libreria SDK.
    - L'SDK tenta di eseguire il link alla libreria personalizzata definita nel comando `cmake`.

3. Eseguire l'app di esempio "prov_dev_client_ll_sample" in "Provision_Samples" (in `\azure-iot-sdk-c\cmake\provisioning_client\samples\prov_dev_client_ll_sample`) per verificare che il meccanismo di attestazione personalizzato sia implementato correttamente.

## <a name="connecting-to-iot-hub-after-provisioning"></a>Connessione all'hub IoT dopo il provisioning

Dopo il provisioning del dispositivo con il servizio di provisioning, questa API usa la modalità di autenticazione specificata (X **.** 509 o TPM) per connettersi all'hub IoT: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
