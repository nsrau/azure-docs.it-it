<properties
	pageTitle="Creare una VM con un modello di Resource Manager | Microsoft Azure"
	description="Usare un modello di Resource Manager e PowerShell per creare facilmente una nuova macchina virtuale Windows."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# Creare una macchina virtuale Windows con un modello di Gestione risorse

Questo articolo presenta un modello di Azure Resource Manager e illustra come usare PowerShell per distribuirlo. Il modello consente di distribuire una singola macchina virtuale che esegue Windows Server in una nuova rete virtuale con un'unica subnet.

L'esecuzione della procedura illustrata in questo articolo richiede circa 20 minuti.

## Passaggio 1: Creare il file del modello

È possibile creare un modello personalizzato usando le informazioni presenti in [Creazione di modelli di Azure Resource Manager](../resource-group-authoring-templates.md). È anche possibile distribuire i modelli creati automaticamente per l'utente partendo da [modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/). L'esempio illustrato in questo articolo è simile al modello descritto in [Distribuire una VM Windows semplice negli Stati Uniti occidentali](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows/).

1. Aprire l'editor di testo preferito e copiare le informazioni JSON in un nuovo file denominato *VirtualMachineTemplate.json*:

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": {
              "type": "string",
              "metadata": {
                "Description": "The name of the storage account where the VM disk is stored."
              }
            },
            "adminUsername": {
              "type": "string",
              "metadata": {
                "Description": "The name of the administrator account on the VM."
              }
            },
            "adminPassword": {
              "type": "securestring",
              "metadata": {
                "Description": "The administrator account password on the VM."
              }
            },
            "dnsNameForPublicIP": {
              "type": "string",
              "metadata": {
                "Description": "The name of the public IP address used to access the VM."
              }
            }
          },
          "variables": {
            "location": "Central US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
            "OSDiskName": "osdisk1",
            "nicName": "nc1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "sn1",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "ip1",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "vm1",
            "vmSize": "Standard_A0",
            "virtualNetworkName": "vn1",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "apiVersion": "2015-06-15",
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
              "apiVersion": "2016-03-30",
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
                  "computername": "[variables('vmName')]",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "[variables('imagePublisher')]",
                    "offer": "[variables('imageOffer')]",
                    "sku" : "[variables('windowsOSVersion')]",
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
        
    >[AZURE.NOTE] Questo articolo crea una macchina virtuale che esegue una versione del sistema operativo Windows Server. Per altre informazioni sulla selezione di altre immagini, vedere [Esplorare e selezionare immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-ps-findimage.md).
    
2. Salvare il file di modello.

## Passaggio 2: Creare il file dei parametri

Per specificare i valori per i parametri delle risorse definiti nel modello, creare un file dei parametri che include i valori e inviarlo a Gestione risorse insieme al modello.

1. Nell'editor di testo copiare queste informazioni JSON in un nuovo file denominato *Parameters.json*:

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

4. Salvare il file dei parametri.

## Passaggio 3: Installare Azure PowerShell

Vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione che si vuole usare e accedere all'account Azure.

## Passaggio 4: Creare un gruppo di risorse

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

### Passaggio 7: Creare le risorse con il modello e i parametri

1. Sostituire il valore di **$deployName** con il nome della distribuzione. Sostituire il valore di **$templatePath** con il percorso e il nome del file del modello. Sostituire il valore di**$parameterFile** con il percorso e il nome del file dei parametri. Creare le variabili. 

        $deployName="deployment name"
        $templatePath = "template path"
        $parameterFile = "parameter file"

4. Distribuire il modello.

        New-AzureRmResourceGroupDeployment -ResourceGroupName "davidmurg6" -TemplateFile $templatePath -TemplateParameterFile $parameterFile

    Verrà visualizzata una schermata simile alla seguente:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            newStorageAccountName  String                     mytestsa1
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString
                            dnsNameForPublicIP  String                     mytestdns1

        Outputs           :

    >[AZURE.NOTE] È inoltre possibile distribuire i modelli e i parametri da un account di Archiviazione di Azure. Per altre informazioni, vedere [Uso di Azure PowerShell con Archiviazione di Azure](../storage-powershell-guide-full.md)

## Passaggi successivi

- Se si sono verificati problemi con la distribuzione, è consigliabile leggere [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Per informazioni su come gestire la macchina virtuale appena creata, vedere [Gestire macchine virtuali con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0420_2016-->