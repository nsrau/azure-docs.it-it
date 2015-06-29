<properties 
	pageTitle="Distribuire e gestire le macchine virtuali di Microsoft Azure utilizzando modelli di Gestione risorse e PowerShell" 
	description="È possibile distribuire il set più comune di configurazioni di macchine virtuali di Azure e gestirle facilmente mediante modelli di Gestione risorse e PowerShell." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="josephd"/>

# Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e PowerShell

Questo articolo illustra come usare i modelli di Gestione risorse di Azure e PowerShell per automatizzare le attività comuni per la distribuzione e la gestione delle macchine virtuali di Azure. Per ulteriori modelli da poter utilizzare, vedere [Modelli di avvio rapido di Azure](http://azure.microsoft.com/documentation/templates/) e [App Framework](virtual-machines-app-frameworks.md).

- [Distribuire una macchina virtuale Windows](#windowsvm)
- [Creare un’immagine di macchina virtuale personalizzata](#customvm)
- [Distribuire un'applicazione per più macchine virtuali che utilizza una rete virtuale e un servizio di bilanciamento del carico esterno](#multivm)
- [Aggiornare una macchina virtuale distribuita con un modello di Gestione risorse](#updatevm)
- [Rimuovere un gruppo di risorse](#removerg)
- [Accedere a una macchina virtuale](#logon)
- [Visualizzare informazioni relative a una macchina virtuale](#displayvm)
- [Avviare una macchina virtuale](#start)
- [Arrestare una macchina virtuale](#stop)
- [Riavviare una macchina virtuale](#restart)
- [Eliminare una macchina virtuale](#delete)

Prima di iniziare, assicurarsi che Azure PowerShell sia pronto all’uso.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## Informazioni sui modelli di risorse di Azure e sui gruppi di risorse

La maggior parte delle applicazioni distribuite ed eseguite da Microsoft Azure viene creata da una combinazione di tipi diversi di risorse cloud \(ad esempio una o più macchine virtuali e account di archiviazione, un database SQ o una rete virtuale\). I modelli di Gestione risorse di Azure consentono di distribuire e gestire insieme queste risorse diverse tra loro tramite una descrizione JSON delle risorse e parametri di configurazione e distribuzione associati.

Dopo aver definito un modello di risorsa basata su JSON, è possibile eseguirla e sono le risorse definito all'interno di distribuirlo in Azure utilizzando un comando di PowerShell. È possibile eseguire questi comandi in modo autonomo nell’ambito della shell dei comandi PowerShell o integrandoli all'interno di uno script che contenga la logica di automazione aggiuntiva.

Le risorse create mediante i modelli di Gestione risorse di Azure verranno distribuite in un gruppo di risorse Azure nuovo o esistente. Un *gruppo di risorse Azure* consente di gestire più risorse distribuite insieme come gruppo logico: in questo modo è possibile gestire il ciclo di vita complessivo del gruppo o dell’applicazione e fornire le API di gestione che consentono quanto segue:

- Arrestare, avviare o eliminare tutte le risorse all'interno del gruppo contemporaneamente. 
- Applicare le regole di controllo degli accessi in base al ruolo \(RBAC\) per bloccare le autorizzazioni di sicurezza su di essi. 
- Controllare le operazioni. 
- Contrassegnare le risorse con metadati aggiuntivi per una gestione più efficiente. 

Ulteriori informazioni su Gestione risorse di Azure sono disponibili [qui](virtual-machines-azurerm-versus-azuresm.md). Se si è interessati alla creazione di modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).

## <a id="windowsvm"></a>ATTIVITÀ: Distribuire una macchina virtuale Windows

Utilizzare le istruzioni riportate in questa sezione per distribuire una nuova macchina virtuale di Azure utilizzando un modello di Gestione risorse e Azure PowerShell. Questo modello consente di creare una singola macchina virtuale in una nuova rete virtuale con una sola subnet.

![](./media/virtual-machines-deploy-rmtemplates-powershell/windowsvm.png)
 
Attenersi alla seguente procedura per creare una macchina virtuale Windows utilizzando un modello di Gestione risorse nell'archivio dei modelli Github con Microsoft Azure PowerShell.

### Passaggio 1: esaminare il file JSON per il modello.

Di seguito il contenuto del file JSON per il modello.

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
               "Description": "Username for the Virtual Machine."
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
        "windowsOSVersion": {
            "type": "string",
            "defaultValue": "2012-R2-Datacenter",
            "allowedValues": [
                "2008-R2-SP1", 
                "2012-Datacenter", 
                "2012-R2-Datacenter", 
                "Windows-Server-Technical-Preview"
            ],
            "metadata": {
                "Description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
            }
        }
    },
    "variables": {
        "location": "West US",
        "imagePublisher": "MicrosoftWindowsServer", 
        "imageOffer": "WindowsServer", 
        "OSDiskName": "osdiskforwindowssimple",
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16", 
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "storageAccountType": "Standard_LRS",
        "publicIPAddressName": "myPublicIP",
        "publicIPAddressType": "Dynamic",
        "vmStorageAccountContainerName": "vhds",
        "vmName": "MyWindowsVM",
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
                        "sku" : "[parameters('windowsOSVersion')]",
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


### Passaggio 2: creare la macchina virtuale con il modello.

Inserire un nome per la distribuzione di Microsoft Azure, il nome gruppo di risorse e la posizione del data center di Microsoft Azure, quindi eseguire questi comandi.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Quando si esegue il comando **New-AzureResourceGroupDeployment**, verrà richiesto di fornire i valori dei parametri riportati nella sezione "parameters" del file JSON. Dopo aver specificato tutti i valori dei parametri necessari, il comando crea il gruppo di risorse e la macchina virtuale.

Di seguito è fornito un esempio.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Verrà visualizzata una schermata simile alla seguente:

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: newsaacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso
	VERBOSE: 10:56:59 AM - Template is valid.
	VERBOSE: 10:56:59 AM - Create template deployment 'TestDeployment'.
	VERBOSE: 10:57:08 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 10:57:11 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 10:57:11 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is running
	VERBOSE: 10:57:38 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is succeeded
	VERBOSE: 10:57:40 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 10:57:45 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 10:57:45 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:01:59 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded
	
	
	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 6:02:13 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     newsaacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

È ora disponibile una nuova macchina virtuale Windows denominata MyWindowsVM nel nuovo gruppo di risorse.

## <a id="customvm"></a>ATTIVITÀ: Creare un'immagine di macchina virtuale personalizzata

Utilizzare le istruzioni incluse in questa sezione per creare un'immagine di macchina virtuale personalizzata in Azure con un modello di Gestione risorse utilizzato in Azure PowerShell. Questo modello consente di creare una singola macchina virtuale da un disco rigido virtuale \(VHD\) specificato.

### Passaggio 1: esaminare il file JSON per il modello.

Di seguito il contenuto del file JSON per il modello.

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	    "contentVersion": "1.0.0.0",
	    "parameters": {
	        "osDiskVhdUri": {
	            "type": "string",
	            "metadata": {
	                "Description": "Uri of the existing VHD"
	            }
	        },
	        "osType": {
	            "type": "string",
	            "allowedValues": [
	                "windows",
	                "linux"
	            ],
	            "metadata": {
	                "Description": "Type of OS on the existing vhd"
	            }
	        },
	        "location": {
	            "type": "String",
	            "defaultValue": "West US",
	            "metadata": {
	                "Description": "Location to create the VM in"
	            }
	        },
	        "vmSize": {
	            "type": "string",
	            "defaultValue": "Standard_A2",
	            "metadata": {
	                "Description": "Size of the VM"
	            }
	        },
	        "vmName": {
	            "type": "string",
	            "defaultValue": "myVM",
	            "metadata": {
	                "Description": "Name of the VM"
	            }
	        },
	        "nicName": {
	            "type": "string",
	            "defaultValue": "myNIC",
	            "metadata": {
	                "Description": "NIC to attach the new VM to"
	            }
	        }
	    },
	    "resources": [{
	        "apiVersion": "2014-12-01-preview",
	        "type": "Microsoft.Compute/virtualMachines",
	        "name": "[parameters('vmName')]",
	        "location": "[parameters('location')]",
	        "properties": {
	            "hardwareProfile": {
	                "vmSize": "[parameters('vmSize')]"
	            },
	            "storageProfile": {
	                "osDisk": {
	                    "name": "[concat(parameters('vmName'),'-osDisk')]",
	                    "osType": "[parameters('osType')]",
	                    "caching": "ReadWrite",
	                    "vhd": {
	                        "uri": "[parameters('osDiskVhdUri')]"
	                    }
	                }
	            },
	            "networkProfile": {
	                "networkInterfaces": [{
	                    "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
	                }]
	            }
	        }
	    }]
	}

### Passaggio 2: ottenere il disco rigido virtuale.

Per una macchina virtuale basata su Windows, vedere [Creazione e caricamento di un disco rigido virtuale con Windows Server in Azure](virtual-machines-create-upload-vhd-windows-server.md).

Per una macchina virtuale basata su Linux, vedere [Creazione e caricamento di un disco rigido virtuale con Linux in Azure](virtual-machines-linux-create-upload-vhd.md).

### Passaggio 3: creare la macchina virtuale con il modello.

Per creare una nuova macchina virtuale basata sul disco rigido virtuale, sostituire gli elementi all'interno dei segni di minore/maggiore \("\< \>"\) con le informazioni specifiche ed eseguire questi comandi:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Verrà richiesto di fornire i valori dei parametri contenuti nella sezione "parameters" del file JSON. Dopo aver specificato tutti i valori dei parametri, Gestione risorse di Azure crea il gruppo di risorse e la macchina virtuale.

Di seguito è fornito un esempio:

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI


Verrà visualizzato il tipo di informazioni seguente:

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	osDiskVhdUri: http://saacct.blob.core.windows.net/vhds/osdiskforwindows.vhd
	osType: windows
	location: West US
	vmSize: Standard_A3
	...

## <a id="multivm"></a>ATTIVITÀ: Distribuire un'applicazione per più macchine virtuali che usa una rete virtuale e un servizio di bilanciamento del carico esterno

Attenersi alle istruzioni contenute in queste sezioni per distribuire un'applicazione per più macchine virtuali che utilizza una rete virtuale e un servizio di bilanciamento del carico mediante un modello di Gestione risorse tramite PowerShell di Azure. Questo modello consente di creare due macchine virtuali in una nuova rete virtuale con una singola subnet in un nuovo servizio cloud e di aggiungerle a un set con carico bilanciato esterno per il traffico in ingresso alla porta TCP 80.

![](./media/virtual-machines-deploy-rmtemplates-powershell/multivmextlb.png)

Seguire questi passaggi per distribuire un'applicazione per più macchine virtuali che utilizza una rete virtuale e un servizio di bilanciamento del carico mediante un modello di Gestione risorse nell'archivio dei modelli Github tramite i comandi PowerShell di Azure.

### Passaggio 1: esaminare il file JSON per il modello.

Di seguito il contenuto del file JSON per il modello.

	{
	"$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	    "contentVersion": "1.0.0.0",
    "parameters": {
        "region": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string",
            "defaultValue": "uniqueStorageAccountName"
        },
        "adminUsername": {
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring"
        },
        "dnsNameforLBIP": {
            "type": "string",
            "defaultValue": "uniqueDnsNameforLBIP"
        },
        "backendPort": {
            "type": "int",
            "defaultValue": 3389
        },
        "vmNamePrefix": {
            "type": "string",
            "defaultValue": "myVM"
        },
        "vmSourceImageName": {
            "type": "string",
            "defaultValue": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"
        },
        "lbName": {
            "type": "string",
            "defaultValue": "myLB"
        },
        "nicNamePrefix": {
            "type": "string",
            "defaultValue": "nic"
        },
        "publicIPAddressName": {
            "type": "string",
            "defaultValue": "myPublicIP"
        },
        "vnetName": {
            "type": "string",
            "defaultValue": "myVNET"
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_A1",
            "allowedValues": [
                "Standard_A0",
                "Standard_A1",
                "Standard_A2",
                "Standard_A3",
                "Standard_A4"
            ]
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
            "apiVersion": "2014-12-01-preview",
            "location": "[parameters('region')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            "apiVersion": "2014-12-01-preview",
            "location": "[parameters('region')]",
            "properties": {}
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('region')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameforLBIP')]"
                }
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('region')]",
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
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
            "location": "[parameters('region')]",
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
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "name": "[parameters('lbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('region')]",
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
                        "name": "LBBE",
                        "properties": {
                            "backendIPConfigurations": [
                                {
                                    "id": "[variables('backEndIPConfigID1')]"
                                },
                                {
                                    "id": "[variables('backEndIPConfigID2')]"
                                }
                            ]
                        }
                    }
                ],
                "inboundNatRules": [
                    {
                        "name": "RDP-VM1",
                        "properties": {
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
                            "backendIPConfiguration": {
                                "id": "[variables('backEndIPConfigID1')]"
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
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
                            "backendIPConfiguration": {
                                "id": "[variables('backEndIPConfigID2')]"
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
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
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
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
            "copy": {
                "name": "virtualMachineLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "location": "[parameters('region')]",
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

Inserire un nome per la distribuzione di Microsoft Azure, il nome gruppo di risorse e la posizione di Microsoft Azure, quindi eseguire questi comandi.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Quando si esegue il comando New-AzureResourceGroupDeployment, verrà richiesto di fornire i valori dei parametri del file JSON. Dopo aver specificato tutti i valori dei parametri, il comando crea il gruppo di risorse e la distribuzione.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Dovrebbe essere visualizzata una schermata analoga alla seguente:

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	adminUserName: WebAdmin1
	adminPassword: *******
	dnsNameforLBIP: web07
	backendPort: 80
	vmNamePrefix: WEBFARM
	...

## <a id="updatevm"></a>ATTIVITÀ: Aggiornare una macchina virtuale distribuita con un modello di Gestione risorse

Di seguito è riportato un esempio di modifica di un file modello JSON per aggiornare la configurazione di una macchina virtuale distribuita con un modello di Gestione risorse. Questo esempio crea una macchina virtuale Windows e quindi la aggiorna per installare l'estensione di Symantec Endpoint Protection.

### Passaggio 1: Creare la macchina virtuale con un modello

Se necessario, creare una cartella nel computer per archiviare i file modello. Immettere il nome della cartella ed eseguire questi comandi di Azure PowerShell.

	$myFolder="<your folder path, such as C:\azure\templates\CreateVM>"
	$webClient=New-Object System.Net.WebClient
	$url="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	$filePath=$myFolder + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.parameters.json"
	$filePath = $myFolder + "\azuredeploy.parameters.json"
	$webclient.DownloadFile($url,$filePath)

In un editor di testo aprire il file azuredeploy.parameters.json che si trova nella cartella, specificare i valori dei quattro parametri e quindi salvare il file.

Immettere un nuovo nome per la distribuzione, un nuovo nome del gruppo di risorse e una posizione di Azure, quindi eseguire questi comandi.

	$deployName="<name for the new deployment>"
	$RGName="<name for the new Resource Group>"
	$locName="<an Azure location, such as West US>"
	cd $myFolder
	Switch-AzureMode AzureResourceManager
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json

Dovrebbe essere visualizzata una schermata analoga alla seguente.

	PS C:\azure\templates\windowsvm> $deployName="winvmexttest"
	PS C:\azure\templates\windowsvm> $RGName="winvmexttest"
	PS C:\azure\templates\windowsvm> $locname="West US"
	PS C:\azure\templates\windowsvm> New-AzureResourceGroup -Name $RGName -Location $locName
	VERBOSE: 11:22:02 AM - Created resource group 'winvmexttest' in location 'westus'
	
	
	ResourceGroupName : winvmexttest
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
	                    Actions  NotActions
	                    =======  ==========
	                    *
	
	ResourceId        : /subscriptions/a58ce54a-c262-460f-b8ef-fe36e6d5f5ec/resourceGroups/winvmexttest

	PS C:\azure\templates\windowsvm> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -Template
	File azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
	VERBOSE: 11:22:05 AM - Template is valid.
	VERBOSE: 11:22:05 AM - Create template deployment 'winvmexttest'.
	VERBOSE: 11:22:14 AM - Resource Microsoft.Storage/storageAccounts 'contososa' provisioning status is running
	VERBOSE: 11:22:21 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 11:22:21 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is running
	VERBOSE: 11:22:37 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 11:22:39 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 11:22:41 AM - Resource Microsoft.Storage/storageAccounts 'contososa' provisioning status is succeeded
	VERBOSE: 11:22:43 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:22:52 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 11:26:36 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded
	
	DeploymentName    : winvmexttest
	ResourceGroupName : winvmexttest
	ProvisioningState : Succeeded
	Timestamp         : 6/3/2015 6:26:38 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	                    Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     contososa
	                    adminUsername    String                     admin0987
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contosovm
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

Connettere quindi la macchina virtuale dal portale di anteprima di Azure \(\*\*Sfoglia \> Macchine virtuali \(v2\) \>\*\* *Nome VM* **\> Connetti**\).

Nella schermata Start digitare **Symantec**. I componenti di Symantec Endpoint Protection non sono installati, quindi non verranno restituiti risultati della ricerca con "Symantec" nel titolo.

Chiudere la connessione Desktop remoto.

### Passaggio 2 Modificare il file azuredeploy.json per aggiungere l'estensione di Symantec Endpoint Protection

Nell'editor di testo preferito aprire il file azuredeploy.json che si trova nella cartella. Nella sezione **variables** aggiungere la riga seguente subito dopo la riga che definisce la variabile publicIPAddressType:

	"vmExtensionName" : "SymantecExtension",

Nella sezione **resources** aggiungere la nuova sezione seguente subito prima della riga con l'ultima parentesi quadra di chiusura "\]":

	       {
	         "type": "Microsoft.Compute/virtualMachines/extensions",
	        "name": "[concat(variables('vmName'),'/', variables('vmExtensionName'))]",
	        "apiVersion": "2014-12-01-preview",
	        "location": "[variables('location')]",
	        "dependsOn": [
	            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
	        ],
	        "properties": {
	            "publisher": "Symantec",
	            "type": "SymantecEndpointProtection",
	            "typeHandlerVersion": "12.1",
	            "settings": null,
	            "protectedSettings": null
	        }
	    }

Salvare il file azuredeploy.json con le nuove modifiche. Verificare che le modifiche siano state apportate correttamente usando questo comando.

	Test-AzureResourceGroupTemplate -ResourceGroupName $RGName -TemplateFile azuredeploy.json

Se le modifiche sono state applicate nel modo corretto, dovrebbe essere visualizzato l'output seguente.

	Template is valid.

In caso contrario, analizzare il messaggio di errore per individuare l'origine dell'errore.

### Passaggio 3: Eseguire il modello modificato per aggiungere l'estensione di Symantec Endpoint Protection

Eseguire questo comando al prompt di Azure PowerShell.

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json

Dovrebbe essere visualizzata una schermata analoga alla seguente.

	PS C:\azure\templates\winvmext> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateF	ile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
	VERBOSE: 12:49:42 PM - Template is valid.
	VERBOSE: 12:49:42 PM - Create template deployment 'winvmexttest'.
	VERBOSE: 12:49:45 PM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 12:49:45 PM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 12:49:47 PM - Resource Microsoft.Storage/storageAccounts 'contososa' provisioning status is succeeded
	VERBOSE: 12:49:49 PM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 12:49:51 PM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 12:50:08 PM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded
	VERBOSE: 12:50:15 PM - Resource Microsoft.Compute/virtualMachines/extensions 'MyWindowsVM/SymantecExtension'	provisioning status is running
	VERBOSE: 12:53:07 PM - Resource Microsoft.Compute/virtualMachines/extensions 'MyWindowsVM/SymantecExtension' provisioning status is succeeded
	
	
	DeploymentName    : winvmexttest
	ResourceGroupName : winvmexttest
	ProvisioningState : Succeeded
	Timestamp         : 6/3/2015 7:53:07 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	                    Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     contososa
	                    adminUsername    String                     admin0987
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contosovm
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

Connettere la macchina virtuale dal portale di anteprima di Azure \(\*\*Sfoglia \> Macchine virtuali \(v2\) \>\*\* *Nome VM* **\> Connetti**\).

Nella schermata Start digitare **Symantec**. Dovrebbe essere visualizzata una schermata analoga alla seguente, dove è indicato che l'estensione di Symantec Endpoint Protection è installata.

![](./media/virtual-machines-deploy-rmtemplates-powershell/SymantecExt.png)

## <a id="removerg"></a>ATTIVITÀ: Rimuovere un gruppo di risorse

È possibile rimuovere qualsiasi gruppo di risorse è stato creato con il comando **Remove-AzureResourceGroup**. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con il nome corretto.

	Remove-AzureResourceGroup  -Name "<resource group name>"

Verranno visualizzate informazioni come segue:

	Confirm
	Are you sure you want to remove resource group 'BuildRG'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

## <a id="logon"></a>ATTIVITÀ: Accedere a una macchina virtuale di Windows

Nel [portale di anteprima di Azure](https://portal.azure.com/) fare clic su **Sfoglia tutto \> Macchine virtuali \(v2\) \>** *Nome VM* **\> Connetti**.

Quando viene richiesto di aprire o salvare un file RDP, fare clic su **Apri** e quindi su **Connetti**. Digitare le credenziali di un account valido e quindi fare clic su **OK**.

Quando viene richiesto di connettersi nonostante gli errori del certificato, fare clic su **Sì**.

## <a id="displayvm"></a>ATTIVITÀ: Visualizzare informazioni relative a una macchina virtuale

È possibile visualizzare informazioni sull'utilizzo di una macchina virtuale mediante il comando **Get-AzureVM**. Questo comando restituisce un oggetto macchina virtuale che può essere modificato utilizzando vari altri cmdlet per aggiornare lo stato della macchina virtuale. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	Get-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Verranno visualizzate informazioni sulla macchina virtuale come segue:

	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          : {
	                             "VirtualMachineSize": "Standard_D1"
	                           }
	Id                       : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/BuildRG/providers/Microso
	                           ft.Compute/virtualMachines/MyWindowsVM
	InstanceView             : null
	Location                 : westus
	Name                     : MyWindowsVM
	NetworkProfile           : {
	                             "NetworkInterfaces": [
	                               {
	                                 "Primary": null,
	                                 "ReferenceUri": "/subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/Bu
	                           ildRG/providers/Microsoft.Network/networkInterfaces/myVMNic"
	                               }
	                             ]
	                           }
	OSProfile                : {
	                             "AdminPassword": null,
	                             "AdminUsername": "WinAdmin1",
	                             "ComputerName": "MyWindowsVM",
	                             "CustomData": null,
	                             "LinuxConfiguration": null,
	                             "Secrets": [],
	                             "WindowsConfiguration": {
	                               "AdditionalUnattendContents": [],
	                               "EnableAutomaticUpdates": true,
	                               "ProvisionVMAgent": true,
	                               "TimeZone": null,
	                               "WinRMConfiguration": null
	                             }
	                           }
	Plan                     : null
	ProvisioningState        : Succeeded
	StorageProfile           : {
	                             "DataDisks": [],
	                             "ImageReference": {
	                               "Offer": "WindowsServer",
	                               "Publisher": "MicrosoftWindowsServer",
	                               "Sku": "2012-R2-Datacenter",
	                               "Version": "latest"
	                             },
	                             "OSDisk": {
	                               "OperatingSystemType": "Windows",
	                               "Caching": "ReadWrite",
	                               "CreateOption": "FromImage",
	                               "Name": "osdisk",
	                               "SourceImage": null,
	                               "VirtualHardDisk": {
	                                 "Uri": "http://buildsaacct.blob.core.windows.net/vhds/osdiskforwindowssimple.vhd"
	                               }
	                             },
	                             "SourceImage": null
	                           }
	Tags                     : {}
	Type                     : Microsoft.Compute/virtualMachines


## <a id="start"></a>ATTIVITÀ: Avviare una macchina virtuale

È possibile avviare una macchina virtuale mediante il comando **Start-AzureVM**. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	Start-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Verranno visualizzate informazioni come segue:

	EndTime             : 4/28/2015 11:11:41 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	Status              : Succeeded
	TrackingOperationId : e1705973-d266-467e-8655-920016145347
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	StatusCode          : OK

## <a id="stop"></a>ATTIVITÀ: Arrestare una macchina virtuale

È possibile arrestare una macchina virtuale utilizzando il comando **Stop-AzureVM**. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	Stop-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Verranno visualizzate informazioni come segue:

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
	
	
	EndTime             : 4/28/2015 11:09:08 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:06:55 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 0c94dc74-c553-412c-a187-108bdb29657e
	RequestId           : 5cc9ddba-0643-4b5e-82b6-287b321394ee
	StatusCode          : OK

## <a id=restart"></a>ATTIVITÀ: Riavviare una macchina virtuale

È possibile riavviare una macchina virtuale mediante il comando **Restart-AzureVM**. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con il nome corretto.

	Restart-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Verranno visualizzate informazioni come segue:

	EndTime             : 4/28/2015 11:16:26 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:16:25 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 390571e0-c804-43ce-88c5-f98e0feb588e
	RequestId           : 7dac33e3-0164-4a08-be33-96205284cb0b
	StatusCode          : OK

## <a id=delete"></a>ATTIVITÀ: Eliminare una macchina virtuale

È possibile eliminare una macchina virtuale utilizzando il comando **Remove-AzureVM**. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con il nome corretto. È possibile utilizzare il parametro **-Force** per ignorare la richiesta di conferma.

	Remove-AzureVM -ResourceGroupName "<resource group name>" –Name "<VM name>"

Verranno visualizzate informazioni come segue:

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
	
	
	EndTime             : 4/28/2015 11:21:55 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:20:13 AM -07:00
	Status              : Succeeded
	TrackingOperationId : f74fad9e-f6bc-46ae-82b1-bfad3952aa44
	RequestId           : 6a30d2e0-63ca-43cf-975b-058631e048e7
	StatusCode          : OK

## Risorse aggiuntive

[Provider di calcolo, rete e archiviazione in Gestione risorse di Microsoft Azure](virtual-machines-azurerm-versus-azuresm.md)

[Panoramica di Gestione risorse di Microsoft Azure](../resource-group-overview.md)

[Distribuire e gestire le macchine virtuali utilizzando modelli di Gestione risorse di Azure e l’interfaccia della riga di comando di Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Documentazione delle macchine virtuali](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Come installare e configurare Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=58_postMigration-->