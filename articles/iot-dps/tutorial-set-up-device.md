---
title: Configurare un dispositivo per il servizio Device Provisioning in hub IoT di Azure| Microsoft Docs
description: Configurare un dispositivo per il provisioning tramite il servizio Device Provisioning in hub IoT durante il processo di produzione del dispositivo
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 266b9b7eb228744075627e1e80710e63c27880cc
ms.openlocfilehash: c0ef886a8c51f43cba875d532835acb194e994d9
ms.contentlocale: it-it
ms.lasthandoff: 09/06/2017

---

# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Configurare un dispositivo per il provisioning usando il servizio Device Provisioning in hub IoT di Azure

Nell'esercitazione precedente è stato descritto come configurare il servizio Device Provisioning in hub IoT di Azure per effettuare automaticamente il provisioning dei dispositivi per l'hub IoT. Questa esercitazione include le istruzioni per configurare il dispositivo durante il processo di produzione, in modo che sia possibile configurare il servizio Device Provisioning per il dispositivo in base al relativo [modulo di protezione hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security) e il dispositivo possa connettersi al servizio Device Provisioning la prima volta che viene avviato. Questa esercitazione illustra i processi per:

> [!div class="checklist"]
> * Selezionare un modulo di protezione hardware
> * Compilare l'SDK client di Device Provisioning per il modulo di protezione hardware selezionato
> * Estrarre gli elementi di sicurezza
> * Impostare la configurazione del servizio Device Provisioning nel dispositivo

## <a name="prerequisites"></a>Prerequisiti

Prima di procedere, creare l'istanza del servizio Device Provisioning e un hub IoT seguendo le istruzioni indicate nell'esercitazione [Set up cloud for device provisioning](./tutorial-set-up-cloud.md) (Configurare il cloud per il provisioning dei dispositivi).


## <a name="select-a-hardware-security-module"></a>Selezionare un modulo di protezione hardware

L'[SDK client del servizio Device Provisioning](https://github.com/Azure/azure-iot-device-auth/tree/master/dps_client) offre il supporto per due tipi di moduli di protezione hardware (HSM): 

