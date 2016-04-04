<properties
	pageTitle="Distribuire le risorse di Azure mediante un modello | Microsoft Azure"
	description="Informazioni su come usare alcuni dei client disponibili nella libreria di Gestione risorse di Azure per distribuire una macchina virtuale, una rete virtuale e un account di archiviazione."
	services="virtual-machines-windows,virtual-networks,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="davidmu"/>

# Distribuire le risorse di Azure usando le librerie .NET e un modello

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.


L'uso di gruppi di risorse e modelli permette di gestire insieme tutte le risorse che supportano l'applicazione. Questa esercitazione illustra come usare alcuni dei client disponibili nella libreria di Gestione risorse di Azure e come creare un modello per distribuire una macchina virtuale, una rete virtuale e un account di archiviazione.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Per completare questa esercitazione sono inoltre necessari:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Account di archiviazione di Azure](../storage/storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

>[AZURE.NOTE] L'account di archiviazione creato a questo punto viene usato per archiviare il modello. Un altro account di archiviazione viene creato quando si distribuisce il modello usato per archiviare il disco per la macchina virtuale. Creare un contenitore nell'account di archiviazione denominato templates.

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

L'esecuzione di questi passaggi richiede circa 30 minuti.

## Passaggio 1: Aggiungere un'applicazione ad Azure AD e impostare le autorizzazioni

Per usare Azure AD per autenticare le richieste a Gestione risorse di Azure, è necessario aggiungere un'applicazione alla directory predefinita. Per aggiungere un'applicazione, eseguire le operazioni seguenti:

1. Aprire un prompt dei comandi di Azure PowerShell, e poi eseguire questo comando e, quando richiesto, immettere le credenziali per la sottoscrizione:

			Login-AzureRmAccount

2. Sostituire {password} nel comando seguente con quella che si desidera usare, quindi eseguire il comando per creare l'applicazione:

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE] Annotare l'identificatore dell’applicazione che viene restituito dopo che l'applicazione viene creata perché sarà necessario per il passaggio successivo. L'identificatore dell'applicazione è disponibile anche nel campo ID client dell'applicazione nella sezione Active Directory del portale di Azure.

3. Sostituire {application-id} con l'identificatore appena registrato, quindi creare l'entità servizio per l'applicazione:

			New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. Impostare le autorizzazioni per l'uso dell'applicazione:

			New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Passaggio 2: Creare il progetto di Visual Studio, il file di modello e i file dei parametri

### Creare il file di modello

Un modello di Gestione risorse di Azure permette di distribuire e gestire insieme le risorse di Azure tramite una descrizione JSON delle risorse e dei relativi parametri di distribuzione. Il modello creato in questa esercitazione è simile a un modello disponibile nella raccolta modelli. Per altre informazioni, vedere l'articolo relativo alla [distribuzione di una semplice VM di Windows negli Stati Uniti occidentali](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

In Visual Studio eseguire le operazioni seguenti:

1. Fare clic su **File** > **Nuovo** > **Progetto**.

2. In **Modelli** > **Visual C#** selezionare **Applicazione console** immettere il nome e il percorso del progetto quindi fare clic su **OK**.

3. In Esplora soluzioni fare clic con il pulsante destro del mouse, quindi scegliere **Aggiungere** > **Nuovo elemento**.

4. Nella finestra Aggiungi nuovo elemento selezionare **File di testo**, immettere *VirtualMachineTemplate.json* come nome e quindi fare clic su **Aggiungi**.

5. Aprire il file VirtualMachineTemplate.json e quindi aggiungere le parentesi di apertura e di chiusura, l'elemento schema obbligatorio e l'elemento contentVersion obbligatorio:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
	}
	```

6. I [parametri](../resource-group-authoring-templates.md#parameters) non sono sempre obbligatori, ma semplificano la gestione dei modelli. Illustrano il tipo del valore, il valore predefinito, se necessario, ed eventualmente i valori consentiti del parametro. Per questa esercitazione vengono aggiunti al modello i parametri usati per creare una macchina virtuale, un account di archiviazione e una rete virtuale.

    Aggiungere l'elemento parameters e i rispettivi elementi figlio dopo l'elemento contentVersion:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUserName": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter" ]
			}
		},
 	}
	```

