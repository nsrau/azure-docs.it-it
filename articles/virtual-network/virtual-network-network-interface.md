---
title: Creare, modificare o eliminare le interfacce di rete di Azure | Microsoft Docs
description: Informazioni sulle interfacce di rete, come crearle, modificare le loro impostazioni ed eliminarle.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: ffbd36beda179afc4bee6fb2d102215ac8f5f5c2
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539416"
---
# <a name="create-change-or-delete-a-network-interface"></a>Creare, modificare o eliminare un'interfaccia di rete

Informazioni su come creare, modificare le impostazioni ed eliminare un'interfaccia di rete. Un'interfaccia di rete consente a una macchina virtuale di Azure di comunicare con Internet, Azure e le risorse locali. Quando si crea una macchina virtuale tramite il portale di Azure, il portale crea automaticamente un'interfaccia di rete con le impostazioni predefinite. È invece possibile scegliere di creare interfacce di rete con impostazioni personalizzate e aggiungere una o più interfacce di rete a una macchina virtuale durante il processo di creazione. È anche possibile modificare le impostazioni predefinite di un'interfaccia di rete esistente. Questo articolo spiega come creare un'interfaccia di rete con impostazioni personalizzate, modificare le impostazioni esistenti, ad esempio l'assegnazione del filtro di rete (gruppi di sicurezza di rete), l'assegnazione di subnet, le impostazioni di server DNS e l'inoltro IP, ed eliminare le interfaccia di rete.

Se è necessario aggiungere, modificare o rimuovere indirizzi IP per un'interfaccia di rete, vedere [Gestire gli indirizzi IP](virtual-network-network-interface-addresses.md). Se è necessario aggiungere o rimuovere interfacce di rete nelle macchine virtuali, vedere [Aggiungere o rimuovere interfacce di rete](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Prima di iniziare

Prima di completare i passaggi di qualsiasi sezione di questo articolo, eseguire le attività seguenti:

- Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usa il portale, aprire https://portal.azure.com e accedere con l'account Azure.
- Se si usano i comandi di PowerShell per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/powershell) o tramite PowerShell dal computer in uso. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Questa esercitazione richiede il modulo di Azure PowerShell 5.4.1 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.
- Se si usano i comandi dell'interfaccia della riga di comando di Azure per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/bash) o tramite l'interfaccia della riga di comando dal computer in uso. Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.28 o versioni successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

L'account con cui si accede o con cui ci si collega ad Azure deve essere assegnato al ruolo [collaboratore di rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le operazioni appropriate elencate nelle [Autorizzazioni](#permissions).

## <a name="create-a-network-interface"></a>Creare un'interfaccia di rete

