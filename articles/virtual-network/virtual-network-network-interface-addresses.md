---
title: Configurare gli indirizzi IP per le interfacce di rete di Azure | Microsoft Docs
description: Informazioni su come aggiungere, modificare e rimuovere indirizzi IP pubblici e privati dalle schede di interfaccia di rete.
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
ms.openlocfilehash: cf7dbc648136897772bbd068687313eec16bed75
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---

# <a name="add-change-or-remove-ip-addresses-for-azure-network-interfaces"></a>Aggiungere, modificare o rimuovere indirizzi IP per interfacce di rete di Azure

Informazioni su come aggiungere, modificare e rimuovere indirizzi IP pubblici e privati per interfacce di rete. Gli indirizzi IP privati assegnati a una scheda di interfaccia di rete consentono a una VM di comunicare con Internet e con altre risorse connesse a una rete virtuale di Azure (VNet). Gli indirizzi IP pubblici assegnati a una scheda di interfaccia di rete consentono la comunicazione in ingresso a una VM da Internet. 

Se è necessario creare, modificare o eliminare schede di interfaccia di rete, leggere l'articolo sulle [impostazioni e attività relative alle schede di interfaccia di rete](virtual-network-network-interface.md). Se è necessario aggiungere o eliminare schede di interfaccia di rete da macchine virtuali, leggere l'articolo su come [aggiungere o rimuovere schede di interfaccia di rete](virtual-network-network-interface-vm.md). 


## <a name="before"></a>Prima di iniziare

Prima di completare qualsiasi altro passaggio nelle altre sezioni di questo articolo, eseguire le operazioni seguenti:

- Per informazioni sui limiti per gli indirizzi IP pubblici e privati, leggere l'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Accedere al portale di Azure, all'interfaccia della riga di comando di Azure oppure ad Azure PowerShell con un account Azure. Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si sceglie di usare i comandi di PowerShell per completare le attività indicate in questo articolo, installare e configurare Azure PowerShell seguendo i passaggi descritti nell'articolo [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dei cmdlet di Azure PowerShell. Per informazioni ed esempi relativi ai comandi di PowerShell, digitare `get-help <command> -full`.
- Se si sceglie di usare i comandi dell'interfaccia della riga di comando di Azure per completare le attività indicate in questo articolo, installare e configurare l'interfaccia della riga di comando di Azure seguendo i passaggi descritti nell'articolo [Come installare e configurare Azure PowerShell](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `az <command> --help`.

## <a name="about"></a>Informazioni sulle schede di interfaccia di rete e gli indirizzi IP

A ogni scheda di interfaccia di rete possono essere assegnati più indirizzi IP privati e pubblici entro i [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). L'assegnazione di più indirizzi IP a un'interfaccia di rete è utile per gli scenari seguenti:
- Ospitare più siti Web o servizi con indirizzi IP e certificati SSL diversi in un singolo server.
- Una VM che funge da appliance virtuale di rete, ad esempio un firewall o un servizio di bilanciamento del carico.
- Aggiungere qualsiasi indirizzo IP per qualsiasi scheda di interfaccia di rete a un pool back-end di Azure Load Balancer. In passato, era possibile aggiungere a un pool di back-end solo gli indirizzi IP primari per la scheda di interfaccia di rete primaria. Per altre informazioni su come bilanciare il carico di più configurazioni IP, leggere l'articolo [Load balancing multiple IP configurations](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Bilanciamento del carico di più configurazioni IP).

