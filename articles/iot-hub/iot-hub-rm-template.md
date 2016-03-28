<properties
	pageTitle="Creare un hub IoT usando un modello di Gestione risorse di Azure e C# | Microsoft Azure"
	description="Seguire questa esercitazione per iniziare a usare modelli di Gestione risorse per creare un hub IoT con un programma C#."
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
     ms.date="02/12/2016"
     ms.author="dobett"/>

# Creare un hub IoT tramite un programma C# con un modello di Gestione risorse di Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Introduzione

È possibile usare Gestione risorse di Azure per creare e gestire hub IoT di Azure a livello di codice. In questa esercitazione viene illustrato come utilizzare un modello di gestione risorse per creare un hub IoT da un programma C#.

> [AZURE.NOTE] Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md). Questo articolo illustra l’utilizzo del modello di distribuzione Gestione risorse.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

- Microsoft Visual Studio 2015
- Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk-free-trial].
- Un [account di archiviazione di Azure][lnk-storage-account] in cui è possibile archiviare i file del modello.
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] o versione successiva.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Preparare il progetto di Visual Studio

1. In Visual Studio creare un nuovo progetto Windows Visual C# usando il modello di progetto **Applicazione console**. Denominare il progetto **CreateIoTHub**.

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Gestisci pacchetti NuGet**.

3. In Gestione pacchetti NuGet selezionare **Includi versione preliminare** e cercare **Microsoft.Azure.Management.Resources**. Fare clic su **Installa**, in **Verificare le modifiche**, fare clic su **OK**, quindi fare clic su **Accetto** per accettare le licenze.

4. In Gestione pacchetti NuGet cercare **Microsoft.IdentityModel.Clients.ActiveDirectory**. Fare clic su **Installa**, in **Verificare le modifiche**, fare clic su **OK**, quindi fare clic su **Accetto** per accettare la licenza.

5. In Gestione pacchetti NuGet cercare **Microsoft.Azure.Common**. Fare clic su **Installa**, in **Verificare le modifiche**, fare clic su **OK**, quindi fare clic su **Accetto** per accettare le licenze.

6. In Program.cs sostituire il codice esistente **usando** le dichiarazioni con quanto segue:

    ```
    using System;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
7. In Program.cs aggiungere le seguenti variabili statiche sostituendo i valori dei segnaposto. Si è preso nota di **ApplicationId**, **SubscriptionId**, **TenantId** e **Password** in precedenza in questa esercitazione. **Il nome dell'account di archiviazione** è il nome dell'account di archiviazione di Azure in cui verranno archiviati i file del modello. **Nome gruppo di risorse** è il nome del gruppo di risorse che verrà utilizzato quando si crea l'hub IoT; può essere un gruppo di risorse preesistenti o uno nuovo. **Nome distribuzione** è un nome per la distribuzione, ad esempio **Deployment\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Inviare un modello per creare un hub IoT

Usare un modello JSON e un file di parametri per creare un nuovo hub IoT nel gruppo di risorse. È anche possibile utilizzare un modello per apportare modifiche a un hub IoT esistente.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, fare clic su **Aggiungi** e infine su **Nuovo elemento**. Aggiungere un nuovo file JSON denominato **template.json** al progetto.

2. Sostituire il contenuto di **template.json** con la seguente definizione di risorsa per aggiungere un nuovo hub IoT standard per l’area **Stati Uniti orientali**:

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, fare clic su **Aggiungi** e infine su **Nuovo elemento**. Aggiungere un nuovo file JSON denominato **parameters.json** al progetto.

4. Sostituire il contenuto di **parameters.json** con le informazioni di parametro seguenti che impostano il nome del nuovo hub IoT su **mynewiothub**:

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. In **Esplora server** connettersi alla sottoscrizione di Azure e nell'account di archiviazione creare un nuovo contenitore denominato **templates**. Nel pannello **Proprietà** impostare le autorizzazioni **Accesso in lettura pubblico** per il contenitore **templates** su **BLOB**.

6. In **Esplora server** fare clic con il pulsante destro del mouse sul contenitore **templates** e quindi fare clic su **Visualizza contenitore BLOB**. Fare clic sul pulsante **Carica BLOB**, selezionare i due file **parameters.json** e **templates.json** e quindi fare clic su **Apri** per caricare i file JSON nel contenitore **templates**. Gli URL dei BLOB contenenti i dati JSON sono:

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.windows.net/templates/template.json
    ```

7. Aggiungere il metodo seguente a Program.cs:
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Aggiungere il codice seguente al metodo **CreateIoTHub** per inviare i file di modello e di parametri a Gestione risorse di Azure:

    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

6. Aggiungere il codice seguente al metodo **CreateIoTHub** che visualizza lo stato e le chiavi del nuovo hub IoT:

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## Compilare ed eseguire l'applicazione

È ora possibile completare l'applicazione chiamando il metodo **CreateIoTHub** prima di compilarla ed eseguirla.

1. Alla fine del metodo **Main** aggiungere il codice seguente:

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. Fare clic su **Compila** e quindi su **Compila soluzione**. Correggere eventuali errori.

3. Fare clic su **Debug** e quindi su **Avvia debug** per eseguire l'applicazione. Potrebbero occorrere alcuni minuti per l'esecuzione della distribuzione.

4. È possibile verificare che l'applicazione abbia aggiunto il nuovo hub IoT visitando il [portale][lnk-azure-portal] e visualizzare l'elenco di risorse, oppure utilizzando il cmdlet PowerShell **Get-AzureRmResource**.

> [AZURE.NOTE] Questa applicazione di esempio aggiunge un hub IoT Standard S1 che verrà addebitato. È possibile eliminare l'hub IoT tramite il [portale][lnk-azure-portal] o utilizzando il cmdlet PowerShell **Remove AzureRmResource** al termine.

## Passaggi successivi

Dopo aver distribuito un hub IoT usando un modello di Gestione risorse di Azure con un programma C#, può essere opportuno ottenere informazioni più dettagliate:

- Informazioni sulle funzionalità dell'[API REST del provider di risorse dell'hub IoT][lnk-rest-api].
- Per ulteriori informazioni sulle funzionalità di Gestione risorse di Azure, leggere la [Panoramica su Gestione risorse di Azure][lnk-azure-rm-overview].

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

<!---HONumber=AcomDC_0316_2016-->