Quando si crea una macchina virtuale tramite il portale di Azure, il portale crea automaticamente un'interfaccia di rete con le impostazioni predefinite. Se si preferisce specificare tutte le impostazioni dell'interfaccia di rete, è possibile creare un'interfaccia di rete con impostazioni personalizzate e collegare l'interfaccia di rete a una macchina virtuale quando si crea la macchina virtuale (tramite PowerShell o l'interfaccia della riga di comando di Azure). È anche possibile creare un'interfaccia di rete e aggiungerla a una macchina virtuale esistente (tramite PowerShell o l'interfaccia della riga di comando di Azure). Per informazioni su come creare una macchina virtuale con un'interfaccia di rete esistente o aggiungere o rimuovere le interfacce di rete da macchine virtuali esistenti, vedere [Aggiungere o rimuovere interfacce di rete](virtual-network-network-interface-vm.md). Prima di creare un'interfaccia di rete è necessario avere una [rete virtuale](manage-virtual-network.md#create-a-virtual-network) esistente nella stessa località e sottoscrizione in cui si crea l'interfaccia di rete.

1. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Selezionare **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **+ Aggiungi** in **Interfacce di rete**.
3. Immettere o selezionare i valori per le impostazioni seguenti e quindi selezionare **Crea**:

    |Impostazione|Obbligatorio?|Dettagli|
    |---|---|---|
    |NOME|Yes|Il nome deve essere univoco all'interno del gruppo di risorse selezionato. Nel corso del tempo, probabilmente si accumuleranno più interfacce di rete nella sottoscrizione di Azure. Per suggerimenti sulla creazione di una convenzione di denominazione che faciliti la gestione di più interfacce di rete, vedere [Convenzioni di denominazione](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Il nome non può essere modificato dopo la creazione dell'interfaccia di rete.|
    |Rete virtuale|Yes|Selezionare la rete virtuale per l'interfaccia di rete. È possibile assegnare solo un'interfaccia di rete a una rete virtuale presente nella stessa sottoscrizione e località dell'interfaccia di rete. Dopo aver creato un'interfaccia di rete, non è possibile modificare la rete virtuale a cui viene assegnata. La macchina virtuale che viene aggiunta all'interfaccia di rete deve esistere anche nella stessa località e sottoscrizione dell'interfaccia di rete.|
    |Subnet|Yes|Selezionare una subnet all'interno della rete virtuale selezionata. È possibile modificare la subnet a cui l'interfaccia di rete è assegnata dopo la creazione.|
    |Assegnazione di indirizzi IP privati|Yes| In questa impostazione, si è scelto il metodo di assegnazione dell'indirizzo IPv4. Scegliere uno dei metodi di assegnazione seguenti: **Dinamico:** quando si seleziona questa opzione, Azure assegna automaticamente l'indirizzo disponibile successivo dallo spazio di indirizzi della subnet selezionata. **Statico:** quando si seleziona questa opzione, è necessario assegnare manualmente un indirizzo IP disponibile dallo spazio di indirizzi della subnet selezionata. Gli indirizzi statici e dinamici non cambiano fino a quando non vengono modificati o non si elimina l'interfaccia di rete. È possibile modificare il metodo di assegnazione dopo aver creato la l'interfaccia di rete. Il server DHCP di Azure assegna questo indirizzo all'interfaccia di rete all'interno del sistema operativo della macchina virtuale.|
    |Gruppo di sicurezza di rete|No | Lasciare impostato su **Nessuno**, selezionare un [gruppo di sicurezza di rete](security-overview.md) esistente o [creare un gruppo di sicurezza di rete](tutorial-filter-network-traffic.md). I gruppi di sicurezza di rete consentono di filtrare il traffico di rete in ingresso e in uscita da un'interfaccia. È possibile applicare uno o nessun gruppo di sicurezza di rete a un'interfaccia di rete. È inoltre possibile applicare uno o nessun gruppo di sicurezza di rete alla subnet a cui è assegnata l'interfaccia di rete. Quando un gruppo di sicurezza di rete viene applicato a un'interfaccia di rete e alla subnet a cui l'interfaccia di rete è assegnata, potrebbero verificarsi risultati imprevisti. Per risolvere i problemi dei gruppi di sicurezza di rete applicati alle interfacce di rete e alle subnet, vedere [Risolvere i problemi relativi ai gruppi di sicurezza di rete](diagnose-network-traffic-filter-problem.md).|
    |Sottoscrizione|Yes|Selezionare una delle [sottoscrizioni](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) di Azure. La macchina virtuale a cui viene collegata un'interfaccia di rete e la rete virtuale a cui viene connessa devono esistere nella stessa sottoscrizione.|
    |Indirizzo IP privato (IPv6)|No | Se si seleziona questa casella di controllo, viene assegnato un indirizzo IPv6 all'interfaccia di rete, oltre all'indirizzo IPv4 assegnato all'interfaccia di rete. Vedere la sezione [IPv6](#IPv6) di questo articolo per informazioni importanti sull'uso di IPv6 con le interfacce di rete. Non è possibile selezionare un metodo di assegnazione di un indirizzo IPv6. Se si sceglie di assegnare un indirizzo IPv6, questo viene assegnato con il metodo dinamico.
    |Nome IPv6 (viene visualizzata solo quando è selezionata la casella di controllo **Indirizzo IP privato (IPv6)**) |Sì, se la casella di controllo **Indirizzo IP privato (IPv6)** è selezionata.| Questo nome viene assegnato a una configurazione IP secondaria per l'interfaccia di rete. Per altre informazioni sulle configurazioni IP, vedere [Visualizzare le impostazioni dell'interfaccia di rete](#view-network-interface-settings).|
    |Gruppo di risorse|Yes|Selezionare un [gruppo di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) esistente o crearne uno. Un'interfaccia di rete può trovarsi nello stesso gruppo di risorse o in un gruppo diverso rispetto alla macchina virtuale alla quale è collegata o alla rete virtuale a cui è connessa.|
    |Località|Yes|La macchina virtuale a cui viene collegata un'interfaccia di rete e la rete virtuale a cui viene connessa devono esistere nella stessa [località](https://azure.microsoft.com/regions), denominata anche area.|

Il portale non prevede la possibilità di assegnare un indirizzo IP pubblico all'interfaccia di rete in fase di creazione, anche se il portale crea un indirizzo IP pubblico e lo assegna a un'interfaccia di rete quando si crea una macchina virtuale tramite il portale. Per informazioni su come aggiungere un indirizzo IP pubblico all'interfaccia di rete dopo averla creata, vedere [Gestire gli indirizzi IP](virtual-network-network-interface-addresses.md). Se si desidera creare un'interfaccia di rete con un indirizzo IP pubblico, è necessario usare l'interfaccia della riga di comando o PowerShell per creare l'interfaccia di rete.

Il portale non offre la possibilità di assegnare l'interfaccia di rete ai gruppi di sicurezza delle applicazioni quando si crea un'interfaccia di rete, mentre l'interfaccia della riga di comando di Azure e PowerShell offrono questa opportunità. È possibile assegnare un'interfaccia di rete esistente a un gruppo di sicurezza delle applicazioni tramite il portale, purché l'interfaccia di rete sia collegata a una macchina virtuale. Per informazioni su come assegnare un'interfaccia di rete a un gruppo di sicurezza delle applicazioni, vedere [Aggiunta o rimozione dai gruppi di sicurezza dell'applicazione](#add-to-or-remove-from-application-security-groups).

>[!Note]
> Azure assegna un indirizzo MAC all'interfaccia di rete solo dopo che quest'ultima viene collegata a una macchina virtuale e la macchina virtuale viene avviata per la prima volta. Non è possibile specificare l'indirizzo MAC che Azure assegna all'interfaccia di rete. L'indirizzo MAC rimarrà assegnato all'interfaccia di rete finché l'interfaccia non viene eliminata oppure non viene modificato l'indirizzo IP privato assegnato alla configurazione IP primaria dell'interfaccia di rete primaria. Per altre informazioni sugli indirizzi IP e le configurazioni IP, vedere [Gestire gli indirizzi IP](virtual-network-network-interface-addresses.md)

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic#az_network_nic_create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface#create)|

## <a name="view-network-interface-settings"></a>Visualizzare le impostazioni dell'interfaccia di rete

È possibile visualizzare e modificare la maggior parte delle impostazioni di un'interfaccia di rete dopo la sua creazione. Il portale non mostra il suffisso DNS o l'appartenenza al gruppo di sicurezza dell'applicazione per l'interfaccia di rete. È possibile usare i [comandi](#view-settings-commands) di PowerShell o dell'interfaccia della riga di comando di Azure per visualizzare il suffisso DNS e l'appartenenza a un gruppo di sicurezza dell'applicazione.

1. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Selezionare **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
2. Selezionare l'interfaccia di rete di cui si vogliono visualizzare o modificare le impostazioni dall'elenco.
3. Per l'interfaccia di rete selezionata vengono visualizzati gli elementi seguenti:
    - **Panoramica:** fornisce informazioni sull'interfaccia di rete, ad esempio gli indirizzi IP assegnati, la rete virtuale/subnet a cui l'interfaccia di rete è assegnata e la macchina virtuale a cui è collegata l'interfaccia di rete (se collegata). L'immagine seguente illustra la panoramica delle impostazioni per un'interfaccia di rete denominata **mywebserver256**: ![Panoramica dell'interfaccia di rete](./media/virtual-network-network-interface/nic-overview.png) È possibile spostare un'interfaccia di rete in un diverso gruppo di risorse o sottoscrizione selezionando (**modifica**) accanto a **Gruppo di risorse** o **Nome sottoscrizione**. Se si sposta l'interfaccia di rete, è necessario spostare tutte le risorse correlate all'interfaccia di rete. Se ad esempio l'interfaccia di rete viene collegata a una macchina virtuale, è necessario spostare anche la macchina virtuale e le altre risorse correlate alla macchina virtuale. Per spostare un'interfaccia di rete, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). L'articolo elenca i prerequisiti e descrive come spostare le risorse tramite il portale di Azure, PowerShell e l'interfaccia della riga di comando di Azure.
    - **Configurazioni IP:** gli indirizzi IPv4 e IPv6 pubblici e privati vengono assegnati alle configurazioni IP sono elencati qui. Se un indirizzo IPv6 viene assegnato a una configurazione IP, l'indirizzo non viene visualizzato. Per altre informazioni sulle configurazioni IP e su come aggiungere e rimuovere indirizzi IP, vedere [Configurare gli indirizzi IP per le interfacce di rete di Azure](virtual-network-network-interface-addresses.md). In questa sezione vengono configurati anche l'inoltro IP e l'assegnazione di subnet. Per altre informazioni su queste impostazioni, vedere [Abilitare o disabilitare l'inoltro IP](#enable-or-disable-ip-forwarding) e [Cambiare l'assegnazione delle subnet](#change-subnet-assignment).
    - **Server DNS:** è possibile specificare il server DNS al quale i server DHCP di Azure assegnano un'interfaccia di rete. L'interfaccia di rete può ereditare l'impostazione dalla rete virtuale a cui è assegnata l'interfaccia di rete, oppure dispone di un'impostazione personalizzata che sostituisce l'impostazione per la rete virtuale a cui è assegnata. Per modificare il contenuto visualizzato, vedere [Modificare i server DNS](#change-dns-servers).
    - **Gruppo di sicurezza di rete**: mostra il gruppo di sicurezza di rete (se esiste) associato all'interfaccia di rete. Un gruppo di sicurezza di rete contiene regole per filtrare il traffico di rete in entrata e in uscita per l'interfaccia di rete. Se all'interfaccia di rete è associato un gruppo di sicurezza di rete, viene visualizzato il nome del gruppo associato. Per modificare gli elementi visualizzati, vedere [Associare o dissociare un gruppo di sicurezza di rete](#associate-or-dissociate-a-network-security-group).
    - **Proprietà:** visualizza le impostazioni chiave relative all'interfaccia di rete, incluso il suo indirizzo MAC (vuoto se l'interfaccia di rete è collegata a una macchina virtuale) e la sottoscrizione in cui si trova.
    - **Regole di sicurezza effettive:**  le regole di sicurezza sono elencate se l'interfaccia di rete è collegata a una macchina virtuale in esecuzione e un gruppo di sicurezza di rete è associato all'interfaccia, alla subnet cui l'interfaccia è assegnata o a entrambe. Per ulteriori informazioni sul contenuto, vedere [Visualizzare regole di sicurezza valide](#view-effective-security-rules). Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza di rete](security-overview.md).
    - **Route valide:** le route vengono elencate se l'interfaccia di rete è collegata a una macchina virtuale in esecuzione. Le route sono una combinazione di route predefinite di Azure, route definite dall'utente e route BGP esistenti per la subnet a cui è assegnata l'interfaccia di rete. Per ulteriori informazioni sul contenuto, vedere [Visualizzare route valide](#view-effective-routes). Per altre informazioni sulle route predefinite di Azure e le route definite dall'utente, vedere [Panoramica delle route](virtual-networks-udr-overview.md).
    - **Impostazioni comuni di Azure Resource Manager:**  per altre informazioni sulle impostazioni comuni di Azure Resource Manager, vedere [Log attività](../azure-monitor/platform/activity-logs-overview.md), [Controllo di accesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Tag](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Blocchi](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Script di automazione](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

<a name="view-settings-commands"></a>**Comandi**

Se un indirizzo IPv6 viene assegnato a un'interfaccia di rete, l'output di PowerShell restituisce il fatto che l'indirizzo è assegnato, ma non restituisce l'indirizzo assegnato. Analogamente, l'interfaccia della riga di comando restituisce il fatto che l'indirizzo è assegnato, ma restituisce *null* nel relativo output per l'indirizzo.

|Strumento|Comando|
|---|---|
|CLI|[az network nic list](/cli/azure/network/nic#az_network_nic_list) per visualizzare le interfacce di rete nella sottoscrizione; [az network nic show](/cli/azure/network/nic#az_network_nic_show) per visualizzare le impostazioni di un'interfaccia di rete|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) per visualizzare le interfacce di rete nella sottoscrizione o le impostazioni di un'interfaccia di rete|

## <a name="change-dns-servers"></a>Modificare i server DNS

Il server DNS viene assegnato dal server DHCP di Azure all'interfaccia di rete all'interno del sistema operativo della macchina virtuale. Il server DNS assegnato corrisponde all'impostazione relativa al server DNS per un'interfaccia di rete. Per altre informazioni sulle impostazioni di risoluzione dei nomi per le interfacce di rete, vedere [Risoluzione dei nomi per le macchine virtuali](virtual-networks-name-resolution-for-vms-and-role-instances.md). L'interfaccia di rete può ereditare le impostazioni dalla rete virtuale oppure usare proprie impostazioni univoche che sostituiscono l'impostazione della rete virtuale.

1. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Selezionare **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
2. Selezionare l'interfaccia di rete per cui si vuole modificare un server DNS dall'elenco.
3. Selezionare **Server DNS** in **IMPOSTAZIONI**.
4. Selezionare una delle due opzioni seguenti:
    - **Eredita da rete virtuale**: scegliere questa opzione per ereditare l'impostazione del server DNS definita per la rete virtuale alla quale è assegnata l'interfaccia di rete. A livello di rete virtuale viene definito un server DNS personalizzato o il server DNS fornito da Azure. Il server DNS fornito da Azure può risolvere i nomi host per le risorse assegnate alla stessa rete virtuale. Il FQDN deve essere usato per la risoluzione delle risorse assegnate a reti virtuali diverse.
    - **Personalizzato**: è possibile configurare il server DNS per risolvere i nomi tra più reti virtuali. Immettere l'indirizzo IP del server da usare come server DNS. L'indirizzo del server DNS specificato viene assegnato solo a questa interfaccia di rete e sostituisce tutte le impostazioni DNS per la rete virtuale a cui è assegnata l'interfaccia di rete.
5. Selezionare **Salva**.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Abilitare o disabilitare l'inoltro IP

L'inoltro IP abilita la macchina virtuale a cui è collegata un'interfaccia di rete:
- Ricevere il traffico di rete non destinato a uno degli indirizzi IP assegnati a una delle configurazioni IP associate all'interfaccia di rete.
- Inviare il traffico di rete con un indirizzo IP di origine diverso da quello assegnato a una delle configurazioni IP dell'interfaccia di rete.

L'impostazione deve essere abilitata per ogni interfaccia di rete collegata alla macchina virtuale che riceve il traffico che la macchina virtuale deve inoltrare. Una macchina virtuale può inoltrare il traffico se dispone di più interfacce di rete o una singola interfaccia di rete collegata. Anche se l'inoltro IP è un'impostazione di Azure, la macchina virtuale deve anche eseguire un'applicazione che possa inoltrare il traffico, ad esempio applicazioni firewall, di ottimizzazione WAN e di bilanciamento del carico. Una macchina virtuale che esegue applicazioni di rete è spesso definita "appliance virtuale di rete". È possibile visualizzare un elenco di appliance virtuali di rete pronte per la distribuzione in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). L'inoltro IP viene in genere usato con le route definite dall'utente. Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente](virtual-networks-udr-overview.md).

1. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Selezionare **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
2. Selezionare l'interfaccia di rete per la quale abilitare o disabilitare l'inoltro IP.
3. Selezionare **Configurazioni IP** nella sezione **IMPOSTAZIONI**.
4. Selezionare **Abilitato** o **Disabilitato** (impostazione predefinita) per modificare l'impostazione.
5. Selezionare **Salva**.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="change-subnet-assignment"></a>Cambiare l'assegnazione delle subnet

È possibile modificare la subnet, ma non la rete virtuale, a cui viene assegnata un'interfaccia di rete.

1. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Selezionare **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
2. Selezionare l'interfaccia di rete per cui si vuole modificare l'assegnazione della subnet.
3. Selezionare **Configurazioni IP** in **IMPOSTAZIONI**. Se sono presenti indirizzi IP privati per le configurazioni IP elencate che mostrano l'indicazione **(Statico)** a fianco, è necessario modificare il metodo in dinamico seguendo questa procedura. Tutti gli indirizzi IP privati devono essere assegnati con il metodo di assegnazione dinamico per poter modificare l'assegnazione della subnet per l'interfaccia di rete. Se gli indirizzi sono assegnati con il metodo dinamico, procedere al passaggio 5. Se ci sono indirizzi IPv4 assegnati con il metodo statico, completare i passaggi seguenti per modificare il metodo di assegnazione in dinamico:
    - Nell'elenco delle configurazioni IP selezionare la configurazione IP di cui cambiare il metodo di assegnazione degli indirizzi IPv4.
    - Selezionare **Dinamico** per il metodo **Assegnazione** per gli indirizzi IP privati. Non è possibile assegnare un indirizzo IPv6 con il metodo di assegnazione statico.
    - Selezionare **Salva**.
4. Selezionare la subnet nella quale spostare l'interfaccia di rete nell'elenco a discesa **Subnet**.
5. Selezionare **Salva**. I nuovi indirizzi dinamici vengono assegnati dall'intervallo di indirizzi della nuova subnet. Dopo aver assegnato l'interfaccia di rete a una nuova subnet, è possibile assegnare un indirizzo IPv4 statico dall'intervallo di indirizzi della nuova subnet. Per altre informazioni su come aggiungere, modificare e rimuovere indirizzi IP per una scheda di interfaccia di rete, vedere [Gestire gli indirizzi IP](virtual-network-network-interface-addresses.md).

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Aggiunta o rimozione dai gruppi di sicurezza dell'applicazione

È possibile aggiungere o eliminare un'interfaccia di rete da un gruppo di sicurezza delle applicazioni tramite il portale solo se l'interfaccia di rete è collegata a una macchina virtuale. È possibile usare PowerShell o l'interfaccia della riga di comando di Azure per aggiungere o eliminare un'interfaccia di rete da un gruppo di sicurezza delle applicazioni sia che l'interfaccia di rete sia collegata a una macchina virtuale o meno. Altre informazioni sui [gruppi di sicurezza delle applicazioni](security-overview.md#application-security-groups) e su come [creare un gruppo di sicurezza delle applicazioni](manage-network-security-group.md#create-an-application-security-group).

1. Nella casella *Cerca risorse, servizi e documentazione* nella parte superiore del portale iniziare a digitare il nome di una macchina virtuale con un'interfaccia di rete che si vuole aggiungere o eliminare da un gruppo di sicurezza delle applicazioni. Quando il nome della VM compare nei risultati della ricerca, selezionarlo.
2. In **IMPOSTAZIONI** selezionare **Rete**.  Selezionare **Configura i gruppi di sicurezza delle applicazioni**, selezionare i gruppi di sicurezza delle applicazioni a cui si vuole aggiungere l'interfaccia di rete o deselezionare i gruppi di sicurezza delle applicazioni da cui si vuole eliminare l'interfaccia di rete, quindi selezionare **Salva**. Solo le interfacce di rete della stessa rete virtuale possono essere aggiunte allo stesso gruppo di sicurezza delle applicazioni. Il gruppo di sicurezza delle applicazioni deve trovarsi nella stessa posizione dell'interfaccia di rete.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Associare o dissociare un gruppo di sicurezza di rete

1. Nella casella di ricerca nella parte superiore del portale immettere *network interfaces*. Selezionare **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
2. Selezionare dall'elenco l'interfaccia di rete a cui si desidera associare o dissociare un gruppo di sicurezza di rete.
3. Selezionare **Gruppo di sicurezza di rete** in **IMPOSTAZIONI**.
4. Selezionare **Modifica**.
5. Selezionare **Gruppo di sicurezza di rete** e quindi selezionare il gruppo di sicurezza di rete che si desidera associare all'interfaccia di rete, oppure selezionare **Nessuno** per annullare l'associazione di un gruppo di sicurezza di rete.
6. Selezionare **Salva**.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network nic update](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)

## <a name="delete-a-network-interface"></a>Eliminare un'interfaccia di rete

È possibile eliminare un'interfaccia purché non sia collegata a una macchina virtuale. Se un'interfaccia di rete è collegata a una macchina virtuale, è innanzitutto necessario arrestare la macchina virtuale (deallocarla) e quindi scollegare l'interfaccia di rete dalla macchina virtuale. Per scollegare un'interfaccia di rete da una macchina virtuale, seguire la procedura descritta in [Scollegare un'interfaccia di rete da una macchina virtuale](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Non è possibile rimuovere un'interfaccia di rete da una macchina virtuale se questa è l'unica interfaccia di rete collegata alla macchina virtuale. Una macchina virtuale deve sempre avere almeno un'interfaccia di rete collegata. Eliminando una macchina virtuale vengono scollegate tutte le interfacce di rete a essa collegate, ma non eliminate.

1. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Selezionare **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **...** a destra dell'interfaccia di rete che si vuole eliminare dall'elenco di interfacce di rete.
3. Selezionare **Elimina**.
4. Selezionare **Sì** per confermare l'eliminazione dell'interfaccia di rete.

Quando si elimina un'interfaccia di rete vengono rilasciati tutti gli indirizzi MAC o IP assegnati.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic#az_network_nic_delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Risolvere i problemi di connettività

Se non si riesce a comunicare da o verso una macchina virtuale, il problema potrebbe essere causato dalle regole di sicurezza del gruppo di sicurezza di rete o dalle route valide per un'interfaccia di rete. Sono disponibili le opzioni seguenti per risolvere il problema:

### <a name="view-effective-security-rules"></a>Visualizzare le regole di sicurezza valide

Le regole di sicurezza valide per ciascuna interfaccia di rete collegata a una macchina virtuale sono una combinazione delle regole create in un gruppo di sicurezza di rete e delle [regole di sicurezza predefinite](security-overview.md#default-security-rules). La comprensione delle regole di sicurezza valide per un'interfaccia di rete può aiutare a determinare il motivo per cui non è possibile comunicare da o verso una macchina virtuale. È possibile visualizzare le regole valide per qualsiasi interfaccia di rete associata a una macchina virtuale in esecuzione.

1. Nella casella di ricerca nella parte superiore del portale immettere il nome di una macchina virtuale per la quale si vogliono visualizzare le regole di sicurezza valide. Se non si conosce il nome della macchina virtuale, immettere *macchine virtuali* nella casella di ricerca. Quando **Macchine virtuali** viene visualizzato nei risultati della ricerca, selezionarlo e scegliere una macchina virtuale dall'elenco.
2. Selezionare **Rete** in **IMPOSTAZIONI**.
3. Selezionare il nome di un'interfaccia di rete.
4. Selezionare **Regole di sicurezza valide** in **SUPPORTO + RISOLUZIONE DEI PROBLEMI**.
5. Rivedere l'elenco delle regole di sicurezza valide per determinare se esistono regole corrette per le comunicazioni richieste in entrata e in uscita. Ulteriori informazioni su quanto visualizzato nell'elenco sono disponibili in [Panoramica del gruppo di sicurezza di rete](security-overview.md).

La funzione di verifica del flusso IP di Azure Network Watcher consente inoltre di determinare se le regole di sicurezza impediscono la comunicazione tra una macchina virtuale e un endpoint. Per altre informazioni, vedere [Verifica flusso IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Comandi**

- Interfaccia della riga di comando di Azure: [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup) 

### <a name="view-effective-routes"></a>Visualizzare le route valide

Le route valide per ogni interfaccia di rete associata a una macchina virtuale sono costituite da una combinazione di route predefinite, route create ed eventuali route propagate da reti locali tramite BGP attraverso un gateway di rete virtuale di Azure. La comprensione delle route valide per un'interfaccia di rete può aiutare a determinare il motivo per cui non è possibile comunicare da o verso una macchina virtuale. È possibile visualizzare le route valide per qualsiasi interfaccia di rete associata a una macchina virtuale in esecuzione.

1. Nella casella di ricerca nella parte superiore del portale immettere il nome di una macchina virtuale per la quale si vogliono visualizzare le regole di sicurezza valide. Se non si conosce il nome della macchina virtuale, immettere *macchine virtuali* nella casella di ricerca. Quando **Macchine virtuali** viene visualizzato nei risultati della ricerca, selezionarlo e scegliere una macchina virtuale dall'elenco.
2. Selezionare **Rete** in **IMPOSTAZIONI**.
3. Selezionare il nome di un'interfaccia di rete.
4. Selezionare **Route valide** in **SUPPORTO + RISOLUZIONE DEI PROBLEMI**.
5. Rivedere l'elenco delle route valide per determinare se esistono route corrette per le comunicazioni richieste in entrata e in uscita. Per altre informazioni sugli elementi visualizzati nell'elenco, vedere [Panoramica del routing](virtual-networks-udr-overview.md).

La funzione hop successiva di Azure Network Watcher consente inoltre di determinare se le route impediscono la comunicazione tra una macchina virtuale e un endpoint. Per ulteriori informazioni, vedere [Hop successivo](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Comandi**

- Interfaccia della riga di comando di Azure: [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable)

## <a name="permissions"></a>Autorizzazioni

Per eseguire attività nelle interfacce di rete, l'account deve essere assegnato al ruolo [Collaboratore Rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un ruolo [personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le autorizzazioni appropriate elencate nella tabella seguente:

| Azione                                                                     | NOME                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Ottenere l'interfaccia di rete                                     |
| Microsoft.Network/networkInterfaces/write                                  | Creare o aggiornare un'interfaccia di rete                        |
| Microsoft.Network/networkInterfaces/join/action                            | Collegare un'interfaccia di rete a una macchina virtuale           |
| Microsoft.Network/networkInterfaces/delete                                 | Eliminare un'interfaccia di rete                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Aggiungere una risorsa a un'interfaccia di rete tramite un'associazione di servizio     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Ottenere una tabella di route valida per l'interfaccia di rete               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Ottenere gruppi di sicurezza validi per l'interfaccia di rete           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Ottenere i servizi di bilanciamento del carico dell'interfaccia di rete                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Ottenere un'associazione di servizio                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Creare o aggiornare un'associazione di servizio                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Eliminare un'associazione di servizio                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Convalidare un'associazione di servizio                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Ottiene la configurazione IP dell’interfaccia di rete                    |

## <a name="next-steps"></a>Passaggi successivi

- Creare una macchina virtuale con più schede di interfaccia di rete usando l'[interfaccia della riga di comando di Azure](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Creare una VM con una singola scheda di interfaccia di rete e più indirizzi IPv4 usando l'[interfaccia della riga di comando di Azure](virtual-network-multiple-ip-addresses-cli.md) o [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Creare una VM con una singola scheda di interfaccia di rete con un indirizzo IPv6 privato (protetta da Azure Load Balancer) usando l'[interfaccia della riga di comando di Azure](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o il [modello di Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Creare un'interfaccia di rete usando gli script di esempio di [PowerShell](powershell-samples.md) o dell'[interfaccia della riga di comando di Azure](cli-samples.md) oppure i [modelli di Azure Resource Manager](template-samples.md)
- Creare e applicare i [criteri di Azure](policy-samples.md) per le reti virtuali