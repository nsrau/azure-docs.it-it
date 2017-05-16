---
title: Creare un hub IoT di Azure con l&quot;API REST del provider di risorse | Documentazione Microsoft
description: Come usare l&quot;API REST del provider di risorse per creare un hub IoT.
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
ms.date: 05/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: e9185862bd1f15adacf7fd407a6f5165b2b337f5
ms.lasthandoff: 03/10/2017


---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Creare un hub IoT con l'API REST del provider di risorse (.NET)
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduzione
È possibile usare l'[API REST del provider di risorse dell'hub IoT][lnk-rest-api] per creare e gestire hub IoT di Azure a livello di codice. In questa esercitazione viene illustrato come usare l'API REST del provider di risorse hub IoT per creare un hub IoT da un programma C#.

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [modello di distribuzione classica e Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md).  In questo articolo viene illustrato l'uso del modello di distribuzione Azure Resource Manager.
> 
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Visual Studio 2015 o Visual Studio 2017.
* Un account Azure attivo. <br/>Se non si ha un account, è possibile crearne uno [gratuito][lnk-free-trial] in pochi minuti.
* [Azure PowerShell 1.0][lnk-powershell-install] o versione successiva.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparare il progetto di Visual Studio
1. In Visual Studio creare un progetto desktop classico di Windows Visual C# usando il modello di progetto **App console (.NET Framework)**. Denominare il progetto **CreateIoTHubREST**.
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Gestisci pacchetti NuGet**.
3. In Gestione pacchetti NuGet selezionare **Includi versione preliminare** e nella pagina **Sfoglia** cercare **Microsoft.Azure.Management.ResourceManager**. Selezionare il pacchetto, fare clic su **Installa**, in **Rivedi modifiche** fare clic su **OK**, quindi fare clic su **I Accept** (Accetto) per accettare le licenze.
4. In Gestione pacchetti NuGet cercare **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Fare clic su **Installa**, in **Rivedi modifiche** fare clic su **OK**, quindi fare clic su **I Accept** (Accetto) per accettare la licenza.
5. In Program.cs sostituire le istruzioni **using** esistenti con il codice seguente:
   
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
2. Aggiungere il codice seguente al metodo **CreateIoTHub**. Questo codice crea un oggetto **HttpClient** con il token di autenticazione nelle intestazioni:
   
    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```
3. Aggiungere il codice seguente al metodo **CreateIoTHub**. Questo codice descrive l'hub IoT per creare e genera una rappresentazione JSON. Per un elenco aggiornato delle località in cui è supportato l'hub IoT, vedere lo [Stato di Azure][lnk-status]:
   
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
4. Aggiungere il codice seguente al metodo **CreateIoTHub**. Questo codice invia la richiesta REST ad Azure, verifica la risposta e recupera l'URL da usare per monitorare lo stato dell'attività di distribuzione:
   
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
5. Alla fine del metodo **CreateIoTHub** aggiungere il codice seguente. Questo codice usa l'indirizzo **asyncStatusUri** recuperato nel passaggio precedente per attendere il completamento della distribuzione:
   
    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```
6. Alla fine del metodo **CreateIoTHub** aggiungere il codice seguente. Questo codice recupera le chiavi dell'hub IoT creato e le visualizza nella console:
   
    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
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

Per altre informazioni sulle attività di sviluppo per l'hub IoT, vedere gli articoli seguenti:

* [Introduzione a C SDK][lnk-c-sdk]
* [Azure IoT SDKs][lnk-sdks] (SDK di IoT di Azure)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

