<properties
	pageTitle="Distribuire una VM con C# e un modello di Azure Resource Manager | Microsoft Azure"
	description="Informazioni su come usare C# e un modello di Resource Manager per distribuire una macchina virtuale di Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# Distribuire una macchina virtuale di Azure con C# e un modello di Azure Resource Manager

L'uso di gruppi di risorse e modelli permette di gestire insieme tutte le risorse che supportano l'applicazione. Questo articolo illustra come configurare l'autenticazione e l'archiviazione con Azure PowerShell e quindi compilare e distribuire un modello con C# per creare risorse di Azure.

Per completare questa esercitazione, sono necessari:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- [Un token di autenticazione](../resource-group-authenticate-service-principal.md)

L'esecuzione di questi passaggi richiede circa 30 minuti.

## Passaggio 1: installare Azure PowerShell

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione che si vuole usare e accedere all'account Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
    
## Passaggio 2: Creare un gruppo di risorse per l'archiviazione del modello

Tutte le risorse devono essere distribuite in un gruppo di risorse. Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../resource-group-overview.md).

1. Ottenere un elenco di località disponibili in cui possono essere create le risorse.

	    Get-AzureLocation | sort Name | Select Name

2. Sostituire il valore di **$locName** con una località dell'elenco, ad esempio **Stati Uniti centrali**. Creare la variabile.

        $locName = "location name"
        
3. Sostituire il valore di **$rgName** con il nome del nuovo gruppo di risorse. Creare la variabile e il gruppo di risorse.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Verrà visualizzata una schermata analoga alla seguente:
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1
    
## Passaggio 3: Creare un account di archiviazione e il contenitore di modelli

È necessario un account di archiviazione per archiviare il modello da creare e distribuire.

1. Sostituire il valore di $stName, solo lettere minuscole e numeri, con il nome dell'account di archiviazione. Testare l'univocità del nome.

        $stName = "storage account name"
        Test-AzureName -Storage $stName

    Se questo comando restituisce **False**, il nome proposto è univoco.
    
2. Ora eseguire il comando per creare l'account di archiviazione.
    
        New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
3. Sostituire {blob-storage-endpoint} con l'endpoint dell'archivio BLOB nell'account. Sostituire {storage-account-name} con il nome del proprio account di archiviazione. Sostituire {primary-storage-key} con la chiave di accesso primaria. Eseguire questi comandi per creare il contenitore in cui sono archiviati i file. È possibile ottenere i valori della chiave e dell'endpoint dal portale di Azure.

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint={blob-storage-endpoint};AccountName={storage-account-name};AccountKey={primary-storage-key}"
        $ctx = New-AzureStorageContext -ConnnectionString $ConnectionString
        New-AzureStorageContainer -Name "templates" -Permission Blob -Context $ctx

## Passaggio 3: Creare il progetto di Visual Studio, il file modello e i file di parametri

### Creare il file di modello

Un modello di Gestione risorse di Azure permette di distribuire e gestire insieme le risorse di Azure tramite una descrizione JSON delle risorse e dei relativi parametri di distribuzione. Il modello creato in questa esercitazione è simile a un modello disponibile nella raccolta modelli. Per altre informazioni, vedere l'articolo relativo alla [distribuzione di una semplice VM di Windows negli Stati Uniti occidentali](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows/).

In Visual Studio eseguire queste operazioni:

1. Fare clic su **File** > **Nuovo** > **Progetto**.

2. In **Modelli** > **Visual C#** selezionare **Applicazione console** immettere il nome e il percorso del progetto quindi fare clic su **OK**.

3. In Esplora soluzioni fare clic con il pulsante destro del mouse, quindi scegliere **Aggiungere** > **Nuovo elemento**.

4. Fare clic su Web, selezionare il file JSON, immettere *VirtualMachineTemplate.json* come nome e quindi fare clic su **Aggiungi**.

5. Nelle parentesi di apertura e chiusura del file VirtualMachineTemplate.json aggiungere l'elemento dello schema e l'elemento contentVersion obbligatori:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. I [parametri](../resource-group-authoring-templates.md#parameters) non sono sempre obbligatori, ma semplificano la gestione dei modelli. Illustrano il tipo del valore, il valore predefinito, se necessario, ed eventualmente i valori consentiti del parametro. Per questa esercitazione vengono aggiunti al modello i parametri usati per creare una macchina virtuale, un account di archiviazione e una rete virtuale. Aggiungere l'elemento parameters e i rispettivi elementi figlio dopo l'elemento contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
        }

7. Le [variabili](../resource-group-authoring-templates.md#variables) possono essere usate in un modello per specificare i valori che possono subire modifiche frequenti o che devono essere creati da una combinazione di valori dei parametri. Aggiungere l'elemento variables dopo la sezione parameters:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" },
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
            "OSDiskName": "osdiskforwindowssimple",
            "nicName": "myVMnic1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyWindowsVM",
            "vmSize": "Standard_A2",
            "virtualNetworkName": "MyVNET",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"  
          },
        }

