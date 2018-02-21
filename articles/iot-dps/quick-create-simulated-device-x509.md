---
title: Effettuare il provisioning di un dispositivo X.509 simulato nell'hub IoT di Azure usando C | Microsoft Docs
description: Guida introduttiva di Azure - Creare ed effettuare il provisioning di un dispositivo simulato X.509 usando l'SDK per dispositivi C per il servizio Device Provisioning in hub IoT di Azure
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 12/20/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4d723a3b78a43d3b609d5a884591a92606ca11cc
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="create-and-provision-an-x509-simulated-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Creare ed effettuare il provisioning di un dispositivo simulato X.509 usando l'SDK per dispositivi C per il servizio Device Provisioning in hub IoT
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Questi passaggi illustrano come simulare un dispositivo X.509 in un computer di sviluppo con sistema operativo Windows e usare un esempio di codice per connettere il dispositivo simulato con il servizio Device Provisioning e l'hub IoT. 

Assicurarsi di completare la procedura descritta in [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Configurare il servizio Device Provisioning in hub IoT con il portale di Azure) prima di continuare.

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo 

1. Verificare che nel computer sia installato Visual Studio 2015 o [Visual Studio 2017](https://www.visualstudio.com/vs/). È necessario che il carico di lavoro ['Sviluppo di applicazioni desktop con C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) sia abilitato per l'installazione di Visual Studio.

2. Scaricare e installare il [sistema di compilazione CMake](https://cmake.org/download/). È importante che nel computer sia installato Visual Studio con il carico di lavoro 'Sviluppo di applicazioni desktop con C++' **prima** dell'installazione di `cmake`. 

3. Verificare che `git` sia installato nel computer e venga aggiunto alle variabili di ambiente accessibili alla finestra di comando. Vedere gli [strumenti client Git di Software Freedom Conservancy](https://git-scm.com/download/) per la versione più recente degli strumenti `git` da installare, tra cui **Git Bash**, l'app da riga di comando che è possibile usare per interagire con il repository Git locale. 

4. Aprire un prompt dei comandi o Git Bash. Clonare il repository GitHub per l'esempio di codice di simulazione del dispositivo:
    
    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

5. Creare una cartella nella copia locale di questo repository GitHub per il processo di compilazione CMake. 

    ```cmd
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

6. Eseguire questo comando per creare la soluzione di Visual Studio per il client di provisioning.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```
    
    Se `cmake` non trova il compilatore C++, si potrebbero verificare errori di compilazione durante l'esecuzione del comando precedente. In tal caso, provare a eseguire questo comando nel [prompt dei comandi di Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 


<a id="portalenroll"></a>

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Creare una voce di registrazione del dispositivo nel servizio Device Provisioning

1. Aprire la soluzione generata nella cartella *cmake* denominata `azure_iot_sdks.sln` e compilarla in Visual Studio.

2. Fare clic con il pulsante destro del mouse sul progetto **dice\_device\_enrollment** nella cartella **Provision\_Tools** e scegliere **Imposta come progetto di avvio**. Eseguire la soluzione. Nella finestra di output immettere **i** per la registrazione singola quando richiesto. Nella finestra di output viene visualizzato un certificato X.509 generato in locale per il dispositivo simulato. Copiare l'output da *-----BEGIN CERTIFICATE-----* alla prima occorrenza di *-----END CERTIFICATE-----*, assicurandosi di includere anche queste due righe. Si noti che è necessario solo il primo certificato indicato nella finestra di output.
 
3. Creare un file denominato **_X509testcert.pem_** nel computer Windows, aprirlo in un editor di propria scelta e copiare il contenuto degli Appunti in questo file. Salvare il file. 

4. Accedere al portale di Azure, fare clic sul pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio di provisioning.

4. Aprire il pannello **Gestisci registrazioni** per il servizio. Selezionare la scheda **Registrazioni singole** e fare clic sul pulsante **Aggiungi** nella parte superiore. 

5. In **Add enrollment list entry** (Aggiungi voce elenco di registrazione) immettere le informazioni seguenti:
    - Selezionare **X.509** come *meccanismo* di attestazione dell'identità.
    - In *File di certificato con estensione pem o cer* selezionare il file di certificato **_X509testcert.pem_** creato nei passaggi precedenti con il widget *Esplora file*.
    - Facoltativamente, è possibile specificare le informazioni seguenti:
        - Selezionare un hub IoT collegato al servizio di provisioning.
        - Immettere un ID dispositivo univoco. Assicurarsi di non usare dati sensibili quando si assegna un nome al dispositivo. 
        - Aggiornare lo **stato iniziale del dispositivo gemello** con la configurazione iniziale desiderata per il dispositivo.
    - Al termine, fare clic sul pulsante **Save** (Salva). 

    ![Immettere le informazioni di registrazione del dispositivo X.509 nel pannello del portale](./media/quick-create-simulated-device-x509/enter-device-enrollment.png)  

   Al termine della registrazione, il dispositivo X.509 verrà visualizzato come **riot-device-cert** nella colonna *ID registrazione* della scheda *Registrazioni singole*. 



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simulare la sequenza del primo avvio per il dispositivo

1. Nel portale di Azure selezionare il pannello **Panoramica** per il servizio Device Provisioning e prendere nota del valore di **_Ambito ID_**.

    ![Estrarre le informazioni dell'endpoint del servizio Device Provisioning dal pannello del portale](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. In Visual Studio sul computer passare al progetto di esempio denominato **prov\_dev\_client\_sample** nella cartella **Provision\_Samples** e aprire il file **prov\_dev\_client\_sample.c**.

3. Assegnare il valore _ID Scope_ (Ambito ID) alla variabile `id_scope`. 

    ```c
    static const char* id_scope = "[ID Scope]";
    ```

4. Nella funzione **main()** dello stesso file, verificare che **SECURE_DEVICE_TYPE** sia impostato su X.509.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

   Impostare come commento o eliminare l'istruzione `hsm_type = SECURE_DEVICE_TYPE_TPM;`, se presente. 

5. Fare clic con il pulsante destro del mouse sul progetto **prov\_dev\_client\_sample** e scegliere **Imposta come progetto di avvio**. Eseguire l'esempio. Si notino i messaggi che simulano l'avvio e la connessione del dispositivo al servizio Device Provisioning per ottenere le informazioni dell'hub IoT. Cercare il messaggio che indica il completamento della registrazione nell'hub: *Registration Information received from service: yourhuburl!* (Ricevute informazioni di registrazione dal servizio: urlhub). Chiudere la finestra quando richiesto.

6. Nel portale passare all'hub IoT collegato al servizio di provisioning e aprire il pannello **Dispositivi IoT**. Al termine del provisioning del dispositivo X.509 simulato nell'hub, il relativo ID dispositivo verrà visualizzato nel pannello **Dispositivi IoT** con *STATO* **abilitato**. Si noti che potrebbe essere necessario fare clic sul pulsante **Aggiorna** in alto, se il pannello è stato aperto prima dell'esecuzione dell'applicazione del dispositivo di esempio. 

    ![Il dispositivo viene registrato con l'hub IoT](./media/quick-create-simulated-device/hub-registration.png) 

    Se si è modificato lo *stato iniziale del dispositivo gemello* rispetto al valore predefinito della voce di registrazione del dispositivo, è possibile eseguire il pull dello stato del dispositivo desiderato dall'hub e agire di conseguenza. Per altre informazioni, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).


> [!IMPORTANT]
> È inoltre possibile eseguire una *registrazione di gruppo* dei dispositivi X.509 apportando le modifiche seguenti ai passaggi di questa guida introduttiva:
>    1. Configurare il computer Windows per l'uso della libreria **OpenSSL** al posto della libreria **SChannel** predefinita consultando la sezione **WebSockets** della Guida [Set up a Windows development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#windows) (Configurare un ambiente di sviluppo Windows). Si noti che i computer Linux usano OpenSSL per impostazione predefinita. 
>    2. Nel passaggio 2 della sezione [Creare una voce di registrazione del dispositivo nel servizio Device Provisioning](#portalenroll) illustrata in precedenza, immettere **g** per la registrazione di gruppo.
>    3. Nei passaggi 4 e 5 della [stessa sezione](#portalenroll) selezionare **Gruppi di registrazioni** e immettere le informazioni necessarie per la voce di gruppo.  
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
