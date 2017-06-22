---
title: Aggiungere o rimuovere interfacce di rete da macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come aggiungere o rimuovere schede di interfaccia di rete da macchine virtuali.
services: virtual-network
documentationcenter: na
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
ms.date: 05/04/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 0d609b20040572e3fb371a277603109d64a0ba37
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---

# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>Aggiungere o rimuovere interfacce di rete da macchine virtuali

Informazioni su come aggiungere una scheda di interfaccia di rete esistente durante la creazione di una VM e su come aggiungere o rimuovere schede di interfaccia di rete da una VM esistente in stato di arresto (deallocata). Una scheda di interfaccia di rete consente a una macchina virtuale (VM) di Azure di comunicare con Internet, Azure e le risorse locali. Una macchina virtuale può avere una o più schede di interfaccia di rete. 

Se si desidera aggiungere, modificare o rimuovere indirizzi IP da una scheda di interfaccia di rete, leggere l'articolo [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md). Se è necessario creare, modificare o eliminare schede di interfaccia di rete, leggere l'articolo sulle [impostazioni e attività relative alle schede di interfaccia di rete](virtual-network-network-interface.md).

## <a name="before"></a>Prima di iniziare

Prima di completare qualsiasi altro passaggio nelle altre sezioni di questo articolo, eseguire le operazioni seguenti:

