---
title: Spostare la configurazione dell'IP pubblico di Azure in un'altra area usando Azure PowerShell
description: Utilizzo del modello di Azure Resource Manager per spostare la configurazione dell’IP pubblico di Azure da un'area di Azure a un'altra usando Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 4f72c22ee26375e025af7b3a391fdd45187e7041
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006269"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-azure-powershell"></a>Spostare la configurazione dell’IP pubblico di Azure in un'altra area usando Azure PowerShell

In vari scenari in cui può essere opportuno spostare le configurazioni IP pubbliche di Azure esistenti da un'area a un'altra. Ad esempio, si potrebbe voler creare un IP pubblico con la stessa configurazione e SKU per i test. Inoltre, si potrebbe spostare una configurazione IP pubblica in un'altra area come parte della pianificazione del ripristino di emergenza.

**Gli indirizzi IP pubblici di Azure sono specifici dell'area e non possono essere spostati da un'area all'altra.** Tuttavia, è possibile usare un modello di Azure Resource Manager per esportare la configurazione esistente di un IP pubblico.  È quindi possibile spostare la risorsa in un'altra area di staging esportando l'IP pubblico in un modello, modificando i parametri in base all'area di destinazione e quindi distribuendo il modello nella nuova area.  Per altre informazioni su Resource Manager e sui modelli, vedere [Esporta gruppi di risorse in modelli](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che l'indirizzo IP pubblico di Azure si trovi nell'area di Azure da cui si vuole eseguire lo spostamento.

- Gli indirizzi IP pubblici di Azure non possono essere spostati tra le aree.  È necessario associare il nuovo indirizzo IP pubblico alle risorse nell'area di destinazione.

- Per esportare una configurazione IP pubblica e distribuire un modello per creare un IP pubblico in un'altra area, è necessario un ruolo di collaboratore di rete o superiore.
   
- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, tra cui i servizi di bilanciamenti del carico, i gruppi di sicurezza di rete e le reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare IP pubblici nell'area di destinazione in uso. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di indirizzi IP pubblici per il processo.  Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparazione e spostamento
I passaggi seguenti illustrano come preparare l'IP pubblico per lo spostamento della configurazione usando un modello di Resource Manager e spostare la configurazione dell’IP pubblico nell'area di destinazione con Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Esportare il modello e distribuirlo da uno script

1. Accedere alla propria sottoscrizione di Azure con il comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e seguire le istruzioni visualizzate:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Ottenere l'ID risorsa dell'IP pubblico che si vuole spostare nell'area di destinazione e inserirlo in una variabile usando [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Esportare la rete virtuale di origine in un file con estensione JSON nella directory in cui si esegue il comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Il file scaricato avrà il nome del gruppo di risorse da cui è stata esportata la risorsa.  Individuare il file che è stato esportato dal comando **\<resource-group-name>.json** e aprilo in un editor a propria scelta:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Per modificare il parametro del nome IP pubblico, modificare la proprietà **defaultValue** del nome IP pubblico di origine con il nome dell'IP pubblico di destinazione assicurandosi che il nome sia tra virgolette:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Per modificare l'area di destinazione in spostare l'indirizzo IP pubblico, modificare la proprietà **location** nelle risorse:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Per ottenere i codici di posizione dell'area, è possibile usare il cmdlet di Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) eseguendo questo comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Se lo si desidera, è anche possibile modificare altri parametri nel modello, che sono facoltativi in base ai requisiti:

    * **SKU**: è possibile modificare il parametro SKU dell'indirizzo IP pubblico nella configurazione da standard a basic o da basic a standard modificando la proprietà s **sku** > **name** nel file **\<resource-group-name>.json**:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Per altre informazioni sulle differenze tra gli indirizzi IP pubblici con SKU basic o standard, vedere [Creare, modificare o eliminare un indirizzo IP pubblico](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Metodo di allocazione dell'IP pubblico** e **timeout di inattività**: è possibile modificare entrambe queste opzioni nel modello modificando la proprietà **publicIPAllocationMethod** da **dinamica** a **statica** o da **statica** a **dinamica**. Il timeout di inattività può essere modificato impostando la proprietà **idleTimeoutInMinutes** sul valore desiderato.  Il valore predefinito è **4**:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Per ulteriori informazioni sui metodi di allocazione e i valori di timeout di inattività, vedere [Creare, modificare o eliminare un indirizzo IP pubblico](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


9. Salvare il file **\<resource-group-name>.json**.

10. Creare un gruppo di risorse nell'area di destinazione per l'IP pubblico di destinazione da distribuire utilizzando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Distribuire il file **\<resource-group-name>.json** modificato nel gruppo di risorse creato nel passaggio precedente utilizzando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Per verificare che le risorse siano state create nell'area di destinazione, utilizzare [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>Discard 

Dopo la distribuzione, se si desidera ricominciare o eliminare l'IP pubblico nella destinazione, eliminare il gruppo di risorse creato nella destinazione e l'IP pubblico spostato verrà eliminato.  Per rimuovere il gruppo di risorse, usare [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento della rete virtuale, eliminare la rete virtuale di origine o il gruppo di risorse, utilizzare [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) o [Remove-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un indirizzo IP pubblico di Azure da un'area a un'altra ed è stata eseguita la pulizia delle risorse di origine.  Per altre informazioni sullo spostamento di risorse tra aree e sul ripristino di emergenza in Azure, vedere:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
