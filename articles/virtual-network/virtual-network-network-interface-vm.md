---
title: Aggiungere o rimuovere interfacce di rete da macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come aggiungere o rimuovere interfacce di rete da macchine virtuali.
services: virtual-network
documentationcenter: na
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
ms.date: 12/15/2017
ms.author: jdial
ms.openlocfilehash: abe6abb942d206330e809f3aef388b846d7d7c7f
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Interfacce di rete per aggiungere o rimuovere le interfacce di rete da macchine virtuali

Informazioni su come aggiungere un'interfaccia di rete esistente durante la creazione di una macchina virtuale e su come aggiungere o rimuovere le interfacce di rete da una macchina virtuale esistente in stato di arresto, ovvero deallocata. Un'interfaccia di rete consente a una macchina virtuale di Azure di comunicare con Internet, Azure e le risorse locali. Una macchina virtuale può avere una o più interfacce di rete. 

Se si desidera aggiungere, modificare o rimuovere indirizzi IP da un'interfaccia di rete, leggere l'articolo [Gestire gli indirizzi IP dell’interfaccia di rete](virtual-network-network-interface-addresses.md). Se è necessario creare, modificare o eliminare le interfacce di rete, leggere l'articolo [Gestire le interfacce di rete](virtual-network-network-interface.md).

## <a name="before"></a>Prima di iniziare

Prima di completare qualsiasi passaggio nelle altre sezioni di questo articolo, eseguire le operazioni seguenti:

