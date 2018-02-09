---
title: Creare un peering di rete virtuale di Azure - Resource Manager - stessa sottoscrizione | Microsoft Docs
description: Informazioni su come creare un peering di rete virtuale tra reti virtuali distribuite tramite Resource Manager e che si trovano nella stessa sottoscrizione di Azure.
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anavin;jdial
ms.openlocfilehash: 70fe948070147c01922fab68fb55a0f00c26a0f3
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="create-a-virtual-network-peering---resource-manager-same-subscription"></a>Creare un peering di rete virtuale - Resource Manager, stessa sottoscrizione

In questa esercitazione si apprenderà a creare un peering di rete virtuale tra reti virtuali distribuite tramite Resource Manager. Le reti virtuali devono trovarsi nella stessa sottoscrizione. Il peering di due reti virtuali consente alle risorse che si trovano in reti virtuali diverse di comunicare tra loro con la stessa larghezza di banda e la stessa latenza come se fossero nella stessa rete virtuale. Altre informazioni sul [Peering di rete virtuale](virtual-network-peering-overview.md). 

I passaggi per creare un peering di rete virtuale sono diversi a seconda che le reti virtuali siano incluse nella stessa sottoscrizione o in sottoscrizioni diverse e in base al [modello di distribuzione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) con il quale sono state create le reti virtuali. Per informazioni su come creare un peering di rete virtuale in altri scenari, fare clic sullo scenario nella tabella seguente:

|Modello di distribuzione di Azure  | Sottoscrizione di Azure  |
|--------- |---------|
|[Entrambi con Resource Manager](create-peering-different-subscriptions.md) |Diversa|
|[Uno con Resource Manager, uno con una distribuzione classica](create-peering-different-deployment-models.md) |Uguale|
|[Uno con Resource Manager, uno con una distribuzione classica](create-peering-different-deployment-models-subscriptions.md) |Diversa|

