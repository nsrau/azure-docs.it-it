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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 871d469ada9857af6d6fe70f1dd38385fb18e312
ms.contentlocale: it-it
ms.lasthandoff: 06/01/2017


---

# <a name="create-change-or-delete-network-interfaces"></a>Creare, modificare o eliminare le interfacce di rete

Informazioni su come creare, modificare le impostazioni ed eliminare le interfacce di rete. Una scheda di interfaccia di rete consente a una macchina virtuale (VM) di Azure di comunicare con Internet, Azure e le risorse locali. Quando si crea una VM tramite il portale di Azure, il portale crea automaticamente una scheda di interfaccia di rete con le impostazioni predefinite. In alternativa è possibile scegliere di creare schede di interfaccia di rete con impostazioni personalizzate e aggiungerne una o più alle VM al momento della creazione. È inoltre consigliabile modificare le impostazioni delle schede di interfaccia di rete predefinite per le schede di rete esistenti. Questo articolo spiega come creare schede di interfaccia di rete con impostazioni personalizzate, modificare le impostazioni delle schede di interfaccia di rete esistenti, ad esempio l'assegnazione del filtro di rete (gruppi di sicurezza di rete), l'assegnazione di subnet, le impostazioni di server DNS e l'inoltro IP, ed eliminare le schede di interfaccia di rete.

Se si desidera aggiungere, modificare o rimuovere indirizzi IP da una scheda di interfaccia di rete, leggere l'articolo [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md). Se si desidera aggiungere o rimuovere schede di interfaccia di rete da una VM, leggere l'articolo su come [aggiungere o rimuovere le schede di interfaccia di rete](virtual-network-network-interface-vm.md).


## <a name="before"></a>Prima di iniziare

Prima di completare qualsiasi altro passaggio nelle altre sezioni di questo articolo, eseguire le operazioni seguenti:

- Per informazioni sui limiti delle schede di interfaccia di rete, vedere l'articolo sui [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Accedere al portale di Azure, all'interfaccia della riga di comando di Azure oppure ad Azure PowerShell con un account Azure. Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si sceglie di usare i comandi di PowerShell per completare le attività indicate in questo articolo, installare e configurare Azure PowerShell seguendo i passaggi riportati nell'articolo [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dei cmdlet di Azure PowerShell. Per informazioni ed esempi relativi ai comandi di PowerShell, digitare `get-help <command> -full`.
- Se si sceglie di usare i comandi dell'interfaccia della riga di comando di Azure per completare le attività indicate in questo articolo, installare e configurare l'interfaccia della riga di comando di Azure seguendo i passaggi riportati nell'articolo [Come installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assicurarsi che sia installata la versione più recente dell'interfaccia della riga di comando di Azure. Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `az <command> --help`.

