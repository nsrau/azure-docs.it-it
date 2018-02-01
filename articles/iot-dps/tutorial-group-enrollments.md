---
title: Effettuare il provisioning di un dispositivo X.509 simulato nell'hub IoT di Azure usando Java e i gruppi di registrazioni | Microsoft Docs
description: Esercitazione di Azure - Creare ed effettuare il provisioning di un dispositivo X.509 simulato usando un dispositivo Java, un SDK del servizio e gruppi di registrazioni per il servizio Device Provisioning in hub IoT
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 01/04/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 14e5e7613fd5df650625cf8997d569b754ceb689
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>Creare ed effettuare il provisioning di un dispositivo X.509 simulato usando un dispositivo Java, un SDK del servizio e gruppi di registrazioni per il servizio Device Provisioning in hub IoT

Questi passaggi illustrano come simulare un dispositivo X.509 in un computer di sviluppo con sistema operativo Windows e usare un esempio di codice per connettere il dispositivo simulato con il servizio Device Provisioning e l'hub IoT tramite i gruppi di registrazioni. 

Assicurarsi di completare la procedura descritta in [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md) prima di continuare.


## <a name="prepare-the-environment"></a>Preparare l'ambiente 

1. Verificare che [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) sia installato nel computer.

1. Scaricare e installare [Maven](https://maven.apache.org/install.html).

1. Verificare che `git` sia installato nel computer e venga aggiunto alle variabili di ambiente accessibili alla finestra di comando. Vedere gli [strumenti client Git di Software Freedom Conservancy](https://git-scm.com/download/) per la versione più recente degli strumenti `git` da installare, tra cui **Git Bash**, l'app da riga di comando che è possibile usare per interagire con il repository Git locale. 

1. Usare le informazioni disponibili nella [panoramica dei certificati](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) per creare i certificati di test. Per un'analisi più approfondita sulla creazione di certificati, vedere [Script di PowerShell per la gestione di certificati X.509 firmati dall'autorità di certificazione](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-security-x509-create-certificates).

    > [!NOTE]
    > Questo passaggio richiede [OpenSSL](https://www.openssl.org/), che può essere compilato e installato dall'origine e installato da [terze parti](https://wiki.openssl.org/index.php/Binaries) come [questa](https://sourceforge.net/projects/openssl/). Se sono già stati creati i certificati di tipo _radice_, _intermedio_ e _dispositivo_, è possibile saltare questo passaggio.
    >

1. Creare le informazioni sul gruppo di registrazioni:

    1. Eseguire il **Passaggio 1** e il **Passaggio 2** per creare i certificati _radice_ e _intermedio_.

    1. Accedere al portale di Azure, fare clic sul pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio di provisioning.

        1. Nel pannello di riepilogo del servizio Device Provisioning selezionare **Certificati** e fare clic sul pulsante **Aggiungi** nella parte superiore.

        1. In **Aggiungi certificato** immettere le informazioni seguenti:
            - Immettere un nome certificato univoco.
            - Selezionare il file **_RootCA.pem_** appena creato.
            - Al termine, fare clic sul pulsante **Save** (Salva).

        ![Aggiungere il certificato](./media/tutorial-group-enrollments/add-certificate.png)

        1. Selezionare il certificato appena creato:
            - Fare clic su **Genera codice di verifica**. Copiare il codice generato.
            - Eseguire completamente il **Passaggio 3**. Immettere il _codice di verifica_ o fare clic con il pulsante destro del mouse o incollare il codice nella finestra di PowerShell in esecuzione.  Premere **Invio**.
            - Selezionare il file **_verifyCert4.pem_** appena creato nel portale di Azure. Fare clic su **Verifica**.

            ![Convalidare il certificato](./media/tutorial-group-enrollments/validate-certificate.png)

1. Completare la procedura eseguendo il **Passaggio 4** e il **Passaggio 5** per creare i certificati del dispositivo e pulire le risorse.

> [!NOTE]
> Quando si creano certificati del dispositivo, assicurarsi di usare solo caratteri alfanumerici minuscoli e trattini nel nome del dispositivo.
>


## <a name="create-a-device-enrollment-entry"></a>Creare una voce per la registrazione dei dispositivi

1. Aprire un prompt dei comandi. Clonare il repository di GitHub per gli esempi di codice Java SDK:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. Nel codice sorgente scaricato passare alla cartella dell'esempio **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**. Aprire il file **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** nell'editor preferito e aggiungere i dettagli seguenti:

    1. Aggiungere `[Provisioning Connection String]` per il servizio di provisioning dal portale, come indicato di seguito:

        1. Passare al servizio di provisioning nel [portale di Azure](https://portal.azure.com). 

        1. Aprire **Criteri di accesso condivisi** e selezionare un criterio con l'autorizzazione *EnrollmentWrite*.
    
        1. Copiare la **stringa di connessione chiave primaria**. 

            ![Ottenere la stringa di connessione per il provisioning dal portale](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. Nel file del codice di esempio **_ServiceEnrollmentGroupSample.java_** sostituire `[Provisioning Connection String]` con la **stringa di connessione chiave primaria**.

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. Aprire il file **_RootCA.pem_** in un editor di testo. Assegnare il valore del **certificato radice** al parametro **PUBLIC_KEY_CERTIFICATE_STRING**, come illustrato sotto:

        ```java
        private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
        ```
 
    1. Passare all'hub IoT collegato al servizio di provisioning nel [portale di Azure](https://portal.azure.com). Aprire la scheda **Panoramica** dell'hub e copiare il **nome host**. Assegnare questo **nome host** al parametro *IOTHUB_HOST_NAME*.

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. Esaminare il codice di esempio, che crea, aggiorna, esegue query ed elimina la registrazione di un gruppo per i dispositivi X.509. Per verificare che la registrazione sia stata eseguita correttamente nel portale, impostare temporaneamente come commento le righe di codice seguenti alla fine del file _ServiceEnrollmentGroupSample.java_:

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. Salvare il file _ServiceEnrollmentGroupSample.java_. 

1. Aprire una finestra di comando e passare alla cartella **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**.

1. Compilare il codice di esempio usando questo comando:

    ```cmd\sh
    mvn install -DskipTests
    ```

1. Eseguire l'esempio usando questi comandi nella finestra di comando:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. Verificare nella finestra di output che la registrazione sia stata eseguita correttamente.

    ![Registrazione completata](./media/tutorial-group-enrollments/enrollment.png) 

1. Passare al servizio di provisioning nel portale di Azure. Fare clic su **Gestisci registrazioni**. Si noti che il gruppo di dispositivi X.509 viene visualizzato nella scheda **Gruppi di registrazioni** con un *NOME GRUPPO* generato automaticamente. 


## <a name="simulate-the-device"></a>Simulare il dispositivo

1. Nel pannello di riepilogo del servizio Device Provisioning selezionare **Panoramica** e prendere nota dell'_ambito ID_ e dell'_endpoint globale del servizio di provisioning_.

    ![Informazioni sul servizio](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. Aprire un prompt dei comandi. Passare alla cartella del progetto di esempio.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Immettere le informazioni sul gruppo di registrazioni nel modo seguente:

    - Modificare `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` per poter includere l'_ambito ID_ e l'_endpoint globale del servizio di provisioning_ annotati prima. Aprire il file **_{deviceName}-public.pem_** e includere questo valore come _certificato client_. Aprire il file **_{deviceName}-all.pem_** e copiare il testo da _-----BEGIN PRIVATE KEY-----_ a _-----END PRIVATE KEY-----_.  Usare questo valore come _chiave privata del certificato client_.

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

1. Compilare l'esempio. Passare alla cartella di destinazione ed eseguire il file JAR creato.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![Registrazione completata](./media/tutorial-group-enrollments/registration.png)

1. Nel portale passare all'hub IoT collegato al servizio di provisioning e aprire il pannello **Esplora dispositivi**. Al termine del provisioning del dispositivo X.509 simulato nell'hub, il relativo ID dispositivo verrà visualizzato nel pannello **Esplora dispositivi** con *STATO* **abilitato**. Si noti che potrebbe essere necessario fare clic sul pulsante **Aggiorna** in alto, se il pannello è stato aperto prima dell'esecuzione dell'applicazione del dispositivo di esempio. 

    ![Il dispositivo viene registrato con l'hub IoT](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare ed esplorare l'esempio di client del dispositivo, non pulire le risorse create in questa guida introduttiva. Se non si prevede di continuare, usare i passaggi seguenti per eliminare tutte le risorse create da questa guida introduttiva.

1. Chiudere la finestra di output di esempio di client del dispositivo sul computer.
1. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare il servizio Device Provisioning. Aprire il pannello **Gestisci registrazioni** per il servizio, quindi fare clic sulla scheda **Registrazioni singole**. Selezionare l'*ID registrazione* del dispositivo registrato in questa guida introduttiva e fare clic sul pulsante **Elimina** nella parte superiore. 
1. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare l'hub IoT. Aprire il pannello **Dispositivi IoT** per l'hub, selezionare l'*ID* del dispositivo registrato in questa guida introduttiva e quindi fare clic su **Elimina** nella parte superiore.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un dispositivo X.509 simulato nel computer Windows ed è stato effettuato il provisioning del dispositivo nell'hub IoT usando il servizio Device Provisioning in hub IoT e i gruppi di registrazioni. Per altre informazioni sul dispositivo X.509, passare ai concetti relativi al dispositivo. 

> [!div class="nextstepaction"]
> [Concetti relativi ai dispositivi del servizio Device Provisioning in hub IoT](concepts-device.md)
