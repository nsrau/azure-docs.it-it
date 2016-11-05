---
title: Distribuire una VM con C# e un modello di Azure Resource Manager | Microsoft Docs
description: Informazioni su come usare C# e un modello di Resource Manager per distribuire una macchina virtuale di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: davidmu

---
# <a name="deploy-an-azure-virtual-machine-using-c#-and-a-resource-manager-template"></a>Distribuire una macchina virtuale di Azure con C# e un modello di Azure Resource Manager
L'uso di gruppi di risorse e modelli permette di gestire insieme tutte le risorse che supportano l'applicazione. Questo articolo illustra come usare Visual Studio e C# per configurare l'autenticazione, creare un modello e quindi distribuire le risorse di Azure con il modello creato.

È prima necessario assicurarsi di aver eseguito queste operazioni di configurazione:

* Installare [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* Verificare l'installazione di [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
* Ottenere un [token di autenticazione](../resource-group-authenticate-service-principal.md)
* Creare un gruppo di risorse usando [Azure PowerShell](../resource-group-template-deploy.md), l'[interfaccia della riga di comando di Azure](../resource-group-template-deploy-cli.md) o il [portale di Azure](../resource-group-template-deploy-portal.md).

L'esecuzione di questi passaggi richiede circa 30 minuti.

## <a name="step-1:-create-the-visual-studio-project,-the-template-file,-and-the-parameters-file"></a>Passaggio 1: Creare il progetto di Visual Studio, il file modello e i file di parametri
### <a name="create-the-template-file"></a>Creare il file di modello
Un modello di Azure Resource Manager consente di distribuire e gestire insieme le risorse di Azure. Il modello è una descrizione JSON delle risorse e dei parametri di distribuzione associati.

In Visual Studio seguire questa procedura:

1. Fare clic su **File** > **Nuovo** > **Progetto**.
2. In **Modelli** > **Visual C#** selezionare **Applicazione console**, immettere il nome e il percorso del progetto e fare clic su **OK**.
3. In Esplora soluzioni fare clic con il pulsante destro del mouse, quindi scegliere **Aggiungi** > **Nuovo elemento**.
4. Fare clic su Web, selezionare il file JSON, immettere *VirtualMachineTemplate.json* come nome e quindi fare clic su **Aggiungi**.
5. Nelle parentesi di apertura e chiusura del file VirtualMachineTemplate.json aggiungere l'elemento dello schema e l'elemento contentVersion obbligatori:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }
6. I [parametri](../resource-group-authoring-templates.md#parameters) non sono sempre necessari, ma consentono di immettere i valori quando il modello viene distribuito. Aggiungere l'elemento parameters e i rispettivi elementi figlio dopo l'elemento contentVersion:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }
7. [variabili](../resource-group-authoring-templates.md#variables) possono essere usate in un modello per specificare i valori che possono subire modifiche frequenti o che devono essere creati da una combinazione di valori dei parametri. Aggiungere l'elemento variables dopo la sezione parameters:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
8. [risorse](../resource-group-authoring-templates.md#resources) , ad esempio la macchina virtuale, la rete virtuale e l'account di archiviazione, vengono definite più avanti nel modello. Aggiungere la sezione resources dopo la sezione variables:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
9. Salvare il file di modello creato.

### <a name="create-the-parameters-file"></a>Creare il file dei parametri
Per specificare i valori per i parametri delle risorse definiti nel modello, creare un file dei parametri contenente i valori usati nella distribuzione del modello. In Visual Studio seguire questa procedura:

1. In Esplora soluzioni fare clic con il pulsante destro del mouse, quindi scegliere **Aggiungi** > **Nuovo elemento**.
2. Fare clic su Web, selezionare il file JSON, immettere *Parameters.json* come nome e quindi fare clic su **Aggiungi**.
3. Aprire il file parameters.json e quindi aggiungere il contenuto JSON seguente:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }
   
   > [!NOTE]
   > Questo articolo crea una macchina virtuale che esegue una versione del sistema operativo Windows Server. Per altre informazioni sulla selezione di altre immagini, vedere [Esplorare e selezionare immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-ps-findimage.md).
   > 
   > 
4. Salvare il file dei parametri creato.

## <a name="step-2:-install-the-libraries"></a>Passaggio 2: Installare le librerie
I pacchetti NuGet sono il modo più semplice per installare le librerie necessarie per completare questa esercitazione. Sono necessarie la libreria di Azure Resource Management e Azure Active Directory Authentication Library per creare le risorse. Per ottenere queste librerie in Visual Studio, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Gestisci pacchetti NuGet** e fare clic su Sfoglia.
2. Digitare *Active Directory* nella casella di ricerca, fare clic su **Installa** per il pacchetto di Active Directory Authentication Library, quindi seguire le istruzioni per l'installazione del pacchetto.
3. Nella parte superiore della pagina selezionare **Includi versione preliminare**. Digitare *Microsoft.Azure.Management.ResourceManager* nella casella di ricerca, fare clic su **Installa** per le librerie di gestione risorse di Microsoft Azure e quindi seguire le istruzioni per l'installazione del pacchetto.

È ora possibile iniziare a usare le librerie per creare l'applicazione.

## <a name="step-3:-create-the-credentials-that-are-used-to-authenticate-requests"></a>Passaggio 3: Creare le credenziali usate per autenticare le richieste
Verrà creata l'applicazione Azure Active Directory e verrà installata la libreria di autenticazione. Le informazioni sull'applicazione verranno ora formattate come credenziali usate per autenticare le richieste ad Azure Resource Manager.

1. Aprire il file Program.cs per il progetto creato e quindi aggiungere le istruzioni using seguenti all'inizio del file:
   
        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;
2. Aggiungere questo metodo alla classe Program per ottenere il token necessario per creare le credenziali:
   
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
   
   Sostituire {client-id} con l'identificatore dell'applicazione Azure Active Directory, {client-secret} con la chiave di accesso dell'applicazione di Active Directory e {tenant-id} con l'identificatore del tenant per la sottoscrizione. Per trovare l'ID tenant, eseguire Get-AzureRmSubscription. È possibile trovare la chiave di accesso mediante il portale di Azure.
3. Per creare le credenziali, aggiungere questo codice al metodo Main nel file Program.cs:
   
        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
4. Salvare il file Program.cs.

## <a name="step-4:-deploy-the-template"></a>Passaggio 4: Distribuire il modello
In questo passaggio si userà il gruppo di risorse creato in precedenza, ma è anche possibile creare un gruppo di risorse usando le classi [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) e [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx).

1. Aggiungere le variabili al metodo Main della classe Program per specificare i nomi delle risorse create in precedenza, il nome della distribuzione e l'identificatore della sottoscrizione:
   
        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";
   
    Sostituire il valore di groupName con il nome del gruppo di risorse. Sostituire il valore di deploymentName con il nome che si vuole usare per la distribuzione. Per trovare l'ID della sottoscrizione, è possibile eseguire il cmdlet Get-AzureRmSubscription.
2. Aggiungere questo metodo alla classe Program per distribuire le risorse nel gruppo di risorse usando il modello definito:
   
        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential) 
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }
   
    Se si vuole distribuire il modello da un account di archiviazione, è possibile sostituire la proprietà Template con la proprietà TemplateLink.
3. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:
   
        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5:-delete-the-resources"></a>Passaggio 5: Eliminare le risorse
Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Non è necessario eliminare separatamente ogni risorsa da un gruppo di risorse. Eliminando il gruppo di risorse, tutte le relative risorse verranno eliminate automaticamente.

1. Per eliminare il gruppo di risorse, aggiungere questo metodo alla classe Program:
   
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
2. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:
   
       DeleteResourceGroupAsync(
         credential,
         groupName,
         subscriptionId);
       Console.ReadLine();

## <a name="step-6:-run-the-console-application"></a>Passaggio 6: Eseguire l'applicazione console
1. Per eseguire l'applicazione console, fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando le stesse credenziali specificate per la sottoscrizione.
2. Premere **INVIO** dopo la visualizzazione dello stato Accettato.
   
   L'esecuzione completa dell'applicazione console dall'inizio alla fine richiederà circa cinque minuti. Prima di premere Invio per avviare l'eliminazione delle risorse, è consigliabile dedicare alcuni minuti alla verifica della creazione delle risorse nel portale di Azure prima dell'eliminazione.
3. Per visualizzare lo stato delle risorse, passare a Log di controllo nel portale di Azure:
   
    ![Sfogliare i log di controllo nel portale di Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>Passaggi successivi
* Se si sono verificati problemi con la distribuzione, è consigliabile vedere come [risolvere i problemi della distribuzione di gruppi di risorse con il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md).
* Per informazioni su come gestire la macchina virtuale creata, vedere [Gestire macchine virtuali con Azure Resource Manager e PowerShell](virtual-machines-windows-csharp-manage.md).

<!--HONumber=Oct16_HO2-->


