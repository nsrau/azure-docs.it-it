<properties 
	pageTitle="Distribuire le risorse di Azure mediante un modello" 
	description="Informazioni su come usare alcuni dei client disponibili nella libreria di Gestione risorse di Azure per distribuire una macchina virtuale, una rete virtuale e un account di archiviazione." 
	services="virtual-machines,virtual-networks,storage" 
	documentationCenter="" 
	authors="davidmu1" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="davidmu"/>

# Distribuire le risorse di Azure usando le librerie .NET e un modello

L'uso di gruppi di risorse e modelli permette di gestire insieme tutte le risorse che supportano l'applicazione. Questa esercitazione illustra come usare alcuni dei client disponibili nella libreria di Gestione risorse di Azure e come creare un modello per distribuire una macchina virtuale, una rete virtuale e un account di archiviazione.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Per completare questa esercitazione sono inoltre necessari:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Account di archiviazione di Azure](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/it-it/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/it-it/download/details.aspx?id=40855)
- [Azure PowerShell](../powershell-install-configure.md)

L'esecuzione di questi passaggi richiede circa 30 minuti.

## Passaggio 1: Aggiungere un'applicazione ad Azure AD e impostare le autorizzazioni

Per usare Azure AD per autenticare le richieste a Gestione risorse di Azure, è necessario aggiungere un'applicazione alla directory predefinita. Per aggiungere un'applicazione, eseguire le operazioni seguenti:

1. Aprire un prompt dei comandi di Azure PowerShell e quindi eseguire il comando seguente:

        Switch-AzureMode –Name AzureResourceManager

2. Impostare l'account di Azure da usare per questa esercitazione. Eseguire questo comando e, quando richiesto, immettere le credenziali per la sottoscrizione:

	    Add-AzureAccount

3. Sostituire {password} nel comando seguente con il valore da usare e quindi eseguire il comando per creare l'applicazione:

	    New-AzureADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