## <a name="create-nic"></a>Creare una scheda di interfaccia di rete
Quando si crea una VM tramite il portale di Azure, il portale crea automaticamente una scheda di interfaccia di rete con le impostazioni predefinite. Se si preferisce specificare tutte le impostazioni per la scheda di interfaccia di rete, è possibile creare una scheda di interfaccia di rete con impostazioni personalizzate e collegarla a una VM durante la creazione della VM. È possibile anche creare una scheda di interfaccia di rete e aggiungerla a una VM esistente. Per informazioni su come creare una VM con una scheda di interfaccia di rete esistente o aggiungere o rimuovere schede di interfaccia di rete da VM esistenti, leggere l'articolo su come [aggiungere o rimuovere le schede di interfaccia di rete](virtual-network-network-interface-vm.md). Prima di creare una scheda di interfaccia di rete è necessario avere una rete virtuale esistente nello stesso percorso e sottoscrizione in cui si crea la scheda di interfaccia di rete. Per informazioni su come creare una rete virtuale, leggere l'articolo [Creare una rete virtuale](virtual-networks-create-vnet-arm-pportal.md).

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** visualizzato fare clic su **+ Aggiungi**.
4. Immettere o selezionare i valori per le impostazioni seguenti nel pannello **Crea interfaccia di rete** che viene visualizzato, quindi fare clic su **Crea**:

    |Impostazione|Obbligatorio?|Dettagli|
    |---|---|---|
    |Nome|Sì|Il nome deve essere univoco all'interno del gruppo di risorse selezionato. Nel corso del tempo probabilmente si accumuleranno più schede di interfaccia di rete nella sottoscrizione di Azure. Per suggerimenti sulla creazione di una convenzione di denominazione che faciliti la gestione di più schede di interfaccia di rete, leggere l'articolo [Convenzioni di denominazione](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Il nome non può essere modificato dopo la creazione dell'interfaccia di rete.|
    |Rete virtuale|Sì|Selezionare una rete virtuale a cui connettere la scheda di interfaccia di rete. L'interfaccia di rete può essere connessa solo a una rete virtuale che si trova nella stessa posizione e sottoscrizione dell'interfaccia di rete stessa. Dopo aver creato un'interfaccia di rete non è possibile modificare la rete virtuale a cui è connessa. La VM a cui si aggiunge la scheda di interfaccia di rete deve trovarsi nella stessa posizione e nella stessa sottoscrizione della scheda di interfaccia di rete.|
    |Subnet|Sì|Selezionare una subnet all'interno della rete virtuale selezionata. È possibile modificare la subnet a cui l'interfaccia di rete è connessa dopo la creazione.|
    |Assegnazione di indirizzi IP privati|Sì| Scegliere uno dei seguenti metodi di assegnazione: **Dinamico:** quando si seleziona questa opzione, Azure assegna automaticamente un indirizzo disponibile dallo spazio di indirizzi della subnet selezionata. Azure può assegnare un indirizzo diverso a una scheda di interfaccia di rete quando la VM in cui si trova viene avviata dopo essere stata arrestata (deallocata). L'indirizzo rimane invariato se la macchina virtuale viene riavviata senza essere stata arrestata (deallocata). **Statico**: quando si seleziona questa opzione, è necessario assegnare manualmente un indirizzo IP disponibile dallo spazio di indirizzi della subnet selezionata. Gli indirizzi statici non cambiano fino a quando non vengono modificati o non si elimina l'interfaccia di rete. È possibile modificare il metodo di assegnazione dopo aver creato la l'interfaccia di rete. Il server DHCP di Azure assegna questo indirizzo alla scheda di interfaccia di rete all'interno del sistema operativo della VM.|
    |Gruppo di sicurezza di rete|No| Lasciare impostato su **Nessuno**, selezionare un gruppo di sicurezza di rete esistente o creare un gruppo di sicurezza di rete. I gruppi di sicurezza di rete consentono di filtrare il traffico di rete da e verso una scheda di interfaccia di rete. Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo [Gruppi di sicurezza di rete](virtual-networks-nsg.md). Per creare un gruppo di sicurezza di rete, leggere l'articolo su come [creare un gruppo di sicurezza di rete](virtual-networks-create-nsg-arm-pportal.md). A una scheda di interfaccia di rete è possibile applicare uno o nessun gruppo di sicurezza di rete. È possibile applicare uno o nessun gruppo di sicurezza di rete anche alla subnet cui è connessa l'interfaccia di rete. Quando un gruppo di sicurezza di rete viene applicato a una scheda di interfaccia di rete e alla subnet a cui è connessa, a volte si verificano risultati imprevisti. Per risolvere i problemi dei gruppi di sicurezza di rete applicati alle schede di interfaccia di rete e alle subnet, vedere l'articolo [Risolvere i problemi relativi ai gruppi di sicurezza di rete](virtual-network-nsg-troubleshoot-portal.md#a-namensgaview-effective-security-rules-for-a-network-security-group-nsg).|
    |Sottoscrizione|Sì|Selezionare una delle [sottoscrizioni](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) di Azure. La VM a cui viene collegata un'interfaccia di rete e la rete virtuale a cui viene connessa devono trovarsi nella stessa sottoscrizione.|
    |Gruppo di risorse|Sì|Selezionare un [gruppo di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) esistente o crearne uno. Una scheda di interfaccia di rete può trovarsi nello stesso gruppo di risorse o in un gruppo diverso rispetto alla VM alla quale è collegata o alla rete virtuale a cui è connessa.|
    |Località|Sì|La VM a cui viene collegata un'interfaccia di rete e la rete virtuale a cui viene connessa devono trovarsi nella stessa [posizione](https://azure.microsoft.com/regions), anche detta area.|

Il portale non offre questa opzione di assegnare un indirizzo IP pubblico alla scheda di interfaccia di rete quando la si crea, sebbene assegni un indirizzo IP pubblico a una scheda di interfaccia di rete quando si crea una VM tramite il portale. Per informazioni su come aggiungere un indirizzo IP pubblico alla scheda di interfaccia rete dopo averla creata, leggere l'articolo [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md). Se si desidera creare una scheda di interfaccia di rete con un indirizzo IP pubblico, è necessario usare l'interfaccia della riga di comando o PowerShell per creare la scheda di interfaccia di rete.

>[!Note]
> Azure assegna un indirizzo MAC all'interfaccia di rete solo dopo aver collegato l'interfaccia di rete a una macchina virtuale e aver avviato la VM per la prima volta. Non è possibile specificare l'indirizzo MAC che Azure assegna all'interfaccia di rete. L'indirizzo MAC rimarrà assegnato all'interfaccia di rete finché l'interfaccia non viene eliminata oppure non viene modificato l'indirizzo IP privato assegnato alla configurazione IP primaria dell'interfaccia di rete primaria. Per altre informazioni sugli indirizzi IP e le configurazioni IP, leggere l'articolo [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md).

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-nics"></a>Visualizzare le impostazioni della scheda di interfaccia di rete

È possibile visualizzare e modificare la maggior parte delle impostazioni per una scheda di interfaccia di rete.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** visualizzato fare clic sull'interfaccia da visualizzare o per la quale modificare le impostazioni.
4. Nel pannello visualizzato per l'interfaccia selezionata sono elencate le impostazioni seguenti:
    - **Panoramica**: fornisce informazioni sulla scheda di interfaccia di rete, ad esempio gli indirizzi IP assegnati, la rete virtuale/subnet a cui è connessa la scheda di interfaccia di rete e la VM a cui è collegata l'interfaccia, se è collegata a una VM. L'immagine seguente illustra la panoramica delle impostazioni per una scheda di interfaccia di rete denominata **mywebserver256**:   ![Panoramica dell'interfaccia di rete](./media/virtual-network-network-interface/nic-overview.png). È possibile spostare una scheda di interfaccia di rete in un diverso gruppo di risorse o sottoscrizione facendo clic su (**modifica**) accanto a **Gruppo di risorse** o **Nome sottoscrizione**. Se si sposta la scheda di interfaccia di rete, è necessario spostare insieme a essa tutte le sue risorse correlate. Se la scheda di interfaccia di rete è collegata a una VM, ad esempio, è necessario spostare anche la VM e le altre risorse correlate. Per spostare un'interfaccia di rete, vedere l'articolo [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal), L'articolo elenca i prerequisiti e descrive come spostare le risorse tramite il portale di Azure, PowerShell e l'interfaccia della riga di comando di Azure.
    - **Configurazioni IP**: indirizzi IP pubblici e privati vengono assegnati a una o più configurazioni IP per una scheda di interfaccia di rete. Per informazioni sul numero massimo di configurazioni IP supportate per una scheda di interfaccia di rete, vedere l'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Ogni configurazione IP ha un indirizzo IP privato e può avere un indirizzo IP pubblico. Per aggiungere, modificare o eliminare configurazioni IP da una scheda di interfaccia di rete, seguire la procedura illustrata nelle sezioni [Aggiungere una configurazione IP secondaria a un'interfaccia di rete](virtual-network-network-interface-addresses.md#create-ip-config), [Modificare una configurazione IP](virtual-network-network-interface-addresses.md#change-ip-config) o [Eliminare una configurazione IP](virtual-network-network-interface-addresses.md#delete-ip-config) dell'articolo [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md). In questa sezione vengono configurati anche l'inoltro IP e l'assegnazione di subnet. Per altre informazioni su queste impostazioni, leggere le sezioni [Attivare e disattivare l'inoltro IP](#ip-forwarding) e [Cambiare l'assegnazione delle subnet](#subnet) di questo articolo.
    - **Server DNS:** è possibile specificare il server DNS al quale i server DHCP di Azure assegnano un'interfaccia di rete. La scheda di interfaccia di rete può ereditare l'impostazione relativa alla rete virtuale a cui è connessa o avere un'impostazione personalizzata che la sostituisce. Per modificare l'impostazione visualizzata, seguire la procedura descritta nella sezione [Modificare i server DNS](#dns) di questo articolo.
    - **Gruppo di sicurezza di rete**: mostra il gruppo di sicurezza di rete (se esiste) associato alla scheda di interfaccia di rete. Un gruppo di sicurezza di rete contiene regole per filtrare il traffico di rete in entrata e in uscita per la scheda di interfaccia di rete. Se all'interfaccia di rete è associato un gruppo di sicurezza di rete, viene visualizzato il nome del gruppo associato. Per modificare l'impostazione visualizzata, seguire la procedura descritta nella sezione [Associare o dissociare un gruppo di sicurezza di rete per un'interfaccia di rete](#associate-nsg) di questo articolo.
    - **Proprietà**: visualizza le impostazioni chiave relative alla scheda di interfaccia di rete, incluso il suo indirizzo MAC (vuoto se la scheda di interfaccia di rete è collegata a una VM) e la sottoscrizione in cui si trova.
    - **Regole di sicurezza effettive:** le regole di sicurezza sono elencate se l'interfaccia di rete è collegata a una macchina virtuale in esecuzione e un gruppo di sicurezza di rete è associato all'interfaccia, alla subnet cui l'interfaccia è connessa o a entrambe. Per altre informazioni sulle impostazioni visualizzate, vedere l'articolo [Risolvere i problemi relativi ai gruppi di sicurezza di rete](virtual-network-nsg-troubleshoot-portal.md#a-namensgaview-effective-security-rules-for-a-network-security-group-nsg). Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo [Gruppi di sicurezza di rete](virtual-networks-nsg.md).
    - **Route valide:** le route vengono elencate se l'interfaccia di rete è collegata a una VM in esecuzione. Le route sono una combinazione di route predefinite di Azure, route definite dall'utente e route BGP esistenti per la subnet a cui è connessa l'interfaccia di rete. Per altre informazioni sulle impostazioni visualizzate, vedere l'articolo [Risolvere i problemi relativi alle route](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface). Per altre informazioni sulle impostazioni predefinite di Azure e le route definite dall'utente, vedere l'articolo [Route definite dall'utente](virtual-networks-udr-overview.md).
    - **Impostazioni comuni di Azure Resource Manager:** per altre informazioni sulle impostazioni comuni di Azure Resource Manager, vedere gli articoli [Log attività](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Controllo di accesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Tag](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Blocchi](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Script di automazione](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) per visualizzare le interfacce di rete nella sottoscrizione; [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) per visualizzare le impostazioni di un'interfaccia di rete|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) per visualizzare le interfacce di rete nella sottoscrizione o le impostazioni di un'interfaccia di rete|

## <a name="dns"></a>Modificare i server DNS

Il server DNS viene assegnato dal server DHCP di Azure alla scheda di interfaccia di rete all'interno del sistema operativo della VM. Il server DNS assegnato corrisponde all'impostazione relativa al server DNS per una scheda di interfaccia di rete. Per altre informazioni sulle impostazioni di risoluzione dei nomi per le interfacce di rete, vedere l'articolo [Risoluzione dei nomi per le macchine virtuali](virtual-networks-name-resolution-for-vms-and-role-instances.md). La scheda di interfaccia di rete può ereditare le impostazioni dalla rete virtuale oppure usare proprie impostazioni univoche che sostituiscono l'impostazione della rete virtuale.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** visualizzato fare clic sull'interfaccia da visualizzare o per la quale modificare le impostazioni.
4. Nel pannello relativo alla scheda di interfaccia di rete selezionata fare clic su **Server DNS** sotto **IMPOSTAZIONI**.
5. Selezionare una delle opzioni seguenti:
    - **Eredita da rete virtuale (impostazione predefinita)**: scegliere questa opzione per ereditare l'impostazione del server DNS definita per la rete virtuale alla quale è connessa l'interfaccia di rete. A livello di rete virtuale viene definito un server DNS personalizzato o il server DNS fornito da Azure. Il server DNS fornito da Azure può risolvere i nomi host per le risorse collegate alla stessa rete virtuale. Il FQDN deve essere usato per la risoluzione per le risorse connesse a reti virtuali diverse.
    - **Personalizzata**: è possibile configurare il server DNS per risolvere i nomi tra più reti virtuali. Immettere l'indirizzo IP del server da usare come server DNS. L'indirizzo del server DNS specificato viene assegnato solo a questa interfaccia di rete e sostituisce qualsiasi impostazione DNS per la rete virtuale alla quale è connessa l'interfaccia di rete.
6. Fare clic su **Salva**.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-forwarding"></a>Attivare e disattivare l'inoltro IP

L'inoltro IP consente alla macchina virtuale cui è collegata un'interfaccia di rete di eseguire queste operazioni:
- Ricevere il traffico di rete non destinato a uno degli indirizzi IP assegnati a una delle configurazioni IP associate all'interfaccia di rete.
- Inviare il traffico di rete con un indirizzo IP di origine diverso da quello assegnato a una delle configurazioni IP della scheda di interfaccia di rete.

L'impostazione deve essere abilitata per ogni interfaccia di rete collegata alla VM che riceve il traffico che la VM deve inoltrare. Una VM può inoltrare il traffico avendo sia una che più interfacce di rete collegate. Anche se l'inoltro IP è un'impostazione di Azure, la VM deve anche eseguire un'applicazione che possa inoltrare il traffico, ad esempio applicazioni firewall, di ottimizzazione WAN e di bilanciamento del carico. Una VM che esegue applicazioni di rete è spesso definita "appliance virtuale di rete". È possibile visualizzare un elenco di appliance virtuali di rete pronte per la distribuzione in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). L'inoltro IP viene in genere usato con le route definite dall'utente. Per altre informazioni sulle route definite dall'utente, vedere l'articolo [Route definite dall'utente](virtual-networks-udr-overview.md).

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** che viene visualizzato fare clic sulla scheda di interfaccia di rete per la quale attivare o disattivare l'inoltro IP.
4. Nel pannello per la scheda di interfaccia di rete selezionata fare clic su **Configurazioni IP** nella sezione **IMPOSTAZIONI**.
5. Fare clic su **Abilitato** o **Disabilitato** (impostazione predefinita) per modificare l'impostazione.
6. Fare clic su **Salva**.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="subnet"></a>Cambiare l'assegnazione delle subnet

È possibile modificare la subnet, ma non la rete virtuale a cui l'interfaccia di rete è connessa.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** visualizzato fare clic sull'interfaccia da visualizzare o per la quale modificare le impostazioni.
4. Nel pannello relativo all'interfaccia di rete selezionata fare clic su **Configurazioni IP** sotto **IMPOSTAZIONI**. Se sono presenti indirizzi IP privati per le configurazioni IP elencate che mostrano l'indicazione **(Statico)** a fianco, è necessario modificare il metodo in dinamico seguendo questa procedura. Tutti gli indirizzi IP privati devono essere assegnati con il metodo di assegnazione dinamico per poter modificare l'assegnazione della subnet per la scheda NIC. Se gli indirizzi sono assegnati con il metodo dinamico, procedere al passaggio 5. Se ci sono indirizzi assegnati con il metodo statico, completare i passaggi seguenti per modificare il metodo di assegnazione in dinamico:
    - Nell'elenco delle configurazioni IP fare clic sulla configurazione IP di cui cambiare il metodo di assegnazione degli indirizzi IP.
    - Nel pannello visualizzato per la configurazione IP fare clic su **Dinamico** per il metodo di **Assegnazione**.
    - Fare clic su **Salva**.
5. Selezionare la subnet alla quale connettere l'interfaccia di rete nell'elenco a discesa **Subnet**.
6. Fare clic su **Salva**. I nuovi indirizzi dinamici vengono assegnati dall'intervallo di indirizzi della nuova subnet. Dopo aver assegnato la scheda di interfaccia di rete a una nuova subnet, è possibile assegnare un indirizzo IP statico dall'intervallo di indirizzi della nuova subnet. Per altre informazioni su come aggiungere, modificare e rimuovere indirizzi IP per una scheda di interfaccia di rete, leggere l'articolo [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md).

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-nic"></a>Eliminare una scheda di interfaccia di rete

È possibile eliminare un'interfaccia di rete se non è collegata a una VM. Se è collegata a una VM, prima di poter eliminare la scheda è necessario innanzitutto mettere la VM in stato di arresto (deallocata), quindi scollegare la scheda di interfaccia di rete dalla VM. Per scollegare una scheda di interfaccia di rete da una VM, seguire la procedura descritta nella sezione [Scollegare un'interfaccia di rete da una macchina virtuale esistente](virtual-network-network-interface-vm.md#vm-remove-nic) dell'articolo [Aggiungere o rimuovere interfacce di rete](virtual-network-network-interface-vm.md). Se si elimina una VM, tutte le schede di interfaccia di rete collegate vengono scollegate ma non vengono eliminate.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Fare clic con il pulsante destro del mouse sull'interfaccia di rete da eliminare, quindi scegliere **Elimina**.
4. Fare clic su **Sì** per confermare l'eliminazione dell'interfaccia di rete.

Quando si elimina un'interfaccia di rete vengono rilasciati tutti gli indirizzi MAC o IP assegnati.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Passaggi successivi
Per creare una VM con più schede di interfaccia di rete o indirizzi IP, vedere gli articoli seguenti:

**Comandi**

|Attività|Strumento|
|---|---|
|Creare una macchina virtuale con più NIC|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creare una VM con una singola scheda di interfaccia di rete e più indirizzi IP|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