- Accedere al [portale](https://portal.azure.com) di Azure, all'interfaccia della riga di comando di Azure oppure ad Azure PowerShell con un account Azure. Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usano comandi di PowerShell per completare le attività di questo articolo, [installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dei cmdlet di Azure PowerShell. Per informazioni ed esempi relativi ai comandi di PowerShell, digitare `get-help <command> -full`. Anziché installare Azure PowerShell, è possibile usare la Shell di Cloud di Azure. La Shell di Cloud di Azure è un PowerShell gratuito che è possibile eseguire direttamente nel portale di Azure. Dispone di Azure PowerShell preinstallato e configurato per utilizzare con l'account. Per utilizzare la Shell di Cloud, selezionare la Shell Cloud **> _** pulsante nella parte superiore del [portale](https://portal.azure.com) e selezionare PowerShell nell'angolo superiore sinistro, quando viene visualizzata la finestra della shell.
- Se si usano comandi dell'interfaccia della riga di comando di Azure per completare le attività di questo articolo, [installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `az <command> --help`. Invece di installare l'interfaccia della riga di comando e i rispettivi prerequisiti, è possibile usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Per utilizzare la Shell di Cloud, selezionare la Shell Cloud **> _** pulsante nella parte superiore del [portale](https://portal.azure.com) e selezionare Bash nell'angolo superiore sinistro, quando viene visualizzata la finestra della shell.

## <a name="vm-create"></a>Aggiungere interfacce di rete esistenti a una nuova macchina virtuale

Quando si crea una macchina virtuale tramite il portale, il portale crea un'interfaccia di rete con le impostazioni predefinite e la collega alla macchina virtuale automaticamente. Non è possibile aggiungere interfacce di rete esistenti a una nuova macchina virtuale o creare una macchina virtuale con più interfacce di rete tramite il portale di Azure. È possibile fare entrambe le cose usando l'interfaccia della riga di comando o PowerShell. Prima di creare una macchina virtuale con un'interfaccia di rete esistente, tuttavia, usare PowerShell o l'interfaccia CLI, acquisire familiarità con la [vincoli](#constraints). Se si crea una macchina virtuale con più interfacce di rete, è inoltre necessario configurare il sistema operativo per utilizzarli correttamente una volta creata la macchina virtuale. Per informazioni dettagliate, vedere configurare [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) per più interfacce di rete.

**Comandi** prima di creare la macchina virtuale, creare un'interfaccia di rete utilizzando la procedura descritta in [creare un'interfaccia di rete](virtual-network-network-interface.md#create-a-network-interface).

|Strumento|Comando|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Aggiungere un'interfaccia di rete a una macchina virtuale esistente

1. Accedere al portale di Azure.
2. Nella casella di ricerca nella parte superiore del portale, il nome della macchina virtuale a cui si desidera aggiungere l'interfaccia di rete a, una ricerca per la macchina virtuale facendo clic su **tutti i servizi**, quindi **macchine virtuali**. Dopo aver trovato la macchina virtuale, selezionarla. Si desidera aggiungere un'interfaccia di rete per la macchina virtuale deve supportare il numero di interfacce di rete da aggiungere. Per informazioni sul numero di interfacce di rete supportate per ogni dimensione di macchina virtuale, vedere gli articoli relativi alle dimensioni delle macchine virtuali [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Fare clic su **Panoramica**in **impostazioni**. Fare clic su **arrestare**e attendere finché il **stato** della macchina virtuale viene modificato in *arrestato (deallocato)*. 
4. Fare clic su **rete**in **impostazioni**.
5. Fare clic su **Allega l'interfaccia di rete**. Dall'elenco delle interfacce di rete esistente che non sono attualmente collegato a un'altra macchina virtuale, fare clic per collegare l'interfaccia di rete. L'interfaccia di rete selezionato non può avere accelerated networking abilitato, non può avere un indirizzo IPv6 assegnato e deve esistere nella stessa rete virtuale, come la rete virtuale, l'interfaccia di rete attualmente collegato alla macchina virtuale è in. Se non si dispone di un'interfaccia di rete esistente, è necessario crearlo. Per creare un'interfaccia di rete, fare clic su **interfaccia di rete crea**. Per ulteriori informazioni sulla creazione di un'interfaccia di rete, vedere [creare un'interfaccia di rete](virtual-network-network-interface.md#create-a-network-interface). Per ulteriori informazioni sui vincoli aggiuntivi durante l'aggiunta di interfacce di rete alle macchine virtuali, vedere [vincoli](#constraints).
6. Fare clic su **OK**.
7. Fare clic su **Panoramica**in **impostazioni**. Fare clic su **avviare** per avviare la macchina virtuale.
8. Configurare il sistema operativo di macchina virtuale per utilizzare correttamente più interfacce di rete. Per informazioni dettagliate, vedere configurare [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) per più interfacce di rete.

|Strumento|Comando|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add) (riferimento) o [passaggi dettagliati](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (riferimento) o [passaggi dettagliati](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="vm-view-nic"></a> Visualizzare le interfacce di rete di una macchina virtuale

È possibile visualizzare le interfacce di rete attualmente collegate a una macchina virtuale per conoscere la configurazione e gli indirizzi IP assegnati di ogni interfaccia di rete. 

1. Accedere al [portale di Azure](https://portal.azure.com) con un account che abbia le autorizzazioni per il ruolo di Proprietario, Collaboratore o Collaboratore Rete per la sottoscrizione. Per altre informazioni sull'assegnazione dei ruoli agli account, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *macchine virtuali*. Fare clic su **macchine virtuali** quando viene visualizzato nei risultati della ricerca.
3. Fare clic sul nome della macchina virtuale che si desidera visualizzare per le interfacce di rete.
4. Nel **impostazioni** sezione per la macchina virtuale è stata selezionata, fare clic su **rete**. Per ulteriori informazioni sulle impostazioni interfaccia di rete e su come modificarle, vedere [gestire interfacce di rete](virtual-network-network-interface.md). Per informazioni su come aggiungere, modificare o rimuovere indirizzi IP assegnati a un'interfaccia di rete, vedere [Gestione di indirizzi IP](virtual-network-network-interface-addresses.md).

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a> Rimuovere l’interfaccia di rete da una macchina virtuale

1. Accedere al portale di Azure.
2. Nella casella di ricerca nella parte superiore del portale, cercare il nome della macchina virtuale che si desidera rimuovere (disconnettere) l'interfaccia di rete da o Sfoglia per la macchina virtuale, fare clic su **tutti i servizi**, quindi **macchine virtuali**. Dopo aver trovato la macchina virtuale, selezionarla.
3. Fare clic su **Panoramica**in **impostazioni**. Fare clic su **arrestare**e attendere finché il **stato** della macchina virtuale viene modificato in *arrestato (deallocato)*. 
4. Fare clic su **rete**in **impostazioni**.
5. Fare clic su **interfaccia di rete scollegamento**. Dall'elenco delle interfacce di rete attualmente collegato alla macchina virtuale, fare clic per scollegare l'interfaccia di rete. Se solo un'interfaccia di rete è elencata, è possibile scollegare, poiché una macchina virtuale deve sempre avere almeno un'interfaccia di rete associata.
6. Fare clic su **OK**.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove) (riferimento) o [passaggi dettagliati](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (riferimento) o [passaggi dettagliati](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="next-steps"></a>Passaggi successivi
Per creare una macchina virtuale con più interfacce di rete o indirizzi IP, vedere gli articoli seguenti:

**Comandi**

|Attività|Strumento|
|---|---|
|Creare una macchina virtuale con più NIC|[Interfaccia della riga di comando](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creare una macchina virtuale con una singola scheda di interfaccia di rete e più indirizzi IPv4|[Interfaccia della riga di comando](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Creare una macchina virtuale con una singola scheda di interfaccia di rete con un indirizzo IPv6 privato (dietro un Azure Load Balancer)|[Interfaccia della riga di comando](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Modello di Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="constraints"></a>Vincoli

- Una macchina virtuale deve avere almeno un'interfaccia di rete associata.
- Come molte interfacce collegate come il supporta dimensioni di macchina virtuale di rete, può avere solo una macchina virtuale. Per ulteriori informazioni sulle interfacce di rete quanti supporta ogni dimensione della macchina virtuale, vedere [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dimensioni delle macchine Virtuali. Tutte le dimensioni supportano almeno due interfacce di rete.
- Le interfacce di rete che si aggiungono a una macchina virtuale non possono essere collegate a un'altra macchina virtuale. Per ulteriori informazioni sulla creazione di interfacce di rete, vedere [creare un'interfaccia di rete](virtual-network-network-interface.md#create-a-network-interface).
- In precedenza, le interfacce di rete potevano essere aggiunte solo a macchine virtuali in grado di supportare più interfacce di rete e create con almeno due interfacce di rete. Non era possibile aggiungere un'interfaccia di rete a una macchina virtuale creata con un'interfaccia di rete, anche se le dimensioni della macchina virtuale supportavano più interfacce di rete. Al contrario, era possibile rimuovere le interfacce di rete solo dalle macchine virtuali con almeno tre interfacce di rete, perché alle macchine virtuali create con almeno due interfacce di rete ne dovevano essere sempre collegate almeno due. Attualmente non si applica alcuno di questi vincoli. È ora possibile creare una macchina virtuale con un numero qualsiasi di interfacce di rete (fino al numero supportato dalle dimensioni macchina virtuale).
- Per impostazione predefinita, la prima interfaccia di rete associata a una macchina virtuale è definita come il *primario* interfaccia di rete. Tutte le altre interfacce di rete nella macchina virtuale sono interfacce di rete *secondarie*.
- Se è possibile controllare l'interfaccia di rete è stato inviato il traffico in uscita, per impostazione predefinita, tutto il traffico in uscita dalla macchina virtuale viene inviato l'indirizzo IP assegnato alla configurazione IP primaria dell'interfaccia di rete primaria.
- In passato, tutte le macchine virtuali nello stesso set di disponibilità dovevano avere una o più interfacce di rete. Ora possono esistere macchine virtuali con un numero qualsiasi di interfacce di rete nello stesso set di disponibilità, fino al numero supportato dalla dimensione della macchina virtuale. Una VM può essere aggiunta a un set di disponibilità solo in fase di creazione. Per altre informazioni sui set di disponibilità, vedere l'articolo [Gestire la disponibilità delle VM Windows in Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Anche se le interfacce di rete nella stessa macchina virtuale possono essere connesse a subnet diverse all'interno di una rete virtuale, le interfacce di rete devono essere tutte connesse alla stessa rete virtuale.
- È possibile aggiungere qualsiasi indirizzo IP per qualsiasi configurazione IP di un'interfaccia di rete primaria e secondaria a un pool back-end di Azure Load Balancer. In passato, era possibile aggiungere a un pool di back-end solo l'indirizzo IP primario per l'interfaccia di rete primaria. Per altre informazioni sugli indirizzi e le configurazioni IP, leggere l'articolo [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md).
- Se si elimina una macchina virtuale, le interfacce di rete virtuale connesse a questa non vengono eliminate. Quando si elimina una macchina virtuale, le interfacce di rete vengono scollegate dalla macchina virtuale. È possibile aggiungere le interfacce di rete a macchine virtuali diverse o eliminarle.
- Se un'interfaccia di rete è assegnato un indirizzo IPv6 privato a esso, è necessario aggiungere (collegamento) in una macchina virtuale quando si crea la macchina virtuale. Dopo aver creata la macchina virtuale, è possibile aggiungere un'interfaccia di rete con un indirizzo IPv6 assegnato a una macchina virtuale. Se si aggiunge un'interfaccia di rete con un indirizzo IPv6 privato assegnato durante la creazione di una macchina virtuale, è possibile aggiungere solo l'interfaccia di rete alla macchina virtuale, indipendentemente dal numero di interfacce di rete supportano le dimensioni della VM. Vedere [Indirizzi IP dell'interfaccia di rete](virtual-network-network-interface-addresses.md) per ulteriori informazioni sull'assegnazione di indirizzi IP alle interfacce di rete.
- Analogamente a IPv6, è possibile collegare un'interfaccia di rete con rete accelerata abilitata per una macchina virtuale dopo aver creata la macchina virtuale. Inoltre, per sfruttare i vantaggi di rete con accelerata, è inoltre necessario completare i passaggi all'interno del sistema operativo VM. Per ulteriori informazioni sulla rete con accelerata e altri vincoli in uso, vedere Accelerated di rete per [Windows](create-vm-accelerated-networking-powershell.md) o [Linux](create-vm-accelerated-networking-cli.md) macchine virtuali.
