---
title: Visualizzare la topologia di una rete virtuale di Azure | Microsoft Docs
description: Informazioni su come visualizzare le risorse in una rete virtuale e le relazioni tra di esse.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: jdial
ms.openlocfilehash: a9cddf3f8091115f7cd39999e8c52d87ead4af07
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786830"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Visualizzare la topologia di una rete virtuale di Azure

In questo articolo si apprenderà come visualizzare le risorse in una rete virtuale di Microsoft Azure e le relazioni tra di esse. Ad esempio, una rete virtuale contiene più subnet, che a loro volta contengono risorse, come Macchine virtuali (VM) di Azure. Le VM hanno una o più interfacce di rete. Ogni subnet può avere un gruppo di sicurezza di rete e una tabella di route associata a tale gruppo. La funzionalità di topologia di Azure Network Watcher consente di visualizzare tutte le risorse presenti in una rete virtuale, le risorse associate a tali risorse e le relazioni tra di esse.

Per visualizzare una topologia è possibile usare il [portale di Azure](#azure-portal), l'[interfaccia della riga di comando di Azure](#azure-cli) o [PowerShell](#powershell).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name = "azure-portal"></a>Visualizzare la topologia tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) con un account che dispone delle [autorizzazioni](required-rbac-permissions.md) necessarie.
2. Nell'angolo in alto a sinistra del portale selezionare **Tutti i servizi**.
3. Nella casella di filtro **Tutti i servizi** immettere *Network Watcher*. Quando **Network Watcher** viene visualizzato tra i risultati, selezionarlo.
4. Selezionare **Topologia**. Per generare una topologia è necessario che sia presente un'istanza di Network Watcher nella stessa area della rete virtuale per cui si vuole generare la topologia. Se in tale area non è abilitata un'istanza di Network Watcher, vengono create automaticamente istanze di Network Watcher in tutte le aree. Le istanze di Network Watcher vengono create in un gruppo di risorse denominato **NetworkWatcherRG**.
5. Selezionare una sottoscrizione e il gruppo di risorse di una rete virtuale per cui visualizzare la topologia e quindi selezionare la rete virtuale. L'immagine seguente illustra una topologia relativa a una rete virtuale denominata *MyVnet*, nel gruppo di risorse denominato *MyResourceGroup*:

    ![Visualizzare la topologia](./media/view-network-topology/view-topology.png)

    Come illustrato nell'immagine precedente, la rete virtuale contiene tre subnet. In una delle subnet è implementata una macchina virtuale a cui sono associati un'interfaccia di rete e un indirizzo IP pubblico. Alle altre due subnet è associata una tabella di route. Ogni tabella contiene due route. A una delle subnet è associato un gruppo di sicurezza di rete. Le informazioni sulla topologia vengono visualizzate solo per le risorse che:
    
    - Si trovano all'interno dello stesso gruppo di risorse e della stessa area della rete virtuale *myVnet*. Ad esempio, un gruppo di sicurezza di rete incluso in un gruppo di risorse diverso da *MyResourceGroup* non viene visualizzato, anche se tale gruppo è associato a una subnet nella rete virtuale *MyVnet*.
    - Si trovano all'interno della rete virtuale *myVnet* o sono associate alle risorse incluse in tale rete. Ad esempio, un gruppo di sicurezza di rete che non è associato a una subnet o a un'interfaccia di rete nella rete virtuale *myVnet* non viene visualizzato, anche se tale gruppo è incluso nel gruppo di risorse *MyResourceGroup*.

   Nell'immagine è illustrata la topologia relativa alla rete virtuale creata dopo la distribuzione dell'**esempio di script per instradare il traffico attraverso un'appliance virtuale di rete**, che è possibile distribuire tramite l'[interfaccia della riga di comando di Azure](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) o [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Selezionare **Scarica topologia** per scaricare l'immagine come file modificabile, con estensione svg.

Le risorse visualizzate nel diagramma sono un sottoinsieme dei componenti della rete virtuale. Nel diagramma è ad esempio illustrato un gruppo di sicurezza di rete, ma le regole di sicurezza all'interno di tale gruppo non sono visualizzate. Anche se non differenziate nel diagramma, le linee rappresentano due diversi tipi di relazione: *contenimento* o *associazione*. Per visualizzare l'elenco completo delle risorse nella rete virtuale e il tipo di relazione tra le risorse, generare la topologia usando [PowerShell](#powershell) o l'[interfaccia della riga di comando di Azure](#azure-cli).

## <a name = "azure-cli"></a>Visualizzare la topologia tramite l'interfaccia della riga di comando di Azure

I comandi descritti nei passaggi seguenti possono essere eseguiti in due modi:
- In Azure Cloud Shell, selezionando **Prova** nell'angolo in alto a destra di ogni comando. Azure Cloud Shell è una shell interattiva gratuita in cui sono disponibili gli strumenti comuni di Azure preinstallati e configurati per l'uso con un account.
- Tramite l'interfaccia della riga di comando dal computer. Se si adotta questa seconda soluzione, per eseguire i passaggi descritti in questo articolo è necessario disporre dell'interfaccia della riga di comando di Azure versione 2.0.31 o successiva. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

L'account usato deve avere le [autorizzazioni](required-rbac-permissions.md) necessarie.

1. Se è già presente un'istanza di Network Watcher nella stessa area della rete virtuale per cui si vuole creare una topologia, andare al passaggio 3. Creare un gruppo di risorse in cui includere un'istanza di Network Watcher con il comando [az group create](/cli/azure/group). L'esempio seguente crea il gruppo di risorse nell'area *eastus*:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Creare un'istanza di Network Watcher con il comando [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure). L'esempio seguente crea un'istanza di Network Watcher nell'area *eastus*:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Visualizzare la topologia con il comando [az network watcher show-topology](/cli/azure/network/watcher#az-network-watcher-show-topology). L'esempio seguente visualizza la topologia per un gruppo di risorse denominato *myResourceGroup*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Le informazioni sulla topologia restituite riguardano solo le risorse incluse nel gruppo *MyResourceGroup* e nella stessa area dell'istanza di Network Watcher. Ad esempio, un gruppo di sicurezza di rete incluso in un gruppo di risorse diverso da *MyResourceGroup* non viene visualizzato, anche se tale gruppo è associato a una subnet nella rete virtuale *MyVnet*.

   Nelle sezioni Relazioni e [Proprietà](#properties) sono disponibili altre informazioni sull'output restituito. Se non si ha una rete virtuale per cui visualizzare una topologia, è possibile crearne una tramite l'esempio di script per [instradare il traffico attraverso un'appliance virtuale di rete](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Per visualizzare un diagramma della topologia e scaricarlo in un file modificabile, usare il [portale](#azure-portal).

## <a name = "powershell"></a>Visualizzare la topologia tramite PowerShell

I comandi descritti nei passaggi seguenti possono essere eseguiti in due modi:
- In Azure Cloud Shell, selezionando **Prova** nell'angolo in alto a destra di ogni comando. Azure Cloud Shell è una shell interattiva gratuita in cui sono disponibili gli strumenti comuni di Azure preinstallati e configurati per l'uso con un account.
- Tramite PowerShell dal computer. Se si esegue PowerShell dal computer, questo articolo è necessario Azure PowerShell `Az` modulo. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

L'account usato deve avere le [autorizzazioni](required-rbac-permissions.md) necessarie.

1. Se è già presente un'istanza di Network Watcher nella stessa area della rete virtuale per cui si vuole creare una topologia, andare al passaggio 3. Creare un gruppo di risorse per contenere un network watcher con [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). L'esempio seguente crea il gruppo di risorse nell'area *eastus*:

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Creare un'istanza di network watcher con [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). L'esempio seguente crea un'istanza di Network Watcher nell'area eastus:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Recuperare un'istanza di Network Watcher con [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). L'esempio seguente recupera un'istanza di Network Watcher nell'area eastus:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Recuperare una topologia con [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). L'esempio seguente recupera una topologia relativa a una rete virtuale nel gruppo di risorse denominato *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Le informazioni sulla topologia restituite riguardano solo le risorse incluse nel gruppo *MyResourceGroup* e nella stessa area dell'istanza di Network Watcher. Ad esempio, un gruppo di sicurezza di rete incluso in un gruppo di risorse diverso da *MyResourceGroup* non viene visualizzato, anche se tale gruppo è associato a una subnet nella rete virtuale *MyVnet*.

   Nelle sezioni Relazioni e [Proprietà](#properties) sono disponibili altre informazioni sull'output restituito. Se non si ha una rete virtuale per cui visualizzare una topologia, è possibile crearne una tramite l'esempio di script per [instradare il traffico attraverso un'appliance virtuale di rete](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Per visualizzare un diagramma della topologia e scaricarlo in un file modificabile, usare il [portale](#azure-portal).

## <a name="relationships"></a>Relazioni

Tutte le risorse restituite in una topologia sono correlate in base a uno dei tipi di relazione seguenti:

| Tipo di relazione | Esempio                                                                                                |
| ---               | ---                                                                                                    |
| Contenimento       | Una rete virtuale contiene una subnet. Una subnet contiene un'interfaccia di rete.                            |
| Associazione        | Un'interfaccia di rete è associata a una VM. Un indirizzo IP pubblico è associato a un'interfaccia di rete. |

## <a name="properties"></a>Properties

Tutte le risorse restituite in una topologia hanno le proprietà seguenti:

- **Nome**: nome della risorsa.
- **Id**: URI della risorsa.
- **Località**: area di Azure in cui si trova la risorsa.
- **Associazioni**: elenco delle associazioni all'oggetto di riferimento. Ogni associazione ha le proprietà seguenti:
    - **AssociationType**: fa riferimento alla relazione tra l'oggetto figlio e l'oggetto padre. I valori validi sono *Contains* o *Associated*.
    - **Nome**: nome della risorsa a cui si fa riferimento.
    - **ResourceId**: URI della risorsa di riferimento nell'associazione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [diagnosticare un problema di filtro del traffico di rete da o verso una macchina virtuale](diagnose-vm-network-traffic-filtering-problem.md) usando la funzionalità di verifica del flusso IP di Network Watcher
- Informazioni su come [diagnosticare un problema di routing del traffico di rete da una macchina virtuale](diagnose-vm-network-routing-problem.md) usando la funzionalità Hop successivo di Network Watcher