7. Le [variabili](../resource-group-authoring-templates.md#variables) possono essere usate in un modello per specificare i valori che possono subire modifiche frequenti o che devono essere creati da una combinazione di valori dei parametri.

    Aggiungere l'elemento variables dopo la sezione parameters:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": ["2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
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
	```

8. Le [risorse](../resource-group-authoring-templates.md#resources), ad esempio la macchina virtuale, la rete virtuale e l'account di archiviazione, vengono definite più avanti nel modello.

    Aggiungere la sezione resources dopo la sezione variables:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
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
		"resources": [
			{
  			"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Storage/storageAccounts",
				"name": "[parameters('newStorageAccountName')]",
				"location": "[variables('location')]",
				"properties": {
					"accountType": "[variables('storageAccountType')]"
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
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
				"apiVersion": "2015-05-01-preview",
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
				"apiVersion": "2015-05-01-preview",
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
				"apiVersion": "2015-06-15",
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
							"sku": "[parameters('windowsOSVersion')]",
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
		```

9. Salvare il file di modello creato.

### Creare il file dei parametri

Per specificare i valori per i parametri delle risorse definiti nel modello, creare un file dei parametri che include i valori e inviarlo a Gestione risorse insieme al modello. In Visual Studio eseguire le operazioni seguenti:

1. In Esplora soluzioni fare clic con il pulsante destro del mouse, quindi scegliere **Aggiungi** e quindi **Nuovo elemento**.

2. Nella finestra Aggiungi nuovo elemento selezionare **File di testo**, immettere *Parameters.json* come nome e quindi fare clic su **Aggiungi**.

3. Aprire il file parameters.json e quindi aggiungere il contenuto JSON seguente:

	```
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
	```

    >[AZURE.NOTE] Questa esercitazione crea una macchina virtuale con una versione del sistema operativo Windows Server in esecuzione. Per altre informazioni sulla selezione di altre immagini, vedere [Esplorare e selezionare immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-ps-findimage.md).


4. Salvare il file dei parametri creato.

### Caricare i file

Gestione risorse di Azure accede al file dei modelli e al file dei parametri da un account di archiviazione di Azure. Per inserire i file nella prima archiviazione creata, seguire questa procedura:

1. Aprire Esplora server e passare al contenitore dell'account di archiviazione in cui si vuole inserire il file. Per questa esercitazione, il contenitore che include il modello è denominato templates.

2. Nell'angolo superiore destro del riquadro del contenitore templates, fare clic sull'icona Carica BLOB, passare al file VirtualMachineTemplate.json creato e quindi fare clic su **Apri**.

3. Fare di nuovo clic sull'icona Carica BLOB, passare al file Parameters.json creato, quindi fare clic su **Apri**.

## Passaggio 3: Installare le librerie

I pacchetti NuGet sono il modo più semplice per installare le librerie necessarie per completare questa esercitazione. È necessario installare la libreria di Gestione risorse di Azure e Azure Active Directory Authentication Library. Per ottenere queste librerie in Visual Studio, eseguire le operazioni seguenti:

1. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e quindi scegliere **Gestisci pacchetti NuGet**.

2. Digitare *Active Directory* nella casella di ricerca, fare clic su **Installa** per il pacchetto di Active Directory Authentication Library, quindi seguire le istruzioni per l'installazione del pacchetto.

3. Nella parte superiore della pagina selezionare **Includi versione preliminare**. Digitare *Microsoft.Azure.Management.Resources* nella casella di ricerca, fare clic su **Installa** per le librerie di gestione risorse di Microsoft Azure e quindi seguire le istruzioni per l'installazione del pacchetto.

È ora possibile iniziare a usare le librerie per creare l'applicazione.

##Passaggio 4: Creare le credenziali usate per autenticare le richieste

Dopo la creazione dell'applicazione Azure Active Directory e l'installazione della libreria di autenticazione, formattare le informazioni sull'applicazione nelle credenziali usate per autenticare le richieste in Gestione risorse di Azure. Eseguire le operazioni seguenti:

1. Aprire il file Program.cs per il progetto creato, quindi aggiungere le seguenti istruzioni using all'inizio del file:

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Rest;
	```

2.	Aggiungere il metodo seguente alla classe Program per ottenere il token necessario per creare le credenziali:

	```
	private static string GetAuthorizationHeader()
	{
		ClientCredential cc = new ClientCredential("{application-id}", "{password}");
		var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
		var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
		if (result == null)
		{
			throw new InvalidOperationException("Failed to obtain the JWT token");
		}

		string token = result.AccessToken;

		return token;
	}
	```

	Sostituire {application-id} con l'identificatore dell'applicazione registrato in precedenza, {password} con la password scelta per l'applicazione AD e {tenant-id} con l'identificatore del tenant per la sottoscrizione. Per trovare l'ID del tenant, eseguire Get-AzureSubscription.

3. Aggiungere il codice seguente al metodo Main nel file Program.cs per creare le credenziali:

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

4. Salvare il file Program.cs.


## Passaggio 5: Aggiungere il codice per distribuire il modello

Le risorse vengono sempre distribuite da un modello al gruppo di risorse. È possibile usare le classi [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) e [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) per creare il gruppo di risorse in cui vengono distribuite le risorse.

1. Aggiungere il metodo seguente alla classe Program per creare il gruppo di risorse:

	```
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
		Console.WriteLine(rgResult.StatusCode.Properties.ProvisioningState);
	}
	```

2. Aggiungere il codice seguente al metodo Main per chiamare il metodo appena aggiunto:

	```
	CreateResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}",
		"{location}");
		Console.ReadLine();
	```

	Sostituire {group-name} con il nome che si vuole usare per il gruppo di risorse. Sostituire {subscription-id} con l'identificatore della sottoscrizione che è possibile ottenere eseguendo Get-AzureSubscription. Sostituire la posizione con l'area in cui creare le risorse, ad esempio "Stati Uniti occidentali".

3. Aggiungere il metodo seguente alla classe Program per distribuire le risorse nel gruppo di risorse usando il modello definito:

	```
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
				Uri = "https://{storage-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json"
			},
			ParametersLink = new ParametersLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/Parameters.json"
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
	```

	Sostituire {storage-name} con il nome dell'account in cui sono stati inseriti in precedenza i file.

4. Aggiungere il codice seguente al metodo Main per chiamare il metodo appena aggiunto:

	```
	CreateTemplateDeployment(
		credential,
		"{group-name}",
		"{storage-name}",
		"{deployment-name}",
		"{subscription-id}");
	Console.ReadLine();
	```

    Sostituire {group-name} con il nome del gruppo di risorse creato. Sostituire {storage-name} con il nome dell'account di archiviazione in cui sono stati inseriti i file del modello. Sostituire {deployment-name} con il nome che si vuole usare per identificare il set di distribuzione delle risorse. Sostituire {subscription-id} con l'identificatore della sottoscrizione che è possibile ottenere eseguendo Get-AzureSubscription.

##Passaggio 6: Aggiungere codice per eliminare le risorse

Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Non è necessario eliminare separatamente ogni risorsa da un gruppo di risorse. È possibile eliminare il gruppo di risorse, in modo che vengano eliminate automaticamente tutte le risorse di tale gruppo.

1.	Aggiungere il metodo seguente alla classe Program per eliminare il gruppo di risorse:

	```
	public static void DeleteResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId)
	{
		Console.WriteLine("Deleting resource group...");
		var resourceGroupClient = new ResourceManagementClient(credential);
		resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
	}
	```

2.	Aggiungere il codice seguente al metodo Main per chiamare il metodo appena aggiunto:

	```
	DeleteResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}");
	Console.ReadLine();
	```
    Sostituire {group-name} con il nome del gruppo di risorse creato. Sostituire {subscription-id} con l'identificatore della sottoscrizione che è possibile ottenere eseguendo Get-AzureSubscription.

##Passaggio 7: Eseguire l'applicazione console

1.	Per eseguire l'applicazione console, fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la stessa password specificati per la sottoscrizione.

2.	Premere **INVIO** dopo che viene visualizzato lo stato Accettato.

	L'esecuzione completa dell'applicazione console dall'inizio alla fine dovrebbe richiedere circa 5 minuti. Prima di premere Invio per avviare l'eliminazione delle risorse, è consigliabile dedicare alcuni minuti alla verifica della creazione delle risorse nel portale di Azure prima dell'eliminazione.

3. Passare a Log di controllo nel portale di Azure per visualizzare lo stato delle risorse:

	![Creare un'applicazione di Active Directory](./media/virtual-machines-windows-csharp-template/crpportal.png)

<!---HONumber=AcomDC_0323_2016-->