4. Registrare il valore ApplicationId nella risposta dal passaggio precedente. Sarà necessario usarlo più avanti nell'esercitazione.

	![Creare un'applicazione di Active Directory](./media/arm-template-deployment/azureapplicationid.png)

	>[AZURE.NOTE]L'identificatore dell'applicazione è disponibile anche nel campo ID client dell'applicazione nel portale di gestione.

5. Sostituire {application-id} con l'identificatore appena registrato, quindi creare l'entità servizio per l'applicazione:

        New-AzureADServicePrincipal -ApplicationId {application-id} 

6. Impostare le autorizzazioni per l'uso dell'applicazione:

	    New-AzureRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Passaggio 2: Creare il progetto di Visual Studio, il file di modello e i file dei parametri

###Creare il file di modello

Un modello di Gestione risorse di Azure permette di distribuire e gestire insieme le risorse di Azure tramite una descrizione JSON delle risorse e dei relativi parametri di distribuzione. In Visual Studio eseguire le operazioni seguenti:

1. Fare clic su **File** \> **Nuovo** \> **Progetto**.

2. In **Modelli** \> **Visual C\#** selezionare **Applicazione console** immettere il nome e il percorso del progetto quindi fare clic su **OK**.

3. In Esplora soluzioni fare clic con il pulsante destro del mouse, quindi scegliere **Aggiungere** \> **Nuovo elemento**.

4.	Nella finestra Aggiungi nuovo elemento selezionare **File di testo**, immettere *VirtualMachineTemplate.json* come nome e quindi fare clic su **Aggiungi**.

5.	Aprire il file VirtualMachineTemplate.json e quindi aggiungere le parentesi di apertura e di chiusura, l'elemento schema obbligatorio e l'elemento contentVersion obbligatorio:

        {
            "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
            "contentVersion": "1.0.0.0",
        }

6. I [parametri](https://msdn.microsoft.com/library/azure/dn835138.aspx#parameters) non sono sempre obbligatori, ma semplificano la gestione dei modelli. Illustrano il tipo del valore, il valore predefinito, se necessario, ed eventualmente i valori consentiti del parametro. Per questa esercitazione vengono aggiunti al modello i parametri usati per creare una macchina virtuale, un account di archiviazione e una rete virtuale.

    Aggiungere l'elemento parameters e i rispettivi elementi figlio dopo l'elemento contentVersion:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
        }

7.	Le [variabili](https://msdn.microsoft.com/library/azure/dn835138.aspx#variables) possono essere usate in un modello per specificare i valori che possono subire modifiche frequenti o che devono essere creati da una combinazione di valori dei parametri.

    Aggiungere l'elemento variables dopo la sezione parameters:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
        }

8.	Le [risorse](https://msdn.microsoft.com/library/azure/dn835138.aspx#resources), ad esempio la macchina virtuale, la rete virtuale e l'account di archiviazione, vengono definite più avanti nel modello.

    Aggiungere la sezione resources dopo la sezione variables:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
          "resources": [ {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('newStorageAccountName')]",       
            "location": "[parameters('location')]",
            "properties": { "accountType": "[parameters('storageAccountType')]" }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": { 
              "publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
              "dnsSettings": { "domainNameLabel": "[parameters('dnsName')]" }
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
              "addressSpace": { "addressPrefixes": [ "[parameters('addressPrefix')]" ] },
              "subnets": [ {
                "name": "[parameters('subnet1Name')]",
                "properties": { "addressPrefix": "[parameters('subnet1Prefix')]" }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties": { "addressPrefix": "[parameters('subnet2Prefix')]" }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
              "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
              "ipConfigurations": [ {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddressName'))]"
                  },
                  "subnet": { "id": "[variables('subnet1Ref')]" }
                }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
              "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
              "hardwareProfile": { "vmSize": "[parameters('vmSize')]" },
              "osProfile": {
                "computername": "[parameters('vmName')]",
                "adminUsername": "[parameters('adminUsername')]",
                "adminPassword": "[parameters('adminPassword')]",
                "windowsProfile": { "provisionVMAgent": "true" }
              },
              "storageProfile": { 
                "sourceImage": { "id": "[variables('sourceImageName')]" },
                "destinationVhdsContainer" : "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmStorageAccountContainerName'),'/')]"
              },
              "networkProfile": {
                "networkInterfaces" : [ {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                } ]
              }
            }
          } ]
        }

9.	Salvare il file di modello creato.

###Creare il file dei parametri

Per specificare i valori per i parametri delle risorse definiti nel modello, creare un file dei parametri che include i valori e inviarlo a Gestione risorse insieme al modello. In Visual Studio eseguire le operazioni seguenti:

1.	In Esplora soluzioni fare clic con il pulsante destro del mouse, quindi scegliere **Aggiungi** e quindi **Nuovo elemento**.

2.	Nella finestra Aggiungi nuovo elemento selezionare **File di testo**, immettere *Parameters.json* come nome e quindi fare clic su **Aggiungi**.

3.	Aprire il file parameters.json e quindi aggiungere il contenuto JSON seguente:

        {
          "contentVersion": "1.0.0.0",
          "parameters": { 
            "vmName": { "value": "mytestvm1" },
            "newStorageAccountName": { "value": "mytestsa1" },
            "storageAccountType": { "value": "Standard_LRS" },
            "publicIPaddressName": { "value": "mytestip1" },
            "location": { "value": "West US" },
            "vmStorageAccountContainerName": { "value": "vhds" },
            "vmSize": { "value": "Standard_A1" },
            "subscriptionId": { "value": "{subscription-id}" },
            "vmSourceImageName": { "value": "{source-image-name}" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "virtualNetworkName": { "value": "mytestvn1" },
            "dnsName": { "value": "mytestdns1" }, 
            "nicName": { "value": "mytestnic1" } 
          }
        }

    >[AZURE.NOTE]I nomi dei file VHD dell'immagine cambiano regolarmente nella raccolta immagini. È quindi necessario ottenere un nome di immagine corrente per distribuire la macchina virtuale. Per eseguire questa operazione, vedere [Gestire le immagini mediante Windows PowerShell](https://msdn.microsoft.com/library/azure/dn790330.aspx), quindi sostituire {source-image-name} con il nome del file VHD da usare. Ad esempio, "a699494373c04fc0bc8f2bb1389d6106\_\_Windows-Server-2012-R2-201412.01-en.us-127GB.vhd". Sostituire {subscription-id} con l'identificatore della sottoscrizione.


4.	Salvare il file dei parametri creato.

Gestione risorse di Azure accede al file dei modelli e al file dei parametri da un account di archiviazione di Azure. Per inserire i file nell'archivio, eseguire le operazioni seguenti:

1.	Aprire Esplora server e passare al contenitore dell'account di archiviazione in cui si vuole inserire il file. Per questa esercitazione, il contenitore che include il modello è denominato templates.

2.	Nell'angolo superiore destro del riquadro del contenitore templates, fare clic sull'icona Carica BLOB, passare al file VirtualMachineTemplate.json creato e quindi fare clic su **Apri**.

3. Fare di nuovo clic sull'icona Carica BLOB, passare al file Parameters.json creato, quindi fare clic su **Apri**.

##Passaggio 3: Installare le librerie

I pacchetti NuGet sono il modo più semplice per installare le librerie necessarie per completare questa esercitazione. È necessario installare la libreria di Gestione risorse di Azure e Azure Active Directory Authentication Library. Per ottenere queste librerie in Visual Studio, eseguire le operazioni seguenti:

1.	Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e quindi scegliere **Gestisci pacchetti NuGet**.

2.	Digitare *Active Directory* nella casella di ricerca, fare clic su **Installa** per il pacchetto di Active Directory Authentication Library, quindi seguire le istruzioni per l'installazione del pacchetto.

3.	Nella parte superiore della pagina selezionare **Includi versione preliminare**. Digitare *Gestione risorse di Azure* nella casella di ricerca, fare clic su **Installa** per le librerie di Gestione risorse di Microsoft Azure e quindi seguire le istruzioni per l'installazione del pacchetto.

È ora possibile iniziare a usare le librerie per creare l'applicazione.

##Passaggio 4: Creare le credenziali usate per autenticare le richieste

Dopo la creazione dell'applicazione Azure Active Directory e l'installazione della libreria di autenticazione, formattare le informazioni sull'applicazione nelle credenziali usate per autenticare le richieste in Gestione risorse di Azure. Eseguire le operazioni seguenti:

1.	Aprire il file Program.cs per il progetto creato, quindi aggiungere le seguenti istruzioni using all'inizio del file:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;

2.	Aggiungere il metodo seguente alla classe Program per ottenere il token necessario per creare le credenziali:

        private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

	Sostituire {application-id} con l'identificatore dell'applicazione registrato in precedenza, {password} con la password scelta per l'applicazione AD e {tenant-id} con l'identificatore del tenant per la sottoscrizione. Per trovare l'ID del tenant, eseguire Get-AzureSubscription.

3.	Aggiungere il codice seguente al metodo Main nel file Program.cs per creare le credenziali:

		var token = GetAuthorizationHeader();
		var credential = new TokenCloudCredentials("{subscription-id}", token);

4.	Salvare il file Program.cs.


##Passaggio 5: Aggiungere il codice per distribuire il modello

Le risorse vengono sempre distribuite da un modello al gruppo di risorse. È possibile usare le classi [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) e [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) per creare il gruppo di risorse in cui vengono distribuite le risorse.

1.	Aggiungere il metodo seguente alla classe Program per creare il gruppo di risorse:

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");
		  var resourceGroup = new ResourceGroup { Location = "West US" };
		  using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", resourceGroup);
		    Console.WriteLine(rgResult.StatusCode);
		  }
		}

