---
title: Creare un peering di rete virtuale di Azure - Resource Manager - sottoscrizioni diverse
titlesuffix: Azure Virtual Network
description: Informazioni su come creare un peering di rete virtuale tra reti virtuali distribuite tramite Resource Manager e incluse in sottoscrizioni di Azure diverse.
services: virtual-network
documentationcenter: ''
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: jdial;anavin
ms.openlocfilehash: 11ef6f2f09aacc175f095f7118ddb26ec77b2446
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268363"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Creare un peering di rete virtuale - Resource Manager, sottoscrizioni diverse

In questa esercitazione si apprenderà a creare un peering di rete virtuale tra reti virtuali distribuite tramite Resource Manager. Le reti virtuali sono incluse in sottoscrizioni diverse. Il peering di due reti virtuali consente alle risorse che si trovano in reti virtuali diverse di comunicare tra loro con la stessa larghezza di banda e la stessa latenza come se fossero nella stessa rete virtuale. Altre informazioni sul [Peering di rete virtuale](virtual-network-peering-overview.md).

I passaggi per creare un peering di rete virtuale sono diversi a seconda che le reti virtuali siano incluse nella stessa sottoscrizione o in sottoscrizioni diverse e in base al [modello di distribuzione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) con il quale sono state create le reti virtuali. Per informazioni su come creare un peering di rete virtuale in altri scenari, selezionare lo scenario nella tabella seguente:

|Modello di distribuzione di Azure  | Sottoscrizione di Azure  |
|--------- |---------|
|[Entrambi con Resource Manager](tutorial-connect-virtual-networks-portal.md) |Uguale|
|[Uno con Resource Manager, uno con una distribuzione classica](create-peering-different-deployment-models.md) |Uguale|
|[Uno con Resource Manager, uno con una distribuzione classica](create-peering-different-deployment-models-subscriptions.md) |Diversa|

