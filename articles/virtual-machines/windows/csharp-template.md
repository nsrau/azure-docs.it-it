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
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 738ff9882cffc428f571ab6aea96c0927d2ce443
ms.lasthandoff: 03/31/2017


---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Distribuire una macchina virtuale di Azure con C# e un modello di Azure Resource Manager
Questo articolo descrive come distribuire un modello di Azure Resource Manager tramite C#. Il [modello](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json) consente di distribuire una singola macchina virtuale che esegue Windows Server in una nuova rete virtuale con un'unica subnet.

Per una descrizione dettagliata della risorsa macchina virtuale, vedere [Virtual machines in an Azure Resource Manager template](template-description.md) (Macchine virtuali in un modello di Azure Resource Manager). Per altre informazioni su tutte le risorse in un modello, vedere [Azure Resource Manager template walkthrough](../../azure-resource-manager/resource-manager-template-walkthrough.md) (Procedura dettagliata sui modelli di Azure Resource Manager).

L'esecuzione di questi passaggi richiede circa 10 minuti.

## <a name="step-1-create-a-visual-studio-project"></a>Passaggio 1: creare un progetto di Visual Studio

In questo passaggio, ci si assicura che Visual Studio sia installato e si crea un'applicazione console da usare per distribuire il modello.

1. Se non è già installato, installare [Visual Studio](https://www.visualstudio.com/).
2. In Visual Studio fare clic su **File** > **Nuovo** > **Progetto**.
3. In **Modelli** > **Visual C#** selezionare **Applicazione console**, immettere il nome e il percorso del progetto e fare clic su **OK**.

## <a name="step-2-install-libraries"></a>Passaggio 2: installare le librerie

I pacchetti NuGet sono il modo più semplice per installare le librerie necessarie per completare questi passaggi. Sono necessarie la libreria di Azure Resource Manager e Azure Active Directory Authentication Library per creare le risorse. Per ottenere queste librerie in Visual Studio, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Gestisci pacchetti NuGet** e fare clic su **Sfoglia**.
2. Digitare *Microsoft.IdentityModel.Clients.ActiveDirectory* nella casella di ricerca, quindi fare clic su **Installa** e seguire le istruzioni per installare il pacchetto.
3. Nella parte superiore della pagina selezionare **Includi versione preliminare**. Digitare *Microsoft.Azure.Management.ResourceManager* nella casella di ricerca, quindi fare clic su **Installa** e seguire le istruzioni per installare il pacchetto.

È ora possibile iniziare a usare le librerie per creare l'applicazione.

## <a name="step-3-create-credentials-used-to-authenticate-requests"></a>Passaggio 3: creare le credenziali usate per autenticare le richieste

Prima di iniziare questo passaggio, assicurarsi di avere accesso a un'[entità servizio Active Directory](../../resource-group-authenticate-service-principal.md). Dall'entità servizio si acquisisce un token per autenticare le richieste ad Azure Resource Manager.

1. Aprire il file Program.cs per il progetto creato e quindi aggiungere le istruzioni using seguenti all'inizio del file:

    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Rest;
    using System.IO;
    ```

2. Aggiungere questo metodo alla classe Program per ottenere il token necessario per creare le credenziali:

    ```
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("{client-id}", "{client-secret}");
      var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token.");
      } 
      return token;
    }
    ```

    Sostituire questi valori:
    
    - *{client-id}* con l'identificatore dell'applicazione Azure Active Directory. È possibile trovare questo identificatore nel pannello Proprietà dell'applicazione AD. Per individuare l'applicazione AD nel Portale di Azure, fare clic su **Azure Active Directory** nel menu di risorse e quindi fare clic su **Registrazioni per l'app**.
    - *{client-secret}* con la chiave di accesso dell'applicazione AD. È possibile trovare questo identificatore nel pannello Proprietà dell'applicazione AD.
    - *{tenant-id}* con l'identificatore del tenant della sottoscrizione. È possibile trovare l'identificatore del tenant nel pannello Proprietà di Azure Active Directory nel Portale di Azure. È indicato come *ID directory*.

3. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:

    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. Salvare il file Program.cs.

## <a name="step-4-create-a-resource-group"></a>Passaggio 4: Creare un gruppo di risorse

Sebbene sia possibile creare un gruppo di risorse da un modello, il modello usato dalla raccolta non ne crea uno. In questo passaggio, si aggiunge il codice per creare un gruppo di risorse.

1. Per specificare valori per l'applicazione, aggiungere le variabili al metodo Main della classe Program:

    ```
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var deploymentName = "deploymentName;
    var location = "location";
    ```

    Sostituire questi valori:
    
    - *myResourceGroup* con il nome del gruppo di risorse in fase di creazione.
    - *subscriptionId* con l'ID sottoscrizione. È possibile reperire l'ID sottoscrizione nel pannello Sottoscrizioni nel Portale di Azure.
    - *deploymentName* con il nome della distribuzione.
    - *location* con l'[area di Azure](https://azure.microsoft.com/regions/) in cui si vogliono creare le risorse.

2. Aggiungere questo metodo alla classe Program per creare il gruppo di risorse:

    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location)
    {
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the resource group...");
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:

    ```
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-5-create-a-parameters-file"></a>Passaggio 5: creare un file dei parametri

