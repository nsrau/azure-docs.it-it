<properties
	pageTitle="Creare un hub IoT mediante l'API REST | Microsoft Azure"
	description="Seguire questa esercitazione per iniziare a usare l'API REST per creare un hub IoT."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/23/2015"
     ms.author="dobett"/>

# Esercitazione: Creare un hub IoT tramite un programma C#

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Introduzione

È possibile utilizzare l’[API REST del Provider di risorse hub IoT][lnk-rest-api] per creare e gestire hub IoT di Azure a livello di codice. In questa esercitazione viene illustrato come utilizzare l’API REST del Provider di risorse per creare un hub IoT da un programma C#.

> [AZURE.NOTE] Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md). Questo articolo illustra l’utilizzo del modello di distribuzione Gestione risorse.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

- Microsoft Visual Studio 2015
- Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] o versione successiva.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Preparare il progetto di Visual Studio

1. In Visual Studio creare un nuovo progetto Windows Visual C# usando il modello di progetto **Applicazione console**. Denominare il progetto **CreateIoTHubREST**.

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Gestisci pacchetti NuGet**.

3. In Gestione pacchetti NuGet cercare **Microsoft.Azure.Management.Resources**. Selezionare la versione **2.18.11-preview**. Fare clic su **Installa**, in **Verificare le modifiche**, fare clic su **OK**, quindi fare clic su **Accetto** per accettare le licenze.

4. In Gestione pacchetti NuGet cercare **Microsoft.IdentityModel.Clients.ActiveDirectory**. Selezionare la versione **2.19.208020213**. Fare clic su **Installa**, in **Verificare le modifiche**, fare clic su **OK**, quindi fare clic su **Accetto** per accettare la licenza.

5. In Gestione pacchetti NuGet cercare **Microsoft.Azure.Common**. Selezionare la versione **2.1.0**. Fare clic su **Installa**, in **Verificare le modifiche**, fare clic su **OK**, quindi fare clic su **Accetto** per accettare le licenze.

6. In Program.cs sostituire il codice esistente **usando** le dichiarazioni con quanto segue:

    ```
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    ```
    
7. In Program.cs aggiungere le seguenti variabili statiche sostituendo i valori dei segnaposto. Si è preso nota di **ApplicationId**, **SubscriptionId**, **TenantId** e **Password** in precedenza in questa esercitazione. **Nome gruppo di risorse** è il nome del gruppo di risorse che verrà utilizzato quando si crea l'hub IoT; può essere un gruppo di risorse preesistenti o uno nuovo. **Nome IoT Hub** è il nome dell'hub IoT che verrà creato, ad esempio **MyIoTHub**. **Nome distribuzione** è un nome per la distribuzione, ad esempio **Deployment\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Utilizzare l'API REST per creare un hub IoT

Utilizzare l'API REST del Provider di risorse dell’hub IoT per creare un nuovo hub IoT nel gruppo di risorse. È anche possibile utilizzare l’[API REST][lnk-rest-api] per apportare modifiche a un hub IoT esistente.

1. Aggiungere il metodo seguente a Program.cs:
    
    ```
    static bool CreateIoTHub(ResourceManagementClient client, string token)
    {
        
    }
    ```

2. Aggiungere il codice seguente al metodo **CreateIoTHub** per aggiungere il token di autenticazione alla richiesta:

    ```
    client.HttpClient.DefaultRequestHeaders.Authorization = 
      new AuthenticationHeaderValue("Bearer", token);
    ```

3. Aggiungere il codice seguente al metodo **CreateIoTHub** per descrivere l'hub IoT per creare e generare una rappresentazione JSON:

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
    
    var content = new StringContent(
      JsonConvert.SerializeObject(description),
      Encoding.UTF8, "application/json");
    ```

4. Aggiungere il codice seguente al metodo **CreateIoTHub** per inviare la richiesta REST di Azure e verifica la risposta:

    ```
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2015-08-15-preview",
      subscriptionId, rgName, iotHubName);
    var httpsRepsonse = client.HttpClient.PutAsync(
      requestUri, content).Result;

    if (!httpsRepsonse.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", httpsRepsonse.Content.ReadAsStringAsync().Result);
      return false;
    }
    ```

5. Aggiungere il codice seguente alla fine del metodo **CreateIoTHub** per attendere il completamento della distribuzione:

    ```
    ResourceGetResult resourceGetResult = null;
    do
    {
    resourceGetResult = client.Resources.GetAsync(
      rgName,
      new ResourceIdentity()
      {
        ResourceName = iotHubName,
        ResourceProviderApiVersion = "2015-08-15-preview",
        ResourceProviderNamespace = "Microsoft.Devices",
        ResourceType = "IotHubs"
      }).Result;
    Console.WriteLine("IoTHub state {0}", 
      resourceGetResult.Resource.ProvisioningState);
    } while (resourceGetResult.Resource.ProvisioningState != "Succeeded" 
          && resourceGetResult.Resource.ProvisioningState != "Failed");
    
    if (resourceGetResult.Resource.ProvisioningState != "Succeeded")
    {
        Console.WriteLine("Failed to create iothub");
        return false;
    }
    return true;
    ```

[AZURE.INCLUDE [iot-hub-retrieve-keys](../../includes/iot-hub-retrieve-keys.md)]

## Compilare ed eseguire l'applicazione

È ora possibile completare l'applicazione chiamando il metodo **CreateIoTHub** e **ShowIoTHubKeys** prima di compilarla ed eseguirla.

1. Alla fine del metodo **Main** aggiungere il codice seguente:

    ```
    if (CreateIoTHub(client, token.AccessToken))
        ShowIoTHubKeys(client, token.AccessToken);
    Console.ReadLine();
    ```
    
2. Fare clic su **Compila** e quindi su **Compila soluzione**. Correggere eventuali errori.

3. Fare clic su **Debug** e quindi su **Avvia debug** per eseguire l'applicazione. Potrebbero occorrere alcuni minuti per l'esecuzione della distribuzione.

4. È possibile verificare che l'applicazione abbia aggiunto il nuovo hub IoT visitando il [portale][lnk-azure-portal] e visualizzare l'elenco di risorse, oppure utilizzando il cmdlet PowerShell **Get-AzureRmResource**.

> [AZURE.NOTE] Questa applicazione di esempio aggiunge un hub IoT Standard S1 che verrà addebitato. È possibile eliminare l'hub IoT tramite il [portale][lnk-azure-portal] o utilizzando il cmdlet PowerShell **Remove AzureRmResource** al termine.

## Passaggi successivi

- Esplorare le funzionalità di [API REST del provider di risorse Hub IoT][lnk-rest-api].
- Per ulteriori informazioni sulle funzionalità di Gestione risorse di Azure, leggere la [Panoramica su Gestione risorse di Azure][lnk-azure-rm-overview].

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: https://azure.microsoft.com/it-IT/blog/azps-1-0-pre/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: https://azure.microsoft.com/documentation/articles/resource-group-overview/

<!---HONumber=AcomDC_0204_2016-->