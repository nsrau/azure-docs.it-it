---
title: Creare una rete virtuale di Azure | Microsoft Docs
description: "Informazioni su come creare una rete virtuale con più subnet."
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
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 19857ad1e970ad32359708ded320c53a4778ef8c
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Creare una rete virtuale con più subnet

Questa esercitazione spiega come creare una Rete virtuale di Azure basilare con subnet pubblica e privata separate. È possibile connettere alle subnet risorse di Azure come Macchine virtuali (VM), ambienti servizio app, set di scalabilità di macchine virtuali, HDInsight e Servizi cloud. Le risorse connesse alle reti virtuali possono comunicare tra loro attraverso la rete privata di Azure.

Le sezioni seguenti riportano anche i passaggi per distribuire una rete virtuale usando il [portale](#portal) di Azure, l'[interfaccia della riga di comando](#cli) di Azure, Azure [PowerShell](#powershell) e il [modello](#template) Azure Resource Manager. Il risultato è lo stesso, indipendentemente dallo strumento con cui si sceglie di distribuire la rete virtuale. Facendo clic sul collegamento per qualsiasi strumento si accede direttamente a quella sezione dell'articolo. Per altre informazioni su tutte le impostazioni di rete virtuale e subnet, leggere gli articoli [Manage VNets](virtual-network-manage-network.md) (Gestire le reti virtuali) e [Manage subnets](virtual-network-manage-subnet.md) (Gestire le subnet).

## <a name="portal"></a>Portale di Azure

1. In un browser Internet passare al [portale](https://portal.azure.com) di Azure ed eseguire l'accesso con l'[account](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) di Azure. Se non si ha un account, è possibile registrarsi per ottenere una [versione di prova gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Nel portale fare clic su **+ Nuovo** > **Rete** > **Rete virtuale**.
3. Nel pannello **Rete virtuale** che viene visualizzato lasciare selezionato *Resource Manager* sotto **Selezionare un modello di distribuzione** e fare clic su **Crea**.
4. Nel pannello **Crea rete virtuale** che viene visualizzato immettere i valori seguenti e quindi fare clic sul pulsante **Crea**:

    |Impostazione|Valore|
    |---|---|
    |Nome|*MyVnet*|
    |Spazio degli indirizzi|*10.0.0.0/16*|
    |Nome della subnet|Pubblico|
    |Intervallo di indirizzi subnet|*10.0.0.0/24*|
    |Gruppo di risorse|Lasciare selezionata l'opzione **Crea nuovo** e immettere *MyResourceGroup*|
    |Sottoscrizione e posizione|Selezionare la sottoscrizione e la posizione.

    Se non si ha familiarità con Azure, vedere altre informazioni su [gruppi di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [sottoscrizioni](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [posizioni](https://azure.microsoft.com/regions) (anche chiamati aree).
6. Quando si crea una rete virtuale il portale consente di creare una sola subnet. Per questa esercitazione, dopo aver creato la rete virtuale viene creata una seconda subnet. In un secondo momento è possibile connettere le risorse accessibili da Internet alla subnet *Public* e connettere le risorse che non sono accessibili da Internet a una subnet privata. Per creare la seconda subnet immettere *MyVnet* nella casella *Cerca risorse* nella parte superiore del portale. Fare clic su **MyVnet** quando viene visualizzato nei risultati della ricerca. Se si hanno più reti virtuali con lo stesso nome nella sottoscrizione, vengono visualizzati i nomi dei gruppi di risorse sotto ciascuna rete virtuale con lo stesso nome. Assicurarsi di fare clic sul risultato MyVnet sotto il quale compare *MyResourceGroup*.
7. Nel pannello **MyVnet** che viene visualizzato fare clic su **Subnet** sotto **IMPOSTAZIONI**.
8. Nel pannello **MyVnet - Subnet** fare clic su **+Subnet**.
9. Immettere *Private* per **Nome**, *10.0.1.0/24* per **Intervallo di indirizzi** nel pannello **Aggiungi subnet** e fare clic su **OK**.
10. Esaminare le subnet nel pannello **MyVnet - Subnet**. Vengono visualizzate le subnet **Public** e **Private** che sono state create.
11. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete-portal) di questo articolo.

## <a name="cli"></a>CLI
Mentre i comandi dell'interfaccia della riga di comando sono uguali se eseguiti in Windows, Linux o macOS, vi sono differenze negli script tra le shell del sistema operativo. Le istruzioni seguenti sono valide per eseguire uno script Bash che contiene comandi dell'interfaccia della riga di comando:

1. In un browser Internet passare al [portale](https://portal.azure.com) di Azure ed eseguire l'accesso con l'[account](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) di Azure. Se non si ha un account, è possibile registrarsi per ottenere una [versione di prova gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Nella parte superiore del portale, a destra della barra *Cerca risorse*, fare clic sull'icona **> _** per avviare una Azure Cloud Shell Bash (anteprima). Il riquadro della cloud shell viene visualizzato nella parte inferiore del portale e dopo alcuni secondi mostra il prompt **username@Azure:~$**. La cloud shell esegue automaticamente l'accesso ad Azure usando le credenziali con cui l'utente si è autenticato nel portale.
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
    
    # Create an additional subnet within the VNet.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. Creare un file di script e salvarlo. Al prompt della cloud shell digitare `nano myscript.sh --nonewlines`. Il comando avvia il nano editor GNU con un file myscript.sh vuoto. Posizionare il puntatore del mouse all'interno della finestra dell'editor, fare clic con il pulsante destro del mouse e quindi fare clic su **Incolla**. L'archiviazione della cloud shell non viene mantenuta fra le sessioni. Se si preferisce mantenere lo script fra più sessioni della cloud shell, configurare l'[archiviazione permanente](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per la cloud shell. 
5. Tenere premuti i tasti **CTRL + X** della tastiera, immettere **Y** e premere **INVIO** per salvare il file con il nome myscript.sh.
6. Dal prompt della cloud shell contrassegnare il file come eseguibile con il comando `chmod +x myscript.sh`.
7. Eseguire lo script immettendo `./myscript.sh`.
8. Una volta completato lo script, esaminare le subnet per la rete virtuale copiando e incollando il seguente comando nella cloud shell Bash:
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete-cli) di questo articolo.

## <a name="powershell"></a>PowerShell
1. Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di Azure PowerShell. Se non si ha familiarità con Azure PowerShell, vedere l'articolo [Panoramica di Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Avviare una sessione di PowerShell facendo clic sul pulsante Start, digitando **powershell** e facendo clic su **PowerShell** fra i risultati della ricerca.
3. Nella finestra di PowerShell immettere il comando `login-azurermaccount` per eseguire l'accesso con l'[account](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) di Azure. Se non si ha un account, è possibile registrarsi per ottenere una [versione di prova gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Nel browser copiare lo script seguente:
    ```powershell
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. Per eseguire lo script, fare doppio clic nella finestra di PowerShell.
6. Esaminare le subnet per la rete virtuale copiando il comando seguente e incollandolo nella finestra di PowerShell:
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete-powershell) di questo articolo.

## <a name="template"></a>Modello

È possibile distribuire una rete virtuale con un modello Azure Resource Manager. Per altre informazioni sui modelli, vedere l'articolo [Informazioni su Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). Per accedere al modello e conoscere i suoi parametri, visualizzare la pagina Web [Create a VNet with two subnets template](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Creare una rete virtuale con il modello con due subnet). È possibile distribuire il modello mediante il [portale](#template-portal), l'[interfaccia della riga di comando](#template-cli) o [PowerShell](#template-powershell).

**Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi di qualsiasi procedura della sezione [Eliminare risorse](#delete) di questo articolo.

### <a name="template-portal"></a>Portale

1. Nel browser aprire il modello [pagina Web](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Fare clic sul pulsante **Distribuisci in Azure** che apre la pagina di accesso del portale di Azure.
3. Accedere al portale con il proprio [account](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Se non si ha un account, è possibile registrarsi per ottenere una [versione di prova gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Per i parametri inserire i valori seguenti:

    |Parametro|Valore|
    |---|---|
    |Subscription|Selezionare la propria sottoscrizione.|
    |Gruppo di risorse|MyResourceGroup|
    |Località|Selezionare una località.|
    |Nome della rete virtuale|MyVnet|
    |Prefisso di indirizzo della rete virtuale|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Pubblico|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Privato|

5. Accettare i termini e le condizioni e quindi fare clic su **Acquista** per distribuire la rete virtuale.

### <a name="template-cli"></a>Interfaccia della riga di comando

1. In un browser Internet passare al [portale](https://portal.azure.com) di Azure ed eseguire l'accesso con l'[account](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) di Azure. Se non si ha un account, è possibile registrarsi per ottenere una [versione di prova gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Nella parte superiore del portale, a destra della barra *Cerca risorse*, fare clic sull'icona **> _** per avviare una Azure Cloud Shell Bash (anteprima). Il riquadro della cloud shell viene visualizzato nella parte inferiore del portale e dopo alcuni secondi mostra il prompt **username@Azure:~$**. La cloud shell esegue automaticamente l'accesso ad Azure usando le credenziali con cui l'utente si è autenticato nel portale.
3. Creare un gruppo di risorse per la rete virtuale immettendo il comando seguente:  `az group create --name MyResourceGroup --location eastus`
4. È possibile distribuire il modello con:
    - **Valori predefiniti dei parametri**: immettere il seguente comando:   `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
    - **Valori personalizzati per i parametri**: scaricare e modificare il modello prima di distribuirlo, distribuire il modello con i parametri della riga di comando o distribuire il modello usando un file di parametri separato. È possibile scaricare i file del modello e dei parametri facendo clic sul pulsante **Sfoglia su GitHub** della pagina Web [Create a VNet with two subnets template](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Creare una rete virtuale con il modello con due subnet). In GitHub fare clic sul file **azuredeploy.parameters.json** o **azuredeploy.json** e quindi fare clic sul pulsante **Raw** per il file. Nel browser copiare il contenuto e salvarlo in un file nel computer in uso. Modificare i valori dei parametri nel modello o distribuire il modello con un file di parametri separato.  

    Per altre informazioni su come distribuire i modelli usando questi metodi, digitare `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di Azure PowerShell. Se non si ha familiarità con Azure PowerShell, vedere l'articolo [Panoramica di Azure PowerShell](/azure/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Avviare una sessione di PowerShell facendo clic sul pulsante Start, digitando **powershell** e facendo clic su **PowerShell** fra i risultati della ricerca.
3. Nella finestra di PowerShell immettere il comando `login-azurermaccount` per eseguire l'accesso con l'[account](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) di Azure. Se non si ha un account, è possibile registrarsi per ottenere una [versione di prova gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Creare un gruppo di risorse per la rete virtuale immettendo il comando seguente:  `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`
5. È possibile distribuire il modello con:
    - **Valori predefiniti dei parametri**: immettere il seguente comando:   `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`        
    - **Valori personalizzati per i parametri**: scaricare e modificare il modello prima di distribuirlo, distribuire il modello con i parametri della riga di comando o distribuire il modello usando un file di parametri separato. È possibile scaricare i file del modello e dei parametri facendo clic sul pulsante **Sfoglia su GitHub** della pagina Web [Create a VNet with two subnets template](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) (Creare una rete virtuale con il modello con due subnet). In GitHub fare clic sul file **azuredeploy.parameters.json** o **azuredeploy.json** e quindi fare clic sul pulsante **Raw** per il file. Nel browser copiare il contenuto e salvarlo in un file nel computer in uso. Modificare i valori dei parametri nel modello o distribuire il modello con un file di parametri separato.  

    Per altre informazioni su come distribuire i modelli usando questi metodi, digitare `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Eliminare risorse
Dopo aver completato questa esercitazione, è possibile eliminare la risorsa in modo da non dare luogo ad addebiti. Eliminando un gruppo di risorse vengono eliminate anche tutte le risorse contenute in esso.

### <a name="delete-portal"></a>Portale

1. Nel portale iniziare digitando *MyResourceGroup* nella casella *Cerca risorse* nella parte superiore del portale. Fare clic su **MyResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Nel pannello MyResourceGroup che viene visualizzato fare clic sull'icona Elimina nella parte superiore del pannello.
3. Per confermare l'eliminazione, immettere *MyResourceGroup* nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE:** e fare clic su **Elimina**.

### <a name="delete-cli"></a>Interfaccia della riga di comando

Al prompt della cloud shell immettere il comando seguente: `az group delete --name MyResourceGroup --yes`

### <a name="delete-powershell"></a>PowerShell

Al prompt di PowerShell immettere il comando seguente: `Remove-AzureRmResourceGroup -Name MyResourceGroup`

## <a name="next-steps"></a>Passaggi successivi

- Per conoscere tutte le impostazioni di rete virtuale e subnet, leggere gli articoli [Manage VNets](virtual-network-manage-network.md#view-vnet) (Gestire le reti virtuali) e [Manage subnets](virtual-network-manage-subnet.md#create-subnet) (Gestire le subnet). Esistono varie opzioni che consentono di creare reti virtuali di produzione e subnet per soddisfare requisiti diversi.
- Filtrare il traffico delle subnet in ingresso e in uscita creando e applicando [gruppi di sicurezza di rete](virtual-networks-nsg.md) alle subnet.
- Creare una VM [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e connetterla alla rete virtuale.
- Connettere la rete virtuale a un'altra rete virtuale nella stessa posizione con il [peering reti virtuali](virtual-network-peering-overview.md).
- Connettere la rete virtuale a una rete locale tramite una [rete privata virtuale da sito a sito](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o il circuito [ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

