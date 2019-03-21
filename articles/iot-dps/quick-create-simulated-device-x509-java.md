---
title: Effettuare il provisioning di un dispositivo X.509 simulato nell'hub IoT di Azure usando Java | Microsoft Docs
description: Guida introduttiva di Azure - Creare ed effettuare il provisioning di un dispositivo simulato X.509 usando l'SDK per dispositivi Java per il servizio Device Provisioning in hub IoT. Questa guida introduttiva usa registrazioni singole.
author: wesmc7777
ms.author: wesmc
ms.date: 04/09/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 52fbef24e8a1b9fd7e0ade404c23a587c81d6b0a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105999"
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Creare ed effettuare il provisioning di un dispositivo simulato X.509 usando l'SDK per dispositivi Java per il servizio Device Provisioning in hub IoT
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Questi passaggi illustrano come simulare un dispositivo X.509 in un computer di sviluppo con sistema operativo Windows e usare un esempio di codice per connettere il dispositivo simulato con il servizio Device Provisioning e l'hub IoT. 

Se non si ha familiarità con il processo di provisioning automatico, è necessario vedere [Concetti relativi al provisioning automatico](concepts-auto-provisioning.md). È anche necessario aver completato la procedura descritta in [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md) prima di continuare. 

Il servizio Device Provisioning in Azure IoT supporta due tipi di registrazione:
- [Gruppi di registrazioni](concepts-service.md#enrollment-group): Usato per registrare più dispositivi correlati.
- [Registrazioni singole](concepts-service.md#individual-enrollment): Usato per registrare un singolo dispositivo.

Questo articolo descrive le registrazioni singole.

## <a name="prepare-the-environment"></a>Preparare l'ambiente 

1. Verificare che [Java SE Development Kit 8](https://aka.ms/azure-jdks) sia installato nel computer.

2. Scaricare e installare [Maven](https://maven.apache.org/install.html).

3. Verificare che Git sia installato nel computer e che venga aggiunto alle variabili di ambiente accessibili alla finestra di comando. Vedere gli [strumenti client Git di Software Freedom Conservancy](https://git-scm.com/download/) per la versione più recente degli strumenti `git` da installare, tra cui **Git Bash**, l'app da riga di comando che è possibile usare per interagire con il repository Git locale. 

4. Aprire un prompt dei comandi. Clonare il repository GitHub per l'esempio di codice di simulazione del dispositivo:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. Passare alla directory radice `azure-iot-sdk-`java e compilare il progetto per scaricare tutti i pacchetti necessari.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. Passare al progetto di generazione del certificato e compilare il progetto. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Creare un certificato dispositivo X.509 autofirmato e la voce di registrazione singola

In questa sezione si userà un certificato X.509 autofirmato. È importante tenere presente quanto segue:

* I certificati autofirmati sono destinati solo alle operazioni di testing e non dovrebbero essere usati nell'ambiente di produzione.
* La data di scadenza predefinita per un certificato autofirmato è un anno.

Si userà il codice di esempio da [Azure IoT SDK per Java](https://github.com/Azure/azure-iot-sdk-java.git) per creare il certificato da usare con la voce di registrazione singola per il dispositivo simulato.


1. Usando il prompt dei comandi come descritto nei passaggi precedenti, passare alla cartella `target`, quindi eseguire il file JAR creato nel passaggio precedente.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. Immettere **N** per _Do you want to input common name_ (Immettere un nome comune?) Copiare negli Appunti l'output di `Client Cert`, da *-----BEGIN CERTIFICATE-----* a *-----END CERTIFICATE-----*.

   ![Generatore di certificato singolo](./media/java-quick-create-simulated-device-x509/individual.png)

3. Creare un file denominato **_X509individual.pem_** nel computer Windows, aprirlo in un editor di propria scelta e copiare il contenuto degli Appunti in questo file. Salvare il file e chiudere l'editor.

4. Nel prompt dei comandi immettere **N** per _Do you want to input Verification Code_ (Inserire codice di verifica?) e tenere aperto l'output del programma per farvi riferimento più avanti nella guida introduttiva. Successivamente verranno copiati i valori `Client Cert` e `Client Cert Private Key` per essere usati nella sezione successiva.

5. Accedere al [portale di Azure](https://portal.azure.com), fare clic sul pulsante **Tutte le risorse** nel menu a sinistra e aprire l'istanza del servizio Device Provisioning.

6. Nel pannello di riepilogo del servizio Device Provisioning selezionare **Manage enrollments** (Gestisci registrazioni). Selezionare la scheda **Individual Enrollments** (Registrazione singola) e fare clic sul pulsante in alto **Add individual enrollment** (Aggiungi registrazione singola). 

7. Nel riquadro **Add Enrollment** (Aggiungi registrazione) immettere le informazioni seguenti:
   - Selezionare **X.509** come *meccanismo* di attestazione dell'identità.
   - In *Primary certificate .pem or .cer file* (File di certificato primario con estensione pem o cer) fare clic su *Selezionare un file* per selezionare il file di certificato **X509individual.pem** creato nei passaggi precedenti.  
   - Facoltativamente, è possibile specificare le informazioni seguenti:
     - Selezionare un hub IoT collegato al servizio di provisioning.
     - Immettere un ID dispositivo univoco. Assicurarsi di non usare dati sensibili quando si assegna un nome al dispositivo. 
     - Aggiornare lo **stato iniziale del dispositivo gemello** con la configurazione iniziale desiderata per il dispositivo.
     - Al termine, fare clic sul pulsante **Save** (Salva). 

     [![Aggiungere una registrazione singola per l'attestazione X.509 nel portale](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     Al termine della registrazione, il dispositivo X.509 verrà visualizzato come **microsoftriotcore** nella colonna *ID registrazione* della scheda *Registrazioni singole*. 



## <a name="simulate-the-device"></a>Simulare il dispositivo

1. Nel pannello di riepilogo del servizio Device Provisioning selezionare **Panoramica** e prendere nota dell'_ambito ID_ e dell'_endpoint globale del servizio di provisioning_.

    ![Informazioni sul servizio](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Aprire un prompt dei comandi. Passare alla cartella del progetto di esempio del repository Java SDK.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Immettere le informazioni sul servizio di provisioning e l'identità X.509 nel codice. Queste informazioni verranno usate durante il provisioning automatico, per l'attestazione del dispositivo simulato, prima della registrazione del dispositivo:

   - Modificare il file `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` per includere l'_ambito ID_ e l'_endpoint globale del servizio di provisioning_ annotati in precedenza. Includere anche i valori _Client Cert_ (Certificato client) e _Client Cert Private Key_ (Chiave privata certificato client) annotati nella sezione precedente.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - Usare il formato seguente per copiare/incollare il certificato e la chiave privata:
        
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

4. Compilare l'esempio. Passare alla cartella `target` ed eseguire il file JAR creato.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. Nel portale di Azure passare all'hub IoT collegato al servizio di provisioning e aprire il pannello **Device Explorer**. Al termine del provisioning del dispositivo X.509 simulato nell'hub, il relativo ID dispositivo verrà visualizzato nel pannello **Device Explorer** con *STATO* **abilitato**.  Potrebbe essere necessario fare clic sul pulsante **Aggiorna** nella parte superiore se il pannello è stato aperto prima dell'esecuzione dell'applicazione del dispositivo di esempio. 

    ![Il dispositivo viene registrato con l'hub IoT](./media/java-quick-create-simulated-device-x509/hubregistration.png) 

> [!NOTE]
> Se si è modificato lo *stato iniziale del dispositivo gemello* rispetto al valore predefinito della voce di registrazione del dispositivo, è possibile eseguire il pull dello stato del dispositivo desiderato dall'hub e agire di conseguenza. Per altre informazioni, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare ed esplorare l'esempio di client del dispositivo, non pulire le risorse create in questa guida introduttiva. Se non si prevede di continuare, usare i passaggi seguenti per eliminare tutte le risorse create da questa guida introduttiva.

1. Chiudere la finestra di output di esempio di client del dispositivo sul computer.
2. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare il servizio Device Provisioning. Aprire il pannello **Gestisci registrazioni** per il servizio, quindi fare clic sulla scheda **Registrazioni singole**. Selezionare l'*ID registrazione* del dispositivo registrato in questa guida introduttiva e fare clic sul pulsante **Elimina** nella parte superiore. 
3. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare l'hub IoT. Aprire il pannello **Dispositivi IoT** per l'hub, selezionare l'*ID* del dispositivo registrato in questa guida introduttiva e quindi fare clic su **Elimina** nella parte superiore.


## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un dispositivo X.509 simulato nel computer Windows. La registrazione del dispositivo è stata configurata nel servizio Device Provisioning in hub IoT, quindi è stato eseguito il provisioning automatico del dispositivo nell'hub IoT. Per informazioni su come registrare il dispositivo X.509 a livello di codice, passare alla guida introduttiva per la registrazione a livello di codice dei dispositivi X.509. 

> [!div class="nextstepaction"]
> [Guida introduttiva di Azure - Registrare dispositivi X.509 nel servizio Device Provisioning in hub IoT di Azure](quick-enroll-device-x509-java.md)
