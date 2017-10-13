---
title: Creare un peering di rete virtuale di Azure - Resource Manager - Sottoscrizioni diverse | Microsoft Docs
description: Informazioni su come creare un peering di rete virtuale tra reti virtuali distribuite tramite Resource Manager e incluse in sottoscrizioni di Azure diverse.
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
ms.openlocfilehash: 13e9e83dc277d8e88ab46d8d51b3f4a70ccd3b46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Creare un peering di rete virtuale - Resource Manager, sottoscrizioni diverse 

In questa esercitazione si apprenderà a creare un peering di rete virtuale tra reti virtuali distribuite tramite Resource Manager. Le reti virtuali sono incluse in sottoscrizioni diverse. Il peering di due reti virtuali consente alle risorse che si trovano in reti virtuali diverse di comunicare tra loro con la stessa larghezza di banda e la stessa latenza come se fossero nella stessa rete virtuale. Altre informazioni sul [Peering di rete virtuale](virtual-network-peering-overview.md). 

I passaggi per creare un peering di rete virtuale sono diversi a seconda che le reti virtuali siano incluse nella stessa sottoscrizione o in sottoscrizioni diverse e in base al [modello di distribuzione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) con il quale sono state create le reti virtuali. Per informazioni su come creare un peering di rete virtuale in altri scenari, fare clic sullo scenario nella tabella seguente:

|Modello di distribuzione di Azure  | Sottoscrizione di Azure  |
|--------- |---------|
|[Entrambi con Resource Manager](virtual-network-create-peering.md) |Uguale|
|[Uno con Resource Manager, uno con una distribuzione classica](create-peering-different-deployment-models.md) |Uguale|
|[Uno con Resource Manager, uno con una distribuzione classica](create-peering-different-deployment-models-subscriptions.md) |Diversa|

È possibile creare un peering di rete virtuale solo tra due reti virtuali che si trovano nella stessa area di Azure.

  > [!WARNING]
  > La creazione di un peering di rete virtuale tra reti virtuali in aree diverse è attualmente in anteprima. È possibile registrare la sottoscrizione per l'anteprima di seguito. I peering di rete virtuale creati in questo scenario potrebbero non avere lo stesso livello di disponibilità e affidabilità di quelli creati in scenari di rilascio con disponibilità generale. I peering di rete virtuale creati in questo scenario non sono supportati, possono presentare funzionalità limitate e potrebbero non essere disponibili in tutte le aree di Azure. Per ricevere le notifiche più aggiornate su disponibilità e stato della funzionalità, vedere la pagina [Aggiornamenti della rete virtuale di Azure](https://azure.microsoft.com/updates/?product=virtual-network) .

