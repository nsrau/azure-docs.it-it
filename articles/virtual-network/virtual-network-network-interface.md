---
title: Interfacce di rete di Azure | Microsoft Docs
description: Informazioni su come creare ed eliminare le interfacce di rete e assegnare indirizzi IP pubblici e privati alle interfacce di rete. Informazioni su come collegare e scollegare interfacce di rete dalle macchine virtuali di Azure.
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
ms.date: 03/14/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: e2d70bbb2af4231a9ba7e4d9a843593ff5d9f7d3
ms.lasthandoff: 04/14/2017


---

# <a name="network-interfaces"></a>Interfacce di rete

Informazioni sulle interfacce di rete e come usarle. Un'interfaccia di rete è l'interconnessione tra una macchina virtuale di Azure e la rete software sottostante. L'immagine seguente illustra le funzionalità di un'interfaccia di rete:

![Interfaccia di rete](./media/virtual-network-network-interface/nic.png)

Questo articolo descrive i concetti illustrati nell'immagine. Fare clic su uno dei concetti seguenti per passare direttamente alla sezione corrispondente dell'articolo:

- [Interfacce di rete](#nics): un'interfaccia di rete è connessa a una subnet in una rete virtuale di Azure. Nell'immagine, a **VM1** sono collegate due schede di interfaccia di rete, mentre a **VM2** ne è collegata una. Ogni interfaccia di rete è connessa alla stessa rete virtuale, ma a subnet diverse. Questa sezione descrive la procedura per elencare le interfacce di rete esistenti e creare, modificare ed eliminare le interfacce di rete.
- [Configurazioni IP](#ip-configs): a ogni scheda di rete sono associate una o più configurazioni IP. Ogni configurazione IP ha un indirizzo IP privato assegnato. Una configurazione IP può avere un indirizzo IP pubblico associato. Nell'immagine, **NIC1** e **NIC3** hanno ognuna una configurazione IP associata, mentre **NIC2** ne ha due. La configurazione IP assegnata a NIC1 e NIC3 ha indirizzi IP pubblici, mentre nessuna delle due configurazioni IP assegnate a NIC2 ha un indirizzo IP pubblico. Questa sezione spiega come creare, modificare ed eliminare le configurazioni IP con indirizzi IP privati assegnati tramite i metodi di assegnazione statica e dinamica. Illustra anche come associare e dissociare gli indirizzi IP pubblici per una configurazione IP.
- [Gruppi di sicurezza di rete](#nsgs): i gruppi di sicurezza di rete contengono una o più regole di sicurezza in ingresso o in uscita. Le regole stabiliscono il tipo di traffico di rete che può attraversare un'interfaccia di rete, una subnet o entrambe. Nell'immagine, **NIC1** e **NIC3** hanno un gruppo di sicurezza di rete associato, mentre **NIC2** non ne ha. Questa sezione illustra come visualizzare i gruppi di sicurezza di rete applicati a un'interfaccia di rete, aggiungere un gruppo di sicurezza di rete a un'interfaccia di rete e rimuovere un gruppo di sicurezza di rete da un'interfaccia di rete.
- [Macchine virtuali](#vms): una macchina virtuale ha almeno un'interfaccia di rete associata, ma può anche averne diverse, a seconda delle sue dimensioni. Per informazioni sul numero di interfacce di rete supportate per ogni dimensione di VM, vedere gli articoli relativi alle dimensioni delle macchine virtuali [Windows](../virtual-machines/windows/sizes.md) o [Linux](../virtual-machines/linux/sizes.md). Questa sezione descrive come creare VM con una o più interfacce di rete e come collegare e scollegare queste interfacce dalle VM esistenti.

Se non si ha familiarità con le interfacce di rete e le VM in Azure, è consigliabile svolgere l'esercizio disponibile in [Creare la prima rete virtuale di Azure](virtual-network-get-started-vnet-subnet.md) prima di leggere questo articolo. L'esercizio consente di acquisire familiarità con le reti virtuali e le macchine virtuali.

Questo articolo è applicabile alle VM e alle interfacce di rete create con il modello di distribuzione Azure Resource Manager. È consigliabile creare le risorse tramite il modello di distribuzione Resource Manager piuttosto che con il modello di distribuzione classica. Per le differenze tra i due modelli, vedere le [informazioni sui modelli di distribuzione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Le sezioni rimanenti di questo articolo illustrano come completare tutte le attività correlate alle interfacce di rete. Ogni sezione elenca:
- La procedura per completare l'attività nel portale di Azure. Per completare la procedura è necessario accedere al [portale di Azure](http://portal.azure.com). È possibile iscriversi per [ottenere un account di valutazione gratuito](https://azure.microsoft.com/free), se non se ne ha già uno.
- Comandi per completare l'attività usando Azure PowerShell con collegamenti al riferimento per il comando. Installare e configurare Azure PowerShell eseguendo i passaggi descritti nell'articolo sull'[installazione e la configurazione di Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Per informazioni ed esempi relativi ai comandi di PowerShell, digitare `get-help <command> -full`.
- Comandi per completare l'attività usando l'interfaccia della riga di comando di Azure con collegamenti al riferimento per il comando. Per installare l'interfaccia della riga di comando di Azure, seguire la procedura riportata nell'articolo sull'[installazione e la configurazione dell'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Per informazioni sui comandi dell'interfaccia della riga di comando, digitare `az <command> -h`.

## <a name="nics"></a>Interfacce di rete
Completare la procedura nelle sezioni seguenti per creare, modificare ed eliminare le interfacce di rete e le impostazioni:

### <a name="create-nic"></a>Creare un'interfaccia di rete

Un'interfaccia di rete può essere collegata a una macchina virtuale o esistere autonomamente. Per informazioni su come collegare un'interfaccia di rete a una macchina virtuale, vedere la sezione [Collegare un'interfaccia di rete a una macchina virtuale esistente](#vm-attach-nic) di questo articolo.

Per creare un'interfaccia di rete seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** visualizzato fare clic su **+ Aggiungi**.
4. Immettere o selezionare i valori per le impostazioni seguenti nel pannello **Crea interfaccia di rete** visualizzato, quindi fare clic su **Crea**:

    |**Impostazione**|**Obbligatorio?**|**Dettagli**|
    |---|---|---|
    |**Nome**|Sì|Il nome non può essere modificato dopo la creazione dell'interfaccia di rete. Il nome deve essere univoco all'interno del gruppo di risorse selezionato. Leggere [Naming conventions](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) (Convenzioni di denominazione) per i suggerimenti di denominazione.|
    |**Rete virtuale**|Sì|L'interfaccia di rete può essere connessa solo a una rete virtuale che si trova nella stessa posizione e sottoscrizione dell'interfaccia di rete stessa. Anche la VM a cui è collegata l'interfaccia di rete deve trovarsi nella stessa posizione e nella stessa sottoscrizione dell'interfaccia di rete. Se non sono presenti reti virtuali è necessario crearne una. Per creare una rete virtuale, seguire la procedura disponibile nell'articolo [Rete virtuale](virtual-networks-create-vnet-arm-pportal.md). Dopo aver creato un'interfaccia di rete non è possibile modificare la rete virtuale a cui è connessa.|
    |**Subnet**|Sì|Selezionare una subnet all'interno della rete virtuale selezionata. È possibile modificare la subnet a cui l'interfaccia di rete è connessa dopo la creazione.|
    |**Assegnazione di indirizzi IP privati**|Sì| Un indirizzo IP privato viene assegnato dal server DHCP di Azure all'interfaccia di rete al momento della creazione. Il server DHCP assegna un indirizzo disponibile dall'intervallo di indirizzi definiti per la subnet a cui è connessa l'interfaccia di rete. **Indirizzi dinamici:** Azure può assegnare un indirizzo diverso a un'interfaccia di rete quando la macchina virtuale a cui è connessa viene avviata dopo essere stata arrestata (deallocata). L'indirizzo rimane invariato se la macchina virtuale viene riavviata senza essere stata arrestata (deallocata). **Indirizzi statici:** gli indirizzi statici non cambiano fino a quando non vengono modificati o non si elimina l'interfaccia di rete. È possibile modificare il metodo di assegnazione dopo aver creato la l'interfaccia di rete.|
    |**Gruppo di sicurezza di rete**|No|I gruppi di sicurezza di rete consentono di controllare il flusso del traffico di rete in ingresso e in uscita da un'interfaccia. Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo [Gruppi di sicurezza di rete](virtual-networks-nsg.md). È possibile applicare uno o nessun gruppo di sicurezza di rete a un'interfaccia di rete. È possibile applicare uno o nessun gruppo di sicurezza di rete anche alla subnet cui è connessa l'interfaccia di rete. Quando un gruppo di sicurezza di rete viene applicato a un'interfaccia di rete e alla subnet a cui è connessa, a volte si verificano risultati imprevisti. Per risolvere i problemi dei gruppi di sicurezza di rete applicati alle interfacce di rete, vedere l'articolo [Risolvere i problemi relativi ai gruppi di sicurezza di rete](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface).|
    |**Sottoscrizione**|Sì| La VM a cui viene collegata un'interfaccia di rete e la rete virtuale a cui viene connessa devono trovarsi nella stessa sottoscrizione.|
    |**Gruppo di risorse**|Sì| L'interfaccia di rete può trovarsi nello stesso gruppo di risorse o in un gruppo diverso rispetto alla VM alla quale è collegata o alla rete virtuale a cui è connessa.|
    |**Posizione**|Sì|La VM a cui viene collegata un'interfaccia di rete e la rete virtuale a cui viene connessa devono trovarsi nella stessa posizione.|

Il portale di Azure crea una configurazione IP primaria denominata **ipconfig1** con un indirizzo IP privato dinamico e la associa all'interfaccia di rete creata. Per altre informazioni sulle configurazioni IP, vedere la sezione [Configurazioni IP](#ip-configs) di questo articolo. Non è possibile specificare il nome della configurazione IP creata dal portale, assegnare un indirizzo IP privato statico o un indirizzo IP pubblico quando si crea l'interfaccia di rete. Se l'interfaccia di rete viene creata tramite PowerShell o l'interfaccia della riga di comando, è possibile specificare il nome della configurazione IP e assegnare un indirizzo IP statico e un indirizzo IP pubblico. È possibile modificare il metodo di assegnazione degli indirizzi IP privati e definire se all'interfaccia di rete è associato un indirizzo IP pubblico dopo aver creato l'interfaccia di rete. Per modificare le impostazioni dopo la creazione di un'interfaccia di rete, seguire la procedura descritta nella sezione [Modificare una configurazione IP](#change-ip-config) di questo articolo.

>[!Note]
> Azure assegna un indirizzo MAC all'interfaccia di rete solo dopo aver collegato l'interfaccia di rete a una macchina virtuale e aver avviato la VM per la prima volta. Non è possibile specificare l'indirizzo MAC che Azure assegna all'interfaccia di rete. L'indirizzo MAC rimarrà assegnato all'interfaccia di rete finché l'interfaccia non viene eliminata oppure non viene modificato l'indirizzo IP privato assegnato alla configurazione IP primaria dell'interfaccia di rete primaria. Per altre informazioni sulle configurazioni IP, vedere la sezione [Configurazioni IP](#ip-configs) di questo articolo.

|**Strumento**|**Comando**|
|:---|:---|
|**CLI**|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

### <a name="view-nics"></a>Visualizzare e modificare le interfacce e le impostazioni di rete

Per visualizzare e modificare le interfacce e le impostazioni di rete, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account avente almeno le autorizzazioni del ruolo Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli e autorizzazioni agli account.
2. Nella casella che contiene il testo *Cerca risorse* nella parte superiore del portale di Azure digitare *interfacce di rete*. Fare clic su **Interfacce di rete** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Interfacce di rete** visualizzato fare clic sull'interfaccia da visualizzare o per la quale modificare le impostazioni.
4. Nel pannello visualizzato per l'interfaccia selezionata sono elencate le impostazioni seguenti:
    - **Panoramica:** fornisce informazioni sull'interfaccia di rete, ad esempio gli indirizzi IP assegnati, la rete virtuale/subnet a cui è connessa l'interfaccia di rete e la VM a cui è collegata l'interfaccia, se è collegata a una VM. L'immagine seguente illustra la panoramica delle impostazioni per un'interfaccia di rete denominata **mywebserver256**:   ![Panoramica dell'interfaccia di rete](./media/virtual-network-network-interface/nic-overview.png)
    - **Configurazioni IP:** un'interfaccia di rete ha almeno una configurazione IP assegnata, ma può averne più di una. Per informazioni sul numero massimo di configurazioni IP supportate per un'interfaccia di rete, vedere l'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Ogni configurazione IP ha un indirizzo IP privato e può avere un indirizzo IP pubblico. Per modificare l'impostazione visualizzata, seguire la procedura illustrata nelle sezioni [Aggiungere una configurazione IP secondaria a un'interfaccia di rete](#create-ip-config), [Modificare una configurazione IP](#change-ip-config) o [Eliminare una configurazione IP](#delete-ip-config) di questo articolo.
    - **Server DNS:** è possibile specificare il server DNS al quale i server DHCP di Azure assegnano un'interfaccia di rete. Scegliere tra un server DNS interno di Azure o un server DNS personalizzato. Per modificare l'impostazione visualizzata, seguire la procedura descritta nella sezione [Modificare le impostazioni DNS per un'interfaccia di rete](#dns) di questo articolo.
    - **Gruppo di sicurezza di rete:** indica se all'interfaccia di rete è associato un gruppo di sicurezza di rete. Se all'interfaccia di rete è associato un gruppo di sicurezza di rete, viene visualizzato il nome del gruppo associato. Per modificare l'impostazione visualizzata, seguire la procedura descritta nella sezione [Associare o dissociare un gruppo di sicurezza di rete per un'interfaccia di rete](#associate-nsg) di questo articolo.
    - **Proprietà:** visualizza le impostazioni chiave dell'interfaccia di rete, incluso l'indirizzo MAC e la sottoscrizione in cui si trova. È possibile spostare un'interfaccia di rete in un gruppo di risorse o in una sottoscrizione diversi, se si spostano anche tutte le risorse correlate all'interfaccia di rete. Se l'interfaccia di rete è collegata a una macchina virtuale, ad esempio, è necessario spostare anche la macchina virtuale e altre risorse correlate. Per spostare un'interfaccia di rete, vedere l'articolo [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal), che elenca i prerequisiti e descrive come spostare le risorse tramite il portale di Azure, PowerShell e l'interfaccia della riga di comando di Azure.
    - **Regole di sicurezza effettive:** le regole di sicurezza sono elencate se l'interfaccia di rete è collegata a una macchina virtuale in esecuzione e un gruppo di sicurezza di rete è associato all'interfaccia, alla subnet cui l'interfaccia è connessa o a entrambe. Per altre informazioni sulle impostazioni visualizzate, vedere l'articolo [Risolvere i problemi relativi ai gruppi di sicurezza di rete](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface). Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo [Gruppi di sicurezza di rete](virtual-networks-nsg.md).
    - **Route valide:** le route vengono elencate se l'interfaccia di rete è collegata a una VM in esecuzione. Le route sono una combinazione di route predefinite di Azure, route definite dall'utente e route BGP esistenti per la subnet a cui è connessa l'interfaccia di rete. Per altre informazioni sulle impostazioni visualizzate, vedere l'articolo [Risolvere i problemi relativi alle route](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface). Per altre informazioni sulle route definite dall'utente, vedere l'articolo [Route definite dall'utente](virtual-networks-udr-overview.md).
    - **Impostazioni comuni di Azure Resource Manager:** per altre informazioni sulle impostazioni comuni di Azure Resource Manager, vedere gli articoli [Log attività](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Controllo di accesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Tag](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Blocchi](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Script di automazione](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

|**Strumento**|**Comando**|
|---|---|
|**CLI**|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) per visualizzare le interfacce di rete nella sottoscrizione; [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) per visualizzare le impostazioni di un'interfaccia di rete|
|**PowerShell**|[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) per visualizzare le interfacce di rete nella sottoscrizione o le impostazioni di un'interfaccia di rete|

### <a name="dns"></a>Modificare le impostazioni DNS per un'interfaccia di rete

Per modificare le impostazioni DNS di un'interfaccia di rete seguire questa procedura. Il server DNS viene assegnato alla VM dal server DHCP di Azure. Per altre informazioni sulle impostazioni di risoluzione dei nomi per le interfacce di rete, vedere l'articolo [Risoluzione dei nomi per le macchine virtuali](virtual-networks-name-resolution-for-vms-and-role-instances.md).

1. Completare i passaggi da 1 a 3 nella sezione [Visualizzare e modificare le interfacce e le impostazioni di rete](#view-nics) di questo articolo per l'interfaccia di rete per cui modificare le impostazioni.
2. Nel pannello relativo all'interfaccia di rete selezionata fare clic su **Server DNS**.
3. Selezionare una delle opzioni seguenti:
    - **Eredita da rete virtuale (impostazione predefinita)**: scegliere questa opzione per ereditare l'impostazione del server DNS definita per la rete virtuale alla quale è connessa l'interfaccia di rete. A livello di rete virtuale viene definito un server DNS personalizzato o il server DNS fornito da Azure. Il server DNS fornito da Azure può risolvere i nomi delle risorse collegate alla stessa rete virtuale, ma non delle risorse collegate a reti virtuali differenti.
    - **Personalizzata**: è possibile configurare il server DNS per risolvere i nomi tra più reti virtuali. Immettere l'indirizzo IP del server da usare come server DNS. L'indirizzo del server DNS specificato viene assegnato solo a questa interfaccia di rete e sostituisce qualsiasi impostazione DNS per la rete virtuale alla quale è connessa l'interfaccia di rete.
4. Fare clic su **Save**.

|**Strumento**|**Comando**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="ip-forwarding"></a>Modificare l'inoltro IP per un'interfaccia di rete

L'inoltro IP consente alla macchina virtuale cui è collegata un'interfaccia di rete di eseguire queste operazioni:
- Ricevere il traffico di rete non destinato a uno degli indirizzi IP assegnati a una delle configurazioni IP associate all'interfaccia di rete.
- Inviare il traffico di rete con un indirizzo IP di origine diverso rispetto a quello assegnato a una delle configurazioni IP.

L'impostazione deve essere abilitata per ogni interfaccia di rete collegata alla VM che riceve il traffico che la VM deve inoltrare. Una VM può inoltrare il traffico avendo sia una che più interfacce di rete collegate. Anche se l'inoltro IP è un'impostazione di Azure, la VM deve anche eseguire un'applicazione che possa inoltrare il traffico, ad esempio applicazioni firewall, di ottimizzazione WAN e di bilanciamento del carico. Una VM che esegue applicazioni di rete è spesso definita "appliance virtuale di rete". È possibile visualizzare un elenco di appliance virtuali di rete pronte per la distribuzione in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). L'inoltro IP viene in genere usato con le route definite dall'utente. Per altre informazioni sulle route definite dall'utente, vedere l'articolo [Route definite dall'utente](virtual-networks-udr-overview.md).

Per modificare le impostazioni di inoltro IP di un'interfaccia di rete seguire questa procedura:

1. Completare i passaggi da 1 a 3 nella sezione [Visualizzare e modificare le interfacce e le impostazioni di rete](#view-nics) di questo articolo per l'interfaccia di rete da modificare.
2. Nel pannello relativo all'interfaccia di rete selezionata fare clic su Configurazioni IP
3. Fare clic su **Abilitato** o **Disabilitato** (impostazione predefinita) per modificare l'impostazione.
4. Fare clic su **Salva**.

|**Strumento**|**Comando**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="subnet"></a>Modificare la subnet a cui è connessa un'interfaccia di rete

È possibile modificare la subnet, ma non la rete virtuale a cui l'interfaccia di rete è connessa. Per modificare la subnet è necessario assegnare indirizzi IP privati dinamici a tutte le configurazioni IP associate all'interfaccia di rete. Per modificare la subnet cui è connessa un'interfaccia di rete seguire questa procedura:

1. Completare i passaggi da 1 a 3 nella sezione [Visualizzare e modificare le interfacce e le impostazioni di rete](#view-nics) di questo articolo per l'interfaccia di rete da connettere a una subnet diversa.
2. Fare clic su **Configurazioni IP** nel pannello relativo all'interfaccia di rete selezionata. Se sono presenti indirizzi IP privati delle configurazioni IP elencate che hanno un indirizzo IP privato assegnato con il metodo statico, è necessario modificare il metodo in dinamico seguendo questa procedura. Se gli indirizzi sono stati assegnati con il metodo dinamico, procedere al passaggio 3:
    - Nell'elenco di configurazioni IP fare clic sull'indirizzo IP statico per la configurazione IP da modificare.
    - Nel pannello visualizzato per la configurazione IP fare clic su **Dinamico** per il metodo di **Assegnazione**.
    - Fare clic su **Salva**.
3. Selezionare la subnet alla quale connettere l'interfaccia di rete nell'elenco a discesa **Subnet**.
4. Fare clic su **Salva**. I nuovi indirizzi dinamici verranno assegnati dall'intervallo di indirizzi della subnet. Sarà eventualmente anche possibile assegnare indirizzi IP statici dal nuovo intervallo di indirizzi della subnet.

|**Strumento**|**Comando**|
|---|---|
|**CLI**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


### <a name="delete-nic"></a>Eliminare un'interfaccia di rete

È possibile eliminare un'interfaccia di rete se non è collegata a una VM. Se l'interfaccia è collegata a una VM, è necessario scollegarla prima di procedere all'eliminazione. Per scollegare un'interfaccia di rete da una VM, seguire la procedura descritta nella sezione [Scollegare un'interfaccia di rete da una macchina virtuale esistente](#vm-detach-nic) di questo articolo.

1. Completare i passaggi 1 e 2 nella sezione [Visualizzare e modificare le interfacce e le impostazioni di rete](#view-nics) di questo articolo per l'interfaccia di rete da eliminare.
2. Fare clic con il pulsante destro del mouse sull'interfaccia di rete da eliminare, quindi scegliere **Elimina**.
3. Fare clic su **Sì** per confermare l'eliminazione dell'interfaccia di rete.

Quando si elimina un'interfaccia di rete vengono rilasciati tutti gli indirizzi MAC o IP assegnati.

|**Strumento**|**Comando**|
|---|---|
|**CLI**|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configs"></a>Configurazioni IP
Ogni interfaccia di rete ha almeno una configurazione IP, denominata configurazione **primaria**. Un'interfaccia di rete può anche avere una o più configurazioni IP *secondarie* associate. È previsto un limite al numero di indirizzi IP che possono essere assegnati a un'interfaccia di rete. Per informazioni dettagliate, vedere l'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Ogni configurazione IP:
- Ha un indirizzo IP privato assegnato con il metodo statico o dinamico. Gli indirizzi IP dinamici possono cambiare se una VM viene avviata dopo essere stata arrestata (deallocata). Gli indirizzi IP statici vengono rilasciati da un'interfaccia di rete solo se questa viene eliminata.
- Può avere un indirizzo IP pubblico associato.

I server DHCP di Azure assegnano l'indirizzo IP privato della configurazione IP primaria dell'interfaccia all'interfaccia di rete all'interno del sistema operativo della VM.

L'assegnazione di più indirizzi IP a un'interfaccia di rete è utile per gli scenari seguenti:
- Ospitare più siti Web o servizi con indirizzi IP e certificati SSL diversi in un singolo server.
- Una VM che funge da appliance virtuale di rete, ad esempio un firewall o un servizio di bilanciamento del carico.
- Aggiungere qualsiasi indirizzo IP per qualsiasi scheda di interfaccia di rete a un pool back-end di Azure Load Balancer. In passato, era possibile aggiungere a un pool di back-end solo gli indirizzi IP primari per la scheda di interfaccia di rete primaria. Per altre informazioni su come bilanciare il carico di più configurazioni IP, leggere l'articolo [Load balancing multiple IP configurations](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Bilanciamento del carico di più configurazioni IP).

È previsto un limite per il numero di indirizzi IP pubblici che possono essere usati in una sottoscrizione e per il numero di indirizzi IP privati che possono essere assegnati a un'interfaccia di rete. Per altre informazioni sui limiti, vedere l'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="create-ip-config"></a>Aggiungere una configurazione IP secondaria a un'interfaccia di rete

È possibile aggiungere a un'interfaccia di rete il numero di configurazioni IP necessarie, entro i limiti elencati nell'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Per aggiungere una configurazione IP a un'interfaccia di rete seguire questa procedura:

1. Completare i passaggi da 1 a 3 nella sezione [Visualizzare e modificare le interfacce e le impostazioni di rete](#view-nics) di questo articolo per l'interfaccia di rete alla quale aggiungere la configurazione IP.
2. Fare clic su **Configurazioni IP** nel pannello relativo all'interfaccia di rete selezionata.
3. Fare clic su **+ Aggiungi** nel pannello visualizzato per le configurazioni IP.
4. Specificare le impostazioni seguenti, quindi fare clic su **OK** per chiudere il pannello **Aggiungi configurazione IP**:

    |**Impostazione**|**Obbligatorio?**|**Dettagli**|
    |---|---|---|
    |**Nome**|Sì|Deve essere univoco per l'interfaccia di rete|
    |**Tipo**|Sì|Dato che si aggiunge una configurazione IP a un'interfaccia di rete esistente e ogni interfaccia di rete deve avere una configurazione IP primaria, l'unica opzione possibile è **Secondaria**.|
    |**Metodo di assegnazione di indirizzi IP privati**|Sì|Gli indirizzi **dinamici** possono cambiare se la macchina virtuale viene riavviata dopo essere stata arrestata (deallocata). Gli indirizzi IP **statici** non vengono rilasciati fino a quando non viene eliminata l'interfaccia di rete. Dall'intervallo di indirizzi della subnet specificare un indirizzo IP non attualmente usato da un'altra configurazione IP.|
    |**Indirizzo IP pubblico**|No|**Disabilitato:** nessuna risorsa di indirizzo IP pubblico è attualmente associata alla configurazione IP. **Abilitato:** selezionare un indirizzo IP pubblico esistente o crearne uno nuovo. Per informazioni su come creare un indirizzo IP pubblico, vedere l'articolo [Indirizzi IP](virtual-network-public-ip-address.md#create).|
5. Aggiungere manualmente gli indirizzi IP privati secondari al sistema operativo della VM seguendo le istruzioni disponibili nell'articolo [Assegnare più indirizzi IP alle macchine virtuali](virtual-network-multiple-ip-addresses-portal.md#os-config). Non aggiungere indirizzi IP pubblici al sistema operativo della VM.

|**Strumento**|**Comando**|
|---|---|
|**CLI**|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="change-ip-config"></a>Modificare una configurazione IP

Per modificare le impostazioni degli indirizzi IP privati e pubblici per qualsiasi configurazione IP primaria o secondaria, seguire questa procedura:

1. Completare i passaggi da 1 a 3 nella sezione [Visualizzare e modificare le interfacce e le impostazioni di rete](#view-nics) di questo articolo per l'interfaccia di rete da modificare.
2. Fare clic su **Configurazioni IP** nel pannello relativo all'interfaccia di rete selezionata.
3. Fare clic sulla configurazione IP da modificare nel pannello visualizzato per le configurazioni IP.
4. Modificare le impostazioni in base alle esigenze usando le informazioni sulle impostazioni descritte nella sezione [Aggiungere una configurazione IP](#create-ip-config) di questo articolo, quindi fare clic su **Salva** per chiudere il pannello relativo alla configurazione IP selezionata.

>[!NOTE]
>Se l'interfaccia di rete primaria ha più configurazioni IP e si modifica l'indirizzo IP privato della configurazione IP primaria, in Windows è necessario riassegnare manualmente tutti gli indirizzi IP secondari all'interfaccia di rete. Questa operazione non è necessaria in Linux. Per assegnare manualmente gli indirizzi IP a un'interfaccia di rete in un sistema operativo, vedere l'articolo [Assegnare più indirizzi IP alle macchine virtuali](virtual-network-multiple-ip-addresses-portal.md#os-config). Non aggiungere indirizzi IP pubblici al sistema operativo della VM.

>[!WARNING]
>Per modificare l'indirizzo IP privato di una configurazione IP secondaria associata a una scheda di interfaccia di rete secondaria, i passaggi precedenti devono essere completati solo dopo l'arresto e la deallocazione della macchina virtuale.

|**Strumento**|**Comando**|
|---|---|
|**CLI**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="delete-ip-config"></a>Eliminare una configurazione IP secondaria da un'interfaccia di rete

Seguire questa procedura per eliminare una configurazione IP secondaria da un'interfaccia di rete:

1. Completare i passaggi da 1 a 3 nella sezione [Visualizzare e modificare le interfacce e le impostazioni di rete](#view-nics) di questo articolo per l'interfaccia di rete da modificare.
2. Fare clic su **Configurazioni IP** nel pannello relativo all'interfaccia di rete selezionata.
3. Fare clic con il pulsante destro del mouse sulla configurazione IP secondaria da eliminare, quindi scegliere **Elimina**. Se la configurazione aveva una risorsa di indirizzo IP pubblico associata, la risorsa verrà dissociata dalla configurazione IP, ma non eliminata.
4. Chiudere il pannello **Configurazioni IP**.

|**Strumento**|**Comando**|
|---|---|
|**CLI**|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="nsgs"></a>Gruppi di sicurezza di rete
Un gruppo di sicurezza di rete contiene un elenco di regole in ingresso e in uscita che consentono o impediscono il traffico verso un'interfaccia di rete. Un'interfaccia di rete e la subnet cui è connessa l'interfaccia non devono avere necessariamente un gruppo di sicurezza di rete associato. Un gruppo di sicurezza di rete può essere tuttavia associato a un'interfaccia di rete, alla subnet cui è connessa l'interfaccia di rete o a entrambe. I gruppi di sicurezza di rete con regole applicabili a tutte le interfacce di rete connesse alla subnet sono in genere associate alle subnet. È poi possibile applicare un gruppo di sicurezza di rete con regole più granulari alle singole interfacce di rete. Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo [Gruppi di sicurezza di rete](virtual-networks-nsg.md).

### <a name="associate-nsg"></a>Associare o dissociare un gruppo di sicurezza di rete per un'interfaccia di rete

Per associare un gruppo di sicurezza di rete a un'interfaccia di rete oppure dissociare un gruppo di sicurezza di rete da un'interfaccia di rete seguire questa procedura:

1. Completare i passaggi da 1 a 3 nella sezione [Visualizzare e modificare le interfacce e le impostazioni di rete](#view-nics) di questo articolo per l'interfaccia di rete per la quale associare o dissociare un gruppo di sicurezza di rete.
2. Nel pannello relativo all'interfaccia di rete selezionata fare clic su **Gruppo di sicurezza di rete**. Verrà visualizzato un pannello con **Modifica** nella parte superiore. Se all'interfaccia di rete non sono attualmente associati gruppi di sicurezza di rete, verrà visualizzato **Gruppo di sicurezza di rete** *Nessuno*. Se un gruppo di sicurezza di rete è attualmente associato a un'interfaccia di rete, verrà visualizzato **Gruppo di sicurezza di rete** *nome gruppo di sicurezza di rete*, dove "nome gruppo di sicurezza di rete" è il nome del gruppo di sicurezza di rete attualmente associato all'interfaccia di rete.
3. Fare clic su **Modifica**.
4. Fare clic su **Gruppo di sicurezza di rete**. Se non sono elencati gruppi di sicurezza di rete, evidentemente la sottoscrizione non ne contiene. Per creare un gruppo di sicurezza di rete seguire la procedura disponibile nell'articolo [Gruppi di sicurezza di rete](virtual-networks-create-nsg-arm-pportal.md).
5. Nel pannello **Scegli un gruppo di sicurezza di rete** visualizzato fare clic su un gruppo di sicurezza di rete esistente dall'elenco per associarlo all'interfaccia di rete, oppure fare clic su **Nessuno** per dissociare un gruppo di sicurezza di rete attualmente associato a un'interfaccia di rete.
6. Fare clic su **Salva**.

|**Strumento**|**Comando**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vms"></a>Collegare e scollegare interfacce di rete da una macchina virtuale

È possibile collegare un'interfaccia di rete esistente a una VM al momento della creazione oppure collegare un'interfaccia di rete esistente a una VM esistente. È anche possibile scollegare un'interfaccia di rete da una VM esistente che ha almeno due interfacce di rete. Anche se il portale crea un'interfaccia di rete quando si crea una VM, non consente di eseguire queste operazioni:

- Specificare un'interfaccia di rete esistente da collegare quando si crea la VM
- Creare una VM con più interfacce di rete collegate
- Specificare un nome per l'interfaccia di rete. Il portale crea l'interfaccia di rete con un nome predefinito.
- Specificare il metodo di assegnazione statico per gli indirizzi IP privati. Il portale assegna automaticamente un indirizzo IP privato dinamico, anche se è possibile modificare il metodo di assegnazione dopo che il portale avrà creato l'interfaccia di rete.

È possibile usare PowerShell o l'interfaccia della riga di comando per creare un'interfaccia di rete o una VM con tutti gli attributi indicati in precedenza per i quali non è possibile usare il portale. Prima di completare le attività nelle sezioni seguenti, prendere in considerazione i vincoli e i comportamenti seguenti:

- Diverse dimensioni delle macchine virtuali supportano volumi diversi di schede di interfaccia di rete. Per informazioni sul numero di schede di interfaccia di rete supportate per ogni dimensione di macchina virtuale, vedere gli articoli relativi alle dimensioni delle macchine virtuali [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- In precedenza, le schede di interfaccia di rete potevano essere aggiunte solo a macchine virtuali in grado di supportare più schede e create con almeno due schede di interfacce di rete. Non era possibile aggiungere alcuna scheda di interfaccia di rete a una macchina virtuale creata con una scheda, anche se le dimensioni della macchina virtuale supportavano più schede. Al contrario, era possibile rimuovere solo schede di interfaccia di rete da una macchina virtuale con almeno tre schede collegate, perché alle macchine virtuali create con almeno due schede dovevano essere sempre collegate almeno due schede di interfacce di rete. Attualmente non si applica alcuno di questi vincoli. È ora possibile creare una macchina virtuale con qualsiasi numero di schede di interfacce di rete (fino al numero supportato dalle dimensioni della macchina virtuale) e aggiungere o rimuovere qualsiasi numero di schede, purché alla macchina virtuale sia sempre collegata almeno una scheda di interfaccia di rete. 
- Per impostazione predefinita, la prima interfaccia di rete collegata a una VM è definita *primaria*. Tutte le altre interfacce di rete collegate alla VM sono *secondarie*.
- Per impostazione predefinita, tutto il traffico in uscita dalla VM viene inviato all'indirizzo IP assegnato alla configurazione IP primaria dell'interfaccia di rete primaria. È naturalmente possibile definire l'indirizzo IP usato per il traffico in uscita all'interno del sistema operativo della VM.
- In passato, tutte le VM nello stesso set di disponibilità dovevano avere una o più interfacce di rete. Possono ora esistere VM con un numero qualsiasi di interfacce di rete nello stesso set di disponibilità. Una VM può essere tuttavia aggiunta a un set di disponibilità solo in fase di creazione. Per altre informazioni sui set di disponibilità, vedere l'articolo [Gestire la disponibilità delle macchine virtuali Windows in Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Anche se le interfacce di rete collegate alla stessa VM possono essere connesse a subnet diverse all'interno di una rete virtuale, le interfacce di rete devono essere tutte connesse alla stessa rete virtuale.
- È possibile aggiungere qualsiasi indirizzo IP per qualsiasi configurazione IP di un'interfaccia di rete primaria e secondaria a un pool back-end di Azure Load Balancer. In passato, era possibile aggiungere a un pool di back-end solo gli indirizzi IP primari per la scheda di interfaccia di rete primaria.
- Se si elimina una macchina virtuale, le schede di interfaccia di rete virtuale a questa connesse non vengono eliminate. Quando si elimina una macchina virtuale, le schede di interfaccia di rete vengono scollegate dalla macchina virtuale. È possibile collegare le schede di interfaccia di rete a macchine virtuali diverse o eliminarle.

### <a name="vm-create"></a>Collegare una o più interfacce di rete quando si crea una macchina virtuale

Non è possibile collegare interfacce di rete esistenti a una nuova VM oppure creare una VM con più interfacce di rete tramite il portale di Azure. È possibile usare i seguenti comandi dell'interfaccia della riga di comando di Azure o di PowerShell per collegare una o più interfacce di rete durante la creazione di una VM:

- **Interfaccia della riga di comando:** [az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)
- **PowerShell:** [New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-view-nic"></a> Visualizzare le interfacce di rete collegate a una macchina virtuale

1. Accedere al [portale di Azure](https://portal.azure.com) con un account che abbia le autorizzazioni per il ruolo di Proprietario, Collaboratore o Collaboratore Rete per la sottoscrizione. Vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) per altre informazioni sull'assegnazione di ruoli agli account.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *macchine virtuali*. Fare clic su **macchine virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Macchine virtuali** visualizzato fare clic sul nome della VM per la quale visualizzare le interfacce di rete collegate.
4. Nel pannello **Macchina virtuale** visualizzato per la VM selezionata fare clic su **Interfacce di rete**.

|**Strumento**|**Comando**|
|---|---|
|**CLI**|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|**PowerShell**|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="vm-attach-nic"></a>Collegare un'interfaccia di rete a una macchina virtuale esistente

La VM alla quale collegare un'interfaccia di rete deve supportare più interfacce di rete ed essere arrestata (deallocata). Non è possibile collegare interfacce di rete a una VM esistente tramite il portale di Azure. È possibile usare i seguenti comandi dell'interfaccia della riga di comando di Azure o di PowerShell per collegare interfacce di rete alle VM:

- **Interfaccia della riga di comando:** [az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)
- **PowerShell:** [Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-detach-nic"></a>Scollegare un'interfaccia di rete da una macchina virtuale esistente

La VM dalla quale si vuole scollegare un'interfaccia di rete deve essere arrestata (deallocata) e avere attualmente almeno due interfacce di rete collegate. È possibile scollegare qualsiasi interfaccia di rete, ma la VM deve essere sempre almeno un'interfaccia di rete collegata. Se si scollega un'interfaccia di rete primaria, Azure assegnerà l'attributo di interfaccia primaria a all'interfaccia di rete rimanente che è collegata alla VM da più tempo. È anche possibile designare autonomamente l'interfaccia di rete primaria. Non è possibile scollegare interfacce di rete da una VM o impostare l'attributo di interfaccia primaria per un'interfaccia di rete tramite il portale di Azure, ma è possibile eseguire entrambe le operazioni usando l'interfaccia della riga di comando o PowerShell. È possibile usare i seguenti comandi dell'interfaccia della riga di comando di Azure o di PowerShell per scollegare interfacce di rete dalle VM:

- **Interfaccia della riga di comando:** [az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)
- **PowerShell:** [Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="next-steps"></a>Passaggi successivi
Per creare una VM con più interfacce di rete o configurazioni IP usando gli script, vedere gli articoli seguenti:

|**Attività**|**Strumento**|
|---|---|
|**Creare una macchina virtuale con più NIC**|[Interfaccia della riga di comando](virtual-network-deploy-multinic-arm-cli.md) e [PowerShell](virtual-network-deploy-multinic-arm-ps.md)|
|**Creare una VM con una singola interfaccia di rete con più indirizzi IP assegnati**|[Interfaccia della riga di comando](virtual-network-multiple-ip-addresses-cli.md) e [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