Non è possibile creare un peering di rete virtuale tra due reti virtuali distribuite tramite il modello di distribuzione classica. Se è necessario connettere due reti virtuali, entrambe create tramite il modello di distribuzione classica, è possibile usare un [gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure.

Questa esercitazione consente di eseguire il peering di due reti virtuali nella stessa area. È anche possibile eseguire il peering di reti virtuali in diverse [aree supportate](virtual-network-manage-peering.md#cross-region). È consigliabile acquisire familiarità con [i requisiti e i vincoli del peering](virtual-network-manage-peering.md#requirements-and-constraints) prima di eseguire il peering di reti virtuali.

Per creare un peering di rete virtuale, è possibile usare il [portale di Azure](#portal), l'[interfaccia della riga di comando](#cli) di Azure, Azure [PowerShell](#powershell) o un [modello di Azure Resource Manager](#template). Facendo clic sui collegamenti degli strumenti precedenti, si passa direttamente alle procedure per la creazione di un peering di rete virtuale con lo strumento specifico.

## <a name="portal"></a>Creare un peering - Portale di Azure

Se le reti virtuali di cui si vuole eseguire il peering si trovano in sottoscrizioni associate a tenant di Azure Active Directory diversi, seguire la procedura nella sezione relativa all'interfaccia della riga di comando e a PowerShell di questo articolo. Il portale non supporta il peering di reti virtuali appartenenti a sottoscrizioni di tenant di Active Directory diversi.

La procedura seguente usa account diversi per ogni sottoscrizione. Se si usa un account dotato di autorizzazioni per entrambe le sottoscrizioni, è possibile usare lo stesso account per tutti i passaggi, ignorando i passaggi per la disconnessione dal portale e quelli per l'assegnazione a un altro utente delle autorizzazioni per le reti virtuali.

1. Accedere al [portale di Azure](https://portal.azure.com) come *UserA*. L'account con cui si esegue l'accesso deve avere le autorizzazioni necessarie per la creazione di un peering di rete virtuale. Per un elenco di autorizzazioni, vedere [Autorizzazioni di peering di reti virtuali](virtual-network-manage-peering.md#permissions).
2. Selezionare **+ Crea una risorsa**, quindi **Rete** e infine **Rete virtuale**.
3. Selezionare o immettere i valori di esempio seguenti per le impostazioni elencate di seguito e quindi selezionare **Crea**:
    - **Nome**: *myVnetA*
    - **Spazio degli indirizzi**: *10.0.0.0/16*
    - **Nome subnet**: *predefinito*
    - **Intervallo di indirizzi subnet**: *10.0.0.0/24*
    - **Sottoscrizione** Selezionare la sottoscrizione A.
    - **Gruppo di risorse**: Selezionare **Crea nuovo** e immettere *myResourceGroupA*
    - **Posizione**: *Stati Uniti orientali*
4. Nella casella **Cerca risorse** nella parte superiore del portale digitare *myVnetA*. Selezionare la voce **myVnetA** quando viene visualizzata nei risultati della ricerca. 
5. Selezionare **Controllo di accesso (IAM)** nell'elenco di opzioni a sinistra.
6. In **myVnetA - Controllo di accesso (IAM)** selezionare **+ Aggiungi assegnazione di ruolo**.
7. Selezionare **Collaboratore Rete** nella casella **Ruolo**.
8. Nella casella **Seleziona** selezionare *UserB* o digitare l'indirizzo di posta elettronica di UserB per cercare l'utente.
9. Selezionare **Salva**.
10. In **myVnetA - Controllo di accesso (IAM)** selezionare **Proprietà** nell'elenco di opzioni a sinistra. Copiare il valore di **ID RISORSA**, che verrà usato in un passaggio successivo. L'ID risorsa è simile a questo: /subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA.
11. Disconnettersi dal portale come UserA e quindi accedere come UserB.
12. Completare i passaggi 2 e 3, immettendo o selezionando i valori seguenti nel passaggio 3:

    - **Nome**: *myVnetB*
    - **Spazio degli indirizzi**: *10.1.0.0/16*
    - **Nome subnet**: *predefinito*
    - **Intervallo di indirizzi subnet**: *10.1.0.0/24*
    - **Sottoscrizione** Selezionare la sottoscrizione B.
    - **Gruppo di risorse**: Selezionare **Crea nuovo** e immettere *myResourceGroupB*
    - **Posizione**: *Stati Uniti orientali*

13. Nella casella **Cerca risorse** nella parte superiore del portale digitare *myVnetB*. Selezionare la voce **myVnetB** quando viene visualizzata nei risultati della ricerca.
14. In **myVnetB** selezionare **Proprietà** nell'elenco di opzioni a sinistra. Copiare il valore di **ID RISORSA**, che verrà usato in un passaggio successivo. L'ID risorsa è simile all'esempio seguente: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
15. Selezionare **Controllo di accesso (IAM)** in **myVnetB** e quindi completare i passaggi da 5 a 10 per myVnetB immettendo **UserA** al passaggio 8.
16. Disconnettersi dal portale come UserB e accedere come UserA.
17. Nella casella **Cerca risorse** nella parte superiore del portale digitare *myVnetA*. Selezionare la voce **myVnetA** quando viene visualizzata nei risultati della ricerca.
18. Selezionare **myVnetA**.
19. In **Impostazioni** selezionare **Peer**.
20. In **myVnetA - Peer** selezionare **+ Aggiungi**
21. In **Aggiungi peering** immettere o selezionare le opzioni seguenti e quindi fare clic su **OK**:
     - **Nome**: *myVnetAToMyVnetB*
     - **Modello di distribuzione della rete virtuale**:  Selezionare **Gestione risorse**.
     - **Conosco l'ID della risorsa**: selezionare questa casella.
     - **ID risorsa**: immettere l'ID risorsa indicato nel passaggio 14.
     - **Consenti accesso alla rete virtuale**: assicurarsi che sia selezionato **Abilitato**.
    Questa esercitazione non prevede l'uso di altre impostazioni. Per informazioni su tutte le impostazioni per il peering, vedere [Gestire i peering di rete virtuale](virtual-network-manage-peering.md#create-a-peering).
22. Il peering creato viene visualizzato poco dopo aver selezionato **OK** nel passaggio precedente. Nella colonna **STATO PEERING** relativa al peering **myVnetAToMyVnetB** creato è specificato lo stato **Avviato**. È stato effettuato il peering da myVnetA a myVnetB, ma ora è necessario eseguire il peering da myVnetB a myVnetA. Affinché le risorse delle reti virtuali possano comunicare tra loro, il peering deve essere creato in entrambe le direzioni.
23. Disconnettersi dal portale come UserA e accedere come UserB.
24. Completare di nuovo i passaggi da 17 a 21 per myVnetB. Nel passaggio 21 assegnare al peering il nome *myVnetBToMyVnetA*, selezionare *myVnetA* per **Rete virtuale** e immettere l'ID indicato nel passaggio 10 nella casella **ID risorsa**.
25. Pochi secondi dopo aver selezionato **OK** per creare il peering per myVnetB, viene visualizzato il peering **myVnetBToMyVnetA** appena creato, con l'indicazione **Connesso** nella colonna **STATO PEERING**.
26. Disconnettersi dal portale come UserB e accedere come UserA.
27. Completare di nuovo i passaggi da 17 a 19. Anche il valore di **STATO PEERING** per il peering **myVnetAToVNetB** è ora **Connesso**. Quando viene visualizzato **Connesso** nella colonna **STATO PEERING** per entrambe le reti virtuale in peering, significa che il peering è stato stabilito correttamente. Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
29. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete-portal) di questo articolo.

## <a name="cli"></a>Creare un peering - Interfaccia della riga di comando di Azure

Questa esercitazione usa account diversi per ogni sottoscrizione. Se si usa un account dotato di autorizzazioni per entrambe le sottoscrizioni, è possibile usare lo stesso account per tutti i passaggi, ignorando i passaggi per la disconnessione da Azure e rimuovendo le righe dello script per la creazione delle assegnazioni dei ruoli utente. Sostituire UserA@azure.com e UserB@azure.com in tutti gli script seguenti con i nomi utente usati per UserA e UserB. Se le reti virtuali si trovano in sottoscrizioni diverse e le sottoscrizioni sono associate a tenant di Azure Active Directory diversi, eseguire i passaggi seguenti prima di continuare:
 - Aggiungere l'utente di ogni tenant di Active Directory come [utente guest](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) nel tenant di Azure Active Directory opposto.
 - Ogni utente deve accettare l'invito per l'utente guest dal tenant di Azure Active Directory opposto.

Lo script seguente:

- Richiede l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure nel client Windows, vedere [Installare l'interfaccia della riga di comando di Azure in Windows](/cli/azure/install-azure-cli-windows).

Invece di installare l'interfaccia della riga di comando e le sue dipendenze, è possibile usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Fare clic sul pulsante **Prova** nello script seguente per richiamare un'istanza di Cloud Shell a cui è possibile accedere con l'account di Azure. 

1. Aprire una sessione dell'interfaccia della riga di comando e accedere ad Azure come UserA usando il comando `azure login`. L'account con cui si esegue l'accesso deve avere le autorizzazioni necessarie per la creazione di un peering di rete virtuale. Per un elenco di autorizzazioni, vedere [Autorizzazioni di peering di reti virtuali](virtual-network-manage-peering.md#permissions).
2. Copiare lo script seguente in un editor di testo nel PC, sostituire `<SubscriptionA-Id>` con l'ID di SubscriptionA, quindi copiare lo script modificato nella sessione dell'interfaccia della riga di comando e premere `Enter`. Se l'ID sottoscrizione non è noto, immettere il comando 'az account show'. Il valore per **id** nell'output è l'ID della sottoscrizione.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```
    
3. Disconnettersi da Azure come UserA usando il comando `az logout` e quindi accedere come UserB. L'account con cui si esegue l'accesso deve avere le autorizzazioni necessarie per la creazione di un peering di rete virtuale. Per un elenco di autorizzazioni, vedere [Autorizzazioni di peering di reti virtuali](virtual-network-manage-peering.md#permissions).
4. Creare myVnetB. Copiare il contenuto dello script indicato nel passaggio 2 in un editor di testo nel PC. Sostituire `<SubscriptionA-Id>` con l'ID di SubscriptionB. Modificare 10.0.0.0/16 in 10.1.0.0/16, modificare tutte le A in B e tutte le B in A. Copiare lo script modificato, incollarlo nella sessione dell'interfaccia della riga di comando e premere `Enter`. 
5. Disconnettersi da Azure come UserB e accedere come UserA.
6. Creare un peering di rete virtuale da myVnetA a myVnetB. Copiare il contenuto dello script seguente in un editor di testo nel PC. Sostituire `<SubscriptionB-Id>` con l'ID di SubscriptionB. Per eseguire lo script, copiare lo script modificato, incollarlo nella sessione dell'interfaccia della riga di comando e premere INVIO.

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Visualizzare lo stato di peering di myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Lo stato è **Avviato**. Viene modificato in **Connesso** dopo la creazione del peering a myVnetA da myVnetB.

8. Disconnettere UserA da Azure e accedere come UserB.
9. Creare il peering da myVnetB a myVnetA. Copiare il contenuto dello script riportato nel passaggio 6 in un editor di testo nel PC. Sostituire `<SubscriptionB-Id>` con l'ID per SubscriptionA e modificare tutte le A in B e tutte le B in A. Dopo avere apportato le modifiche, copiare lo script modificato nella sessione dell'interfaccia della riga di comando e premere `Enter`.
10. Visualizzare lo stato di peering di myVnetB. Copiare il contenuto dello script del passaggio 7 in un editor di testo nel PC. Modificare A in B per i nomi del gruppo di risorse e della rete virtuale, copiare lo script, incollare lo script modificato nell'interfaccia della riga di comando e quindi premere `Enter`. Lo stato di peering è **Connesso**. Lo stato di peering di myVnetA viene modificato in **Connesso** dopo la creazione del peering da myVnetB a myVnetA. È possibile accedere ad Azure di nuovo come UserA e ripetere il passaggio 7 per verificare lo stato di peering di myVnetA. 

    > [!NOTE]
    > Il peering viene stabilito correttamente solo dopo che lo stato di peering per entrambe le reti virtuali è **Connesso**.

11. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
12. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-cli) in questo articolo.

Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
 
## <a name="powershell"></a>Creare un peering - PowerShell

Questa esercitazione usa account diversi per ogni sottoscrizione. Se si usa un account dotato di autorizzazioni per entrambe le sottoscrizioni, è possibile usare lo stesso account per tutti i passaggi, ignorando i passaggi per la disconnessione da Azure e rimuovendo le righe dello script per la creazione delle assegnazioni dei ruoli utente. Sostituire UserA@azure.com e UserB@azure.com in tutti gli script seguenti con i nomi utente usati per UserA e UserB. Se le reti virtuali si trovano in sottoscrizioni diverse e le sottoscrizioni sono associate a tenant di Azure Active Directory diversi, eseguire i passaggi seguenti prima di continuare:
 - Aggiungere l'utente di ogni tenant di Active Directory come [utente guest](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) nel tenant di Azure Active Directory opposto.
 - Ogni utente deve accettare l'invito per l'utente guest dal tenant di Active Directory opposto.

1. Verificare di avere la versione 6.5.0 o successiva. A tale scopo, eseguire il comando `Get-Module -Name AzureRm` È consigliabile installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di PowerShell. Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell). 
2. Avviare una sessione di PowerShell.
3. In PowerShell accedere ad Azure come UserA immettendo il comando `Connect-AzureRmAccount`. L'account con cui si esegue l'accesso deve avere le autorizzazioni necessarie per la creazione di un peering di rete virtuale. Per un elenco di autorizzazioni, vedere [Autorizzazioni di peering di reti virtuali](virtual-network-manage-peering.md#permissions).
4. Creare un gruppo di risorse e una rete virtuale A. Copiare lo script seguente in un editor di testo nel PC. Sostituire `<SubscriptionA-Id>` con l'ID di SubscriptionA. Se l'ID sottoscrizione non è noto, immettere il comando `Get-AzureRmSubscription` per visualizzarlo. Il valore di **Id** nell'output restituito è l'ID sottoscrizione. Per eseguire lo script, copiare lo script modificato, incollarlo in PowerShell e quindi premere `Enter`.

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Disconnettere UserA da Azure e accedere come UserB. L'account con cui si esegue l'accesso deve avere le autorizzazioni necessarie per la creazione di un peering di rete virtuale. Per un elenco di autorizzazioni, vedere [Autorizzazioni di peering di reti virtuali](virtual-network-manage-peering.md#permissions).
6. Copiare il contenuto dello script del passaggio 4 in un editor di testo nel PC. Sostituire `<SubscriptionA-Id>` con l'ID della sottoscrizione B. Modificare 10.0.0.0/16 in 10.1.0.0/16. Modificare tutte le A in B e tutte le B in A. Per eseguire lo script, copiare lo script modificato, incollarlo in PowerShell e quindi premere `Enter`.
7. Disconnettere UserB da Azure e accedere come UserA.
8. Creare il peering da myVnetA a myVnetB. Copiare lo script seguente in un editor di testo nel PC. Sostituire `<SubscriptionB-Id>` con l'ID della sottoscrizione B. Per eseguire lo script, copiare lo script modificato, incollarlo in PowerShell e quindi premere `Enter`.
 
    ```powershell
    # Peer myVnetA to myVnetB.
    $vNetA=Get-AzureRmVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vNetA `
      -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
    ```

9. Visualizzare lo stato di peering di myVnetA.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Lo stato è **Avviato**. Viene modificato in **Connesso** dopo la configurazione del peering a myVnetA da myVnetB.

10. Disconnettere UserA da Azure e accedere come UserB.
11. Creare il peering da myVnetB a myVnetA. Copiare il contenuto dello script del passaggio 8 in un editor di testo nel PC. Sostituire `<SubscriptionB-Id>` con l'ID della sottoscrizione A e modificare tutte le A in B e tutte le B in A. Per eseguire lo script, copiare lo script modificato, incollarlo in PowerShell e quindi premere `Enter`.
12. Visualizzare lo stato di peering di myVnetB. Copiare il contenuto dello script del passaggio 9 in un editor di testo nel PC. Modificare le A in B per i nomi del gruppo di risorse e della rete virtuale. Per eseguire lo script, incollare lo script modificato in PowerShell e quindi premere `Enter`. Lo stato è **Connesso**. Lo stato di peering di **myVnetA** viene modificato in **Connesso** dopo la creazione del peering da **myVnetB** a **myVnetA**. È possibile accedere ad Azure di nuovo come UserA e ripetere il passaggio 9 per verificare lo stato di peering di myVnetA. 

    > [!NOTE]
    > Il peering viene stabilito correttamente solo dopo che lo stato di peering per entrambe le reti virtuali è **Connesso**.

    Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
14. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-powershell) in questo articolo.

## <a name="template"></a>Creare un peering - Modello di Resource Manager

Se le reti virtuali si trovano in sottoscrizioni diverse e le sottoscrizioni sono associate a tenant di Azure Active Directory diversi, eseguire i passaggi seguenti prima di continuare:
 - Aggiungere l'utente di ogni tenant di Active Directory come [utente guest](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) nel tenant di Azure Active Directory opposto.
 - Ogni utente deve accettare l'invito per l'utente guest dal tenant di Active Directory opposto.
 
1. Per creare una rete virtuale e assegnare le [autorizzazioni](virtual-network-manage-peering.md#permissions) appropriate, completare i passaggi descritti nelle sezioni di questo articolo relative al [portale](#portal), all'[interfaccia della riga di comando di Azure](#cli) o a [PowerShell](#powershell).
2. Salvare il testo seguente in un file nel computer locale. Sostituire `<subscription ID>` con l'ID della sottoscrizione di UserA. È ad esempio possibile salvare il file come vnetpeeringA.json.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
    }
    ```

3. Accedere ad Azure come UserA e distribuire il modello usando il [portale](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-templates-stored-locally) o l'[interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Specificare il nome file in cui si è salvato il testo JSON di esempio nel passaggio 2.
4. Copiare il codice JSON di esempio dal passaggio 2 a un file nel computer e apportare modifiche alle righe che iniziano con:
    - **name**: sostituire *myVnetA/myVnetAToMyVnetB* con *myVnetB/myVnetBToMyVnetA*.
    - **id**: sostituire `<subscription ID>` con l'ID della sottoscrizione di UserB e sostituire *myVnetB* con *myVnetA*.
5. Completare di nuovo il passaggio 3, dopo avere effettuato l'accesso ad Azure come UserB.
6. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
7. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete) di questo articolo usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="delete"></a>Eliminare risorse
Al termine di questa esercitazione, è necessario eliminare le risorse create, per non incorrere in costi di utilizzo. Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse all'interno di esso.

### <a name="delete-portal"></a>Portale di Azure

1. Accedere al portale di Azure come UserA.
2. Nella casella di ricerca del portale immettere **myResourceGroupA**. Nei risultati della ricerca selezionare **myResourceGroupA**.
3. Selezionare **Elimina**.
4. Per confermare l'eliminazione, nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE** immettere **myResourceGroupA** e quindi selezionare **Elimina**.
5. Disconnettersi dal portale come UserA e accedere come UserB.
6. Completare i passaggi da 2 a 4 per myResourceGroupB.

### <a name="delete-cli"></a>

1. Accedere ad Azure come UserA ed eseguire il comando seguente:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```
2. Disconnettersi da Azure come UserA e accedere come UserB.
3. Eseguire il comando seguente:

    ```azurecli-interactive
    az group delete --name myResourceGroupB --yes
    ```

### <a name="delete-powershell"></a>PowerShell

1. Accedere ad Azure come UserA ed eseguire il comando seguente:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -force
    ```

2. Disconnettersi da Azure come UserA e accedere come UserB.
3. Eseguire il comando seguente:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupB -force
    ```

## <a name="next-steps"></a>Passaggi successivi

- Acquisire familiarità con importanti [vincoli e comportamenti del peering di rete virtuale](virtual-network-manage-peering.md#requirements-and-constraints) prima di creare un peering di rete virtuale per l'uso in produzione.
- Acquisire informazioni più dettagliate su tutte le [impostazioni per il peering di rete virtuale](virtual-network-manage-peering.md#create-a-peering).
- Acquisire informazioni su come [Creare una topologia di rete hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con il peering di rete virtuale.
