---
title: Creare una VM con un modello di Resource Manager | Microsoft Docs
description: Usare un modello di Resource Manager e PowerShell per creare facilmente una nuova macchina virtuale Windows.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/06/2016
ms.author: davidmu

---
# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>Creare una macchina virtuale Windows con un modello di Gestione risorse
Questo articolo presenta un modello di Azure Resource Manager e illustra come usare PowerShell per distribuirlo. Il modello consente di distribuire una singola macchina virtuale che esegue Windows Server in una nuova rete virtuale con un'unica subnet.

L'esecuzione della procedura illustrata in questo articolo richiede circa 20 minuti.

> [!IMPORTANT]
> Per inserire la VM in un set di disponibilità, aggiungerla in fase di creazione. Attualmente non è possibile aggiungere una VM a un set di disponibilità dopo la sua creazione.
> 
> 

## <a name="step-1:-create-the-template-file"></a>Passaggio 1: Creare il file del modello
È possibile creare un modello personalizzato usando le informazioni presenti in [Creazione di modelli di Azure Resource Manager](../resource-group-authoring-templates.md). È anche possibile distribuire i modelli creati automaticamente per l'utente partendo da [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).

1. Aprire un editor di testo e aggiungere l'elemento schema e l'elemento contentVersion richiesti:
   
        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
        }
2. I [parametri](../resource-group-authoring-templates.md#parameters) non sono sempre necessari, ma consentono di immettere i valori quando il modello viene distribuito. Aggiungere l'elemento parameters e i rispettivi elementi figlio dopo l'elemento contentVersion:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }
3. [variabili](../resource-group-authoring-templates.md#variables) possono essere usate in un modello per specificare i valori che possono subire modifiche frequenti o che devono essere creati da una combinazione di valori dei parametri. Aggiungere l'elemento variables dopo la sezione parameters:
   
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
4. [risorse](../resource-group-authoring-templates.md#resources) , ad esempio la macchina virtuale, la rete virtuale e l'account di archiviazione, vengono definite più avanti nel modello. Aggiungere la sezione resources dopo la sezione variables:
   
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
   
   > [!NOTE]
   > Questo articolo crea una macchina virtuale che esegue una versione del sistema operativo Windows Server. Per altre informazioni sulla selezione di altre immagini, vedere [Esplorare e selezionare immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-ps-findimage.md).  
   > 
   > 
5. Salvare il file modello con il nome *VirtualMachineTemplate.json*.

## <a name="step-2:-create-the-parameters-file"></a>Passaggio 2: Creare il file dei parametri
Per specificare i valori per i parametri delle risorse definiti nel modello, creare un file dei parametri contenente i valori usati nella distribuzione del modello.

1. Nell'editor di testo copiare questo contenuto JSON in un nuovo file denominato *Parameters.json*:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }
   
   > [!NOTE]
   > Sono disponibili altre informazioni sui [requisiti relativi a nome utente e password](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).
   > 
   > 
2. Salvare il file dei parametri.

## <a name="step-3:-install-azure-powershell"></a>Passaggio 3: Installare Azure PowerShell
Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## <a name="step-4:-create-a-resource-group"></a>Passaggio 4: Creare un gruppo di risorse
Tutte le risorse devono essere distribuite in un [gruppo di risorse](../resource-group-overview.md).

1. Ottenere un elenco di località disponibili in cui possono essere create le risorse.
   
        Get-AzureRmLocation | sort DisplayName | Select DisplayName
2. Sostituire il valore di **$locName** con una località dell'elenco, ad esempio **Stati Uniti centrali**. Creare la variabile.
   
        $locName = "location name"
3. Sostituire il valore di **$rgName** con il nome del nuovo gruppo di risorse. Creare la variabile e il gruppo di risorse.
   
        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
   
    L'output sarà simile all'esempio seguente:
   
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

## <a name="step-5:-create-the-resources-with-the-template-and-parameters"></a>Passaggio 5: Creare le risorse con il modello e i parametri
Sostituire il valore di **$templateFile** con il percorso e il nome del file del modello. Sostituire il valore di **$parameterFile** con il percorso e il nome del file dei parametri. Creare le variabili e quindi distribuire il modello. 

        $templateFile = "template file"
        $parameterFile = "parameter file"
        New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile -TemplateParameterFile $parameterFile

    You will see something like this:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString

        Outputs           :

> [!NOTE]
> È inoltre possibile distribuire i modelli e i parametri da un account di Archiviazione di Azure. Per altre informazioni, vedere [Uso di Azure PowerShell con Archiviazione di Azure](../storage/storage-powershell-guide-full.md).
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* Se si sono verificati problemi con la distribuzione, è consigliabile vedere come [risolvere i problemi della distribuzione di gruppi di risorse con il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md)
* Per informazioni su come gestire la macchina virtuale creata, vedere [Gestire macchine virtuali con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md).

<!--HONumber=Oct16_HO2-->


