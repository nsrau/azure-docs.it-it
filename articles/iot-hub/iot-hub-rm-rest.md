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
     ms.date="05/31/2016"
     ms.author="dobett"/>

# Esercitazione: Creare un hub IoT tramite un programma C# e l'API REST

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

3. In Gestione pacchetti NuGet selezionare **Includi versione preliminare** e cercare **Microsoft.Azure.Management.ResourceManager**. Fare clic su **Installa**, in **Verificare le modifiche**, fare clic su **OK**, quindi fare clic su **Accetto** per accettare le licenze.

4. In Gestione pacchetti NuGet cercare **Microsoft.IdentityModel.Clients.ActiveDirectory**. Fare clic su **Installa**, in **Verificare le modifiche**, fare clic su **OK**, quindi fare clic su **Accetto** per accettare la licenza.

6. In Program.cs sostituire il codice esistente **usando** le dichiarazioni con quanto segue:

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
    using Newtonsoft.Json;
    ```
    
7. In Program.cs aggiungere le seguenti variabili statiche sostituendo i valori dei segnaposto. Si è preso nota di **ApplicationId**, **SubscriptionId**, **TenantId** e **Password** in precedenza in questa esercitazione. **Nome gruppo di risorse** è il nome del gruppo di risorse che verrà utilizzato quando si crea l'hub IoT; può essere un gruppo di risorse preesistenti o uno nuovo. Il **nome dell'hub IoT** è il nome dell'hub IoT che verrà creato, ad esempio **MyIoTHub**, che deve essere globalmente univoco, quindi deve includere il nome o le iniziali dell'utente. **Nome distribuzione** è un nome per la distribuzione, ad esempio **Deployment\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Utilizzare l'API REST per creare un hub IoT

Usare l'[API REST dell'hub IoT][lnk-rest-api] per creare un nuovo hub IoT nel gruppo di risorse. È anche possibile usare l'API REST per apportare modifiche a un hub IoT esistente.

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
    } while (body == "{"Status":"Running"}");
    ```

6. Aggiungere il codice seguente alla fine del metodo **CreateIoTHub** per recuperare le chiavi dell'hub IoT creato e stamparle nella console:

    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
    
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```
    
## Compilare ed eseguire l'applicazione

È ora possibile completare l'applicazione chiamando il metodo **CreateIoTHub** prima di compilarla ed eseguirla.

1. Alla fine del metodo **Main** aggiungere il codice seguente:

    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
    
2. Fare clic su **Compila** e quindi su **Compila soluzione**. Correggere eventuali errori.

3. Fare clic su **Debug** e quindi su **Avvia debug** per eseguire l'applicazione. Potrebbero occorrere alcuni minuti per l'esecuzione della distribuzione.

4. È possibile verificare che l'applicazione abbia aggiunto il nuovo hub IoT visitando il [portale][lnk-azure-portal] e visualizzare l'elenco di risorse, oppure utilizzando il cmdlet PowerShell **Get-AzureRmResource**.

> [AZURE.NOTE] Questa applicazione di esempio aggiunge un hub IoT Standard S1 che verrà addebitato. È possibile eliminare l'hub IoT tramite il [portale][lnk-azure-portal] o utilizzando il cmdlet PowerShell **Remove AzureRmResource** al termine.

## Passaggi successivi

Dopo aver distribuito un hub IoT mediante l'API REST, può essere opportuno ottenere informazioni più dettagliate:

- Esplorare le funzionalità di [API REST del provider di risorse Hub IoT][lnk-rest-api].
- Per altre informazioni sulle funzionalità di Azure Resource Manager, leggere la [Panoramica su Azure Resource Manager][lnk-azure-rm-overview].

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md

<!---HONumber=AcomDC_0601_2016-->