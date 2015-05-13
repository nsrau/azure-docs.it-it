<properties 
	pageTitle="Distribuire e gestire le macchine virtuali di Azure utilizzando i modelli di Gestione risorse e l’interfaccia della riga di comando di Azure per Mac, Linux e Windows" 
	description="È possibile distribuire il set più comune di configurazioni di macchine virtuali di Azure e gestirle facilmente mediante modelli di Gestione risorse e l’interfaccia della riga di comando di Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="rasquill"/>

# Distribuire e gestire le macchine virtuali utilizzando modelli di Gestione risorse di Azure e l’interfaccia della riga di comando di Azure

In questo articolo vengono fornite indicazioni su come automatizzare le attività comuni per la distribuzione e la gestione delle macchine virtuali di Azure utilizzando modelli di Gestione risorse e l’interfaccia della riga di comando di Azure, nonché collegamenti a ulteriori informazioni sull'automazione per le macchine virtuali.

## Preparazione

Prima di poter utilizzare l’interfaccia della riga di comando di Azure con i gruppi di risorse di Azure, sarà necessario disporre della versione corretta dell’interfaccia e un ID azienda o istituzione scolastica \(noto anche come ID organizzazione\).

### Passaggio 1: aggiornare la versione di dell’interfaccia della riga di comando di Azure alla 0.9.0

Digitare `azure --version` per verificare se è già stata installata la versione 0.9.0.

	azure --version
    0.9.0 (node: 0.10.25)

Se la versione installata non è la 0.9.0, sarà necessario [installare l’interfaccia della riga di comando di Azure](xplat-cli-install.md) o l'aggiornamento utilizzando uno dei programmi di installazione nativi o tramite **npm** digitando `npm update -g azure-cli`.

### Passaggio 2: impostare l'account Azure e la sottoscrizione

