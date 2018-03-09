---
title: Aggiungere o rimuovere interfacce di rete da macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come aggiungere o rimuovere interfacce di rete da macchine virtuali.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
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
ms.openlocfilehash: bb21690865cd9384fe3d3c82e60f11e0fc64114c
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Aggiungere o rimuovere interfacce di rete da macchine virtuali

Informazioni su come aggiungere un'interfaccia di rete esistente durante la creazione di una macchina virtuale di Azure o su come aggiungere o rimuovere interfacce di rete da una macchina virtuale esistente in stato di arresto, ovvero deallocata. Un'interfaccia di rete consente a una macchina virtuale di Azure di comunicare con Internet, Azure e le risorse locali. Una macchina virtuale può avere una o più interfacce di rete. 

Se è necessario aggiungere, modificare o rimuovere indirizzi IP per un'interfaccia di rete, vedere [Gestire gli indirizzi IP dell'interfaccia di rete](virtual-network-network-interface-addresses.md). Se è necessario creare, modificare o eliminare interfacce di rete, vedere [Gestire le interfacce di rete](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Prima di iniziare

Prima di completare i passaggi di qualsiasi sezione di questo articolo, eseguire le attività seguenti:

- Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usa il portale, aprire https://portal.azure.com e accedere con l'account Azure.
- Se si usano i comandi di PowerShell per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/powershell) o tramite PowerShell dal computer in uso. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Questa esercitazione richiede il modulo di Azure PowerShell 5.2.0 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.
- Se si usano i comandi dell'interfaccia della riga di comando di Azure per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/bash) o tramite l'interfaccia della riga di comando dal computer in uso. Questa esercitazione richiede l'interfaccia della riga di comando di Azure versione 2.0.26 o successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Aggiungere interfacce di rete esistenti a una nuova macchina virtuale

