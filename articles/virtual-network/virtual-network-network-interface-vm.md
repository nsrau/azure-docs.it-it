---
title: Aggiungere o rimuovere interfacce di rete da macchine virtuali di Azure
description: Informazioni su come aggiungere o rimuovere interfacce di rete dalle macchine virtuali.
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
ms.date: 12/15/2017
ms.author: kumud
ms.openlocfilehash: a55bf014a2da10069e4e6a5f6f4eb4b8cd9ff205
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383183"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Aggiungere o rimuovere interfacce di rete da macchine virtuali

Informazioni su come aggiungere un'interfaccia di rete esistente durante la creazione di una macchina virtuale (VM) di Azure o per aggiungere o rimuovere interfacce di rete da una VM esistente in stato di arresto (deallocata). Un'interfaccia di rete consente a una macchina virtuale di Azure di comunicare con Internet, Azure e le risorse locali. Una macchina virtuale può avere una o più interfacce di rete. 

Se è necessario aggiungere, modificare o rimuovere indirizzi IP per un'interfaccia di rete, vedere [gestire gli indirizzi IP dell'interfaccia di rete](virtual-network-network-interface-addresses.md). Se è necessario creare, modificare o eliminare le interfacce di rete, vedere [gestire le interfacce di rete](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prima di completare i passaggi di qualsiasi sezione di questo articolo, completare le attività seguenti:

- Se non si ha già un account Azure, iscriversi per ottenere un [account di valutazione gratuito](https://azure.microsoft.com/free).
- Se si usa il portale, aprire https://portal.azure.come accedere con l'account Azure.
- Se si usano i comandi di PowerShell per completare le attività descritte in questo articolo, eseguire i comandi nel [Azure cloud Shell](https://shell.azure.com/powershell)o eseguire PowerShell dal computer. Il Azure Cloud Shell è una shell interattiva gratuita che è possibile usare per eseguire la procedura descritta in questo articolo. Con gli strumenti di Azure comuni preinstallati e configurati per l'uso con l'account. Per questa esercitazione è necessario il modulo Azure PowerShell versione 1.0.0 o successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Install Azure PowerShell Module](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.
- Se si usano i comandi dell'interfaccia della riga di comando di Azure per completare le attività descritte in questo articolo, eseguire i comandi nel [Azure cloud Shell](https://shell.azure.com/bash)o eseguendo l'interfaccia della riga di comando dal computer. Questa esercitazione richiede l'interfaccia della riga di comando di Azure versione 2.0.26 o successiva. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia](/cli/azure/install-azure-cli)della riga di comando di Azure. Se si esegue l'interfaccia della riga di comando di Azure in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Aggiungere interfacce di rete esistenti a una nuova macchina virtuale

Quando si crea una macchina virtuale tramite il portale, il portale crea un'interfaccia di rete con le impostazioni predefinite e la collega alla macchina virtuale. Non è possibile aggiungere interfacce di rete esistenti a una nuova macchina virtuale, né creare una macchina virtuale con più interfacce di rete, usando il portale di Azure. È possibile eseguire entrambe le operazioni usando l'interfaccia della riga di comando o PowerShell, ma assicurarsi di acquisire familiarità con i [vincoli](#constraints). Se si crea una macchina virtuale con più interfacce di rete, è necessario configurare anche il sistema operativo per usarle correttamente dopo la creazione della macchina virtuale. Informazioni su come configurare [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) per più interfacce di rete.

### <a name="commands"></a>Comandi

Prima di creare la macchina virtuale, creare un'interfaccia di rete usando la procedura descritta in [creare un'interfaccia di rete](virtual-network-network-interface.md#create-a-network-interface).

|Strumento|Comando|
|---|---|
|CLI|[AZ VM create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Aggiungere un'interfaccia di rete a una macchina virtuale esistente

1. Accedere al portale di Azure.
2. Nella casella di ricerca nella parte superiore del portale digitare il nome della macchina virtuale a cui si vuole aggiungere l'interfaccia di rete o cercare la macchina virtuale selezionando **tutti i servizi**e quindi **macchine virtuali**. Dopo aver trovato la macchina virtuale, selezionarla. La macchina virtuale deve supportare il numero di interfacce di rete che si desidera aggiungere. Per conoscere il numero di interfacce di rete supportate da ogni dimensione di macchina virtuale, vedere [dimensioni delle macchine virtuali Linux in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [dimensioni per le macchine virtuali Windows in Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Selezionare **Panoramica**in **Impostazioni**. Selezionare **Arresta**, quindi attendere che lo **stato** della macchina virtuale venga modificato in **arrestato (deallocato)** .
4. Selezionare **rete**in **Impostazioni**.
5. Selezionare **Connetti interfaccia di rete**. Dall'elenco di interfacce di rete attualmente collegate a un'altra VM, selezionare quella che si vuole collegare.

   >[!NOTE]
   >Per l'interfaccia di rete selezionata non è possibile abilitare la rete accelerata, non è possibile assegnargli un indirizzo IPv6 e deve essere presente nella stessa rete virtuale di quella che contiene l'interfaccia di rete attualmente collegata alla macchina virtuale.

   Se non si dispone di un'interfaccia di rete esistente, è innanzitutto necessario crearne una. A tale scopo, selezionare **Crea interfaccia di rete**. Per altre informazioni su come creare un'interfaccia di rete, vedere [creare un'interfaccia di rete](virtual-network-network-interface.md#create-a-network-interface). Per ulteriori informazioni sui vincoli aggiuntivi quando si aggiungono interfacce di rete alle macchine virtuali, vedere [vincoli](#constraints).

6. Fare clic su **OK**.
7. Selezionare **Panoramica**, in **Impostazioni**, quindi **avviare** per avviare la macchina virtuale.
8. Configurare il sistema operativo della macchina virtuale per l'uso corretto di più interfacce di rete. Informazioni su come configurare [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) per più interfacce di rete.

### <a name="commands"></a>Comandi
|Strumento|Comando|
|---|---|
|CLI|[AZ VM NIC Add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (Reference) o [passaggi dettagliati](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (riferimento) o [passaggi dettagliati](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Visualizzare le interfacce di rete per una macchina virtuale

È possibile visualizzare le interfacce di rete attualmente collegate a una macchina virtuale per ottenere informazioni sulla configurazione di ogni interfaccia di rete e sugli indirizzi IP assegnati a ogni interfaccia di rete. 

1. Accedere al [portale di Azure](https://portal.azure.com) con un account a cui è assegnato il ruolo proprietario, collaboratore o collaboratore rete per la sottoscrizione. Per altre informazioni su come assegnare i ruoli agli account, vedere [ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Nella casella contenente le **risorse di ricerca** del testo nella parte superiore del portale di Azure digitare **macchine virtuali**. Selezionare la finestra **macchine virtuali** visualizzata nei risultati della ricerca.
3. Selezionare il nome della macchina virtuale per cui si desidera visualizzare le interfacce di rete.
4. Nella sezione **Impostazioni** per la macchina virtuale selezionata selezionare **rete**. Per informazioni sulle impostazioni dell'interfaccia di rete e su come modificarle, vedere [gestire le interfacce di rete](virtual-network-network-interface.md). Per informazioni su come aggiungere, modificare o rimuovere indirizzi IP assegnati a un'interfaccia di rete, vedere [gestire gli indirizzi IP dell'interfaccia di rete](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Comandi

|Strumento|Comando|
|---|---|
|CLI|[AZ VM Show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Rimuovere un'interfaccia di rete da una macchina virtuale

1. Accedere al portale di Azure.
2. Nella casella di ricerca nella parte superiore del portale cercare il nome della macchina virtuale da cui si vuole rimuovere (scollegare) l'interfaccia di rete o cercare la macchina virtuale selezionando **tutti i servizi**e quindi **macchine virtuali**. Dopo aver trovato la macchina virtuale, selezionarla.
3. Selezionare **Panoramica**, in **Impostazioni**, quindi **arrestare**. Attendere che lo **stato** della macchina virtuale venga modificato in **arrestato (deallocato)** .
4. Selezionare **rete**in **Impostazioni**.
5. Selezionare **Scollega interfaccia di rete**. Dall'elenco di interfacce di rete attualmente collegate alla macchina virtuale, selezionare l'interfaccia di rete che si desidera scollegare.

   >[!NOTE]
   >Se è elencata una sola interfaccia di rete, non è possibile scollegarla perché a una macchina virtuale deve essere sempre associata almeno un'interfaccia di rete.
6. Fare clic su **OK**.

### <a name="commands"></a>Comandi

|Strumento|Comando|
|---|---|
|CLI|[AZ VM NIC Remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (riferimento) o [passaggi dettagliati](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (riferimento) o [passaggi dettagliati](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Vincoli

- Una macchina virtuale deve avere almeno un'interfaccia di rete collegata.
- Una macchina virtuale può avere solo il numero di interfacce di rete associate, come supportato dalle dimensioni della macchina virtuale. Per altre informazioni sul numero di interfacce di rete supportate da ogni dimensione di VM, vedere [dimensioni delle macchine virtuali Linux in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [dimensioni per le macchine virtuali Windows in Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tutte le dimensioni supportano almeno due interfacce di rete.
- Le interfacce di rete aggiunte a una macchina virtuale non possono attualmente essere collegate a un'altra macchina virtuale. Per altre informazioni su come creare interfacce di rete, vedere [creare un'interfaccia di rete](virtual-network-network-interface.md#create-a-network-interface).
- In passato, le interfacce di rete potevano essere aggiunte solo a macchine virtuali che supportavano più interfacce di rete e sono state create con almeno due interfacce di rete. Non è stato possibile aggiungere un'interfaccia di rete a una macchina virtuale creata con un'interfaccia di rete, anche se le dimensioni della macchina virtuale supportano più interfacce di rete. Viceversa, è possibile rimuovere solo le interfacce di rete da una macchina virtuale con almeno tre interfacce di rete, perché le macchine virtuali create con almeno due interfacce di rete devono avere sempre almeno due interfacce di rete. Nessuno di questi vincoli si applica più. È ora possibile creare una macchina virtuale con un numero qualsiasi di interfacce di rete (fino al numero supportato dalle dimensioni della macchina virtuale).
- Per impostazione predefinita, la prima interfaccia di rete collegata a una macchina virtuale è definita come interfaccia di rete *primaria* . Tutte le altre interfacce di rete nella macchina virtuale sono interfacce di rete *secondarie* .
- Sebbene sia possibile controllare l'interfaccia di rete a cui è stato inviato il traffico in uscita, per impostazione predefinita, tutto il traffico in uscita dalla macchina virtuale viene inviato all'indirizzo IP assegnato alla configurazione IP primaria dell'interfaccia di rete primaria.
- In passato, tutte le macchine virtuali nello stesso set di disponibilità dovevano avere un'unica o più interfacce di rete. Le VM con un numero qualsiasi di interfacce di rete possono ora esistere nello stesso set di disponibilità, fino al numero supportato dalle dimensioni della macchina virtuale. È possibile aggiungere una macchina virtuale solo a un set di disponibilità al momento della creazione. Per altre informazioni sui set di disponibilità, vedere [gestire la disponibilità delle macchine virtuali in Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Anche se le interfacce di rete nella stessa VM possono essere connesse a subnet diverse all'interno di una rete virtuale, le interfacce di rete devono essere tutte connesse alla stessa rete virtuale.
- È possibile aggiungere qualsiasi indirizzo IP per qualsiasi configurazione IP di qualsiasi interfaccia di rete primaria o secondaria a un pool di back-end Azure Load Balancer. In passato, era possibile aggiungere a un pool back-end solo l'indirizzo IP primario per l'interfaccia di rete primaria. Per altre informazioni sugli indirizzi IP e le configurazioni, vedere [aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md).
- L'eliminazione di una macchina virtuale non comporta l'eliminazione delle interfacce di rete collegate. Quando si elimina una macchina virtuale, le interfacce di rete vengono scollegate dalla macchina virtuale. È possibile aggiungere le interfacce di rete a macchine virtuali diverse o eliminarle.
- Analogamente a IPv6, non è possibile collegare un'interfaccia di rete con rete accelerata abilitata a una macchina virtuale dopo averla creata. Inoltre, per sfruttare i vantaggi della rete accelerata, è necessario completare i passaggi all'interno del sistema operativo della macchina virtuale. Scopri di più sulla rete accelerata e sugli altri vincoli che usi per le macchine virtuali [Windows](create-vm-accelerated-networking-powershell.md) o [Linux](create-vm-accelerated-networking-cli.md) .

## <a name="next-steps"></a>Passaggi successivi
Per creare una VM con più interfacce di rete o indirizzi IP, vedere gli articoli seguenti:

|Attività|Strumento|
|---|---|
|Creare una VM con più schede di rete|[Interfaccia](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)della riga di comando, [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creare una singola VM NIC con più indirizzi IPv4|[Interfaccia](virtual-network-multiple-ip-addresses-cli.md)della riga di comando, [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Creare una singola VM NIC con un indirizzo IPv6 privato (dietro una Azure Load Balancer)|[Interfaccia](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)della riga di comando, [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [modello di Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
