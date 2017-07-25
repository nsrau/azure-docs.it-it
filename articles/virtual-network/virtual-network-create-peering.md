---
title: Creare un peering di rete virtuale di Azure | Microsoft Docs
description: Informazioni su come creare un peering di rete virtuale tra due reti virtuali.
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
ms.date: 06/06/2017
ms.author: jdial;narayan;annahar
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 666165115e80a39d02386719b0d7e64d7ae17749
ms.contentlocale: it-it
ms.lasthandoff: 06/09/2017


---
# <a name="create-a-virtual-network-peering"></a>Creare un peering di rete virtuale

In questa esercitazione verrà illustrato come creare un peering di rete virtuale tra due reti virtuali. Il peering di due reti virtuali consente alle risorse che si trovano in reti virtuali diverse di comunicare tra loro con la stessa larghezza di banda e la stessa latenza come se fossero nella stessa rete virtuale. È possibile creare un peering di rete virtuale solo tra due reti virtuali che si trovano nella stessa area di Azure. Per altre informazioni sul peering di rete virtuale, vedere l'articolo [Peering di rete virtuale](virtual-network-peering-overview.md). 

Se occorre connettere reti virtuali che si trovano in aree di Azure diverse, si può usare un [gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure. 

Per creare un peering di rete virtuale, è possibile usare il [portale di Azure](#portal), [Azure PowerShell](#powershell), l'[interfaccia della riga di comando di Azure](#cli) o un [modello di Azure Resource Manager](#template). Facendo clic sui collegamenti degli strumenti precedenti, si passa direttamente alle procedure per la creazione di un peering di rete virtuale con il determinato strumento.

## <a name="portal"></a>Creare un peering - Portale di Azure

Completare la procedura seguente per creare un peering di rete virtuale tra due reti virtuali distribuite tramite Resource Manager e che si trovano nella stessa sottoscrizione. È anche possibile [eseguire il peering di due reti virtuali in sottoscrizioni diverse](#different-subscriptions) o [eseguire il peering di una rete virtuale (Resource Manager) con una rete virtuale (versione classica)](#different-models). 

1. Accedere al [portale di Azure](https://portal.azure.com). L'account con cui che si esegue l'accesso deve disporre delle autorizzazioni necessarie per creare un peering di rete virtuale. Vedere la sezione [Autorizzazioni](#permissions) di questo articolo per informazioni dettagliate.
2. Fare clic su **+ Nuovo**, **Rete** e quindi **Rete virtuale**.
3. Nella casella **Selezionare un modello di distribuzione** del pannello **Rete virtuale** lasciare selezionato **Resource Manager** e quindi fare clic su **Crea**.
4. Nel pannello **Crea rete virtuale** immettere o selezionare i valori necessari per le impostazioni seguenti e quindi fare clic su **Crea**:
    - **Nome**: *myVnet1*
    - **Spazio indirizzi**: *10.0.0.0/16*
    - **Nome subnet**: *predefinito*
    - **Intervallo di indirizzi subnet**: *10.0.0.0/24*
    - **Sottoscrizione**: selezionare la propria sottoscrizione
    - **Gruppo di risorse**: selezionare **Crea nuovo** e immettere *myResourceGroup*
    - **Località**: *Stati Uniti orientali*
5. Completare di nuovo i passaggi da 2 a 4 specificando i valori seguenti nel passaggio 4:
    - **Nome**: *myVnet2*
    - **Spazio indirizzi**: *10.1.0.0/16*
    - **Nome subnet**: *predefinito*
    - **Intervallo di indirizzi subnet**: *10.1.0.0/24*
    - **Sottoscrizione**: selezionare la propria sottoscrizione
    - **Gruppo di risorse**: selezionare **Usa esistente** e quindi *myResourceGroup*
    - **Località**: *Stati Uniti orientali*
6. Nella casella **Cerca risorse** nella parte superiore del portale digitare *myResourceGroup*. Fare clic su **MyResourceGroup** quando viene visualizzato nei risultati della ricerca. Viene visualizzato un pannello per il gruppo di risorse **myresourcegroup**. Il gruppo di risorse contiene le due reti virtuali create nei passaggi precedenti.
7. Fare clic su **myVNet1**.
8. Nel pannello **myVnet1** visualizzato fare clic su **Peer** nell'elenco verticale di opzioni sul lato sinistro del pannello.
9. Nel pannello **myVnet1 - Peer** visualizzato fare clic su **+ Aggiungi**
10. Nel pannello **Aggiungi peering** visualizzato immettere o selezionare le opzioni seguenti e quindi fare clic su **OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modello di distribuzione della rete virtuale**: selezionare **Resource Manager**. 
     - **Sottoscrizione**: selezionare la propria sottoscrizione
     - **Rete virtuale**: fare clic su **Scegliere una rete virtuale** e quindi scegliere **myVnet2**.
     - **Consenti accesso alla rete virtuale:** assicurarsi che sia selezionato **Abilitato**.
    Questa esercitazione non prevede l'uso di altre impostazioni. Per informazioni su tutte le impostazioni per il peering, vedere [Gestire i peering di rete virtuale](virtual-network-manage-peering.md#create-peering).
11. Dopo aver fatto clic su **OK** nel passaggio precedente, il pannello **Aggiungi peering** si chiude e viene visualizzato di nuovo il pannello **myVnet1 - Peer**. Dopo alcuni secondi, il peering creato viene visualizzato nel pannello. Nella colonna **STATO PEERING** relativa al peering **myVnet1ToMyVnet2** creato è riportato **Avviato**. È stato effettuato il peering da Vnet1 a Vnet2, ma ora occorre effettuare il peering da Vnet2 a Vnet1. Affinché le risorse delle reti virtuali possano comunicare tra loro, il peering deve essere creato in entrambe le direzioni.
12. Completare di nuovo i passaggi da 6 a 11 per myVnet2.  Denominare il peering *myVnet2ToMyVnet1*.
13. Pochi secondi dopo aver fatto clic su **OK** per creare il peering per MyVnet2, viene visualizzato il peering **myVnet2ToMyVnet1** appena creato con**Connesso** nella colonna **STATO PEERING**.
14. Completare di nuovo i passaggi da 6 a 8 per MyVnet1. Lo **STATO PEERING** per il peering **myVnet1ToVNet2** ora è anch'esso **Connesso**. Quando viene visualizzato **Connesso** nella colonna **STATO PEERING** per entrambe le reti virtuale in peering, significa che il peering è stato stabilito correttamente.
15. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
16. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete-portal) di questo articolo.

Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="cli"></a>Creare un peering - Interfaccia della riga di comando di Azure

Lo script seguente:

- Richiede l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure nel client Windows, vedere [Running the Azure CLI in Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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
    #
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
    #
    ```
3. Dopo aver eseguito lo script, rivedere i peering di ciascuna rete virtuale. Copiare il comando seguente e quindi incollarlo nella finestra di comando:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    #
    ```
Eseguire di nuovo il comando precedente sostituendo *myVnet1* con *myVnet2*. L'output di entrambi i comandi mostra **Connesso** nella colonna **Stato peering**.
4. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
5. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-cli) in questo articolo.

Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
 
## <a name="powershell"></a>Creare un peering - PowerShell

1. Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di PowerShell. Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell).
2. Per avviare una sessione di PowerShell, passare a **Start**, immettere **powershell** e quindi fare clic su **PowerShell**.
3. Nella finestra di PowerShell accedere ad Azure immettendo il comando `login-azurermaccount`. L'account con cui che si esegue l'accesso deve disporre delle autorizzazioni necessarie per creare un peering di rete virtuale. Vedere la sezione [Autorizzazioni](#permissions) di questo articolo per informazioni dettagliate.
4. Copiare lo script seguente nel browser, quindi fare clic con il pulsante destro del mouse nella finestra di PowerShell per eseguire questo script che crea un gruppo di risorse e due reti virtuali:
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
    #
    ```
5. Copiare lo script seguente nel browser, quindi fare clic con il pulsante destro del mouse nella finestra di PowerShell per eseguire questo script che crea un peering di rete virtuale tra le due reti virtuali:
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
    #
    ```
6. Per rivedere le subnet della rete virtuale, copiare il comando seguente e quindi incollarlo nella finestra di PowerShell:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    #
    ```

Eseguire di nuovo il comando precedente sostituendo *myVnet1* con *myVnet2*. L'output di entrambi i comandi mostra **Connesso** nella colonna **Stato peering**.
7. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
8. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-powershell) in questo articolo.

Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="template"></a>Creare un peering - Modello di Resource Manager

1. Fare riferimento al modello di Resource Manager per [creare un peering di rete virtuale](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering). Insieme al modello vengono fornite le istruzioni per distribuirlo tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Accedere allo strumento con cui sceglie di distribuire il modello usando un account che disponga delle autorizzazioni necessarie per creare un peering di rete virtuale. Vedere la sezione [Autorizzazioni](#permissions) di questo articolo per informazioni dettagliate.
2. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
3. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete) di questo articolo usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="different-models"></a>Eseguire il peering di reti virtuali create con modelli di distribuzione diversi

Nei passaggi riportati nelle sezioni precedenti è stato illustrato come creare un peering di rete virtuale tra due reti virtuali create tramite il modello di distribuzione Resource Manager. È possibile creare un peering anche tra una rete virtuale (Resource Manager) e una rete virtuale (versione classica). Non è possibile configurare un peering di rete virtuale tra due reti virtuali create tramite il modello di distribuzione classica. Per altre informazioni sui modelli di distribuzione di Azure, leggere l'articolo [Comprensione dei modelli di implementazione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Per creare un peering di rete virtuale, completare i passaggi da 1 a 11 riportati nella sezione relativa al [Portale](#portal) di questo articolo, ma scegliere il modello di distribuzione**Classica** quando si crea myVnet2 nel passaggio 3.

Quando si crea un peering di rete virtuale tra una rete virtuale (Resource Manager) e una rete virtuale (versione classica), si configura solo il peering dalla rete virtuale (Resource Manager) alla rete virtuale (versione classica). Dopo aver creato il peering per la rete virtuale (Resource Manager), lo stato del peering è **Aggiornamento in corso**. Lo stato passa automaticamente a **Connesso** dopo alcuni secondi, poiché non è necessario creare un peering per la rete virtuale (versione classica). 

> [!NOTE]
> Anche se non è un argomento trattato in questa esercitazione, è possibile modificare gli script riportati nelle sezioni relative all'[interfaccia della riga di comando di Azure](#cli) e a [PowerShell](#powershell) di questo articolo in modo da riflettere le differenze descritte in questa sezione.

## <a name="different-subscriptions"></a>Eseguire il peering di reti virtuali in sottoscrizioni diverse

Nei passaggi riportati nelle sezioni precedenti è stato illustrato come creare un peering di rete virtuale tra due reti nella stessa sottoscrizione. È possibile creare un peering anche tra reti virtuali in sottoscrizioni diverse, purché entrambe le sottoscrizioni siano associate allo stesso tenant di Azure Active Directory. Se non si ha già un tenant di AD, è possibile [crearne uno](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch) rapidamente. Se le sottoscrizioni sono associate a tenant di Active Directory diversi, non è possibile creare un peering di rete virtuale tra reti virtuali in sottoscrizioni diverse. I passaggi per creare il peering variano leggermente come indicato di seguito, in base al modello di distribuzione usato per creare le reti virtuali:

### <a name="different-subscriptions-same-deployment-model"></a>Entrambe le reti virtuali create tramite Resource Manager

1. Completare i passaggi da 1 a 7 riportati nella sezione relativa al [Portale](#portal) di questo articolo, ma scegliere una sottoscrizione diversa (associata allo stesso tenant di Azure Active Directory) per myVnet2 nel passaggio 5 rispetto a quella scelta per myVnet1 nel passaggio 4.
2. Nel pannello **myVnet1** visualizzato fare clic su **Controllo di accesso (IAM)** nell'elenco verticale di opzioni sul lato sinistro del pannello.
3. Nel pannello **myVnet1 - Controllo di accesso (IAM)** visualizzato fare clic su **+ Aggiungi**.
4. Nel pannello **Aggiungi autorizzazioni** visualizzato selezionare **Collaboratore rete** nella casella **Ruolo**.
5. Nella casella **Seleziona** selezionare un nome utente o digitare un indirizzo e-mail per cercarlo. L'elenco di utenti mostrato è tratto dallo stesso tenant di Azure Active Directory della rete virtuale per la quale si sta configurando il peering.
6. Fare clic su **Salva**.
7. Nel pannello **myVnet1 - Controllo di accesso (IAM)** visualizzato fare clic su **Proprietà** nell'elenco verticale di opzioni sul lato sinistro del pannello. Copiare l'**ID RISORSA**, che è simile all'esempio seguente: /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet1
8. Completare i passaggi da 1 a 7 di questa sezione per myVnet2.
9. Per assicurarsi che l'autorizzazione sia stata abilitata correttamente, disconnettersi da Azure e quindi accedere di nuovo. Se sono stati usati account diversi per le due reti virtuali, disconnettersi da entrambi gli account e accedere di nuovo a entrambi.
10. Nella casella **Cerca risorse** nella parte superiore del portale digitare *myResourceGroup*. Fare clic su **MyResourceGroup** quando viene visualizzato nei risultati della ricerca. Viene visualizzato un pannello per il gruppo di risorse **myresourcegroup**. Il gruppo di risorse contiene le due reti virtuali create nei passaggi precedenti.
11. Fare clic su **myVNet1**.
12. Nel pannello **myVnet1** visualizzato fare clic su **Peer** nell'elenco verticale di opzioni sul lato sinistro del pannello.
13. Nel pannello **myVnet1 - Peer** visualizzato fare clic su **+ Aggiungi**
14. Nel pannello **Aggiungi peering** visualizzato immettere o selezionare le opzioni seguenti e quindi fare clic su **OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modello di distribuzione della rete virtuale**: selezionare **Classica**. 
     - **Conosco l'ID della risorsa**: selezionare questa casella di controllo.
     - **ID risorsa**: immettere l'id risorsa per myVnet2. Questa casella viene visualizzata solo quando la casella di controllo **Conosco l'ID della risorsa** è selezionata.
     - **Consenti accesso alla rete virtuale:** assicurarsi che sia selezionato **Abilitato**.
    Questa esercitazione non prevede l'uso di altre impostazioni. Per informazioni su tutte le impostazioni per il peering, vedere [Gestire i peering di rete virtuale](virtual-network-manage-peering.md#create-peering).
15. Dopo aver fatto clic su **OK** nel passaggio precedente, il pannello **Aggiungi peering** si chiude e viene visualizzato di nuovo il pannello **myVnet1 - Peer**. Dopo alcuni secondi, il peering creato viene visualizzato nel pannello. Nella colonna **STATO PEERING** relativa al peering **myVnet1ToMyVnet2** creato è riportato **Avviato**. È stato effettuato il peering da Vnet1 a Vnet2, ma ora occorre effettuare il peering da Vnet2 a Vnet1. Affinché le risorse delle reti virtuali possano comunicare tra loro, il peering deve essere creato in entrambe le direzioni.
16. Completare di nuovo i passaggi da 10 a 14 di questa sezione per myVnet2.  Denominare il peering *myVnet2ToMyVnet1* e immettere l'ID risorsa per myVnet1 in **ID risorsa**.
17. Pochi secondi dopo aver fatto clic su **OK** per creare il peering per MyVnet2, viene visualizzato il peering **myVnet2ToMyVnet1** appena creato con**Connesso** nella colonna **STATO PEERING**.
18. Completare di nuovo i passaggi da 10 a 11 di questa sezione per MyVnet1. Lo **STATO PEERING** per il peering **myVnet1ToVNet2** ora è anch'esso **Connesso**. Quando viene visualizzato **Connesso** nella colonna **STATO PEERING** per entrambe le reti virtuale in peering, significa che il peering è stato stabilito correttamente.
19. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
20. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete-portal) di questo articolo.

### <a name="different-subscriptions-different-deployment-models"></a>Una rete virtuale (Resource Manager), una rete virtuale (versione classica)
 
[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-preview.md)]

1. Eseguire la registrazione per l'anteprima. È possibile eseguire il peering di reti virtuali create tramite modelli di distribuzione diversi che esistono in sottoscrizioni diverse solo dopo aver eseguito la registrazione per l'anteprima. Non è possibile eseguire la registrazione per l'anteprima nel portale o usando l'interfaccia della riga di comando di Azure. La registrazione per l'anteprima può essere eseguita solo tramite PowerShell. Per eseguire la registrazione per l'anteprima, completare le attività seguenti:
    - Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di PowerShell. Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell).
    - Per avviare una sessione di PowerShell da un PC Windows, passare a **Start**, immettere **powershell** e quindi fare clic su **PowerShell**.
    - Nella finestra di PowerShell accedere ad Azure immettendo il comando `login-azurermaccount`. L'account con cui che si esegue l'accesso deve disporre delle autorizzazioni necessarie per creare un peering di rete virtuale. Vedere la sezione [Autorizzazioni](#permissions) di questo articolo per informazioni dettagliate.
    - Registrare la funzionalità in anteprima per la sottoscrizione di Azure immettendo il comando seguente in PowerShell: 
    
    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    #
    ```
    Eseguire la registrazione per l'anteprima dopo l'accesso a ciascuna sottoscrizione. Non procedere con i passaggi seguenti fino a quando l'output di **RegistrationState** ricevuto dopo l'immissione del comando seguente non è **Registrato** in entrambe le sottoscrizioni.

    ```powershell
    Get-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    #
    ```

2. Completare i passaggi da 1 a 7 riportati nella sezione relativa al [Portale](#portal) di questo articolo apportando le modifiche seguenti:
    - Scegliere una sottoscrizione diversa (associata allo stesso tenant di Azure Active Directory) per myVnet2 nel passaggio 5 rispetto a quella scelta per myVnet1 nel passaggio 4.
    - Scegliere **Classica** nel passaggio 3 quando si crea myVnet2.
3. Nella casella **Cerca risorse** nella parte superiore del portale digitare *myResourceGroup*. Fare clic su **MyResourceGroup** quando viene visualizzato nei risultati della ricerca. Viene visualizzato un pannello per il gruppo di risorse **myresourcegroup**. Il gruppo di risorse contiene le due reti virtuali create nei passaggi precedenti.
4. Fare clic su **myVNet1**.
5. Completare i passaggi da 2 a 9 riportati nella sezione [Entrambe le reti virtuali create tramite Resource Manager](#different-subscriptions-same-deployment-model) di questo articolo.
6. Nella casella **Cerca risorse** nella parte superiore del portale digitare *myResourceGroup*. Fare clic su **MyResourceGroup** quando viene visualizzato nei risultati della ricerca. Viene visualizzato un pannello per il gruppo di risorse **myresourcegroup**. Il gruppo di risorse contiene le due reti virtuali create nei passaggi precedenti.
7. Fare clic su **myVNet1**.
8. Nel pannello **myVnet1** visualizzato fare clic su **Peer** nell'elenco verticale di opzioni sul lato sinistro del pannello.
9. Nel pannello **myVnet1 - Peer** visualizzato fare clic su **+ Aggiungi**
10. Nel pannello **Aggiungi peering** visualizzato immettere o selezionare le opzioni seguenti e quindi fare clic su **OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modello di distribuzione della rete virtuale**: selezionare **Classica**. 
     - **Conosco l'ID della risorsa**: selezionare questa casella di controllo.
     - **ID risorsa**: immettere l'id risorsa per myVnet2. Questa casella viene visualizzata solo quando la casella di controllo **Conosco l'ID della risorsa** è selezionata.
     - **Consenti accesso alla rete virtuale:** assicurarsi che sia selezionato **Abilitato**.
    Questa esercitazione non prevede l'uso di altre impostazioni. Per informazioni su tutte le impostazioni per il peering, vedere [Gestire i peering di rete virtuale](virtual-network-manage-peering.md#create-peering).

    Quando si crea un peering di rete virtuale tra una rete virtuale (Resource Manager) e una rete virtuale (versione classica), si configura solo il peering dalla rete virtuale (Resource Manager) alla rete virtuale (versione classica). Dopo aver creato il peering per la rete virtuale (Resource Manager), lo stato del peering è **Aggiornamento in corso**. Lo stato passa automaticamente a **Connesso** dopo alcuni secondi, poiché non è necessario creare un peering per la rete virtuale (versione classica). 
11. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
12. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete-portal) di questo articolo.

> [!NOTE]
> Anche se non è un argomento trattato in questa esercitazione, è possibile modificare gli script riportati nelle sezioni relative all'[interfaccia della riga di comando di Azure](#cli) e a [PowerShell](#powershell) di questo articolo in modo da riflettere le differenze descritte in questa sezione.

## <a name="permissions"></a>Autorizzazioni

Gli account usati per creare un peering di rete virtuale devono disporre del ruolo o delle autorizzazioni necessarie. Se, ad esempio, si deve eseguire il peering di due reti virtuali denominate VNet1 e VNet2, all'account deve essere assegnato il ruolo o le autorizzazioni minime seguenti per ogni rete virtuale:
    
|Rete virtuale|Modello di distribuzione|Ruolo|Autorizzazioni|
|---|---|---|---|
|VNet1|Gestione risorse|[Collaboratore di rete](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classico|[Collaboratore reti virtuali classiche](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|VNet2|Gestione risorse|[Collaboratore di rete](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classico|[Collaboratore reti virtuali classiche](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Altre informazioni sui [ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (solo Resource Manager).

## <a name="delete"></a>Eliminare risorse
Al termine di questa esercitazione, è necessario eliminare le risorse create, per non incorrere in costi di utilizzo. Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse all'interno di esso.

### <a name="delete-portal"></a>Portale di Azure

1. Nella casella di ricerca del portale immettere **myResourceGroup**. Nei risultati della ricerca fare clic su **myResourceGroup**.
2. Nel pannello **myResourceGroup** fare clic sull'icona **Elimina**.
3. Per confermare l'eliminazione, nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE** immettere **myResourceGroup** e quindi fare clic su **Elimina**.

### <a name="delete-cli"></a>

In una shell dei comandi di Linux, macOS o Windows immettere il comando che segue. In alternativa, fare clic sul pulsante Prova in alto a destra del blocco di codice seguente per avviare Cloud Shell. Usare quindi il pulsante Copia per copiare e incollare il codice di esempio in Cloud Shell.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Al prompt dei comandi di PowerShell immettere il comando seguente:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

- Acquisire familiarità con importanti [vincoli e comportamenti del peering di rete virtuale](virtual-network-manage-peering.md#about-peering) prima di creare un peering di rete virtuale per l'uso in produzione.
- Acquisire informazioni più dettagliate su tutte le [impostazioni per il peering di rete virtuale](virtual-network-manage-peering.md#create-peering).
- Acquisire informazioni su come [Creare una topologia di rete hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con il peering di rete virtuale.

