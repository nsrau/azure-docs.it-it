---
title: Distribuire una macchina virtuale con C# e un modello di Azure Resource Manager | Microsoft Docs
description: Informazioni su come usare C# e un modello di Resource Manager per distribuire una macchina virtuale di Azure.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: davidmu
ms.openlocfilehash: bd1c860db026f948202cd1f3aa763b4547c597b4
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Distribuire una macchina virtuale di Azure con C# e un modello di Azure Resource Manager
Questo articolo descrive come distribuire un modello di Azure Resource Manager tramite C#. Il modello creato consente di distribuire una singola macchina virtuale che esegue Windows Server in una nuova rete virtuale con un'unica subnet.

Per una descrizione dettagliata della risorsa macchina virtuale, vedere [Virtual machines in an Azure Resource Manager template](template-description.md) (Macchine virtuali in un modello di Azure Resource Manager). Per altre informazioni su tutte le risorse in un modello, vedere [Azure Resource Manager template walkthrough](../../azure-resource-manager/resource-manager-template-walkthrough.md) (Procedura dettagliata sui modelli di Azure Resource Manager).

L'esecuzione di questi passaggi richiede circa 10 minuti.

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

In questo passaggio, ci si assicura che Visual Studio sia installato e si crea un'applicazione console da usare per distribuire il modello.

1. Se non è già installato, installare [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selezionare **Sviluppo per desktop .NET** nella pagina Carichi di lavoro e quindi fare clic su **Installa**. Nel riepilogo si noti che **Strumenti di sviluppo per .NET Framework 4-4.6** viene selezionato automaticamente. Se Visual Studio è già stato installato, è possibile aggiungere il carico di lavoro .NET usando l'utilità di avvio di Visual Studio.
2. In Visual Studio fare clic su **File** > **Nuovo** > **Progetto**.
3. In **Modelli** > **Visual C#** selezionare **App console (.NET Framework)**, immettere *myDotnetProject* come nome del progetto, selezionare il percorso del progetto e quindi fare clic su **OK**.

## <a name="install-the-packages"></a>Installare i pacchetti

I pacchetti NuGet sono il modo più semplice per installare le librerie necessarie per completare questi passaggi. Per ottenere le librerie necessarie in Visual Studio, eseguire questa procedura:

1. Fare clic su **Strumenti** > **Gestione pacchetti Nuget** e quindi su **Console di Gestione Pacchetti**.
2. Nella console digitare questi comandi:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Creare i file

In questo passaggio si crea un file di modello che consente di distribuire le risorse e un file di parametri che fornisce i valori dei parametri nel modello. È possibile anche creare un file di autorizzazione da usare per eseguire operazioni in Azure Resource Manager.

### <a name="create-the-template-file"></a>Creare il file di modello

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su *myDotnetProject* > **Aggiungi** > **Nuovo elemento** e quindi selezionare **File di testo** in *Elementi di Visual C#*. Assegnare un nome al file *CreateVMTemplate.json* e quindi fare clic su **Aggiungi**.
2. Aggiungere questo codice JSON al file appena creato:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. Salvare il file CreateVMTemplate.json.

### <a name="create-the-parameters-file"></a>Creare il file dei parametri

Per specificare i valori per i parametri delle risorse definiti nel modello, creare un file dei parametri contenente i valori.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su *myDotnetProject* > **Aggiungi** > **Nuovo elemento** e quindi selezionare **File di testo** in *Elementi di Visual C#*. Assegnare un nome al file *Parameters.json* e quindi fare clic su **Aggiungi**.
2. Aggiungere questo codice JSON al file appena creato:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. Salvare il file Parameters.json.

### <a name="create-the-authorization-file"></a>Creare il file di autorizzazione

Prima di poter distribuire un modello, è necessario assicurarsi di avere accesso a un'[entità servizio Active Directory](../../resource-group-authenticate-service-principal.md). Dall'entità servizio si acquisisce un token per autenticare le richieste ad Azure Resource Manager. È necessario anche registrare l'ID dell'applicazione, la chiave di autenticazione e l'ID del tenant necessari nel file di autorizzazione.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su *myDotnetProject* > **Aggiungi** > **Nuovo elemento** e quindi selezionare **File di testo** in *Elementi di Visual C#*. Assegnare al file il nome *azureauth.properties* e quindi fare clic su **Aggiungi**.
2. Aggiungere le proprietà di autorizzazione seguenti:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Sostituire **&lt;subscription-id&gt;** con l'identificatore della sottoscrizione, **&lt;application-id&gt;** con l'identificatore dell'applicazione Active Directory, **&lt;authentication-key&gt;** con la chiave dell'applicazione e **&lt;tenant-id&gt;** con l'identificatore del tenant.

3. Salvare il file azureauth.properties.
4. Impostare una variabile di ambiente Windows denominata AZURE_AUTH_LOCATION con il percorso completo al file di autorizzazione creato. È possibile usare, ad esempio, il comando PowerShell seguente:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```
    
## <a name="create-the-management-client"></a>Creare il client di gestione

1. Aprire il file Program.cs per il progetto creato e quindi aggiungere le istruzioni using seguenti alle istruzioni esistenti all'inizio del file:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Per creare il client di gestione, aggiungere questo codice al metodo Main:

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per specificare i valori per l'applicazione, aggiungere il codice necessario al metodo Main:

```
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Il modello e i parametri vengono distribuiti da un account di archiviazione in Azure. In questo passaggio si creerà l'account e si caricheranno i file. 

Per creare l'account, aggiungere questo codice al metodo Main:

```
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFile("..\\..\\CreateVMTemplate.json");

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFile("..\\..\\Parameters.json");
```

## <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello e i parametri dall'account di archiviazione appena creato. 

Per distribuire il modello, aggiungere questo codice al metodo Main:

```
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>Eliminare le risorse

Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Non è necessario eliminare separatamente ogni risorsa da un gruppo di risorse. Eliminando il gruppo di risorse, tutte le relative risorse verranno eliminate automaticamente. 

Per eliminare il gruppo di risorse, aggiungere questo codice al metodo Main:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Eseguire l'applicazione

L'esecuzione completa dell'applicazione console dall'inizio alla fine richiederà circa cinque minuti. 

1. Per eseguire l'applicazione console, fare clic su **Avvia**.

2. Prima di premere **INVIO** per avviare l'eliminazione delle risorse, è consigliabile dedicare alcuni minuti alla verifica della creazione delle risorse nel Portale di Azure. Fare clic sullo stato della distribuzione per visualizzare le informazioni corrispondenti.

## <a name="next-steps"></a>Passaggi successivi
* Se si sono verificati problemi con la distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
* Informazioni su come distribuire una macchina virtuale e le relative risorse di supporto sono disponibili in [Distribuire una macchina virtuale di Azure con C#](csharp.md).
