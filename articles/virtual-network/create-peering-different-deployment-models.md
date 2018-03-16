---
title: Creare un peering di rete virtuale di Azure - Modelli di distribuzione diversa - Stessa sottoscrizione | Microsoft Docs
description: Informazioni su come creare un peering di rete virtuale tra reti virtuali distribuite con modelli di distribuzione di Azure diversi e incluse nella stessa sottoscrizione di Azure.
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: 8558593633de47ce67adbfe28dc8973fd21f1a4b
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Creare un peering di rete virtuale: diversi modelli di distribuzione, stessa sottoscrizione 

In questa esercitazione si apprenderà a creare un peering di rete virtuale tra reti virtuali create con modelli di distribuzione diversi. Le reti virtuali devono trovarsi nella stessa sottoscrizione. Il peering di due reti virtuali consente alle risorse che si trovano in reti virtuali diverse di comunicare tra loro con la stessa larghezza di banda e la stessa latenza come se fossero nella stessa rete virtuale. Altre informazioni sul [Peering di rete virtuale](virtual-network-peering-overview.md). 

I passaggi per creare un peering di rete virtuale sono diversi a seconda che le reti virtuali siano incluse nella stessa sottoscrizione o in sottoscrizioni diverse e in base al [modello di distribuzione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) con il quale sono state create le reti virtuali. Per informazioni su come creare un peering di rete virtuale in altri scenari, fare clic sullo scenario nella tabella seguente:

|Modello di distribuzione di Azure  | Sottoscrizione di Azure  |
|--------- |---------|
|[Entrambi con Resource Manager](tutorial-connect-virtual-networks-portal.md) |Uguale|
|[Entrambi con Resource Manager](create-peering-different-subscriptions.md) |Diversa|
|[Uno con Resource Manager, uno con la distribuzione classica](create-peering-different-deployment-models-subscriptions.md) |Diversa|

