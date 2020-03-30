---
title: Spostare l'indirizzo IP pubblico di Azure in un'altra area di Azure usando Azure PowerShellMove Azure Public IP to another Azure region using Azure PowerShell
description: Usare il modello di Azure Resource Manager per spostare l'indirizzo IP pubblico di Azure da un'area di Azure a un'altra usando Azure PowerShell.Use Azure Resource Manager template to move Azure Public IP from an Azure region to another using Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: c55b6011381d385fed7c7b8175ff02ec9be66fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641554"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>Spostare l'indirizzo IP pubblico di Azure in un'altra area usando Azure PowerShellMove Azure Public IP to another region using Azure PowerShell

Esistono vari scenari in cui si desidera spostare gli indirizzi IP pubblici di Azure esistenti da un'area a un'altra. Ad esempio, è possibile creare un indirizzo IP pubblico con la stessa configurazione e sku per il test. È anche possibile spostare un indirizzo IP pubblico in un'altra area come parte della pianificazione del ripristino di emergenza.

Gli indirizzi IP pubblici di Azure sono specifici dell'area e non possono essere spostati da un'area a un'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare la configurazione esistente di un indirizzo IP pubblico.  È quindi possibile creare temporaneamente la risorsa in un'altra area esportando l'indirizzo IP pubblico in un modello, modificando i parametri in modo che corrispondano all'area di destinazione e quindi distribuire il modello nella nuova area.  Per altre informazioni su Resource Manager e sui modelli, vedere Esportare gruppi di risorse in [modelliFor more information](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) on Resource Manager and templates, see Export resource groups to templates


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che l'indirizzo IP pubblico di Azure si trovi nell'area di Azure da cui si vuole spostare.

- Gli indirizzi IP pubblici di Azure non possono essere spostati tra le aree.  Sarà necessario associare il nuovo IP pubblico alle risorse nell'area di destinazione.

- Per esportare una configurazione IP pubblica e distribuire un modello per creare un indirizzo IP pubblico in un'altra area, è necessario il ruolo Collaboratore di rete o versione successiva.
   
- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato aservizi di bilanciamento del carico, gruppi di sicurezza di rete (NSG) e reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare indirizzi IP pubblici nell'area di destinazione usata. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di indirizzi IP pubblici per questo processo.  Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparare e spostare
The following steps show how to prepare the public IP for the configuration move using a Resource Manager template, and move the public IP configuration to the target region using Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Esportare il modello e distribuirlo da uno script

1. Accedere alla sottoscrizione di Azure con il comando Connect-AzAccount e seguire le istruzioni visualizzate:Sign in to your Azure subscription with the [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) command and follow the on-screen directions:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Ottenere l'ID risorsa dell'IP pubblico che si desidera spostare nell'area di destinazione e inserirlo in una variabile utilizzando [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Esportare la rete virtuale di origine in un file con estensione json nella directory in cui viene eseguito il comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Il file scaricato verrà denominato in base al gruppo di risorse da cui è stata esportata la risorsa.  Individuare il file esportato dal comando denominato ** \<nome-gruppo-risorse>.json** e aprirlo in un editor di propria scelta:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Per modificare il parametro del nome IP pubblico, modificare la proprietà **defaultValue** del nome IP pubblico di origine con il nome dell'IP pubblico di destinazione, assicurarsi che il nome sia racchiuso tra virgolette:
    
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

6. Per modificare l'area di destinazione in cui verrà spostato l'IP pubblico, modificare la proprietà **location** in risorse:

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
  
7. Per ottenere i codici di posizione dell'area, è possibile usare il cmdlet di Azure PowerShell Get-AzLocation eseguendo il comando seguente:To obtain region location codes, you can use the Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) by running the following command:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. È inoltre possibile modificare altri parametri nel modello, se lo si desidera, e sono facoltativi a seconda dei requisiti:

    * **Sku** - È possibile modificare lo sku dell'IP pubblico nella configurazione da standard a basic o basic to standard modificando la proprietà del**nome** **sku** > nel file ** \<>.json del gruppo di risorse:**

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

         Per ulteriori informazioni sulle differenze tra ips pubblici sku di base e standard, vedere [Creare, modificare o eliminare un indirizzo IP pubblico.](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)

    * Metodo di **allocazione IP pubblico** e **Timeout inattivo:** è possibile modificare entrambe queste opzioni nel modello modificando la proprietà **publicIPAllocationMethod** da **Dynamic** a **Static** a **Dynamic**. **Static** Il timeout di inattività può essere modificato modificando la proprietà **idleTimeoutInMinutes** impostandola sulla quantità desiderata.  Il valore predefinito è **4**:

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

        Per ulteriori informazioni sui metodi di allocazione e sui valori di timeout di inattività, vedere [Creare, modificare o eliminare un indirizzo IP pubblico.](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)


9. Salvare il ** \<nome del gruppo di risorse>** file json.

10. Creare un gruppo di risorse nell'area di destinazione per l'indirizzo IP pubblico di destinazione da distribuire tramite [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Distribuire il file ** \<modificato nome-gruppo di risorse>.json** nel gruppo di risorse creato nel passaggio precedente usando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

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

Dopo la distribuzione, se si desidera ricominciare o eliminare l'ip pubblico nella destinazione, eliminare il gruppo di risorse creato nella destinazione e l'INDIRIZZO IP pubblico spostato verrà eliminato.  Per rimuovere il gruppo di risorse, usare [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento della rete virtuale, eliminare la rete virtuale o il gruppo di risorse di origine, usare [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) o [Remove-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un indirizzo IP pubblico di Azure da un'area a un'altra e sono stati puliti le risorse di origine.  Per altre informazioni sullo spostamento delle risorse tra aree e il ripristino di emergenza in Azure, vedere:To learn more about moving resources between regions and disaster recovery in Azure, refer to:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
