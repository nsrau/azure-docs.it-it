---
title: Creare, modificare o eliminare un gruppo di sicurezza di rete di Azure | Microsoft Docs
description: Informazioni su come creare, modificare o eliminare un gruppo di sicurezza di rete.
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
ms.date: 04/05/2018
ms.author: jdial
ms.openlocfilehash: d6dfd88f9349312cc81d760a2c473f32ff932885
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543088"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Creare, modificare o eliminare un gruppo di sicurezza di rete

Le regole di sicurezza dei gruppi di sicurezza di rete consentono di filtrare il tipo di traffico di rete consentito in ingresso e in uscita dalle subnet della rete virtuale e dalle interfacce di rete. Se non si ha familiarità con i gruppi di sicurezza di rete, vedere [Panoramica dei gruppi di sicurezza di rete](security-overview.md) per altre informazioni e completare l'esercitazione [Filtrare il traffico di rete](tutorial-filter-network-traffic.md) per acquisire dimestichezza con i gruppi di sicurezza di rete.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di completare i passaggi di qualsiasi sezione di questo articolo, eseguire le attività seguenti:

- Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usa il portale, aprire https://portal.azure.com e accedere con l'account Azure.
- Se si usano i comandi di PowerShell per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/powershell) o tramite PowerShell dal computer in uso. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Questa esercitazione richiede il modulo di Azure PowerShell 5.4.1 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.
- Se si usano i comandi dell'interfaccia della riga di comando di Azure per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/bash) o tramite l'interfaccia della riga di comando dal computer in uso. Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.28 o versioni successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

L'account con cui si accede o con cui ci si collega ad Azure deve essere assegnato al ruolo [collaboratore di rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le operazioni appropriate elencate nelle [Autorizzazioni](#permissions).

## <a name="work-with-network-security-groups"></a>Usare i gruppi di sicurezza di rete