- Per informazioni sul numero di schede di interfaccia di rete supportate per ogni dimensione di VM Linux e Windows, vedere gli articoli relativi alle dimensioni delle VM [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Accedere al portale di Azure, all'interfaccia della riga di comando di Azure oppure ad Azure PowerShell con un account Azure. Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si sceglie di usare i comandi di PowerShell per completare le attività indicate in questo articolo, installare e configurare Azure PowerShell seguendo i passaggi descritti nell'articolo [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dei cmdlet di Azure PowerShell. Per informazioni ed esempi relativi ai comandi di PowerShell, digitare `get-help <command> -full`.
- Se si sceglie di usare i comandi dell'interfaccia della riga di comando di Azure per completare le attività indicate in questo articolo, installare e configurare l'interfaccia della riga di comando di Azure seguendo i passaggi descritti nell'articolo [Come installare e configurare Azure PowerShell](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per ottenere assistenza per i comandi dell'interfaccia della riga di comando digitare `az <command> --help`.

## <a name="about"></a>Informazioni sulle schede di interfaccia di rete e le VM

È possibile aggiungere (collegare) una scheda di interfaccia di rete esistente a una VM quando si crea la VM purché la scheda di interfaccia di rete non sia attualmente collegata a un'altra VM. È possibile aggiungere o rimuovere (scollegare) una scheda di interfaccia di rete da una VM esistente purché la VM di cui si trova sia in stato di arresto (deallocata). Se si crea una VM tramite il portale di Azure, il portale crea automaticamente una scheda di interfaccia di rete con le impostazioni predefinite. Il portale non consente di:

- Specificare un'interfaccia di rete esistente da aggiungere quando si crea la VM
- Creare una macchina virtuale con più NIC
- Specificare un nome per l'interfaccia di rete. Il portale crea l'interfaccia di rete con un nome predefinito.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando per creare un'interfaccia di rete o una VM con tutti gli attributi indicati in precedenza per i quali non è possibile usare il portale. Prima di completare le attività nelle sezioni seguenti, prendere in considerazione i vincoli e i comportamenti seguenti:

- Tutte le dimensioni di VM supportano almeno due schede di interfaccia di rete, ma alcune dimensioni di VM ne supportano più di due. In passato alcune dimensioni di VM supportavano una sola scheda di interfaccia di rete. Per informazioni sul numero di schede di interfaccia di rete supportate per ogni dimensione di VM, vedere gli articoli relativi alle dimensioni delle VM [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- In precedenza, le schede di interfaccia di rete potevano essere aggiunte solo a macchine virtuali in grado di supportare più schede e create con almeno due schede di interfacce di rete. Non era possibile aggiungere alcuna scheda di interfaccia di rete a una macchina virtuale creata con una scheda, anche se le dimensioni della macchina virtuale supportavano più schede. Al contrario, era possibile rimuovere schede di interfaccia di rete solo dalle VM con almeno tre schede collegate, perché alle VM create con almeno due schede dovevano essere sempre collegate almeno due schede di interfaccia di rete. Attualmente non si applica alcuno di questi vincoli. Ora è possibile creare una VM con qualsiasi numero di schede di interfaccia di rete, fino al numero supportato dalla dimensione della VM, e aggiungere o rimuovere qualsiasi numero di schede, con la VM in stato di arresto (deallocata), purché alla VM sia sempre collegata almeno una scheda di interfaccia di rete.
- Per impostazione predefinita, la prima scheda di interfaccia di rete in una VM è definita *principale*. Tutte le altre schede di interfaccia di rete della VM sono dette *secondarie*.
- Alle schede di interfaccia di rete principali è assegnato un gateway predefinito dai server DHCP di Azure mentre ciò non avviene per le schede secondarie. Poiché alle schede di interfaccia di rete secondarie non è assegnato un gateway predefinito, non possono comunicare con le risorse all'esterno della loro subnet per impostazione predefinita. Per consentire alle schede di interfaccia di rete secondarie di una VM Windows di comunicare con le risorse esterne alla loro subnet, aggiungere le route al sistema operativo usando il comando `route add` da un riga di comando di Windows. Per le VM Linux, poiché il comportamento predefinito usa un host routing vulnerabile, è consigliabile limitare il traffico per le schede di interfaccia di rete secondarie a una sola subnet. Se è richiesta la connettività all'esterno della subnet per le schede di interfaccia di rete secondarie, attivare il routing basato su criteri per fare in modo che il traffico in entrata e in uscita utilizzi la stessa scheda di interfaccia di rete.
- Per impostazione predefinita, tutto il traffico in uscita dalla VM viene inviato all'indirizzo IP assegnato alla configurazione IP primaria dell'interfaccia di rete primaria. È possibile scegliere l'indirizzo IP da usare per il traffico in uscita all'interno del sistema operativo della VM, ma per impostazione predefinita passa attraverso la scheda di interfaccia di rete principale.
- In passato, tutte le VM nello stesso set di disponibilità dovevano avere una o più interfacce di rete. Ora possono esistere VM con un numero qualsiasi di schede di interfaccia di rete nello stesso set di disponibilità, fino al numero supportato dalla dimensione della VM. Una VM può essere aggiunta a un set di disponibilità solo in fase di creazione. Per altre informazioni sui set di disponibilità, vedere l'articolo [Gestire la disponibilità delle VM Windows in Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Anche se le schede di interfaccia di rete nella stessa VM possono essere connesse a subnet diverse all'interno di una rete virtuale, le interfacce di rete devono essere tutte connesse alla stessa rete virtuale.
- È possibile aggiungere qualsiasi indirizzo IP per qualsiasi configurazione IP di un'interfaccia di rete primaria e secondaria a un pool back-end di Azure Load Balancer. In passato, era possibile aggiungere a un pool di back-end solo gli indirizzi IP primari per la scheda di interfaccia di rete primaria. Per altre informazioni sugli indirizzi e le configurazioni IP, leggere l'articolo [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md).
- Se si elimina una VM, le schede di interfaccia di rete virtuale connesse a questa non vengono eliminate. Quando si elimina una macchina virtuale, le schede di interfaccia di rete vengono scollegate dalla macchina virtuale. È possibile aggiungere le schede di interfaccia di rete a VM diverse o eliminarle.

## <a name="vm-create"></a>Aggiungere schede di interfaccia di rete esistenti a una nuova VM
Quando si crea una VM tramite il portale, il portale crea una scheda di interfaccia di rete con le impostazioni predefinite e la collega alla VM automaticamente. Non è possibile aggiungere interfacce di rete esistenti a una nuova VM oppure creare una VM con più interfacce di rete tramite il portale di Azure. È possibile fare entrambe le cose usando l'interfaccia della riga di comando o PowerShell. È possibile aggiungere a una VM tutte le schede di interfaccia di rete che la dimensione della VM può supportare. Per informazioni sul numero di schede di interfaccia di rete supportate per ogni dimensione di macchina virtuale, vedere gli articoli relativi alle dimensioni delle macchine virtuali [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Le schede di interfaccia di rete che si aggiungono a una VM non possono essere collegate a un'altra VM. Per altre informazioni sulla creazione delle schede di interfaccia di rete, leggere l'articolo sulle [impostazioni e attività relative alle schede di interfaccia di rete](virtual-network-network-interface.md#create-nic).

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Aggiungere schede di interfaccia di rete esistenti a una VM esistente

È possibile aggiungere a una VM tutte le schede di interfaccia di rete che la dimensione della VM può supportare. Per informazioni sul numero di schede di interfaccia di rete supportate per ogni dimensione di VM, vedere gli articoli relativi alle dimensioni delle VM [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). La VM alla quale aggiungere una scheda di interfaccia di rete deve supportare più interfacce di rete ed essere in stato di arresto (deallocata). Le schede di interfaccia di rete da aggiungere non possono essere collegate a un'altra VM. Non è possibile aggiungere schede di interfaccia di rete a una VM esistente tramite il portale di Azure. Per aggiungere schede di interfaccia di rete a una VM esistente, è necessario usare l'interfaccia della riga di comando o PowerShell.

|Strumento|Comando|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-view-nic"></a>Visualizzare le schede di interfaccia di rete per una VM

È possibile visualizzare le schede di interfaccia di rete attualmente collegate a una VM per conoscere la configurazione e gli indirizzi IP assegnati di ogni scheda. 

1. Accedere al [portale di Azure](https://portal.azure.com) con un account che abbia le autorizzazioni per il ruolo di Proprietario, Collaboratore o Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli agli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *macchine virtuali*. Fare clic su **macchine virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Macchine virtuali** che viene visualizzato fare clic sul nome della VM di cui visualizzare le schede di interfaccia di rete.
4. Nella sezione **IMPOSTAZIONI** del pannello della macchina virtuale che viene visualizzato per la VM selezionata fare clic su **Interfacce di rete**. Per altre informazioni sulle impostazioni di rete e come modificarle, leggere l'articolo sulle [impostazioni e attività relative alle schede di interfaccia di rete](virtual-network-network-interface.md). Per informazioni su come aggiungere, modificare o rimuovere indirizzi IP assegnati a una scheda di interfaccia di rete, leggere l'articolo [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md).

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a>Rimuovere schede di interfaccia di rete da una VM

La VM da cui rimuovere una scheda di interfaccia di rete deve essere in stato di arresto (deallocata) e avere attualmente almeno due schede di interfaccia di rete collegate. È possibile rimuovere qualsiasi scheda di interfaccia di rete, ma la VM deve sempre avere almeno una scheda di interfaccia di rete collegata. Se si rimuove una scheda di interfaccia di rete principale, Azure assegnerà l'attributo di interfaccia principale alla scheda di interfaccia di rete che è collegata alla VM da più tempo. È possibile designare autonomamente l'interfaccia di rete principale. Non è possibile rimuovere schede di interfaccia di rete da una VM o impostare l'attributo di interfaccia principale per una scheda tramite il portale di Azure, ma è possibile eseguire entrambe le operazioni usando l'interfaccia della riga di comando o PowerShell. 

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Passaggi successivi
Per creare una VM con più schede di interfaccia di rete o indirizzi IP, vedere gli articoli seguenti:

**Comandi**

|Attività|Strumento|
|---|---|
|Creare una macchina virtuale con più NIC|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creare una VM con una singola scheda di interfaccia di rete e più indirizzi IP|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

