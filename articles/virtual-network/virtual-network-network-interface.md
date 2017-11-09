---
title: Creare, modificare o eliminare le interfacce di rete di Azure | Microsoft Docs
description: Informazioni sulle interfacce di rete, come crearle, modificare le loro impostazioni ed eliminarle.
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
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 7dafb491cec908ffbb3683991919654f3d3eb452
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-change-or-delete-a-network-interface"></a>Creare, modificare o eliminare un'interfaccia di rete

Informazioni su come creare, modificare le impostazioni ed eliminare un'interfaccia di rete. Un'interfaccia di rete consente a una macchina virtuale di Azure di comunicare con Internet, Azure e le risorse locali. Quando si crea una macchina virtuale tramite il portale di Azure, il portale crea automaticamente un'interfaccia di rete con le impostazioni predefinite. È invece possibile scegliere di creare interfacce di rete con impostazioni personalizzate e aggiungere una o più interfacce di rete a una macchina virtuale durante il processo di creazione. È anche possibile modificare le impostazioni predefinite di un'interfaccia di rete esistente. Questo articolo spiega come creare un'interfaccia di rete con impostazioni personalizzate, modificare le impostazioni esistenti, ad esempio l'assegnazione del filtro di rete (gruppi di sicurezza di rete), l'assegnazione di subnet, le impostazioni di server DNS e l'inoltro IP, ed eliminare le interfaccia di rete.

Se si desidera aggiungere, modificare o rimuovere indirizzi IP da un'interfaccia di rete, leggere l'articolo [Gestire gli indirizzi IP](virtual-network-network-interface-addresses.md). Se è necessario aggiungere o rimuovere interfacce di rete nelle macchine virtuali, leggere l'articolo [Aggiungere o rimuovere interfacce di rete](virtual-network-network-interface-vm.md).


## <a name="before-you-begin"></a>Prima di iniziare

Prima di completare qualsiasi altro passaggio nelle altre sezioni di questo articolo, eseguire le operazioni seguenti:

