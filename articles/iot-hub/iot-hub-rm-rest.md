---
title: Creare un hub IoT mediante l&quot;API REST del provider di risorse | Documentazione Microsoft
description: Seguire questa esercitazione per iniziare a usare l&quot;API REST del provider di risorse per la creazione di un hub IoT.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 52814ee5-bc10-4abe-9eb2-f8973096c2d8
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 44d73863ebe968a025654f501a820bdf0f9b3c49


---
# <a name="tutorial-create-an-iot-hub-using-a-c-program-and-the-resource-provider-rest-api"></a>Esercitazione: Creare un hub IoT tramite un programma C# e l'API REST del provider di risorse
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduzione
È possibile usare l'[API REST del provider di risorse dell'hub IoT][lnk-rest-api] per creare e gestire hub IoT di Azure a livello di codice. In questa esercitazione viene illustrato come usare l'API REST del provider di risorse hub IoT per creare un hub IoT da un programma C#.

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [modello di distribuzione classica e Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md).  In questo articolo viene illustrato l'uso del modello di distribuzione Azure Resource Manager.
> 
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Microsoft Visual Studio 2015
* Un account Azure attivo. <br/>Se non si ha un account, è possibile crearne uno [gratuito][lnk-free-trial] in pochi minuti.
* [Microsoft Azure PowerShell 1.0][lnk-powershell-install] o versione successiva.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparare il progetto di Visual Studio
1. In Visual Studio creare un nuovo progetto Windows Visual C# usando il modello di progetto **Applicazione console** . Denominare il progetto **CreateIoTHubREST**.
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Gestisci pacchetti NuGet**.
3. In Gestione pacchetti NuGet selezionare **Includi versione preliminare** e cercare **Microsoft.Azure.Management.ResourceManager**. Fare clic su **Installa**, in **Rivedi modifiche** fare clic su **OK**, quindi fare clic su **I Accept** (Accetto) per accettare le licenze.
4. In Gestione pacchetti NuGet cercare **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Fare clic su **Installa**, in **Rivedi modifiche** fare clic su **OK**, quindi fare clic su **I Accept** (Accetto) per accettare la licenza.
5. In Program.cs sostituire il codice esistente **usando** le dichiarazioni con quanto segue:
   
    ```
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
6. In Program.cs aggiungere le seguenti variabili statiche sostituendo i valori dei segnaposto. Nella parte precedente di questa esercitazione si è preso nota di **ApplicationId**, **SubscriptionId**, **TenantId** e **Password**. **Nome gruppo di risorse** è il nome del gruppo di risorse che viene usato quando si crea l'hub IoT; può essere un gruppo di risorse preesistenti o uno nuovo. Il **nome dell'hub IoT** è il nome dell'hub IoT che viene creato, ad esempio **MyIoTHub**, che deve essere globalmente univoco, quindi deve includere il nome o le iniziali dell'utente. **Nome distribuzione** è un nome per la distribuzione, ad esempio **Deployment_01**.
   
    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
   
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Usare l'API REST del provider di risorse per creare un hub IoT
Usare l'[API REST del provider di risorse dell'hub IoT][lnk-rest-api] per creare un hub IoT nel gruppo di risorse. È possibile usare l'API REST del provider di risorse anche per apportare modifiche a un hub IoT esistente.

1. Aggiungere il metodo seguente a Program.cs:
   
    ```
    static void CreateIoTHub(string token)
    {
   
    }
    ```
2. Aggiungere il codice seguente al metodo **CreateIoTHub** per creare un oggetto **HttpClient** con il token di autenticazione nelle intestazioni:
   
    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```
3. Aggiungere il codice seguente al metodo **CreateIoTHub** per descrivere l'hub IoT da creare e generare una rappresentazione JSON. Per un elenco aggiornato delle località in cui è supportato l'hub IoT, vedere lo [Stato di Azure][lnk-status]:
   
    ```
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
4. Aggiungere il codice seguente al metodo **CreateIoTHub** per inviare la richiesta REST ad Azure, verificare la risposta e recuperare l'URL da usare per monitorare lo stato dell'attività di distribuzione:
   
    ```
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
5. Aggiungere il codice seguente alla fine del metodo **CreateIoTHub** per usare l'indirizzo **asyncStatusUri** recuperato nel passaggio precedente per attendere il completamento della distribuzione:
   
    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```
6. Aggiungere il codice seguente alla fine del metodo **CreateIoTHub** per recuperare le chiavi dell'hub IoT creato e stamparle nella console:
   
    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
   
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Compilare ed eseguire l'applicazione
È ora possibile completare l'applicazione chiamando il metodo **CreateIoTHub** prima di compilarla ed eseguirla.

1. Alla fine del metodo **Main** aggiungere il codice seguente:
   
    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
2. Fare clic su **Compila** e quindi su **Compila soluzione**. Correggere eventuali errori.
3. Fare clic su **Debug** e quindi su **Avvia debug** per eseguire l'applicazione. Potrebbero occorrere alcuni minuti per l'esecuzione della distribuzione.
4. Per verificare che l'applicazione abbia aggiunto il nuovo hub IoT, è possibile visitare il [portale di Azure][lnk-azure-portal] e visualizzare l'elenco delle risorse oppure usare il cmdlet di PowerShell **Get-AzureRmResource**.

> [!NOTE]
> Questa applicazione di esempio aggiunge un hub IoT Standard S1 che viene addebitato. Al termine è possibile eliminare l'hub IoT tramite il [portale di Azure][lnk-azure-portal] o usando il cmdlet di PowerShell **Remove-AzureRmResource**.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Dopo aver distribuito un hub IoT mediante l'API REST del provider di risorse, può essere opportuno approfondire gli argomenti seguenti:

* Informazioni sulle funzionalità dell'[API REST del provider di risorse dell'hub IoT][lnk-rest-api].
* Per altre informazioni sulle funzionalità di Azure Resource Manager, vedere la [Panoramica di Azure Resource Manager][lnk-azure-rm-overview].

Per altre informazioni sulle attività di sviluppo per l'hub IoT, vedere quanto segue:

* [Introduzione a C SDK][lnk-c-sdk]
* [Azure IoT SDKs][lnk-sdks] (SDK di IoT di Azure)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Nov16_HO5-->


