
Questo articolo illustra come usare i modelli di Gestione risorse e l'interfaccia della riga di comando di Azure per eseguire attività comuni per la distribuzione e la gestione delle macchine virtuali di Azure. Per altri modelli disponili, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/) e [Creare framework di applicazioni utilizzando modelli](../articles/virtual-machines/virtual-machines-linux-app-frameworks.md).


- [Creazione rapida di una macchina virtuale in Azure](#quick-create-a-vm-in-azure)
- [Distribuire una macchina virtuale in Azure da un modello](#deploy-a-vm-in-azure-from-a-template)
- [Creare una macchina virtuale da un'immagine personalizzata](#create-a-custom-vm-image)
- [Distribuire una macchina virtuale che usa una rete virtuale e un servizio di bilanciamento del carico](#deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer)
- [Rimuovere un gruppo di risorse](#remove-a-resource-group)
- [Visualizzare il log per una distribuzione del gruppo di risorse](#show-the-log-for-a-resource-group-deployment)
- [Visualizzare informazioni relative a una macchina virtuale](#display-information-about-a-virtual-machine)
- [Connettersi a una macchina virtuale basata su Linux](#log-on-to-a-linux-based-virtual-machine)
- [Arrestare una macchina virtuale](#stop-a-virtual-machine)
- [Avviare una macchina virtuale](#start-a-virtual-machine)
- [Collegare un disco dati](#attach-a-data-disk)

## Preparazione

Prima di poter usare l'interfaccia della riga di comando di Azure con i gruppi di risorse di Azure, è necessario disporre della versione corretta dell'interfaccia della riga di comando di Azure e di un account Azure. Se non è disponibile l'interfaccia della riga di comando di Azure, è necessario [installarla](xplat-cli-install.md).

### Aggiornare la versione dell'interfaccia della riga di comando di Azure alla 0.9.0 o successiva

Digitare `azure --version` per verificare se è già installata la versione 0.9.0 o una versione successiva.

	azure --version
    0.9.0 (node: 0.10.25)

Se la versione installata non corrisponde alla 0.9.0 o a una versione successiva, è necessario aggiornarla usando uno dei programmi di installazione nativi oppure tramite **npm** digitando `npm update -g azure-cli`.

È anche possibile eseguire l'interfaccia della riga di comando di Azure come contenitore Docker usando l'[immagine Docker](https://registry.hub.docker.com/u/microsoft/azure-cli/) seguente. Da un host Docker, eseguire il comando seguente:

	docker run -it microsoft/azure-cli

### Impostare l'account e la sottoscrizione di Azure

Se non si dispone già una sottoscrizione di Azure, ma si dispone di un abbonamento MSDN, è possibile attivare i [Vantaggi per gli abbonati MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure [iscriversi per una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

[Accedere all'account Azure in modo interattivo](../articles/xplat-cli-connect.md#use-the-log-in-method) digitando `azure login` e seguendo le istruzioni dell'esperienza interattiva per l'accesso all'account Azure.

> [AZURE.NOTE] Se si dispone di un ID aziendale o dell'istituto di istruzione e si è certi che l'autenticazione a due fattori non è abilitata, è **anche** possibile usare `azure login -u` con l'ID aziendale o dell'istituto di istruzione per accedere *senza* una sessione interattiva. Se non si dispone di un ID aziendale o dell'istituto di istruzione, è possibile [crearne uno dall'account Microsoft personale](../articles/virtual-machines/virtual-machines-windows-create-aad-work-id.md) per accedere nello stesso modo.

L'account può includere più di una sottoscrizione. È possibile elencare le sottoscrizioni digitando `azure account list`, comando che potrebbe essere visualizzato in modo simile al seguente:

    azure account list
    info:    Executing command account list
    data:    Name                              Id                                    Tenant Id                            Current
    data:    --------------------------------  ------------------------------------  ------------------------------------  -------
    data:    Contoso Admin                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  true
    data:    Fabrikam dev                      xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    data:    Fabrikam test                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    data:    Contoso production                xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  

È possibile impostare la sottoscrizione di Azure corrente digitando quanto segue. Usare il nome o l'ID della sottoscrizione che contiene le risorse da gestire.

	azure account set <subscription name or ID> true



### Passare alla modalità gruppo di risorse dell'interfaccia della riga di comando di Azure

Per impostazione predefinita, l'interfaccia della riga di comando di Azure viene avviata nella modalità di gestione dei servizi (modalità **Gestione dei servizi di Azure**). Digitare quanto segue per passare alla modalità gruppo di risorse.

	azure config mode arm

## Informazioni su modelli di risorse e gruppi di risorse di Azure

La maggior parte delle applicazioni è costituita da una combinazione di tipi di risorse diversi, ad esempio una o più macchine virtuali e account di archiviazione, un database SQL, una rete virtuale o una rete per la distribuzione di contenuti. L'API di gestione del servizio Azure predefinita e il portale di Azure classico rappresentano questi elementi tramite un approccio servizio per servizio. Questo approccio prevede che i singoli servizi vengano distribuiti e gestiti individualmente (o tramite appositi strumenti) e non come una singola unità logica di distribuzione.

I *modelli di Gestione risorse di Azure*, tuttavia, consentono di distribuire e gestire queste risorse diverse come una singola unità logica di distribuzione in modo dichiarativo. Anziché in modo imperativo indicando a Azure gli elementi da distribuire un comando dopo l'altro, è possibile descrivere l'intera distribuzione in un file JSON (tutte le risorse, la configurazione associata e i parametri di distribuzione) e indicare ad Azure di distribuire le risorse come un singolo gruppo.

È quindi possibile gestire il ciclo di vita complessivo delle risorse del gruppo tramite i comandi di gestione delle risorse dell'interfaccia della riga di comando di Azure per effettuare le operazioni seguenti:

- Arrestare, avviare o eliminare tutte le risorse all'interno del gruppo contemporaneamente.
- Applicare le regole di controllo degli accessi in base al ruolo (RBAC) per bloccare le autorizzazioni di sicurezza su di essi.
- Controllare le operazioni.
- Contrassegnare le risorse con metadati aggiuntivi per una gestione più efficiente.

Per altre informazioni sui gruppi di risorse di Azure e su come usarli, vedere [Panoramica di Gestione risorse di Azure](../articles/resource-group-overview.md). Se si è interessati alla creazione di modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../articles/resource-group-authoring-templates.md).

## <a id="quick-create-a-vm-in-azure"></a>Attività: Creare rapidamente una macchina virtuale in Azure

A volte si sa quale immagine è necessaria e occorre una macchina virtuale di tale immagine al momento e non è rilevante eccessivamente l'infrastruttura (forse potrebbe essere necessario testare un elemento in una nuova macchina virtuale). Ovvero quando si desidera utilizzare il comando `azure vm quick-create` e passare gli argomenti necessari per creare una macchina virtuale e la relativa infrastruttura.

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


In secondo luogo, è necessaria un'immagine. Per trovare un'immagine con l'interfaccia della riga di comando di Azure, vedere [Esplorazione e selezione di immagini delle macchine virtuali di Azure con PowerShell e l'interfaccia della riga di comando di Azure](../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md). In questo articolo è riportato un breve elenco delle immagini più diffuse. Verrà creata un'immagine stabile di CoreOS per questa creazione rapida.

> [AZURE.NOTE] Per ComputeImageVersion, è anche possibile specificare semplicemente 'latest' come parametro nel linguaggio di modello e nell'interfaccia della riga di comando di Azure. In questo modo sarà possibile usare sempre la versione dell'immagine più recente e con l'applicazione di tutte le patch necessarie senza dover modificare script o modelli, come illustrato di seguito.

| PublisherName | Offerta | Sku | Versione |
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
| Canonical | UbuntuServer | 12\.04.5-LTS | 12\.04.201504230 |
| Canonical | UbuntuServer | 14\.04.2-LTS | 14\.04.201503090 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | 3\.0.201503 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | 4\.0.201503 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview | 5\.0.201504 |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials | 1\.0.141204 |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 | 4\.3.4665 |

Creare la macchina virtuale immettendo il comando `azure vm quick-create` e prepararsi a seguire le istruzioni visualizzate. Dovrebbe essere visualizzata una schermata analoga alla seguente:

    azure vm quick-create
    info:    Executing command vm quick-create
    Resource group name: coreos-quick
    Virtual machine name: coreos
    Location name: westus
    Operating system Type [Windows, Linux]: linux
    ImageURN (format: "publisherName:offer:skus:version"): coreos:coreos:stable:latest
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

## <a id="deploy-a-vm-in-azure-from-a-template"></a>Attività: Distribuire una macchina virtuale in Azure da un modello

Per distribuire una nuova macchina virtuale di Azure da un modello usando l'interfaccia della riga di comando di Azure, seguire le istruzioni riportate nelle sezioni seguenti. Questo modello consente di creare una singola macchina virtuale in una nuova rete virtuale con una sola subnet e, a differenza di `azure vm quick-create`, consente di descrivere esattamente cosa si desidera e ripeterlo senza errori. Di seguito ciò che viene creato da questo modello:

![](./media/virtual-machines-common-cli-deploy-templates/new-vm.png)

### Passaggio 1: esaminare il file JSON per i parametri del modello

Di seguito il contenuto del file JSON per il modello. Anche il modello è disponibile in [GitHub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json).

I modelli sono flessibili, quindi l'autore potrebbe aver scelto di rendere disponibili molti parametri oppure di offrirne un numero più limitato creando un modello più rigido. Per raccogliere le informazioni necessarie per passare il modello come parametri, aprire il file di modello (in questo argomento è un modello inline di seguito riportato) ed esaminare i valori dei **parametri**.

In questo caso, il modello riportato di seguito chiederà:

- Un nome dell'account di archiviazione univoco.
- Un nome utente dell'amministratore per la macchina virtuale.
- Una password.
- Un nome di dominio che potrà essere usato dall'esterno.
- Un numero di versione di Ubuntu Server (verrà accettato un solo numero dall'interno di un elenco).

Una volta definiti questi valori, è possibile creare un gruppo relativo e distribuire il modello nella sottoscrizione di Azure.

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "newStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "Unique DNS name for the storage account where the virtual machine's disks will be placed."
        }
        },
        "adminUsername": {
        "type": "string",
        "metadata": {
            "description": "User name for the virtual machine."
        }
        },
        "adminPassword": {
        "type": "securestring",
        "metadata": {
            "description": "Password for the virtual machine."
        }
        },
        "dnsNameForPublicIP": {
        "type": "string",
        "metadata": {
            "description": "Unique DNS name for the public IP used to access the virtual machine."
        }
        },
        "ubuntuOSVersion": {
        "type": "string",
        "defaultValue": "14.04.2-LTS",
        "allowedValues": [
            "12.04.5-LTS",
            "14.04.2-LTS",
            "15.04"
        ],
        "metadata": {
            "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version. Allowed values: 12.04.5-LTS, 14.04.2-LTS, 15.04."
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
                "sku": "[parameters('ubuntuOSVersion')]",
                "version": "latest"
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


### Passaggio 2: Creare la macchina virtuale usando il modello

Dopo aver preparato i valori di parametro, è necessario creare un gruppo di risorse per la distribuzione del modello, quindi procedere con l'operazione.

Per creare il gruppo di risorse, digitare `azure group create <group name> <location>` con il nome del gruppo desiderato e la posizione del data center in cui si intende eseguire la distribuzione. Ciò si verifica rapidamente:

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

- Il file di modello (se il modello JSON precedente è stato salvato in un file locale).
- Un URI del modello (se si vuole fare riferimento al file in GitHub o a un altro indirizzo Web).
- Il gruppo di risorse in cui si vuole effettuare la distribuzione.
- Un nome di distribuzione facoltativo.

Verrà richiesto di fornire i valori dei parametri contenuti nella sezione "parameters" del file JSON. Dopo aver specificato tutti i valori dei parametri, verrà avviata la distribuzione.

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



## <a id="create-a-custom-vm-image"></a>Attività: Creare un'immagine di macchina virtuale personalizzata

Nelle sezioni precedenti è stato illustrato l'utilizzo di base dei modelli. È quindi possibile usare istruzioni simili per creare una macchina virtuale personalizzata da un file VHD in Azure usando un modello tramite l'interfaccia della riga di comando di Azure. La differenza è che questo modello consente di creare una singola macchina virtuale da un disco rigido virtuale (VHD) specificato.

### Passaggio 1: esaminare il file JSON per il modello

Ecco il contenuto del file JSON per il modello che viene usato come esempio in questa sezione.

Anche in questo caso, è necessario trovare i valori da immettere per i parametri privi di valori predefiniti. Quando si esegue il comando `azure group deployment create` dell'interfaccia della riga di comando di Azure, verrà richiesto di immettere tali valori.

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

Ovviamente, è necessario un file VHD per questa operazione. È possibile usarne uno già presente in Azure o caricarne uno.

Per una macchina virtuale basata su Windows, vedere [Creazione e caricamento di un disco rigido virtuale con Windows Server in Azure](../articles/virtual-machines/virtual-machines-windows-classic-createupload-vhd.md).

Per una macchina virtuale basata su Linux, vedere [Creazione e caricamento di un disco rigido virtuale contenente il sistema operativo Linux](../articles/virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md).

### Passaggio 3: Creare la macchina virtuale usando il modello

A questo punto è possibile creare una nuova macchina virtuale in base al file VHD. Creare un gruppo in cui eseguire la distribuzione, usando `azure group create <location>`:

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

Creare quindi la distribuzione usando l'opzione `--template-uri`, che consente di chiamare direttamente il modello. In alternativa, l'opzione `--template-file` consente di usare un file salvato in locale. Si noti che poiché nel modello sono specificati valori predefiniti, sarà necessario fornire solo poche informazioni. Se si distribuisce il modello in posizioni diverse, potrebbero verificarsi alcuni conflitti di denominazione con i valori predefiniti (in particolare per il nome DNS creato).

    azure group deployment create \
    > --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json \
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
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json
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


## <a id="deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer"></a>Attività: Distribuire un'applicazione per più macchine virtuali che usa una rete virtuale e un servizio di bilanciamento del carico esterno

Questo modello consente di creare due macchine virtuali in un servizio di bilanciamento del carico e configurare una regola per il bilanciamento del carico sulla porta 80. Questo modello distribuisce anche un account di archiviazione, una rete virtuale, l'indirizzo IP pubblico, il set di disponibilità e le interfacce di rete.

![](./media/virtual-machines-common-cli-deploy-templates/multivmextlb.png)

Seguire queste procedure per distribuire un'applicazione per più macchine virtuali che usa una rete virtuale e un servizio di bilanciamento del carico usando un modello di Gestione risorse nel repository dei modelli di GitHub tramite i comandi di Azure PowerShell.

### Passaggio 1: esaminare il file JSON per il modello

Di seguito il contenuto del file JSON per il modello. Se si desidera che la versione più recente, si trova [nel repository Github per i modelli](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json). In questo argomento viene utilizzato lo switch `--template-uri` per passare la chiamata del modello, ma è anche possibile utilizzare lo switch `--template-file` per passare a una versione locale.


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
                  "description": "Admin user name"
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
                  "description": "DNS for load balancer IP"
                }
            },
            "backendPort": {
                "type": "int",
                "defaultValue": 3389,
                "metadata": {
                  "description": "Back-end port"
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
                  "description": "Load balancer name"
                }
            },
            "nicNamePrefix": {
                "type": "string",
                "defaultValue": "nic",
                "metadata": {
                  "description": "Network interface name prefix"
                }
            },
            "publicIPAddressName": {
                "type": "string",
                "defaultValue": "myPublicIP",
                "metadata": {
                  "description": "Public IP name"
                }
            },
            "vnetName": {
                "type": "string",
                "defaultValue": "myVNET",
                "metadata": {
                  "description": "Virtual network name"
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

### Passaggio 2: Creare la distribuzione usando il modello

Creare un gruppo di risorse per il modello usando l'opzione `azure group create <location>`. Creare quindi una distribuzione nel gruppo di risorse usando `azure group deployment create` e passando il gruppo di risorse e un nome di distribuzione, nonché specificando i parametri del modello per cui non sono previsti valori predefiniti.


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


A questo punto, utilizzare il comando `azure group deployment create` e l'opzione `--template-uri` per distribuire il modello. Quando richiesto, specificare i valori dei parametri, come illustrato di seguito.

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

Si noti che questo modello distribuisce un'immagine di Windows Server. Questa immagine potrebbe essere tuttavia sostituita con qualsiasi immagine Linux. Si vuole creare un cluster Docker con più swarm manager? [Sì, è possibile](https://azure.microsoft.com/documentation/templates/docker-swarm-cluster/).

## <a id="remove-a-resource-group"></a>Attività: Rimuovere un gruppo di risorse

Tenere presente che in un gruppo di risorse è possibile eseguire una nuova distribuzione, tuttavia, se il gruppo di risorse non è più necessario, è possibile eliminarlo usando il comando `azure group delete <group name>`.

    azure group delete myResourceGroup
    info:    Executing command group delete
    Delete resource group myResourceGroup? [y/n] y
    + Deleting resource group myResourceGroup
    info:    group delete command OK

## <a id="show-the-log-for-a-resource-group-deployment"></a>Attività: Visualizzare il log per una distribuzione nel gruppo di risorse

Questa operazione è piuttosto comune durante la creazione o l'utilizzo di modelli. Il comando per visualizzare i log di distribuzione per un gruppo è `azure group log show <groupname>` e offre numerose informazioni utili per comprendere il motivo per cui si è verificato, o non si è verificato, un evento. Per altre informazioni sulla risoluzione dei problemi relativi alle distribuzioni o ad altri errori, vedere [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](../articles/resource-manager-troubleshoot-deployments-cli.md).

Per errori specifici ad esempio, è possibile utilizzare strumenti come **jq** per eseguire query in modo più preciso, ad esempio individuare i singoli errori che è necessario correggere. L'esempio seguente usa **jq** per analizzare un log di distribuzione per **lbgroup**, per trovare eventuali errori.

    azure group log show lbgroup -l --json | jq '.[] | select(.status.value == "Failed") | .properties'

È possibile individuare molto rapidamente la causa dell'errore, correggerlo e riprovare. Nel caso seguente con il modello sono state create due macchine virtuali contemporaneamente, che hanno generato un blocco sul file VHD. Dopo aver modificato il modello, la distribuzione è stata completata rapidamente.

    {
      "statusCode": "Conflict",
      "statusMessage": "{"status":"Failed","error":{"code":"ResourceDeploymentFailure","message":"The resource operation completed with terminal provisioning state 'Failed'.","details":[{"code":"AcquireDiskLeaseFailed","message":"Failed to acquire lease while creating disk 'osdisk' using blob with URI http://storage.blob.core.windows.net/vhds/osdisk.vhd."}]}}"
    }


## <a id="display-information-about-a-virtual-machine"></a>Attività: Visualizzare informazioni relative a una macchina virtuale

Per visualizzare informazioni sulle macchine virtuali specifiche nel gruppo di risorse in uso, usare il comando `azure vm show <groupname> <vmname> command`. Se nel gruppo sono presenti più macchine virtuali, potrebbe essere prima necessario elencare le macchine virtuali nel gruppo usando il comando `azure vm list <groupname>`.

    azure vm list zoo
    info:    Executing command vm list
    + Getting virtual machines
    data:    Name   ProvisioningState  Location  Size
    data:    -----  -----------------  --------  -----------
    data:    myVM0  Succeeded          westus    Standard_A1
    data:    myVM1  Failed             westus    Standard_A1

Selezionare quindi myVM1:

    azure vm show zoo myVM1
    info:    Executing command vm show
    + Looking up the VM "myVM1"
    + Looking up the NIC "nic1"
    data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/virtualMachines/myVM1
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
    data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Network/networkInterfaces/nic1
    data:          Primary                   :false
    data:          Provisioning State        :Succeeded
    data:          Name                      :nic1
    data:          Location                  :westus
    data:            Private IP alloc-method :Dynamic
    data:            Private IP address      :10.0.0.5
    data:
    data:    AvailabilitySet:
    data:      Id                            :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/availabilitySets/MYAVSET
    info:    vm show command OK


> [AZURE.NOTE] Per archiviare e modificare l'output dei comandi della console a livello di codice, è possibile usare uno strumento di analisi JSON, ad esempio **[jq](https://github.com/stedolan/jq)**, **[jsawk](https://github.com/micha/jsawk)** o le librerie dei linguaggi idonee per l'attività.

## <a id="log-on-to-a-linux-based-virtual-machine"></a>Attività: Accedere a una macchina virtuale basata su Linux

In genere macchine Linux sono connesse tramite SSH. Per altre informazioni, vedere la pagina relativa all'[Uso di SSH con Linux in Azure](../articles/virtual-machines/virtual-machines-linux-ssh-from-linux.md).

## <a id="stop-a-virtual-machine"></a>Attività: Arrestare una macchina virtuale

Eseguire questo comando:

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT] Utilizzare questo parametro per mantenere l'IP virtuale (VIP) del vnet, qualora fosse l'ultima macchina virtuale inclusa vnet. <br><br> Se si usa il parametro `StayProvisioned`, i costi per la macchina virtuale continueranno a essere addebitati.

## <a id="start-a-virtual-machine"></a>Attività: Avviare una macchina virtuale

Eseguire questo comando:

    azure vm start <group name> <virtual machine name>

## <a id="attach-a-data-disk"></a>Attività: Collegare un disco dati

È inoltre necessario decidere se collegare un nuovo disco o uno che contiene già dati. Per un nuovo disco, il comando permette di creare il file con estensione VHD e contemporaneamente di collegarlo.

Per collegare un nuovo disco, eseguire questo comando:

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb>

Per collegare un disco dati esistente, eseguire questo comando:

    azure vm disk attach <resource-group> <vm-name> [vhd-url]

Quindi è necessario montare il disco, come si farebbe normalmente in Linux (o in Windows).


## Passaggi successivi

Per altri esempi che mostrano come usare l'interfaccia della riga di comando di Azure nella modalità **Gestione risorse di Azure**, vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure.](../articles/xplat-cli-azure-resource-manager.md). Per altre informazioni sulle risorse di Azure e i relativi concetti, vedere [Panoramica di Gestione risorse di Microsoft Azure](../articles/resource-group-overview.md).


Per altri modelli disponibili, vedere gli articoli relativi ai [modelli della Guida introduttiva di Azure](https://azure.microsoft.com/documentation/templates/) e ai [framework applicazioni con modelli](../articles/virtual-machines/virtual-machines-linux-app-frameworks.md).

<!---HONumber=AcomDC_0330_2016-->