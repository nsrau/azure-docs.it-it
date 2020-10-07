---
title: Esercitazione sull'uso di criteri di allocazione personalizzati con il servizio Device Provisioning in hub IoT di Azure
description: Esercitazione sull'uso di criteri di allocazione personalizzati con il servizio Device Provisioning in hub IoT di Azure
author: wesmc7777
ms.author: wesmc
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: ae43e0ed1bf3f64ce851e9ae779d54b82269a7be
ms.sourcegitcommit: ada9a4a0f9d5dbb71fc397b60dc66c22cf94a08d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91405573"
---
# <a name="tutorial-use-custom-allocation-policies-with-device-provisioning-service-dps"></a>Esercitazione: Usare criteri di allocazione personalizzati con il servizio Device Provisioning

I criteri di allocazione personalizzati offrono maggiore controllo sulle modalità di assegnazione dei dispositivi a un hub IoT. A tale scopo viene usato un codice personalizzato in una [funzione di Azure](../azure-functions/functions-overview.md) che viene eseguito durante il provisioning per assegnare dispositivi a un hub IoT. Il servizio Device Provisioning chiama il codice della funzione di Azure per fornire tutte le informazioni rilevanti sul dispositivo e la registrazione. Viene eseguito il codice della funzione, che restituisce le informazioni dell'hub IoT usate per il provisioning del dispositivo.

È possibile definire e usare criteri di allocazione personalizzati qualora quelli forniti dal servizio di Device Provisioning non soddisfino i requisiti di uno specifico scenario.

Si potrebbe ad esempio scegliere di esaminare il certificato in uso su un dispositivo durante il provisioning e assegnare il dispositivo a un hub IoT in base a una proprietà del certificato. Oppure si potrebbero avere informazioni sui dispositivi archiviate in un database su cui è necessario eseguire una query per stabilire a quale hub IoT assegnare un dispositivo.

Questo articolo illustra un gruppo di registrazioni con un criterio di allocazione personalizzato che usa una funzione di Azure scritta in C# per effettuare il provisioning di dispositivi toaster tramite chiavi simmetriche. Il provisioning in un hub IoT non verrà effettuato per qualsiasi dispositivo non riconosciuto come toaster.

I dispositivi richiederanno il provisioning usando il codice di esempio incluso in [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).


In questa esercitazione verranno effettuate le attività seguenti:

> [!div class="checklist"]
> * Creare una nuova app per le funzioni di Azure per supportare una funzione di allocazione personalizzata
> * Creare una nuova registrazione di gruppo con una funzione di Azure per i criteri di allocazione personalizzati
> * Creare le chiavi per due dispositivi
> * Configurare l'ambiente di sviluppo per il codice del dispositivo di esempio da [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)
> * Eseguire i dispositivi e verificare che il provisioning venga effettuato in base al criterio di allocazione personalizzato


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Questo articolo presuppone che sia stata completata la procedura descritta in [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md) per creare l'hub IoT e l'istanza del servizio Device Provisioning.