Non è possibile creare un peering di rete virtuale tra due reti virtuali distribuite tramite il modello di distribuzione classica. Se è necessario connettere reti virtuali create entrambe con il modello di distribuzione classica o che si trovano in aree di Azure diverse, è possibile usare il [Gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure per connettere le reti virtuali. 

Per creare un peering di rete virtuale, è possibile usare il [portale di Azure](#portal), l'[interfaccia della riga di comando](#cli) di Azure, Azure [PowerShell](#powershell) o un [modello di Azure Resource Manager](#template). Facendo clic sui collegamenti degli strumenti precedenti, si passa direttamente alle procedure per la creazione di un peering di rete virtuale con il determinato strumento.

## <a name="register"></a>Eseguire la registrazione per l'anteprima del peering reti virtuali globale

Per creare un peering di reti virtuali tra aree, eseguire la registrazione per l'anteprima e completare la procedura seguente per entrambe le sottoscrizioni contenenti le reti virtuali per cui si desidera creare il peering. L'unico strumento che consente di registrare per l'anteprima è PowerShell.

1. Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di PowerShell. Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell).
2. Avviare una sessione di PowerShell e accedere ad Azure usando il comando `Login-AzureRmAccount`.
3. Registrare la sottoscrizione all'anteprima immettendo i comandi seguenti:

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
    Non completare la procedura nelle sezioni del portale, dell'interfaccia della riga di comando di Azure o di PowerShell di questo articolo finché l'output **RegistrationState** che si riceve dopo aver immesso il comando seguente non è **Registrato** per entrambe le sottoscrizioni:

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```
  > [!WARNING]
  > La creazione di un peering di rete virtuale tra reti virtuali in aree diverse è attualmente in anteprima. I peering di rete virtuale creati in questo scenario possono presentare funzionalità limitate e potrebbero non essere disponibili in tutte le aree di Azure. Per ricevere le notifiche più aggiornate su disponibilità e stato della funzionalità, vedere la pagina [Aggiornamenti della rete virtuale di Azure](https://azure.microsoft.com/updates/?product=virtual-network) .

## <a name="portal"></a>Creare un peering - Portale di Azure

Questa esercitazione usa account diversi per ogni sottoscrizione. Se si usa un account dotato di autorizzazioni per entrambe le sottoscrizioni, è possibile usare lo stesso account per tutti i passaggi, ignorando i passaggi per la disconnessione dal portale e quelli per l'assegnazione delle autorizzazioni per le reti virtuali a un altro utente.

1. Accedere al [portale di Azure](https://portal.azure.com) come UserA. L'account con cui si esegue l'accesso deve avere le autorizzazioni necessarie per la creazione di un peering di rete virtuale. Vedere la sezione [Autorizzazioni](#permissions) di questo articolo per informazioni dettagliate.
2. Fare clic su **+ Nuovo**, **Rete** e quindi **Rete virtuale**.
3. Nel pannello **Crea rete virtuale** immettere o selezionare i valori necessari per le impostazioni seguenti e quindi fare clic su **Crea**:
    - **Nome**: *myVnetA*
    - **Spazio indirizzi**: *10.0.0.0/16*
    - **Nome subnet**: *predefinito*
    - **Intervallo di indirizzi subnet**: *10.0.0.0/24*
    - **Sottoscrizione**: selezionare la sottoscrizione A.
    - **Gruppo di risorse**: selezionare **Crea nuovo** e immettere *myResourceGroupA*
    - **Località**: *Stati Uniti orientali*
4. Nella casella **Cerca risorse** nella parte superiore del portale digitare *myVnetA*. Fare clic su **myVnetA** quando viene visualizzato nei risultati della ricerca. Viene visualizzato un pannello per la rete virtuale **myVnetA**.
5. Nel pannello **myVnetA** visualizzato fare clic su **Controllo di accesso (IAM)** nell'elenco verticale di opzioni sul lato sinistro del pannello.
6. Nel pannello **myVnetA - Controllo di accesso (IAM)** visualizzato fare clic su **+ Aggiungi**.
7. Nel pannello **Aggiungi autorizzazioni** visualizzato selezionare **Collaboratore rete** nella casella **Ruolo**.
8. Nella casella **Seleziona** selezionare UserB o digitare l'indirizzo e-mail di UserB per cercarlo. L'elenco di utenti mostrato è tratto dallo stesso tenant di Azure Active Directory della rete virtuale per la quale si sta configurando il peering.
9. Fare clic su **Salva**.
10. Nel pannello **myVnetA - Controllo di accesso (IAM)** fare clic su **Proprietà** nell'elenco verticale di opzioni sul lato sinistro del pannello. Copiare il valore di **ID RISORSA**, che verrà usato in un passaggio successivo. L'ID risorsa è simile a questo: /subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA.
11. Disconnettersi dal portale come UserA e quindi accedere come UserB.
12. Completare i passaggi 2 e 3, immettendo o selezionando i valori seguenti nel passaggio 3:

    - **Nome**: *myVnetB*
    - **Spazio indirizzi**: *10.1.0.0/16*
    - **Nome subnet**: *predefinito*
    - **Intervallo di indirizzi subnet**: *10.1.0.0/24*
    - **Sottoscrizione**: selezionare la sottoscrizione B.
    - **Gruppo di risorse**: selezionare **Crea nuovo** e immettere *myResourceGroupB*
    - **Località**: *Stati Uniti orientali*

13. Nella casella **Cerca risorse** nella parte superiore del portale digitare *myVnetB*. Fare clic su **myVnetB** quando viene visualizzato nei risultati della ricerca. Viene visualizzato un pannello per la rete virtuale **myVnetB**.
14. Nel pannello **myVnetB** visualizzato fare clic su **Proprietà** nell'elenco verticale di opzioni sul lato sinistro del pannello. Copiare il valore di **ID RISORSA**, che verrà usato in un passaggio successivo. L'ID risorsa è simile a questo: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
15. Fare clic su **Controllo di accesso (IAM)** nel pannello **myVnetB** e quindi completare i passaggi da 5 a 10 per myVnetB, immettendo **UserA** nel passaggio 8.
16. Disconnettersi dal portale come UserB e accedere come UserA.
17. Nella casella **Cerca risorse** nella parte superiore del portale digitare *myVnetA*. Fare clic su **myVnetA** quando viene visualizzato nei risultati della ricerca. Viene visualizzato un pannello per la rete virtuale **myVnet**.
18. Fare clic su **myVnetA**.
19. Nel pannello **myVnetA** visualizzato fare clic su **Peer** nell'elenco di opzioni verticale sul lato sinistro del pannello.
20. Nel pannello **myVnetA - Peer** visualizzato fare clic su **+ Aggiungi**
21. Nel pannello **Aggiungi peering** visualizzato immettere o selezionare le opzioni seguenti e quindi fare clic su **OK**:
     - **Nome**: *myVnetAToMyVnetB*
     - **Modello di distribuzione della rete virtuale**: selezionare **Resource Manager**.
     - **Conosco l'ID della risorsa**: selezionare questa casella di controllo.
     - **ID risorsa**: immettere l'ID risorsa indicato nel passaggio 14.
     - **Consenti accesso alla rete virtuale:** assicurarsi che sia selezionato **Abilitato**.
    Questa esercitazione non prevede l'uso di altre impostazioni. Per informazioni su tutte le impostazioni per il peering, vedere [Gestire i peering di rete virtuale](virtual-network-manage-peering.md#create-a-peering).
22. Dopo aver fatto clic su **OK** nel passaggio precedente, il pannello **Aggiungi peering** si chiude e viene visualizzato di nuovo il pannello **myVnetA - Peer**. Dopo alcuni secondi, il peering creato viene visualizzato nel pannello. Nella colonna **STATO PEERING** relativa al peering **myVnetAToMyVnetB** creato è specificato lo stato **Avviato**. È stato effettuato il peering da myVnetA a myVnetB, ma ora è necessario eseguire il peering da myVnetB a myVnetA. Affinché le risorse delle reti virtuali possano comunicare tra loro, il peering deve essere creato in entrambe le direzioni.
23. Disconnettersi dal portale come UserA e accedere come UserB.
24. Completare di nuovo i passaggi da 17 a 21 per myVnetB. Nel passaggio 21 assegnare al peering il nome *myVnetBToMyVnetA*, selezionare *myVnetA* per **Rete virtuale** e immettere l'ID indicato nel passaggio 10 nella casella **ID risorsa**.
25. Pochi secondi dopo aver fatto clic su **OK** per creare il peering per myVnetB, viene visualizzato il peering **myVnetBToMyVnetA** appena creato, che specifica **Connesso** nella colonna **STATO PEERING**.
26. Disconnettersi dal portale come UserB e accedere come UserA.
27. Completare di nuovo i passaggi da 17 a 19. Anche il valore di **STATO PEERING** per il peering **myVnetAToVNetB** è ora **Connesso**. Quando viene visualizzato **Connesso** nella colonna **STATO PEERING** per entrambe le reti virtuale in peering, significa che il peering è stato stabilito correttamente. Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
28. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
29. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete-portal) di questo articolo.

## <a name="cli"></a>Creare un peering - Interfaccia della riga di comando di Azure

Questa esercitazione usa account diversi per ogni sottoscrizione. Se si usa un account dotato di autorizzazioni per entrambe le sottoscrizioni, è possibile usare lo stesso account per tutti i passaggi, ignorando i passaggi per la disconnessione da Azure e rimuovendo le righe dello script per la creazione delle assegnazioni dei ruoli utente. Sostituire UserA@azure.com e UserB@azure.com in tutti gli script seguenti con i nomi utente usati per UserA e UserB.

Lo script seguente:

- Richiede l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure nel client Windows, vedere [Running the Azure CLI in Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows). 

Invece di installare l'interfaccia della riga di comando e le sue dipendenze, è possibile usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Fare clic sul pulsante **Prova** nello script seguente per richiamare un'istanza di Cloud Shell a cui è possibile accedere con l'account Azure personale. 

1. Aprire una sessione dell'interfaccia della riga di comando e accedere ad Azure come UserA usando il comando `azure login`. L'account con cui si esegue l'accesso deve avere le autorizzazioni necessarie per la creazione di un peering di rete virtuale. Vedere la sezione [Autorizzazioni](#permissions) di questo articolo per informazioni dettagliate.
2. Copiare lo script seguente in un editor di testo nel PC, sostituire `<SubscriptionA-Id>` con l'ID di SubscriptionA, quindi copiare lo script modificato nella sessione dell'interfaccia della riga di comando e premere `Enter`. Se l'ID sottoscrizione non è noto, immettere il comando 'az account show'. Il valore per **id** nell'output è l'ID sottoscrizione.

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
    
     L'assegnazione delle autorizzazioni per UserB non è un requisito. Il peering può essere stabilito anche se gli utenti generano singolarmente richieste di peering per le rispettive reti virtuali, purché le richieste corrispondano. L'aggiunta di un utente con privilegi della rete virtuale myVnetB come collaboratore di rete nella rete virtuale locale semplifica la configurazione.
3. Disconnettersi da Azure come UserA usando il comando `az logout` e quindi accedere come UserB. L'account con cui si esegue l'accesso deve avere le autorizzazioni necessarie per la creazione di un peering di rete virtuale. Vedere la sezione [Autorizzazioni](#permissions) di questo articolo per informazioni dettagliate.
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
12. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-cli) in questo articolo.

Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
 
## <a name="powershell"></a>Creare un peering - PowerShell

Questa esercitazione usa account diversi per ogni sottoscrizione. Se si usa un account dotato di autorizzazioni per entrambe le sottoscrizioni, è possibile usare lo stesso account per tutti i passaggi, ignorando i passaggi per la disconnessione da Azure e rimuovendo le righe dello script per la creazione delle assegnazioni dei ruoli utente. Sostituire UserA@azure.com e UserB@azure.com in tutti gli script seguenti con i nomi utente usati per UserA e UserB.

1. Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di PowerShell. Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell).
2. Avviare una sessione di PowerShell.
3. In PowerShell accedere ad Azure come UserA immettendo il comando `login-azurermaccount`. L'account con cui si esegue l'accesso deve avere le autorizzazioni necessarie per la creazione di un peering di rete virtuale. Vedere la sezione [Autorizzazioni](#permissions) di questo articolo per informazioni dettagliate.
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

    L'assegnazione delle autorizzazioni per UserB non è un requisito. Il peering può essere stabilito anche se gli utenti generano singolarmente richieste di peering per le rispettive reti virtuali, purché le richieste corrispondano. L'aggiunta di un utente con privilegi dell'altra rete virtuale come utente nella rete virtuale locale semplifica la configurazione.
5. Disconnettere UserA da Azure e accedere come UserB. L'account con cui si esegue l'accesso deve avere le autorizzazioni necessarie per la creazione di un peering di rete virtuale. Vedere la sezione [Autorizzazioni](#permissions) di questo articolo per informazioni dettagliate.
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

    Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

13. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
14. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-powershell) in questo articolo.

## <a name="template"></a>Creare un peering - Modello di Resource Manager

1. Completare i passaggi descritti nelle sezioni di questo articolo relative al [portale](#portal), all'[interfaccia della riga di comando di Azure](#cli) o a [PowerShell](#powershell) per creare una rete virtuale e assegnare le [autorizzazioni](#permissions) appropriate all'account in ogni sottoscrizione.
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

3. Accedere ad Azure come UserA e distribuire il modello usando il [portale](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-a-template-from-your-local-machine) o l'[interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Specificare il nome file in cui si è salvato il testo JSON di esempio nel passaggio 2.
4. Copiare il codice JSON di esempio dal passaggio 2 a un file nel computer e apportare modifiche alle righe che iniziano con:
    - **name**: sostituire *myVnetA/myVnetAToMyVnetB* con *myVnetB/myVnetBToMyVnetA*.
    - **id**: sostituire `<subscription ID>` con l'ID della sottoscrizione di UserB e sostituire *myVnetB* con *myVnetA*.
5. Completare di nuovo il passaggio 3, dopo avere effettuato l'accesso ad Azure come UserB.
6. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
7. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete) di questo articolo usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="permissions"></a>Autorizzazioni

Gli account usati per creare un peering di rete virtuale devono disporre del ruolo o delle autorizzazioni necessarie. Ad esempio, per eseguire il peering di due reti virtuali denominate **myVnetA** e **myVnetB**, è necessario assegnare all'account il ruolo minimo o le autorizzazioni minime seguenti per ogni rete virtuale:
    
|Rete virtuale|Ruolo|Autorizzazioni|
|---|---|---|
|myVnetA|[Collaboratore di rete](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|myVnetB|[Collaboratore di rete](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

Altre informazioni sui [ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (solo Resource Manager).

## <a name="delete"></a>Eliminare risorse
Al termine di questa esercitazione, è necessario eliminare le risorse create, per non incorrere in costi di utilizzo. Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse all'interno di esso.

### <a name="delete-portal"></a>Portale di Azure

1. Accedere al portale di Azure come UserA.
2. Nella casella di ricerca del portale immettere **myResourceGroupA**. Nei risultati della ricerca fare clic su **myResourceGroupA**.
3. Nel pannello **myResourceGroupA** fare clic sull'icona **Elimina**.
4. Per confermare l'eliminazione, nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE** immettere **myResourceGroupA** e quindi fare clic su **Elimina**.
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