8. Le [risorse](../resource-group-authoring-templates.md#resources), ad esempio la macchina virtuale, la rete virtuale e l'account di archiviazione, vengono definite più avanti nel modello. Aggiungere la sezione resources dopo la sezione variables:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "OSDiskName": "osdiskforwindowssimple",
            "windowsOSVersion": "2012-R2-Datacenter",
            "nicName": "myVMnic1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyWindowsVM",
            "vmSize": "Standard_A2",
            "virtualNetworkName": "MyVNET",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "apiVersion": "2015-06-15",
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "location": "[variables('location')]",
              "properties": {
                "accountType": "[variables('storageAccountType')]"
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "[variables('publicIPAddressName')]",
              "location": "[variables('location')]",
              "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                  "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "[variables('virtualNetworkName')]",
              "location": "[variables('location')]",
              "properties": {
                "addressSpace": {
                  "addressPrefixes": [ "[variables('addressPrefix')]" ]
                },
                "subnets": [ {
                  "name": "[variables('subnetName')]",
                  "properties": {
                    "addressPrefix": "[variables('subnetPrefix')]"
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "[variables('nicName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
                    },
                    "subnet": {
                      "id": "[variables('subnetRef')]"
                    }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "[variables('vmName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
              ],
              "properties": {
                "hardwareProfile": {
                  "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                  "computerName": "[variables('vmName')]",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]",
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "[variables('imagePublisher')]",
                    "offer": "[variables('imageOffer')]",
                    "sku": "[variables('windowsOSVersion')]",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "osdisk",
                    "vhd": {
                      "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                  } ]
                }
              }
            } ]
          }
      
9. Salvare il file di modello creato.

### Creare il file dei parametri

Per specificare i valori per i parametri delle risorse definiti nel modello, creare un file dei parametri che include i valori e inviarlo a Gestione risorse insieme al modello. In Visual Studio eseguire queste operazioni:

1. In Esplora soluzioni fare clic con il pulsante destro del mouse, quindi scegliere **Aggiungi** e quindi **Nuovo elemento**.

2. Fare clic su Web, selezionare il file JSON, immettere *Parameters.json* come nome e quindi fare clic su **Aggiungi**.

3. Aprire il file parameters.json e quindi aggiungere il contenuto JSON seguente:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "value": "mytestsa1" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "dnsNameForPublicIP": { "value": "mytestdns1" }
          }
        }

    >[AZURE.NOTE] Questo articolo crea una macchina virtuale che esegue una versione del sistema operativo Windows Server. Per altre informazioni sulla selezione di altre immagini, vedere [Esplorare e selezionare immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-ps-findimage.md).

4. Salvare il file dei parametri creato.

### Caricare i file e impostare le autorizzazioni per usarli 

Gestione risorse di Azure accede al file dei modelli e al file dei parametri da un account di archiviazione di Azure. Per inserire i file nella prima risorsa di archiviazione creata, seguire questa procedura:

1. Aprire Esplora Cloud e quindi passare al contenitore di modelli nell'account di archiviazione creato in precedenza.

2. Nella finestra del contenitore di modelli far clic sull'icona Carica BLOB nell'angolo in alto a destra della finestra, passare al file VirtualMachineTemplate.json creato e quindi fare clic su **Apri**.

3. Fare di nuovo clic sull'icona Carica BLOB, passare al file Parameters.json creato, quindi fare clic su **Apri**.

## Passaggio 4: Installare le librerie

I pacchetti NuGet sono il modo più semplice per installare le librerie necessarie per completare questa esercitazione. È necessario installare la libreria di Gestione risorse di Azure e Azure Active Directory Authentication Library. Per ottenere queste librerie in Visual Studio, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**.

2. Digitare *Active Directory* nella casella di ricerca, fare clic su **Installa** per il pacchetto di Active Directory Authentication Library, quindi seguire le istruzioni per l'installazione del pacchetto.

4. Nella parte superiore della pagina selezionare **Includi versione preliminare**. Digitare *Microsoft.Azure.ResourceManager* nella casella di ricerca, fare clic su **Installa** per le librerie di Microsoft Azure Resource Manager e quindi seguire le istruzioni per l'installazione del pacchetto.

È ora possibile iniziare a usare le librerie per creare l'applicazione.

##Passaggio 5: Creare le credenziali da usare per autenticare le richieste

L'applicazione di Azure Active Directory è stata creata e la libreria di autenticazione è stata installata. È ora necessario formattare le informazioni sull'applicazione in credenziali usate per autenticare le richieste in Azure Resource Manager. Procedere come segue:

