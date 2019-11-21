---
title: Custom allocation policies with Azure IoT Hub Device Provisioning Service
description: Come usare i criteri di allocazione personalizzati con il servizio Device Provisioning in hub IoT di Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 2a17cc6c9f2211de31d4551bd12e6c832d4eee38
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228744"
---
# <a name="how-to-use-custom-allocation-policies"></a>Come usare i criteri di allocazione personalizzati

I criteri di allocazione personalizzati offrono maggiore controllo sulle modalità di assegnazione dei dispositivi a un hub IoT. A tale scopo viene usato un codice personalizzato in una [funzione di Azure](../azure-functions/functions-overview.md) per assegnare dispositivi a un hub IoT. Il servizio Device Provisioning chiama il codice della funzione di Azure per fornire tutte le informazioni rilevanti sul dispositivo e la registrazione. Viene eseguito il codice della funzione, che restituisce le informazioni dell'hub IoT usate per il provisioning del dispositivo.

By using custom allocation policies, you define your own allocation policies when the policies provided by the Device Provisioning Service don't meet the requirements of your scenario.

For example, maybe you want to examine the certificate a device is using during provisioning and assign the device to an IoT hub based on a certificate property. Or, maybe you have information stored in a database for your devices and need to query the database to determine which IoT hub a device should be assigned to.

Questo articolo illustra i criteri di allocazione personalizzati che usano una funzione di Azure scritta in C#. Vengono creati due nuovi hub IoT per rappresentare una *divisione toaster di Contoso* e una *divisione pompe di calore di Contoso*. Per essere accettati per il provisioning, i dispositivi che richiedono questo servizio devono avere un ID di registrazione con uno dei suffissi di seguito:

* **-contoso-tstrsd-007**: divisione toaster di Contoso
* **-contoso-hpsd-088**: divisione pompe di calore di Contoso

Il provisioning dei dispositivi verrà effettuato sulla base di uno di questi suffissi richiesti per l'ID di registrazione. Questi dispositivi verranno simulati usando un esempio di provisioning incluso in [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

You perform the following steps in this article:

* Usare l'interfaccia della riga di comando di Azure per creare due hub IoT per le divisioni di Contoso (**divisione toaster di Contoso** e **divisione pompe di calore di Contoso**)
* Creare una nuova registrazione di gruppo con una funzione di Azure per i criteri di allocazione personalizzati
* Creare le chiavi di dispositivo per due simulazioni di dispositivo.
* Configurare un ambiente di sviluppo per Azure IoT C SDK
* Simulate the devices and verify that they are provisioned according to the example code in the custom allocation policy

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 o versione successiva con il carico di lavoro [Sviluppo di applicazioni desktop con C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) abilitato.
* La versione più recente di [Git](https://git-scm.com/download/) installata.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Create the provisioning service and two divisional IoT hubs

In this section, you use the Azure Cloud Shell to create a provisioning service and two IoT hubs representing the **Contoso Toasters Division** and the **Contoso Heat Pumps division**.

> [!TIP]
> The commands used in this article create the provisioning service and other resources in the West US location. We recommend that you create your resources in the region nearest you that supports Device Provisioning Service. Per un elenco delle località disponibili, è possibile eseguire il comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` oppure passare alla pagina [Stato di Azure](https://azure.microsoft.com/status/) e cercare "Servizio Device Provisioning". In commands, locations can be specified either in one word or multi-word format; for example: westus, West US, WEST US, etc. The value is not case sensitive. Se si usa il formato a più parole per specificare la località, racchiudere il valore tra virgolette, ad esempio `-- location "West US"`.
>

1. Usare Azure Cloud Shell per creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

    The following example creates a resource group named *contoso-us-resource-group* in the *westus* region. È consigliabile usare questo gruppo per tutte le risorse create in questo articolo. This approach will make clean up easier after you're finished.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Use the Azure Cloud Shell to create a device provisioning service with the [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) command. The provisioning service will be added to *contoso-us-resource-group*.

    The following example creates a provisioning service named *contoso-provisioning-service-1098* in the *westus* location. You must use a unique service name. Make up your own suffix in the service name in place of **1098**.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Il completamento del comando può richiedere alcuni minuti.

3. Usare Azure Cloud Shell per creare l'hub IoT della **divisione toaster di Contoso** con il comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). L'hub IoT verrà aggiunto a *contoso-us-resource-group*.

    The following example creates an IoT hub named *contoso-toasters-hub-1098* in the *westus* location. You must use a unique hub name. Creare il proprio suffisso nel nome dell'hub da sostituire a **1098**. Il codice di esempio per i criteri di allocazione personalizzati richiede `-toasters-` nel nome dell'hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Il completamento del comando può richiedere alcuni minuti.

4. Usare Azure Cloud Shell per creare l'hub IoT della **divisione pompe di calore di Contoso** con il comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Questo hub IoT verrà aggiunto anche a *contoso-us-resource-group*.

    The following example creates an IoT hub named *contoso-heatpumps-hub-1098* in the *westus* location. You must use a unique hub name. Creare il proprio suffisso nel nome dell'hub da sostituire a **1098**. Il codice di esempio per i criteri di allocazione personalizzati richiede `-heatpumps-` nel nome dell'hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Il completamento del comando può richiedere alcuni minuti.

## <a name="create-the-custom-allocation-function"></a>Create the custom allocation function

In this section, you create an Azure function that implements your custom allocation policy. This function decides which divisional IoT hub a device should be registered to based on whether its registration ID contains the string **-contoso-tstrsd-007** or **-contoso-hpsd-088**. It also sets the initial state of the device twin based on whether the device is a toaster or a heat pump.

1. Accedere al [portale di Azure](https://portal.azure.com). From your home page, select **+ Create a resource**.

2. In the *Search the Marketplace* search box, type "Function App". From the drop-down list select **Function App**, and then select **Create**.

3. On **Function App** create page, under the **Basics** tab, enter the following settings for your new function app and select **Review + create**:

    **Resource Group**: Select the **contoso-us-resource-group** to keep all resources created in this article together.

    **Function App name**: Enter a unique function app name. This example uses **contoso-function-app-1098**.

    **Publish**: Verify that **Code** is selected.

    **Runtime Stack**: Select **.NET Core** from the drop-down.

    **Region**: Select the same region as your resource group. This example uses **West US**.

    > [!NOTE]
    > By default, Application Insights is enabled. Application Insights is not necessary for this article, but it might help you understand and investigate any issues you encounter with the custom allocation. If you prefer, you can disable Application Insights by selecting the **Monitoring** tab and then selecting **No** for **Enable Application Insights**.

    ![Create an Azure Function App to host the custom allocation function](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. On the **Summary** page, select **Create** to create the function app. Deployment may take several minutes. When it completes, select **Go to resource**.

5. On the left pane of the function app **Overview** page, select **+** next to **Functions** to add a new function.

    ![Add a function to the Function App](./media/how-to-use-custom-allocation-policies/create-function.png)

6. On the **Azure Functions for .NET - getting started** page, for the **CHOOSE A DEPLOYMENT ENVIRONMENT** step, select the **In-portal** tile, then select **Continue**.

    ![Select the portal development environment](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. On the next page, for the **CREATE A FUNCTION** step, select the **Webhook + API** tile, then select **Create**. A function named **HttpTrigger1** is created, and the portal displays the contents of the **run.csx** code file.

8. Reference required Nuget packages. To create the initial device twin, the custom allocation function uses classes that are defined in two Nuget packages that must be loaded into the hosting environment. With Azure Functions, Nuget packages are referenced using a *function.host* file. In this step, you save and upload a *function.host* file.

    1. Copy the following lines into your favorite editor and save the file on your computer as *function.host*.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.5.0" />  
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.16.0" />  
            </ItemGroup>  
        </Project>
        ```

    2. On the **HttpTrigger1** function, expand the **View Files** tab on the right side of the window.

        ![Open view files](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. Select **Upload**, browse to the **function.proj** file, and select **Open** to upload the file.

        ![Select upload file](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. Replace the code for the **HttpTrigger1** function with the following code and select **Save**:

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
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
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
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
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>Creare la registrazione

In this section, you'll create a new enrollment group that uses the custom allocation policy. Per semplicità, in questo articolo viene usata l'[attestazione con chiave simmetrica](concepts-symmetric-key-attestation.md) per la registrazione. Per una soluzione più sicura è consigliabile usare l'[attestazione del certificato X.509](concepts-security.md#x509-certificates) con una catena di certificati.

1. Still on the [Azure portal](https://portal.azure.com), open your provisioning service.

2. Select **Manage enrollments** on the left pane, and then select the **Add enrollment group** button at the top of the page.

3. On **Add Enrollment Group**, enter the following information, and select the **Save** button.

    **Nome del gruppo**: immettere **contoso-custom-allocated-devices**.

    **Tipo di attestazione**: selezionare **Chiave simmetrica**.

    **Genera chiavi automaticamente**: questa casella di controllo dovrebbe essere già selezionata.

    **Selezionare la modalità secondo cui assegnare i dispositivi agli hub**: selezionare **Personalizzata (usa funzione di Azure)** .

    ![Aggiungere il gruppo di registrazioni dell'allocazione personalizzata per l'attestazione con chiave simmetrica](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. On **Add Enrollment Group**, select **Link a new IoT hub** to link both of your new divisional IoT hubs.

    Execute this step for both of your divisional IoT hubs.

    **Sottoscrizione**: se si dispone di più sottoscrizioni, scegliere quella in cui sono stati creati gli hub IoT delle divisioni.

    **Hub IoT**: selezionare uno degli hub delle divisioni creato.

    **Criteri di accesso**: scegliere **iothubowner**.

    ![Collegare gli hub IoT delle divisioni al servizio di provisioning](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. Una volta collegati entrambi gli hub IoT delle divisioni, in **Aggiungi gruppo di registrazioni** è necessario selezionarli come gruppo dell'hub IoT per il gruppo di registrazioni, come mostrato di seguito:

    ![Creare il gruppo dell'hub della divisione per la registrazione](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. On **Add Enrollment Group**, scroll down to the **Select Azure Function** section, select the Function app you created in the previous section. Then select the function you created and select Save to save the enrollment group.

    ![Select the function and save the enrollment group](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. Dopo aver salvato la registrazione, aprirla nuovamente e annotare la **Chiave primaria**. È necessario salvare la registrazione prima di poter generare le chiavi. Questa chiave verrà usata per generare chiavi univoche di dispositivo per simulare i dispositivi in un secondo momento.

## <a name="derive-unique-device-keys"></a>Derivare le chiavi univoche di dispositivo

In this section, you create two unique device keys. Una chiave verrà utilizzata per simulare un dispositivo toaster, l'altra chiave per simulare un dispositivo pompa di calore.

To generate the device key, you use the **Primary Key** you noted earlier to compute the [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) of the device registration ID for each device and convert the result into Base64 format. Per altre informazioni sulla creazione di chiavi di dispositivo derivate con i gruppi di registrazione, vedere la sezione sulle registrazioni di gruppo dell'articolo [Attestazione con chiave simmetrica](concepts-symmetric-key-attestation.md).

Per l'esempio di questo articolo, usare i due ID di registrazione del dispositivo seguenti e calcolare una chiave di dispositivo per entrambi i dispositivi. Entrambi gli ID di registrazione presentano un suffisso valido per il funzionamento con il codice di esempio per i criteri di allocazione personalizzati:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Workstation di Linux

If you're using a Linux workstation, you can use openssl to generate your derived device keys as shown in the following example.

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

### <a name="windows-based-workstations"></a>Workstation basate su Windows

If you're using a Windows-based workstation, you can use PowerShell to generate your derived device key as shown in the following example.

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

In this section, you prepare the development environment used to build the [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). L'SDK include il codice di esempio per il dispositivo simulato. Il dispositivo simulato tenterà di effettuare il provisioning durante la sequenza di avvio del dispositivo.

Questa sezione si riferisce a una workstation basata su Windows. Per un esempio di Linux, vedere la configurazione delle macchine virtuali in [Come effettuare il provisioning per la multi-tenancy](how-to-provision-multitenant.md).

1. Scaricare il [sistema di compilazione CMake](https://cmake.org/download/).

    È importante che i prerequisiti di Visual Studio (Visual Studio e il carico di lavoro "Sviluppo di applicazioni desktop con C++") siano installati nel computer **prima** di avviare l'installazione di `CMake`. Quando i prerequisiti sono pronti e il download è stato verificato, installare il sistema di compilazione CMake.

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

    If `cmake` doesn't find your C++ compiler, you might get build errors while running the command. If that happens, try running the command in the [Visual Studio command prompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

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

In this section, you update a provisioning sample named **prov\_dev\_client\_sample** located in the Azure IoT C SDK you set up previously.

Questo codice di esempio simula una sequenza di avvio di dispositivo che invia la richiesta di provisioning all'istanza del servizio Device Provisioning. La sequenza di avvio riconoscerà il dispositivo toaster e lo assegnerà all'hub IoT usando i criteri di allocazione personalizzati.

1. Nel portale di Azure selezionare la scheda **Panoramica** per il servizio Device Provisioning e prendere nota del valore di **_Ambito ID_** .

    ![Estrarre le informazioni dell'endpoint del servizio Device Provisioning dal pannello del portale](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. In Visual Studio aprire il file di soluzione **azure_iot_sdks.sln** generato in precedenza tramite l'esecuzione di CMake. Il file di soluzione deve trovarsi nel percorso seguente:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Nella finestra *Esplora soluzioni* di Visual Studio passare alla cartella **Provision\_Samples**. Espandere il progetto di esempio denominato **prov\_dev\_client\_sample**. Espandere **File di origine** e aprire **prov\_dev\_client\_sample.c**.

4. Trovare la costante `id_scope` e sostituire il valore con il valore **Ambito ID** copiato in precedenza. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Trovare la definizione per la funzione `main()` nello stesso file. Assicurarsi che la variabile `hsm_type` sia impostata su `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` come illustrato di seguito:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Fare clic con il pulsante destro del mouse sul progetto **prov\_dev\_client\_sample** e scegliere **Imposta come progetto di avvio**.

### <a name="simulate-the-contoso-toaster-device"></a>Simulare il dispositivo toaster di Contoso

1. Per simulare il dispositivo toaster, individuare la chiamata a `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c** che è impostata come commento.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Uncomment the function call and replace the placeholder values (including the angle brackets) with the toaster registration ID and derived device key you generated previously. Il valore di chiave **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** visualizzato di seguito viene fornito solo come esempio.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Salvare il file.

2. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire la soluzione. In the prompt to rebuild the project, select **Yes**, to rebuild the project before running.

    The following output is an example of the simulated toaster device successfully booting up and connecting to the provisioning service instance to be assigned to the toasters IoT hub by the custom allocation policy:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simulare il dispositivo pompa di calore di Contoso

1. Per simulare il dispositivo pompa di calore, aggiornare di nuovo la chiamata a `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c** con l'ID registrazione del dispositivo pompa di calore e la chiave di dispositivo derivata generata in precedenza. Il valore di chiave **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** visualizzato di seguito viene fornito solo come esempio.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Salvare il file.

2. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire la soluzione. In the prompt to rebuild the project, select **Yes** to rebuild the project before running.

    The following output is an example of the simulated heat pump device successfully booting up and connecting to the provisioning service instance to be assigned to the Contoso heat pumps IoT hub by the custom allocation policy:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Risoluzione dei problemi relativi ai criteri di allocazione personalizzati

The following table shows expected scenarios and the results error codes you might receive. Usare questa tabella come supporto per la risoluzione dei problemi relativi ai criteri di allocazione personalizzati con le Funzioni di Azure.

| Scenario | Risultato della registrazione dal servizio di provisioning | Effettuare il provisioning dei risultati SDK |
| -------- | --------------------------------------------- | ------------------------ |
| Il webhook restituisce 200 OK con 'iotHubHostName' impostato su un nome host dell'hub IoT valido | Stato del risultato: Assegnato  | L'SDK restituisce PROV_DEVICE_RESULT_OK insieme alle informazioni dell'hub |
| Il webhook restituisce 200 OK con 'iotHubHostName' incluso nella risposta ma impostato su una stringa vuota o Null | Stato del risultato: Non riuscito<br><br> Codice di errore: CustomAllocationIotHubNotSpecified (400208) | L'SDK restituisce PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Il webhook restituisce 401 - Non autorizzato | Stato del risultato: Non riuscito<br><br>Codice di errore: CustomAllocationUnauthorizedAccess (400209) | L'SDK restituisce PROV_DEVICE_RESULT_UNAUTHORIZED |
| È stata creata una registrazione singola per disabilitare il dispositivo | Stato del risultato: Non disponibile | L'SDK restituisce PROV_DEVICE_RESULT_DISABLED |
| Il webhook restituisce il codice di errore > = 429 | L'orchestrazione di DPS effettuerà diversi nuovi tentativi. I criteri di ripetizione sono attualmente:<br><br>&nbsp;&nbsp;- Numero di tentativi: 10<br>&nbsp;&nbsp;- Intervallo iniziale: 1 s<br>&nbsp;&nbsp;- Incremento: 9 s | L'SDK ignorerà l'errore e invierà un altro messaggio per l'ottenimento dello stato nel periodo specificato |
| Il webhook restituisce qualsiasi altro codice di stato | Stato del risultato: Non riuscito<br><br>Codice di errore: CustomAllocationFailed (400207) | L'SDK restituisce PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Pulire le risorse

If you plan to continue working with the resources created in this article, you can leave them. If you don't plan to continue using the resources, use the following steps to delete all of the resources created in this article to avoid unnecessary charges.

Questi passaggi presuppongono che tutte le risorse in questo articolo siano state create come indicato nello stesso gruppo di risorse denominato **contoso-us-resource-group**.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se si è creato l'hub IoT all'interno di un gruppo di risorse esistente che contiene risorse che si vogliono conservare, eliminare solo la risorsa dell'hub IoT invece di eliminare il gruppo di risorse.
>

Per eliminare il gruppo di risorse per nome:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.

2. Nella casella di testo **Filtra per nome...** digitare il nome del gruppo di risorse che contiene le risorse di interesse, **contoso-us-resource-group**. 

3. A destra del gruppo di risorse nell'elenco dei risultati selezionare **...** quindi su **Elimina gruppo di risorse**.

4. You'll be asked to confirm the deletion of the resource group. Digitare di nuovo il nome del gruppo di risorse per confermare e quindi selezionare **Elimina**. Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

* To learn more Reprovisioning, see [IoT Hub Device reprovisioning concepts](concepts-device-reprovision.md) 
* To learn more Deprovisioning, see [How to deprovision devices that were previously autoprovisioned](how-to-unprovision-devices.md) 
