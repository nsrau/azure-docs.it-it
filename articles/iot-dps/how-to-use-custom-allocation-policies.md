---
title: Come usare i criteri di allocazione personalizzati con il servizio Device Provisioning in hub IoT di Azure | Documentazione Microsoft
description: Come usare i criteri di allocazione personalizzati con il servizio Device Provisioning in hub IoT di Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 8f9cc48384e6e1e85a92b3f23c3a362db0df98e0
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108220"
---
# <a name="how-to-use-custom-allocation-policies"></a>Come usare i criteri di allocazione personalizzati

I criteri di allocazione personalizzati offrono maggiore controllo sulle modalità di assegnazione dei dispositivi a un hub IoT. A tale scopo viene usato un codice personalizzato in una [funzione di Azure](../azure-functions/functions-overview.md) per assegnare dispositivi a un hub IoT. Il servizio Device Provisioning chiama il codice della funzione di Azure per fornire tutte le informazioni rilevanti sul dispositivo e la registrazione. Viene eseguito il codice della funzione, che restituisce le informazioni dell'hub IoT usate per il provisioning del dispositivo.

Con i criteri di allocazione personalizzati è possibile definire i propri criteri di allocazione quando i criteri forniti dal servizio Device provisioning non soddisfano i requisiti dello scenario.

Ad esempio, si potrebbe voler esaminare il certificato usato da un dispositivo durante il provisioning e assegnare il dispositivo a un hub Internet in base a una proprietà del certificato. In alternativa, è possibile che si disponga di informazioni archiviate in un database per i dispositivi e che sia necessario eseguire una query sul database per determinare a quale hub Internet deve essere assegnato un dispositivo.

Questo articolo illustra i criteri di allocazione personalizzati che usano una funzione di Azure scritta in C#. Vengono creati due nuovi hub IoT per rappresentare una *divisione toaster di Contoso* e una *divisione pompe di calore di Contoso*. Per essere accettati per il provisioning, i dispositivi che richiedono questo servizio devono avere un ID di registrazione con uno dei suffissi di seguito:

* **-contoso-tstrsd-007**: divisione toaster di Contoso
* **-contoso-hpsd-088**: divisione pompe di calore di Contoso

Il provisioning dei dispositivi verrà effettuato sulla base di uno di questi suffissi richiesti per l'ID di registrazione. Questi dispositivi verranno simulati usando un esempio di provisioning incluso in [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

In questo articolo vengono eseguiti i passaggi seguenti:

* Usare l'interfaccia della riga di comando di Azure per creare due hub IoT per le divisioni di Contoso (**divisione toaster di Contoso** e **divisione pompe di calore di Contoso**)
* Creare una nuova registrazione di gruppo con una funzione di Azure per i criteri di allocazione personalizzati
* Creare le chiavi di dispositivo per due simulazioni di dispositivo.
* Configurare un ambiente di sviluppo per Azure IoT C SDK
* Simulare i dispositivi e verificare che vengano sottoposti a provisioning in base al codice di esempio nei criteri di allocazione personalizzati

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 o versione successiva con il carico di lavoro [Sviluppo di applicazioni desktop con C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) abilitato.
* La versione più recente di [Git](https://git-scm.com/download/) installata.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Creare il servizio di provisioning e due hub di Internet delle cose

In questa sezione si usa il Azure Cloud Shell per creare un servizio di provisioning e due hub Internet che rappresentano la **divisione dei tostapane di Contoso** e la divisione delle pompe di calore di **Contoso**.

> [!TIP]
> I comandi usati in questo articolo creano il servizio di provisioning e altre risorse nella località Stati Uniti occidentali. Si consiglia di creare le risorse nell'area più vicina all'utente che supporta il servizio Device provisioning. Per un elenco delle località disponibili, è possibile eseguire il comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` oppure passare alla pagina [Stato di Azure](https://azure.microsoft.com/status/) e cercare "Servizio Device Provisioning". Nei comandi, i percorsi possono essere specificati in un formato Word o in più parole; ad esempio: westus, Stati Uniti occidentali, Stati Uniti occidentali e così via. Il valore non fa distinzione tra maiuscole e minuscole. Se si usa il formato a più parole per specificare la località, racchiudere il valore tra virgolette, ad esempio `-- location "West US"`.
>

1. Usare Azure Cloud Shell per creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

    Nell'esempio seguente viene creato un gruppo di risorse denominato *Contoso-US-Resource-Group* nell'area *westus* . È consigliabile usare questo gruppo per tutte le risorse create in questo articolo. Questo approccio renderà più semplice la pulizia al termine dell'operazione.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Usare il Azure Cloud Shell per creare un servizio Device provisioning con il comando [AZ Internet create](/cli/azure/iot/dps#az-iot-dps-create) . Il servizio di provisioning verrà aggiunto a *Contoso-US-Resource-Group*.

    Nell'esempio seguente viene creato un servizio di provisioning denominato *Contoso-Provisioning-Service-1098* nella località *westus* . È necessario usare un nome di servizio univoco. Creare il proprio suffisso nel nome del servizio al posto di **1098**.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Il completamento del comando può richiedere alcuni minuti.

3. Usare Azure Cloud Shell per creare l'hub IoT della **divisione toaster di Contoso** con il comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). L'hub IoT verrà aggiunto a *contoso-us-resource-group*.

    L'esempio seguente crea un hub tutto denominato *Contoso-tostapane-Hub-1098* nella località *westus* . È necessario usare un nome di hub univoco. Creare il proprio suffisso nel nome dell'hub da sostituire a **1098**. Il codice di esempio per i criteri di allocazione personalizzati richiede `-toasters-` nel nome dell'hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Il completamento del comando può richiedere alcuni minuti.

4. Usare Azure Cloud Shell per creare l'hub IoT della **divisione pompe di calore di Contoso** con il comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Questo hub IoT verrà aggiunto anche a *contoso-us-resource-group*.

    L'esempio seguente crea un hub tutto denominato *Contoso-Heatpumps-Hub-1098* nella località *westus* . È necessario usare un nome di hub univoco. Creare il proprio suffisso nel nome dell'hub da sostituire a **1098**. Il codice di esempio per i criteri di allocazione personalizzati richiede `-heatpumps-` nel nome dell'hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Il completamento del comando può richiedere alcuni minuti.

## <a name="create-the-custom-allocation-function"></a>Creazione della funzione di allocazione personalizzata

In questa sezione viene creata una funzione di Azure che implementa i criteri di allocazione personalizzati. Questa funzione decide quale Hub di tutti i componenti di un dispositivo deve essere registrato a seconda che l'ID di registrazione contenga la stringa **-Contoso-tstrsd-007** o **-Contoso-hpsd-088**. Imposta anche lo stato iniziale del dispositivo gemello a seconda che il dispositivo sia un tostapane o una pompa termica.

1. Accedere al [portale di Azure](https://portal.azure.com). Dal home page selezionare **+ Crea una risorsa**.

2. Nella casella Cerca nel *Marketplace* digitare "app per le funzioni". Nell'elenco a discesa selezionare **app per le funzioni**e quindi fare clic su **Crea**.

3. Nella pagina **app per le funzioni** crea, nella scheda **nozioni di base** , immettere le impostazioni seguenti per la nuova app per le funzioni e selezionare **Verifica + crea**:

    **Gruppo di risorse**: selezionare **Contoso-US-Resource-Group** per includere insieme tutte le risorse create in questo articolo.

    **Nome app per le funzioni**: immettere un nome univoco per l'app per le funzioni. Questo esempio usa **Contoso-Function-app-1098**.

    **Publish**: verificare che il **codice** sia selezionato.

    **Stack di runtime**: selezionare **.NET Core** dall'elenco a discesa.

    **Area**: selezionare la stessa area del gruppo di risorse. Questo esempio usa **Stati Uniti occidentali**.

    > [!NOTE]
    > Per impostazione predefinita, Application Insights è abilitato. Application Insights non è necessario per questo articolo, ma può essere utile per comprendere e analizzare eventuali problemi riscontrati con l'allocazione personalizzata. Se si preferisce, è possibile disabilitare Application Insights selezionando la scheda **monitoraggio** e selezionando **No** per **Abilita Application Insights**.

    ![Creare una app per le funzioni di Azure per ospitare la funzione di allocazione personalizzata](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. Nella pagina **Riepilogo** selezionare **Crea** per creare l'app per le funzioni. La distribuzione potrebbe richiedere alcuni minuti. Al termine, selezionare **Vai alla risorsa**.

5. Nel riquadro sinistro della pagina **Panoramica** dell'app per le funzioni selezionare **+** accanto a **funzioni** per aggiungere una nuova funzione.

    ![Aggiungere una funzione al app per le funzioni](./media/how-to-use-custom-allocation-policies/create-function.png)

6. Nella pagina di **Introduzione a funzioni di Azure per .NET** : per il passaggio **scegliere un ambiente di distribuzione** selezionare il riquadro **nel portale** , quindi selezionare **continua**.

    ![Selezionare l'ambiente di sviluppo del portale](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. Nella pagina successiva, per il passaggio **creare una funzione** , selezionare il riquadro **webhook + API** , quindi selezionare **Crea**. Viene creata una funzione denominata **HttpTrigger1** e il portale Visualizza il contenuto del file di codice **Run. CSX** .

8. Fare riferimento ai pacchetti NuGet necessari. Per creare il dispositivo gemello iniziale, la funzione di allocazione personalizzata usa le classi definite in due pacchetti NuGet che devono essere caricati nell'ambiente host. Con funzioni di Azure, viene fatto riferimento ai pacchetti NuGet usando un file *Function. host* . In questo passaggio si salva e si carica un file *Function. host* .

    1. Copiare le righe seguenti nell'editor preferito e salvare il file nel computer come *Function. host*.

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

    2. Nella funzione **HttpTrigger1** espandere la scheda **Visualizza file** sul lato destro della finestra.

        ![Apri file di visualizzazione](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. Selezionare **carica**, passare al file **Function. proj** e selezionare Open ( **Apri** ) per caricare il file.

        ![Seleziona file di caricamento](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. Sostituire il codice per la funzione **HttpTrigger1** con il codice seguente e selezionare **Save (Salva**):

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

In questa sezione verrà creato un nuovo gruppo di registrazione che usa i criteri di allocazione personalizzati. Per semplicità, in questo articolo viene usata l'[attestazione con chiave simmetrica](concepts-symmetric-key-attestation.md) per la registrazione. Per una soluzione più sicura è consigliabile usare l'[attestazione del certificato X.509](concepts-security.md#x509-certificates) con una catena di certificati.

1. Sempre nella [portale di Azure](https://portal.azure.com)aprire il servizio di provisioning.

2. Selezionare **Gestisci registrazioni** nel riquadro sinistro, quindi selezionare il pulsante **Aggiungi gruppo di registrazioni** nella parte superiore della pagina.

3. In **Aggiungi gruppo di registrazione**immettere le informazioni seguenti e selezionare il pulsante **Salva** .

    **Nome del gruppo**: immettere **contoso-custom-allocated-devices**.

    **Tipo di attestazione**: selezionare **Chiave simmetrica**.

    **Genera chiavi automaticamente**: questa casella di controllo dovrebbe essere già selezionata.

    **Selezionare la modalità secondo cui assegnare i dispositivi agli hub**: selezionare **Personalizzata (usa funzione di Azure)** .

    ![Aggiungere il gruppo di registrazioni dell'allocazione personalizzata per l'attestazione con chiave simmetrica](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. In **Aggiungi un gruppo di registrazione**selezionare **collega un nuovo hub** Internet per collegare entrambi i nuovi hub di Internet delle cose.

    Eseguire questo passaggio per entrambi gli hub di Internet delle cose.

    **Sottoscrizione**: se si dispone di più sottoscrizioni, scegliere quella in cui sono stati creati gli hub IoT delle divisioni.

    **Hub IoT**: selezionare uno degli hub delle divisioni creato.

    **Criteri di accesso**: scegliere **iothubowner**.

    ![Collegare gli hub IoT delle divisioni al servizio di provisioning](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. Una volta collegati entrambi gli hub IoT delle divisioni, in **Aggiungi gruppo di registrazioni** è necessario selezionarli come gruppo dell'hub IoT per il gruppo di registrazioni, come mostrato di seguito:

    ![Creare il gruppo dell'hub della divisione per la registrazione](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. In **Aggiungi gruppo di registrazione**scorrere verso il basso fino alla sezione **selezionare la funzione di Azure** e selezionare l'app per le funzioni creata nella sezione precedente. Selezionare quindi la funzione creata e fare clic su Save (Salva) per salvare il gruppo di registrazione.

    ![Selezionare la funzione e salvare il gruppo di registrazione](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. Dopo aver salvato la registrazione, aprirla nuovamente e annotare la **Chiave primaria**. È necessario salvare la registrazione prima di poter generare le chiavi. Questa chiave verrà usata per generare chiavi univoche di dispositivo per simulare i dispositivi in un secondo momento.

## <a name="derive-unique-device-keys"></a>Derivare le chiavi univoche di dispositivo

In questa sezione vengono create due chiavi univoche del dispositivo. Una chiave verrà utilizzata per simulare un dispositivo toaster, l'altra chiave per simulare un dispositivo pompa di calore.

Per generare la chiave del dispositivo, usare la **chiave primaria** annotata in precedenza per calcolare il [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) dell'ID registrazione del dispositivo per ogni dispositivo e convertire il risultato in formato Base64. Per altre informazioni sulla creazione di chiavi di dispositivo derivate con i gruppi di registrazione, vedere la sezione sulle registrazioni di gruppo dell'articolo [Attestazione con chiave simmetrica](concepts-symmetric-key-attestation.md).

Per l'esempio di questo articolo, usare i due ID di registrazione del dispositivo seguenti e calcolare una chiave di dispositivo per entrambi i dispositivi. Entrambi gli ID di registrazione presentano un suffisso valido per il funzionamento con il codice di esempio per i criteri di allocazione personalizzati:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Workstation di Linux

Se si usa una workstation Linux, è possibile usare OpenSSL per generare le chiavi del dispositivo derivate, come illustrato nell'esempio seguente.

1. Sostituire il valore della **CHIAVE** con la **chiave primaria** annotata in precedenza.

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

Se si usa una workstation basata su Windows, è possibile usare PowerShell per generare la chiave del dispositivo derivata, come illustrato nell'esempio seguente.

1. Sostituire il valore della **CHIAVE** con la **chiave primaria** annotata in precedenza.

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

In questa sezione viene preparato l'ambiente di sviluppo usato per compilare l' [SDK di Azure](https://github.com/Azure/azure-iot-sdk-c). L'SDK include il codice di esempio per il dispositivo simulato. Il dispositivo simulato tenterà di effettuare il provisioning durante la sequenza di avvio del dispositivo.

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

    Se `cmake` non trova il C++ compilatore, è possibile che vengano generati errori di compilazione durante l'esecuzione del comando. In tal caso, provare a eseguire il comando nel [prompt dei comandi di Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

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

In questa sezione viene aggiornato un esempio di provisioning denominato **dimostrativo\_dev\_client\_esempio** che si trova nell'SDK di Azure in Azure.

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

    Rimuovere il commento dalla chiamata di funzione e sostituire i valori segnaposto (incluse le parentesi angolari) con l'ID di registrazione del tostapane e la chiave del dispositivo derivato generata in precedenza. Il valore di chiave **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** visualizzato di seguito viene fornito solo come esempio.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Salvare il file.

2. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire la soluzione. Nella richiesta di ricompilazione del progetto, selezionare **Sì**per ricompilare il progetto prima di eseguire.

    L'output seguente è un esempio del dispositivo del tostapane simulato che esegue correttamente l'avvio e la connessione all'istanza del servizio di provisioning da assegnare all'hub degli elementi del tostapane da parte dei criteri di allocazione personalizzati:

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

2. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire la soluzione. Nella richiesta di ricompilazione del progetto, selezionare **Sì** per ricompilare il progetto prima di eseguire.

    L'output seguente è un esempio del dispositivo a pompa termica simulato che esegue correttamente l'avvio e la connessione all'istanza del servizio di provisioning da assegnare all'hub delle risorse di approvvigionamento di Contoso per i criteri di allocazione personalizzati:

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

La tabella seguente illustra gli scenari previsti e i codici di errore dei risultati che potrebbero essere ricevuti. Usare questa tabella come supporto per la risoluzione dei problemi relativi ai criteri di allocazione personalizzati con le Funzioni di Azure.

| Scenario | Risultato della registrazione dal servizio di provisioning | Effettuare il provisioning dei risultati SDK |
| -------- | --------------------------------------------- | ------------------------ |
| Il webhook restituisce 200 OK con 'iotHubHostName' impostato su un nome host dell'hub IoT valido | Stato del risultato: Assegnato  | L'SDK restituisce PROV_DEVICE_RESULT_OK insieme alle informazioni dell'hub |
| Il webhook restituisce 200 OK con 'iotHubHostName' incluso nella risposta ma impostato su una stringa vuota o Null | Stato del risultato: Non riuscito<br><br> Codice di errore: CustomAllocationIotHubNotSpecified (400208) | L'SDK restituisce PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Il webhook restituisce 401 - Non autorizzato | Stato del risultato: Non riuscito<br><br>Codice di errore: CustomAllocationUnauthorizedAccess (400209) | L'SDK restituisce PROV_DEVICE_RESULT_UNAUTHORIZED |
| È stata creata una registrazione singola per disabilitare il dispositivo | Stato del risultato: Non disponibile | L'SDK restituisce PROV_DEVICE_RESULT_DISABLED |
| Il webhook restituisce il codice di errore > = 429 | L'orchestrazione di DPS effettuerà diversi nuovi tentativi. I criteri di ripetizione sono attualmente:<br><br>&nbsp;&nbsp;- Numero di tentativi: 10<br>&nbsp;&nbsp;- Intervallo iniziale: 1 s<br>&nbsp;&nbsp;- Incremento: 9 s | L'SDK ignorerà l'errore e invierà un altro messaggio per l'ottenimento dello stato nel periodo specificato |
| Il webhook restituisce qualsiasi altro codice di stato | Stato del risultato: Non riuscito<br><br>Codice di errore: CustomAllocationFailed (400207) | L'SDK restituisce PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare le risorse create in questo articolo, è possibile lasciarle invariate. Se non si prevede di continuare a usare le risorse, attenersi alla procedura seguente per eliminare tutte le risorse create in questo articolo per evitare addebiti superflui.

Questi passaggi presuppongono che tutte le risorse in questo articolo siano state create come indicato nello stesso gruppo di risorse denominato **contoso-us-resource-group**.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se si è creato l'hub IoT all'interno di un gruppo di risorse esistente che contiene risorse che si vogliono conservare, eliminare solo la risorsa dell'hub IoT invece di eliminare il gruppo di risorse.
>

Per eliminare il gruppo di risorse per nome:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.

2. Nella casella di testo **Filtra per nome...** digitare il nome del gruppo di risorse che contiene le risorse di interesse, **contoso-us-resource-group**. 

3. A destra del gruppo di risorse nell'elenco dei risultati selezionare **...** quindi su **Elimina gruppo di risorse**.

4. Verrà richiesto di confermare l'eliminazione del gruppo di risorse. Digitare di nuovo il nome del gruppo di risorse per confermare e quindi selezionare **Elimina**. Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sul nuovo provisioning, vedere [concetti relativi al provisioning dei dispositivi dell'hub](concepts-device-reprovision.md) . 
* Per ulteriori informazioni sul deprovisioning, vedere [come eseguire il](how-to-unprovision-devices.md) deprovisioning di dispositivi precedentemente sottoposti a provisioning automatico 
