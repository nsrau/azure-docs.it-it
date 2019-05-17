---
title: Creare un hub IoT di Azure con l'API REST del provider di risorse | Documentazione Microsoft
description: Come usare l'API REST del provider di risorse per creare un hub IoT.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 6d91f5e61dfd7c3cb4d1869edf0c6cb8c2c85190
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827482"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Creare un hub IoT con l'API REST del provider di risorse (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

È possibile usare la [provider di risorse dell'IoT Hub API REST](https://docs.microsoft.com/rest/api/iothub/iothubresource) per creare e gestire hub IoT di Azure a livello di codice. In questa esercitazione viene illustrato come usare l'API REST del provider di risorse hub IoT per creare un hub IoT da un programma C#.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Visual Studio.

* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* [Azure PowerShell 1.0](https://docs.microsoft.com/powershell/azure/install-Az-ps) o versione successiva.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparare il progetto di Visual Studio

1. In Visual Studio creare un progetto desktop classico di Windows Visual C# usando il modello di progetto **App console (.NET Framework)**. Denominare il progetto **CreateIoTHubREST**.

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Gestisci pacchetti NuGet**.

3. In Gestione pacchetti NuGet selezionare **Includi versione preliminare** e nella pagina **Sfoglia** cercare **Microsoft.Azure.Management.ResourceManager**. Selezionare il pacchetto, fare clic su **Installa**, in **Rivedi modifiche** fare clic su **OK**, quindi fare clic su **I Accept** (Accetto) per accettare le licenze.

4. In Gestione pacchetti NuGet cercare **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Fare clic su **Installa**, in **Rivedi modifiche** fare clic su **OK**, quindi fare clic su **I Accept** (Accetto) per accettare la licenza.

5. In Program.cs sostituire le istruzioni **using** esistenti con il codice seguente:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. In Program.cs aggiungere le seguenti variabili statiche sostituendo i valori dei segnaposto. Nella parte precedente di questa esercitazione si è preso nota di **ApplicationId**, **SubscriptionId**, **TenantId** e **Password**. **Nome gruppo di risorse** è il nome del gruppo di risorse che viene usato quando si crea l'hub IoT. È possibile usare un gruppo di risorse preesistente o nuovo. **Nome hub IoT** è il nome dell'hub IoT creato, ad esempio **MioHubIoT**. Il nome dell'hub IoT deve essere globalmente univoco. **Nome distribuzione** è un nome per la distribuzione, ad esempio **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
   
    [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Usare l'API REST del provider di risorse per creare un hub IoT

Usare la [provider di risorse dell'IoT Hub API REST](https://docs.microsoft.com/rest/api/iothub/iothubresource) per creare un hub IoT nel gruppo di risorse. È possibile usare l'API REST del provider di risorse anche per apportare modifiche a un hub IoT esistente.

1. Aggiungere il metodo seguente a Program.cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Aggiungere il codice seguente al metodo **CreateIoTHub**. Questo codice crea un oggetto **HttpClient** con il token di autenticazione nelle intestazioni:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Aggiungere il codice seguente al metodo **CreateIoTHub**. Questo codice descrive l'hub IoT per creare e genera una rappresentazione JSON. Per un elenco aggiornato delle località in cui è supportato l'IoT Hub, vedere [stato di Azure](https://azure.microsoft.com/status/):

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Aggiungere il codice seguente al metodo **CreateIoTHub**. Questo codice invia la richiesta REST ad Azure. Il codice verifica quindi la risposta e recupera l'URL da usare per monitorare lo stato dell'attività di distribuzione:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Alla fine del metodo **CreateIoTHub** aggiungere il codice seguente. Questo codice usa l'indirizzo **asyncStatusUri** recuperato nel passaggio precedente per attendere il completamento della distribuzione:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Alla fine del metodo **CreateIoTHub** aggiungere il codice seguente. Questo codice recupera le chiavi dell'hub IoT creato e le visualizza nella console:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Compilare ed eseguire l'applicazione

È ora possibile completare l'applicazione chiamando il metodo **CreateIoTHub** prima di compilarla ed eseguirla.

1. Alla fine del metodo **Main** aggiungere il codice seguente:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Fare clic su **Compila** e quindi su **Compila soluzione**. Correggere eventuali errori.

3. Fare clic su **Debug** e quindi su **Avvia debug** per eseguire l'applicazione. Potrebbero occorrere alcuni minuti per l'esecuzione della distribuzione.

4. Per verificare che l'applicazione abbia aggiunto il nuovo hub IoT, visitare il [portale di Azure](https://portal.azure.com/) e visualizzare l'elenco delle risorse. In alternativa, usare il **Get-AzResource** cmdlet di PowerShell.

> [!NOTE]
> Questa applicazione di esempio aggiunge un hub IoT Standard S1 che viene addebitato. Al termine, è possibile eliminare l'hub IoT tramite il [portale di Azure](https://portal.azure.com/) oppure usando la **Remove-AzResource** cmdlet di PowerShell dopo averli completati.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito un hub IoT mediante l'API REST del provider di risorse, può essere opportuno approfondire gli argomenti seguenti:

* Informazioni sulle funzionalità dei [provider di risorse dell'IoT Hub API REST](https://docs.microsoft.com/rest/api/iothub/iothubresource).

* Lettura [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) per altre informazioni sulle funzionalità di Azure Resource Manager.

Per altre informazioni sulle attività di sviluppo per l'hub IoT, vedere gli articoli seguenti:

* [Introduzione a C SDK](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)