* La versione più recente di [Git](https://git-scm.com/download/) installata.

* Per un ambiente di sviluppo Windows, è necessario [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 con il carico di lavoro ['Sviluppo di applicazioni desktop con C++'](https://docs.microsoft.com/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) abilitato. Sono supportati anche Visual Studio 2015 e Visual Studio 2017.

* Per Linux o macOS, vedere la sezione appropriata in [Preparare l'ambiente di sviluppo](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) nella documentazione di [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-custom-allocation-function"></a>Creare la funzione di allocazione personalizzata

In questa sezione viene creata una funzione di Azure che implementa il criterio di allocazione personalizzato. La funzione decide se un dispositivo deve essere registrato nell'hub IoT a seconda che il relativo ID registrazione contenga o meno il prefisso di stringa **contoso-toaster**.

1. Accedere al [portale di Azure](https://portal.azure.com). Nella home page selezionare **+ Crea una risorsa**.

2. Nella casella di ricerca *Cerca nel Marketplace* digitare "App per le funzioni". Nell'elenco a discesa selezionare **App per le funzioni**, quindi selezionare **Crea**.

3. Nella pagina di creazione di **App per le funzioni**, nella scheda **Informazioni di base**, immettere le impostazioni seguenti per la nuova app per le funzioni e selezionare **Rivedi e crea**:

    **Sottoscrizione** Se sono presenti più sottoscrizioni e quella da usare non è selezionata, selezionarla.

    **Gruppo di risorse**: questo campo consente di creare un nuovo gruppo di risorse o di sceglierne uno esistente in cui inserire l'app per le funzioni. Scegliere lo stesso gruppo di risorse che contiene l'hub IoT creato in precedenza per i test, ad esempio, **TestResources**. Inserendo tutte le risorse correlate in un gruppo è possibile gestirle insieme.

    **Nome dell'app per le funzioni**: immettere un nome univoco per l'app per le funzioni. Questo esempio usa **contoso-function-app**.

    **Publish**: verificare che sia selezionata l'opzione **Codice**.

    **Stack di runtime**: selezionare **.NET Core** nell'elenco a discesa.

    **Area**: selezionare la stessa area del gruppo di risorse. Questo esempio usa **Stati Uniti occidentali**.

    > [!NOTE]
    > Per impostazione predefinita, la funzionalità Application Insights è abilitata. Questa funzionalità non è necessaria per l'articolo, ma potrebbe risultare utile per identificare e analizzare i problemi che si verificano con l'allocazione personalizzata. Se si preferisce, è possibile disabilitare Application Insights selezionando la scheda **Monitoraggio** e quindi **No** per **Abilita Application Insights**.

    ![Creare un'app per le funzioni in cui ospitare la funzione di allocazione personalizzata](./media/tutorial-custom-allocation-policies/create-function-app.png)

4. Nella pagina **Riepilogo** selezionare **Crea** per creare l'app per le funzioni. Questa operazione può richiedere alcuni minuti. Al termine, selezionare **Vai alla risorsa**.

5. Nel riquadro sinistro, in **Funzioni**, fare clic su **Funzioni** e quindi su **+ Aggiungi** per aggiungere una nuova funzione.

6. Nella pagina dei modelli selezionare il riquadro **Trigger HTTP**, quindi selezionare **Crea funzione**. Viene creata una funzione denominata **HttpTrigger1** e nel portale viene visualizzata la relativa pagina di panoramica.

7. Fare clic su **Codice e test** per la nuova funzione. Nel portale viene visualizzato il contenuto del file di codice **run.csx**. 

8. Sostituire il codice della funzione **HttpTrigger1** con il codice seguente e selezionare **Salva**. Il codice dell'allocazione personalizzata è pronto per l'uso.

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

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
                // This is a Contoso Toaster 
                if (regId.Contains("contoso-toaster"))
                {
                    //Log IoT hubs configured for the enrollment
                    foreach(string hubString in hubs)
                    {
                        log.LogInformation("linkedHub : " + hubString);
                    }

                    obj.iotHubHostName = hubs[0];
                    log.LogInformation("Selected hub : " + obj.iotHubHostName);
                }
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
    }
    ```

9. Sotto il file di codice **run.csx** fare clic su **Log** per monitorare la registrazione della funzione di allocazione personalizzata. 


## <a name="create-the-enrollment"></a>Creare la registrazione

In questa sezione si creerà un nuovo gruppo di registrazioni che usa il criterio di allocazione personalizzato. Per semplicità, in questo articolo viene usata l'[attestazione con chiave simmetrica](concepts-symmetric-key-attestation.md) per la registrazione. Per una soluzione più sicura è consigliabile usare l'[attestazione del certificato X.509](concepts-x509-attestation.md) con una catena di certificati.

1. Sempre nel [portale di Azure](https://portal.azure.com) aprire il servizio di provisioning.

2. Selezionare **Gestisci registrazioni** nel riquadro sinistro, quindi selezionare il pulsante **Aggiungi gruppo di registrazioni** nella parte superiore della pagina.

3. In **Aggiungi gruppo di registrazioni** immettere le informazioni della tabella seguente e fare clic sul pulsante **Salva**.

    | Campo | Descrizione e/o valore suggerito |
    | :---- | :----------------------------- |
    | **Nome gruppo** | Immettere **contoso-custom-allocated-devices** |
    | **Tipo di attestazione** | Selezionare **Chiave simmetrica** |
    | **Genera chiavi automaticamente** | questa casella di controllo dovrebbe essere già selezionata. |
    | **Selezionare la modalità di assegnazione dei dispositivi agli hub** | Selezionare **Personalizzata (funzione di Azure)** |
    | **Selezionare gli hub IoT a cui può essere assegnato questo gruppo** | Selezionare l'hub IoT creato in precedenza quando è stata completata la guida di avvio rapido. |
    | **Seleziona funzione di Azure** | Selezionare la sottoscrizione che contiene l'app per le funzioni creata. Quindi selezionare **contoso-function-app** e **HttpTrigger1** per la funzione. |

    ![Aggiungere il gruppo di registrazioni dell'allocazione personalizzata per l'attestazione con chiave simmetrica](./media/tutorial-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Dopo aver salvato la registrazione, aprirla nuovamente e annotare la **Chiave primaria**. È necessario salvare la registrazione prima di poter generare le chiavi. Questa chiave simmetrica primaria verrà usata per generare chiavi univoche per i dispositivi che provano a effettuare il provisioning in seguito. 

## <a name="derive-unique-device-keys"></a>Derivare le chiavi univoche di dispositivo

I dispositivi non usano direttamente la chiave simmetrica primaria. Al contrario, la chiave primaria viene usata per derivare una chiave per ogni dispositivo. In questa sezione si creeranno due chiavi univoche per i dispositivi. Una chiave verrà utilizzata per simulare un dispositivo toaster, l'altra chiave per simulare un dispositivo pompa di calore. Le chiavi generate consentiranno a entrambi i dispositivi di provare a eseguire una registrazione. Solo un ID registrazione dispositivo avrà un suffisso valido per essere accettato dal codice di esempio del criterio di allocazione personalizzato. Di conseguenza, ne verrà accettato uno e l'altro verrà rifiutato

Per derivare la chiave di dispositivo, usare la chiave simmetrica annotata in precedenza per calcolare il valore [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) dell'ID di registrazione per ogni dispositivo e convertire il risultato in formato Base64. Per altre informazioni sulla creazione di chiavi di dispositivo derivate con i gruppi di registrazione, vedere la sezione sulle registrazioni di gruppo dell'articolo [Attestazione con chiave simmetrica](concepts-symmetric-key-attestation.md).

Per l'esempio di questo articolo, usare questi due ID registrazione dispositivo con il codice seguente per calcolare una chiave per entrambi i dispositivi:

* **contoso-toaster-007**
* **contoso-heatpump-088**

Sostituire il valore della variabile **KEY** con quello di **Chiave primaria** annotato in precedenza dopo la creazione del gruppo di registrazioni. Il valore della chiave e l'output mostrato con il codice seguente rappresentano solo un esempio.

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Se si usa una workstation basata su Windows, è possibile usare PowerShell per generare la chiave di dispositivo derivata come illustrato nell'esempio seguente.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='contoso-toaster-007'
$REG_ID2='contoso-heatpump-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($key)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

#### <a name="bash"></a>[Bash](#tab/bash)

Se si sa una workstation Linux, è possibile usare openssl per generare le chiavi di dispositivo derivate come illustrato nell'esempio Bash seguente.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=contoso-toaster-007
REG_ID2=contoso-heatpump-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Preparare un ambiente di sviluppo per Azure IoT C SDK

I dispositivi richiederanno il provisioning usando il codice di esempio incluso in [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

In questa sezione si prepara l'ambiente di sviluppo da usare per creare [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). L'SDK include il codice di esempio per il dispositivo simulato. Il dispositivo simulato tenterà di effettuare il provisioning durante la sequenza di avvio del dispositivo.

Questa sezione si riferisce a una workstation basata su Windows. Per un esempio di Linux, vedere la configurazione delle macchine virtuali in [Come effettuare il provisioning per la multi-tenancy](how-to-provision-multitenant.md).

1. Scaricare il [sistema di compilazione CMake](https://cmake.org/download/).

    È importante che nel computer siano installati i prerequisiti di Visual Studio (Visual Studio e carico di lavoro 'Sviluppo di applicazioni desktop con C++') **prima** di avviare l'installazione di `CMake`. Quando i prerequisiti sono pronti e il download è stato verificato, installare il sistema di compilazione CMake.

2. Trovare il nome del tag per la [versione più recente](https://github.com/Azure/azure-iot-sdk-c/releases/latest) dell'SDK.

3. Aprire un prompt dei comandi o la shell Git Bash. Eseguire i comandi seguenti per clonare la versione più recente del repository GitHub [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Usare il tag trovato nel passaggio precedente come valore per il parametro `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Il completamento di questa operazione richiederà alcuni minuti.

4. Creare una sottodirectory `cmake` nella directory radice del repository Git e passare a tale cartella. Eseguire i comandi seguenti dalla directory `azure-iot-sdk-c`:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Eseguire il comando seguente che compila una versione dell'SDK specifica per la piattaforma di sviluppo client. Verrà generata una soluzione di Visual Studio per il dispositivo simulato nella directory `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Se `cmake` non trova il compilatore C++, è possibile che si verifichino errori di compilazione durante l'esecuzione del comando. In tal caso, provare a eseguire il comando nel [prompt dei comandi di Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

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

In questa sezione si aggiorna un esempio di provisioning denominato **prov\_dev\_client\_sample** che si trova in Azure IoT C SDK precedentemente configurato.

Questo codice di esempio simula una sequenza di avvio di dispositivo che invia la richiesta di provisioning all'istanza del servizio Device Provisioning. La sequenza di avvio riconoscerà il dispositivo toaster e lo assegnerà all'hub IoT usando i criteri di allocazione personalizzati.

1. Nel portale di Azure selezionare la scheda **Panoramica** per il servizio Device Provisioning e prendere nota del valore di **_Ambito ID_**.

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

5. Trovare la definizione per la funzione `main()` nello stesso file. Assicurarsi che la variabile`hsm_type` sia impostata su `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` come illustrato di seguito:

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

    Rimuovere il commento dalla chiamata alla funzione e sostituire i valori segnaposto (incluse le parentesi angolari) con l'ID registrazione del toaster e la chiave di dispositivo derivata generata in precedenza. Il valore di chiave **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** visualizzato di seguito viene fornito solo come esempio.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-toaster-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Salvare il file.

2. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire la soluzione. Nella richiesta di ricompilare il progetto fare clic su **Sì** per ricompilare il progetto prima dell'esecuzione.

    Di seguito è riportato un esempio di output di registrazione del codice della funzione di allocazione personalizzata in esecuzione nel dispositivo toaster. Si noti che per un dispositivo toaster è stato correttamente selezionare un hub. Questa registrazione è disponibile facendo clic su **Log** sotto il codice della funzione nel portale:

    ```cmd
    2020-09-23T11:44:37.505 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=4596d45e-086f-4e86-929b-4a02814eee40)
    2020-09-23T11:44:41.380 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:44:41.381 [Information] Request.Body:...
    2020-09-23T11:44:41.381 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-toaster-007","symmetricKey":{}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:44:41.687 [Information] linkedHub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Selected hub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Response
    2020-09-23T11:44:41.688 [Information] {"iotHubHostName":"contoso-toasters-hub-1098.azure-devices.net"}
    2020-09-23T11:44:41.689 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=4596d45e-086f-4e86-929b-4a02814eee40, Duration=4347ms)    
    ```

    L'output seguente è un esempio di dispositivo toaster simulato che si sta avviando e connettendo correttamente all'istanza del servizio di provisioning che il criterio di allocazione personalizzato assegnerà all'hub IoT dei toaster:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: contoso-toaster-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simulare il dispositivo pompa di calore di Contoso

1. Per simulare il dispositivo pompa di calore, aggiornare di nuovo la chiamata a `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c** con l'ID registrazione del dispositivo pompa di calore e la chiave di dispositivo derivata generata in precedenza. Il valore di chiave **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** visualizzato di seguito viene fornito solo come esempio.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-heatpump-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Salvare il file.

2. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire la soluzione. Nella richiesta di ricompilare il progetto fare clic su **Sì** per ricompilare il progetto prima dell'esecuzione.

    Di seguito è riportato un esempio di output di registrazione del codice della funzione di allocazione personalizzata in esecuzione nel dispositivo pompa di calore. Il criterio di allocazione personalizzato rifiuta questa registrazione con un errore HTTP 400 - Richiesta non valida. Questa registrazione è disponibile facendo clic su **Log** sotto il codice della funzione nel portale:

    ```cmd
    2020-09-23T11:50:23.652 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68)
    2020-09-23T11:50:23.653 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:50:23.654 [Information] Request.Body:...
    2020-09-23T11:50:23.654 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-heatpump-088","symmetricKey":{}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:50:23.654 [Information] Unknown device registration
    2020-09-23T11:50:23.654 [Information] Response
    2020-09-23T11:50:23.654 [Information] Unrecognized device registration.
    2020-09-23T11:50:23.655 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68, Duration=11ms)
    ```

    L'output seguente è un esempio di dispositivo pompa di calore simulato che si sta avviando e connettendo all'istanza del servizio di provisioning per provare a eseguire la registrazione a un hub IoT usando il criterio di allocazione personalizzato. Questa operazione non riesce con l'errore `Custom allocation failed with status code: 400` perché il criterio di allocazione personalizzato è stato progettato per consentire solo i dispositivi toaster:


    ```cmd
    Provisioning API Version: 1.3.7
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_prov_transport_process_json_reply Line:658 Provisioning Failure: OperationId: 4.eb89f3e8407a3711.2525bd34-02e9-4e91-a9c0-4dbc4ad5de66 - Date: 2020-09-23T17:05:58.2363145Z - Msg: Custom allocation failed with status code: 400
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_transport_mqtt_common.c Func:_prov_transport_common_mqtt_dowork Line:1014 Unable to process registration reply.
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_on_transport_registration_data Line:770 Failure retrieving data from the provisioning service
    
    Failure registering device: PROV_DEVICE_RESULT_DEV_AUTH_ERROR
    Press enter key to exit:    
    ```
    
## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare a usare le risorse create in questo articolo, è possibile mantenerle. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create in questo articolo in modo da evitare inutili addebiti.

Questi passaggi presuppongono che tutte le risorse in questo articolo siano state create come indicato nello stesso gruppo di risorse denominato **contoso-us-resource-group**.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se si è creato l'hub IoT all'interno di un gruppo di risorse esistente che contiene risorse che si vogliono conservare, eliminare solo la risorsa dell'hub IoT invece di eliminare il gruppo di risorse.
>

Per eliminare il gruppo di risorse per nome:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.

2. Nella casella di testo **Filtra per nome...** digitare il nome del gruppo di risorse che contiene le risorse di interesse, **contoso-us-resource-group**. 

3. A destra del gruppo di risorse nell'elenco dei risultati selezionare **...** quindi su **Elimina gruppo di risorse**.

4. Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Digitare di nuovo il nome del gruppo di risorse per confermare e quindi selezionare **Elimina**. Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio più approfondito di criterio di allocazione personalizzato, vedere [Come usare i criteri di allocazione personalizzati](how-to-use-custom-allocation-policies.md).
* Per altre informazioni sulla ripetizione del provisioning, vedere [Concetti per la ripetizione del provisioning di dispositivo in hub IoT](concepts-device-reprovision.md).
* Per altre informazioni sul deprovisioning, vedere [Come effettuare il deprovisioning di dispositivi di cui è stato effettuato il provisioning automatico in precedenza ](how-to-unprovision-devices.md).
