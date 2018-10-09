---
title: Diagnosticare i problemi di routing di una macchina virtuale di Azure | Microsoft Docs
description: Come diagnosticare un problema di routing di una macchina virtuale visualizzando le route valide per una macchina virtuale.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: jdial
ms.openlocfilehash: 695d5f1507f766cf0a2ad96d7dcd25f45f98c20e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994718"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnosticare un problema di routing di una macchina virtuale

Questo articolo illustra come diagnosticare un problema relativo al routing visualizzando le route valide per un'interfaccia di rete in una macchina virtuale (VM). Azure crea diverse route predefinite per ogni subnet della rete virtuale. È possibile eseguire l'override delle route predefinite di Azure definendo route personalizzate in una tabella di route e quindi associando la tabella a una subnet. La combinazione di route create dall'utente, route predefinite di Azure e route propagate dalla rete locale tramite un gateway VPN di Azure (se la rete virtuale è connessa alla rete locale) con BGP (Border Gateway Protocol), rappresenta le route valide per tutte le interfacce di rete in una subnet. Se non si ha familiarità con le rete virtuali, le interfacce di rete o i concetti di routing, vedere [Panoramica di Rete virtuale](virtual-networks-overview.md), [Interfaccia di rete](virtual-network-network-interface.md) e [Panoramica sul routing](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Scenario

