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
ms.date: 07/25/2017
ms.author: jdial
ms.openlocfilehash: 7df1dfbea8c985907d5330819dc1e7bf1578aafa
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>Aggiungere o rimuovere interfacce di rete da macchine virtuali

Informazioni su come aggiungere un'interfaccia di rete esistente durante la creazione di una macchina virtuale e su come aggiungere o rimuovere le interfacce di rete da una macchina virtuale esistente in stato di arresto, ovvero deallocata. Un'interfaccia di rete consente a una macchina virtuale di Azure di comunicare con Internet, Azure e le risorse locali. Una macchina virtuale può avere una o più interfacce di rete. 

Se si desidera aggiungere, modificare o rimuovere indirizzi IP da un'interfaccia di rete, leggere l'articolo [Gestire gli indirizzi IP dell’interfaccia di rete](virtual-network-network-interface-addresses.md). Se è necessario creare, modificare o eliminare le interfacce di rete, leggere l'articolo [Gestire le interfacce di rete](virtual-network-network-interface.md).

## <a name="before"></a>Prima di iniziare

Prima di completare qualsiasi passaggio nelle altre sezioni di questo articolo, eseguire le operazioni seguenti:

- Per informazioni sul numero di interfacce di rete supportate per ogni dimensione di macchina virtuale Linux e Windows, vedere gli articoli relativi alle dimensioni delle macchine virtuali [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Accedere al [portale di Azure](https://portal.azure.com), all'interfaccia della riga di comando di Azure oppure ad Azure PowerShell con un account Azure. Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usano comandi di PowerShell per completare le attività di questo articolo, [installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dei cmdlet di Azure PowerShell. Per informazioni ed esempi relativi ai comandi di PowerShell, digitare `get-help <command> -full`.
- Se si usano comandi dell'interfaccia della riga di comando di Azure per completare le attività di questo articolo, [installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `az <command> --help`. Invece di installare l'interfaccia della riga di comando e i rispettivi prerequisiti, è possibile usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Per usare Cloud Shell, fare clic sul pulsante Cloud Shell **>_** nella parte superiore del [portale](https://portal.azure.com).

## <a name="about"></a>Interfacce di rete e macchine virtuali

È possibile aggiungere, collegare, un'interfaccia di rete esistente a una macchina virtuale quando la si crea purché l'interfaccia di rete non sia attualmente collegata a un'altra macchina virtuale. È possibile aggiungere o rimuovere, scollegare, un'interfaccia di rete da una macchina virtuale esistente purché questa sia in stato di arresto, deallocata. Se si crea una macchina virtuale tramite il portale di Azure, il portale crea un'interfaccia di rete con le impostazioni predefinite. Il portale non consente di:

- Specificare un'interfaccia di rete esistente da aggiungere quando si crea la macchina virtuale
- Creare una VM con più interfacce di rete
- Specificare un nome per l'interfaccia di rete. Il portale la crea con un nome predefinito.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando per creare un'interfaccia di rete o una macchina virtuale con tutti gli attributi indicati in precedenza per i quali non è possibile usare il portale. Prima di completare le attività nelle sezioni seguenti, prendere in considerazione i vincoli e i comportamenti seguenti:

- Tutte le dimensioni di macchina virtuale supportano almeno due interfacce di rete, ma alcune dimensioni di macchina virtuale ne supportano più di due. In passato alcune dimensioni di macchine virtuali supportavano una sola interfaccia di rete. Per informazioni sul numero di interfacce di rete supportate per ogni dimensione di macchina virtuale, vedere gli articoli relativi alle dimensioni delle macchine virtuali [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- In precedenza, le interfacce di rete potevano essere aggiunte solo a macchine virtuali in grado di supportare più interfacce di rete e create con almeno due interfacce di rete. Non era possibile aggiungere un'interfaccia di rete a una macchina virtuale creata con un'interfaccia di rete, anche se le dimensioni della macchina virtuale supportavano più interfacce di rete. Al contrario, era possibile rimuovere le interfacce di rete solo dalle macchine virtuali con almeno tre interfacce di rete, perché alle macchine virtuali create con almeno due interfacce di rete ne dovevano essere sempre collegate almeno due. Attualmente non si applica alcuno di questi vincoli. Ora è possibile creare una macchina virtuale con un numero qualsiasi di interfacce di rete, fino al numero supportato dalla dimensione della macchina virtuale, e aggiungere o rimuovere qualsiasi numero di interfacce di rete, da una macchina virtuale in stato di arresto (deallocata), purché alla macchina virtuale sia sempre collegata almeno una scheda di interfaccia di rete.
- Per impostazione predefinita, la prima interfaccia di rete in una macchina virtuale è definita come interfaccia di rete *primaria*. Tutte le altre interfacce di rete nella macchina virtuale sono interfacce di rete *secondarie*.
- Alle interfacce di rete primarie è assegnato un gateway predefinito dai server DHCP di Azure mentre ciò non avviene per le interfacce di rete secondarie. Poiché alle interfacce di rete secondarie non è assegnato un gateway predefinito, non possono comunicare con le risorse all'esterno della loro subnet per impostazione predefinita. Per abilitare le interfacce di rete secondarie per comunicare con risorse esterne alla subnet, vedere [Routing in un sistema operativo di macchina virtuale con più interfacce di rete](#routing-within-a-virtual-machine-operating-system-with-multiple-network-interfaces).
- Per impostazione predefinita, tutto il traffico in uscita dalla macchina virtuale viene inviato all'indirizzo IP assegnato alla configurazione IP primaria dell'interfaccia di rete primaria. È possibile scegliere l'indirizzo IP da usare per il traffico in uscita all'interno del sistema operativo della macchina virtuale, ma per impostazione predefinita passa attraverso l'interfaccia di rete primaria.
- In passato, tutte le macchine virtuali nello stesso set di disponibilità dovevano avere una o più interfacce di rete. Ora possono esistere macchine virtuali con un numero qualsiasi di interfacce di rete nello stesso set di disponibilità, fino al numero supportato dalla dimensione della macchina virtuale. Una VM può essere aggiunta a un set di disponibilità solo in fase di creazione. Per altre informazioni sui set di disponibilità, vedere l'articolo [Gestire la disponibilità delle VM Windows in Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Anche se le interfacce di rete nella stessa macchina virtuale possono essere connesse a subnet diverse all'interno di una rete virtuale, le interfacce di rete devono essere tutte connesse alla stessa rete virtuale.
- È possibile aggiungere qualsiasi indirizzo IP per qualsiasi configurazione IP di un'interfaccia di rete primaria e secondaria a un pool back-end di Azure Load Balancer. In passato, era possibile aggiungere a un pool di back-end solo l'indirizzo IP primario per l'interfaccia di rete primaria. Per altre informazioni sugli indirizzi e le configurazioni IP, leggere l'articolo [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md).
- Se si elimina una macchina virtuale, le interfacce di rete virtuale connesse a questa non vengono eliminate. Quando si elimina una macchina virtuale, le interfacce di rete vengono scollegate dalla macchina virtuale. È possibile aggiungere le interfacce di rete a macchine virtuali diverse o eliminarle.
- Se a un'interfaccia di rete è assegnato un indirizzo IPv6 privato, è possibile collegarla a una macchina virtuale quando si crea la macchina virtuale. Dopo aver creato la macchina virtuale, non è più possibile collegare un'interfaccia di rete con un indirizzo IPv6 assegnato a una macchina virtuale. Se si collega un'interfaccia di rete con un indirizzo IPv6 privato assegnato durante la creazione di una macchina virtuale, è possibile collegare solo questa interfaccia di rete specifica alla macchina virtuale, indipendentemente dal numero di interfacce di rete supportato dalle dimensioni della macchina virtuale. Vedere [Indirizzi IP dell'interfaccia di rete](virtual-network-network-interface-addresses.md) per ulteriori informazioni sull'assegnazione di indirizzi IP alle interfacce di rete.

## <a name="vm-create"></a>Aggiungere interfacce di rete esistenti a una nuova macchina virtuale

Quando si crea una macchina virtuale tramite il portale, il portale crea un'interfaccia di rete con le impostazioni predefinite e la collega alla macchina virtuale automaticamente. Non è possibile aggiungere interfacce di rete esistenti a una nuova macchina virtuale o creare una macchina virtuale con più interfacce di rete tramite il portale di Azure. È possibile fare entrambe le cose usando l'interfaccia della riga di comando o PowerShell. È possibile aggiungere a una macchina virtuale tutte le interfacce di rete che la dimensione della macchina virtuale che si sta creando può supportare. Per altre informazioni sul numero di interfacce di rete supportate per ogni dimensione di macchina virtuale, vedere gli articoli relativi alle dimensioni delle macchine virtuali [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Le interfacce di rete che si aggiungono a una macchina virtuale non possono essere collegate a un'altra macchina virtuale. Per altre informazioni sulla creazione delle interfacce di rete, leggere l'articolo [Gestire le interfacce di rete](virtual-network-network-interface.md#create-a-network-interface).

### <a name="routing-within-a-virtual-machine-operating-system-with-multiple-network-interfaces"></a>Routing in un sistema operativo di macchina virtuale con più interfacce di rete

Azure assegna un gateway predefinito alla prima interfaccia di rete (primaria) associata alla macchina virtuale. Azure non assegna un gateway predefinito ad altre interfacce di rete (secondarie) associate a una macchina virtuale. Di conseguenza, per impostazione predefinita, non è possibile comunicare con risorse esterne alla subnet in cui si trova un'interfaccia di rete secondaria. Le interfacce di rete secondarie possono tuttavia comunicare con risorse esterne alla subnet, ma i passaggi per abilitare la comunicazione variano a seconda del sistema operativo.

### <a name="windows"></a>Windows

Eseguire le operazioni seguenti da un prompt dei comandi di Windows:

1. Eseguire il comando `route print` che restituisce un output simile al seguente per una macchina virtuale con due interfacce di rete associate:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    In questo esempio la **Scheda di rete Hyper-V Microsoft 4** (interfaccia 7) è l'interfaccia di rete secondaria senza un gateway predefinito assegnato.

2. Da un prompt dei comandi, eseguire il comando `ipconfig` per individuare l'indirizzo IP assegnato all'interfaccia di rete secondaria. In questo esempio 192.168.2.4 è assegnato all'interfaccia 7. Per l'interfaccia di rete secondaria non viene restituito alcun indirizzo di gateway predefinito.

3. Per instradare tutto il traffico destinato agli indirizzi esterni alla subnet dell'interfaccia di rete secondaria al gateway per la subnet, eseguire il comando seguente:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    L'indirizzo del gateway per la subnet è il primo indirizzo IP (che termina con .1) dell'intervallo di indirizzi definito per la subnet. Se non si vuole instradare tutto il traffico all'esterno della subnet, è possibile aggiungere singole route a destinazioni specifiche. Ad esempio, per instradare solo il traffico dall'interfaccia di rete secondaria alla rete 192.168.3.0, immettere il comando:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Ad esempio, per verificare che sia attiva la comunicazione con una risorsa nella rete 192.168.3.0, immettere il comando seguente per eseguire il ping di 192.168.3.4 usando l'interfaccia 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Potrebbe essere necessario aprire il protocollo ICMP attraverso il firewall di Windows del dispositivo di cui viene eseguito il ping con il comando seguente:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Per verificare che la route aggiunta sia inclusa nella tabella di route, immettere il comando `route print` che restituisce un output simile al testo seguente:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    La route specificata con *192.168.1.1* in **Gateway** è la route predefinita per l'interfaccia di rete primaria. La route con *192.168.2.1* in **Gateway** è la route aggiunta.

### <a name="linux"></a>Linux

Poiché il comportamento predefinito usa un routing host vulnerabile, è consigliabile limitare il traffico delle interfacce di rete secondarie tra le risorse alla stessa subnet. Se è necessario che le interfacce di rete secondarie comunichino all'esterno della subnet, creare regole di routing che consentano alla macchina virtuale di inviare e ricevere traffico attraverso un'interfaccia di rete specifica. In caso contrario, il traffico appartenente a eth1, ad esempio, non potrà essere elaborato correttamente dalla route predefinita specificata. Per informazioni su come configurare le regole di routing, vedere [Configure Linux for multiple NICs](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) (Configurare Linux per più schede di rete).

> [!WARNING]
> Se a un'interfaccia di rete è assegnato un indirizzo IPv6 privato, è possibile aggiungere l'interfaccia di rete alla macchina virtuale solo quando quest'ultima viene creata. Non è possibile collegare più interfacce di rete alla macchina virtuale durante o dopo la creazione della macchina virtuale finché l'indirizzo IPv6 è assegnato a un'interfaccia di rete collegata alla macchina virtuale. Vedere [Indirizzi IP dell'interfaccia di rete](virtual-network-network-interface-addresses.md) per ulteriori informazioni sull'assegnazione di indirizzi IP alle interfacce di rete.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Aggiungere un'interfaccia di rete esistente a una macchina virtuale esistente

È possibile aggiungere a una macchina virtuale tutte le interfacce di rete che la dimensione della macchina virtuale che si desidera aggiungere alle interfacce di rete può supportare. Per informazioni sul numero di interfacce di rete supportate per ogni dimensione di macchina virtuale, vedere gli articoli relativi alle dimensioni delle macchine virtuali [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). La macchina virtuale alla quale si desidera aggiungere un'interfaccia di rete deve supportare il numero di interfacce di rete che si desidera aggiungere e deve essere in stato di arresto, ovvero deallocata. Le interfacce di rete che si desidera aggiungere non possono essere collegate a un'altra macchina virtuale. Non è possibile aggiungere interfacce di rete a una macchina virtuale esistente tramite il portale di Azure. Per aggiungere interfacce di rete a una macchina virtuale esistente, è necessario usare l'interfaccia della riga di comando o PowerShell. 

Azure assegna un gateway predefinito alla prima interfaccia di rete (primaria) associata alla macchina virtuale. Azure non assegna un gateway predefinito ad altre interfacce di rete (secondarie) associate a una macchina virtuale. Di conseguenza, per impostazione predefinita, non è possibile comunicare con risorse esterne alla subnet in cui si trova un'interfaccia di rete secondaria. Le interfacce di rete secondarie possono tuttavia comunicare con risorse esterne alla loro subnet. Se è necessario che le interfacce di rete secondarie comunichino con risorse esterne alla subnet, vedere [Routing in un sistema operativo di macchina virtuale con più interfacce di rete](#routing-within-a virtual-machine-operating-system-with-multiple-network-interfaces).

> [!WARNING]
> Se a un'interfaccia di rete è stato assegnato un indirizzo IPv6 privato, non può essere aggiunto a una macchina virtuale esistente. È possibile aggiungere un'interfaccia di rete con un indirizzo IPv6 privato a una macchina virtuale solo quando la macchina virtuale viene creata. Vedere [Indirizzi IP dell'interfaccia di rete](virtual-network-network-interface-addresses.md) per ulteriori informazioni sull'assegnazione di indirizzi IP alle interfacce di rete.

|Strumento|Comando|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add) (riferimento) o [passaggi dettagliati](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (riferimento) o [passaggi dettagliati](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="vm-view-nic"></a> Visualizzare le interfacce di rete di una macchina virtuale

È possibile visualizzare le interfacce di rete attualmente collegate a una macchina virtuale per conoscere la configurazione e gli indirizzi IP assegnati di ogni interfaccia di rete. 

1. Accedere al [portale di Azure](https://portal.azure.com) con un account che abbia le autorizzazioni per il ruolo di Proprietario, Collaboratore o Collaboratore Rete per la sottoscrizione. Per altre informazioni sull'assegnazione dei ruoli agli account, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *macchine virtuali*. Fare clic su **macchine virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Macchine virtuali** visualizzato fare clic sul nome della macchina virtuale per la quale si desidera visualizzare le interfacce di rete.
4. Nella sezione **Impostazioni** del pannello visualizzato per la macchina virtuale selezionata fare clic su **Rete**. Per informazioni sulle impostazioni dell'interfaccia di rete e su come modificarle, vedere l'articolo [Gestire le interfacce di rete](virtual-network-network-interface.md). Per informazioni su come aggiungere, modificare o rimuovere indirizzi IP assegnati a un'interfaccia di rete, vedere [Gestione di indirizzi IP](virtual-network-network-interface-addresses.md).

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a> Rimuovere l’interfaccia di rete da una macchina virtuale

La macchina virtuale da cui si vuole rimuovere (o rendere non visibile) un'interfaccia di rete deve essere in stato di arresto (deallocata) e avere almeno due interfacce di rete collegate. È possibile rimuovere qualsiasi interfaccia di rete, ma la macchina virtuale deve sempre avere almeno un'interfaccia di rete collegata. Se si rimuove un'interfaccia di rete primaria, Azure assegna l'attributo principale all'interfaccia di rete che è collegata alla macchina virtuale da più tempo. 

1. Accedere al [portale di Azure](https://portal.azure.com) con un account che abbia le autorizzazioni per il ruolo di Proprietario, Collaboratore o Collaboratore Rete per la sottoscrizione. Per altre informazioni sull'assegnazione dei ruoli agli account, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *macchine virtuali*. Fare clic su **macchine virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Macchine virtuali** visualizzato fare clic sul nome della macchina virtuale per la quale si vuole rimuovere un'interfaccia di rete.
4. Nella sezione **Impostazioni** del pannello visualizzato per la macchina virtuale selezionata fare clic su **Rete**. Per informazioni sulle impostazioni dell'interfaccia di rete e su come modificarle, vedere l'articolo [Gestire le interfacce di rete](virtual-network-network-interface.md). Per informazioni su come aggiungere, modificare o rimuovere indirizzi IP assegnati a un'interfaccia di rete, vedere [Gestione di indirizzi IP](virtual-network-network-interface-addresses.md).
5. Fare clic su **Scollega interfaccia di rete**.
6. Selezionare l'interfaccia di rete da scollegare dall'elenco a discesa, quindi fare clic su **OK**.

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