Per specificare i valori per i parametri delle risorse definiti nel modello, creare un file dei parametri contenente i valori. Il file dei parametri viene usato durante la distribuzione del modello. Il modello usato dalla raccolta richiede valori per i parametri *adminUserName*, *adminPassword* e *dnsLabelPrefix*.

In Visual Studio seguire questa procedura:

1. In Esplora soluzioni fare clic con il pulsante destro del mouse, quindi scegliere **Aggiungi** > **Nuovo elemento**.
2. Fare clic su **Web**, selezionare il **file JSON**, immettere *Parameters.json* come nome e quindi fare clic su **Aggiungi**.
3. Aprire il file Parameters.json e quindi aggiungere il contenuto JSON seguente:

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "mytestacct1" },
        "adminPassword": { "value": "mytestpass1" },
        "dnsLabelPrefix": { "value": "mydns1" }
      }
    }
    ```

    Sostituire i valori dei parametri con valori appropriati all'ambiente.

4. Salvare il file Parameters.json.

## <a name="step-6-deploy-a-template"></a>Passaggio 6: distribuire un modello

In questo esempio si distribuisce un modello dalla raccolta di modelli di Azure e si indicano i valori dei parametri dal file locale creato. 

1. Per distribuire il modello, aggiungere questo metodo alla classe Program:

    ```
    public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
      TokenCredentials credential,
      string groupName,
      string deploymentName,
      string subscriptionId)
    {
    
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the template deployment...");
      var deployment = new Deployment();
      deployment.Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          TemplateLink = new TemplateLink("https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json"),
          Parameters = File.ReadAllText("..\\..\\Parameters.json")
        };
      
      return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
        groupName,
        deploymentName,
        deployment
      );
    }
    ```

    È inoltre possibile distribuire un modello da una cartella locale tramite la proprietà Template anziché la proprietà TemplateLink.

2. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:

    ```
    var dpResult = CreateTemplateDeploymentAsync(
      credential,
      groupName,
      deploymentName,
      subscriptionId);
    Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-7-delete-the-resources"></a>Passaggio 7: eliminare le risorse

Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Non è necessario eliminare separatamente ogni risorsa da un gruppo di risorse. Eliminando il gruppo di risorse, tutte le relative risorse verranno eliminate automaticamente.

1. Per eliminare il gruppo di risorse, aggiungere questo metodo alla classe Program:

   ```
   public static async void DeleteResourceGroupAsync(
     TokenCredentials credential,
     string groupName,
     string subscriptionId)
   {
     Console.WriteLine("Deleting resource group...");
     var resourceManagementClient = new ResourceManagementClient(credential)
       { SubscriptionId = subscriptionId };
     await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
   }
   ```

2. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:

   ```
   DeleteResourceGroupAsync(
     credential,
     groupName,
     subscriptionId);
   Console.ReadLine();
   ```

## <a name="step-8-run-the-console-application"></a>Passaggio 8: Eseguire l'applicazione console

L'esecuzione completa dell'applicazione console dall'inizio alla fine richiederà circa cinque minuti. 

1. Per eseguire l'applicazione console, fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando le stesse credenziali specificate per la sottoscrizione.

2. Premere **INVIO** dopo la visualizzazione dello stato *Riuscito*. 

    Dovrebbe essere visualizzato **1 Riuscito** in Distribuzioni nel pannello Panoramica per il gruppo di risorse nel Portale di Azure.

3. Prima di premere **INVIO** per avviare l'eliminazione delle risorse, è consigliabile dedicare alcuni minuti alla verifica della creazione delle risorse nel Portale di Azure. Fare clic sullo stato della distribuzione per visualizzare le informazioni corrispondenti.

## <a name="next-steps"></a>Passaggi successivi
* Se si sono verificati problemi con la distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
* Informazioni su come distribuire una macchina virtuale e le relative risorse di supporto sono disponibili in [Distribuire una macchina virtuale di Azure con C#](csharp.md).
* Per informazioni su come gestire la macchina virtuale creata, vedere [Gestire macchine virtuali di Azure con Azure Resource Manager e C#](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