- [TPM (Trusted Platform Module)](https://en.wikipedia.org/wiki/Trusted_Platform_Module).
    - Il modulo TPM è uno standard stabilito per la maggior parte delle piattaforme per dispositivi basati su Windows, nonché per alcuni dispositivi basati su Linux/Ubuntu. In qualità di produttore di dispositivi, è possibile scegliere questo modulo se nei dispositivi è installato uno di questi sistemi operativi e se si sta cercando uno standard stabilito per i moduli di protezione hardware. Con i chip TPM è possibile registrare solo ogni dispositivo individualmente nel servizio Device Provisioning. Ai fini dello sviluppo è possibile usare il simulatore TPM nel computer di sviluppo Windows o Linux.

- Moduli di protezione hardware basati su [X.509](https://cryptography.io/en/latest/x509/). 
    - I moduli di protezione hardware basati su X.509 sono chip relativamente più recenti e attualmente in fase di evoluzione nei chip Microsoft su RIoT o DICE che implementano i certificati X.509. I chip X.509 consentono di eseguire registrazioni in blocco nel portale. Supportano anche determinati sistemi operativi diversi da Windows, ad esempio embedOS. Ai fini dello sviluppo, l'SDK client del servizio Device Provisioning supporta il simulatore di dispositivi X.509. 

In qualità di produttore di dispositivi, è necessario selezionare i moduli/chip di protezione hardware che si basano su uno dei tipi precedenti. Altri tipi di moduli di protezione hardware non sono attualmente supportati nell'SDK client del servizio Device Provisioning.   


## <a name="build-device-provisioning-client-sdk-for-the-selected-hsm"></a>Compilare l'SDK client di Device Provisioning per il modulo di protezione hardware selezionato

L'SDK client del servizio Device Provisioning aiuta a implementare il meccanismo di sicurezza selezionato nel software. La procedura seguente mostra come usare l'SDK per il chip del modulo di protezione hardware selezionato:

1. Se è stata seguita la [Guida introduttiva per creare un dispositivo simulato](./quick-create-simulated-device.md), la configurazione è pronta per la compilazione dell'SDK. In caso contrario, seguire i primi quattro passaggi nella sezione intitolata [Preparare l'ambiente di sviluppo](./quick-create-simulated-device.md#setupdevbox). Questi passaggi clonano il repository GitHub per l'SDK client del servizio Device Provisioning e installano lo strumento di compilazione `cmake`. 

1. Compilare l'SDK per il tipo di modulo di protezione hardware selezionato per il dispositivo interessato, digitando al prompt dei comandi uno dei comandi seguenti:
    - Per i dispositivi TPM:
        ```cmd/sh
        cmake -Ddps_auth_type=tpm ..
        ```

    - Per il simulatore TPM:
        ```cmd/sh
        cmake -Ddps_auth_type=tpm_simulator ..
        ```

    - Per i dispositivi e il simulatore X.509:
        ```cmd/sh
        cmake -Ddps_auth_type=x509 ..
        ```

1. L'SDK include il supporto predefinito per i dispositivi che eseguono implementazioni Windows o Ubuntu per i moduli di protezione hardware TPM e X.509. Per questi moduli di protezione hardware supportati, passare alla sezione intitolata [Estrarre gli elementi di sicurezza](#extractsecurity) più avanti. 
 
## <a name="support-custom-tpm-and-x509-devices"></a>Supportare i dispositivi TPM e X.509 personalizzati

L'SDK client del servizio Device Provisioning non offre supporto predefinito per alcun dispositivo TPM e X.509 che non esegue Windows o Ubuntu. Per tali dispositivi è necessario scrivere codice personalizzato per il chip del modulo di protezione hardware specifico, come illustrato nei passaggi seguenti:

### <a name="develop-your-custom-repository"></a>Sviluppare il repository personalizzato

1. Sviluppare un repository GitHub per accedere al modulo di protezione hardware. Questo progetto richiede che si produca una raccolta statica ad uso dell'SDK del servizio Device Provisioning.
1. La raccolta deve implementare le funzioni definite nel file di intestazione seguente: a. Per il modulo TPM personalizzato, implementare le funzioni definite in `\azure-iot-device-auth\dps_client\adapters\custom_hsm_tpm_impl.h`.
    b. Per il modulo X.509 personalizzato, implementare le funzioni definite in `\azure-iot-device-auth\dps_client\adapters\custom_hsm_x509_impl.h`. 
1. Il repository del modulo di protezione hardware deve inoltre contenere un file `CMakeLists.txt` nella directory radice per il repository che deve essere creato.

### <a name="integrate-with-the-device-provisioning-service-client"></a>Integrare con il client del servizio Device Provisioning

Dopo la creazione della raccolta è possibile passare all'SDK per C IoThub e inserire il repository:

1. Specificare il repository GitHub del modulo di protezione hardware personalizzato, il percorso della raccolta e il relativo nome nel comando cmake seguente:
    ```cmd/sh
    cmake -Ddps_auth_type=<custom_hsm> -Ddps_hsm_custom_repo=<github_repo_name> -Ddps_hsm_custom_lib=<path_and_name_of library> <PATH_TO_AZURE_IOT_SDK>
    ```
   Sostituire `<custom_hsm>` nel comando con `tpm` o `x509`. Questo comando crea un indicatore per il repository del modulo di protezione hardware personalizzato all'interno della directory `cmake`. Il modulo di protezione hardware personalizzato deve ancora essere basato sui meccanismi di sicurezza TPM o X.509.

1. Aprire l'SDK in Visual Studio e compilarlo. 

    - Il processo di compilazione clona il repository personalizzato e compila la raccolta.
    - L'SDK tenterà di collegarsi al modulo di protezione hardware personalizzato definito nel comando cmake.

1. Eseguire l'esempio `\azure-iot-device-auth\dps_client\samples\dps_client_sample\dps_client_sample.c` per verificare se il modulo di protezione hardware è stato implementato correttamente.

<a id="extractsecurity"></a>
## <a name="extract-the-security-artifacts"></a>Estrarre gli elementi di sicurezza

Il passaggio successivo consiste nell'estrarre gli elementi di sicurezza per il modulo di protezione hardware nel dispositivo.

1. Per un dispositivo TPM, è necessario individuare la **chiave di verifica autenticità** associata dal produttore del chip TPM. È possibile derivare un **ID registrazione** univoco per il dispositivo TPM mediante l'uso di hash nella chiave di verifica autenticità. 
2. Per un dispositivo X.509, è necessario ottenere i certificati rilasciati al dispositivo o ai dispositivi: i certificati di entità di fine per le registrazioni dei dispositivi singoli e i certificati radice per le registrazioni di gruppo di dispositivi.

Questi elementi di sicurezza sono necessari per registrare i dispositivi nel servizio Device Provisioning. Il servizio di provisioning attende quindi che uno di questi dispositivi venga ad un certo punto avviato e connesso. Per informazioni su come usare questi elementi di sicurezza per creare le registrazioni, vedere [How to manage device enrollments](how-to-manage-enrollments.md) (Come gestire le registrazioni dei dispositivi). 

Quando il dispositivo viene avviato per la prima volta, l'SDK client interagisce con il chip per estrarre gli elementi di sicurezza dal dispositivo e verifica la registrazione con il servizio Device Provisioning. 


## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Impostare la configurazione del servizio Device Provisioning nel dispositivo

L'ultimo passaggio del processo di produzione del dispositivo consiste nello scrivere un'applicazione che usa l'SDK client del servizio Device Provisioning per registrare il dispositivo con il servizio. Questo SDK include le API seguenti da usare per le applicazioni:

```C
typedef void(*DPS_REGISTER_DEVICE_CALLBACK)(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* user_context); // Callback to notify user of device registration results.
DPS_CLIENT_LL_HANDLE DPS_Client_LL_Create (const char* dps_uri, const char* scope_id, DPS_TRANSPORT_PROVIDER_FUNCTION protocol, DPS_CLIENT_ON_ERROR_CALLBACK on_error_callback, void* user_ctx); // Creates the IOTHUB_DPS_LL_HANDLE to be used in subsequent calls.
void DPS_Client_LL_Destroy(DPS_CLIENT_LL_HANDLE handle); // Frees any resources created by the IoTHub Device Provisioning Service module.
DPS_RESULT DPS_LL_Register_Device(DPS_LL_HANDLE handle, DPS_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, DPS_CLIENT_REGISTER_STATUS_CALLBACK status_cb, void* status_ctx); // Registers a device that has been previously registered with Device Provisioning Service
void DPS_Client_LL_DoWork(DPS_LL_HANDLE handle); // Processes the communications with the Device Provisioning Service and calls any user callbacks that are required.
```

Ricordarsi di inizializzare le variabili `dps_uri` e `dps_scope_id` come menzionato nella sezione [Simulate first boot sequence](./quick-create-simulated-device.md#firstbootsequence) (Simulare la prima sequenza di avvio del dispositivo) di questa guida introduttiva prima di usarle. L'API di registrazione del client Device Provisioning `DPS_Client_LL_Create` esegue la connessione al servizio Device Provisioning globale. L'*ambito ID* viene generato dal servizio e garantisce l'univocità. Non può essere modificato e viene usato per identificare in modo univoco gli ID di registrazione. Il parametro `iothub_uri` consente all'API di registrazione del client per l'hub IoT `IoTHubClient_LL_CreateFromDeviceAuth` di eseguire la connessione all'hub IoT corretto. 


Queste API aiutano il dispositivo a connettersi e a registrarsi al servizio Device Provisioning quando viene avviato, a ottenere le informazioni sull'hub IoT e a connettersi all'hub. Il file `dps_client/samples/dps_client_sample/dps_client_sample.c` mostra come usare queste API. È necessario, in generale, creare il framework seguente per la registrazione del client:

```C
static const char* dps_uri = "global.azure-devices-provisioning.net";
static const char* dps_scope_id = "[ID scope for your provisioning service]";
...
static void register_callback(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* context)
{
    USER_DEFINED_INFO* user_info = (USER_DEFINED_INFO *)user_context;
    ...
    user_info. reg_complete = 1;
}
static void registation_status(DPS_REGISTRATION_STATUS reg_status, void* user_context)
{
}
int main()
{
    ...    
    security_device_init(); // initialize your HSM 

    DPS_CLIENT_LL_HANDLE handle = DPS_Client_LL_Create(dps_uri, dps_scope_id, dps_transport, on_dps_error_callback, &user_info); // Create your DPS client

    if (DPS_Client_LL_Register_Device(handle, register_callback, &user_info, register_status, &user_info) == IOTHUB_DPS_OK) {
        do {
            // The dps_register_callback is called when registration is complete or fails
            DPS_Client_LL_DoWork(handle);
        } while (user_info.reg_complete == 0);
    }
    DPS_Client_LL_Destroy(handle); // Clean up the DPS client
    ...
    iothub_client = IoTHubClient_LL_CreateFromDeviceAuth(user_info.iothub_uri, user_info.device_id, transport); // Create your IoT hub client and connect to your hub
    ...
}
```

È possibile perfezionare l'applicazione di registrazione del client del servizio Device Provisioning che usa all'inizio un dispositivo simulato usando una configurazione di prova del servizio. Dopo avere verificato il corretto funzionamento dell'applicazione nell'ambiente di test, è possibile compilarla per il dispositivo specifico e copiare il file eseguibile nell'immagine del dispositivo. Non avviare ancora il dispositivo. È necessario [registrare il dispositivo con il servizio Device Provisioning](./tutorial-provision-device-to-hub.md#enrolldevice) prima di avviarlo. Vedere i passaggi successivi per informazioni su questo processo. 

## <a name="clean-up-resources"></a>Pulire le risorse

I servizi Device Provisioning e per l'hub IoT sono a questo punto configurati nel portale. Se si desidera interrompere la configurazione del provisioning dei dispositivi e/o eseguirla in un secondo momento usando uno di questi servizi, è consigliabile arrestarli per evitare costi non necessari.

1. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare il servizio Device Provisioning. Nella parte superiore del pannello **Tutte le risorse** fare clic su **Elimina**.  
1. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare l'hub IoT. Nella parte superiore del pannello **Tutte le risorse** fare clic su **Elimina**.  


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Selezionare un modulo di protezione hardware
> * Compilare l'SDK client di Device Provisioning per il modulo di protezione hardware selezionato
> * Estrarre gli elementi di sicurezza
> * Impostare la configurazione del servizio Device Provisioning nel dispositivo

Passare all'esercitazione successiva per informazioni su come effettuare il provisioning del dispositivo per l'hub IoT registrandolo nel servizio Device Provisioning in hub IoT di Azure per il provisioning automatico.

> [!div class="nextstepaction"]
> [Effettuare il provisioning del dispositivo nell'hub IoT](tutorial-provision-device-to-hub.md)


