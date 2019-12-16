---
title: Registrare un dispositivo TPM nel servizio Azure Device Provisioning con Python
description: "Avvio rapido: registrare il dispositivo TPM nel servizio Device Provisioning in hub IoT di Azure con l'SDK per servizi di provisioning Python. Questa guida introduttiva usa registrazioni singole."
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 6266ef3479e74103d0989b8eb0286626da5eb28f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976792"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Guida introduttiva: Registrare un dispositivo TPM nel servizio Device Provisioning in hub IoT con Python Provisioning Service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Questa procedura illustra come creare una registrazione singola a livello di codice per un dispositivo TPM nel servizio Device Provisioning in hub IoT di Azure usando l'[SDK per servizi di provisioning Python versione 1](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client) e un'applicazione Python di esempio. Anche se Python Provisioning Service SDK funziona in computer sia Windows che Linux, in questo articolo si usa un computer di sviluppo Windows per illustrare il processo di registrazione.

> [!IMPORTANT]
> Questo articolo si applica solo alla versione 1 dell'SDK per Python, ora deprecata. I client dispositivo e servizio per il servizio Device Provisioning in hub IoT non sono ancora disponibili nella versione 2. Il team si sta attualmente impegnando per offrire la parità delle funzionalità nella versione 2.

Assicurarsi di [configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md) prima di continuare.


<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Preparare l'ambiente 

1. Scaricare e installare [Python 2.x o 3.x](https://www.python.org/downloads/). Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Quando richiesto durante l'installazione, assicurarsi di aggiungere Python alle variabili di ambiente specifiche per la piattaforma. 

1. Scegliere una delle opzioni seguenti:

    - Creare e compilare **Azure IoT Python SDK**. Seguire [queste istruzioni](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) per compilare i pacchetti Python. Se si usa il sistema operativo Windows, installare anche [Visual C++ Redistributable Package](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) per consentire l'uso di DLL native di Python.

    - [Installare o aggiornare *pip*, il sistema di gestione pacchetti Python](https://pip.pypa.io/en/stable/installing/). Installare il pacchetto con il comando seguente:

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. È necessaria la chiave di verifica dell'autenticità per il dispositivo. Se è stata seguita la procedura descritta nella guida introduttiva [Creare ed effettuare il provisioning di un dispositivo simulato](quick-create-simulated-device.md) per creare un dispositivo TPM simulato, usare la chiave creata per il dispositivo. In caso contrario è possibile usare la chiave di verifica dell'autenticità seguente fornita con l'SDK:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
    ```


## <a name="modify-the-python-sample-code"></a>Modificare il codice Python di esempio

Questa sezione illustra come aggiungere i dettagli del provisioning del dispositivo TPM al codice di esempio. 

1. Usando un editor di testo, creare un nuovo file **TpmEnrollment.py**.

1. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **TpmEnrollment.py**. Sostituire quindi `dpsConnectionString` con la stringa di connessione trovata in **Criteri di accesso condivisi** di **Servizio Device Provisioning** nel **portale di Azure**. Sostituire `endorsementKey` con il valore annotato in precedenza in [Preparare l'ambiente](quick-enroll-device-tpm-python.md#prepareenvironment). Creare infine un `registrationid` univoco e assicurarsi che sia costituito solo da caratteri alfanumerici minuscoli e trattini.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    ENDORSEMENT_KEY = "{endorsementKey}"

    REGISTRATION_ID = "{registrationid}"
    ```

1. Aggiungere la funzione e la chiamata di funzione seguenti per implementare la creazione della registrazione di gruppo:
   
    ```python
    def main():
        print ( "Starting individual enrollment..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)

        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)

        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

1. Salvare e chiudere il file **TpmEnrollment.py**.
 

## <a name="run-the-sample-tpm-enrollment"></a>Eseguire la registrazione TPM di esempio

1. Aprire un prompt dei comandi ed eseguire lo script.

    ```cmd/sh
    python TpmEnrollment.py
    ```

1. Verificare nell'output che la registrazione sia stata eseguita correttamente.

1. Passare al servizio di provisioning nel portale di Azure. Selezionare **Gestisci registrazioni**. Il dispositivo TPM viene visualizzato nella scheda **Registrazioni singole** con il nome `registrationid` assegnato in precedenza. 

    ![Verificare che la registrazione TPM nel portale sia riuscita](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>Pulire le risorse
Se si prevede di esplorare l'esempio di servizio Java, non eseguire la pulizia delle risorse create in questa guida di avvio rapido. Se non si intende continuare, seguire questa procedura per eliminare tutte le risorse create in questa guida.

1. Chiudere la finestra dell'output di esempio di Python nel computer.
1. Se è stato creato un dispositivo TPM simulato, chiudere la finestra del simulatore TPM.
1. Passare al servizio Device Provisioning nel portale di Azure e selezionare **Gestisci registrazioni** e quindi la scheda **Registrazioni singole**. Selezionare la casella di controllo accanto all'*ID registrazione* della voce di registrazione creata con questa guida di avvio rapido e fare clic sul pulsante **Elimina** nella parte superiore del riquadro.


## <a name="next-steps"></a>Passaggi successivi
In questa guida di avvio rapido si è creata a livello di codice una voce di registrazione singola per un dispositivo TPM e, facoltativamente, è stato creato un dispositivo simulato TPM nel computer e ne è stato effettuato il provisioning nell'hub IoT usando il servizio Device Provisioning in hub IoT di Azure. Per informazioni approfondite sul provisioning del dispositivo, passare all'esercitazione sulla configurazione del servizio Device Provisioning nel portale di Azure.

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials (Esercitazioni sul servizio Device Provisioning in hub IoT di Azure)](./tutorial-set-up-cloud.md)