Se non si dispone già una sottoscrizione di Azure, ma si dispone di un abbonamento MSDN, è possibile attivare i [Vantaggi per gli abbonati MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure [iscriversi per una versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

È necessario sia presente un account aziendale o di istituto scolastico per utilizzare i modelli di gestione risorse di Azure. Se disponibile, è possibile digitare `azure login`, immettere il nome utente e la password e dovrebbe essere possibile eseguire correttamente l'accesso.

> [AZURE.NOTE]Se non è disponibile, verrà visualizzato un messaggio di errore che indica che è necessario un diverso tipo di account. Per crearne uno dal proprio account Azure corrente, vedere la documentazione relativa alla [connessione all'account Azure](xplat-cli-connect.md).

L'account può includere più di una sottoscrizione. È possibile elencare le sottoscrizioni digitando `azure account list`, comando che potrebbe essere visualizzato in modo simile al seguente:

    azure account list
    info:    Executing command account list
    data:    Name                              Id                                    Tenandt Id                            Current
    data:    --------------------------------  ------------------------------------  ------------------------------------  -------
    data:    Contoso Admin                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  true   
    data:    Fabrikam dev                      xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    data:    Fabrikam test                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    data:    Contoso production                xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    
È possibile impostare la sottoscrizione Azure corrente digitando

	`azure account set <subscription name or ID> true

con il nome della sottoscrizione o l'ID che dispone di risorse che si desidera gestire.

### Passaggio 3: passare alla modalità gruppo di risorse dell’interfaccia della riga di comando di Azure

Per impostazione predefinita, l’interfaccia della riga di comando viene avviata in modalità di Gestione servizi \(\*\* modalità asm\*\*\). Tipo

	azure config mode arm

per passare alla modalità del gruppo di risorse.

> [AZURE.NOTE]È possibile passare nuovamente al set predefinito di comandi digitando `azure config mode asm`.

## Informazioni sui modelli di risorse di Azure e sui gruppi di risorse

La maggior parte delle applicazioni vengono create da una combinazione di tipi diversi di risorse \(ad esempio, uno o più macchine virtuali e account di archiviazione, un database SQL, una rete virtuale o una rete di distribuzione dei contenuti o *CDN*\). L'API di Gestione servizi di Azure predefinita e il portale Azure classico rappresentavano questi elementi che utilizzavano un approccio individuale, che richiede la distribuzione e la gestione dei singoli servizi in modo sequenziale \(o altri strumenti che consentono di eseguire questa operazione\) e non come una singola unità logica di distribuzione.

*I modelli di Gestione risorse di Azure* consentono di distribuire e gestire queste diverse risorse come una singola unità logica di distribuzione in modo dichiarativo. Anziché in modo imperativo indicando a Azure gli elementi da distribuire un comando dopo l'altro, è possibile descrivere l'intera distribuzione in un file JSON \(tutte le risorse, la configurazione associata e i parametri di distribuzione\) e indicare ad Azure di distribuire le risorse come un singolo gruppo.

È quindi possibile gestire il ciclo di vita complessivo delle risorse del gruppo utilizzando i comandi di Gestione risorse dell’interfaccia della riga di comando di Azure per effettuare quanto segue:

- Arrestare, avviare o eliminare tutte le risorse all'interno del gruppo contemporaneamente. 
- Applicare le regole di controllo degli accessi in base al ruolo \(RBAC\) per bloccare le autorizzazioni di sicurezza su di essi. 
- Controllare le operazioni. 
- Contrassegnare le risorse con metadati aggiuntivi per una gestione più efficiente. 

[Qui](resource-groups-overview.md) è possibile ottenere ulteriori informazioni sui gruppi di risorse di Azure e ciò che è possibile eseguire automaticamente. Se si è interessati alla creazione di modelli, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).

## Attività comuni: creazione rapida di una macchina virtuale in Azure

A volte si sa quale immagine è necessaria e occorre una macchina virtuale di tale immagine al momento e non è rilevante eccessivamente l'infrastruttura \(forse potrebbe essere necessario testare un elemento in una nuova macchina virtuale\). Ovvero quando si desidera utilizzare il comando `azure vm quick-create` e passare gli argomenti necessari per creare una macchina virtuale e la relativa infrastruttura.

Innanzitutto, creare il gruppo di risorse.

    azure group create coreos-quick westus
    info:    Executing command group create
    + Getting resource group coreos-quick                                          
    + Creating resource group coreos-quick                                         
    info:    Created resource group coreos-quick
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick
    data:    Name:                coreos-quick
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: 
    data:    
    info:    group create command OK
    

In secondo luogo, è necessaria un'immagine. Per trovare un'immagine con l’interfaccia della riga di comando di Azure, vedere [Esplorazione e selezione di immagini delle macchine virtuali di Azure con PowerShell e l'interfaccia della riga di comando di Azure](resource-groups-vm-searching.md). Tuttavia, per questa Guida rapida, di seguito è riportato un breve elenco di immagini più diffuse. Verrà creata un’immagine stabile di CoreOS per questa creazione rapida.

| Autore | ImageOffer | ImageSku | ComputeImageVersion |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 | 7\.0.201503 |
| OpenLogic | CentOS | 7\.1 | 7\.1.201504 |
| CoreOS | CoreOS | Beta | 647\.0.0 |
| CoreOS | CoreOS | Stabile | 633\.1.0 |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 | 8\.0.40459 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 | 1\.0.0 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Standard | 1\.0.0 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Enterprise | 1\.0.0 |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW | 12\.0.2430 |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP | 12\.0.2430 |
| Canonical | UbuntuServer | 14\.04.1-LTS | 14\.04.201501230 |
| Canonical | UbuntuServer | 14\.04.2-LTS | 14\.04.201503090 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview | 5\.0.201504 |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials | 1\.0.141204 |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 | 4\.3.4665 |

Creare la macchina virtuale immettendo il `azure vm quick-create command` e prepararsi a seguire le istruzioni visualizzate. Dovrebbe essere visualizzata una schermata analoga alla seguente:

    azure vm quick-create 
    info:    Executing command vm quick-create
    Resource group name: coreos-quick
    Virtual machine name: coreos
    Location name: westus
    Operating system Type [Windows, Linux]: linux
    ImageURN (format: "publisherName:offer:skus:version"): coreos:coreos:stable:633.1.0
    User name: ops
    Password: *********
    Confirm password: *********
    + Looking up the VM "coreos"                                                   
    info:    Using the VM Size "Standard_A1"
    info:    The [OS, Data] Disk or image configuration requires storage account
    + Retrieving storage accounts                                                  
    info:    Could not find any storage accounts in the region "westus", trying to create new one
    + Creating storage account "cli9fd3fce49e9a9b3d14302" in "westus"              
    + Looking up the storage account cli9fd3fce49e9a9b3d14302                      
    + Looking up the NIC "coreo-westu-1430261891570-nic"                           
    info:    An nic with given name "coreo-westu-1430261891570-nic" not found, creating a new one
    + Looking up the virtual network "coreo-westu-1430261891570-vnet"              
    info:    Preparing to create new virtual network and subnet
    / Creating a new virtual network "coreo-westu-1430261891570-vnet" [address prefix: "10.0.0.0/16"] with subnet "coreo-westu-1430261891570-sne+" [address prefix: "10.0.1.0/24"]
    + Looking up the virtual network "coreo-westu-1430261891570-vnet"              
    + Looking up the subnet "coreo-westu-1430261891570-snet" under the virtual network "coreo-westu-1430261891570-vnet"
    info:    Found public ip parameters, trying to setup PublicIP profile
    + Looking up the public ip "coreo-westu-1430261891570-pip"                     
    info:    PublicIP with given name "coreo-westu-1430261891570-pip" not found, creating a new one
    + Creating public ip "coreo-westu-1430261891570-pip"                           
    + Looking up the public ip "coreo-westu-1430261891570-pip"                     
    + Creating NIC "coreo-westu-1430261891570-nic"                                 
    + Looking up the NIC "coreo-westu-1430261891570-nic"                           
    + Creating VM "coreos"                                                         
    + Looking up the VM "coreos"                                                   
    + Looking up the NIC "coreo-westu-1430261891570-nic"                           
    + Looking up the public ip "coreo-westu-1430261891570-pip"                     
    data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Compute/virtualMachines/coreos
    data:    ProvisioningState               :Succeeded
    data:    Name                            :coreos
    data:    Location                        :westus
    data:    FQDN                            :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:    
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:    
    data:    Storage Profile:
    data:      Image reference:
    data:        Publisher                   :coreos
    data:        Offer                       :coreos
    data:        Sku                         :stable
    data:        Version                     :633.1.0
    data:    
    data:      OS Disk:
    data:        OSType                      :Linux
    data:        Name                        :cli9fd3fce49e9a9b3d-os-1430261892283
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://cli9fd3fce49e9a9b3d14302.blob.core.windows.net/vhds/cli9fd3fce49e9a9b3d-os-1430261892283.vhd
    data:    
    data:    OS Profile:
    data:      Computer Name                 :coreos
    data:      User Name                     :ops
    data:      Linux Configuration:
    data:        Disable Password Auth       :false
    data:    
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Network/networkInterfaces/coreo-westu-1430261891570-nic
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-30-72-E3
    data:          Provisioning State        :Succeeded
    data:          Name                      :coreo-westu-1430261891570-nic
    data:          Location                  :westus
    data:            Private IP alloc-method :Dynamic
    data:            Private IP address      :10.0.1.4
    data:            Public IP address       :104.40.24.124
    data:            FQDN                    :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
    info:    vm quick-create command OK
    
La nuova macchina virtuale è stata completata.

## Attività comuni: distribuire una macchina virtuale in Azure da un modello

Utilizzare le istruzioni in queste sezioni per distribuire una nuova macchina virtuale Azure con un modello di interfaccia della riga di comando di Azure. Questo modello consente di creare una singola macchina virtuale in una nuova rete virtuale con una sola subnet e, a differenza di `azure vm quick-create`, consente di descrivere esattamente cosa si desidera e ripeterlo senza errori. Di seguito ciò che viene creato da questo modello:

![](./media/virtual-machines-deploy-rmtemplates-azure-cli/new-vm.png)
 
### Passaggio 1: esaminare il file JSON per i parametri del modello

Di seguito il contenuto del file JSON per il modello. Anche il modello è disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/101-simple-linux-vm/azuredeploy.json)  in GitHub.

I modelli sono flessibili, in modo che la finestra di progettazione può scegliere di assegnare un numero elevato di parametri o potrebbe aver scelto di offrirne solo poche creando un modello più corretto. Per raccogliere le informazioni necessarie per passare il modello come parametri, aprire il file di modello \(in questo argomento è un modello inline di seguito riportato\) ed esaminare i valori dei **parametri**.

In questo caso, il modello riportato di seguito chiederà:

- Un nome dell'account di archiviazione di univoco
- Un nome utente dell'amministratore per la macchina virtuale
- Una password
- Un nome di dominio che dovrà essere utilizzato dal mondo esterno
- e che accetterò un numero di versione Ubuntu Server, ma solo uno di un elenco. 

Una volta definiti questi valori, è possibile creare un gruppo relativo e distribuire il modello nella sottoscrizione di Azure.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "newStorageAccountName": {
                "type": "string",
                "metadata": {
                    "Description": "Unique DNS Name for the Storage Account where the Virtual Machine's disks will be placed."
                }
            },
            "adminUsername": {
                "type": "string",
                "metadata": {
                   "Description": "User name for the Virtual Machine."
                }
            },
            "adminPassword": {
                "type": "securestring",
                "metadata": {
                    "Description": "Password for the Virtual Machine."
                }
            },
            "dnsNameForPublicIP": {
                "type": "string",
                "metadata": {
                      "Description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
                }
            },
            "ubuntuOSVersion": {
                "type": "string",
                "defaultValue": "14.10",
                "allowedValues": [
                    "12.04.2-LTS",
                    "12.04.3-LTS",
                    "12.04.4-LTS",
                    "12.04.5-LTS",
    				"12.10",
                    "14.04.2-LTS",
                    "14.10",
                    "15.04"
                ],
                "metadata": {
                    "Description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version. Allowed values: 12.04.2-LTS, 12.04.3-LTS, 12.04.4-LTS, 12.04.5-LTS, 12.10, 14.04.2-LTS, 14.10, 15.04."
                }
            }
        },
        "variables": {
            "location": "West US",
            "imagePublisher": "Canonical", 
            "imageOffer": "UbuntuServer", 
            "OSDiskName": "osdiskforlinuxsimple",
            "nicName": "myVMNic",
            "addressPrefix": "10.0.0.0/16", 
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyUbuntuVM",
            "vmSize": "Standard_D1",
            "virtualNetworkName": "MyVNET",        
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
        },    
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[parameters('newStorageAccountName')]",
                "apiVersion": "2015-05-01-preview",
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
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnetName')]",
                            "properties": {
                                "addressPrefix": "[variables('subnetPrefix')]"
                            }
                        }
                    ]
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
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnetRef')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
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
                        "computername": "[variables('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]"
                    },
                    "storageProfile": {
                        "imageReference": {
                            "publisher": "[variables('imagePublisher')]",
                            "offer": "[variables('imageOffer')]",
                            "sku" : "[parameters('ubuntuOSVersion')]",
                            "version":"latest"
                        },
                       "osDisk" : {
                            "name": "osdisk",
                            "vhd": {
                                "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                            },
                            "caching": "ReadWrite",
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                            }
                        ]
                    }
                }
            }
        ]
    } 
  
### Passaggio 2: creare la macchina virtuale con il modello

Dopo aver preparato i valori di parametro, è necessario creare un gruppo di risorse per la distribuzione del modello, quindi procedere con l’operazione.

Per creare il gruppo di risorse, digitare `azure group create <group name> <location>` con il nome del gruppo desiderato e la posizione del data center in cui si desidera effettuare la distribuzione. Ciò si verifica rapidamente:

    azure group create myResourceGroup westus
    info:    Executing command group create
    + Getting resource group myResourceGroup                                       
    + Creating resource group myResourceGroup                                      
    info:    Created resource group myResourceGroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup
    data:    Name:                myResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: 
    data:    
    info:    group create command OK
    

Per creare la distribuzione, chiamare `azure group deployment create` e passare:

- Il file di modello \(nel caso in cui il modello JSON precedente è stato salvato in un file locale\) 
- un URI del modello \(nel caso in cui si desidera indicare il file in Github o un altro indirizzo Web\)
- Il gruppo di risorse in cui si desidera effettuare la distribuzione
- e un nome di distribuzione facoltativo. 

Verrà richiesto di fornire i valori dei parametri contenuti nella sezione **"parameters"** del file JSON. Dopo aver specificato tutti i valori dei parametri, verrà avviata la distribuzione.

Di seguito è fornito un esempio:

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json myResourceGroup firstDeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    newStorageAccountName: storageaccount
    adminUsername: ops
    adminPassword: password
    dnsNameForPublicIP: newdomainname
    
Verrà visualizzato il tipo di informazioni seguente:

    + Initializing template configurations and parameters                          
    + Creating a deployment                                                        
    info:    Created template deployment "firstDeployment"
    + Registering providers                                                        
    info:    Registering provider microsoft.storage
    info:    Registering provider microsoft.network
    info:    Registering provider microsoft.compute
    + Waiting for deployment to complete                                           
    data:    DeploymentName     : firstDeployment
    data:    ResourceGroupName  : myResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T07:53:55.1828878Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                   Type          Value        
    data:    ---------------------  ------------  -------------
    data:    newStorageAccountName  String        storageaccount
    data:    adminUsername          String        ops          
    data:    adminPassword          SecureString  undefined    
    data:    dnsNameForPublicIP     String        newdomainname   
    data:    ubuntuOSVersion        String        14.10        
    info:    group deployment create command OK
    


## Attività comuni: creare un'immagine di macchina virtuale personalizzata

È stato osservato l'utilizzo di base dei modelli precedenti, pertanto è ora possibile utilizzare istruzioni simili per creare una macchina virtuale personalizzata da un file VHD in Azure con un modello tramite l’interfaccia della riga di comando di Azure. La differenza è che questo modello consente di creare una singola macchina virtuale da un disco rigido virtuale \(VHD\) specificato.

### Passaggio 1: esaminare il file JSON per il modello

Di seguito il contenuto del file JSON per il modello di questa sezione viene utilizzato come esempio, ma è possibile trovare il modello stesso [qui](https://raw.githubusercontent.com/azurermtemplates/azurermtemplates/master/101-vm-from-user-image/azuredeploy.json).

Anche in questo caso, è necessario trovare i valori da immettere per i parametri privi di valori predefiniti. Quando si esegue il `azure group deployment create` comando dell’interfaccia della riga di comando di Azure verrà richiesto di immettere tali valori.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
            "userImageStorageAccountName": {
                "type": "string",
                "defaultValue" : "userImageStorageAccountName"
            },
            "userImageStorageContainerName" : {
                "type" : "string",
                "defaultValue" : "userImageStorageContainerName"
            },
            "userImageVhdName" : {
                "type" : "string",
                "defaultValue" : "userImageVhdName"
            },
            "dnsNameForPublicIP" : {
                "type" : "string",
                "defaultValue": "uniqueDnsNameForPublicIP"
            },
            "adminUserName": {
                "type": "string"
            },
            "adminPassword": {
                "type": "securestring"
            },
            "osType" : {
                "type" : "string",
                "allowedValues" : [
                    "windows",
                    "linux"
                ]
            },
            "subscriptionId":{
                "type" : "string"
            },
            "location": {
                "type": "String",
                "defaultValue" : "West US"
            },
            "vmSize": {
                "type": "string",
                "defaultValue": "Standard_A2"
            },
            "publicIPAddressName": {
                "type": "string",
                "defaultValue" : "myPublicIP"
            },
            "vmName": {
                "type": "string",
                "defaultValue" : "myVM"
            },
            "virtualNetworkName":{
                "type" : "string",
                "defaultValue" : "myVNET"
            },
            "nicName":{
                "type" : "string",
                "defaultValue":"myNIC"
            }
        },
        "variables": {
            "addressPrefix":"10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix" : "10.0.0.0/24",
            "subnet2Prefix" : "10.0.1.0/24",
            "publicIPAddressType" : "Dynamic",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "userImageName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('userImageVhdName'))]",
            "osDiskVhdName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
          "apiVersion": "2014-12-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[variables('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[variables('subnet1Name')]",
                "properties" : {
                    "addressPrefix": "[variables('subnet1Prefix')]"
                }
              },
              {
                "name": "[variables('subnet2Name')]",
                "properties" : {
                    "addressPrefix": "[variables('subnet2Prefix')]"
                }
              }
            ]
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
                "ipConfigurations": [
                {
                    "name": "ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod": "Dynamic",
                        "publicIPAddress": {
                            "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                        },
                        "subnet": {
                            "id": "[variables('subnet1Ref')]"
                        }
                    }
                }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk" : {
                        "name" : "[concat(parameters('vmName'),'-osDisk')]",
                        "osType" : "[parameters('osType')]",
                        "caching" : "ReadWrite",
                        "image" : {
                            "uri" : "[variables('userImageName')]"
                        },
                        "vhd" : {
                            "uri" : "[variables('osDiskVhdName')]"
                        }
                    }
                },
                "networkProfile": {
                    "networkInterfaces" : [
                    {
                        "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                    }
                    ]
                }
            }
        }
        ]
    }

### Passaggio 2: ottenere il disco rigido virtuale

Ovviamente, è necessario un file VHD per questa operazione. È possibile utilizzare uno che già si trova in Azure oppure è possibile caricarne uno.

Per una macchina virtuale basata su Windows, vedere [Creazione e caricamento di un disco rigido virtuale con Windows Server in Azure](virtual-machines-create-upload-vhd-windows-server.md).

Per una macchina virtuale basata su Linux, vedere [Creazione e caricamento di un disco rigido virtuale con Linux in Azure](virtual-machines-linux-create-upload-vhd.md).

### Passaggio 3: creare la macchina virtuale con il modello

A questo punto è possibile creare una nuova macchina virtuale in base al file VHD. Creare un gruppo di distribuzione, utilizzando `azure group create <location>`:

    azure group create myResourceGroupUser eastus
    info:    Executing command group create
    + Getting resource group myResourceGroupUser                                            
    + Creating resource group myResourceGroupUser                                           
    info:    Created resource group myResourceGroupUser
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupUser
    data:    Name:                myResourceGroupUser
    data:    Location:            eastus
    data:    Provisioning State:  Succeeded
    data:    Tags: 
    data:    
    info:    group create command OK
    
Quindi creare la distribuzione utilizzando l’opzione `--template-uri` che consente di chiamare direttamente nel modello \(o è possibile utilizzare l’opzione `--template-file` per utilizzare un file salvato in locale\). Si noti che poiché il modello dispone di valori predefiniti specificati, vengono richiesti solo per alcune operazioni. Se si distribuisce il modello in posizioni diverse, è probabile che si verificano alcuni conflitti di denominazione con i valori predefiniti \(in particolare il nome DNS creato\).

    azure group deployment create \
    > --template-uri https://raw.githubusercontent.com/azurermtemplates/azurermtemplates/master/101-vm-from-user-image/azuredeploy.json \
    > myResourceGroup \
    > customVhdDeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    adminUserName: ops
    adminPassword: password
    osType: linux
    subscriptionId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        
Verrà visualizzata una schermata simile alla seguente:

    + Initializing template configurations and parameters                          
    + Creating a deployment                                                        
    info:    Created template deployment "customVhdDeployment"
    + Registering providers                                                        
    info:    Registering provider microsoft.network
    info:    Registering provider microsoft.compute
    + Waiting for deployment to complete                                           
    error:   Deployment provisioning state was not successful
    data:    DeploymentName     : customVhdDeployment
    data:    ResourceGroupName  : myResourceGroupUser
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T14:55:48.0963829Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/azurermtemplates/azurermtemplates/master/101-vm-from-user-image/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                           Type          Value                               
    data:    -----------------------------  ------------  ------------------------------------
    data:    userImageStorageAccountName    String        userImageStorageAccountName         
    data:    userImageStorageContainerName  String        userImageStorageContainerName       
    data:    userImageVhdName               String        userImageVhdName                    
    data:    dnsNameForPublicIP             String        uniqueDnsNameForPublicIP            
    data:    adminUserName                  String        ops                                 
    data:    adminPassword                  SecureString  undefined                           
    data:    osType                         String        linux                               
    data:    subscriptionId                 String        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    data:    location                       String        West US                             
    data:    vmSize                         String        Standard_A2                         
    data:    publicIPAddressName            String        myPublicIP                          
    data:    vmName                         String        myVM                                
    data:    virtualNetworkName             String        myVNET                              
    data:    nicName                        String        myNIC                               
    info:    group deployment create command OK
    

## Distribuire un'applicazione per la distribuzione di più macchine virtuali che utilizza una rete virtuale e un servizio di bilanciamento del carico esterno

Questo modello consente di creare 2 macchine virtuali in un servizio di bilanciamento del carico e configurare una regola sulla porta 80 di bilanciamento del carico. Questo modello consente inoltre di distribuire un account di archiviazione, una rete virtuale, l'indirizzo IP pubblico, il set di disponibilità e le interfacce di rete.

![](./media/virtual-machines-deploy-rmtemplates-azure-cli/multivmextlb.png)
 
Seguire questi passaggi per distribuire un'applicazione per più macchine virtuali che utilizza una rete virtuale e un servizio di bilanciamento del carico mediante un modello di Gestione risorse nell'archivio dei modelli Github tramite i comandi PowerShell di Azure.

### Passaggio 1: esaminare il file JSON per il modello.

Di seguito il contenuto del file JSON per il modello. Se si desidera la versione più recente, si trova [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json). In questo argomento viene utilizzato lo switch `--template-uri` per passare la chiamata del modello, ma è anche possibile utilizzare lo switch `--template-file` per passare a una versione locale.


    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "location": {
                "type": "string",
                "metadata": {
                  "description": "Location of resources"
                }
            },
            "storageAccountName": {
                "type": "string",
                "metadata": {
                  "description": "Name of storage account"
                }
            },
            "adminUsername": {
                "type": "string",
                "metadata": {
                  "description": "Admin username"
                }
            },
            "adminPassword": {
                "type": "securestring",
                "metadata": {
                  "description": "Admin password"
                }
            },
            "dnsNameforLBIP": {
                "type": "string",
                "metadata": {
                  "description": "DNS for Load Balancer IP"
                }
            },
            "backendPort": {
                "type": "int",
                "defaultValue": 3389,
                "metadata": {
                  "description": "Backend port"
                }
            },
            "vmNamePrefix": {
                "type": "string",
                "defaultValue": "myVM",
                "metadata": {
                  "description": "Prefix to use for VM names"
                }
            },
            "vmSourceImageName": {
                "type": "string",
                "defaultValue": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"
            },
            "lbName": {
                "type": "string",
                "defaultValue": "myLB",
                "metadata": {
                  "description": "Load Balancer name"
                }
            },
            "nicNamePrefix": {
                "type": "string",
                "defaultValue": "nic",
                "metadata": {
                  "description": "Network Interface name prefix"
                }
            },
            "publicIPAddressName": {
                "type": "string",
                "defaultValue": "myPublicIP",
                "metadata": {
                  "description": "Public IP Name"
                }
            },
            "vnetName": {
                "type": "string",
                "defaultValue": "myVNET",
                "metadata": {
                  "description": "VNET name"
                }
            },
            "vmSize": {
                "type": "string",
                "defaultValue": "Standard_A1",
                "metadata": {
                  "description": "Size of the VM"
                }
            }
        },
        "variables": {
            "storageAccountType": "Standard_LRS",
            "vmStorageAccountContainerName": "vhds",
            "availabilitySetName": "myAvSet",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet-1",
            "subnetPrefix": "10.0.0.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
            "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
            "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
            "numberOfInstances": 2,
            "nicId1": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 0))]",
            "nicId2": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 1))]",
            "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LBFE')]",
            "backEndIPConfigID1": "[concat(variables('nicId1'),'/ipConfigurations/ipconfig1')]",
            "backEndIPConfigID2": "[concat(variables('nicId2'),'/ipConfigurations/ipconfig1')]",
            "sourceImageName": "[concat('/', subscription().subscriptionId,'/services/images/',parameters('vmSourceImageName'))]",
            "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/LBBE')]",
            "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
        },
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[parameters('storageAccountName')]",
                "apiVersion": "2015-05-01-preview",
                "location": "[parameters('location')]",
                "properties": {
                    "accountType": "[variables('storageAccountType')]"
                }
            },
            {
                "type": "Microsoft.Compute/availabilitySets",
                "name": "[variables('availabilitySetName')]",
                "apiVersion": "2015-05-01-preview",
                "location": "[parameters('location')]",
                "properties": { }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameforLBIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('vnetName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnetName')]",
                            "properties": {
                                "addressPrefix": "[variables('subnetPrefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
                "location": "[parameters('location')]",
                "copy": {
                    "name": "nicLoop",
                    "count": "[variables('numberOfInstances')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "subnet": {
                                    "id": "[variables('subnetRef')]"
                                }
                            },
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/backendAddressPools/LBBE')]"
                                }
                            ],
                            "loadBalancerInboundNatRules": [
                                {
                                    "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/inboundNatRule/RDP-VM', copyindex())]"
                                }
                            ]
    
    
                        }
                    ]
    
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "name": "[parameters('lbName')]",
                "type": "Microsoft.Network/loadBalancers",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "nicLoop",
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LBFE",
                            "properties": {
                                "publicIPAddress": {
                                    "id": "[variables('publicIPAddressID')]"
                                }
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LBBE"
    
                        }
                    ],
                    "inboundNatRules": [
                        {
                            "name": "RDP-VM1",
                            "properties": {
                                "frontendIPConfiguration":
                                    {
                                        "id": "[variables('frontEndIPConfigID')]"
                                    },
                                "protocol": "tcp",
                                "frontendPort": 50001,
                                "backendPort": 3389,
                                "enableFloatingIP": false
                            }
                        },
                        {
                            "name": "RDP-VM2",
                            "properties": {
                                "frontendIPConfiguration":
                                    {
                                        "id": "[variables('frontEndIPConfigID')]"
                                    },
                                "protocol": "tcp",
                                "frontendPort": 50002,
                                "backendPort": 3389,
                                "enableFloatingIP": false
                            }
                        }
                    ],
                    "loadBalancingRules": [
                        {
                            "name": "LBRule",
                            "properties": {
                                "frontendIPConfiguration": {
                                    "id": "[variables('frontEndIPConfigID')]"
                                },
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID')]"
                                },
                                "protocol": "tcp",
                                "frontendPort": 80,
                                "backendPort": 80,
                                "enableFloatingIP": false,
                                "idleTimeoutInMinutes": 5,
                                "probe": {
                                    "id": "[variables('lbProbeID')]"
                                }
                            }
                        }
                    ],
                    "probes": [
                        {
                            "name": "tcpProbe",
                            "properties": {
                                "protocol": "tcp",
                                "port": 80,
                                "intervalInSeconds": "5",
                                "numberOfProbes": "2"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
                "copy": {
                    "name": "virtualMachineLoop",
                    "count": "[variables('numberOfInstances')]"
                },
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
                    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
                ],
                "properties": {
                    "availabilitySet": {
                        "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
                    },
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[concat(parameters('vmNamePrefix'), copyIndex())]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]"
                    },
                    "storageProfile": {
                        "sourceImage": {
                            "id": "[variables('sourceImageName')]"
                        },
                        "destinationVhdsContainer": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/')]"
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
                            }
                        ]
                    }
                }
            }
        ]
    }

### Passaggio 2: creare la distribuzione con il modello.

Creare un gruppo di risorse per il modello utilizzando `azure group create <location>`, quindi creare una distribuzione in tale gruppo di risorse utilizzando `azure group deployment create` e passando il gruppo di risorse, un nome di distribuzione e rispondere alle istruzioni visualizzate per i parametri di modello senza valori predefiniti.


    azure group create lbgroup westus
    info:    Executing command group create
    + Getting resource group lbgroup                                               
    + Creating resource group lbgroup                                              
    info:    Created resource group lbgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/lbgroup
    data:    Name:                lbgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: 
    data:    
    info:    group create command OK
    

A questo punto, utilizzare il comando `azure group deployment create` e l’opzione `--template-uri` per distribuire il modello. Quando viene richiesto, come mostrato di seguito, fornire i valori dei parametri.

    azure group deployment create \
    > --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json \
    > lbgroup \
    > newdeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    location: westus
    newStorageAccountName: storagename
    adminUsername: ops
    adminPassword: password
    dnsNameforLBIP: lbdomainname
    + Initializing template configurations and parameters                          
    + Creating a deployment                                                        
    info:    Created template deployment "newdeployment"
    + Registering providers                                                        
    info:    Registering provider microsoft.storage
    info:    Registering provider microsoft.compute
    info:    Registering provider microsoft.network
    + Waiting for deployment to complete                                           
    data:    DeploymentName     : newdeployment
    data:    ResourceGroupName  : lbgroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T20:58:40.1678876Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                   Type          Value                 
    data:    ---------------------  ------------  ----------------------
    data:    location               String        westus                
    data:    newStorageAccountName  String        storagename         
    data:    adminUsername          String        ops                   
    data:    adminPassword          SecureString  undefined             
    data:    dnsNameforLBIP         String        lbdomainname         
    data:    backendPort            Int           3389                  
    data:    vmNamePrefix           String        myVM                  
    data:    imagePublisher         String        MicrosoftWindowsServer
    data:    imageOffer             String        WindowsServer         
    data:    imageSKU               String        2012-R2-Datacenter    
    data:    lbName                 String        myLB                  
    data:    nicNamePrefix          String        nic                   
    data:    publicIPAddressName    String        myPublicIP            
    data:    vnetName               String        myVNET                
    data:    vmSize                 String        Standard_A1           
    info:    group deployment create command OK
    
Si noti che questo modello consente di distribuire un'immagine di Windows Server; Tuttavia, potrebbe facilmente essere sostituita anche con qualsiasi immagine Linux. Si desidera creare un cluster docker in più aree? [Sì, è possibile](http://azure.microsoft.com/documentation/templates/201-discover-private-ip-dynamically/).

## Rimuovere un gruppo di risorse

Tenere presente che è possibile effettuare una nuova distribuzione a un gruppo di risorse, ma in tal caso, se viene effettuata con un’unica risorsa, sarà possibile eliminare la distribuzione utilizzando il comando `azure group delete <group name>`.

    azure group delete myResourceGroup
    info:    Executing command group delete
    Delete resource group myResourceGroup? [y/n] y
    + Deleting resource group myResourceGroup                                               
    info:    group delete command OK
    
## Visualizzare il log per una distribuzione del gruppo di risorse

Questa operazione è piuttosto comune durante la creazione o l'utilizzo di modelli. Il comando per richiamare e visualizzare i log di distribuzione relativi a un gruppo è `azure group log show <groupname>`, che consente di visualizzare una certa quantità di informazioni utili per comprendere perché qualcosa si è verificato o meno un evento. Per ulteriori informazioni sulla risoluzione dei problemi delle distribuzioni, nonché altre informazioni sui problemi, vedere [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](resource-group-deploy-debug.md).

Per errori specifici  ad esempio, è possibile utilizzare strumenti come **jq** per eseguire query in modo più preciso, ad esempio individuare i singoli errori che è necessario correggere. Nell'esempio seguente viene utilizzato **jq** per analizzare un log di distribuzione denominato **lbgroup** al fine di individuare eventuali errori

    azure group log show lbgroup -l --json | jq '.[] | select(.status.value == "Failed") | .properties' 

È possibile individuare molto rapidamente la causa dell'errore, correggerlo e riprovare. Nel caso seguente con il modello sono state create due macchine virtuali contemporaneamente, che hanno generato un blocco sul file VHD. Dopo aver modificato il modello, la distribuzione è stata completata rapidamente.

    {
      "statusCode": "Conflict",
      "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"AcquireDiskLeaseFailed\",\"message\":\"Failed to acquire lease while creating disk 'osdisk' using blob with URI http://storage.blob.core.windows.net/vhds/osdisk.vhd.\"}]}}"
    }
    

## Visualizzare informazioni relative a una macchina virtuale

È possibile visualizzare informazioni sulle specifiche macchine virtuali nel gruppo risorse in uso utilizzando il comando `azure vm show <groupname> <vmname> command`. In primo luogo, qualora si disponesse di più macchine virtuali potrebbe essere necessario includere le macchine virtuali in un gruppo utilizzando il comando `azure vm list <groupname>`.

    azure vm list zoo
    info:    Executing command vm list
    + Getting virtual machines                                                     
    data:    Name   ProvisioningState  Location  Size       
    data:    -----  -----------------  --------  -----------
    data:    myVM0  Succeeded          westus    Standard_A1
    data:    myVM1  Failed             westus    Standard_A1
    
Quindi,  osservando myVM1 si evince che:

    azure vm show zoo myVM1
    info:    Executing command vm show
    + Looking up the VM "myVM1"                                                    
    + Looking up the NIC "nic1"                                                    
    data:    Id                              :/subscriptions/8f2d8c5f-742a-4f1b-a2ed-a2b8b246bcd6/resourceGroups/zoo/providers/Microsoft.Compute/virtualMachines/myVM1
    data:    ProvisioningState               :Failed
    data:    Name                            :myVM1
    data:    Location                        :westus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:    
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:    
    data:    Storage Profile:
    data:      Image reference:
    data:        Publisher                   :MicrosoftWindowsServer
    data:        Offer                       :WindowsServer
    data:        Sku                         :2012-R2-Datacenter
    data:        Version                     :latest
    data:    
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :osdisk
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :http://zoostorageralph.blob.core.windows.net/vhds/osdisk.vhd
    data:    
    data:    OS Profile:
    data:      Computer Name                 :myVM1
    data:      User Name                     :ops
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:    
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/8f2d8c5f-742a-4f1b-a2ed-a2b8b246bcd6/resourceGroups/zoo/providers/Microsoft.Network/networkInterfaces/nic1
    data:          Primary                   :false
    data:          Provisioning State        :Succeeded
    data:          Name                      :nic1
    data:          Location                  :westus
    data:            Private IP alloc-method :Dynamic
    data:            Private IP address      :10.0.0.5
    data:    
    data:    AvailabilitySet:
    data:      Id                            :/subscriptions/8f2d8c5f-742a-4f1b-a2ed-a2b8b246bcd6/resourceGroups/zoo/providers/Microsoft.Compute/availabilitySets/MYAVSET
    info:    vm show command OK
    

> [AZURE.NOTE]Se si desidera archiviare e modificare l'output dei comandi della console a livello di programmazione, è possibile utilizzare un formato JSON come strumento di analisi, tra cui **[jq](https://github.com/stedolan/jq)**, **[jsawk](https://github.com/micha/jsawk)** o librerie di linguaggi valido per l'attività.

## Visualizzare informazioni su una macchina virtuale

Si tratta di un'attività di base che si utilizzerà spesso. È possibile utilizzarla per ottenere informazioni su una macchina virtuale, eseguire le attività in una macchina virtuale o recuperare l'output da archiviare in una variabile.

Per ottenere informazioni sulla macchina virtuale, eseguire questo comando, sostituendo tutto ciò che è racchiuso tra virgolette, inclusi i caratteri < and >:

     azure vm show -g <group name> -n <virtual machine name>

Per archiviare l'output in una variabile $vm come un documento JSON, eseguire:

    vmInfo=$(azure vm show -g <group name> -n <virtual machine name> --json)
    
In alternativa, è possibile reindirizzare stdout in un file.

## Accedere a una macchina virtuale basata su Linux

In genere macchine Linux sono connesse tramite SSH. Per altre informazioni, vedere [Come usare SSH con Linux in Azure](virtual-machines-linux-use-ssh-key.md).

## Arrestare una macchina virtuale

Eseguire questo comando.

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT]Utilizzare questo parametro per mantenere l'IP virtuale \(VIP\) del servizio cloud, qualora fosse l'ultima macchina virtuale inclusa nel servizio cloud specifico. <br><br> Se si utilizza il parametro StayProvisioned, sarà ancora configurato per la macchina virtuale.

## Avviare una macchina virtuale

Panoramica su Gestione risorse di Azure. Eseguire questo comando: azure vm start <group name> <virtual machine name>

## Collegare un disco dati

È inoltre necessario decidere se collegare un nuovo disco o uno che contiene già dati. Per un nuovo disco, il comando permette di creare il file con estensione VHD e contemporaneamente di collegarlo.

Per collegare un nuovo disco, eseguire questo comando:

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb> 

Per collegare un disco dati esistente, eseguire questo comando:

    azure vm disk attach <resource-group> <vm-name> [vhd-url]
    
Quindi è necessario montare il disco, come si farebbe normalmente in Linux \(o in Windows\).


## Passaggi successivi

Per  ulteriori esempi di utilizzo dell’interfaccia della riga di comando di Azure in modalità **arm**, vedere il documento relativo all’[utilizzo dell’interfaccia della riga di comando di Microsoft Azure per Mac, Linux e Windows con Gestione risorse di Microsoft Azure.](xplat-cli-resource-manager.md). Per ulteriori informazioni sulle risorse di Azure e i relativi concetti, vedere [Panoramica di Gestione risorse di Microsoft Azure](resource-group-overview.md).









<!--HONumber=52-->
