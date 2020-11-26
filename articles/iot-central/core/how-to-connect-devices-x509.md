---
title: Connettere i dispositivi con certificati X. 509 in un'applicazione IoT Central di Azure
description: Come connettere i dispositivi con certificati X. 509 usando Node.js SDK per dispositivi per IoT Central applicazione
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: d36cf2344891bb70ab5499e77699b111429a936b
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96121833"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>Come connettere i dispositivi con certificati X. 509 usando Node.js SDK per dispositivi per IoT Central applicazione

IoT Central supporta sia le firme di accesso condiviso (SAS) sia i certificati X. 509 per proteggere la comunicazione tra un dispositivo e l'applicazione. L'esercitazione [creare e connettere un'applicazione client all'applicazione Azure IOT Central](./tutorial-connect-device.md) usa la firma di accesso condiviso. In questo articolo si apprenderà come modificare l'esempio di codice per usare X. 509.  Negli ambienti di produzione è consigliabile usare i certificati X.509. Per altre informazioni, vedere [connettersi ad Azure IOT Central](./concepts-get-connected.md).

Questo articolo illustra due modi per usare le [registrazioni di gruppo](how-to-connect-devices-x509.md#use-a-group-enrollment) X. 509 utilizzate in genere in un ambiente di produzione e le [registrazioni individuali](how-to-connect-devices-x509.md#use-an-individual-enrollment) utili per i test.

## <a name="prerequisites"></a>Prerequisiti

- Completamento dell'esercitazione [creare e connettere un'applicazione client all'applicazione Azure IOT Central (JavaScript)](./tutorial-connect-device.md) .
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

1. Passare allo script del generatore di certificati e installare i pacchetti necessari:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Creare un certificato radice e quindi derivare un certificato del dispositivo eseguendo lo script:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > Un ID dispositivo può contenere lettere, numeri e il carattere `-`.

Questi comandi producono tre file ognuno per la radice e il certificato del dispositivo

filename | sommario
-------- | --------
\<name\>_cert. pem | Parte pubblica del certificato X509
\<name\>_key. pem | Chiave privata per il certificato X509
\<name\>_fullchain. pem | L'intero Keychain per il certificato X509.

## <a name="create-a-group-enrollment"></a>Creare una registrazione di gruppo

1. Aprire l'applicazione IoT Central e passare ad **Amministrazione**  nel riquadro sinistro e selezionare **connessione del dispositivo**.

1. Selezionare **+ Crea gruppo di registrazione** e creare un nuovo gruppo di registrazione denominato _MyX509Group_ con un tipo di attestazione di **certificati (X. 509)**.

1. Aprire il gruppo di registrazioni creato e selezionare **Gestisci primario**.

1. Selezionare l'opzione file e caricare il file del certificato radice denominato _mytestrootcert_cert. pem_ generato in precedenza:

    ![Caricamento del certificato](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Per completare la verifica, generare il codice di verifica, copiarlo e usarlo per creare un certificato di verifica X. 509 al prompt dei comandi:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Caricare il certificato di verifica firmato _verification_cert. pem_ per completare la verifica:

    ![Certificato verificato](./media/how-to-connect-devices-x509/verified.png)

È ora possibile connettere i dispositivi che hanno un certificato X. 509 derivato da questo certificato radice primario.

Dopo aver salvato il gruppo di registrazione, prendere nota dell'ambito ID.

## <a name="run-sample-device-code"></a>Eseguire codice del dispositivo di esempio

1. Copiare i file **sampleDevice01_key. pem** e **sampleDevice01_cert. pem** nella cartella Azure-Internet-NET- _SDK-node/Device/Samples/pnp_ che contiene l'applicazione **simple_thermostat.js** . Questa applicazione è stata usata quando è stata completata l' [esercitazione connettere un dispositivo (JavaScript)](./tutorial-connect-device.md).

1. Passare alla cartella _Azure-cose-SDK-node/Device/Samples/PNP_ che contiene l'applicazione **simple_thermostat.js** ed eseguire il comando seguente per installare il pacchetto X. 509:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Aprire il file **simple_thermostat.js** in un editor di testo.

1. Modificare le `require` istruzioni in modo da includere quanto segue:

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Aggiungere le quattro righe seguenti alla sezione "informazioni di connessione DPS" per inizializzare la `deviceCert` variabile:

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. Modificare la `provisionDevice` funzione che crea il client sostituendo la prima riga con la seguente:

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. Nella stessa funzione modificare la riga che imposta la `deviceConnectionString` variabile come indicato di seguito:

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. Nella `main` funzione aggiungere la riga seguente dopo la riga che chiama `Client.fromConnectionString` :

    ```javascript
    client.setOptions(deviceCert);
    ```

1. Nell'ambiente della shell, impostare le due variabili di ambiente seguenti:

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=sampleDevice01_key.pem
    ```

    > [!TIP]
    > È possibile impostare le altre variabili di ambiente necessarie dopo aver completato l'esercitazione [creare e connettere un'applicazione client all'applicazione Azure IOT Central](./tutorial-connect-device.md) .

1. Eseguire lo script e verificare che il provisioning del dispositivo sia stato eseguito correttamente:

    ```cmd/sh
    node simple_thermostat.js
    ```

    È anche possibile verificare che i dati di telemetria siano visualizzati nel dashboard.

    ![Verificare la telemetria del dispositivo](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>Usare una registrazione singola

Usare i certificati X. 509 con una registrazione singola per testare il dispositivo e la soluzione. In una registrazione singola non è presente alcun certificato X. 509 radice o intermedio nell'applicazione IoT Central. I dispositivi usano un certificato X. 509 autofirmato per connettersi all'applicazione.

## <a name="generate-self-signed-device-cert"></a>Genera certificato dispositivo autofirmato

In questa sezione si userà un certificato X. 509 autofirmato per connettere i dispositivi per la registrazione singola, che vengono usati per registrare un singolo dispositivo. I certificati autofirmati sono destinati solo ai test.

Creare un certificato del dispositivo X. 509 autofirmato eseguendo lo script. Assicurarsi di usare solo caratteri alfanumerici minuscoli e trattini per il nome del certificato:

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>Crea registrazione singola

1. Nell'applicazione IoT Central di Azure selezionare **dispositivi** e creare un nuovo dispositivo con **ID dispositivo** come _mytestselfcertprimary_ dal modello di dispositivo termostato. Prendere nota dell' **ambito ID**, che verrà usato in un secondo momento.

1. Aprire il dispositivo creato e selezionare **Connetti**.

1. Selezionare le **registrazioni individuali** come **metodo Connect** e **certificati (X. 509)** come meccanismo:

    ![Registrazione individuale](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Selezionare l'opzione file in primario e caricare il file del certificato denominato _mytestselfcertprimary_cert. pem_ generato in precedenza.

1. Selezionare l'opzione file per il certificato secondario e caricare il file del certificato denominato _mytestselfcertsecondary_cert. pem._ Quindi selezionare **Salva**:

    ![Caricamento del certificato di registrazione singolo](./media/how-to-connect-devices-x509/individual-enrollment.png)

Il dispositivo è ora sottoposta a provisioning con certificato X. 509.

## <a name="run-a-sample-individual-enrollment-device"></a>Eseguire un dispositivo di registrazione singolo di esempio

1. Copiare i file _mytestselfcertprimary_key. pem_ e _mytestselfcertprimary_cert. pem_ nella cartella Azure-Internet-NET- _SDK-node/Device/Samples/pnp_ che contiene l'applicazione **simple_thermostat.js** . Questa applicazione è stata usata quando è stata completata l' [esercitazione connettere un dispositivo (JavaScript)](./tutorial-connect-device.md).

1. Modificare le variabili di ambiente usate in come segue:

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. Eseguire lo script e verificare che il provisioning del dispositivo sia stato eseguito correttamente:

    ```cmd/sh
    node environmentalSensor.js
    ```

    È anche possibile verificare che i dati di telemetria siano visualizzati nel dashboard.

    ![Registrazione singola di telemetria](./media/how-to-connect-devices-x509/telemetry-primary.png)

È anche possibile ripetere i passaggi precedenti per il certificato _mytestselfcertsecondary_ .

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere i dispositivi usando i certificati X. 509, il passaggio successivo suggerito consiste nell'apprendere come [monitorare la connettività dei dispositivi con l'interfaccia](howto-monitor-devices-azure-cli.md) della riga di comando di Azure
