---
title: Creare una rete virtuale di Azure con subnet | Microsoft Docs
description: "Informazioni su come creare una rete virtuale con più subnet in Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: jdial
ms.custom: 
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 1ecfba0470c31d1119b5e6c0f9d8538abe40f834
ms.contentlocale: it-it
ms.lasthandoff: 06/01/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Creare una rete virtuale con più subnet

Questa esercitazione spiega come creare una rete virtuale di Azure di base con subnet pubblica e privata separate. È possibile connettere alle subnet risorse di Azure come macchine virtuali, ambienti Servizio app di Azure, set di scalabilità di macchine virtuali, Azure HDInsight e altri servizi cloud. Le risorse connesse alle reti virtuali possono comunicare tra loro attraverso la rete privata di Azure.

Le sezioni seguenti includono la procedura da eseguire per distribuire una rete virtuale tramite il [Portale di Azure](#portal), l'[interfaccia della riga di comando di Azure ](#azure-cli), [Azure PowerShell](#powershell)e un [modello di Azure Resource Manager](#resource-manager-template). Il risultato è lo stesso, indipendentemente dallo strumento usato per distribuire la rete virtuale. Fare clic sul collegamento relativo a uno strumento per passare alla sezione corrispondente dell'esercitazione. Per altre informazioni sulle impostazioni delle reti virtuali e delle subnet, vedere [Gestire le reti virtuali](virtual-network-manage-network.md) e [Gestire le subnet virtuali](virtual-network-manage-subnet.md).

## <a name="portal"></a>Portale di Azure

1. In un browser Internet passare al [Portale di Azure](https://portal.azure.com). Accedere tramite l'[account di Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) personale. Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Nel portale fare clic su **+Nuovo** > **Rete** > **Rete virtuale**.
3. In **Selezionare un modello di distribuzione** all'interno del pannello **Rete virtuale** lasciare selezionato **Gestione risorse** e quindi fare clic su **Crea**.
4. Nel pannello **Crea rete virtuale** immettere i valori seguenti e quindi fare clic su **Crea**:

    |Impostazione|Valore|
    |---|---|
    |Nome|MyVnet|
    |Spazio degli indirizzi|10.0.0.0/16|
    |Nome della subnet|Pubblico|
    |Intervallo di indirizzi subnet|10.0.0.0/24|
    |Gruppo di risorse|Lasciare selezionata l'opzione **Crea nuovo** e quindi immettere **MyResourceGroup**.|
    |Sottoscrizione e località|Selezionare la sottoscrizione e la posizione.

    Se non si ha familiarità con Azure, acquisire altre informazioni su [gruppi di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [sottoscrizioni](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [località](https://azure.microsoft.com/regions), dette anche *aree*.
6. Quando si crea una rete virtuale nel portale, è possibile creare una sola subnet. In questa esercitazione verrà creata una seconda subnet dopo la creazione della rete virtuale. Sarà quindi possibile in seguito connettere risorse accessibili da Internet alla subnet **pubblica**. Sarà anche possibile connettere risorse non accessibili da Internet a una subnet **privata**. Per creare la seconda subnet, nella casella **Cerca risorse** nella parte superiore della pagina immettere **MyVnet**. Nei risultati della ricerca fare clic su **MyVnet**. Se nella sottoscrizione sono presenti più reti virtuali con lo stesso nome, controllare i gruppi di risorse elencati per ogni rete virtuale. Assicurarsi di fare clic sul risultato della ricerca **MyVnet** con il gruppo di risorse **MyResourceGroup**.
7. Nel pannello **MyVnet**, in **IMPOSTAZIONI**, fare clic su **Subnet**.
8. Nel pannello **MyVnet - Subnet** fare clic su **+Subnet**.
9. In **Nome** nel pannello **Aggiungi subnet** immettere **Private**. Come **Intervallo indirizzi** immettere **10.0.1.0/24**.  Fare clic su **OK**.
10. Rivedere le subnet nel pannello **MyVnet - Subnet**. Qui sono visibili le subnet **Public** e **Private** create.
11. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-portal) in questo articolo.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

I comandi dell'interfaccia della riga di comando di Azure sono gli stessi, indipendentemente dall'esecuzione dei comandi stessi in Windows, Linux o macOS. Esistono tuttavia differenze di scripting tra le shell dei sistemi operativi. Le istruzioni seguenti consentono di eseguire uno script Bash contenente comandi dell'interfaccia della riga di comando di Azure:

1. In un browser Internet aprire il [Portale di Azure](https://portal.azure.com). Accedere tramite l'[account di Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) personale. Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Nella parte superiore della pagina del portale, a destra della casella **Cerca risorse**, fare clic sull'icona **>_** per avviare Azure Cloud Shell Bash (anteprima). Nella parte inferiore del portale viene visualizzato il riquadro Cloud Shell. Dopo qualche secondo compare il prompt **username@Azure:~$**. Cloud Shell esegue automaticamente l'accesso ad Azure tramite le credenziali usate per l'accesso al portale.
3. Nel browser copiare lo script seguente:
    ```azurecli
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name MyResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet.
    az network vnet create \
      --name MyVnet \
      --resource-group MyResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet within the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. Creare un file di script e salvarlo. Al prompt dei comandi di Cloud Shell digitare `nano myscript.sh --nonewlines`. Il comando avvia l'editor GNU nano con un file myscript.sh vuoto. Posizionare il cursore all'interno della finestra dell'editor, fare clic con il pulsante destro del mouse e quindi fare clic su **Incolla**.  
5. Per salvare il file con il nome myscript.sh, premere Ctrl + X, digitare **Y** e quindi premere Invio. L'archiviazione di Cloud Shell non prevede il salvataggio permanente da una sessione all'altra dei file salvati. Se si vuole eseguire il salvataggio permanente dello script da una sessione di Cloud Shell, configurare l'[archiviazione permanente](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per Cloud Shell e salvare il file nell'archivio permanente.
6. Al prompt dei comandi di Cloud Shell eseguire il comando `chmod +x myscript.sh` per contrassegnare il file come eseguibile.
7. Per eseguire lo script, immettere `./myscript.sh`.
8. Al termine dell'esecuzione dello script, per rivedere le subnet della rete virtuale, copiare il comando seguente e quindi incollarlo nel riquadro Cloud Shell Bash:
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-cli) in questo articolo.

## <a name="powershell"></a>PowerShell

1. Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di PowerShell. Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell).
2. Per avviare una sessione di PowerShell, passare a **Start**, immettere **powershell** e quindi fare clic su **PowerShell**.
3. Nella finestra di PowerShell, per accedere con l'[account di Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) immettere `login-azurermaccount`.
4. Nel browser copiare lo script seguente:
    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. Per eseguire lo script, fare clic con il pulsante destro del mouse nella finestra di PowerShell.
6. Per rivedere le subnet della rete virtuale, copiare il comando seguente e quindi incollarlo nella finestra di PowerShell:
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-powershell) in questo articolo.

## <a name="resource-manager-template"></a>Modello di Resource Manager

È possibile distribuire una rete virtuale usando un modello di Azure Resource Manager. Per altre informazioni sui modelli, vedere [Panoramica di Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). Per accedere al modello e conoscerne i parametri, vedere il modello [Create a virtual network with two subnets](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Creare una rete virtuale con due subnet). È possibile distribuire il modello tramite il [portale](#template-portal), l'[interfaccia della riga di comando di Azure](#template-cli) o [PowerShell](#template-powershell).

**Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in una delle sezioni secondarie di [Eliminare risorse](#delete) in questo articolo.

### <a name="template-portal"></a>Portale di Azure

1. Nel browser aprire la [pagina del modello](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Fare clic sul pulsante **Distribuisci in Azure**. Verrà visualizzata la pagina di accesso al Portale di Azure.
3. Accedere al Portale di Azure con l'[account di Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) personale. Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Per i parametri inserire i valori seguenti:

    |Parametro|Valore|
    |---|---|
    |Sottoscrizione|Selezionare la propria sottoscrizione|
    |Gruppo di risorse|MyResourceGroup|
    |Località|Selezionare una località|
    |Nome della rete virtuale|MyVnet|
    |Prefisso di indirizzo della rete virtuale|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Pubblico|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Privato|

5. Per distribuire la rete virtuale, accettare i termini e le condizioni e quindi fare clic su **Acquista**.

### <a name="template-cli"></a>

1. Accedere al [portale](https://portal.azure.com) con l'[account di Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) personale. Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. A destra della casella **Cerca risorse**, fare clic sull'icona **>_** per avviare Azure Cloud Shell Bash (anteprima). Nella parte inferiore del portale viene visualizzato il riquadro Cloud Shell. Dopo qualche secondo compare il prompt **username@Azure:~$**. Cloud Shell esegue automaticamente l'accesso ad Azure tramite le credenziali usate per l'accesso al Portale di Azure.
3. Per creare un gruppo di risorse per la rete virtuale, immettere il comando seguente:  `az group create --name MyResourceGroup --location eastus`
4. È possibile distribuire il modello tramite una delle opzioni seguenti per i parametri:
    - **Valori predefiniti**. Immettere il comando seguente:   `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
    - **Valori personalizzati**. Scaricare e modificare il modello prima di distribuirlo. È anche possibile distribuire il modello digitando i parametri dalla riga di comando o con un file di parametri separato. Per scaricare i file del modello e dei parametri, fare clic sul pulsante **Sfoglia su GitHub** della pagina del modello [Create a virtual network with two subnets](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Creare una rete virtuale con due subnet). In GitHub fare clic sul file **azuredeploy.parameters.json** o **azuredeploy.json**. e quindi fare clic su **Raw** (Non elaborato) per visualizzare il file. Dal browser copiare il contenuto del file. Salvare il contenuto in un file nel computer. È possibile modificare i valori dei parametri nel modello o distribuire il modello con un file di parametri separato.  

    Per altre informazioni su come distribuire modelli tramite questi metodi, digitare `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di PowerShell. Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell).
2. Per avviare una sessione di PowerShell, passare a **Start**, immettere **powershell** e quindi fare clic su **PowerShell**.
3. Nella finestra di PowerShell, per accedere con l'[account di Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) immettere `login-azurermaccount`.
4. Per creare un gruppo di risorse per la rete virtuale, immettere il comando seguente:  `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`
5. È possibile distribuire il modello tramite una delle opzioni seguenti per i parametri:
    - **Valori predefiniti**. Immettere il comando seguente:   `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`        
    - **Valori personalizzati**. Scaricare e modificare il modello prima di distribuirlo. È anche possibile distribuire il modello digitando i parametri dalla riga di comando o con un file di parametri separato. Per scaricare i file del modello e dei parametri, fare clic sul pulsante **Sfoglia su GitHub** della pagina del modello [Create a virtual network with two subnets](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Creare una rete virtuale con due subnet). In GitHub fare clic sul file **azuredeploy.parameters.json** o **azuredeploy.json**. e quindi fare clic su **Raw** (Non elaborato) per visualizzare il file. Dal browser copiare il contenuto del file. Salvare il contenuto in un file nel computer. È possibile modificare i valori dei parametri nel modello o distribuire il modello con un file di parametri separato.  

    Per altre informazioni su come distribuire modelli tramite questi metodi, digitare `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Eliminare risorse
Al termine di questa esercitazione, è necessario eliminare le risorse usate, per non incorrere in costi di utilizzo. Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse all'interno di esso.

### <a name="delete-portal"></a>Portale di Azure

1. Nella casella di ricerca del portale immettere **MyResourceGroup**. Nei risultati della ricerca fare clic su **MyResourceGroup**.
2. Nel pannello **MyResourceGroup** fare clic sull'icona **Elimina**.
3. Per confermare l'eliminazione, nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE** immettere **MyResourceGroup** e quindi fare clic su **Elimina**.

### <a name="delete-cli"></a>

Al prompt dei comandi di Cloud Shell immettere il comando seguente: `az group delete --name MyResourceGroup --yes`

### <a name="delete-powershell"></a>PowerShell

Al prompt dei comandi di PowerShell immettere il comando seguente: `Remove-AzureRmResourceGroup -Name MyResourceGroup`

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle impostazioni delle reti virtuali e delle subnet, vedere [Gestire le reti virtuali](virtual-network-manage-network.md#view-vnet) e [Gestire le subnet virtuali](virtual-network-manage-subnet.md#create-subnet). In un ambiente di produzione sono disponibili varie opzioni per l'uso di reti virtuali e subnet per soddisfare requisiti diversi.
- Creare e applicare [gruppi di sicurezza di rete](virtual-networks-nsg.md) (NSG, Network Security Group) alle subnet per filtrare il traffico delle subnet in ingresso e in uscita.
- Creare una [macchina virtuale Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o una [macchina virtuale Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e quindi connetterla alla rete virtuale.
- Connettere la rete virtuale a un'altra rete virtuale nella stessa località tramite il [peering di rete virtuale](virtual-network-peering-overview.md).
- Connettere la rete virtuale a una rete locale tramite una [rete privata virtuale da sito a sito](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o un circuito [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