Gli indirizzi IP vengono assegnati a una configurazione IP. A una scheda di interfaccia di rete viene sempre assegnata una configurazione IP **principale**, ma molte hanno anche assegnate più configurazioni **secondarie**. A ogni configurazione IP sono assegnati uno o entrambi i tipi di indirizzi seguenti:
- **Privato**: gli indirizzi IP privati consentono a una VM di comunicare con altre risorse connesse alla rete virtuale in cui la scheda di interfaccia di rete si trova. Ogni configurazione IP deve avere un solo indirizzo IP privato assegnato. I server DHCP di Azure assegnano l'indirizzo IP privato della configurazione IP primaria dell'interfaccia all'interfaccia di rete all'interno del sistema operativo della VM. Un indirizzo IP privato consente anche alla VM di comunicare verso l'esterno su Internet. Le connessioni in uscita sono SNAT (Source Network Address Translated). Per altre informazioni sulla connettività Internet in uscita di Azure, vedere l'articolo [Informazioni sulle connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Non è possibile comunicare verso l'interno con l'indirizzo IP privato di una VM da Internet.
- **Pubblico**: gli indirizzi IP pubblici consentono la connettività in ingresso verso una VM da Internet. Le connessioni in uscita a Internet non sono SNAT. È possibile assegnare un indirizzo IP pubblico a una configurazione IP, ma non è necessario. Per altre informazioni sugli indirizzi IP pubblici, vedere l'articolo [Indirizzo IP pubblico](virtual-network-public-ip-address.md).

È previsto un limite al numero di indirizzi IP pubblici e privati che possono essere assegnati a un'interfaccia di rete. Per informazioni dettagliate, vedere l'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

È possibile assegnare indirizzi IP pubblici e privati usando i seguenti metodi di allocazione:
- **Dinamico**: vengono assegnati indirizzi IP privati e pubblici dinamici per impostazione predefinita. Gli indirizzi dinamici possono cambiare se la VM viene messa nello stato di arresto (deallocata) e poi riavviata. Se si vuole che l'indirizzo IP non cambi per tutta la durata della VM, usare un indirizzo statico.
- **Indirizzi statici**: gli indirizzi statici non cambiano fino a quando la VM non viene eliminata. È necessario assegnare un indirizzo IP privato statico dallo spazio di indirizzi per la subnet a cui la scheda di interfaccia di rete è collegata. Per altre informazioni su come Azure assegna gli indirizzi IP pubblici statici, vedere l'articolo [Indirizzo IP pubblico](virtual-network-public-ip-address.md).

## <a name="create-ip-config"></a>Aggiungere indirizzi IP

È possibile aggiungere a una scheda di interfaccia di rete il numero di indirizzi IP necessari, entro i limiti elencati nell'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** che viene visualizzato fare clic sulla scheda di interfaccia di rete per cui aggiungere l'indirizzo IP.
4. Nel pannello per la scheda di interfaccia di rete selezionata fare clic su **Configurazioni IP** nella sezione **IMPOSTAZIONI**.
5. Fare clic su **+ Aggiungi** nel pannello visualizzato per le configurazioni IP.
6. Specificare le impostazioni seguenti, quindi fare clic su **OK** per chiudere il pannello **Aggiungi configurazione IP**:

    |Impostazione|Obbligatorio?|Dettagli|
    |---|---|---|
    |Nome|Sì|Deve essere univoco per l'interfaccia di rete|
    |Tipo|Sì|Dato che si aggiunge una configurazione IP a un'interfaccia di rete esistente e ogni interfaccia di rete deve avere una configurazione IP primaria, l'unica opzione possibile è **Secondaria**.|
    |Metodo di assegnazione di indirizzi IP privati|Sì|Gli indirizzi **dinamici** possono cambiare se la macchina virtuale viene riavviata dopo essere stata arrestata (deallocata). Azure assegna un indirizzo disponibile dallo spazio di indirizzi della subnet a cui la scheda di interfaccia di rete è connessa. Gli indirizzi IP **statici** non vengono rilasciati fino a quando non viene eliminata l'interfaccia di rete. Dall'intervallo di indirizzi della subnet specificare un indirizzo IP non attualmente usato da un'altra configurazione IP.|
    |Indirizzo IP pubblico|No|**Disabilitato:** nessuna risorsa di indirizzo IP pubblico è attualmente associata alla configurazione IP. **Abilitato:** selezionare un indirizzo IP pubblico esistente o crearne uno nuovo. Per informazioni su come creare un indirizzo IP pubblico, vedere l'articolo [Indirizzi IP](virtual-network-public-ip-address.md#create).|
7. Aggiungere manualmente gli indirizzi IP privati secondari al sistema operativo della VM seguendo le istruzioni disponibili nell'articolo [Assegnare più indirizzi IP alle macchine virtuali](virtual-network-multiple-ip-addresses-portal.md#os-config). Non aggiungere indirizzi IP pubblici al sistema operativo della VM.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-config"></a>Modificare le impostazioni degli indirizzi IP

Può essere necessario modificare il metodo di assegnazione di un indirizzo IP, modificare l'indirizzo IP statico o modificare l'indirizzo IP pubblico assegnato a una scheda di interfaccia di rete. Se si modifica l'indirizzo IP privato di una configurazione IP secondaria associata a una scheda di interfaccia di rete secondaria in una VM (ulteriori informazioni sulle [schede di interfaccia di rete principali e secondarie](virtual-network-network-interface-vm.md#about)), mettere la VM in stato di arresto (deallocata) prima di completare i passaggi seguenti: 

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** che viene visualizzato fare clic sull'interfaccia da visualizzare o per la quale modificare le impostazioni di indirizzo IP.
4. Nel pannello per la scheda di interfaccia di rete selezionata fare clic su **Configurazioni IP** nella sezione **IMPOSTAZIONI**.
5. Fare clic sulla configurazione IP da modificare nel pannello visualizzato per le configurazioni IP.
6. Modificare le impostazioni in base alle esigenze usando le informazioni sulle impostazioni descritte al passaggio 6 della sezione [Aggiungere una configurazione IP](#create-ip-config) di questo articolo. Fare clic su **Salva** per chiudere il pannello relativo alla configurazione IP modificata.

>[!NOTE]
>Se l'interfaccia di rete primaria ha più configurazioni IP e si modifica l'indirizzo IP privato della configurazione IP primaria, in Windows è necessario riassegnare manualmente tutti gli indirizzi IP secondari all'interfaccia di rete. Questa operazione non è necessaria in Linux. Per assegnare manualmente gli indirizzi IP a un'interfaccia di rete in un sistema operativo, vedere l'articolo [Assegnare più indirizzi IP alle macchine virtuali](virtual-network-multiple-ip-addresses-portal.md#os-config). Non aggiungere indirizzi IP pubblici al sistema operativo della VM.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-ip-config"></a>Rimuovere indirizzi IP

È possibile rimuovere indirizzi IP pubblici e privati da una scheda di interfaccia di rete, ma una scheda di interfaccia di rete deve sempre avere almeno un indirizzo IP privato assegnato.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** che viene visualizzato fare clic sulla scheda di interfaccia di rete da cui rimuovere gli indirizzi IP.
4. Nel pannello per la scheda di interfaccia di rete selezionata fare clic su **Configurazioni IP** nella sezione **IMPOSTAZIONI**.
5. Fare clic con il pulsante destro del mouse su una configurazione IP secondaria (non è possibile eliminare la configurazione principale) da eliminare, fare clic su **Elimina** quindi fare clic su **Sì** per confermare l'eliminazione. Se la configurazione aveva una risorsa di indirizzo IP pubblico associata, la risorsa verrà dissociata dalla configurazione IP, ma non eliminata.
6. Chiudere il pannello **Configurazioni IP**.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Passaggi successivi
Per creare una VM con più schede di interfaccia di rete o indirizzi IP, vedere gli articoli seguenti:

**Comandi**

|Attività|Strumento|
|---|---|
|Creare una macchina virtuale con più NIC|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creare una VM con una singola scheda di interfaccia di rete e più indirizzi IP|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

