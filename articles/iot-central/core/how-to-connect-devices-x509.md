---
title: Connettere i dispositivi con certificati X. 509 in un'applicazione IoT Central di Azure
description: Come connettere i dispositivi con certificati X. 509 usando Node.js SDK per dispositivi per IoT Central applicazione
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6de711567e87bcdd1e58185f90264d0c9aecdfde
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343871"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>Come connettere i dispositivi con certificati X. 509 usando Node.js SDK per dispositivi per IoT Central applicazione

IoT Central supporta sia le firme di accesso condiviso (SAS) sia i certificati X. 509 per proteggere la comunicazione tra un dispositivo e l'applicazione. L'esercitazione [creare e connettere un'applicazione client all'applicazione Azure IOT Central](./tutorial-connect-device-nodejs.md) usa la firma di accesso condiviso. In questo articolo si apprenderà come modificare l'esempio di codice per usare X. 509.  Negli ambienti di produzione è consigliabile usare i certificati X.509. Per altre informazioni, vedere [connettersi ad Azure IOT Central](./concepts-get-connected.md).

Questo articolo illustra due modi per usare le [registrazioni di gruppo](how-to-connect-devices-x509.md#use-a-group-enrollment) X. 509 utilizzate in genere in un ambiente di produzione e le [registrazioni individuali](how-to-connect-devices-x509.md#use-an-individual-enrollment) utili per i test.

## <a name="prerequisites"></a>Prerequisiti

- Completamento dell'esercitazione [creare e connettere un'applicazione client all'applicazione Azure IOT Central (Node.js)](./tutorial-connect-device-nodejs.md) .
- [Git](https://git-scm.com/download/).
- Scaricare e installare [openssl](https://www.openssl.org/). Se si usa Windows, è possibile usare i file binari della [pagina OpenSSL su SourceForge](https://sourceforge.net/projects/openssl/).

## <a name="use-a-group-enrollment"></a>Usare una registrazione di gruppo

Usare i certificati X. 509 con una registrazione di gruppo in un ambiente di produzione. In una registrazione di gruppo si aggiunge un certificato X. 509 radice o intermedio all'applicazione IoT Central. I dispositivi con certificati foglia derivati dal certificato radice o intermedio possono connettersi all'applicazione.


## <a name="generate-root-and-device-cert"></a>Genera certificato radice e dispositivo

In questa sezione si userà un certificato X. 509 per connettere un dispositivo a un certificato derivato dal certificato del gruppo di registrazione, che può connettersi all'applicazione IoT Central.

> [!WARNING]
> Questo modo per generare certificati X. 509 è solo per i test. Per un ambiente di produzione è necessario usare il meccanismo ufficiale sicuro per la generazione di certificati.

1. Aprire un prompt dei comandi. Clonare il repository GitHub per gli script di generazione del certificato:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

2. Passare allo script del generatore di certificati e installare i pacchetti necessari:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

3. Creare un certificato radice e quindi derivare un certificato del dispositivo eseguendo lo script. Assicurarsi di usare solo caratteri alfanumerici minuscoli e trattini per il nome del certificato.

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device mytestdevice mytestrootcert
    ```

Questa operazione produrrà tre file per la radice e il certificato del dispositivo

filename | sommario
-------- | --------
\<name\>_cert. pem | Parte pubblica del certificato X509
\<name\>_key. pem | Chiave privata per il certificato X509
\<name\>_fullchain. pem | L'intero Keychain per il certificato X509.


## <a name="create-a-group-enrollment"></a>Creare una registrazione di gruppo


1. Aprire ora l'applicazione IoT Central e passare ad **Amministrazione**  nel riquadro sinistro e fare clic su **connessione dispositivo**. 

2. Selezionare + **Crea gruppo di registrazione**e creare un nuovo gruppo di registrazione denominato _MyX509Group_ con un tipo di attestazione di **certificati (X. 509)**:


3. Aprire il gruppo di registrazioni creato e fare clic su **Gestisci primario**. 

4. Selezionare l'opzione file e caricare il file del certificato radice denominato _mytestrootcert_cert. pem_ generato in precedenza:


    ![Caricamento del certificato](./media/how-to-connect-devices-x509/certificate-upload.png)



5. Per completare la verifica, copiare il codice di verifica e creare un certificato di verifica X. 509 con tale codice nel prompt dei comandi.

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

6. Caricare il certificato di verifica firmato _verification_cert. pem_ per completare la verifica.

    ![Certificato verificato](./media/how-to-connect-devices-x509/verified.png)


È ora possibile connettere i dispositivi che hanno un certificato X. 509 derivato da questo certificato radice primario. Dopo aver salvato il gruppo di registrazione, prendere nota dell'ambito ID.


## <a name="run-sample-device-code"></a>Eseguire codice del dispositivo di esempio


1. Nell'applicazione IoT Central di Azure fare clic su **dispositivi**e creare un nuovo dispositivo con _mytestdevice_ come **ID del dispositivo** dal modello di dispositivo del sensore ambientale.


2. Copiare il _mytestdevice_key. pem_ e _mytestdevice_cert. pem_ nella cartella che contiene l'applicazione _environmentalSensor.js_ al termine dell' [esercitazione connettere un dispositivo (Node.js)](./tutorial-connect-device-nodejs.md).

3. Passare alla cartella che contiene l'applicazione environmentalSensor.js ed eseguire il comando seguente per installare il pacchetto X. 509:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

4. Modificare il file di **environmentalSensor.js** .
    - Sostituire il `idScope` valore con l' **ambito ID** di cui si è preso nota in precedenza 
    - Sostituire `registrationId` value con `mytestdevice` .

5. Modificare le `require` istruzioni come indicato di seguito:

    ```javascript
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    var fs = require('fs');
    var X509Security = require('azure-iot-security-x509').X509Security;
    ```

6. Modificare la sezione che crea il client come segue:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var deviceCert = {
      cert: fs.readFileSync('mytestdevice_cert.pem').toString(),
      key: fs.readFileSync('mytestdevice_key.pem').toString()
    };
    var provisioningSecurityClient = new X509Security(registrationId, deviceCert);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    ```

7. Modificare la sezione che apre la connessione come indicato di seguito:

   ```javascript
    var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
    hubClient.setOptions(deviceCert);
    ```

8. Eseguire lo script e verificare che il provisioning del dispositivo sia stato eseguito correttamente.

    ```cmd/sh
    node environmentalSensor.js
    ```   

    È anche possibile verificare che i dati di telemetria siano visualizzati nel dashboard.

    ![Verificare la telemetria del dispositivo](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>Usare una registrazione singola

Usare i certificati X. 509 con una registrazione singola per testare il dispositivo e la soluzione. In una registrazione singola non è presente alcun certificato X. 509 radice o intermedio nell'applicazione IoT Central. I dispositivi usano un certificato X. 509 autofirmato per connettersi all'applicazione.

## <a name="generate-self-signed-device-cert"></a>Genera certificato dispositivo autofirmato


In questa sezione si userà un certificato X. 509 autofirmato per connettere i dispositivi per la registrazione singola, che vengono usati per registrare un singolo dispositivo. I certificati autofirmati sono destinati solo ai test.

Creare un certificato del dispositivo X. 509 autofirmato eseguendo lo script. Assicurarsi di usare solo caratteri alfanumerici minuscoli e trattini per il nome del certificato.

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>Crea registrazione singola

1. Nell'applicazione IoT Central di Azure selezionare **dispositivi**e creare un nuovo dispositivo con **ID dispositivo** come _Mytestselfcertprimary_ dal modello di dispositivo sensore ambientale. Annotare l' **ambito ID**

2. Aprire il dispositivo creato e selezionare **Connetti** .

3. Selezionare le **registrazioni individuali** come meccanismo di connessione e **certificati (X. 509)** come meccanismo.

    ![Registrazione individuale](./media/how-to-connect-devices-x509/individual-device-connect.png)


4. Selezionare l'opzione file in primario e caricare il file del certificato denominato _mytestselfcertprimary_cert. pem_ generato in precedenza. 

5. Selezionare l'opzione file per il certificato secondario e caricare il file del certificato denominato _mytestselfcertsecondary_cert. pem._ Quindi selezionare **Salva**

    ![Caricamento del certificato di registrazione singolo](./media/how-to-connect-devices-x509/individual-enrollment.png)

Il dispositivo è ora sottoposta a provisioning con certificato X. 509.



## <a name="run-a-sample-individual-enrollment-device"></a>Eseguire un dispositivo di registrazione singolo di esempio

1. Copiare il _mytestselfcertprimary_key. pem_ e _mytestselfcertprimary_cert. pem_nella cartella che contiene l'applicazione environmentalSensor.js al termine dell' [esercitazione connettere un dispositivo (Node.js)](./tutorial-connect-device-nodejs.md).


2. Modificare il file di **environmentalSensor.js** come segue e salvarlo.
    - Sostituire il `idScope` valore con l' **ambito ID** di cui si è preso nota in precedenza.
    - Sostituire `registrationId` value con `mytestselfcertprimary` .
    - Sostituisci **var deviceCert** come:
    ```cmd\sh
    var deviceCert = {
    cert: fs.readFileSync('mytestselfcertprimary_cert.pem').toString(),
    key: fs.readFileSync('mytestselfcertprimary_key.pem').toString()
    };
    ```

3. Eseguire lo script e verificare che il provisioning del dispositivo sia stato eseguito correttamente.

    ```cmd/sh
    node environmentalSensor.js
    ```   

    È anche possibile verificare che i dati di telemetria siano visualizzati nel dashboard.

    ![Registrazione singola di telemetria](./media/how-to-connect-devices-x509/telemetry-primary.png)

È anche possibile ripetere i passaggi precedenti per il certificato _mytestselfcertsecondary_ .

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere i dispositivi usando i certificati X. 509, il passaggio successivo suggerito consiste nell'apprendere come [monitorare la connettività dei dispositivi con l'interfaccia](howto-monitor-devices-azure-cli.md) della riga di comando di Azure

