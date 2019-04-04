---
title: Creare una macchina virtuale in DevTest Labs con Azure PowerShell | Microsoft Docs
description: Informazioni su come usare Azure DevTest Labs per creare e gestire le macchine virtuali con Azure PowerShell.
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
ms.openlocfilehash: df6d7e59c6c569a56162a738924b4ffd05b47da6
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896443"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Creare una macchina virtuale con DevTest Labs usando Azure PowerShell
Questo articolo illustra come creare una macchina virtuale in Azure DevTest Labs usando Azure PowerShell. È possibile utilizzare gli script di PowerShell per automatizzare la creazione di macchine virtuali in un lab in Azure DevTest Labs. 

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare:

- [Creare un lab](devtest-lab-create-lab.md) se non si desidera utilizzare un lab esistente per testare lo script o comandi in questo articolo. 
- [Installare Azure PowerShell](/powershell/azure/azurerm/other-install) o usare Azure Cloud Shell è integrato nel portale di Azure. 

## <a name="powershell-script"></a>Script di PowerShell
Lo script di esempio in questa sezione Usa la [Invoke-AzureRmResourceAction](/powershell/module/azurerm.resources/invoke-azurermresourceaction) cmdlet.  Questo cmdlet accetta l'ID di risorsa del lab, nome dell'azione da eseguire (`createEnvironment`), e i parametri necessari eseguono tale azione. I parametri sono in una tabella hash che contiene tutte le proprietà descrizione della macchina virtuale. 

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
        $SubscriptionId = (Get-AzureRmContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzureRmResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzureRmResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzureRmResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
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
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzureRmResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

Le proprietà per la macchina virtuale nello script precedente ci consentono di creare una macchina virtuale con Windows Server 2016 DataCenter come sistema operativo. Per ogni tipo di macchina virtuale, queste proprietà sarà leggermente diverse. Il [definizione di macchina virtuale](#define-virtual-machine) sezione illustra come determinare quali proprietà utilizzare in questo script.

Il comando riportato di seguito viene fornito un esempio dell'esecuzione dello script salvato in un nome file: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definizione di macchina virtuale
Questa sezione illustra come ottenere le proprietà specifiche di un tipo di macchina virtuale che si desidera creare. 

### <a name="use-azure-portal"></a>Usare il portale di Azure
È possibile generare un modello Azure Resource Manager durante la creazione di una macchina virtuale nel portale di Azure. Non è necessario completare il processo di creazione della VM. È solo possibile seguire la procedura finché non viene visualizzato il modello. Questo è il modo migliore per ottenere la descrizione JSON necessaria se non si dispone già di un lab che VM creata. 

1. Passare al [portale di Azure](https://portal.azure.com).
2. Selezionare **tutti i servizi** dal menu di spostamento a sinistra.
3. Cercare e selezionare **DevTest Labs** dall'elenco dei servizi. 
4. Nel **DevTest Labs** pagina, selezionare il proprio lab nell'elenco dei lab.
5. Nella home page per l'ambiente lab, selezionare **+ Aggiungi** sulla barra degli strumenti. 
6. Selezionare una **immagine di base** per la macchina virtuale. 
7. Selezionare **opzioni di automazione** nella parte inferiore della pagina sopra il **Submit** pulsante. 
8. Viene visualizzato il **modello di Azure Resource Manager** per la creazione della macchina virtuale. 
9. Il segmento JSON il **risorse** sezione presenta la definizione per il tipo di immagine è selezionato in precedenza. 

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

In questo esempio viene illustrato come ottenere una definizione di un'immagine del Marketplace di Azure. È possibile ottenere una definizione di un'immagine personalizzata, una formula o un ambiente nello stesso modo. Aggiungere eventuali elementi necessari per la macchina virtuale e impostare le eventuali impostazioni avanzate necessarie. Dopo aver specificato i valori per i campi obbligatori e tutti i campi facoltativi, selezionando prima la **opzioni di automazione** pulsante.

### <a name="use-azure-rest-api"></a>Usare l'API REST di Azure
La procedura seguente illustra i passaggi per ottenere le proprietà di un'immagine tramite l'API REST: Questi passaggi funzionano solo per una VM esistente in un lab. 

1. Passare al [macchine virtuali - elencare](/rest/api/dtl/virtualmachines/list) pagina, selezionare **Provalo** pulsante. 
2. Selezionare la **sottoscrizione di Azure**.
3. Immettere il **gruppo di risorse per l'ambiente lab**.
4. Immettere il **nome del lab**. 
5. Selezionare **Run** (Esegui).
6. Viene visualizzato il **delle proprietà per l'immagine** basato su cui è stata creata la macchina virtuale. 



## <a name="next-steps"></a>Passaggi successivi
Vedere il contenuto seguente: [Documentazione di Azure PowerShell per Azure DevTest Labs](/powershell/module/az.devtestlabs/)