Si prova a connettersi a una macchina virtuale, ma la connessione non riesce. Per determinare perché non è possibile connettersi alla macchina virtuale, è possibile visualizzare le route valide per un'interfaccia di rete usando il [portale di Azure](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell) o l'[interfaccia della riga di comando di Azure](#diagnose-using-azure-cli).

I passaggi che seguono presuppongono la disponibilità di una macchina virtuale per visualizzarne le route valide. In mancanza di una macchina virtuale esistente, distribuire prima una VM [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) con cui completare le attività in questo articolo. Gli esempi di questo articolo sono per una VM denominata *myVM* con un'interfaccia di rete denominata *myVMVMNic*. L'interfaccia di rete e la VM si trovano in un gruppo di risorse denominato *myResourceGroup* nell'area *Stati Uniti orientali*. Modificare i valori nei passaggi come appropriato per la VM per cui si sta diagnosticando il problema.

## <a name="diagnose-using-azure-portal"></a>Diagnosi tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di Azure che disponga delle [autorizzazioni necessarie](virtual-network-network-interface.md#permissions).
2. Nella parte superiore del portale di Azure immettere nella casella di ricerca il nome di una macchina virtuale in stato di esecuzione. Quando il nome della macchina virtuale viene visualizzato nei risultati della ricerca, selezionarlo.
3. Selezionare **Diagnostica e risolvi i problemi**, quindi nella sezione **Passaggi consigliati** selezionare **route valide** alla voce 7, come illustrato nell'immagine seguente:

    ![Visualizzare le route valide](./media/diagnose-network-routing-problem/view-effective-routes.png)

4. L'immagine seguente mostra le route valide per un'interfaccia di rete denominata **myVMVMNic**:

     ![Visualizzare le route valide](./media/diagnose-network-routing-problem/effective-routes.png)

    Se sono presenti più interfacce di rete collegate alla macchina virtuale, è possibile visualizzare le route valide per qualsiasi interfaccia di rete selezionandola. Poiché ogni interfaccia di rete può trovarsi in una subnet diversa, ogni interfaccia di rete può avere route valide differenti.

    Nell'esempio illustrato nell'immagine precedente, le route elencate sono route predefinite che Azure crea per ogni subnet. L'elenco conterrà almeno queste route, ma potrebbe contenerne altre in base alle funzionalità abilitate per la rete virtuale, ad esempio il peering con un'altra rete virtuale o la connessione alla rete locale tramite un gateway VPN di Azure. Per altre informazioni su ognuna di queste route e sulle altre che possono essere visualizzate per l'interfaccia di rete, vedere [Routing del traffico di rete virtuale](virtual-networks-udr-overview.md). Se l'elenco contiene un numero elevato di route, può risultare più semplice selezionare **Scarica** per scaricarlo in formato CSV.

Anche se nei passaggi precedenti le route valide sono state visualizzate tramite la macchina virtuale, è possibile visualizzarle anche tramite:
- **Una singola interfaccia di rete**: informazioni su come [visualizzare un'interfaccia di rete](virtual-network-network-interface.md#view-network-interface-settings).
- **Una singola tabella di route**: informazioni su come [ visualizzare una tabella di route](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnosi tramite PowerShell

È possibile eseguire i comandi seguenti in [Azure Cloud Shell](https://shell.azure.com/powershell) oppure eseguendo PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Se si esegue PowerShell dal computer, è necessario il modulo *AzureRM* di PowerShell versione 6.0.1 o successiva. Per trovare la versione installata, eseguire `Get-Module -ListAvailable AzureRM` nel computer. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è necessario eseguire anche `Login-AzureRmAccount` per accedere ad Azure con un account con le [autorizzazioni necessarie](virtual-network-network-interface.md#permissions).

Ottenere le route valide per un'interfaccia di rete con [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). L'esempio seguente ottiene le route valide per un'interfaccia di rete denominata *myVMVMNic* che si trova in un gruppo di risorse denominato *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Per comprendere le informazioni restituite nell'output, vedere [Panoramica sul routing](virtual-networks-udr-overview.md). L'output viene restituito solo se la macchina virtuale è nello stato di esecuzione. Se alla macchina virtuale sono collegate più interfacce di rete, è possibile esaminare le route valide per ognuna delle interfacce. Poiché ogni interfaccia di rete può trovarsi in una subnet diversa, ogni interfaccia di rete può avere route valide differenti. Se si verificano ancora problemi di comunicazione, vedere [Diagnosi aggiuntiva](#additional-diagnosis) e [Considerazioni](#considerations).

Se si non conosce il nome di un'interfaccia di rete, ma si conosce il nome della macchina virtuale a cui è collegata, i comandi seguenti restituiscono gli ID di tutte le interfacce di rete collegate a una macchina virtuale:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

L'output che si riceve è simile all'esempio seguente:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Nell'output precedente il nome dell'interfaccia di rete è *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnosi tramite l'interfaccia della riga di comando di Azure

È possibile eseguire i comandi seguenti in [Azure Cloud Shell](https://shell.azure.com/bash) oppure eseguendo l'interfaccia della riga di comando sul computer. Questo articolo richiede l'interfaccia della riga di comando di Azure 2.0.32 o versioni successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è necessario eseguire anche `az login` e accedere ad Azure con un account dotato delle [autorizzazioni necessarie](virtual-network-network-interface.md#permissions).

Ottenere le route valide per un'interfaccia di rete con [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). L'esempio seguente ottiene le route valide per un'interfaccia di rete denominata *myVMVMNic* che si trova in un gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Per comprendere le informazioni restituite nell'output, vedere [Panoramica sul routing](virtual-networks-udr-overview.md). L'output viene restituito solo se la macchina virtuale è nello stato di esecuzione. Se alla macchina virtuale sono collegate più interfacce di rete, è possibile esaminare le route valide per ognuna delle interfacce. Poiché ogni interfaccia di rete può trovarsi in una subnet diversa, ogni interfaccia di rete può avere route valide differenti. Se si verificano ancora problemi di comunicazione, vedere [Diagnosi aggiuntiva](#additional-diagnosis) e [Considerazioni](#considerations).

Se si non conosce il nome di un'interfaccia di rete, ma si conosce il nome della macchina virtuale a cui è collegata, i comandi seguenti restituiscono gli ID di tutte le interfacce di rete collegate a una macchina virtuale:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Risolvere un problema

La risoluzione dei problemi di routing in genere comporta:

- Aggiunta di una route personalizzata per eseguire l'override di una delle route predefinite di Azure. Informazioni su come [aggiungere una route personalizzata](manage-route-table.md#create-a-route).
- Modifica o rimozione di una route personalizzata che può causare il routing a una posizione indesiderata. Informazioni su come [modificare](manage-route-table.md#change-a-route) oppure [eliminare](manage-route-table.md#delete-a-route) una route personalizzata.
- Verifica che la tabella di route che contiene le route personalizzate eventualmente definite sia associata alla subnet in cui si trova l'interfaccia di rete. Informazioni su come [associare una route a una subnet](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Verifica del corretto funzionamento dei dispositivi distribuiti, ad esempio gateway VPN di Azure VPN o appliance di rete virtuali. Usare la funzionalità di [diagnostica VPN](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Network Watcher per individuare eventuali problemi con un gateway VPN di Azure.

Se si verificano ancora problemi di comunicazione, vedere [Considerazioni](#considerations) e [Diagnosi aggiuntiva](#additional-dignosis).

## <a name="considerations"></a>Considerazioni

Durante la risoluzione dei problemi di comunicazione, tenere presente quanto segue:

- Il routing è basato sulla corrispondenza del prefisso più lunga (LPM) tra le route definite dall'utente, BGP e di sistema. Se è presente più di una route con la stessa corrispondenza LPM, la route viene selezionata in base alla sua origine nell'ordine elencato in [Panoramica sul routing](virtual-networks-udr-overview.md#how-azure-selects-a-route). Cercando le route valide, è possibile vedere solo le route valide con corrispondenza LPM basata su tutte le route disponibili. Vedendo il modo in cui vengono effettivamente valutate le route per un'interfaccia di rete, è molto più semplice risolvere i problemi delle route specifiche che potrebbero influire sulle comunicazioni dalla VM.
- Se l'utente ha definito route personalizzate verso un'appliance virtuale di rete, con *Appliance virtuale* come tipo di hop successivo, assicurarsi che l'inoltro IP sia abilitato nell'appliance virtuale di rete che riceve il traffico o che i pacchetti vengano eliminati. Altre informazioni su come [abilitare l'inoltro IP per un'interfaccia di rete](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Inoltre, è necessario che il sistema operativo o l'applicazione all'interno dell'appliance virtuale di rete sia in grado di inoltrare il traffico di rete e configurato a questo scopo.
- Se si crea una route per 0.0.0.0/0, tutto il traffico Internet in uscita viene instradato all'hop successivo specificato, ad esempio un'appliance virtuale di rete o un gateway VPN. La creazione di una route di questo tipo viene spesso definita tunneling forzato. Le connessioni remote che usano i protocolli RDP o SSH da Internet alla macchina virtuale potrebbero non funzionare con questa route, a seconda del modo in cui l'hop successivo gestisce il traffico. Il tunneling forzato può essere abilitato:
    - Quando si usa la VPN da sito a sito, creando una route con un tipo di hop successivo *Gateway VPN*. Altre informazioni sulla [configurazione del tunneling forzato](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Se una route predefinita 0.0.0.0/0 viene annunciata su BGP tramite un gateway di rete virtuale quando si usa una VPN da sito a sito o un circuito ExpressRoute. Altre informazioni sull'uso di BGP con una [VPN da sito a sito](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o con [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- Per il corretto funzionamento del traffico peering di rete virtuale deve esistere una route di sistema con un tipo di hop successivo *Peering reti virtuali* per l'intervallo di prefissi della rete virtuale con peering. Se non esiste una route di questo tipo e il collegamento per il peering di rete virtuale è **Connesso**:
    - Attendere alcuni secondi, quindi riprovare. Se si tratta di un collegamento di peering appena stabilito, in alcuni casi è necessario più tempo per propagare le route a tutte le interfacce di rete in una subnet. Per altre informazioni sul peering di rete virtuale, vedere [Peering di rete virtuale](virtual-network-peering-overview.md) e [Creare, modificare o eliminare un peering reti virtuali](virtual-network-manage-peering.md).
    - Le regole del gruppo di sicurezza di rete potrebbero influire sulle comunicazioni. Per altre informazioni, vedere [Diagnosticare un problema di filtro del traffico di rete di una macchina virtuale](diagnose-network-traffic-filter-problem.md).
- Anche se Azure assegna route predefinite a ogni sua interfaccia di rete, se alla macchina virtuale sono collegate più interfacce di rete solo all'interfaccia di rete primaria vengono assegnati un gateway o una route predefinita (0.0.0.0/0), all'interno del sistema operativo della VM. Informazioni su come creare una route predefinita per le interfacce di rete secondarie collegate a una VM [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) o [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics). Altre informazioni sulle [interfacce di rete primarie e secondarie](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Diagnosi aggiuntiva

* Per eseguire un rapido test per determinare il tipo di hop successivo per il traffico destinato a una posizione, usare la funzionalità [Hop successivo](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure Network Watcher. Questa funzionalità identifica il tipo di hop successivo per il traffico destinato a una posizione specificata.
* Se non vengono individuate route che causano errori nelle comunicazioni di rete di una macchina virtuale, il problema potrebbe essere dovuto al software firewall in esecuzione nel sistema operativo della VM
* Se si [forza il tunneling](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) del traffico a un dispositivo locale tramite un gateway VPN o un'appliance virtuale di rete, potrebbe essere impossibile connettersi a una macchina virtuale da Internet, a seconda del modo in cui si è configurato il routing per i dispositivi. Verificare che il routing configurato per il dispositivo instradi il traffico verso un indirizzo IP pubblico o privato per la macchina virtuale.
* Usare la funzionalità di [risoluzione dei problemi di connessione](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Network Watcher per determinare le cause associate a routing, filtro e sistema operativo dei problemi di comunicazione in uscita.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su tutte le attività, le proprietà e le impostazioni per una [tabella di route e le route](manage-route-table.md).
- Informazioni su tutti i [tipi di hop successivo, le route di sistema e su come Azure seleziona le route](virtual-networks-udr-overview.md).
