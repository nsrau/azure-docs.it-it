---
title: Registrare dispositivi X.509 nel servizio Device Provisioning di Azure con Python
description: Questa guida introduttiva usa registrazioni di gruppo. In questa guida di avvio rapido verrà eseguita la registrazione dei dispositivi X.509 nel servizio Device Provisioning in hub IoT di Azure con Python
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc, tracking-python
ms.openlocfilehash: ae851f5b02c0fc06f346195c5c3b8667284eb1d1
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84608962"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-python"></a>Guida introduttiva: Registrare i dispositivi X.509 nel servizio Device Provisioning con Python

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Questa guida di avvio rapido descrive come usare Python per creare un gruppo di registrazione a livello di codice che usa un certificato X.509 intermedio o CA radice. Un gruppo di registrazione controlla l'accesso al servizio di provisioning per i dispositivi che condividono un certificato di firma comune nella rispettiva catena di certificati. Il gruppo di registrazione viene creato usando Python Provisioning Service SDK e un'applicazione Python di esempio.

## <a name="prerequisites"></a>Prerequisiti

- Completamento dell'esercitazione [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md).
- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 2.x o 3.x](https://www.python.org/downloads/). Aggiungere Python alle variabili di ambiente specifiche della piattaforma. Questa guida di avvio rapido installa [Python Provisioning Service SDK](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client).
- [PIP](https://pip.pypa.io/en/stable/installing/), se non è incluso nella distribuzione di Python.
- [Git](https://git-scm.com/download/).

> [!IMPORTANT]
> Questo articolo si applica solo alla versione 1 dell'SDK per Python, ora deprecata. I client dispositivo e servizio per il servizio Device Provisioning in hub IoT non sono ancora disponibili nella versione 2. Il team si sta attualmente impegnando per offrire la parità delle funzionalità nella versione 2.

## <a name="prepare-test-certificates"></a>Preparare i certificati di test

Per questa guida introduttiva è necessario un file PEM o CER contenente la parte pubblica di un certificato X.509 intermedio o CA radice. Questo certificato deve essere caricato nel servizio di provisioning e verificato dal servizio.

Per altre informazioni sull'uso di un'infrastruttura a chiave pubblica (PKI, Public Key Infrastructure) basata su certificati X.509 con l'hub IoT di Azure e il servizio Device Provisioning, vedere [Panoramica della sicurezza dei certificati della CA X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview).

[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) contiene strumenti di test che consentono di creare una catena di certificati X.509, caricare un certificato intermedio o radice dalla catena ed eseguire la proof-of-possession con il servizio per verificare il certificato. I certificati creati con gli strumenti dell'SDK sono progettati per essere usati solo per **test di sviluppo**. Questi certificati **non devono essere usati nell'ambiente di produzione**. Contengono password hardcoded ("1234") che scadono dopo 30 giorni. Per informazioni su come ottenere certificati idonei all'uso nell'ambiente di produzione, vedere [Come ottenere un certificato della CA X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) nella documentazione dell'hub IoT di Azure.

Per usare gli strumenti di test per generare certificati, seguire questa procedura:

1. Trovare il nome del tag per la [versione più recente](https://github.com/Azure/azure-iot-sdk-c/releases/latest) di Azure IoT C SDK.

2. Aprire un prompt dei comandi o la shell Git Bash e passare a una cartella di lavoro nel computer in uso. Eseguire i comandi seguenti per clonare la versione più recente del repository GitHub [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Usare il tag trovato nel passaggio precedente come valore per il parametro `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Il completamento di questa operazione richiederà alcuni minuti.

   Gli strumenti di test si trovano nel percorso *azure-iot-sdk-c/tools/CACertificates* del repository clonato.

3. Seguire la procedura in [Gestione dei certificati CA di prova per esempi e certificazioni](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md). 

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

Il servizio Device Provisioning in Azure IoT supporta due tipi di registrazione:

- [Gruppi di registrazioni](concepts-service.md#enrollment-group): Usato per registrare più dispositivi correlati.
- [Registrazioni singole](concepts-service.md#individual-enrollment): Usato per registrare un singolo dispositivo.

La creazione di registrazioni singole tramite [Python Provisioning Service SDK](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client) sarà disponibile in futuro. Per altre informazioni, vedere [Controllo dell'accesso dei dispositivi al servizio di provisioning con certificati X.509](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

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
Se si prevede di esplorare l'esempio di servizio Java, non eseguire la pulizia delle risorse create in questa guida di avvio rapido. Se non si intende continuare, seguire questa procedura per eliminare tutte le risorse create in questa guida.

1. Chiudere la finestra di output di esempio di Java sul computer.
1. Chiudere la finestra del _generatore di certificati X509_  sul computer.
1. Passare al servizio Device Provisioning nel portale di Azure, selezionare **Gestisci registrazioni** e quindi la scheda **Gruppi di registrazioni**. Selezionare la casella di controllo accanto a *NOME GRUPPO* per i dispositivi X.509 registrati con questa guida di avvio rapido e fare clic sul pulsante **Elimina** nella parte superiore del riquadro.    


## <a name="next-steps"></a>Passaggi successivi
In questa guida di avvio rapido si è registrato un gruppo simulato di dispositivi X.509 nel servizio Device Provisioning. Per informazioni approfondite sul provisioning del dispositivo, passare all'esercitazione sulla configurazione del servizio Device Provisioning nel portale di Azure. 

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials (Esercitazioni sul servizio Device Provisioning in hub IoT di Azure)](./tutorial-set-up-cloud.md)
