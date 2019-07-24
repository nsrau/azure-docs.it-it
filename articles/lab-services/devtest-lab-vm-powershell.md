---
title: Creare una macchina virtuale in DevTest Labs con Azure PowerShell | Microsoft Docs
description: Informazioni su come usare Azure DevTest Labs per creare e gestire macchine virtuali con Azure PowerShell.
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
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 1a6938bd541e316dbe9f333c670c382faab6ad21
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854255"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Creare una macchina virtuale con DevTest Labs usando Azure PowerShell
Questo articolo illustra come creare una macchina virtuale in Azure DevTest Labs usando Azure PowerShell. È possibile usare gli script di PowerShell per automatizzare la creazione di macchine virtuali in un Lab in Azure DevTest Labs. 

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare:

- [Creare un Lab](devtest-lab-create-lab.md) se non si vuole usare un lab esistente per testare lo script o i comandi in questo articolo. 
- [Installare Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0) o usare Azure cloud Shell integrato nella portale di Azure. 

## <a name="powershell-script"></a>Script di PowerShell
Lo script di esempio in questa sezione usa il cmdlet [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) .  Questo cmdlet accetta l'ID risorsa del Lab, il nome dell'azione da eseguire (`createEnvironment`) e i parametri necessari per eseguire tale azione. I parametri si trovano in una tabella hash che contiene tutte le proprietà di descrizione della macchina virtuale. 

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

Le proprietà della macchina virtuale nello script precedente consentono di creare una macchina virtuale con Windows Server 2016 datacenter come sistema operativo. Per ogni tipo di macchina virtuale, queste proprietà saranno leggermente diverse. Nella sezione [definizione macchina virtuale](#define-virtual-machine) viene illustrato come determinare le proprietà da utilizzare in questo script.

Il comando seguente fornisce un esempio di esecuzione dello script salvato in un nome file: Create-LabVirtualMachine. ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definisci macchina virtuale
In questa sezione viene illustrato come ottenere le proprietà specifiche di un tipo di macchina virtuale che si desidera creare. 

### <a name="use-azure-portal"></a>Usare il portale di Azure
È possibile generare un modello di Azure Resource Manager quando si crea una macchina virtuale nel portale di Azure. Non è necessario completare il processo di creazione della macchina virtuale. Si seguono solo i passaggi fino a quando non viene visualizzato il modello. Questo è il modo migliore per ottenere la descrizione JSON necessaria se non è già stata creata una macchina virtuale Lab. 

1. Passare al [portale di Azure](https://portal.azure.com).
2. Selezionare **tutti i servizi** nel menu di spostamento a sinistra.
3. Cercare e selezionare **DevTest Labs** dall'elenco dei servizi. 
4. Nella pagina **DevTest Labs** selezionare il Lab nell'elenco dei Lab.
5. Nella home page per il Lab selezionare **+ Aggiungi** sulla barra degli strumenti. 
6. Selezionare un' **immagine di base** per la macchina virtuale. 
7. Selezionare le **Opzioni di automazione** nella parte inferiore della pagina sopra il pulsante **Invia** . 
8. Viene visualizzato il **modello di Azure Resource Manager** per la creazione della macchina virtuale. 
9. Il segmento JSON nella sezione  resources contiene la definizione del tipo di immagine selezionato in precedenza. 

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

In questo esempio si vedrà come ottenere una definizione di un'immagine di Azure Market Place. È possibile ottenere una definizione di un'immagine personalizzata, una formula o un ambiente nello stesso modo. Aggiungere gli elementi necessari per la macchina virtuale e impostare le impostazioni avanzate necessarie. Dopo aver fornito i valori per i campi obbligatori e i campi facoltativi, prima di selezionare il pulsante **Opzioni di automazione** .

### <a name="use-azure-rest-api"></a>Usare l'API REST di Azure
La procedura seguente illustra i passaggi per ottenere le proprietà di un'immagine usando l'API REST: Questi passaggi funzionano solo per una macchina virtuale esistente in un Lab. 

1. Passare alla pagina [macchine virtuali-elenco](/rest/api/dtl/virtualmachines/list) , quindi fare clic sul pulsante **prova** . 
2. Selezionare la **sottoscrizione di Azure**.
3. Immettere il **gruppo di risorse per il Lab**.
4. Immettere il **nome del Lab**. 
5. Selezionare **Run** (Esegui).
6. Vengono visualizzate le **proprietà dell'immagine** in base alla quale è stata creata la macchina virtuale. 

## <a name="set-expiration-date"></a>Impostare la data di scadenza
In scenari come il training, le demo e le versioni di valutazione, è possibile creare macchine virtuali ed eliminarle automaticamente dopo una durata fissa, in modo da non incorrere in costi superflui. È possibile impostare una data di scadenza per una macchina virtuale durante la creazione usando PowerShell, come illustrato nella sezione [script PowerShell](#powershell-script) di esempio.

Ecco uno script di PowerShell di esempio che imposta la data di scadenza per tutte le macchine virtuali esistenti in un Lab:

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
Vedere il contenuto seguente: [Documentazione di Azure PowerShell per Azure DevTest Labs](/powershell/module/az.devtestlabs/)
