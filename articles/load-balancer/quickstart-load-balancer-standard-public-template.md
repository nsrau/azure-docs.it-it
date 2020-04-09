---
title: 'Guida introduttiva: Creare una risorsa Load Balancer - Modello di Azure'
titleSuffix: Azure Load Balancer
description: Questa guida di avvio rapido mostra come creare un servizio di bilanciamento del carico usando il modello di Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 175c5a36c873d16d50d5192a489133a01018e335
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474593"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Guida introduttiva: Creare una risorsa Load Balancer Standard per bilanciare il carico delle macchine virtuali con un modello di Azure Resource Manager

Il bilanciamento del carico offre un livello più elevato di disponibilità e scalabilità distribuendo le richieste in ingresso tra più macchine virtuali. Questo argomento di avvio rapido illustra come distribuire un modello di Azure Resource Manager che consente di creare un'istanza di Load Balancer Standard per bilanciare il carico delle macchine virtuali. Usando un modello di Resource Manager è necessario un numero di passaggi minore rispetto ad altri metodi di distribuzione.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-load-balancer"></a>Creare una risorsa Load Balancer

Gli SKU di Load Balancer e quelli degli indirizzi IP pubblici devono corrispondere. Quando si crea una risorsa Load Balancer Standard, è necessario creare anche un nuovo indirizzo IP pubblico Standard che viene configurato come front-end per Load Balancer Standard. Se si desidera creare una risorsa Load Balancer Basic, usare [questo modello](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). Microsoft consiglia di usare SKU Standard per i carichi di lavoro di produzione.

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json" range="1-324" highlight="58-122":::

Nel modello sono state definite più risorse di Azure.

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): per Load Balancer e per ognuna delle tre macchine virtuali.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3 risorse di questo tipo)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3 risorse di questo tipo)
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3 risorse di questo tipo): usare per configurare IIS e le pagine Web

Per altri modelli correlati ad Azure Load Balancer, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare **Prova** per il blocco di codice seguente per aprire Azure Cloud Shell e seguire le istruzioni per la connessione ad Azure.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   Attendere finché non viene visualizzato il prompt nella console.

1. Selezionare **Copia** nel blocco di codice precedente per copiare lo script di PowerShell.

1. Fare clic con il pulsante destro del mouse sul riquadro della console della shell e quindi scegliere **Incolla**.

1. Immettere i valori desiderati.

   La distribuzione del modello creerà tre zone di disponibilità. Le zone di disponibilità sono supportate solo in [alcune aree di Azure](../availability-zones/az-overview.md). Usare una delle aree di Azure supportate. Se non si è certi, immettere **centralus**.

   Il nome del gruppo di risorse è il nome del progetto seguito da **rg**. Questo nome sarà necessario nella prossima sezione.

Per la distribuzione del modello sono necessari circa 10 minuti. Al termine, l'output sarà simile al seguente:

![Modello di Resource Manager per Azure Load Balancer Standard: output della distribuzione con PowerShell](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Per distribuire il modello viene usato Azure PowerShell. Oltre ad Azure PowerShell, è anche possibile usare il portale di Azure, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../azure-resource-manager/templates/deploy-portal.md).

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Gruppi di risorse** nel riquadro sinistro.

1. Selezionare il gruppo di risorse creato nella sezione precedente. Il nome del gruppo di risorse predefinito è il nome del progetto seguito da **rg**.

1. Selezionare l'istanza di Load Balancer. Il nome predefinito è il nome del progetto seguito da **-lb**.

1. Copiare la parte dell'indirizzo IP dell'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.

   ![Modello di Resource Manager per Azure Load Balancer Standard: IP pubblico](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    Verrà visualizzata la pagina predefinita del server Web Internet Information Services (IIS).

   ![Server Web IIS](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Per verificare la distribuzione del traffico dell'istanza di Load Balancer fra tutte e tre le macchine virtuali, è possibile forzare l'aggiornamento del Web browser dal computer client.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate. A questo scopo, accedere al portale di Azure, selezionare il gruppo di risorse che contiene l'istanza di Load Balancer e quindi selezionare **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata un'istanza di Load Balancer Standard, sono state collegate ad essa alcune macchine virtuali, è stata configurata la regola del traffico di Load Balancer, è stato definito il probe di integrità e quindi è stata testata l'istanza di Load Balancer.

Per altre informazioni, passare alle esercitazioni su Load Balancer.

> [!div class="nextstepaction"]
> [Esercitazioni su Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
