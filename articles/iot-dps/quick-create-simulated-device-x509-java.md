---
title: Effettuare il provisioning di un dispositivo X.509 simulato nell'hub IoT di Azure usando Java | Microsoft Docs
description: Guida introduttiva di Azure - Creare ed effettuare il provisioning di un dispositivo simulato X.509 usando l'SDK per dispositivi Java per il servizio Device Provisioning in hub IoT
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 7e4ad361df8a37d4a82c1bc50c6fb134a1ad5159
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Creare ed effettuare il provisioning di un dispositivo simulato X.509 usando l'SDK per dispositivi Java per il servizio Device Provisioning in hub IoT
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Questi passaggi illustrano come simulare un dispositivo X.509 in un computer di sviluppo con sistema operativo Windows e usare un esempio di codice per connettere il dispositivo simulato con il servizio Device Provisioning e l'hub IoT. 

Assicurarsi di completare la procedura descritta in [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md) prima di continuare.


## <a name="prepare-the-environment"></a>Preparare l'ambiente 

1. Verificare che [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) sia installato nel computer.

1. Scaricare e installare [Maven](https://maven.apache.org/install.html).

1. Verificare che `git` sia installato nel computer e venga aggiunto alle variabili di ambiente accessibili alla finestra di comando. Vedere gli [strumenti client Git di Software Freedom Conservancy](https://git-scm.com/download/) per la versione più recente degli strumenti `git` da installare, tra cui **Git Bash**, l'app da riga di comando che è possibile usare per interagire con il repository Git locale. 

1. Aprire un prompt dei comandi. Clonare il repository GitHub per l'esempio di codice di simulazione del dispositivo:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. Passare al progetto di generazione del certificato e compilare il progetto. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

1. Passare alla cartella di destinazione ed eseguire il file JAR creato.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

1. Creare le informazioni di registrazione in uno dei modi seguenti, a seconda della configurazione:

    - **Registrazione singola**:

        1. Immettere **N** per _Do you want to input common name_ (Immettere un nome comune?) Copiare l'output di `Client Cert` da *-----BEGIN CERTIFICATE-----* a *-----END CERTIFICATE-----*.

            ![Generatore di certificato singolo](./media/java-quick-create-simulated-device-x509/individual.png)

        1. Creare un file denominato **_X509individual.pem_** nel computer Windows, aprirlo in un editor di propria scelta e copiare il contenuto degli Appunti in questo file. Salvare il file.

        1. Immettere **N** per _Do you want to input Verification Code_ (Inserire codice di verifica?) e tenere aperto l'output del programma per farvi riferimento più avanti nella guida introduttiva. Si notino i valori _Client Cert_ (Certificato client) e _Client Cert Private Key_ (Chiave privata certificato client).
    
    - **Gruppi di registrazioni**:

        1. Immettere **N** per _Do you want to input common name_ (Immettere un nome comune?) Copiare l'output di `Root Cert` da *-----BEGIN CERTIFICATE-----* a *-----END CERTIFICATE-----*.

            ![Generatore di certificato di gruppo](./media/java-quick-create-simulated-device-x509/group.png)

        1. Creare un file denominato **_X509group.pem_** nel computer Windows, aprirlo in un editor di propria scelta e copiare il contenuto degli Appunti in questo file. Salvare il file.

        1. Immettere **Y** per _Do you want to input Verification Code_ (Inserire codice di verifica?) e tenere aperto il programma per usarlo più avanti nella guida introduttiva. Si notino i valori _Client Cert_ (Certificato client), _Client Cert Private Key_ (Chiave privata certificato client), _Signer Cert_ (Certificato firmatario) e _Root Cert_ (Certificato radice).

        > [!NOTE]
        > Il valore `Root Cert` è applicabile solo ai certificati creati nell'output della console e non può essere usato per la firma di certificati client aggiuntivi. Se è necessario un set più affidabile di certificati di test, vedere l'[esempio di gestione dei certificati dell'autorità di certificazione](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).
        >

## <a name="create-a-device-enrollment-entry"></a>Creare una voce per la registrazione dei dispositivi

1. Accedere al portale di Azure, fare clic sul pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio di provisioning.

1. Immettere le informazioni di registrazione in uno dei modi seguenti, a seconda della configurazione:

    - **Registrazione singola**: 

        1. Nel pannello di riepilogo del servizio Device Provisioning selezionare **Manage enrollments** (Gestisci registrazioni). Selezionare la scheda **Individual Enrollments** (Registrazioni singole) e fare clic sul pulsante **Aggiungi** in alto. 

        1. In **Add enrollment list entry** (Aggiungi voce elenco di registrazione) immettere le informazioni seguenti:
            - Selezionare **X.509** come *meccanismo* di attestazione dell'identità.
            - In *File di certificato con estensione pem o cer* selezionare il file di certificato **_X509individual.pem_** creato nei passaggi precedenti con il widget *Esplora file*.
            - Facoltativamente, è possibile specificare le informazioni seguenti:
                - Selezionare un hub IoT collegato al servizio di provisioning.
                - Immettere un ID dispositivo univoco. Assicurarsi di non usare dati sensibili quando si assegna un nome al dispositivo. 
                - Aggiornare lo **stato iniziale del dispositivo gemello** con la configurazione iniziale desiderata per il dispositivo.
            - Al termine, fare clic sul pulsante **Save** (Salva). 

        ![Immettere le informazioni di registrazione del dispositivo X.509 nel pannello del portale](./media/java-quick-create-simulated-device-x509/enter-device-enrollment.png)  

       Al termine della registrazione, il dispositivo X.509 verrà visualizzato come **microsoftriotcore** nella colonna *ID registrazione* della scheda *Registrazioni singole*. 

    - **Gruppi di registrazioni**: 

        1. Nel pannello di riepilogo del servizio Device Provisioning selezionare **Certificati** e fare clic sul pulsante **Aggiungi** nella parte superiore.

        1. In **Aggiungi certificato** immettere le informazioni seguenti:
            - Immettere un nome certificato univoco.
            - Selezionare il file **_X509group.pem_** creato in precedenza.
            - Al termine, fare clic sul pulsante **Save** (Salva).

        ![Aggiungere il certificato](./media/java-quick-create-simulated-device-x509/add-certificate.png)

        1. Selezionare il certificato appena creato:
            - Fare clic su **Genera codice di verifica**. Copiare il codice generato.
            - Immettere il _codice di verifica_ o fare clic con il pulsante destro del mouse per incollarlo nella finestra _provisioning-x509-cert-generator_.  Premere **Invio**.
            - Copiare l'output di `Verification Cert` da *-----BEGIN CERTIFICATE-----* a *-----END CERTIFICATE-----*.
            
                ![Generatore di convalida](./media/java-quick-create-simulated-device-x509/validation-generator.png)

            - Creare un file denominato **_X509validation.pem_** nel computer Windows, aprirlo in un editor di propria scelta e copiare il contenuto degli Appunti in questo file. Salvare il file.
            - Selezionare il file **_X509validation.pem_** nel portale di Azure. Fare clic su **Verifica**.

            ![Convalidare il certificato](./media/java-quick-create-simulated-device-x509/validate-certificate.png)

        1. Selezionare **Gestisci registrazioni**. Selezionare **Gruppi di registrazioni** e fare clic sul pulsante **Aggiungi** nella parte superiore.
            - Immettere un nome gruppo univoco.
            - Selezionare il nome di certificato univoco creato in precedenza
            - Facoltativamente, è possibile specificare le informazioni seguenti:
                - Selezionare un hub IoT collegato al servizio di provisioning.
                - Aggiornare lo **stato iniziale del dispositivo gemello** con la configurazione iniziale desiderata per il dispositivo.

        ![Immettere le informazioni di registrazione del gruppo X.509 nel pannello del portale](./media/java-quick-create-simulated-device-x509/enter-group-enrollment.png)

        Al termine della registrazione, il gruppo di dispositivi X.509 viene visualizzato nella colonna *Nome gruppo* della scheda *Gruppi di registrazioni*.


## <a name="simulate-the-device"></a>Simulare il dispositivo

1. Nel pannello di riepilogo del servizio Device Provisioning selezionare **Panoramica** e prendere nota dell'_ambito ID_ e dell'_endpoint globale del servizio di provisioning_.

    ![Informazioni sul servizio](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

1. Aprire un prompt dei comandi. Passare alla cartella del progetto di esempio.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Immettere le informazioni di registrazione in uno dei modi seguenti, a seconda della configurazione:

    - **Registrazione singola**: 

        1. Modificare `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` per poter includere l'_ambito ID_ e l'_endpoint globale del servizio di provisioning_ annotati prima. Includere anche i valori _Client Cert_ (Certificato client) e _Client Cert Private Key_ (Chiave privata certificato client) annotati in precedenza.

            ```java
            private static final String idScope = "[Your ID scope here]";
            private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
            private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
            private static final String leafPublicPem = "<Your Public PEM Certificate here>";
            private static final String leafPrivateKey = "<Your Private PEM Key here>";
            ```

            - Usare il formato seguente per specificare il certificato e la chiave:
            
                ```java
                private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXX\n" +
                    "-----END PRIVATE KEY-----\n";
                ```

    - **Gruppi di registrazioni**: 

        1. Seguire le istruzioni per la **registrazione singola** descritte in precedenza.

        1. Aggiungere le righe di codice seguenti all'inizio della funzione `main`.
        
            ```java
            String intermediatePem = "<Your Signer Certificate here>";          
            String rootPem = "<Your Root Certificate here>";
                
            signerCertificates.add(intermediatePem);
            signerCertificates.add(rootPem);
            ```
    
            - Usare il formato seguente per specificare i certificati:
        
                ```java
                String intermediatePem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                String rootPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                ```

1. Compilare l'esempio. Passare alla cartella di destinazione ed eseguire il file JAR creato.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

1. Nel portale passare all'hub IoT collegato al servizio di provisioning e aprire il pannello **Esplora dispositivi**. Al termine del provisioning del dispositivo X.509 simulato nell'hub, il relativo ID dispositivo verrà visualizzato nel pannello **Esplora dispositivi** con *STATO* **abilitato**. Si noti che potrebbe essere necessario fare clic sul pulsante **Aggiorna** in alto, se il pannello è stato aperto prima dell'esecuzione dell'applicazione del dispositivo di esempio. 

    ![Il dispositivo viene registrato con l'hub IoT](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> Se si è modificato lo *stato iniziale del dispositivo gemello* rispetto al valore predefinito della voce di registrazione del dispositivo, è possibile eseguire il pull dello stato del dispositivo desiderato dall'hub e agire di conseguenza. Per altre informazioni, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare ed esplorare l'esempio di client del dispositivo, non pulire le risorse create in questa guida introduttiva. Se non si prevede di continuare, usare i passaggi seguenti per eliminare tutte le risorse create da questa guida introduttiva.

1. Chiudere la finestra di output di esempio di client del dispositivo sul computer.
1. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare il servizio Device Provisioning. Aprire il pannello **Gestisci registrazioni** per il servizio, quindi fare clic sulla scheda **Registrazioni singole**. Selezionare l'*ID registrazione* del dispositivo registrato in questa guida introduttiva e fare clic sul pulsante **Elimina** nella parte superiore. 
1. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare l'hub IoT. Aprire il pannello **Dispositivi IoT** per l'hub, selezionare l'*ID* del dispositivo registrato in questa guida introduttiva e quindi fare clic su **Elimina** nella parte superiore.


## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un dispositivo X.509 simulato nel computer Windows e ne è stato effettuato il provisioning nell'hub IoT usando il servizio Device Provisioning in hub IoT di Azure nel portale. Per informazioni su come registrare il dispositivo X.509 a livello di codice, passare alla guida introduttiva per la registrazione a livello di codice dei dispositivi X.509. 

> [!div class="nextstepaction"]
> [Guida introduttiva di Azure - Registrare dispositivi X.509 nel servizio Device Provisioning in hub IoT di Azure](quick-enroll-device-x509-java.md)