Quando si crea una macchina virtuale tramite il portale, il portale crea un'interfaccia di rete con le impostazioni predefinite e la collega alla macchina virtuale automaticamente. Non è possibile aggiungere interfacce di rete esistenti a una nuova macchina virtuale o creare una macchina virtuale con più interfacce di rete tramite il portale di Azure. È possibile eseguire entrambe le operazioni tramite l'interfaccia della riga di comando o PowerShell, ma assicurarsi di acquisire familiarità con i [vincoli](#constraints). Se si crea una macchina virtuale con più interfacce di rete, è anche necessario configurare il sistema operativo per usarle correttamente dopo aver creato la macchina virtuale. Informazioni su come configurare [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) per più interfacce di rete.

### <a name="commands"></a>Comandi:

Prima di creare la macchina virtuale, creare un'interfaccia di rete seguendo la procedura descritta in [Creare un'interfaccia di rete](virtual-network-network-interface.md#create-a-network-interface).

|Strumento|Comando|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Aggiungere un'interfaccia di rete a una macchina virtuale esistente

1. Accedere al portale di Azure.
2. Nella casella di ricerca nella parte superiore del portale, digitare il nome della macchina virtuale a cui si vuole aggiungere l'interfaccia di rete o cercare la macchina virtuale selezionando **Tutti i servizi** e quindi **Macchine virtuali**. Dopo aver trovato la macchina virtuale, selezionarla. La macchina virtuale deve supportare il numero di interfacce di rete da aggiungere. Per scoprire quante interfacce di rete supporta ogni dimensione di macchina virtuale, vedere [Dimensioni delle macchine virtuali Linux in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Dimensioni per le macchine virtuali Windows in Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Selezionare **Panoramica** in **Impostazioni**. Selezionare **Arresta** e quindi aspettare che lo **Stato** della macchina virtuale diventi **Arrestata (deallocata)**. 
4. Selezionare **Rete** in **Impostazioni**.
5. Selezionare **Collega interfaccia di rete**. Nell'elenco delle interfacce di rete che non sono attualmente collegate a un'altra macchina virtuale, selezionare quella da collegare. 

    >[!NOTE]
    Per l'interfaccia di rete selezionata non può essere abilitata la rete accelerata, non è possibile che abbia un indirizzo IPv6 assegnato e l'interfaccia deve esistere nella stessa rete virtuale di quella che contiene l'interfaccia di rete attualmente collegata alla macchina virtuale. 

    Se non è disponibile un'interfaccia di rete esistente, è prima di tutto necessario crearla. A tale scopo, selezionare **Crea interfaccia di rete**. Per altre informazioni su come creare un'interfaccia di rete, vedere [Creare un'interfaccia di rete](virtual-network-network-interface.md#create-a-network-interface). Per altre informazioni sui vincoli aggiuntivi esistenti per l'aggiunta di interfacce di rete alle macchine virtuali, vedere [Vincoli](#constraints).

6. Selezionare **OK**.
7. Selezionare **Panoramica** in **Impostazioni** e quindi **Avvia** per avviare la macchina virtuale.
8. Configurare il sistema operativo della macchina virtuale per l'uso appropriato di più interfacce di rete. Informazioni su come configurare [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) per più interfacce di rete.

|Strumento|Comando|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add) (riferimento) o [passaggi dettagliati](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (riferimento) o [passaggi dettagliati](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Visualizzare le interfacce di rete per una macchina virtuale

È possibile visualizzare le interfacce di rete attualmente collegate a una macchina virtuale per conoscere la configurazione e gli indirizzi IP assegnati di ogni interfaccia di rete. 

1. Accedere al [portale di Azure](https://portal.azure.com) con un account con il ruolo Proprietario, Collaboratore o Collaboratore Rete per la sottoscrizione. Per altre informazioni su come assegnare i ruoli agli account, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Nella finestra che contiene il testo **Cerca risorse**, nella parte superiore del portale di Azure, digitare **macchine virtuali**. Selezionare **macchine virtuali** quando viene visualizzato nei risultati della ricerca.
3. Selezionare il nome della macchina virtuale per cui si vogliono visualizzare le interfacce di rete.
4. Nella sezione **Impostazioni** per la macchina virtuale selezionata selezionare **Rete**. Per informazioni sulle impostazioni dell'interfaccia di rete e su come modificarle, vedere [Gestire le interfacce di rete](virtual-network-network-interface.md). Per informazioni su come aggiungere, modificare o rimuovere gli indirizzi IP assegnati a un'interfaccia di rete, vedere [Gestire gli indirizzi IP dell'interfaccia di rete](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Comandi:

|Strumento|Comando|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Rimuovere un'interfaccia di rete da una macchina virtuale

1. Accedere al portale di Azure.
2. Nella casella di ricerca nella parte superiore del portale cercare il nome della macchina virtuale da cui si vuole rimuovere (scollegare) l'interfaccia di rete o cercare la macchina virtuale selezionando **Tutti i servizi** e quindi **Macchine virtuali**. Dopo aver trovato la macchina virtuale, selezionarla.
3. Selezionare **Panoramica** in **Impostazioni** e quindi **Arresta**. Aspettare che lo **Stato** della macchina virtuale venga modificato in **Arrestata (deallocata)**. 
4. Selezionare **Rete** in **Impostazioni**.
5. Selezionare **Scollega interfaccia di rete**. Nell'elenco delle interfacce di rete attualmente collegate alla macchina virtuale, selezionare l'interfaccia di rete da scollegare. 

    >[!NOTE]
    Se l'elenco include una sola interfaccia di rete non è possibile scollegarla, perché a una macchina virtuale deve essere sempre collegata almeno un'interfaccia di rete.
6. Selezionare **OK**.

### <a name="commands"></a>Comandi:

|Strumento|Comando|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove) (riferimento) o [passaggi dettagliati](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (riferimento) o [passaggi dettagliati](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Vincoli

- Una macchina virtuale deve avere almeno un'interfaccia di rete collegata,
- fino al numero massimo supportato dalle dimensioni della macchina virtuale stessa. Per informazioni su quante interfacce di rete supporta ogni dimensione di macchina virtuale, vedere [Dimensioni delle macchine virtuali Linux in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Dimensioni per le macchine virtuali Windows in Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tutte le dimensioni supportano almeno due interfacce di rete.
- Le interfacce di rete che si aggiungono a una macchina virtuale non possono essere collegate a un'altra macchina virtuale. Per altre informazioni su come creare interfacce di rete, vedere [Creare un'interfaccia di rete](virtual-network-network-interface.md#create-a-network-interface).
- In precedenza, le interfacce di rete potevano essere aggiunte solo a macchine virtuali in grado di supportare più interfacce di rete e create con almeno due interfacce di rete. Non era possibile aggiungere un'interfaccia di rete a una macchina virtuale creata con un'interfaccia di rete, anche se le dimensioni della macchina virtuale supportavano più interfacce di rete. Al contrario, era possibile rimuovere le interfacce di rete solo dalle macchine virtuali con almeno tre interfacce di rete, perché alle macchine virtuali create con almeno due interfacce di rete ne dovevano essere sempre collegate almeno due. Attualmente non si applica alcuno di questi vincoli. È ora possibile creare una macchina virtuale con un numero qualsiasi di interfacce di rete (fino al numero massimo supportato dalle dimensioni della macchina virtuale).
- Per impostazione predefinita, la prima interfaccia di rete collegata a una macchina virtuale è definita come interfaccia di rete *primaria*. Tutte le altre interfacce di rete nella macchina virtuale sono interfacce di rete *secondarie*.
- Anche se è possibile controllare l'interfaccia di rete a cui viene inviato il traffico in uscita, per impostazione predefinita, tutto il traffico in uscita dalla macchina virtuale viene inviato all'indirizzo IP assegnato alla configurazione IP primaria dell'interfaccia di rete primaria.
- In passato, tutte le macchine virtuali nello stesso set di disponibilità dovevano avere una o più interfacce di rete. Ora possono esistere macchine virtuali con un numero qualsiasi di interfacce di rete nello stesso set di disponibilità, fino al numero supportato dalla dimensione della macchina virtuale. Una macchina virtuale può essere aggiunta a un set di disponibilità solo in fase di creazione. Per altre informazioni sui set di disponibilità, vedere [Gestire la disponibilità delle macchine virtuali Windows in Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Anche se le interfacce di rete nella stessa macchina virtuale possono essere connesse a subnet diverse all'interno di una rete virtuale, le interfacce di rete devono essere tutte connesse alla stessa rete virtuale.
- È possibile aggiungere qualsiasi indirizzo IP per qualsiasi configurazione IP di un'interfaccia di rete primaria e secondaria a un pool back-end di Azure Load Balancer. In passato, era possibile aggiungere a un pool di back-end solo l'indirizzo IP primario per l'interfaccia di rete primaria. Per altre informazioni sugli indirizzi e le configurazioni IP, vedere [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md).
- Se si elimina una macchina virtuale, le interfacce di rete virtuale connesse a questa non vengono eliminate. Quando si elimina una macchina virtuale, le interfacce di rete vengono scollegate dalla macchina virtuale. È possibile aggiungere le interfacce di rete a macchine virtuali diverse o eliminarle.
- Se a un'interfaccia di rete è assegnato un indirizzo IPv6 privato, è necessario aggiungerla (collegarla) a una macchina virtuale quando si crea la macchina virtuale. Dopo aver creato la macchina virtuale, non è più possibile aggiungere un'interfaccia di rete con un indirizzo IPv6 assegnato a una macchina virtuale. Se si aggiunge un'interfaccia di rete con un indirizzo IPv6 privato assegnato durante la creazione di una macchina virtuale, è possibile aggiungere solo questa interfaccia di rete specifica alla macchina virtuale, indipendentemente dal numero di interfacce di rete supportato dalle dimensioni della macchina virtuale. Vedere [Aggiungere, modificare o rimuovere indirizzi IP per un'interfaccia di rete di Azure](virtual-network-network-interface-addresses.md) per altre informazioni sull'assegnazione di indirizzi IP alle interfacce di rete.
- Come per IPv6, non è possibile collegare un'interfaccia di rete con rete accelerata abilitata a una macchina virtuale dopo averla creata. Inoltre, per sfruttare i vantaggi della rete accelerata, è anche necessario completare i passaggi all'interno del sistema operativo della macchina virtuale. Sono disponibili altre informazioni sulle rete accelerata e altri vincoli per il relativo uso per macchine virtuali [Windows](create-vm-accelerated-networking-powershell.md) o [Linux](create-vm-accelerated-networking-cli.md).

## <a name="next-steps"></a>Passaggi successivi
Per creare una macchina virtuale con più interfacce di rete o indirizzi IP, vedere gli articoli seguenti:

### <a name="commands"></a>Comandi:

|Attività|Strumento|
|---|---|
|Creare una macchina virtuale con più NIC|[Interfaccia della riga di comando](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creare una macchina virtuale con una singola scheda di interfaccia di rete e più indirizzi IPv4|[Interfaccia della riga di comando](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Creare una macchina virtuale con una singola scheda di interfaccia di rete con un indirizzo IPv6 privato (dietro un Azure Load Balancer)|[Interfaccia della riga di comando](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Modello di Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|


