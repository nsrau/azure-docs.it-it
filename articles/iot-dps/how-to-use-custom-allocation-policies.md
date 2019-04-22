---
title: Come usare i criteri di allocazione personalizzati con il servizio Device Provisioning in hub IoT di Azure | Documentazione Microsoft
description: Come usare i criteri di allocazione personalizzati con il servizio Device Provisioning in hub IoT di Azure
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: f0eb2f7358e8fb1564275e1de510f302d2eef90b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59500941"
---
# <a name="how-to-use-custom-allocation-policies"></a>Come usare i criteri di allocazione personalizzati


I criteri di allocazione personalizzati offrono maggiore controllo sulle modalità di assegnazione dei dispositivi a un hub IoT. A tale scopo viene usato un codice personalizzato in una [funzione di Azure](../azure-functions/functions-overview.md) per assegnare dispositivi a un hub IoT. Il servizio Device Provisioning chiama il codice della funzione di Azure per fornire tutte le informazioni rilevanti sul dispositivo e la registrazione. Viene eseguito il codice della funzione, che restituisce le informazioni dell'hub IoT usate per il provisioning del dispositivo.

Tramite i criteri di allocazione personalizzati è possibile definire i propri criteri di allocazione qualora quelli forniti dal servizio di Device Provisioning non soddisfino i requisiti dello scenario.

Ad esempio, si potrebbe voler esaminare il certificato in uso su un dispositivo durante il provisioning e assegnare il dispositivo a un hub IoT basato su una proprietà del certificato. È possibile che le informazioni siano archiviate in un database per i dispositivi e che sia necessario eseguire query sul database per determinare l'hub IoT a cui assegnare un dispositivo.


Questo articolo illustra i criteri di allocazione personalizzati che usano una funzione di Azure scritta in C#. Vengono creati due nuovi hub IoT per rappresentare una *divisione toaster di Contoso* e una *divisione pompe di calore di Contoso*. Per essere accettati per il provisioning, i dispositivi che richiedono questo servizio devono avere un ID di registrazione con uno dei suffissi di seguito:

- **-contoso-tstrsd-007**: divisione toaster di Contoso
- **-contoso-hpsd-088**: divisione pompe di calore di Contoso

Il provisioning dei dispositivi verrà effettuato sulla base di uno di questi suffissi richiesti per l'ID di registrazione. Questi dispositivi verranno simulati usando un esempio di provisioning incluso in [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

In questo articolo si eseguiranno i seguenti passaggi:

* Usare l'interfaccia della riga di comando di Azure per creare due hub IoT per le divisioni di Contoso (**divisione toaster di Contoso** e **divisione pompe di calore di Contoso**)
* Creare una nuova registrazione di gruppo con una funzione di Azure per i criteri di allocazione personalizzati
* Creare le chiavi di dispositivo per due simulazioni di dispositivo.
* Configurare un ambiente di sviluppo per Azure IoT C SDK
* Simulare i dispositivi per verificare che il provisioning venga effettuato in base al codice di esempio dei criteri di allocazione personalizzati


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Completamento della guida introduttiva per [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md).
* Visual Studio 2015 oppure [Visual Studio 2017](https://www.visualstudio.com/vs/) con il carico di lavoro [Sviluppo di applicazioni desktop con C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) abilitato.
* La versione più recente di [Git](https://git-scm.com/download/) installata.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Creare due hub IoT delle divisioni

In questa sezione si userà Azure Cloud Shell per creare due nuovi hub IoT per rappresentare la **divisione toaster di Contoso** e la **divisione pompe di calore di Contoso**.

1. Usare Azure Cloud Shell per creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

    L'esempio seguente crea un gruppo di risorse denominato *contoso-us-resource-group* nell'area *eastus*. È consigliabile usare questo gruppo per tutte le risorse create in questo articolo. Questo approccio faciliterà la pulizia una volta completate le operazioni.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Usare Azure Cloud Shell per creare l'hub IoT della **divisione toaster di Contoso** con il comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). L'hub IoT verrà aggiunto a *contoso-us-resource-group*.

    L'esempio seguente crea un hub IoT denominato *contoso-toasters-hub-1098* nella località *eastus*. È necessario usare il proprio nome univoco dell'hub. Creare il proprio suffisso nel nome dell'hub da sostituire a **1098**. Il codice di esempio per i criteri di allocazione personalizzati richiede `-toasters-` nel nome dell'hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Il completamento del comando può richiedere alcuni minuti.

3. Usare Azure Cloud Shell per creare l'hub IoT della **divisione pompe di calore di Contoso** con il comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Questo hub IoT verrà aggiunto anche a *contoso-us-resource-group*.

    L'esempio seguente crea un hub IoT denominato *contoso-heatoumps-hub-1098* nella località *eastus*. È necessario usare il proprio nome univoco dell'hub. Creare il proprio suffisso nel nome dell'hub da sostituire a **1098**. Il codice di esempio per i criteri di allocazione personalizzati richiede `-heatpumps-` nel nome dell'hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Il completamento del comando può richiedere alcuni minuti.




## <a name="create-the-enrollment"></a>Creare la registrazione

In questa sezione si creerà un nuovo gruppo di registrazioni che usa i criteri di allocazione personalizzati. Per semplicità, in questo articolo viene usata l'[attestazione con chiave simmetrica](concepts-symmetric-key-attestation.md) per la registrazione. Per una soluzione più sicura è consigliabile usare l'[attestazione del certificato X.509](concepts-security.md#x509-certificates) con una catena di certificati.

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'istanza del servizio Device Provisioning.

2. Selezionare la scheda **Gestisci registrazioni**, quindi fare clic sul pulsante **Aggiungi gruppo di registrazioni** nella parte superiore della pagina. 

3. In **Aggiungi gruppo di registrazioni** immettere le informazioni seguenti e fare clic sul pulsante **Salva**.

    **Nome del gruppo**: immettere **contoso-custom-allocated-devices**.

    **Tipo di attestazione**: selezionare **Chiave simmetrica**.

    **Genera chiavi automaticamente**: questa casella di controllo dovrebbe essere già selezionata.

    **Selezionare la modalità di assegnazione dei dispositivi agli hub**: selezionare **Personalizzata (usa funzione di Azure)**.

    ![Aggiungere il gruppo di registrazioni dell'allocazione personalizzata per l'attestazione con chiave simmetrica](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)


4. In **Aggiungi gruppo di registrazioni** fare clic su **Collega un nuovo hub IoT** per collegare entrambi gli hub IoT delle divisioni. 

    È necessario eseguire questo passaggio per entrambi gli hub IoT delle divisioni.

    **Sottoscrizione** se si dispone di più sottoscrizioni, scegliere quella in cui sono stati creati gli hub IoT delle divisioni.

    **Hub IoT**: selezionare uno degli hub delle divisioni creato.

    **Criteri di accesso**: scegliere **iothubowner**.

    ![Collegare gli hub IoT delle divisioni al servizio di provisioning](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)


5. Una volta collegati entrambi gli hub IoT delle divisioni, in **Aggiungi gruppo di registrazioni** è necessario selezionarli come gruppo dell'hub IoT per il gruppo di registrazioni, come mostrato di seguito:

    ![Creare il gruppo dell'hub della divisione per la registrazione](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)


6. In **Aggiungi gruppo di registrazioni** scorrere verso il basso fino alla sezione **Seleziona funzione di Azure** e fare clic su **Crea una nuova app per le funzioni**.

7. Nella pagina di creazione dell'**app per le funzioni** che si apre, immettere le seguenti impostazioni per la nuova funzione e fare clic su **Crea**:

    **Nome dell'app**: immettere un nome univoco per l'app per le funzioni. **contoso-function-app-1098** viene visualizzato come esempio.

    **Gruppo di risorse**: selezionare **Usa esistente** e **contoso-us-resource-group** per tenere insieme tutte le risorse create in questo articolo.

    **Application Insights**: per questo esercizio è possibile disattivare questa funzionalità.

    ![Creare l'app per le funzioni](./media/how-to-use-custom-allocation-policies/function-app-create.png)


8. Tornando alla pagina **Aggiungi gruppo di registrazioni**, assicurarsi che la nuova app per le funzioni sia selezionata. È possibile che sia necessario selezionare nuovamente la sottoscrizione per aggiornare l'elenco dell'app per le funzioni.

    Dopo aver selezionato la nuova app per le funzioni, fare clic su **Crea una nuova funzione**.

    ![Creare l'app per le funzioni](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    verrà aperta la nuova app per le funzioni.

9. Nell'app per le funzioni fare clic per creare una nuova funzione

    ![Creare l'app per le funzioni](./media/how-to-use-custom-allocation-policies/new-function.png)

    Per la nuova funzione, usare le impostazioni predefinite per creare un nuovo **Webhook + API** usando il linguaggio di programmazione **CSharp**. Fare clic su **Crea questa funzione**.

    Questa operazione crea una nuova funzione C# denominata **HttpTriggerCSharp1**.

10. Sostituire il codice della nuova funzione C# con il codice riportato di seguito, quindi fare clic su **Salva**:    

    ```C#
    #r "Newtonsoft.Json"
    using System.Net;
    using System.Text;
    using Newtonsoft.Json;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // Just some diagnostic logging
        log.Info("C# HTTP trigger function processed a request.");
        log.Info("Request.Content:...");    
        log.Info(req.Content.ReadAsStringAsync().Result);

        // Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.Info("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.Info("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.Info("Unknown device registration");
                }
            }
        }

        return (fail)
            ? req.CreateResponse(HttpStatusCode.BadRequest, message)
            : new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent(JsonConvert.SerializeObject(obj, Formatting.Indented), Encoding.UTF8, "application/json")
            };
    }    

    public class DeviceTwinObj
    {
        public string deviceId {get; set;}
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public string IoTHub {get; set;}
        public DeviceTwinObj initialTwin {get; set;}
        public string[] linkedHubs {get; set;}
        public string enrollment {get; set;}
    }
    ```


11. Tornare alla pagina **Aggiungi gruppo di registrazioni** e assicurarsi di aver selezionato la nuova funzione. È possibile che sia necessario selezionare nuovamente l'app per le funzioni per aggiornare l'elenco delle funzioni.

    Dopo aver selezionato la nuova funzione, salvare il gruppo di registrazioni facendo clic su **Salva**.

    ![Salvare infine il gruppo di registrazioni](./media/how-to-use-custom-allocation-policies/save-enrollment.png)


12. Dopo aver salvato la registrazione, aprirla nuovamente e annotare la **Chiave primaria**. È necessario salvare la registrazione prima di poter generare le chiavi. Questa chiave verrà usata per generare chiavi univoche di dispositivo per simulare i dispositivi in un secondo momento.


## <a name="derive-unique-device-keys"></a>Derivare le chiavi univoche di dispositivo

In questa sezione si creeranno due chiavi univoche di dispositivo. Una chiave verrà utilizzata per simulare un dispositivo toaster, l'altra chiave per simulare un dispositivo pompa di calore.

Per generare la chiave di dispositivo, si userà la **Chiave primaria** precedentemente annotata per calcolare la [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) dell'ID di registrazione del dispositivo per ciascun dispositivo e si convertirà il risultato nel formato Base64. Per altre informazioni sulla creazione di chiavi di dispositivo derivate con i gruppi di registrazione, vedere la sezione sulle registrazioni di gruppo dell'articolo [Attestazione con chiave simmetrica](concepts-symmetric-key-attestation.md).

Per l'esempio di questo articolo, usare i due ID di registrazione del dispositivo seguenti e calcolare una chiave di dispositivo per entrambi i dispositivi. Entrambi gli ID di registrazione presentano un suffisso valido per il funzionamento con il codice di esempio per i criteri di allocazione personalizzati:

- **breakroom499-contoso-tstrsd-007**
- **mainbuilding167-contoso-hpsd-088**

#### <a name="linux-workstations"></a>Workstation di Linux

Se si sta usando una workstation di Linux, è possibile usare openssl per generare le chiavi di dispositivo derivate come illustrato nell'esempio seguente.

1. Sostituire il valore della **CHIAVE** con la **Chiave primaria** annotata in precedenza.

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


#### <a name="windows-based-workstations"></a>Workstation basate su Windows

Se si usan una workstation basata su Windows, è possibile usare PowerShell per generare le chiavi di dispositivo derivate come illustrato nell'esempio seguente.

1. Sostituire il valore della **CHIAVE** con la **Chiave primaria** annotata in precedenza.

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


I dispositivi simulati useranno le chiavi di dispositivo derivate con ogni ID di registrazione per eseguire l'attestazione con chiave simmetrica.




## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Preparare un ambiente di sviluppo per Azure IoT C SDK

In questa sezione si preparerà un ambiente di sviluppo usato per compilare [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). L'SDK include il codice di esempio per il dispositivo simulato. Il dispositivo simulato tenterà di effettuare il provisioning durante la sequenza di avvio del dispositivo.

Questa sezione si riferisce a una workstation basata su Windows. Per un esempio di Linux, vedere la configurazione delle macchine virtuali in [Come effettuare il provisioning per la multi-tenancy](how-to-provision-multitenant.md).

1. Scaricare il [sistema di compilazione CMake](https://cmake.org/download/).

    È importante che nel computer siano installati i prerequisiti di Visual Studio (Visual Studio e carico di lavoro 'Sviluppo di applicazioni desktop con C++') **prima** di avviare l'installazione di `CMake`. Quando i prerequisiti sono pronti e il download è stato verificato, installare il sistema di compilazione CMake.

2. Aprire un prompt dei comandi o la shell Git Bash. Eseguire il comando seguente per clonare il repository GitHub Azure IoT C SDK:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Il completamento di questa operazione richiederà alcuni minuti.


3. Creare una sottodirectory `cmake` nella directory radice del repository Git e passare a tale cartella. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Eseguire il comando seguente che compila una versione dell'SDK specifica per la piattaforma di sviluppo client. Verrà generata una soluzione di Visual Studio per il dispositivo simulato nella directory `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Se `cmake` non trova il compilatore C++, si potrebbero verificare errori di compilazione durante l'esecuzione del comando precedente. In tal caso, provare a eseguire questo comando nel [prompt dei comandi di Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Al termine della compilazione, le ultime righe di output saranno simili all'output seguente:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```




## <a name="simulate-the-devices"></a>Simulare i dispositivi

In questa sezione si aggiornerà un esempio di provisioning denominato **prov\_dev\_client\_sample** che si trova in Azure IoT C SDK precedentemente configurato. 

Questo codice di esempio simula una sequenza di avvio di dispositivo che invia la richiesta di provisioning all'istanza del servizio Device Provisioning. La sequenza di avvio riconoscerà il dispositivo toaster e lo assegnerà all'hub IoT usando i criteri di allocazione personalizzati.

1. Nel portale di Azure selezionare la scheda **Panoramica** per il servizio Device Provisioning e prendere nota del valore di **_Ambito ID_**.

    ![Estrarre le informazioni dell'endpoint del servizio Device Provisioning dal pannello del portale](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. In Visual Studio aprire il file di soluzione **azure_iot_sdks.sln** generato in precedenza tramite l'esecuzione di CMake. Il file di soluzione deve trovarsi nel percorso seguente:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Nella finestra *Esplora soluzioni* di Visual Studio passare alla cartella **Provision\_Samples**. Espandere il progetto di esempio denominato **prov\_dev\_client\_sample**. Espandere **File di origine** e aprire **prov\_dev\_client\_sample.c**.

4. Trovare la costante `id_scope` e sostituire il valore con il valore **Ambito ID** copiato in precedenza. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Trovare la definizione per la funzione `main()` nello stesso file. Assicurarsi che la variabile`hsm_type` sia impostata su `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` come illustrato di seguito:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Fare clic con il pulsante destro del mouse sul progetto **prov\_dev\_client\_sample** e scegliere **Imposta come progetto di avvio**. 


#### <a name="simulate-the-contoso-toaster-device"></a>Simulare il dispositivo toaster di Contoso

1. Per simulare il dispositivo toaster, individuare la chiamata a `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c** che è impostata come commento.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Rimuovere il commento dalla chiamata alla funzione e sostituire i valori di segnaposto (incluse le parentesi angolari) con l'ID registrazione del toaster e la chiave di dispositivo derivata generata in precedenza. Il valore di chiave **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** visualizzato di seguito viene fornito solo come esempio.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```
   
    Salvare il file.

2. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire la soluzione. Nella richiesta di ricompilare il progetto fare clic su **Sì** per ricompilare il progetto prima dell'esecuzione.

    Il seguente output è un esempio di dispositivo toaster simulato che si sta avviando correttamente e connettendo all'istanza del servizio di provisioning che i criteri di allocazione personalizzati assegneranno all'hub IoT dei toaster:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```


#### <a name="simulate-the-contoso-heat-pump-device"></a>Simulare il dispositivo pompa di calore di Contoso

1. Per simulare il dispositivo pompa di calore, aggiornare di nuovo la chiamata a `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c** con l'ID registrazione del dispositivo pompa di calore e la chiave di dispositivo derivata generata in precedenza. Il valore di chiave **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** visualizzato di seguito viene fornito solo come esempio.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```
   
    Salvare il file.

2. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire la soluzione. Nella richiesta di ricompilare il progetto fare clic su **Sì** per ricompilare il progetto prima dell'esecuzione.

    Il seguente output è un esempio di dispositivo pompa di calore simulato che si sta avviando correttamente e connettendo all'istanza del servizio di provisioning che i criteri di allocazione personalizzati assegneranno all'hub IoT delle pompe di calore di Contoso:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```


## <a name="troubleshooting-custom-allocation-policies"></a>Risoluzione dei problemi relativi ai criteri di allocazione personalizzati

La tabella seguente illustra gli scenari previsti e i codici di errore dei risultati che potrebbero verificarsi. Usare questa tabella come supporto per la risoluzione dei problemi relativi ai criteri di allocazione personalizzati con le Funzioni di Azure.


| Scenario | Risultato della registrazione dal servizio di provisioning | Effettuare il provisioning dei risultati SDK |
| -------- | --------------------------------------------- | ------------------------ |
| Il webhook restituisce 200 OK con 'iotHubHostName' impostato su un nome host dell'hub IoT valido | Stato del risultato: Assegnato  | L'SDK restituisce PROV_DEVICE_RESULT_OK insieme alle informazioni dell'hub |
| Il webhook restituisce 200 OK con 'iotHubHostName' incluso nella risposta ma impostato su una stringa vuota o Null | Stato del risultato: Failed<br><br> Codice errore: CustomAllocationIotHubNotSpecified (400208) | L'SDK restituisce PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Il webhook restituisce 401 - Non autorizzato | Stato del risultato: Failed<br><br>Codice errore: CustomAllocationUnauthorizedAccess (400209) | L'SDK restituisce PROV_DEVICE_RESULT_UNAUTHORIZED |
| È stata creata una registrazione singola per disabilitare il dispositivo | Stato del risultato: Disabled | L'SDK restituisce PROV_DEVICE_RESULT_DISABLED |
| Il webhook restituisce il codice di errore > = 429 | L'orchestrazione di DPS effettuerà diversi nuovi tentativi. I criteri di ripetizione sono attualmente:<br><br>&nbsp;&nbsp;- Numero di tentativi: 10<br>&nbsp;&nbsp;- Intervallo iniziale: 1 sec<br>&nbsp;&nbsp;- Incremento: 9 sec | L'SDK ignorerà l'errore e invierà un altro messaggio per l'ottenimento dello stato nel periodo specificato |
| Il webhook restituisce qualsiasi altro codice di stato | Stato del risultato: Failed<br><br>Codice errore: CustomAllocationFailed (400207) | L'SDK restituisce PROV_DEVICE_RESULT_DEV_AUTH_ERROR |


## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare a usare le risorse create in questo articolo, è possibile mantenerle. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create in questo articolo per evitare inutili spese.

Questi passaggi presuppongono che tutte le risorse in questo articolo siano state create come indicato nello stesso gruppo di risorse denominato **contoso-us-resource-group**.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se si è creato l'hub IoT all'interno di un gruppo di risorse esistente che contiene risorse che si vogliono conservare, eliminare solo la risorsa dell'hub IoT invece di eliminare il gruppo di risorse.
>

Per eliminare il gruppo di risorse per nome:

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.

2. Nella casella di testo **Filtra per nome...** digitare il nome del gruppo di risorse che contiene le risorse di interesse, **contoso-us-resource-group**. 

3. A destra del gruppo di risorse nell'elenco dei risultati fare clic su **...** quindi su **Elimina gruppo di risorse**.

4. Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere di nuovo il nome del gruppo di risorse per confermare e fare clic su **Elimina**. Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su altre Reprovisioning, vedere [dispositivo Hub IoT un nuovo provisioning concetti](concepts-device-reprovision.md) 
- Per informazioni su ulteriori il deprovisioning di utenti, vedere [come effettuare il deprovisioning di dispositivi che sono stati precedentemente sottoposti a provisioning](how-to-unprovision-devices.md) 











