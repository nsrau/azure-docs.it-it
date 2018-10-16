---
title: Configurare il dispositivo per il servizio Device Provisioning in hub IoT di Azure
description: Configurare un dispositivo per il provisioning tramite il servizio Device Provisioning in hub IoT durante il processo di produzione del dispositivo
author: wesmc7777
ms.author: wesmc
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: f0b62f73650294349e1879b306beebabdaf974a7
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2018
ms.locfileid: "45633402"
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Configurare un dispositivo per il provisioning usando il servizio Device Provisioning in hub IoT di Azure

Nell'esercitazione precedente è stato descritto come configurare il servizio Device Provisioning in hub IoT di Azure per effettuare automaticamente il provisioning dei dispositivi per l'hub IoT. Questa esercitazione mostra come configurare il dispositivo durante il processo produttivo, consentendone il provisioning automatico con lhub IoT. Il provisioning del dispositivo viene effettuato in base al rispettivo [meccanismo di attestazione](concepts-device.md#attestation-mechanism), al primo avvio e alla prima connessione al servizio di provisioning. Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Creare un SDK client del servizio Device Provisioning specifico per la piattaforma
> * Estrarre gli elementi di sicurezza
> * Creare il software di registrazione del dispositivo

In questa esercitazione si presuppone che siano già stati creati l'istanza del servizio Device Provisioning e un hub IoT, seguendo le istruzioni indicate nell'esercitazione [Configurare risorse cloud](tutorial-set-up-cloud.md) precedente.

Questa esercitazione usa il [repository per gli SDK e le librerie di Azure IoT per C](https://github.com/Azure/azure-iot-sdk-c), che include l'SDK client per il servizio Device Provisioning per C. L'SDK offre attualmente il supporto per TPM e X.509 per dispositivi in esecuzione su implementazioni Windows o Ubuntu. Questa esercitazione è basata sull'uso di un client di sviluppo Windows e ciò presuppone anche competenze di base di Visual Studio 2017. 

Se non si ha familiarità con il processo di provisioning automatico, vedere [Concetti relativi al provisioning automatico](concepts-auto-provisioning.md) prima di continuare. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Visual Studio 2015 oppure [Visual Studio 2017](https://www.visualstudio.com/vs/) con il carico di lavoro [Sviluppo di applicazioni desktop con C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) abilitato.
* La versione più recente di [Git](https://git-scm.com/download/) installata.



## <a name="build-a-platform-specific-version-of-the-sdk"></a>Creare una versione dell'SDK specifica per la piattaforma

L'SDK client del servizio Device Provisioning consente di implementare il software di registrazione del dispositivo. Prima di poterlo usare, è tuttavia necessario creare una versione dell'SDK specifica per la piattaforma client di sviluppo e per il meccanismo di attestazione. In questa esercitazione viene creato un SDK che usa Visual Studio 2017 su una piattaforma di sviluppo Windows per un tipo supportato di attestazione:

1. Scaricare la versione 3.11.4 del [sistema di compilazione CMake](https://cmake.org/download/). Verificare il file binario scaricato usando il valore hash di crittografia corrispondente. Nell'esempio seguente viene usato Windows PowerShell per verificare l'hash di crittografia per la versione 3.11.4 della distribuzione MSI x64:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    I seguenti valori hash per la versione 3.11.4 venivano elencati nel sito di CMake al momento della stesura di questo articolo:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    È importante che nel computer siano installati i prerequisiti di Visual Studio (Visual Studio e carico di lavoro 'Sviluppo di applicazioni desktop con C++') **prima** di avviare l'installazione di `CMake`. Quando i prerequisiti sono pronti e il download è stato verificato, installare il sistema di compilazione CMake.

1. Aprire un prompt dei comandi o la shell Git Bash. Eseguire il comando seguente per clonare il repository GitHub [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Le dimensioni di questo repository sono attualmente di circa 220 MB. Il completamento di questa operazione richiederà alcuni minuti.


1. Creare una sottodirectory `cmake` nella directory radice del repository Git e passare a tale cartella. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Compilare l'SDK per la piattaforma di sviluppo in uso, in base ai meccanismi di attestazione che verranno usati. Usare uno dei comandi seguenti (notare anche i due caratteri punto finali per ogni comando). Al termine, CMake crea la sottodirectory `/cmake` con contenuto specifico per il dispositivo:
 
    - Per i dispositivi che usano il simulatore TPM per l'attestazione:

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - Per qualsiasi altro dispositivo (TPM/HSM/X.509 fisico o un certificato X.509 simulato):

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```


Ora è possibile usare l'SDK per compilare il codice di registrazione del dispositivo. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>Estrarre gli elementi di sicurezza 

Il passaggio successivo consiste nell'estrarre gli elementi di sicurezza per il meccanismo di attestazione usato dal dispositivo. 

### <a name="physical-devices"></a>Dispositivi fisici 

A seconda del fatto che l'SDK sia stato compilato per usare l'attestazione per un modulo TPM/modulo di protezione hardware fisico o certificati X.509, la raccolta degli artefatti di sicurezza funziona come segue:

- Per un dispositivo TPM, è necessario determinare la **Chiave di approvazione** associata al dispositivo dal produttore del chip TPM. È possibile derivare un **ID registrazione** univoco per il dispositivo TPM mediante l'uso di hash nella chiave di verifica autenticità.  

- Per un dispositivo X.509, è necessario ottenere i certificati rilasciati ai dispositivi. Il servizio di provisioning espone due tipi di voci di registrazione che controllano l'accesso per i dispositivi che usano il meccanismo di attestazione X.509. I certificati necessari dipendono dai tipi di registrazione che verranno usati.

    1. Registrazioni individuali: registrazione per un singolo dispositivo specifico. Questo tipo di voce di registrazione richiede [certificati dell'entità finale, "foglia"](concepts-security.md#end-entity-leaf-certificate).
    1. Gruppi di registrazioni: questo tipo di voce di registrazione richiede certificati intermedi o radice. Per altre informazioni, vedere [Controllo dell'accesso dei dispositivi al servizio di provisioning con certificati X.509](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

### <a name="simulated-devices"></a>Dispositivi simulati

A seconda del fatto che l'SDK sia stato compilato per usare l'attestazione per un dispositivo simulato tramite TPM o certificati X.509, la raccolta degli artefatti di sicurezza funziona come segue:

- Per un dispositivo TPM simulato:

   1. Aprire un prompt dei comandi di Windows, passare alla sottodirectory `azure-iot-sdk-c` ed eseguire il simulatore TPM, che è in ascolto di un socket sulle porte 2321 e 2322. Non chiudere questa finestra di comando. Il simulatore dovrà restare in esecuzione fino alla fine della guida introduttiva seguente. 

      Dalla sottodirectory `azure-iot-sdk-c` eseguire il comando seguente per avviare il simulatore:

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

      > [!NOTE]
      > Se si usa il prompt dei comandi di Git Bash per questo passaggio, sarà necessario sostituire le barre rovesciate con barre, ad esempio: `./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe`.

   1. Usando Visual Studio, aprire la soluzione generata nella cartella *cmake* denominata `azure_iot_sdks.sln` e creata tramite il comando "Compila soluzione" dal menu "Compilazione".

   1. Nel riquadro *Esplora soluzioni* di Visual Studio passare alla cartella **Provision\_Tools**. Fare clic con il pulsante destro del mouse sul progetto **tpm_device_provision** e scegliere **Imposta come progetto di avvio**. 

   1. Eseguire la soluzione usando uno dei comandi "Avvia" dal menu "Debug". La finestra di output visualizza l'**_ID registrazione_** del simulatore TPM e la **_Chiave di approvazione_**, necessari per la registrazione dei dispositivi. Copiare questi valori per un utilizzo successivo. È possibile chiudere questa finestra contenente ID registrazione e Chiave di approvazione, ma lasciare aperta la finestra del simulatore TPM in esecuzione avviato al passaggio 1.

- Per un dispositivo X.509 simulato:

  1. Usando Visual Studio, aprire la soluzione generata nella cartella *cmake* denominata `azure_iot_sdks.sln` e creata tramite il comando "Compila soluzione" dal menu "Compilazione".

  1. Nel riquadro *Esplora soluzioni* di Visual Studio passare alla cartella **Provision\_Tools**. Fare clic con il pulsante destro del mouse sul progetto **dice\_device\_enrollment** e scegliere **Set as Startup Project** (Imposta come progetto di avvio). 
  
  1. Eseguire la soluzione usando uno dei comandi "Avvia" dal menu "Debug". Nella finestra di output immettere **i** per la registrazione singola quando richiesto. Nella finestra di output viene visualizzato un certificato X.509 generato in locale per il dispositivo simulato. Copiare l'output da *-----BEGIN CERTIFICATE-----* alla prima occorrenza di *-----END CERTIFICATE-----*, assicurandosi di includere anche queste due righe. È necessario solo il primo certificato indicato nella finestra di output.
 
  1. Creare un file denominato **_X509testcert.pem_**, aprirlo in un editor di testo di propria scelta e copiare il contenuto degli Appunti in questo file. Salvare il file, perché verrà usato in seguito per la registrazione dei dispositivi. Quando il software di registrazione viene eseguito, usa lo stesso certificato durante il provisioning automatico.    

Questi elementi di sicurezza sono necessari durante la registrazione del dispositivo nel servizio Device Provisioning. Il servizio di provisioning attende che il dispositivo venga ad un certo punto avviato e connesso. Quando il dispositivo viene avviato per la prima volta, la logica dell'SDK client interagisce con il chip o con il simulatore per estrarre gli elementi di sicurezza dal dispositivo e verifica la registrazione con il servizio Device Provisioning. 

## <a name="create-the-device-registration-software"></a>Creare il software di registrazione del dispositivo

L'ultimo passaggio consiste nella scrittura di un'applicazione di registrazione che usa l'SDK client del servizio Device Provisioning per registrare il dispositivo nel servizio hub IoT. 

> [!NOTE]
> Per questo passaggio si presuppone l'uso di un dispositivo simulato, ottenuto tramite l'esecuzione di un'applicazione di registrazione di esempio dell'SDK dalla workstation. Gli stessi concetti sono tuttavia applicabili se si compila un'applicazione di registrazione per la distribuzione in un dispositivo fisico. 

1. Nel portale di Azure selezionare il pannello **Panoramica** per il servizio Device Provisioning e copiare il valore **_Ambito ID_**. L'*ambito ID* viene generato dal servizio e garantisce l'univocità. Non può essere modificato e viene usato per identificare in modo univoco gli ID di registrazione.

    ![Estrarre le informazioni dell'endpoint del servizio Device Provisioning dal pannello del portale](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

1. In *Esplora soluzioni* di Visual Studio passare alla cartella **Provision\_Samples**. Selezionare il progetto di esempio denominato **prov\_dev\_client\_sample** e aprire il file di origine **prov\_dev\_client\_sample.c**.

1. Assegnare il valore _Ambito ID_ ottenuto nel Passaggio 1 alla variabile `id_scope` (rimuovendo le parentesi di sinistra/`[` e di destra/`]`): 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    Come riferimento, vedere la variabile `global_prov_uri`, che consente all'API`IoTHubClient_LL_CreateFromDeviceAuth` di registrazione al client dell'hub IoT di connettersi all'istanza designata del servizio Device Provisioning.

1. Nella funzione **main()** nello stesso file aggiungere/rimuovere il commento alla variabile `hsm_type` che corrisponde al meccanismo di attestazione usato dal software di registrazione del dispositivo (TPM o X.509): 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

1. Salvare le modifiche e ricompilare l'esempio **prov\_dev\_client\_sample** selezionando "Compila soluzione" dal menu "Compilazione". 

1. Fare clic con il pulsante destro del mouse sul progetto **prov\_dev\_client\_sample** nella cartella **Provision\_Samples** e scegliere **Set as Startup Project** (Imposta come progetto di avvio). NON eseguire ancora l'applicazione di esempio.

> [!IMPORTANT]
> Non eseguire/avviare ancora il dispositivo. È necessario completare il processo registrando il dispositivo con il servizio Device Provisioning Service prima di avviare il dispositivo. La sezione Passaggi successivi più avanti fornirà indicazioni sull'articolo successivo.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>API dell'SDK usate durante la registrazione (solo come riferimento)

Come riferimento, l'SDK fornisce le API seguenti per l'applicazione da usare durante la registrazione. Queste API consentono al dispositivo di connettersi e registrarsi al servizio Device Provisioning all'avvio. Il dispositivo riceve a sua volta le informazioni necessarie per stabilire una connessione all'istanza dell'hub IoT:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

È anche possibile che sia necessario perfezionare l'applicazione di registrazione del client del servizio Device Provisioning usando prima di tutto un dispositivo simulato e una configurazione di prova del servizio. Dopo avere verificato il corretto funzionamento dell'applicazione nell'ambiente di test, è possibile compilarla per il dispositivo specifico e copiare il file eseguibile nell'immagine del dispositivo. 

## <a name="clean-up-resources"></a>Pulire le risorse

I servizi Device Provisioning e hub IoT sono a questo punto in esecuzione nel portale. Se si vuole interrompere la configurazione del provisioning dei dispositivi e/o posticipare il completamento di questa serie di esercitazioni, è consigliabile arrestarli per evitare costi non necessari.

1. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare il servizio Device Provisioning. Nella parte superiore del pannello **Tutte le risorse** fare clic su **Elimina**.  
1. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare l'hub IoT. Nella parte superiore del pannello **Tutte le risorse** fare clic su **Elimina**.  

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un SDK client del servizio Device Provisioning specifico per la piattaforma
> * Estrarre gli elementi di sicurezza
> * Creare il software di registrazione del dispositivo

Passare all'esercitazione successiva per informazioni su come effettuare il provisioning del dispositivo per l'hub IoT registrandolo nel servizio Device Provisioning in hub IoT di Azure per il provisioning automatico.

> [!div class="nextstepaction"]
> [Effettuare il provisioning del dispositivo nell'hub IoT](tutorial-provision-device-to-hub.md)