1. Aprire il file Program.cs per il progetto creato e quindi aggiungere le istruzioni using seguenti all'inizio del file:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;
        using Microsoft.Rest;

2.	Aggiungere questo metodo alla classe Program per ottenere il token necessario per creare le credenziali:

        private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.Result.AccessToken;

          return token;
        }

    Sostituire {application-id} con l'identificatore dell'applicazione registrato in precedenza, {password} con la password scelta per l'applicazione AD e {tenant-id} con l'identificatore del tenant per la sottoscrizione. Per trovare l'ID tenant, eseguire Get-AzureRmSubscription.

3. Aggiungere questo codice al metodo Main nel file Program.cs per creare le credenziali:

        var token = GetAuthorizationHeader();
        var credential = new TokenCredentials(token);

4. Salvare il file Program.cs.

## Passaggio 6: Aggiungere il codice per distribuire il modello

In questo passaggio si useranno le classi [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) e [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) per creare il gruppo di risorse in cui vengono distribuite le risorse.

1. Aggiungere le variabili al metodo Main della classe Program per specificare i nomi da usare per le risorse, la località delle risorse, ad esempio "Stati Uniti centrali", le informazioni dell'account amministratore e l'identificatore della sottoscrizione:

        var groupName = "resource group name";
        var storageName = "storage account name";
        var vmName = "virtual machine name";  
        var deploymentName = "deployment name";
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        var location = "location name";
        var subscriptionId = "subsciption id";

    Sostituire tutti i valori delle variabili con i nomi e l'identificatore che si vuole usare. Per trovare l'ID della sottoscrizione, è possibile eseguire il cmdlet Get-AzureRmSubscription. Il valore della variabile storageName è il nome dell'account di archiviazione in cui è stato archiviato il modello.
    
2. Aggiungere questo metodo alla classe Program per creare il gruppo di risorse:

        public static void CreateResourceGroup(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          Console.WriteLine("Creating the resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential);
          resourceManagementClient.SubscriptionId = subscriptionId;
          var resourceGroup = new ResourceGroup {
            Location = location
          };
          var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
          Console.WriteLine(rgResult.Properties.ProvisioningState);
        }

2. Aggiungere questo codice al metodo Main per chiamare il metodo appena aggiunto:

        CreateResourceGroup(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

3. Aggiungere questo metodo alla classe Program per distribuire le risorse nel gruppo di risorse usando il modello definito:

        public static void CreateTemplateDeployment(
          TokenCredentials credential,
          string groupName,
          string storageName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/VirtualMachineTemplate.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/Parameters.json"
            }
          };
          var resourceManagementClient = new ResourceManagementClient(credential);
          resourceManagementClient.SubscriptionId = subscriptionId;
          var dpResult = resourceManagementClient.Deployments.CreateOrUpdate(
            groupName,
            deploymentName,
            deployment);
          Console.WriteLine(dpResult.Properties.ProvisioningState);
        }

4. Aggiungere questo codice al metodo Main per chiamare il metodo appena aggiunto:

        CreateTemplateDeployment(
          credential,
          groupName",
          storageName,
          deploymentName}",
          subscriptionId}");
        Console.ReadLine();

##Passaggio 7: Aggiungere il codice per eliminare le risorse

Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Non è necessario eliminare separatamente ogni risorsa da un gruppo di risorse. Eliminare il gruppo di risorse. Saranno eliminate automaticamente tutte le relative risorse.

1.	Aggiungere questo metodo alla classe Program per eliminare il gruppo di risorse:

        public static void DeleteResourceGroup(
          TokenCredentials credential,
          string groupName)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceGroupClient = new ResourceManagementClient(credential);
          resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
        }

2.	Aggiungere questo codice al metodo Main per chiamare il metodo appena aggiunto:

        DeleteResourceGroup(
          credential,
          groupName);
        Console.ReadLine();

##Passaggio 8: Eseguire l'applicazione console

1.	Per eseguire l'applicazione console, fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la stessa password specificati per la sottoscrizione.

2.	Premere **INVIO** dopo la visualizzazione dello stato Accettato.

	L'esecuzione completa dell'applicazione console dall'inizio alla fine dovrebbe richiedere circa 5 minuti. Prima di premere Invio per avviare l'eliminazione delle risorse, è consigliabile dedicare alcuni minuti alla verifica della creazione delle risorse nel portale di Azure prima dell'eliminazione.

3. Passare a Log di controllo nel portale di Azure per visualizzare lo stato delle risorse:

	![Sfogliare i log di controllo nel portale di Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## Passaggi successivi

- Se si sono verificati problemi con la distribuzione, è consigliabile leggere [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Per informazioni su come gestire la macchina virtuale appena creata, vedere [Gestire macchine virtuali con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0420_2016-->