---
title: "Creare una rete virtuale (classica) di Azure con più subnet | Microsoft Docs"
description: "Informazioni su come creare una rete virtuale (classica) con più subnet in Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 099e3c259f0b63e4376847727eb8e185aeb37380
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Creare una rete virtuale (classica) con più subnet

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di creare la maggior parte di nuove reti virtuali tramite il modello di distribuzione [Resource Manager](virtual-networks-create-vnet-arm-pportal.md).

Questa esercitazione spiega come creare una rete virtuale (classica) di Azure di base con subnet pubblica e privata separate. È possibile creare risorse di Azure, ad esempio macchine virtuali e servizi cloud in una subnet. Le risorse create nelle reti virtuali (classica) possono comunicare tra loro e con le risorse di altre reti connesse a una rete virtuale.

Altre informazioni su tutte le impostazioni relative alla [rete virtuale](virtual-network-manage-network.md) e alla [subnet](virtual-network-manage-subnet.md).

> [!WARNING]
> Le reti virtuali (classica) vengono eliminate immediatamente da Azure quando una [sottoscrizione viene disabilitata](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Le reti virtuali (classica) vengono eliminate indipendentemente dall'esistenza di risorse nella rete virtuale. Se si abilita di nuovo la sottoscrizione in un secondo momento, è necessario ricreare le risorse che erano presenti nella rete virtuale.

Si crea una rete virtuale (classica) usando il [portale di Azure](#portal), l'[interfaccia della riga di comando di Azure 1.0 ](#azure-cli) o [Azure PowerShell](#powershell).

## <a name="portal"></a>Portale

1. In un browser Internet passare al [Portale di Azure](https://portal.azure.com). Accedere usando l'[account Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Fare clic su **+Nuovo** nel portale.
3. Immettere *Rete virtuale* nella casella **Cerca nel Marketplace** nella parte superiore del pannello **Nuovo** visualizzato.  Fare clic su **Rete virtuale** quando viene visualizzato nei risultati della ricerca.
4. Nel pannello **Rete virtuale** visualizzato selezionare **Classico** nella casella **Selezionare un modello di distribuzione** e fare clic su **Crea**. 
5. Nel pannello **Crea rete virtuale (classica)** immettere i valori seguenti e quindi fare clic su **Crea**:

    |Impostazione|Valore|
    |---|---|
    |NOME|myVnet|
    |Spazio degli indirizzi|10.0.0.0/16|
    |Nome della subnet|Pubblico|
    |Intervallo di indirizzi subnet|10.0.0.0/24|
    |Gruppo di risorse|Lasciare selezionata l'opzione **Crea nuovo** e quindi immettere **myResourceGroup**.|
    |Sottoscrizione e località|Selezionare la sottoscrizione e la posizione.

    Se non si ha familiarità con Azure, acquisire altre informazioni su [gruppi di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [sottoscrizioni](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [località](https://azure.microsoft.com/regions), dette anche *aree*.
4. Quando si crea una rete virtuale nel portale, è possibile creare una sola subnet. In questa esercitazione verrà creata una seconda subnet dopo la creazione della rete virtuale. In seguito sarà quindi possibile creare risorse accessibili da Internet nella subnet **pubblica**. Sarà anche possibile creare risorse non accessibili da Internet in una subnet **privata**. Per creare la seconda subnet immettere **myVnet** nella casella **Cerca risorse** nella parte superiore della pagina. Fare clic su **myVnet** quando questo elemento viene visualizzato nei risultati della ricerca.
5. Fare clic su **Subnet** nella sezione **IMPOSTAZIONI** del pannello **Crea rete virtuale (classica)** visualizzato.
6. Fare clic su **+ Aggiungi** nel pannello **myVnet - subnet** visualizzato.
7. Immettere **Privata** per **Nome** nel pannello **Aggiungi subnet**. Immettere **10.0.1.0/24** per **Intervallo indirizzi**.  Fare clic su **OK**.
8. Nel pannello **myVnet - subnet** è possibile visualizzare le subnet **pubblica** e **privata** create.
9. **Facoltativo**: al termine di questa esercitazione è possibile eliminare le risorse create per non incorrere in costi di utilizzo:
    - Fare clic su **Panoramica** nel pannello **myVnet**.
    - Fare clic sull'icona **Elimina** nel pannello **myVnet**.
    - Per confermare l'eliminazione, nella casella **Elimina rete virtuale** fare clic su **Sì**.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

1. È possibile [installare e configurare l'interfaccia della riga di comando di Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oppure usare l'interfaccia della riga di comando all'interno di Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `azure <command> --help`. 
2. In una sessione dell'interfaccia della riga di comando accedere a Azure con il comando seguente. Se si fa clic **Prova** nella casella sottostante, viene aperto il servizio Cloud Shell. È possibile accedere alla sottoscrizione di Azure senza immettere il comando seguente:

    ```azurecli-interactive
    azure login
    ```

3. Per garantire che l'interfaccia della riga di comando si trovi in modalità Gestione dei servizi, immettere il comando seguente:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Creare una rete virtuale con una subnet privata:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Creare una subnet pubblica all'interno della rete virtuale:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Rivedere la rete virtuale e le subnet:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Facoltativo**: al termine di questa esercitazione è possibile eliminare le risorse create per non incorrere in costi di utilizzo:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Anche se non è possibile specificare un gruppo di risorse per creare una rete virtuale (classica) usando l'interfaccia della riga di comando, Azure crea la rete virtuale in un gruppo di risorse denominato *Default-Networking*.

## <a name="powershell"></a>PowerShell

1. Installare la versione più recente del modulo [Azure](https://www.powershellgallery.com/packages/Azure) PowerShell. Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell).
2. Avviare una sessione di PowerShell.
3. In PowerShell accedere ad Azure immettendo il comando `Add-AzureAccount`.
4. Modificare il percorso e il nome file seguenti in base alle esigenze, quindi esportare il file di configurazione di rete esistente:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Per creare una rete virtuale con subnet pubblica e privata, usare un editor di testo per aggiungere l'elemento **VirtualNetworkSite** seguente al file di configurazione di rete.

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    Rivedere la versione completa dello [schema di file di configurazione di rete](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importare il file di configurazione di rete:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > L'importazione di un file di configurazione di rete modificato può modificare le reti virtuali esistenti create con la distribuzione classica nella sottoscrizione. Assicurarsi di aggiungere solo la rete virtuale precedente e di non modificare o rimuovere le reti virtuali esistenti dalla sottoscrizione. 

7. Rivedere la rete virtuale e le subnet:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Facoltativo**: al termine di questa esercitazione è possibile eliminare le risorse create per non incorrere in costi di utilizzo. Per eliminare la rete virtuale, completare i passaggi 4-6, questa volta rimuovendo l'elemento **VirtualNetworkSite** nel passaggio 5.
 
> [!NOTE]
> Anche se non è possibile specificare un gruppo di risorse per creare una rete virtuale (classica) usando PowerShell, Azure crea la rete virtuale in un gruppo di risorse denominato *Default-Networking*.

---

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle impostazioni delle reti virtuali e delle subnet, vedere [Gestire le reti virtuali](virtual-network-manage-network.md) e [Gestire le subnet di rete virtuali](virtual-network-manage-subnet.md). In un ambiente di produzione sono disponibili varie opzioni per l'uso di reti virtuali e subnet per soddisfare requisiti diversi.
- Per filtrare il traffico delle subnet in ingresso e in uscita, creare e applicare [gruppi di sicurezza di rete](virtual-networks-nsg.md) alle subnet.
- Creare una macchina virtuale [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e quindi connetterla a una rete virtuale esistente.
- Per connettere due reti virtuali nella stessa località di Azure, creare un [peering reti virtuali](create-peering-different-deployment-models.md) tra due reti virtuali. È possibile connettere una rete virtuale (Gestione risorse) a una rete virtuale (classica), ma non è possibile creare un peering tra due reti virtuali (classica).
- Connettere la rete virtuale a una rete locale tramite un [Gateway VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o un circuito [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
