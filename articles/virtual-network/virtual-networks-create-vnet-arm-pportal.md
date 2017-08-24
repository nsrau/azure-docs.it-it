---
title: "Creare una rete virtuale di Azure con più subnet | Microsoft Docs"
description: "Informazioni su come creare una rete virtuale con più subnet in Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: jdial
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: a31f0524a6fa1de45498f340a27b863a3c627e04
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Creare una rete virtuale con più subnet

Questa esercitazione spiega come creare una rete virtuale di Azure di base con subnet pubblica e privata separate. All'interno di una subnet è possibile creare risorse di Azure come macchine virtuali, ambienti del servizio app, set di scalabilità di macchine virtuali, Azure HDInsight e altri servizi cloud. Le risorse nelle reti virtuali possono comunicare tra loro e con le risorse di altre reti connesse a una rete virtuale.

Le sezioni seguenti includono la procedura da eseguire per creare una rete virtuale tramite il [Portale di Azure](#portal), l'[interfaccia della riga di comando di Azure](#azure-cli), [Azure PowerShell](#powershell)e un [modello di Azure Resource Manager](#resource-manager-template). Il risultato è lo stesso, indipendentemente dallo strumento usato per creare la rete virtuale. Fare clic sul collegamento relativo a uno strumento per passare alla sezione corrispondente dell'esercitazione. Altre informazioni su tutte le impostazioni relative alla [rete virtuale](virtual-network-manage-network.md) e alla [subnet](virtual-network-manage-subnet.md).

In questo articolo viene descritta la procedura per creare una rete virtuale tramite il modello di distribuzione Resource Manager, ovvero il modello di distribuzione consigliato durante la creazione di nuove reti virtuali. Se è necessario creare una rete virtuale (classico), vedere [Creare una rete virtuale (classica)](create-virtual-network-classic.md). Se non si ha familiarità con i modelli di distribuzione di Azure, vedere l'articolo [Informazioni sui modelli di distribuzione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="portal"></a>Portale di Azure

1. In un browser Internet passare al [Portale di Azure](https://portal.azure.com). Accedere usando l'[account Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Nel portale fare clic su **+Nuovo** > **Rete** > **Rete virtuale**.
3. Nel pannello **Crea rete virtuale** immettere i valori seguenti e quindi fare clic su **Crea**:

    |Impostazione|Valore|
    |---|---|
    |Nome|myVnet|
    |Spazio degli indirizzi|10.0.0.0/16|
    |Nome della subnet|Pubblico|
    |Intervallo di indirizzi subnet|10.0.0.0/24|
    |Gruppo di risorse|Lasciare selezionata l'opzione **Crea nuovo** e quindi immettere **myResourceGroup**.|
    |Sottoscrizione e località|Selezionare la sottoscrizione e la posizione.

    Se non si ha familiarità con Azure, acquisire altre informazioni su [gruppi di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [sottoscrizioni](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [località](https://azure.microsoft.com/regions), dette anche *aree*.
4. Quando si crea una rete virtuale nel portale, è possibile creare una sola subnet. In questa esercitazione verrà creata una seconda subnet dopo la creazione della rete virtuale. Sarà quindi possibile in seguito creare risorse accessibili da Internet nella subnet **pubblica**. Sarà anche possibile creare risorse non accessibili da Internet in una subnet **privata**. Per creare la seconda subnet, nella casella **Cerca risorse** nella parte superiore della pagina immettere **myVnet**. Nei risultati della ricerca fare clic su **myVnet**. Se nella sottoscrizione sono presenti più reti virtuali con lo stesso nome, controllare i gruppi di risorse elencati per ogni rete virtuale. Assicurarsi di fare clic sul risultato della ricerca **myVnet** con il gruppo di risorse **myResourceGroup**.
5. Nel pannello **myVnet**, in **IMPOSTAZIONI**, fare clic su **Subnet**.
6. Nel pannello **myVnet - Subnet** fare clic su **+Subnet**.
7. In **Nome** nel pannello **Aggiungi subnet** immettere **Private**. Come **Intervallo indirizzi** immettere **10.0.1.0/24**.  Fare clic su **OK**.
8. Rivedere le subnet nel pannello **myVnet - Subnet**. Qui sono visibili le subnet **Public** e **Private** create.
9. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-portal) in questo articolo.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

I comandi dell'interfaccia della riga di comando di Azure sono gli stessi, indipendentemente dall'esecuzione dei comandi stessi in Windows, Linux o macOS. Esistono tuttavia differenze di scripting tra le shell dei sistemi operativi. Lo script nei passaggi seguenti viene eseguito in una shell Bash. 

1. [Installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `az <command> --help`. Invece di installare l'interfaccia della riga di comando e i rispettivi prerequisiti, è possibile usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Cloud Shell include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Per usare Cloud Shell, fare clic sul pulsante Cloud Shell (**> _**) nella parte superiore del [portale](https://portal.azure.com) oppure sul pulsante *Prova* nei passaggi che seguono. 
2. Se si esegue l'interfaccia della riga di comando in locale, accedere ad Azure con il comando `az login`. Se si usa Cloud Shell, si è già connessi.
3. Esaminare lo script seguente e i relativi commenti. Nel browser, copiare lo script e incollarlo nella sessione dell'interfaccia della riga di comando:

    ```azurecli-interactive
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet named Public.
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet named Private in the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name myVnet \
      --resource-group myResourceGroup
    ```
    
4. Al termine dell'esecuzione dello script, esaminare le subnet per la rete virtuale. Copiare il comando seguente, quindi incollarlo nella sessione dell'interfaccia della riga di comando:

    ```azurecli
    az network vnet subnet list --resource-group myResourceGroup --vnet-name myVnet --output table
    ```

5. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-cli) in questo articolo.

## <a name="powershell"></a>PowerShell

1. Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di PowerShell. Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell).
2. In una sessione di PowerShell, accedere ad Azure con l'[account Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) usando il comando `login-azurermaccount`.

3. Esaminare lo script seguente e i relativi commenti. Nel browser, copiare lo script e incollarlo nella sessione di PowerShell:

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location eastus
    
    # Create the public and private subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Location eastus `
      -Name myVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    ```

4. Per rivedere le subnet della rete virtuale, copiare il comando seguente e quindi incollarlo nella sessione di PowerShell:

    ```powershell
    $Vnet.subnets | Format-Table Name, AddressPrefix
    ```

5. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-powershell) in questo articolo.

## <a name="resource-manager-template"></a>Modello di Resource Manager

È possibile distribuire una rete virtuale usando un modello di Azure Resource Manager. Per altre informazioni sui modelli, vedere [Panoramica di Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). Per accedere al modello e conoscerne i parametri, vedere il modello [Create a virtual network with two subnets](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Creare una rete virtuale con due subnet). È possibile distribuire il modello tramite il [portale](#template-portal), l'[interfaccia della riga di comando di Azure](#template-cli) o [PowerShell](#template-powershell).

**Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in una delle sezioni secondarie di [Eliminare risorse](#delete) in questo articolo.

### <a name="template-portal"></a>Portale di Azure

1. Nel browser aprire la [pagina del modello](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Fare clic sul pulsante **Distribuisci in Azure**. Se non si è ancora connessi ad Azure, accedere nella schermata di accesso del portale di Azure che viene visualizzata.
3. Accedere al Portale di Azure con l'[account di Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) personale. Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Per i parametri inserire i valori seguenti:

    |Parametro|Valore|
    |---|---|
    |Sottoscrizione|Selezionare la propria sottoscrizione|
    |Gruppo di risorse|myResourceGroup|
    |Percorso|Selezionare una località|
    |Nome della rete virtuale|myVnet|
    |Prefisso di indirizzo della rete virtuale|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Pubblico|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Privato|

5. Per distribuire la rete virtuale, accettare i termini e le condizioni e quindi fare clic su **Acquista**.

### <a name="template-cli"></a>

1. [Installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `az <command> --help`. Invece di installare l'interfaccia della riga di comando e i rispettivi prerequisiti, è possibile usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Cloud Shell include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Per usare Cloud Shell, fare clic sul pulsante Cloud Shell **> _** nella parte superiore del [portale](https://portal.azure.com) oppure sul pulsante **Prova** nei passaggi che seguono. 
2. Se si esegue l'interfaccia della riga di comando in locale, accedere ad Azure con il comando `az login`. Se si usa Cloud Shell, si è già connessi.
3. Per creare un gruppo di risorse per la rete virtuale, copiare il comando seguente e incollarlo nella sessione dell'interfaccia della riga di comando:

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    ```
    
4. È possibile distribuire il modello tramite una delle opzioni seguenti per i parametri:
    - **Valori predefiniti**. Immettere il comando seguente:
    
        ```azurecli-interactive
        az group deployment create --resource-group myResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
        ```
    - **Valori personalizzati**. Scaricare e modificare il modello prima di distribuirlo. È anche possibile distribuire il modello digitando i parametri dalla riga di comando o con un file di parametri separato. Per scaricare i file del modello e dei parametri, fare clic sul pulsante **Sfoglia su GitHub** della pagina del modello [Create a virtual network with two subnets](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Creare una rete virtuale con due subnet). In GitHub fare clic sul file **azuredeploy.parameters.json** o **azuredeploy.json**. e quindi fare clic su **Raw** (Non elaborato) per visualizzare il file. Dal browser copiare il contenuto del file. Salvare il contenuto in un file nel computer. È possibile modificare i valori dei parametri nel modello o distribuire il modello con un file di parametri separato.  

    Per altre informazioni su come distribuire modelli tramite questi metodi, digitare `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di PowerShell. Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell).
2. Nella sessione di PowerShell, per accedere con l'[account di Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) immettere `login-azurermaccount`.
3. Per creare un gruppo di risorse per la rete virtuale, immettere il comando seguente:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location eastus
    ```
    
4. È possibile distribuire il modello tramite una delle opzioni seguenti per i parametri:
    - **Valori predefiniti**. Immettere il comando seguente:
    
        ```powershell
        New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName myResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json
        ```
        
    - **Valori personalizzati**. Scaricare e modificare il modello prima di distribuirlo. È anche possibile distribuire il modello digitando i parametri dalla riga di comando o con un file di parametri separato. Per scaricare i file del modello e dei parametri, fare clic sul pulsante **Sfoglia su GitHub** della pagina del modello [Create a virtual network with two subnets](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Creare una rete virtuale con due subnet). In GitHub fare clic sul file **azuredeploy.parameters.json** o **azuredeploy.json**. e quindi fare clic su **Raw** (Non elaborato) per visualizzare il file. Dal browser copiare il contenuto del file. Salvare il contenuto in un file nel computer. È possibile modificare i valori dei parametri nel modello o distribuire il modello con un file di parametri separato.  

    Per altre informazioni su come distribuire modelli tramite questi metodi, digitare `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Eliminare risorse

Al termine di questa esercitazione, è possibile eliminare le risorse che sono state create per non incorrere in costi di utilizzo. Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse all'interno di esso.

### <a name="delete-portal"></a>Portale di Azure

1. Nella casella di ricerca del portale immettere **myResourceGroup**. Nei risultati della ricerca fare clic su **myResourceGroup**.
2. Nel pannello **myResourceGroup** fare clic sull'icona **Elimina**.
3. Per confermare l'eliminazione, nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE** immettere **myResourceGroup** e quindi fare clic su **Elimina**.

### <a name="delete-cli"></a>

Nella sessione dell'interfaccia della riga di comando immettere il comando seguente:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Nella sessione di PowerShell immettere il comando seguente:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle impostazioni delle reti virtuali e delle subnet, vedere [Gestire le reti virtuali](virtual-network-manage-network.md#view-vnet) e [Gestire le subnet di rete virtuali](virtual-network-manage-subnet.md#create-subnet). In un ambiente di produzione sono disponibili varie opzioni per l'uso di reti virtuali e subnet per soddisfare requisiti diversi.
- Per filtrare il traffico delle subnet in ingresso e in uscita, creare e applicare [gruppi di sicurezza di rete](virtual-networks-nsg.md) alle subnet.
- Creare una macchina virtuale [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e quindi connetterla alla rete virtuale esistente.
- Per connettere due reti virtuali nella stessa località di Azure, creare un [peering reti virtuali](virtual-network-peering-overview.md) tra le reti virtuali.
- Connettere la rete virtuale a una rete locale tramite un [Gateway VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o un circuito [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