Non è possibile creare un peering di rete virtuale tra due reti virtuali distribuite tramite il modello di distribuzione classica. Se è necessario connettere due reti virtuali, entrambe create tramite il modello di distribuzione classica, è possibile usare un [gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure. 

Questa esercitazione consente di eseguire il peering di due reti virtuali nella stessa area. La possibilità di eseguire il peering di reti virtuali in aree diverse è attualmente disponibile in anteprima. Prima di tentare il peering di reti virtuali in aree diverse, eseguire i passaggi in [Effettuare la registrazione per il peering globale della rete virtuale](#register). In caso contrario, il peering avrà esito negativo. La possibilità di connettere reti virtuali in aree diverse con un [gateway VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure è disponibile a livello generale e non richiede la registrazione.

Per creare un peering di rete virtuale, è possibile usare il [portale di Azure](#portal), l'[interfaccia della riga di comando](#cli) di Azure, Azure [PowerShell](#powershell) o un [modello di Azure Resource Manager](#template). Facendo clic sui collegamenti degli strumenti precedenti, si passa direttamente alle procedure per la creazione di un peering di rete virtuale con il determinato strumento.

## <a name="create-peering---azure-portal"></a>Creare un peering: portale di Azure

1. Accedere al [Portale di Azure](https://portal.azure.com). L'account con cui si esegue l'accesso deve avere le autorizzazioni necessarie per la creazione di un peering di rete virtuale. Vedere la sezione [Autorizzazioni](#permissions) di questo articolo per informazioni dettagliate.
2. Fare clic su **+ Nuovo**, **Rete** e quindi **Rete virtuale**.
3. Nel pannello **Crea rete virtuale** immettere o selezionare i valori necessari per le impostazioni seguenti e quindi fare clic su **Crea**:
    - **Nome**: *myVnet1*
    - **Spazio indirizzi**: *10.0.0.0/16*
    - **Nome subnet**: *predefinito*
    - **Intervallo di indirizzi subnet**: *10.0.0.0/24*
    - **Sottoscrizione**: selezionare la propria sottoscrizione
    - **Gruppo di risorse**: selezionare **Crea nuovo** e immettere *myResourceGroup*
    - **Località**: *Stati Uniti orientali*
4. Fare clic su **+ Nuovo**. Nella casella **Cerca nel Marketplace** digitare *Rete virtuale*. Fare clic su **Rete virtuale** quando viene visualizzato nei risultati della ricerca. 
5. Nel pannello **Rete virtuale** selezionare **Classico** nella casella **Selezionare un modello di distribuzione** e fare clic su **Crea**.
6. Nel pannello **Crea rete virtuale** immettere o selezionare i valori necessari per le impostazioni seguenti e quindi fare clic su **Crea**:
    - **Nome**: *myVnet2*
    - **Spazio indirizzi**: *10.1.0.0/16*
    - **Nome subnet**: *predefinito*
    - **Intervallo di indirizzi subnet**: *10.1.0.0/24*
    - **Sottoscrizione**: selezionare la propria sottoscrizione
    - **Gruppo di risorse**: selezionare **Usa esistente** e quindi *myResourceGroup*
    - **Località**: *Stati Uniti orientali*
7. Nella casella **Cerca risorse** nella parte superiore del portale digitare *myResourceGroup*. Fare clic su **MyResourceGroup** quando viene visualizzato nei risultati della ricerca. Viene visualizzato un pannello per il gruppo di risorse **myresourcegroup**. Il gruppo di risorse contiene le due reti virtuali create nei passaggi precedenti.
8. Fare clic su **myVNet1**.
9. Nel pannello **myVnet1** visualizzato fare clic su **Peer** nell'elenco verticale di opzioni sul lato sinistro del pannello.
10. Nel pannello **myVnet1 - Peer** visualizzato fare clic su **+ Aggiungi**
11. Nel pannello **Aggiungi peering** visualizzato immettere o selezionare le opzioni seguenti e quindi fare clic su **OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modello di distribuzione della rete virtuale**: selezionare **Classica**. 
     - **Sottoscrizione**: selezionare la propria sottoscrizione
     - **Rete virtuale**: fare clic su **Scegliere una rete virtuale** e quindi scegliere **myVnet2**.
     - **Consenti accesso alla rete virtuale:** assicurarsi che sia selezionato **Abilitato**.
    Questa esercitazione non prevede l'uso di altre impostazioni. Per informazioni su tutte le impostazioni per il peering, vedere [Gestire i peering di rete virtuale](virtual-network-manage-peering.md#create-a-peering).
12. Dopo aver fatto clic su **OK** nel passaggio precedente, il pannello **Aggiungi peering** si chiude e viene visualizzato di nuovo il pannello **myVnet1 - Peer**. Dopo alcuni secondi, il peering creato viene visualizzato nel pannello. Nella colonna **STATO PEERING** relativa al peering **myVnet1ToMyVnet2** creato è riportato **Connesso**.

    Il peering viene quindi stabilito. Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
13. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
14. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete-portal) di questo articolo.

## <a name="cli"></a>Creare un peering - Interfaccia della riga di comando di Azure

1. [Installare](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) l'interfaccia della riga di comando di Azure 1.0 per creare la rete virtuale con modello di distribuzione classico.
2. Aprire una sessione di comando e accedere ad Azure usando il comando `azure login`.
3. Eseguire l'interfaccia della riga di comando in modalità di gestione del servizio immettendo il comando `azure config mode asm`.
4. Per creare una rete virtuale con modello di distribuzione classico, immettere il comando seguente:
 
    ```azurecli
    azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

5. Creare un gruppo di risorse e una rete virtuale con Resource Manager. È possibile usare l'interfaccia della riga di comando 1.0 o 2.0 ([installare](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)). In questa esercitazione, l'interfaccia della riga di comando 2.0 viene usata per creare la rete virtuale con Resource Manager, poiché è necessaria per creare il peering. Eseguire lo script dell'interfaccia della riga di comando bash seguente dal computer locale con l'interfaccia della riga di comando 2.0.4 o una versione successiva installata. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando bash nel client Windows, vedere [Esecuzione dell'interfaccia della riga di comando di Azure in Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). È anche possibile eseguire lo script con Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Fare clic sul pulsante **Prova** nello script seguente per richiamare una Cloud Shell con cui è possibile accedere all'account Azure personale. Per eseguire lo script, fare clic sul pulsante **Copia** e incollarne il contenuto nella Cloud Shell, quindi premere `Enter`.

    ```azurecli-interactive
    #!/bin/bash

    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus

    # Create the virtual network (Resource Manager).
    az network vnet create \
      --name myVnet1 \
      --resource-group myResourceGroup \
      --location eastus \
      --address-prefix 10.0.0.0/16
    ```

6. Creare un peering di rete virtuale tra due reti virtuali create con modelli di distribuzione diversi. Copiare lo script seguente in un editor di testo nel PC. Sostituire `<subscription id>` con l'ID della sottoscrizione. Se l'ID della sottoscrizione non è noto, immettere il comando `az account show`. Il valore per **id** nell'output è l'ID della sottoscrizione. Incollare lo script modificato nella sessione dell'interfaccia della riga di comando e quindi premere `Enter`.

    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group myResourceGroup \
      --name myVnet1 \
      --query id --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
      --allow-vnet-access
    ```
7. Dopo aver eseguito lo script, rivedere i peering di ciascuna rete virtuale creata con Resource Manager. Copiare il comando seguente, incollarlo nella sessione dell'interfaccia della riga di comando e quindi premere `Enter`:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    L'output mostra **Connesso** nella colonna **PeeringState**. 

    Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
8. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
9. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-cli) in questo articolo.

## <a name="powershell"></a>Creare un peering - PowerShell

1. Installare la versione più recente di [Azure](https://www.powershellgallery.com/packages/Azure) PowerShell e i moduli [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell).
2. Avviare una sessione di PowerShell.
3. In PowerShell, accedere ad Azure immettendo il comando `Add-AzureAccount`.
4. Per creare una rete virtuale con distribuzione classica con PowerShell, è necessario creare un nuovo file di configurazione di rete o modificarne uno esistente. Informazioni su come [esportare, aggiornare e importare il file di configurazione di rete](virtual-networks-using-network-configuration-file.md). Il file deve includere l'elemento **VirtualNetworkSite** seguente per la rete virtuale usata in questa esercitazione:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > L'importazione di un file di configurazione di rete modificato può modificare le reti virtuali esistenti create con la distribuzione classica nella sottoscrizione. Assicurarsi di aggiungere solo la rete virtuale precedente e di non modificare o rimuovere le reti virtuali esistenti dalla sottoscrizione. 
5. Accedere ad Azure per creare la rete virtuale con Resource Manager immettendo il comando `login-azurermaccount`. L'account con cui si esegue l'accesso deve avere le autorizzazioni necessarie per la creazione di un peering di rete virtuale. Vedere la sezione [Autorizzazioni](#permissions) di questo articolo per informazioni dettagliate.
6. Creare un gruppo di risorse e una rete virtuale con Resource Manager. Copiare lo script, incollarlo in PowerShell e quindi premere `Enter`.

    ```powershell
    # Create a resource group.
      New-AzureRmResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Creare un peering di rete virtuale tra due reti virtuali create con modelli di distribuzione diversi. Copiare lo script seguente in un editor di testo nel PC. Sostituire `<subscription id>` con l'ID della sottoscrizione. Se l'ID della sottoscrizione non è noto, immettere il comando `Get-AzureRmSubscription` per visualizzarlo. Il valore di **Id** nell'output restituito è l'ID della sottoscrizione. Per eseguire lo script, copiare lo script modificato dall'editor di testo, quindi fare clic con il pulsante destro del mouse nella sessione di PowerShell e premere `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Dopo aver eseguito lo script, rivedere i peering di ciascuna rete virtuale creata con Resource Manager. Copiare il comando seguente, incollarlo nella sessione di PowerShell e quindi premere `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    L'output mostra **Connesso** nella colonna **PeeringState**.

    Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

9. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
10. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-powershell) in questo articolo.
 
## <a name="permissions"></a>Autorizzazioni

Gli account usati per creare un peering di rete virtuale devono disporre del ruolo o delle autorizzazioni necessarie. Se, ad esempio, si deve eseguire il peering di due reti virtuali denominate myVnet1 e myVnet2, all'account deve essere assegnato il ruolo o le autorizzazioni minime seguenti per ogni rete virtuale:
    
|Rete virtuale|Modello di distribuzione|Ruolo|Autorizzazioni|
|---|---|---|---|
|myVnet1|Gestione risorse|[Collaboratore di rete](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classico|[Collaboratore reti virtuali classiche](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|myVnet2|Gestione risorse|[Collaboratore di rete](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classico|[Collaboratore reti virtuali classiche](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Altre informazioni sui [ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (solo Resource Manager).

## <a name="delete"></a>Eliminare risorse
Al termine di questa esercitazione, è necessario eliminare le risorse create, per non incorrere in costi di utilizzo. Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse all'interno di esso.

### <a name="delete-portal"></a>Portale di Azure

1. Nella casella di ricerca del portale immettere **myResourceGroup**. Nei risultati della ricerca fare clic su **myResourceGroup**.
2. Nel pannello **myResourceGroup** fare clic sull'icona **Elimina**.
3. Per confermare l'eliminazione, nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE** immettere **myResourceGroup** e quindi fare clic su **Elimina**.

### <a name="delete-cli"></a>

1. Usare l'interfaccia della riga di comando di Azure 2.0 per eliminare la rete virtuale creata con Resource Manager con il comando seguente:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Usare l'interfaccia della riga di comando di Azure 1.0 per eliminare la rete virtuale creata con distribuzione classica con il comando seguente:

    ```azurecli
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Immettere il comando seguente per eliminare la rete virtuale creata con Resource Manager:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroup -Force
    ```

2. Per eliminare una rete virtuale creata con distribuzione classica con PowerShell, è necessario modificare un file di configurazione di rete esistente. Informazioni su come [esportare, aggiornare e importare il file di configurazione di rete](virtual-networks-using-network-configuration-file.md). Rimuovere l'elemento VirtualNetworkSite seguente dalla rete virtuale usata in questa esercitazione:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > L'importazione di un file di configurazione di rete modificato può modificare le reti virtuali esistenti create con distribuzione classica nella sottoscrizione. Assicurarsi di rimuovere solo la rete virtuale precedente e di non modificare o rimuovere altre reti virtuali esistenti dalla sottoscrizione. 

## <a name="register"></a>Eseguire la registrazione per l'anteprima del peering di reti virtuali globale

Il peering reti virtuali nella stessa area è disponibile a livello generale. Il peering di reti virtuali in aree diverse è attualmente disponibile in anteprima. Vedere gli [aggiornamenti delle reti virtuali](https://azure.microsoft.com/en-us/updates/?product=virtual-network) per le aree disponibili. Per eseguire il peering di reti virtuali tra aree diverse, è prima necessario registrarsi per l'anteprima, completando i passaggi seguenti (all'interno della sottoscrizione in cui si trova ogni rete virtuale di cui si vuole eseguire il peering) usando Azure PowerShell o l'interfaccia della riga di comando di Azure:

### <a name="powershell"></a>PowerShell

1. Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di PowerShell. Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell).
2. Avviare una sessione di PowerShell e accedere ad Azure usando il comando `Login-AzureRmAccount`.
3. Registrare per l'anteprima la sottoscrizione in cui si trova ognuna delle reti virtuali di cui si vuole eseguire il peering immettendo i comandi seguenti:

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
4. Verificare di essere registrati per l'anteprima immettendo il comando seguente:

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Non completare i passaggi descritti nelle sezioni relative al portale, all'interfaccia della riga di comando di Azure, a PowerShell o al modello di Resource Manager di questo articolo finché l'output di **RegistrationState** che si riceve dopo aver immesso il comando precedente non corrisponde a **Registered** (Registrato) per entrambe le sottoscrizioni.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

1. [Installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).
2. Assicurarsi di usare la versione 2.0.18 o successiva dell'interfaccia della riga di comando di Azure immettendo il comando `az --version`. Se non si usa questa versione, installare la versione più recente.
3. Accedere ad Azure con il comando `az login`.
4. Registrare all'anteprima immettendo i comandi seguenti:

    ```azurecli-interactive
    az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
    az provider register --name Microsoft.Network
    ```

5. Verificare di essere registrati per l'anteprima immettendo il comando seguente:

    ```azurecli-interactive
    az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
    ```

    Non completare i passaggi descritti nelle sezioni relative al portale, all'interfaccia della riga di comando di Azure, a PowerShell o al modello di Resource Manager di questo articolo finché l'output di **RegistrationState** che si riceve dopo aver immesso il comando precedente non corrisponde a **Registered** (Registrato) per entrambe le sottoscrizioni.

## <a name="next-steps"></a>Passaggi successivi

- Acquisire familiarità con importanti [vincoli e comportamenti del peering di rete virtuale](virtual-network-manage-peering.md#requirements-and-constraints) prima di creare un peering di rete virtuale per l'uso in produzione.
- Acquisire informazioni più dettagliate su tutte le [impostazioni per il peering di rete virtuale](virtual-network-manage-peering.md#create-a-peering).
- Acquisire informazioni su come [Creare una topologia di rete hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con il peering di rete virtuale.
