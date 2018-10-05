---
title: Questa guida introduttiva illustra come registrare i dispositivi X.509 nel servizio Device Provisioning di Azure con Python | Microsoft Docs
description: In questa guida introduttiva verrà eseguita la registrazione dei dispositivi X.509 nel servizio Device Provisioning in hub IoT di Azure con Python
author: wesmc7777
ms.author: wesmc
ms.date: 01/25/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 84ec165a78f873f6378e840d7f339cb7590dc098
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227385"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-python"></a>Guida introduttiva: Registrare i dispositivi X.509 nel servizio Device Provisioning con Python

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

I dispositivi vengono registrati in un'istanza del servizio di provisioning creando un [gruppo di registrazione](concepts-service.md#enrollment-group) o una [registrazione singola](concepts-service.md#individual-enrollment). Questa guida introduttiva illustra come usare Python per creare un [gruppo di registrazione](concepts-service.md#enrollment-group) a livello di codice che usa un certificato X.509 intermedio o CA radice. Un gruppo di registrazione controlla l'accesso al servizio di provisioning per i dispositivi che condividono un certificato di firma comune nella rispettiva catena di certificati. Il gruppo di registrazione viene creato usando [Python Provisioning Service SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client) e un'applicazione Python di esempio. La creazione di registrazioni singole tramite *Python Provisioning Service SDK* sarà disponibile in futuro. Per altre informazioni, vedere [Controllo dell'accesso dei dispositivi al servizio di provisioning con certificati X.509](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Per altre informazioni sull'uso di un'infrastruttura a chiave pubblica (PKI, Public Key Infrastructure) basata su certificati X.509 con l'hub IoT di Azure e il servizio Device Provisioning, vedere [Panoramica della sicurezza dei certificati della CA X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Questa guida introduttiva prevede che siano già stati creati un hub IoT e un'istanza del servizio Device Provisioning. Se queste risorse non sono state ancora create, completare la guida introduttiva [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md) prima di continuare con questo articolo.

Anche se i passaggi illustrati in questo articolo funzionano su computer sia Windows che Linux, l'articolo usa un computer di sviluppo Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prerequisiti

- Installare [Python 2.x o 3.x](https://www.python.org/downloads/). Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Quando richiesto durante l'installazione, assicurarsi di aggiungere Python alle variabili di ambiente specifiche per la piattaforma.
- [Installare o aggiornare *pip*, il sistema di gestione pacchetti Python](https://pip.pypa.io/en/stable/installing/).
- Installare [Git](https://git-scm.com/download/).



## <a name="prepare-test-certificates"></a>Preparare i certificati di test

Per questa guida introduttiva è necessario un file PEM o CER contenente la parte pubblica di un certificato X.509 intermedio o CA radice. Questo certificato deve essere caricato nel servizio di provisioning e verificato dal servizio. 

[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) contiene strumenti di test che consentono di creare una catena di certificati X.509, caricare un certificato intermedio o radice dalla catena ed eseguire la proof-of-possession con il servizio per verificare il certificato. I certificati creati con gli strumenti dell'SDK sono progettati per l'uso per il **solo test di sviluppo**. Questi certificati **non devono essere usati nell'ambiente di produzione**. Contengono password hardcoded ("1234") che scadono dopo 30 giorni. Per informazioni su come ottenere certificati idonei all'uso nell'ambiente di produzione, vedere [Come ottenere un certificato della CA X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) nella documentazione dell'hub IoT di Azure.

Per usare gli strumenti di test per generare certificati, seguire questa procedura: 
 
1. Aprire un prompt dei comandi o la shell Git Bash e passare a una cartella di lavoro nel computer in uso. Eseguire il comando seguente per clonare il repository GitHub [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):
    
  ```cmd/sh
  git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
  ```

  Le dimensioni di questo repository sono attualmente di circa 220 MB. Il completamento di questa operazione richiederà alcuni minuti.

  Gli strumenti di test si trovano nel percorso *azure-iot-sdk-c/tools/CACertificates* del repository clonato.    

2. Seguire la procedura in [Gestione dei certificati CA di prova per esempi e certificazioni](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md). 


## <a name="modify-the-python-sample-code"></a>Modificare il codice Python di esempio

Questa sezione illustra come aggiungere i dettagli del provisioning del dispositivo X.509 al codice di esempio. 

1. Usando un editor di testo, creare un nuovo file **EnrollmentGroup.py**.

1. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **EnrollmentGroup.py**. Sostituire quindi `dpsConnectionString` con la stringa di connessione trovata in **Criteri di accesso condivisi** di **Servizio Device Provisioning** nel **portale di Azure**. Sostituire il segnaposto del certificato con il certificato creato in precedenza in [Preparare i certificati di test](quick-enroll-device-x509-python.md#prepare-test-certificates). Creare infine un `registrationid` univoco e assicurarsi che sia costituito solo da caratteri alfanumerici minuscoli e trattini.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import EnrollmentGroup, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    SIGNING_CERT = """-----BEGIN CERTIFICATE-----
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    -----END CERTIFICATE-----"""

    GROUP_ID = "{registrationid}"
    ```

1. Aggiungere la funzione e la chiamata di funzione seguenti per implementare la creazione della registrazione di gruppo:
   
    ```python
    def main():
        print ( "Initiating enrollment group creation..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
        att = AttestationMechanism.create_with_x509_signing_certs(SIGNING_CERT)
        eg = EnrollmentGroup.create(GROUP_ID, att)

        eg = psc.create_or_update(eg)
    
        print ( "Enrollment group created." )

    if __name__ == '__main__':
        main()
    ```

1. Salvare e chiudere il file **EnrollmentGroup.py**.
 

## <a name="run-the-sample-group-enrollment"></a>Eseguire la registrazione del gruppo di esempio

1. Aprire un prompt dei comandi ed eseguire il comando seguente per installare [azure-iot-provisioning-device-client](https://pypi.org/project/azure-iot-provisioning-device-client).

    ```cmd/sh
    pip install azure-iothub-provisioningserviceclient    
    ```

2. Aprire un prompt dei comandi ed eseguire lo script.

    ```cmd/sh
    python EnrollmentGroup.py
    ```

3. Verificare nell'output che la registrazione sia stata eseguita correttamente.

4. Passare al servizio di provisioning nel portale di Azure. Fare clic su **Gestisci registrazioni**. Si noti che il gruppo di dispositivi X.509 viene visualizzato nella scheda **Gruppi di registrazioni** con il nome `registrationid` assegnato in precedenza. 

    ![Verificare nel portale che la registrazione X.509 sia riuscita](./media/quick-enroll-device-x509-python/1.png)  


## <a name="clean-up-resources"></a>Pulire le risorse
Se si prevede di esplorare l'esempio di servizio Java, non eliminare le risorse create in questa guida introduttiva. Se non si prevede di continuare, usare i passaggi seguenti per eliminare tutte le risorse create da questa guida introduttiva.

1. Chiudere la finestra di output di esempio di Java sul computer.
1. Chiudere la finestra del _generatore di certificati X509_  sul computer.
1. Passare al servizio Device Provisioning nel portale di Azure, fare clic su **Gestisci registrazioni** e quindi selezionare la scheda **Gruppi di registrazioni**. Selezionare il *NOME GRUPPO* dei dispositivi X.509 registrati usando questa guida introduttiva e fare clic sul pulsante **Elimina** nella parte superiore del pannello.  


## <a name="next-steps"></a>Passaggi successivi
In questa introduttiva si è registrato un gruppo simulato di dispositivi X.509 nel servizio Device Provisioning. Per informazioni approfondite sul provisioning del dispositivo, passare all'esercitazione sulla configurazione del servizio Device Provisioning nel portale di Azure. 

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials (Esercitazioni sul servizio Device Provisioning in hub IoT di Azure)](./tutorial-set-up-cloud.md)
