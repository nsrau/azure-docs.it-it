---
title: Procedure di Azure - Come usare moduli di protezione hardware diversi con Device Provisioning Service Client SDK in Azure | Microsoft Docs
description: Procedure di Azure - Come usare moduli di protezione hardware diversi con Device Provisioning Service Client SDK in Azure
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 08/28/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: 
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 184bbdc0a6bef74d0e5ac79afe3858354c6b1695
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="how-to-use-different-hardware-security-modules-with-device-provisioning-service-client-sdk"></a>Come usare diversi moduli di protezione hardware con Device Provisioning Service Client SDK
Queste procedure illustrano come usare un [modulo di protezione hardware](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) diverso con Device Provisioning Service Client SDK in C con un dispositivo fisico e un simulatore.  Il servizio di provisioning supporta due modalità di autenticazione: X**.**509 e Trusted Platform Module (TPM).

## <a name="prerequisites"></a>Prerequisiti

Preparare l'ambiente di sviluppo in base alle indicazioni disponibili nella sezione "Preparare l'ambiente di sviluppo" nella Guida [Creare ed effettuare il provisioning di un dispositivo simulato] (./quick-create-simulated-device.md).

## <a name="enable-authentication-with-different-hsms"></a>Abilitare l'autenticazione con diversi moduli di protezione hardware

È necessario che la modalità di autenticazione (X**.**509 o TPM) sia abilitata per il dispositivo fisico o il simulatore prima che ne sia consentita la registrazione nel portale di Azure.  Passare alla cartella radice per azure-iot-sdk-c.  Eseguire il comando specificato in base alla modalità di autenticazione scelta.

### <a name="use-x509-with-simulator"></a>Usare X**.**509 con il simulatore

Il servizio di provisioning include un emulatore DICE (Device Identity Composition Engine) che genera un certificato X**.**509 per l'autenticazione del dispositivo.  Eseguire il comando seguente per abilitare l'autenticazione X**.**509:

```
cmake -Ddps_auth_type=x509 ..
```

Per informazioni sull'hardware con DICE, vedere [qui](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/).

### <a name="use-x509-with-hardware"></a>Usare X**.**509 con l'hardware

Il servizio di provisioning può essere usato con X**.**509 su altro hardware.  È necessaria un'interfaccia tra hardware e SDK per stabilire una connessione.  Per informazioni sull'interfaccia, contattare il produttore del modulo di protezione hardware.

### <a name="use-tpm"></a>Usare TPM

Il servizio di provisioning può connettersi a chip TPM di hardware Windows e Linux con un token di firma di accesso condiviso.  Eseguire il comando seguente per abilitare l'autenticazione TPM:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>Usare TPM con un simulatore

Se non si ha alcun dispositivo con chip TPM, è possibile usare un simulatore per finalità di sviluppo nel sistema operativo Windows.  Eseguire il comando seguente per abilitare l'autenticazione TPM ed eseguire il simulatore TPM:

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

## <a name="create-a-device-enrollment-entry-in-dps"></a>Creare una voce per la registrazione dei dispositivi nel servizio Device Provisioning

### <a name="tpm"></a>TPM
Se si usa TPM, seguire le istruzioni disponibili in ["Creare ed effettuare il provisioning di un dispositivo simulato usando il servizio Device Provisioning in hub IoT"](./quick-create-simulated-device.md) per creare una voce di registrazione dei dispositivi nel servizio Device Provisioning e simulare il primo avvio.

### <a name="x509"></a>X**.**509
1. Per registrare un dispositivo nel servizio di provisioning, è necessario annotare la chiave di verifica dell'autenticità e l'ID di registrazione per ogni dispositivo. Questi valori vengono visualizzati nello strumento di provisioning fornito dall'SDK client. Eseguire il comando seguente per stampare il certificato dell'Autorità di certificazione radice (per i gruppi di registrazione) e il certificato del firmatario (per la registrazione individuale):
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Accedere al portale di Azure, fare clic sul pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio Device Provisioning.
   - Registrazione individuale con X**.**509: nel pannello di riepilogo del servizio di provisioning selezionare **Manage enrollments** (Gestisci le registrazioni). Selezionare la scheda **Individual Enrollments** (Registrazioni singole) e fare clic sul pulsante **Aggiungi** in alto. Selezionare **X**.**509** come *Meccanismo* di attestazione dell'identità e caricare il certificato del firmatario come richiesto dal pannello. Al termine, fare clic sul pulsante **Save** (Salva). 
   - Registrazione di gruppo con X**.**509: nel pannello di riepilogo del servizio di provisioning selezionare **Manage enrollments** (Gestisci le registrazioni). Selezionare la scheda **Group Enrollments** (Registrazioni di gruppo) e fare clic sul pulsante **Aggiungi** in alto. Selezionare **X**.**509** come *Meccanismo* di attestazione dell'identità, immettere un nome di gruppo e un nome di certificato e caricare il certificato dell'Autorità di certificazione radice come richiesto dal pannello. Al termine, fare clic sul pulsante **Save** (Salva). 

## <a name="connecting-to-iot-hub-after-provisioning"></a>Connessione all'hub IoT dopo il provisioning

Dopo il provisioning del dispositivo con il servizio di provisioning, questa API usa la modalità di autenticazione con modulo di protezione hardware per connettersi all'hub IoT: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