2.	Aggiungere il codice seguente al metodo Main per chiamare il metodo appena aggiunto:

		CreateResourceGroup(credential);
		Console.ReadLine();

3.	Aggiungere il metodo seguente alla classe Program per distribuire le risorse nel gruppo di risorse usando il modello definito:

		public async static void CreateTemplateDeployment(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the template deployment...");
		  var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
		  {
		    Mode = DeploymentMode.Incremental,
		    TemplateLink = new TemplateLink
		    {
		      Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json")
			},
			ParametersLink = new ParametersLink
			{
			  Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/Parameters.json")
			}
		  };
		  using (var templateDeploymentClient = new ResourceManagementClient(credential))
		  {
		    var dpResult = await templateDeploymentClient.Deployments.CreateOrUpdateAsync("mytestrg1", "mytestdp1", deployment);
			Console.WriteLine(dpResult.StatusCode);
		  }
		}

	Sostituire {storage-account-name} con il nome dell'account in cui sono stati inseriti in precedenza i file.

4.	Aggiungere il codice seguente al metodo Main per chiamare il metodo appena aggiunto:

		CreateTemplateDeployment(credential);
		Console.ReadLine();

##Passaggio 6: Aggiungere codice per eliminare le risorse

Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Non è necessario eliminare separatamente ogni risorsa da un gruppo di risorse. È possibile eliminare il gruppo di risorse, in modo che vengano eliminate automaticamente tutte le risorse di tale gruppo.

1.	Aggiungere il metodo seguente alla classe Program per eliminare il gruppo di risorse:

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
		{
		  using (var resourceGroupClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
			Console.WriteLine(rgResult.StatusCode);
		  }
		}

2.	Aggiungere il codice seguente al metodo Main per chiamare il metodo appena aggiunto:

		DeleteResourceGroup(credential);
		Console.ReadLine();

##Passaggio 7: Eseguire l'applicazione console

1.	Per eseguire l'applicazione console, fare clic su **Avvia** in Visual Studio e quindi accedere ad Azure AD usando lo stesso nome utente e la stessa password specificati per la sottoscrizione.

2.	Premere **INVIO** dopo la restituzione di tutti i codici di stato per creare ciascuna risorsa. Dopo la creazione della macchina virtuale, eseguire il passaggio seguente prima di premere INVIO per eliminare tutte le risorse.

	L'esecuzione completa dell'applicazione console dall'inizio alla fine dovrebbe richiedere circa 5 minuti. Prima di premere INVIO per avviare l'eliminazione delle risorse, è consigliabile dedicare alcuni minuti alla verifica della creazione delle risorse nel portale di anteprima di Azure prima dell'eliminazione.

3. Passare a Log di controllo nel portale di anteprima di Azure per visualizzare lo stato delle risorse:

	![Creare un'applicazione di Active Directory](./media/arm-template-deployment/crpportal.png)

<!---HONumber=58_postMigration-->