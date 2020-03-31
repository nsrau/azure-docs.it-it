---
title: Creare una macchina virtuale in DevTest Labs con Azure PowerShellCreate a virtual machine in DevTest Labs with Azure PowerShell
description: Informazioni su come usare Azure DevTest Labs per creare e gestire macchine virtuali con Azure PowerShell.Learn how to use Azure DevTest Labs to create and manage virtual machines with Azure PowerShell.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 13014c39641203bddadf858c34cff67462b3a4b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167100"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Creare una macchina virtuale con DevTest Labs usando Azure PowerShellCreate a virtual machine with DevTest Labs using Azure PowerShell
Questo articolo illustra come creare una macchina virtuale in Azure DevTest Labs usando Azure PowerShell.This article shows you how to create a virtual machine in Azure DevTest Labs by using Azure PowerShell. È possibile usare script di PowerShell per automatizzare la creazione di macchine virtuali in un lab in Lab di Azure DevTest.You can use PowerShell scripts to automate creation of virtual machines in a lab in Azure DevTest Labs. 

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare:

- [Creare un lab](devtest-lab-create-lab.md) se non si vuole usare un lab esistente per testare lo script o i comandi in questo articolo. 
- [Installare Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0) o usare Azure Cloud Shell integrato nel portale di Azure.Install Azure PowerShell or use Azure Cloud Shell that's integrated into the Azure portal. 

## <a name="powershell-script"></a>Script di PowerShell
Lo script di esempio in questa sezione utilizza il cmdlet [Invoke-AzResourceAction.](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0)  Questo cmdlet accetta l'ID risorsa del lab,`createEnvironment`il nome dell'azione da eseguire ( ) e i parametri necessari per eseguire tale azione. I parametri si trovano in una tabella hash che contiene tutte le proprietà di descrizione della macchina virtuale. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

Le proprietà per la macchina virtuale nello script precedente consentono di creare una macchina virtuale con DataCenter di Windows Server 2016 come sistema operativo. Per ogni tipo di macchina virtuale, queste proprietà saranno leggermente diverse. Nella sezione [Definire macchina virtuale](#define-virtual-machine) viene illustrato come determinare le proprietà da utilizzare in questo script.

Il comando seguente fornisce un esempio di esecuzione dello script salvato in un nome di file: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definire la macchina virtualeDefine virtual machine
In questa sezione viene illustrato come ottenere le proprietà specifiche di un tipo di macchina virtuale che si desidera creare. 

### <a name="use-azure-portal"></a>Usare il portale di Azure
È possibile generare un modello di Azure Resource Manager durante la creazione di una macchina virtuale nel portale di Azure.You can generate an Azure Resource Manager template when creating a VM in the Azure portal. Non è necessario completare il processo di creazione della macchina virtuale. La procedura viene eseguita solo fino a quando non viene visualizzato il modello. Questo è il modo migliore per ottenere la descrizione JSON necessaria se non è già stata creata una macchina virtuale del lab. 

1. Passare al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** nel menu di navigazione a sinistra.
3. Cercare e selezionare **DevTest Labs** dall'elenco dei servizi. 
4. Nella pagina **DevTest Labs** selezionare il lab nell'elenco dei lab.
5. Nella home page del lab, selezionare **Aggiungi** sulla barra degli strumenti. 
6. Selezionare **un'immagine di base** per la macchina virtuale. 
7. Seleziona le opzioni di **automazione** nella parte inferiore della pagina sopra il pulsante **Invia.** 
8. Viene visualizzato il modello di **Azure Resource Manager** per la creazione della macchina virtuale. 
9. Il segmento JSON nella sezione **resources** include la definizione per il tipo di immagine selezionato in precedenza. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

In questo esempio viene illustrato come ottenere una definizione di un'immagine di Azure Market Place.In this example, you see how to get a definition of an Azure Market Place image. È possibile ottenere una definizione di un'immagine personalizzata, una formula o un ambiente nello stesso modo. Aggiungere gli elementi necessari per la macchina virtuale e impostare le impostazioni avanzate necessarie. Dopo aver fornito i valori per i campi obbligatori ed eventuali campi facoltativi, prima di selezionare il pulsante **Opzioni di automazione.**

### <a name="use-azure-rest-api"></a>Usare l'API REST di AzureUse Azure REST API
La procedura seguente illustra come ottenere le proprietà di un'immagine usando l'API REST: questi passaggi funzionano solo per una macchina virtuale esistente in un lab. 

1. Passare alla pagina [Macchine virtuali - elenco,](/rest/api/dtl/virtualmachines/list) selezionare il pulsante **Prova.** 
2. Selezionare la **sottoscrizione di Azure**.
3. Immettere il **gruppo di risorse per il lab**.
4. Immettere il **nome del lab**. 
5. Selezionare **Esegui**.
6. Vengono visualizzate le **proprietà dell'immagine** in base alla quale è stata creata la macchina virtuale. 

## <a name="set-expiration-date"></a>Impostare la data di scadenza
In scenari come formazione, demo e versioni di valutazione, è possibile creare macchine virtuali ed eliminarle automaticamente dopo una durata fissa in modo da non incorrere in costi non necessari. È possibile impostare una data di scadenza per una macchina virtuale durante la creazione usando PowerShell, come illustrato nella sezione script di [PowerShell](#powershell-script) di esempio.

Ecco uno script di PowerShell di esempio che imposta la data di scadenza per tutte le macchine virtuali esistenti in un lab:Here is a sample PowerShell script that sets expiration date for all existing VMs in a lab:

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>Passaggi successivi
Vedere il contenuto seguente: Documentazione di [Azure PowerShell per Azure DevTest LabsSee](/powershell/module/az.devtestlabs/) the following content: Azure PowerShell documentation for Azure DevTest Labs