Non è possibile creare un peering di rete virtuale tra due reti virtuali distribuite tramite il modello di distribuzione classica. Se è necessario connettere due reti virtuali, entrambe create tramite il modello di distribuzione classica, è possibile usare un [gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure. 

Questa esercitazione consente di eseguire il peering di due reti virtuali nella stessa area. La possibilità di eseguire il peering di reti virtuali in aree diverse è attualmente disponibile in anteprima. Prima di tentare il peering di reti virtuali in aree diverse, eseguire i passaggi in [Effettuare la registrazione per il peering globale della rete virtuale](#register). In caso contrario, il peering avrà esito negativo. La possibilità di connettere reti virtuali in aree diverse con un [gateway VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure è disponibile a livello generale e non richiede la registrazione.

Per creare un peering di rete virtuale, è possibile usare il [portale di Azure](#portal), l'[interfaccia della riga di comando](#cli) di Azure, Azure [PowerShell](#powershell) o un [modello di Azure Resource Manager](#template). Facendo clic sui collegamenti degli strumenti precedenti, si passa direttamente alle procedure per la creazione di un peering di rete virtuale con il determinato strumento.

## <a name="portal"></a>Creare un peering - Portale di Azure

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
4. Completare di nuovo i passaggi 2 e 3 specificando i valori seguenti nel passaggio 3:
    - **Nome**: *myVnet2*
    - **Spazio indirizzi**: *10.1.0.0/16*
    - **Nome subnet**: *predefinito*
    - **Intervallo di indirizzi subnet**: *10.1.0.0/24*
    - **Sottoscrizione**: selezionare la propria sottoscrizione
    - **Gruppo di risorse**: selezionare **Usa esistente** e quindi *myResourceGroup*
    - **Località**: *Stati Uniti orientali*
5. Nella casella **Cerca risorse** nella parte superiore del portale digitare *myResourceGroup*. Fare clic su **MyResourceGroup** quando viene visualizzato nei risultati della ricerca. Viene visualizzato un pannello per il gruppo di risorse **myresourcegroup**. Il gruppo di risorse contiene le due reti virtuali create nei passaggi precedenti.
6. Fare clic su **myVNet1**.
7. Nel pannello **myVnet1** visualizzato fare clic su **Peer** nell'elenco verticale di opzioni sul lato sinistro del pannello.
8. Nel pannello **myVnet1 - Peer** visualizzato fare clic su **+ Aggiungi**
9. Nel pannello **Aggiungi peering** visualizzato immettere o selezionare le opzioni seguenti e quindi fare clic su **OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modello di distribuzione della rete virtuale**: selezionare **Resource Manager**. 
     - **Sottoscrizione**: selezionare la propria sottoscrizione
     - **Rete virtuale**: fare clic su **Scegliere una rete virtuale** e quindi scegliere **myVnet2**.
     - **Consenti accesso alla rete virtuale:** assicurarsi che sia selezionato **Abilitato**.
    Questa esercitazione non prevede l'uso di altre impostazioni. Per informazioni su tutte le impostazioni per il peering, vedere [Gestire i peering di rete virtuale](virtual-network-manage-peering.md#create-a-peering).
10. Dopo aver fatto clic su **OK** nel passaggio precedente, il pannello **Aggiungi peering** si chiude e viene visualizzato di nuovo il pannello **myVnet1 - Peer**. Dopo alcuni secondi, il peering creato viene visualizzato nel pannello. Nella colonna **STATO PEERING** relativa al peering **myVnet1ToMyVnet2** creato è riportato **Avviato**. È stato effettuato il peering da Vnet1 a Vnet2, ma ora occorre effettuare il peering da myVnet2 a myVnet1. Affinché le risorse delle reti virtuali possano comunicare tra loro, il peering deve essere creato in entrambe le direzioni.
11. Completare di nuovo i passaggi da 5 a 10 per myVnet2. Denominare il peering *myVnet2ToMyVnet1*.
12. Pochi secondi dopo aver fatto clic su **OK** per creare il peering per MyVnet2, viene visualizzato il peering **myVnet2ToMyVnet1** appena creato con**Connesso** nella colonna **STATO PEERING**.
13. Completare di nuovo i passaggi da 5 a 7 per MyVnet1. Lo **STATO PEERING** per il peering **myVnet1ToVNet2** ora è anch'esso **Connesso**. Quando viene visualizzato **Connesso** nella colonna **STATO PEERING** per entrambe le reti virtuale in peering, significa che il peering è stato stabilito correttamente.
14. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
15. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete-portal) di questo articolo.

Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="cli"></a>Creare un peering - Interfaccia della riga di comando di Azure

Lo script seguente:

- Richiede l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Per trovare la versione, eseguire il comando `az --version`. Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure nel client Windows, vedere [Running the Azure CLI in Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows). 

Invece di installare l'interfaccia della riga di comando e le sue dipendenze, è possibile usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Fare clic sul pulsante **Prova** nello script seguente per richiamare una Cloud Shell a cui è possibile accedere con l'account Azure personale. Per eseguire lo script, fare clic sul pulsante **Copia** e incollarne il contenuto nella Cloud Shell.

1. Creare un gruppo di risorse e due reti virtuali.

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    ```

2. Creare un peering di rete virtuale tra le due reti virtuali.
 
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    ```

3. Dopo aver eseguito lo script, rivedere i peering di ciascuna rete virtuale. 

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    Eseguire di nuovo il comando precedente sostituendo *myVnet1* con *myVnet2*. L'output di entrambi i comandi mostra **Connesso** nella colonna **Stato peering**.

     Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

4. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
5. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-cli) in questo articolo.


## <a name="powershell"></a>Creare un peering - PowerShell

1. Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di PowerShell. Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell).
2. Per avviare una sessione di PowerShell, passare a **Start**, immettere **powershell** e quindi fare clic su **PowerShell**.
3. In PowerShell accedere ad Azure immettendo il comando `login-azurermaccount`. L'account con cui si esegue l'accesso deve avere le autorizzazioni necessarie per la creazione di un peering di rete virtuale. Vedere la sezione [Autorizzazioni](#permissions) di questo articolo per informazioni dettagliate.
4. Creare un gruppo di risorse e due reti virtuali. Per eseguire lo script, copiare lo script seguente, incollarlo in PowerShell e quindi premere `Enter` dopo che sullo schermo è stata visualizzata l'ultima riga:

    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    ```

5. Creare un peering di rete virtuale tra le due reti virtuali. Copiare lo script seguente, incollarlo in PowerShell e quindi premere `Enter` dopo che sullo schermo è stata visualizzata l'ultima riga:
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    ```
6. Per rivedere le subnet della rete virtuale, copiare il comando seguente, incollarlo in PowerShell e quindi premere `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Eseguire di nuovo il comando precedente sostituendo *myVnet1* con *myVnet2*. L'output di entrambi i comandi mostra **Connesso** nella colonna **Stato peering**.
7. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
8. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-powershell) in questo articolo.

Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="template"></a>Creare un peering - Modello di Resource Manager

1. Fare riferimento al modello di Resource Manager per [creare un peering di rete virtuale](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering). Insieme al modello vengono fornite le istruzioni per distribuirlo tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Accedere allo strumento con cui si sceglie di distribuire il modello usando un account che disponga delle autorizzazioni necessarie per creare un peering di rete virtuale. Vedere la sezione [Autorizzazioni](#permissions) di questo articolo per informazioni dettagliate.
2. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
3. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete) di questo articolo usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="permissions"></a>Autorizzazioni

Gli account usati per creare un peering di rete virtuale devono disporre del ruolo o delle autorizzazioni necessarie. Se, ad esempio, si deve eseguire il peering di due reti virtuali denominate VNet1 e VNet2, all'account deve essere assegnato il ruolo o le autorizzazioni minime seguenti per ogni rete virtuale:
    
|Rete virtuale|Ruolo|Autorizzazioni|
|---|---|---|
|VNet1|[Collaboratore di rete](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|VNet2|[Collaboratore di rete](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

Altre informazioni sui [ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (solo Resource Manager).

## <a name="delete"></a>Eliminare risorse
Al termine di questa esercitazione, è necessario eliminare le risorse create, per non incorrere in costi di utilizzo. Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse all'interno di esso.

### <a name="delete-portal"></a>Portale di Azure

1. Nella casella di ricerca del portale immettere **myResourceGroup**. Nei risultati della ricerca fare clic su **myResourceGroup**.
2. Nel pannello **myResourceGroup** fare clic sull'icona **Elimina**.
3. Per confermare l'eliminazione, nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE** immettere **myResourceGroup** e quindi fare clic su **Elimina**.

### <a name="delete-cli"></a>

Immettere il comando seguente:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Immettere il comando seguente:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -force
```

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
