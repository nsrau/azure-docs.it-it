---
title: Spostare il gruppo di sicurezza di rete di Azure in un'altra area di Azure usando Azure PowerShellMove Azure network security group (NSG) to another Azure region using Azure PowerShell
description: Usare il modello di Azure Resource Manager per spostare il gruppo di sicurezza di rete di Azure da un'area di Azure a un'altra usando Azure PowerShell.Use Azure Resource Manager template to move Azure network security group from one Azure region to another using Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 0cbd8f61cb1b4cb8eae6b30625fb3039ff75adde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641469"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Spostare il gruppo di sicurezza di rete di Azure in un'altra area usando Azure PowerShellMove Azure network security group (NSG) to another region using Azure PowerShell

Esistono vari scenari in cui si desidera spostare i gruppi di sicurezza di rete esistenti da un'area all'altra. Ad esempio, è possibile creare un gruppo di sicurezza di rete con le stesse regole di configurazione e sicurezza per il test. È anche possibile spostare un gruppo di sicurezza di rete in un'altra area come parte della pianificazione del ripristino di emergenza.

I gruppi di sicurezza di Azure non possono essere spostati da un'area all'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare le regole di configurazione e sicurezza esistenti di un gruppo di sicurezza di rete.  È quindi possibile gestire temporaneamente la risorsa in un'altra area esportando il gruppo di sicurezza di rete in un modello, modificando i parametri in modo che corrispondano all'area di destinazione e quindi distribuire il modello nella nuova area.  Per ulteriori informazioni su Resource Manager e sui modelli, vedere [Esportare gruppi](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)di risorse in modelli .


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che il gruppo di sicurezza di rete di Azure si trovi nell'area di Azure da cui si vuole spostare.

- I gruppi di sicurezza di rete di Azure non possono essere spostati da un aree all'altro.  È necessario associare il nuovo gruppo di sicurezza di rete alle risorse nell'area di destinazione.

- Per esportare una configurazione del gruppo di sicurezza di rete e distribuire un modello per creare un gruppo di sicurezza di rete in un'altra area, è necessario il ruolo Collaboratore di rete o versione successiva.
   
- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato a servizi di bilanciamento del carico, INDIRIZZI IP pubblici e reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare gruppi di sicurezza di rete nell'area di destinazione usata. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di gruppi di sicurezza di sicurezza di sicurezza di sicurezza per questo processo.  Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparare e spostare
The following steps show how to prepare the network security group for the configuration and security rule move using a Resource Manager template, and move the NSG configuration and security rules to the target region using Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Esportare il modello e distribuirlo da uno script

1. Accedere alla sottoscrizione di Azure con il comando Connect-AzAccount e seguire le istruzioni visualizzate:Sign in to your Azure subscription with the [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) command and follow the on-screen directions:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Ottenere l'ID risorsa del gruppo di sicurezza di rete che si desidera spostare nell'area di destinazione e inserirlo in una variabile utilizzando [Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Esportare il gruppo di sicurezza di rete di origine in un file con estensione json nella directory in cui viene eseguito il comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. Il file scaricato verrà denominato in base al gruppo di risorse da cui è stata esportata la risorsa.  Individuare il file esportato dal comando denominato ** \<nome-gruppo-risorse>.json** e aprirlo in un editor di propria scelta:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Per modificare il parametro del nome del gruppo di sicurezza di gruppo, modificare la proprietà **defaultValue** del nome del gruppo di sicurezza di gruppo di origine con il nome del gruppo di sicurezza di gruppo di destinazione, assicurarsi che il nome sia tra virgolette:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Per modificare l'area di destinazione in cui verranno spostate le regole di configurazione e sicurezza del gruppo di sicurezza, modificare la proprietà **location** in **Resources**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Per ottenere i codici di posizione dell'area, è possibile usare il cmdlet di Azure PowerShell Get-AzLocation eseguendo il comando seguente:To obtain region location codes, you can use the Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) by running the following command:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. È anche possibile modificare altri parametri nel ** \<nome del gruppo di risorse>.json,** se lo si desidera, e sono facoltativi a seconda dei requisiti:You can also change other parameters in the resource-group-name>.json if you choose, and are optional depending on your requirements:

    * Regole di **sicurezza:** è possibile modificare le regole distribuite nel gruppo di sicurezza di rete di destinazione aggiungendo o rimuovendo regole alla sezione securityRules del file>.json del gruppo di risorse:Security rules - You can edit which rules are deployed into the target NSG by adding or removing rules to the **securityRules** section in the ** \<resource-group-name>.json** file:

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Per completare l'aggiunta o la rimozione delle regole nel gruppo di sicurezza di rete di destinazione, è necessario modificare anche i tipi di regole personalizzate alla fine del file ** \<>.json** del gruppo di risorse nel formato dell'esempio seguente:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Salvare il ** \<nome del gruppo di risorse>** file json.

10. Creare un gruppo di risorse nell'area di destinazione per il gruppo di sicurezza di rete di destinazione da distribuire tramite New-AzResourceGroup:Create a resource group in the target region for the target NSG to be deployed using [New-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Distribuire il file ** \<modificato nome-gruppo di risorse>.json** nel gruppo di risorse creato nel passaggio precedente usando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Per verificare che le risorse siano state create nell'area di destinazione, utilizzare [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Discard 

Dopo la distribuzione, se si desidera ricominciare o eliminare il gruppo di sicurezza di rete nella destinazione, eliminare il gruppo di risorse creato nella destinazione e il gruppo di sicurezza di rete spostato verrà eliminato.  Per rimuovere il gruppo di risorse, usare [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento del gruppo di sicurezza di rete, eliminare il gruppo di sicurezza di rete o il gruppo di risorse di origine, usare [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) o [Remove-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un gruppo di sicurezza di rete di Azure da un'area a un'altra e sono stati puliti le risorse di origine.  Per altre informazioni sullo spostamento delle risorse tra aree e il ripristino di emergenza in Azure, vedere:To learn more about moving resources between regions and disaster recovery in Azure, refer to:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
