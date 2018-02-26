---
title: Registrare un dispositivo TPM nel servizio Azure Device Provisioning con Python | Microsoft Docs
description: Guida introduttiva di Azure - Registrare un dispositivo TPM nel servizio Device Provisioning in hub IoT di Azure con Python Provisioning Service SDK
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 01/26/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 941e6d53b136a3cfef368e436b31a3022f5223fa
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Registrare un dispositivo TPM nel servizio Device Provisioning in hub IoT con Python Provisioning Service SDK
[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Questi passaggi illustrano come creare una registrazione singola a livello di codice per un dispositivo TPM nel servizio Device Provisioning in hub IoT di Azure usando [Python Provisioning Service SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client) e un'applicazione Python di esempio. Anche se Python Provisioning Service SDK funziona in computer sia Windows che Linux, in questo articolo si usa un computer di sviluppo Windows per illustrare il processo di registrazione.

Assicurarsi di [configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md) prima di continuare.


<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Preparare l'ambiente 

1. Scaricare e installare [Python 2.x o 3.x](https://www.python.org/downloads/). Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Quando richiesto durante l'installazione, assicurarsi di aggiungere Python alle variabili di ambiente specifiche per la piattaforma. 

1. Scegliere una delle opzioni seguenti:

    - Creare e compilare **Azure IoT Python SDK**. Seguire [queste istruzioni](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) per compilare i pacchetti Python. Se si usa il sistema operativo Windows, installare anche [Visual C++ Redistributable Package](http://www.microsoft.com/download/confirmation.aspx?id=48145) per consentire l'uso di DLL native di Python.

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

1. Passare al servizio di provisioning nel portale di Azure. Fare clic su **Gestisci registrazioni**. Il dispositivo TPM viene visualizzato nella scheda **Registrazioni singole** con il nome `registrationid` assegnato in precedenza. 

    ![Verificare che la registrazione TPM nel portale sia riuscita](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>Pulire le risorse
Se si prevede di esplorare l'esempio di servizio Java, non eliminare le risorse create in questa guida introduttiva. Se non si prevede di continuare, usare i passaggi seguenti per eliminare tutte le risorse create da questa guida introduttiva.

1. Chiudere la finestra dell'output di esempio di Python nel computer.
1. Se è stato creato un dispositivo TPM simulato, chiudere la finestra del simulatore TPM.
1. Passare al servizio Device Provisioning nel portale di Azure, fare clic su **Gestisci registrazioni** e quindi selezionare la scheda **Registrazioni singole**. Selezionare l'*ID registrazione* della voce di registrazione creata usando questa guida introduttiva e fare clic sul pulsante **Elimina** nella parte superiore del pannello.  


## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stata creata a livello di codice una voce di registrazione singola per un dispositivo TPM e, facoltativamente, è stato creato un dispositivo TPM simulato nel computer e ne è stato effettuato il provisioning nell'hub IoT usando il servizio Device Provisioning in hub IoT di Azure. Per informazioni approfondite sul provisioning del dispositivo, passare all'esercitazione sulla configurazione del servizio Device Provisioning nel portale di Azure.

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials (Esercitazioni sul servizio Device Provisioning in hub IoT di Azure)](./tutorial-set-up-cloud.md)
