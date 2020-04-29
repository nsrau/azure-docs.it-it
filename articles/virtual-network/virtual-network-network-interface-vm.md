---
title: Aggiungere o rimuovere interfacce di rete da macchine virtuali di Azure
description: Informazioni su come aggiungere o rimuovere interfacce di rete da macchine virtuali.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 4169bfb5da5b1ad13bab0eb01397f7c1fb20b11b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80060313"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Aggiungere o rimuovere interfacce di rete da macchine virtuali

Informazioni su come aggiungere un'interfaccia di rete esistente quando si crea una macchina virtuale (VM) di Azure. Viene anche illustrato come aggiungere o rimuovere interfacce di rete da una macchina virtuale esistente in stato di arresto (deallocata). Un'interfaccia di rete consente a una macchina virtuale di Azure di comunicare con Internet, Azure e le risorse locali. Una macchina virtuale ha una o più interfacce di rete. 

Se è necessario aggiungere, modificare o rimuovere indirizzi IP per un'interfaccia di rete, vedere [Gestire gli indirizzi IP dell'interfaccia di rete](virtual-network-network-interface-addresses.md). Per creare, modificare o eliminare le interfacce di rete, vedere [gestire le interfacce di rete](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se non si ha una sottoscrizione, configurare un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Prima di iniziare la parte restante di questo articolo, completare una di queste attività:

- **Utenti del portale**: accedere al [portale di Azure](https://portal.azure.com) con l'account Azure.

- **Utenti di PowerShell**: è possibile eseguire i comandi nel [Azure cloud Shell](https://shell.azure.com/powershell)o eseguire PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Nella scheda Azure Cloud Shell browser trovare l'elenco a discesa **Seleziona ambiente** , quindi scegliere **PowerShell** se non è già selezionato.

    Se si esegue PowerShell localmente, usare Azure PowerShell modulo 1.0.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az.Network` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Eseguire `Connect-AzAccount` per creare una connessione con Azure.

- **Utenti dell'interfaccia della riga di comando di Azure**: è possibile eseguire i comandi nel [Azure cloud Shell](https://shell.azure.com/bash)o eseguire l'interfaccia della riga di comando dal computer. Usare l'interfaccia della riga di comando di Azure versione 2.0.26 o successiva se si esegue l'interfaccia della riga di comando di Azure Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Eseguire `az login` per creare una connessione con Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Aggiungere interfacce di rete esistenti a una nuova macchina virtuale

Quando si crea una macchina virtuale tramite il portale, il portale crea un'interfaccia di rete con le impostazioni predefinite e connette l'interfaccia di rete alla macchina virtuale. Non è possibile usare il portale per aggiungere interfacce di rete esistenti a una nuova macchina virtuale o per creare una macchina virtuale con più interfacce di rete. È possibile eseguire entrambe le operazioni usando l'interfaccia della riga di comando o PowerShell. Assicurarsi di acquisire familiarità con i [vincoli](#constraints). Se si crea una macchina virtuale con più interfacce di rete, è anche necessario configurare il sistema operativo per usarle correttamente dopo aver creato la macchina virtuale. Informazioni su come configurare [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) per più interfacce di rete.

### <a name="commands"></a>Comandi:

Prima di creare la macchina virtuale, [creare un'interfaccia di rete](virtual-network-network-interface.md#create-a-network-interface).

|Strumento|Comando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Aggiungere un'interfaccia di rete a una macchina virtuale esistente

Per aggiungere un'interfaccia di rete alla macchina virtuale:

1. Passare alla [portale di Azure](https://portal.azure.com) per trovare una macchina virtuale esistente. Cercare e selezionare **Macchine virtuali**.

2. Selezionare il nome della macchina virtuale. La macchina virtuale deve supportare il numero di interfacce di rete da aggiungere. Per conoscere il numero di interfacce di rete supportate da ogni dimensione di macchina virtuale, vedere le dimensioni in Azure per [VM Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [VM Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Nella barra dei comandi della macchina virtuale selezionare **Arresta**, quindi **OK** nella finestra di dialogo di conferma. Attendere quindi che lo **stato** della macchina virtuale venga modificato in **arrestato (deallocato)**.

4. Dalla barra dei menu della macchina virtuale scegliere **rete** > **collegare l'interfaccia di rete**. Quindi, in **collegare l'interfaccia di rete esistente**, scegliere l'interfaccia di rete che si vuole collegare e fare clic su **OK**.

    >[!NOTE]
    >L'interfaccia di rete selezionata non può essere abilitata per la rete accelerata, non può avere un indirizzo IPv6 assegnato e deve essere presente nella stessa rete virtuale con l'interfaccia di rete attualmente collegata alla VM.

    Se non è disponibile un'interfaccia di rete esistente, è prima di tutto necessario crearla. A tale scopo, selezionare **Crea interfaccia di rete**. Per altre informazioni su come creare un'interfaccia di rete, vedere [Creare un'interfaccia di rete](virtual-network-network-interface.md#create-a-network-interface). Per altre informazioni sui vincoli aggiuntivi esistenti per l'aggiunta di interfacce di rete alle macchine virtuali, vedere [Vincoli](#constraints).

5. Dalla barra dei menu della VM scegliere **Panoramica** > **Avvia** per riavviare la macchina virtuale.

A questo punto è possibile configurare il sistema operativo della macchina virtuale per l'uso corretto di più interfacce di rete. Informazioni su come configurare [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) per più interfacce di rete.

### <a name="commands"></a>Comandi:

|Strumento|Comando|
|---|---|
|CLI|[AZ VM NIC Add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (Reference); [procedura dettagliata](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (riferimento); [procedura dettagliata](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Visualizzare le interfacce di rete per una macchina virtuale

È possibile visualizzare le interfacce di rete attualmente collegate a una macchina virtuale per conoscere la configurazione e gli indirizzi IP assegnati di ogni interfaccia di rete. 

1. Passare alla [portale di Azure](https://portal.azure.com) per trovare una macchina virtuale esistente. Cercare e selezionare **Macchine virtuali**.

    >[!NOTE]
    >Accedere con un account a cui è assegnato il ruolo proprietario, collaboratore o collaboratore rete per la sottoscrizione. Per altre informazioni su come assegnare i ruoli agli account, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).

2. Selezionare il nome della macchina virtuale per cui si vogliono visualizzare le interfacce di rete collegate.

3. Nella barra dei menu della macchina virtuale selezionare **rete**.

Per informazioni sulle impostazioni dell'interfaccia di rete e su come modificarle, vedere [Gestire le interfacce di rete](virtual-network-network-interface.md). Per informazioni su come aggiungere, modificare o rimuovere gli indirizzi IP assegnati a un'interfaccia di rete, vedere [Gestire gli indirizzi IP dell'interfaccia di rete](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Comandi:

|Strumento|Comando|
|---|---|
|CLI|[AZ VM NIC List](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Rimuovere un'interfaccia di rete da una macchina virtuale

1. Passare alla [portale di Azure](https://portal.azure.com) per trovare una macchina virtuale esistente. Cercare e selezionare **Macchine virtuali**.

2. Selezionare il nome della macchina virtuale per cui si vogliono visualizzare le interfacce di rete collegate.

3. Nella barra degli strumenti della VM selezionare **Arresta**.

4. Aspettare che lo **Stato** della macchina virtuale venga modificato in **Arrestata (deallocata)**.

5. Dalla barra dei menu della macchina virtuale scegliere **rete** > **Scollega interfaccia di rete**.

6. Nella finestra di dialogo **Scollega interfaccia di rete** selezionare l'interfaccia di rete che si desidera scollegare. Selezionare **OK**.

    >[!NOTE]
    >Se è elencata una sola interfaccia di rete, non è possibile scollegarla perché a una macchina virtuale deve essere sempre associata almeno un'interfaccia di rete.

### <a name="commands"></a>Comandi:

|Strumento|Comando|
|---|---|
|CLI|[AZ VM NIC Remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (riferimento); [procedura dettagliata](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (riferimento); [procedura dettagliata](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Vincoli

- Una macchina virtuale deve avere almeno un'interfaccia di rete collegata,

- fino al numero massimo supportato dalle dimensioni della macchina virtuale stessa. Per altre informazioni sul numero di interfacce di rete supportate da ogni dimensione di macchina virtuale, vedere le dimensioni in Azure per [macchine virtuali Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [macchine virtuali Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tutte le dimensioni supportano almeno due interfacce di rete.

- Le interfacce di rete aggiunte a una macchina virtuale non possono attualmente essere collegate a un'altra macchina virtuale. Per altre informazioni su come creare interfacce di rete, vedere [Creare un'interfaccia di rete](virtual-network-network-interface.md#create-a-network-interface).

- In passato, era possibile aggiungere interfacce di rete solo alle macchine virtuali che supportavano più interfacce di rete e sono state create con almeno due interfacce di rete. Non è stato possibile aggiungere un'interfaccia di rete a una macchina virtuale creata con un'interfaccia di rete, anche se le dimensioni della macchina virtuale supportano più di un'interfaccia di rete. Al contrario, era possibile rimuovere le interfacce di rete solo dalle macchine virtuali con almeno tre interfacce di rete, perché alle macchine virtuali create con almeno due interfacce di rete ne dovevano essere sempre collegate almeno due. Questi vincoli non sono più applicabili. È ora possibile creare una macchina virtuale con un numero qualsiasi di interfacce di rete (fino al numero massimo supportato dalle dimensioni della macchina virtuale).

- Per impostazione predefinita, la prima interfaccia di rete collegata a una VM è l'interfaccia di rete *primaria* . Tutte le altre interfacce di rete nella macchina virtuale sono interfacce di rete *secondarie*.

- È possibile controllare l'interfaccia di rete a cui si invia il traffico in uscita. Tuttavia, per impostazione predefinita, una macchina virtuale Invia tutto il traffico in uscita all'indirizzo IP assegnato alla configurazione IP primaria dell'interfaccia di rete primaria.

- In passato, tutte le macchine virtuali nello stesso set di disponibilità dovevano avere una o più interfacce di rete. Ora possono esistere macchine virtuali con un numero qualsiasi di interfacce di rete nello stesso set di disponibilità, fino al numero supportato dalla dimensione della macchina virtuale. Una macchina virtuale può essere aggiunta a un set di disponibilità solo in fase di creazione. Per altre informazioni sui set di disponibilità, vedere [Gestire la disponibilità delle macchine virtuali Windows in Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

- È possibile connettere le interfacce di rete nella stessa macchina virtuale a subnet diverse all'interno di una rete virtuale. Tuttavia, le interfacce di rete devono essere tutte connesse alla stessa rete virtuale.

- È possibile aggiungere qualsiasi indirizzo IP per qualsiasi configurazione IP di un'interfaccia di rete primaria e secondaria a un pool back-end di Azure Load Balancer. In passato, era possibile aggiungere a un pool di back-end solo l'indirizzo IP primario per l'interfaccia di rete primaria. Per altre informazioni sugli indirizzi e le configurazioni IP, vedere [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md).

- L'eliminazione di una macchina virtuale non comporta l'eliminazione delle interfacce di rete collegate. Quando si elimina una macchina virtuale, le interfacce di rete vengono scollegate dalla macchina virtuale. È possibile aggiungere tali interfacce di rete a macchine virtuali diverse o eliminarle.

- Analogamente a IPv6, non è possibile collegare un'interfaccia di rete con rete accelerata abilitata a una macchina virtuale dopo averla creata. Inoltre, per sfruttare i vantaggi della rete accelerata, è anche necessario completare i passaggi all'interno del sistema operativo della macchina virtuale. Sono disponibili altre informazioni sulle rete accelerata e altri vincoli per il relativo uso per macchine virtuali [Windows](create-vm-accelerated-networking-powershell.md) o [Linux](create-vm-accelerated-networking-cli.md).

## <a name="next-steps"></a>Passaggi successivi

Per creare una VM con più interfacce di rete o indirizzi IP, vedere:

|Attività|Strumento|
|---|---|
|Creare una macchina virtuale con più NIC|[Interfaccia della riga di comando](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creare una macchina virtuale con una singola scheda di interfaccia di rete e più indirizzi IPv4|[Interfaccia della riga di comando](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Creare una macchina virtuale con una singola scheda di interfaccia di rete con un indirizzo IPv6 privato (dietro un Azure Load Balancer)|[Interfaccia della riga di comando](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Modello di Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