È possibile creare un gruppo, [visualizzare tutti i gruppi di sicurezza di rete](#view-all-network-security-groups), [visualizzare i dettagli di un gruppo](#view-details-of-a-network-security-group), [modificare](#change-a-network-security-group) ed [eliminare](#delete-a-network-security-group) un gruppo di sicurezza di rete. È anche possibile [associare o annullare l'associazione](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) di un gruppo di sicurezza di rete da un'interfaccia di rete o da una subnet.

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

È previsto un limite al numero di gruppi di sicurezza di rete che è possibile creare per ogni sottoscrizione e località di Azure. Per informazioni dettagliate, vedere [Limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Nell'angolo superiore sinistro del portale selezionare **+ Crea una risorsa**.
2. Selezionare **Rete** e quindi selezionare **Gruppo di sicurezza di rete**.
3. Immettere un **Nome** per il gruppo di sicurezza di rete, selezionare **Sottoscrizione**, creare un nuovo **Gruppo di risorse** o selezionare un gruppo di risorse esistente, selezionare una **Località** e quindi scegliere **Crea**.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Visualizzare tutti i gruppi di sicurezza di rete

Nella casella di ricerca nella parte superiore del portale immettere *Gruppi di sicurezza di rete*. Selezionare la voce **Gruppi di sicurezza di rete** quando viene visualizzata nei risultati della ricerca. Vengono elencati i gruppi di sicurezza di rete presenti nella sottoscrizione.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network nsg list](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Visualizzare i dettagli di un gruppo di sicurezza di rete

1. Nella casella di ricerca nella parte superiore del portale immettere *Gruppi di sicurezza di rete*. Selezionare la voce **Gruppi di sicurezza di rete** quando viene visualizzata nei risultati della ricerca.
2. Selezionare nell'elenco il gruppo di sicurezza di rete di cui si vogliono visualizzare i dettagli. In **IMPOSTAZIONI** è possibile visualizzare le **Regole di sicurezza in ingresso** e le **Regole di sicurezza in uscita**, le **Interfacce di rete** e le **Subnet** a cui è associato il gruppo di sicurezza di rete. È anche possibile abilitare o disabilitare i **Log di diagnostica** e visualizzare le **Regole di sicurezza effettive**. Per altre informazioni, vedere [Log di diagnostica](virtual-network-nsg-manage-log.md) e [Visualizzare le regole di sicurezza effettive](diagnose-network-traffic-filter-problem.md).
3. Per altre informazioni sulle impostazioni comuni di Azure elencate, vedere gli articoli seguenti:
    *   [Log attività](../azure-monitor/platform/activity-logs-overview.md)
    *   [Controllo di accesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Tag](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Blocchi](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Script di automazione](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Comandi**

- Interfaccia della riga di comando di Azure: [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Modificare un gruppo di sicurezza di rete

1. Nella casella di ricerca nella parte superiore del portale immettere *Gruppi di sicurezza di rete*. Selezionare la voce **Gruppi di sicurezza di rete** quando viene visualizzata nei risultati della ricerca.
2. Selezionare il gruppo di sicurezza di rete che si vuole modificare. Le modifiche più comuni sono l'[aggiunta](#create-a-security-rule) o la [rimozione](#delete-a-security-rule) di regole di sicurezza e l'[associazione o l'annullamento dell'associazione di un gruppo di sicurezza di rete da una subnet o un'interfaccia di rete](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Comandi**

- Interfaccia della riga di comando di Azure: [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Associare o annullare l'associazione di un gruppo di sicurezza di rete da una subnet o un'interfaccia di rete

Per associare o annullare l'associazione di un gruppo di sicurezza di rete da un'interfaccia di rete, vedere [Associare o annullare l'associazione di un gruppo di sicurezza di rete da un'interfaccia di rete](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Per associare o annullare l'associazione di un gruppo di sicurezza di rete da una subnet, vedere [Cambiare le impostazioni della subnet](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Eliminare un gruppo di sicurezza di rete

Se un gruppo di sicurezza di rete è associato a una subnet o a un'interfaccia di rete, non può essere eliminato. [Annullare l'associazione](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) di un gruppo di sicurezza di rete da tutte le subnet e le interfacce di rete prima di tentare di eliminarlo.

1. Nella casella di ricerca nella parte superiore del portale immettere *Gruppi di sicurezza di rete*. Selezionare la voce **Gruppi di sicurezza di rete** quando viene visualizzata nei risultati della ricerca.
2. Selezionare il gruppo di sicurezza di rete che si vuole eliminare dall'elenco.
3. Selezionare **Elimina** e quindi scegliere **Sì**.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/remove-azurermnetworksecuritygroup) 

## <a name="work-with-security-rules"></a>Usare le regole di sicurezza

Un gruppo di sicurezza di rete contiene zero o più regole di sicurezza. È possibile creare una regola, [visualizzare tutte le regole di sicurezza](#view-all-security-rules), [visualizzare i dettagli di una regola](#view-details-of-a-security-rule), [modificare](#change-a-security-rule) ed [eliminare](#delete-a-security-rule) una regola di sicurezza.

### <a name="create-a-security-rule"></a>Creare una regola di sicurezza

È previsto un limite al numero di regole per gruppo di sicurezza di rete che è possibile creare per ogni sottoscrizione e località di Azure. Per informazioni dettagliate, vedere [Limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Nella casella di ricerca nella parte superiore del portale immettere *Gruppi di sicurezza di rete*. Selezionare la voce **Gruppi di sicurezza di rete** quando viene visualizzata nei risultati della ricerca.
2. Selezionare nell'elenco il gruppo di sicurezza di rete a cui si vuole aggiungere una regola di sicurezza.
3. Selezionare **Regole di sicurezza in ingresso** in **IMPOSTAZIONI**. Sono elencate le diverse regole esistenti. Alcune delle regole potrebbero non essere state aggiunte dall'utente. Quando si crea un gruppo di sicurezza di rete, al suo interno vengono create molte regole di sicurezza predefinite. Per altre informazioni, vedere [Regole di sicurezza predefinite](security-overview.md#default-security-rules).  Non è possibile eliminare le regole di sicurezza predefinite, ma è possibile eseguirne l'override con regole con una priorità più alta.
4. <a name = "security-rule-settings"></a>Selezionare **+ Aggiungi**.  Selezionare o aggiungere valori per le impostazioni seguenti e quindi scegliere **OK**:
    
    |Impostazione  |Valore  |Dettagli  |
    |---------|---------|---------|
    |Sorgente     | Selezionare **Qualsiasi**, **Gruppo di sicurezza dell'applicazione**, **Indirizzi IP**, oppure **Tag del servizio** per le regole di sicurezza in ingresso. Se si crea una regola di sicurezza in uscita, le opzioni sono le stesse elencate per **Destinazione**.       | Se si seleziona **Gruppo di sicurezza dell'applicazione**, selezionare quindi uno o più gruppi di sicurezza delle applicazioni presenti nella stessa area dell'interfaccia di rete. Come [creare un gruppo di sicurezza delle applicazioni](#create-an-application-security-group). Se si seleziona **Gruppo di sicurezza delle applicazioni** come **Origine** e **Destinazione**, le interfacce di rete all'interno di entrambi i gruppi di sicurezza delle applicazioni devono trovarsi nella stessa rete virtuale. Se si seleziona **Indirizzi IP**, specificare quindi **Indirizzi IP/Intervalli CIDR di origine**. È possibile specificare un valore singolo o un elenco delimitato da virgole di più valori. Un esempio di più valori è 10.0.0.0/16, 192.188.1.1. È previsto un limite al numero di valori che è possibile specificare. Vedere [Limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) per informazioni dettagliate. Se si seleziona **Tag del servizio**, selezionare quindi un tag del servizio. Un tag di servizio è un identificatore predefinito per una categoria di indirizzi IP. Per altre informazioni sui tag di servizio disponibili e su cosa ogni tag rappresenta, vedere [Tag di servizio](security-overview.md#service-tags). Se l'indirizzo IP specificato viene assegnato a una macchina virtuale di Azure, assicurarsi di specificare l'indirizzo IP privato e non l'indirizzo IP pubblico assegnato alla macchina virtuale. Le regole di sicurezza vengono elaborate dopo che Azure ha convertito l'indirizzo IP pubblico in un indirizzo IP privato per le regole di sicurezza in ingresso e prima che Azure trasli un indirizzo IP privato a un indirizzo IP pubblico per le regole in uscita. Per altre informazioni sugli indirizzi IP pubblici e privati in Azure, vedere [Tipi di indirizzo IP](virtual-network-ip-addresses-overview-arm.md).        |
    |Intervalli di porte di origine     | Specificare una porta singola, ad esempio 80, un intervallo di porte, ad esempio 1024-65535, o un elenco delimitato da virgole di porte singole e/o intervalli di porte, ad esempio 80, 1024-65535. Immettere un asterisco per consentire il traffico su qualsiasi porta. | Le porte e gli intervalli specificano il tipo di traffico delle porte consentito o non autorizzato dalla regola. È previsto un limite al numero di porte che è possibile specificare. Vedere [Limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) per informazioni dettagliate.  |
    |Destination     | Selezionare **Qualsiasi**, **Gruppo di sicurezza dell'applicazione**, **Indirizzi IP**, oppure **Rete Virtuale** per le regole di sicurezza in ingresso. Se si crea una regola di sicurezza in uscita, le opzioni sono le stesse elencate per **Origine**.        | Se si seleziona **Gruppo di sicurezza dell'applicazione**, è necessario selezionare quindi uno o più gruppi di sicurezza delle applicazioni presenti nella stessa area dell'interfaccia di rete. Come [creare un gruppo di sicurezza delle applicazioni](#create-an-application-security-group). Se si seleziona **Gruppo di sicurezza dell'applicazione**, selezionare quindi un gruppo di sicurezza delle applicazioni presente nella stessa area dell'interfaccia di rete. Se si seleziona **Indirizzi IP**, specificare quindi **Indirizzi IP/Intervalli CIDR di destinazione**. Analogamente a **Origine** e **Indirizzi IP/Intervalli CIDR di origine**, è possibile specificare un singolo indirizzo o più indirizzi o intervalli ed è previsto un limite al numero che è possibile specificare. Selezionando **Rete virtuale**, ovvero un tag di servizio, il traffico viene consentito a tutti gli indirizzi IP nello spazio indirizzi della rete virtuale. Se l'indirizzo IP specificato viene assegnato a una macchina virtuale di Azure, assicurarsi di specificare l'indirizzo IP privato e non l'indirizzo IP pubblico assegnato alla macchina virtuale. Le regole di sicurezza vengono elaborate dopo che Azure ha convertito l'indirizzo IP pubblico in un indirizzo IP privato per le regole di sicurezza in ingresso e prima che Azure trasli un indirizzo IP privato a un indirizzo IP pubblico per le regole in uscita. Per altre informazioni sugli indirizzi IP pubblici e privati in Azure, vedere [Tipi di indirizzo IP](virtual-network-ip-addresses-overview-arm.md).        |
    |Intervalli di porte di destinazione     | Specificare un valore singolo o un elenco di valori delimitato da virgole. | Analogamente a **Intervalli di porte di origine**, è possibile specificare una singola porta o più porte e intervalli ed è previsto un limite al numero che è possibile specificare. |
    |Protocollo     | Selezionare **Qualsiasi**, **TCP** o **UDP**.        |         |
    |Azione     | Selezionare **Consenti** o **Nega**.        |         |
    |Priorità     | Immettere un valore compreso tra 100 e 4096 univoco per tutte le regole di sicurezza all'interno del gruppo di sicurezza di rete. |Le regole vengono elaborate in ordine di priorità. Più è basso il numero, maggiore sarà la priorità. È consigliabile lasciare uno spazio tra i numeri di priorità durante la creazione delle regole, ad esempio 100, 200, 300. Lasciare uno spazio rende più semplice aggiungere regole in futuro con una priorità superiore o inferiore alle regole esistenti.         |
    |NOME     | Nome univoco per la regola all'interno del gruppo di sicurezza di rete.        |  Il nome può contenere fino a 80 caratteri. Deve iniziare con una lettera o un numero, terminare con una lettera, un numero o un carattere di sottolineatura e può contenere solo lettere, numeri, caratteri di sottolineatura, punti o trattini.       |
    |DESCRIZIONE     | Descrizione facoltativa.        |         |

**Comandi**

- Interfaccia della riga di comando di Azure: [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Visualizzare tutte le regole di sicurezza

Un gruppo di sicurezza di rete contiene zero o più regole. Per saperne di più sulle informazioni elencate quando vengono visualizzate le regole, vedere [Panoramica dei gruppi di sicurezza di rete](security-overview.md).

1. Nella casella di ricerca nella parte superiore del portale immettere *Gruppi di sicurezza di rete*. Selezionare la voce **Gruppi di sicurezza di rete** quando viene visualizzata nei risultati della ricerca.
2. Selezionare nell'elenco il gruppo di sicurezza di rete di cui si vogliono visualizzare le regole.
3. Selezionare **Regole di sicurezza in ingresso** o **Regole di sicurezza in uscita** in **IMPOSTAZIONI**.

L'elenco contiene tutte le regole create e il gruppo di sicurezza di rete [regole di sicurezza predefinite](security-overview.md#default-security-rules).

**Comandi**

- Interfaccia della riga di comando di Azure: [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Visualizzare i dettagli di una regola di sicurezza

1. Nella casella di ricerca nella parte superiore del portale immettere *Gruppi di sicurezza di rete*. Selezionare la voce **Gruppi di sicurezza di rete** quando viene visualizzata nei risultati della ricerca.
2. Selezionare il gruppo di sicurezza di rete per cui si vogliono visualizzare i dettagli della regola di sicurezza.
3. Selezionare **Regole di sicurezza in ingresso** o **Regole di sicurezza in uscita** in **IMPOSTAZIONI**.
4. Selezionare la regola di cui si vogliono visualizzare i dettagli. Per una spiegazione dettagliata di tutte le impostazioni, vedere le [impostazioni delle regole di sicurezza](#security-rule-settings).

**Comandi**

- Interfaccia della riga di comando di Azure: [az network nsg rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Modificare una regola di sicurezza

1. Completare i passaggi descritti in [Visualizzare i dettagli di una regola di sicurezza](#view-details-of-a-security-rule).
2. Modificare le impostazioni come desiderato e quindi selezionare **Salva**. Per una spiegazione dettagliata di tutte le impostazioni, vedere le [impostazioni delle regole di sicurezza](#security-rule-settings).

**Comandi**

- Interfaccia della riga di comando di Azure: [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [Set-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/set-azurermnetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Eliminare una regola di sicurezza

1. Completare i passaggi descritti in [Visualizzare i dettagli di una regola di sicurezza](#view-details-of-a-security-rule).
2. Selezionare **Elimina** e quindi scegliere **Sì**.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network nsg rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/remove-azurermnetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Usare i gruppi di sicurezza delle applicazioni

Un gruppo di sicurezza delle applicazioni contiene zero o più interfacce di rete. Per altre informazioni, vedere [Gruppi di sicurezza delle applicazioni](security-overview.md#application-security-groups). Tutte le interfacce di rete in un gruppo di sicurezza delle applicazioni devono trovarsi nella stessa rete virtuale. Per informazioni su come aggiungere un'interfaccia di rete a un gruppo di sicurezza delle applicazioni, vedere [Aggiungere un'interfaccia di rete a un gruppo di sicurezza delle applicazioni](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Creare un gruppo di sicurezza delle applicazioni

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Nella casella **Cerca nel Marketplace** immettere *Gruppo di sicurezza delle applicazioni*. Quando **Gruppo di sicurezza delle applicazioni** viene visualizzato nei risultati della ricerca, selezionarlo, selezionare ancora **Gruppo di sicurezza delle applicazioni** in **Tutto** e quindi selezionare **Crea**.
3. Immettere o selezionare le informazioni seguenti e quindi selezionare **Crea**:

    | Impostazione        | Valore                                                   |
    | ---            | ---                                                     |
    | NOME           | Il nome deve essere univoco all'interno di un gruppo di risorse.        |
    | Sottoscrizione   | Selezionare la propria sottoscrizione.                               |
    | Gruppo di risorse | Selezionare un gruppo di risorse esistente o crearne uno nuovo. |
    | Località       | Selezionare una località                                       |

**Comandi**

- Interfaccia della riga di comando di Azure: [az network asg create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Visualizzare tutti i gruppi di sicurezza delle applicazioni

1. Nell'angolo in alto a sinistra del portale di Azure selezionare **Tutti i servizi**.
2. Immettere *Gruppi di sicurezza delle applicazioni* nella casella **Filtro tutti i servizi** e quindi selezionare **Gruppi di sicurezza delle applicazioni** quando compare nei risultati di ricerca.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network asg list](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Visualizzare i dettagli di un gruppo di sicurezza delle applicazioni specifico

1. Nell'angolo in alto a sinistra del portale di Azure selezionare **Tutti i servizi**.
2. Immettere *Gruppi di sicurezza delle applicazioni* nella casella **Filtro tutti i servizi** e quindi selezionare **Gruppi di sicurezza delle applicazioni** quando compare nei risultati di ricerca.
3. Selezionare il gruppo di sicurezza dell'applicazione di cui si desidera visualizzare i dettagli.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network asg show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Modificare un gruppo di sicurezza delle applicazioni

1. Nell'angolo in alto a sinistra del portale di Azure selezionare **Tutti i servizi**.
2. Immettere *Gruppi di sicurezza delle applicazioni* nella casella **Filtro tutti i servizi** e quindi selezionare **Gruppi di sicurezza delle applicazioni** quando compare nei risultati di ricerca.
3. Selezionare il gruppo di sicurezza dell'applicazione di cui si desidera modificare le impostazioni. È possibile aggiungere o rimuovere i tag, o assegnare o rimuovere le autorizzazioni al gruppo di sicurezza dell'applicazione.

- Interfaccia della riga di comando di Azure: [az network asg update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: Nessun cmdlet di PowerShell.

### <a name="delete-an-application-security-group"></a>Eliminare un gruppo di sicurezza delle applicazioni

Non è possibile eliminare un gruppo di sicurezza delle applicazioni se contiene interfacce di rete. Rimuovere tutte le interfacce di rete dal gruppo di sicurezza delle applicazioni modificando le impostazioni delle interfacce di rete o eliminando le interfacce di rete stesse. Per maggiori dettagli, vedere [Aggiungere o rimuovere un'interfaccia di rete da gruppi di sicurezza delle applicazioni](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) oppure [Eliminare un'interfaccia di rete](virtual-network-network-interface.md#delete-a-network-interface).

1. Nell'angolo in alto a sinistra del portale di Azure selezionare **Tutti i servizi**.
2. Immettere *Gruppi di sicurezza delle applicazioni* nella casella **Filtro tutti i servizi** e quindi selezionare **Gruppi di sicurezza delle applicazioni** quando compare nei risultati di ricerca.
3. Selezionare il gruppo di sicurezza dell'applicazione che si desidera eliminare.
4. Selezionare **Elimina**, quindi selezionare **Sì** per eliminare il gruppo di sicurezza dell'applicazione.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network asg delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/remove-azurermapplicationsecuritygroup)

## <a name="permissions"></a>Autorizzazioni

Per eseguire attività in gruppi di sicurezza di rete, regole di sicurezza e gruppi di sicurezza delle applicazioni, l'account deve essere assegnato al ruolo [Collaboratore Rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un ruolo [personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le autorizzazioni appropriate elencate nella tabella seguente:

### <a name="network-security-group"></a>Gruppo di sicurezza di rete

| Azione                                                        |   NOME                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Ottenere un gruppo di sicurezza di rete                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Creare o aggiornare un gruppo di sicurezza di rete                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Eliminare un gruppo di sicurezza di rete                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Assegnare un gruppo di sicurezza di rete a una subnet o un'interfaccia di rete 


### <a name="network-security-group-rule"></a>Regola del gruppo di sicurezza di rete

| Azione                                                        |   NOME                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Ottenere una regola                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Creare o aggiornare una regola                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Eliminare una regola                                                         |

### <a name="application-security-group"></a>Gruppo di sicurezza dell'applicazione

| Azione                                                                     | NOME                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Aggiungere una configurazione IP a gruppi di sicurezza dell'applicazione|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Aggiungere una regola di sicurezza a un gruppo di sicurezza dell'applicazione    |
| Microsoft.Network/applicationSecurityGroups/read                           | Ottenere un gruppo di sicurezza dell'applicazione                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Creare o aggiornare un gruppo di sicurezza delle applicazioni           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Eliminare un gruppo di sicurezza delle applicazioni                     |

## <a name="next-steps"></a>Passaggi successivi

- Creare un gruppo di sicurezza di rete o dell'applicazione usando gli script di esempio di [PowerShell](powershell-samples.md) o dell'[interfaccia della riga di comando di Azure](cli-samples.md) oppure i [modelli di Azure Resource Manager](template-samples.md)
- Creare e applicare i [criteri di Azure](policy-samples.md) per le reti virtuali