- Per informazioni sui limiti delle interfacce di rete, vedere l'articolo sui [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Accedere al [portale](https://portal.azure.com) di Azure, all'interfaccia della riga di comando di Azure oppure ad Azure PowerShell con un account Azure. Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usano comandi di PowerShell per completare le attività di questo articolo, [installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dei cmdlet di Azure PowerShell. Per informazioni ed esempi relativi ai comandi di PowerShell, digitare `get-help <command> -full`.
- Se si usano comandi dell'interfaccia della riga di comando di Azure per completare le attività di questo articolo, [installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `az <command> --help`. Invece di installare l'interfaccia della riga di comando e i rispettivi prerequisiti, è possibile usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Per usare Cloud Shell, fare clic sul pulsante Cloud Shell **>_** nella parte superiore del [portale](https://portal.azure.com).

## <a name="create-a-network-interface"></a>Creare un'interfaccia di rete

Quando si crea una macchina virtuale tramite il portale di Azure, il portale crea automaticamente un'interfaccia di rete con le impostazioni predefinite. Se si preferisce specificare tutte le impostazioni dell'interfaccia di rete, è possibile creare un'interfaccia di rete con impostazioni personalizzate e collegare l'interfaccia di rete a una macchina virtuale quando si crea la macchina virtuale (tramite PowerShell o l'interfaccia della riga di comando di Azure). È anche possibile creare un'interfaccia di rete e aggiungerla a una macchina virtuale esistente (tramite PowerShell o l'interfaccia della riga di comando di Azure). Per informazioni su come creare una macchina virtuale con un'interfaccia di rete esistente o aggiungere o rimuovere le interfacce di rete da macchine virtuali esistenti, leggere l'articolo [Aggiungere o rimuovere interfacce di rete](virtual-network-network-interface-vm.md). Prima di creare un'interfaccia di rete è necessario avere una [rete virtuale](virtual-networks-create-vnet-arm-pportal.md) esistente nella stessa località e sottoscrizione in cui si crea l'interfaccia di rete.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** visualizzato fare clic su **+ Aggiungi**.
4. Immettere o selezionare i valori per le impostazioni seguenti nel pannello **Crea interfaccia di rete** che viene visualizzato, quindi fare clic su **Crea**:

    |Impostazione|Obbligatorio?|Dettagli|
    |---|---|---|
    |Nome|Sì|Il nome deve essere univoco all'interno del gruppo di risorse selezionato. Nel corso del tempo, probabilmente si accumuleranno più interfacce di rete nella sottoscrizione di Azure. Per suggerimenti sulla creazione di una convenzione di denominazione che faciliti la gestione di più interfacce di rete, leggere l'articolo [Convenzioni di denominazione](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Il nome non può essere modificato dopo la creazione dell'interfaccia di rete.|
    |Rete virtuale|Sì|Selezionare la rete virtuale per l'interfaccia di rete. È possibile assegnare solo un'interfaccia di rete a una rete virtuale presente nella stessa sottoscrizione e località dell'interfaccia di rete. Dopo aver creato un'interfaccia di rete, non è possibile modificare la rete virtuale a cui viene assegnata. La macchina virtuale che viene aggiunta all'interfaccia di rete deve esistere anche nella stessa località e sottoscrizione dell'interfaccia di rete.|
    |Subnet|Sì|Selezionare una subnet all'interno della rete virtuale selezionata. È possibile modificare la subnet a cui l'interfaccia di rete è assegnata dopo la creazione.|
    |Assegnazione di indirizzi IP privati|Sì| In questa impostazione, si è scelto il metodo di assegnazione dell'indirizzo IPv4. Scegliere uno dei seguenti metodi di assegnazione: **Dinamico:** quando si seleziona questa opzione, Azure assegna automaticamente un indirizzo disponibile dallo spazio di indirizzi della subnet selezionata. Azure può assegnare un indirizzo diverso a un'interfaccia di rete quando la macchina virtuale in cui si trova viene avviata dopo essere stata arrestata (deallocata). L'indirizzo rimane invariato se la macchina virtuale viene riavviata senza essere stata arrestata (deallocata). **Statico**: quando si seleziona questa opzione, è necessario assegnare manualmente un indirizzo IP disponibile dallo spazio di indirizzi della subnet selezionata. Gli indirizzi statici non cambiano fino a quando non vengono modificati o non si elimina l'interfaccia di rete. È possibile modificare il metodo di assegnazione dopo aver creato la l'interfaccia di rete. Il server DHCP di Azure assegna questo indirizzo all'interfaccia di rete all'interno del sistema operativo della macchina virtuale.|
    |Gruppo di sicurezza di rete|No| Lasciare impostato su **Nessuno**, selezionare un [gruppo di sicurezza di rete](virtual-networks-nsg.md) esistente o [creare un gruppo di sicurezza di rete](virtual-networks-create-nsg-arm-pportal.md). I gruppi di sicurezza di rete consentono di filtrare il traffico di rete in ingresso e in uscita da un'interfaccia. È possibile applicare uno o nessun gruppo di sicurezza di rete a un'interfaccia di rete. È inoltre possibile applicare uno o nessun gruppo di sicurezza di rete alla subnet a cui è assegnata l'interfaccia di rete. Quando un gruppo di sicurezza di rete viene applicato a un'interfaccia di rete e alla subnet a cui l'interfaccia di rete è assegnata, potrebbero verificarsi risultati imprevisti. Per risolvere i problemi dei gruppi di sicurezza di rete applicati alle interfacce di rete e alle subnet, leggere l'articolo [Risolvere i problemi relativi ai gruppi di sicurezza di rete](virtual-network-nsg-troubleshoot-portal.md#nsg).|
    |Subscription|Sì|Selezionare una delle [sottoscrizioni](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) di Azure. La macchina virtuale a cui viene collegata un'interfaccia di rete e la rete virtuale a cui viene connessa devono esistere nella stessa sottoscrizione.|
    |Indirizzo IP privato (IPv6)|No| Se si seleziona questa casella di controllo, viene assegnato un indirizzo IPv6 all'interfaccia di rete, oltre all'indirizzo IPv4 assegnato all'interfaccia di rete. Vedere la sezione [IPv6](#IPv6) di questo articolo per informazioni importanti sull'uso di IPv6 con le interfacce di rete. Non è possibile selezionare un metodo di assegnazione di un indirizzo IPv6. Se si sceglie di assegnare un indirizzo IPv6, questo viene assegnato con il metodo dinamico.
    |Nome IPv6 (viene visualizzata solo quando è selezionata la casella di controllo **Indirizzo IP privato (IPv6)**) |Sì, se la casella di controllo **Indirizzo IP privato (IPv6)** è selezionata.| Questo nome viene assegnato a una configurazione IP secondaria per l'interfaccia di rete. Ulteriori informazioni sulle configurazioni IP nella sezione [Visualizzare le impostazioni dell'interfaccia di rete](#view-network-interface-settings) di questo articolo.|
    |Gruppo di risorse|Sì|Selezionare un [gruppo di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) esistente o crearne uno. Un'interfaccia di rete può trovarsi nello stesso gruppo di risorse o in un gruppo diverso rispetto alla macchina virtuale alla quale è collegata o alla rete virtuale a cui è connessa.|
    |Percorso|Sì|La macchina virtuale a cui viene collegata un'interfaccia di rete e la rete virtuale a cui viene connessa devono esistere nella stessa [località](https://azure.microsoft.com/regions), denominata anche area.|

Il portale non prevede la possibilità di assegnare un indirizzo IP pubblico all'interfaccia di rete in fase di creazione, anche se il portale crea un indirizzo IP pubblico e lo assegna a un'interfaccia di rete quando si crea una macchina virtuale tramite il portale. Per informazioni su come aggiungere un indirizzo IP pubblico all'interfaccia di rete dopo averla creata, leggere l'articolo [Gestire gli indirizzi IP](virtual-network-network-interface-addresses.md). Se si desidera creare un'interfaccia di rete con un indirizzo IP pubblico, è necessario usare l'interfaccia della riga di comando o PowerShell per creare l'interfaccia di rete.

>[!Note]
> Azure assegna un indirizzo MAC all'interfaccia di rete solo dopo che quest'ultima viene collegata a una macchina virtuale e la macchina virtuale viene avviata per la prima volta. Non è possibile specificare l'indirizzo MAC che Azure assegna all'interfaccia di rete. L'indirizzo MAC rimarrà assegnato all'interfaccia di rete finché l'interfaccia non viene eliminata oppure non viene modificato l'indirizzo IP privato assegnato alla configurazione IP primaria dell'interfaccia di rete primaria. Per altre informazioni sugli indirizzi IP e le configurazioni IP, leggere l'articolo [Gestire gli indirizzi IP](virtual-network-network-interface-addresses.md).

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-network-interface-settings"></a>Visualizzare le impostazioni dell'interfaccia di rete

È possibile visualizzare e modificare la maggior parte delle impostazioni di un'interfaccia di rete dopo la sua creazione.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** visualizzato fare clic sull'interfaccia da visualizzare o per la quale modificare le impostazioni.
4. Nel pannello visualizzato per l'interfaccia selezionata sono elencate le impostazioni seguenti:
    - **Panoramica:** fornisce informazioni sull'interfaccia di rete, ad esempio gli indirizzi IP assegnati, la rete virtuale/subnet a cui l'interfaccia di rete è assegnata e la macchina virtuale a cui è collegata l'interfaccia di rete (se collegata). L'immagine seguente illustra la panoramica delle impostazioni per un'interfaccia di rete denominata **mywebserver256**: ![Panoramica dell'interfaccia di rete](./media/virtual-network-network-interface/nic-overview.png). È possibile spostare un'interfaccia di rete in un diverso gruppo di risorse o sottoscrizione facendo clic su (**modifica**) accanto a **Gruppo di risorse** o **Nome sottoscrizione**. Se si sposta l'interfaccia di rete, è necessario spostare tutte le risorse correlate all'interfaccia di rete. Se ad esempio l'interfaccia di rete viene collegata a una macchina virtuale, è necessario spostare anche la macchina virtuale e le altre risorse correlate alla macchina virtuale. Per spostare un'interfaccia di rete, vedere l'articolo [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). L'articolo elenca i prerequisiti e descrive come spostare le risorse tramite il portale di Azure, PowerShell e l'interfaccia della riga di comando di Azure.
    - **Configurazioni IP**: gli indirizzi IPv4 e IPv6 pubblici e privati vengono assegnati alle configurazioni IP sono elencati qui. Se un indirizzo IPv6 viene assegnato a una configurazione IP, l'indirizzo non viene visualizzato. Altre informazioni sulle configurazioni IP e su come aggiungere e rimuovere indirizzi IP sono consultabili nell'articolo [Configurare gli indirizzi IP per le interfacce di rete di Azure](virtual-network-network-interface-addresses.md). In questa sezione vengono configurati anche l'inoltro IP e l'assegnazione di subnet. Per altre informazioni su queste impostazioni, leggere le sezioni [Attivare e disattivare l'inoltro IP](#enable-or-disable-ip-forwarding) e [Cambiare l'assegnazione delle subnet](#change-subnet-assignment) di questo articolo.
    - **Server DNS:** è possibile specificare il server DNS al quale i server DHCP di Azure assegnano un'interfaccia di rete. L'interfaccia di rete può ereditare l'impostazione dalla rete virtuale a cui è assegnata l'interfaccia di rete, oppure dispone di un'impostazione personalizzata che sostituisce l'impostazione per la rete virtuale a cui è assegnata. Per modificare l'impostazione visualizzata, seguire la procedura descritta nella sezione [Modificare i server DNS](#change-dns-servers) di questo articolo.
    - **Gruppo di sicurezza di rete**: mostra il gruppo di sicurezza di rete (se esiste) associato all'interfaccia di rete. Un gruppo di sicurezza di rete contiene regole per filtrare il traffico di rete in entrata e in uscita per l'interfaccia di rete. Se all'interfaccia di rete è associato un gruppo di sicurezza di rete, viene visualizzato il nome del gruppo associato. Per modificare i dati visualizzati, seguire i passaggi riportati nell'articolo [Gestire le associazioni dei gruppi di sicurezza di rete](virtual-network-manage-nsg-arm-portal.md#manage-associations).
    - **Proprietà**: visualizza le impostazioni chiave relative all'interfaccia di rete, incluso il suo indirizzo MAC (vuoto se l'interfaccia di rete è collegata a una macchina virtuale) e la sottoscrizione in cui si trova.
    - **Regole di sicurezza effettive:** le regole di sicurezza sono elencate se l'interfaccia di rete è collegata a una macchina virtuale in esecuzione e un gruppo di sicurezza di rete è associato all'interfaccia, alla subnet cui l'interfaccia è assegnata o a entrambe. Per altre informazioni sulle impostazioni visualizzate, vedere l'articolo [Risolvere i problemi relativi ai gruppi di sicurezza di rete](virtual-network-nsg-troubleshoot-portal.md#nsg). Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo [Gruppi di sicurezza di rete](virtual-networks-nsg.md).
    - **Route valide:** le route vengono elencate se l'interfaccia di rete è collegata a una macchina virtuale in esecuzione. Le route sono una combinazione di route predefinite di Azure, route definite dall'utente e route BGP esistenti per la subnet a cui è assegnata l'interfaccia di rete. Per altre informazioni sulle impostazioni visualizzate, vedere l'articolo [Risolvere i problemi relativi alle route](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface). Per altre informazioni sulle impostazioni predefinite di Azure e le route definite dall'utente, vedere l'articolo [Route definite dall'utente](virtual-networks-udr-overview.md).
    - **Impostazioni comuni di Azure Resource Manager:** per altre informazioni sulle impostazioni comuni di Azure Resource Manager, vedere gli articoli [Log attività](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Controllo di accesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Tag](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Blocchi](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Script di automazione](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

**Comandi**

Se un indirizzo IPv6 viene assegnato a un'interfaccia di rete, l'output di PowerShell restituisce il fatto che l'indirizzo è assegnato, ma non restituisce l'indirizzo assegnato. Analogamente, l'interfaccia della riga di comando restituisce il fatto che l'indirizzo è assegnato, ma restituisce *null* nel relativo output per l'indirizzo.

|Strumento|Comando|
|---|---|
|CLI|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) per visualizzare le interfacce di rete nella sottoscrizione; [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) per visualizzare le impostazioni di un'interfaccia di rete|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) per visualizzare le interfacce di rete nella sottoscrizione o le impostazioni di un'interfaccia di rete|

## <a name="change-dns-servers"></a>Modificare i server DNS

Il server DNS viene assegnato dal server DHCP di Azure all'interfaccia di rete all'interno del sistema operativo della macchina virtuale. Il server DNS assegnato corrisponde all'impostazione relativa al server DNS per un'interfaccia di rete. Per altre informazioni sulle impostazioni di risoluzione dei nomi per le interfacce di rete, vedere [Risoluzione dei nomi per le macchine virtuali](virtual-networks-name-resolution-for-vms-and-role-instances.md). L'interfaccia di rete può ereditare le impostazioni dalla rete virtuale oppure usare proprie impostazioni univoche che sostituiscono l'impostazione della rete virtuale.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** visualizzato fare clic sull'interfaccia da visualizzare o per la quale modificare le impostazioni.
4. Nel pannello relativo all'interfaccia di rete selezionata fare clic su **Server DNS** in **IMPOSTAZIONI**.
5. Selezionare una delle opzioni seguenti:
    - **Eredita da rete virtuale (impostazione predefinita)**: scegliere questa opzione per ereditare l'impostazione del server DNS definita per la rete virtuale alla quale è assegnata l'interfaccia di rete. A livello di rete virtuale viene definito un server DNS personalizzato o il server DNS fornito da Azure. Il server DNS fornito da Azure può risolvere i nomi host per le risorse assegnate alla stessa rete virtuale. Il FQDN deve essere usato per la risoluzione delle risorse assegnate a reti virtuali diverse.
    - **Personalizzata**: è possibile configurare il server DNS per risolvere i nomi tra più reti virtuali. Immettere l'indirizzo IP del server da usare come server DNS. L'indirizzo del server DNS specificato viene assegnato solo a questa interfaccia di rete e sostituisce tutte le impostazioni DNS per la rete virtuale a cui è assegnata l'interfaccia di rete.
6. Fare clic su **Salva**.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="enable-or-disable-ip-forwarding"></a>Abilitare o disabilitare l'inoltro IP

L'inoltro IP abilita la macchina virtuale a cui è collegata un'interfaccia di rete:
- Ricevere il traffico di rete non destinato a uno degli indirizzi IP assegnati a una delle configurazioni IP associate all'interfaccia di rete.
- Inviare il traffico di rete con un indirizzo IP di origine diverso da quello assegnato a una delle configurazioni IP dell'interfaccia di rete.

L'impostazione deve essere abilitata per ogni interfaccia di rete collegata alla macchina virtuale che riceve il traffico che la macchina virtuale deve inoltrare. Una macchina virtuale può inoltrare il traffico se dispone di più interfacce di rete o una singola interfaccia di rete collegata. Anche se l'inoltro IP è un'impostazione di Azure, la macchina virtuale deve anche eseguire un'applicazione che possa inoltrare il traffico, ad esempio applicazioni firewall, di ottimizzazione WAN e di bilanciamento del carico. Una macchina virtuale che esegue applicazioni di rete è spesso definita "appliance virtuale di rete". È possibile visualizzare un elenco di appliance virtuali di rete pronte per la distribuzione in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). L'inoltro IP viene in genere usato con le route definite dall'utente. Per altre informazioni sulle route definite dall'utente, vedere l'articolo [Route definite dall'utente](virtual-networks-udr-overview.md).

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** che viene visualizzato fare clic sull'interfaccia di rete per la quale abilitare o disabilitare l'inoltro IP.
4. Nel pannello dell'interfaccia di rete selezionata fare clic su **Configurazioni IP** nella sezione **IMPOSTAZIONI**.
5. Fare clic su **Abilitato** o **Disabilitato** (impostazione predefinita) per modificare l'impostazione.
6. Fare clic su **Salva**.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet-assignment"></a>Cambiare l'assegnazione delle subnet

È possibile modificare la subnet, ma non la rete virtuale, a cui viene assegnata un'interfaccia di rete.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** visualizzato fare clic sull'interfaccia da visualizzare o per la quale modificare le impostazioni.
4. Nel pannello relativo all'interfaccia di rete selezionata fare clic su **Configurazioni IP** in **IMPOSTAZIONI**. Se sono presenti indirizzi IP privati per le configurazioni IP elencate che mostrano l'indicazione **(Statico)** a fianco, è necessario modificare il metodo in dinamico seguendo questa procedura. Tutti gli indirizzi IP privati devono essere assegnati con il metodo di assegnazione dinamico per poter modificare l'assegnazione della subnet per l'interfaccia di rete. Se gli indirizzi sono assegnati con il metodo dinamico, procedere al passaggio 5. Se ci sono indirizzi IPv4 assegnati con il metodo statico, completare i passaggi seguenti per modificare il metodo di assegnazione in dinamico:
    - Nell'elenco delle configurazioni IP fare clic sulla configurazione IP di cui cambiare il metodo di assegnazione degli indirizzi IPv4.
    - Nel pannello visualizzato per la configurazione IP fare clic su **Dinamico** per il metodo di **Assegnazione**. Non è possibile assegnare un indirizzo IPv6 con il metodo di assegnazione statico.
    - Fare clic su **Salva**.
5. Selezionare la subnet alla quale connettere l'interfaccia di rete nell'elenco a discesa **Subnet**.
6. Fare clic su **Salva**. I nuovi indirizzi dinamici vengono assegnati dall'intervallo di indirizzi della nuova subnet. Dopo aver assegnato l'interfaccia di rete a una nuova subnet, è possibile assegnare un indirizzo IPv4 statico dall'intervallo di indirizzi della nuova subnet. Per altre informazioni su come aggiungere, modificare e rimuovere indirizzi IP per una scheda di interfaccia di rete, leggere l'articolo [Gestire gli indirizzi IP](virtual-network-network-interface-addresses.md).

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-a-network-interface"></a>Eliminare un'interfaccia di rete

È possibile eliminare un'interfaccia purché non sia collegata a una macchina virtuale. Se è collegata a una macchina virtuale, è innanzitutto necessario arrestare la macchina virtuale (deallocarla) e quindi scollegare l'interfaccia di rete dalla macchina virtuale, prima di poter eliminare l'interfaccia di rete. Per scollegare un'interfaccia di rete da una macchina virtuale, seguire la procedura descritta nella sezione [Scollegare un'interfaccia di rete da una macchina virtuale](virtual-network-network-interface-vm.md#vm-remove-nic) dell'articolo [Aggiungere o rimuovere interfacce di rete](virtual-network-network-interface-vm.md). Eliminando una macchina virtuale vengono scollegate tutte le interfacce di rete a essa collegate, ma non eliminate.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Fare clic con il pulsante destro del mouse sull'interfaccia di rete da eliminare, quindi scegliere **Elimina**.
4. Fare clic su **Sì** per confermare l'eliminazione dell'interfaccia di rete.

Quando si elimina un'interfaccia di rete vengono rilasciati tutti gli indirizzi MAC o IP assegnati.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Passaggi successivi
Per creare una macchina virtuale con più interfacce di rete o indirizzi IP, vedere gli articoli seguenti:

**Comandi**

|Attività|Strumento|
|---|---|
|Creare una macchina virtuale con più NIC|[Interfaccia della riga di comando](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creare una macchina virtuale con una singola scheda di interfaccia di rete e più indirizzi IPv4|[Interfaccia della riga di comando](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Creare una macchina virtuale con una singola scheda di interfaccia di rete con un indirizzo IPv6 privato (dietro un Azure Load Balancer)|[Interfaccia della riga di comando](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Modello di Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
