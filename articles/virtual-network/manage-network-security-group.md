---
title: Creare, modificare o eliminare un gruppo di sicurezza di rete di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su come creare, modificare o eliminare un gruppo di sicurezza di rete.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 9ed4ce6befda76069e965501a320dc110129a024
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521037"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Creare, modificare o eliminare un gruppo di sicurezza di rete

Le regole di sicurezza dei gruppi di sicurezza di rete consentono di filtrare il tipo di traffico di rete consentito in ingresso e in uscita dalle subnet della rete virtuale e dalle interfacce di rete. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Panoramica dei gruppi di sicurezza di rete](security-overview.md). Successivamente, completare l'esercitazione [filtrare il traffico di rete](tutorial-filter-network-traffic.md) per acquisire esperienza con i gruppi di sicurezza di rete.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se non si ha una sottoscrizione, configurare un account Azure con una sottoscrizione attiva. [Creare un account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Prima di iniziare la parte restante di questo articolo, completare una di queste attività:

- **Utenti del portale**: accedere al [portale di Azure](https://portal.azure.com) con l'account Azure.

- **Utenti di PowerShell**: è possibile eseguire i comandi nel [Azure cloud Shell](https://shell.azure.com/powershell)o eseguire PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Nella scheda Azure Cloud Shell browser trovare l'elenco a discesa **Seleziona ambiente** , quindi scegliere **PowerShell** se non è già selezionato.

    Se si esegue PowerShell localmente, usare Azure PowerShell modulo 1.0.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az.Network` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Eseguire `Connect-AzAccount` per creare una connessione con Azure.

- **Utenti dell'interfaccia della riga di comando di Azure**: è possibile eseguire i comandi nel [Azure cloud Shell](https://shell.azure.com/bash)o eseguire l'interfaccia della riga di comando dal computer. Usare l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva se si esegue l'interfaccia della riga di comando di Azure Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Eseguire `az login` per creare una connessione con Azure.

L'account a cui si accede o che si connette ad Azure deve essere assegnato al [ruolo Collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le azioni appropriate elencate in [autorizzazioni](#permissions).

## <a name="work-with-network-security-groups"></a>Usare i gruppi di sicurezza di rete

È possibile creare un gruppo, [visualizzare tutti i gruppi di sicurezza di rete](#view-all-network-security-groups), [visualizzare i dettagli di un gruppo](#view-details-of-a-network-security-group), [modificare](#change-a-network-security-group) ed [eliminare](#delete-a-network-security-group) un gruppo di sicurezza di rete. È anche possibile [associare o annullare l'associazione](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) di un gruppo di sicurezza di rete da un'interfaccia di rete o da una subnet.

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

È previsto un limite al numero di gruppi di sicurezza di rete che è possibile creare per ogni sottoscrizione e località di Azure. Per altre informazioni, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Nel menu del [portale di Azure](https://portal.azure.com) o dalla pagina **Home** selezionare **Crea una risorsa**.

2. Selezionare **rete**e quindi selezionare **gruppo di sicurezza di rete**.

3. Nella pagina **Crea gruppo di sicurezza di rete** , nella scheda **nozioni di base** , impostare i valori per le impostazioni seguenti:

    | Impostazione | Azione |
    | --- | --- |
    | **Sottoscrizione** | Scegliere la propria sottoscrizione. |
    | **Gruppo di risorse** | Scegliere un gruppo di risorse esistente oppure selezionare **Crea nuovo** per creare un nuovo gruppo di risorse. |
    | **Nome** | Immettere una stringa di testo univoca all'interno di un gruppo di risorse. |
    | **Area** | Scegliere il percorso desiderato. |

4. Selezionare **Rivedi e crea**.

5. Dopo aver visualizzato il messaggio **convalida superata** , selezionare **Crea**.

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Visualizzare tutti i gruppi di sicurezza di rete

Passare alla [portale di Azure](https://portal.azure.com) per visualizzare i gruppi di sicurezza di rete. Cercare e selezionare **gruppi di sicurezza di rete**. Viene visualizzato l'elenco dei gruppi di sicurezza di rete per la sottoscrizione.

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network NSG List](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Visualizzare i dettagli di un gruppo di sicurezza di rete

1. Passare alla [portale di Azure](https://portal.azure.com) per visualizzare i gruppi di sicurezza di rete. Cercare e selezionare **gruppi di sicurezza di rete**.

2. Selezionare il nome del gruppo di sicurezza di rete.

Nella barra dei menu del gruppo di sicurezza di rete, in **Impostazioni**, è possibile visualizzare le **regole di sicurezza in ingresso**, **le regole di sicurezza**in uscita, le interfacce di **rete**e le **subnet** a cui è associato il gruppo di sicurezza di rete.

In **monitoraggio**è possibile abilitare o disabilitare **le impostazioni di diagnostica**. In **supporto e risoluzione dei problemi**è possibile visualizzare **le regole di sicurezza valide**. Per altre informazioni, vedere [registrazione diagnostica per un gruppo di sicurezza di rete](virtual-network-nsg-manage-log.md) e [diagnosticare un problema di filtro del traffico di rete della macchina virtuale](diagnose-network-traffic-filter-problem.md).

Per altre informazioni sulle impostazioni comuni di Azure elencate, vedere gli articoli seguenti:

- [Log attività](../azure-monitor/platform/platform-logs-overview.md)
- [Controllo di accesso (IAM)](../role-based-access-control/overview.md)
- [Tag](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Locks](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Script di automazione](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network NSG Show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Modificare un gruppo di sicurezza di rete

1. Passare alla [portale di Azure](https://portal.azure.com) per visualizzare i gruppi di sicurezza di rete. Cercare e selezionare **gruppi di sicurezza di rete**.

2. Selezionare il nome del gruppo di sicurezza di rete che si desidera modificare.

Le modifiche più comuni sono l' [aggiunta di una regola di sicurezza](#create-a-security-rule), la [rimozione di una regola](#delete-a-security-rule)e l' [associazione o l'associazione di un gruppo di sicurezza di rete a o da una subnet o un'interfaccia di rete](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network NSG Update](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Associare o annullare l'associazione di un gruppo di sicurezza di rete da una subnet o un'interfaccia di rete

Per associare o annullare l'associazione di un gruppo di sicurezza di rete da un'interfaccia di rete, vedere [Associare o annullare l'associazione di un gruppo di sicurezza di rete da un'interfaccia di rete](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Per associare o annullare l'associazione di un gruppo di sicurezza di rete da una subnet, vedere [Cambiare le impostazioni della subnet](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Eliminare un gruppo di sicurezza di rete

Se un gruppo di sicurezza di rete è associato ad alcuna subnet o interfaccia di rete, non può essere eliminato. Annullare l'associazione di un gruppo di sicurezza di rete da tutte le subnet e le interfacce di rete prima di tentare di eliminarlo.

1. Passare alla [portale di Azure](https://portal.azure.com) per visualizzare i gruppi di sicurezza di rete. Cercare e selezionare **gruppi di sicurezza di rete**.

2. Selezionare il nome del gruppo di sicurezza di rete che si desidera eliminare.

3. Nella barra degli strumenti del gruppo di sicurezza di rete selezionare **Elimina**. Quindi, selezionare **Sì** nella finestra di dialogo di conferma.

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network NSG Delete](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Usare le regole di sicurezza

Un gruppo di sicurezza di rete contiene zero o più regole di sicurezza. È possibile creare una regola, [visualizzare tutte le regole di sicurezza](#view-all-security-rules), [visualizzare i dettagli di una regola](#view-details-of-a-security-rule), [modificare](#change-a-security-rule) ed [eliminare](#delete-a-security-rule) una regola di sicurezza.

### <a name="create-a-security-rule"></a>Creare una regola di sicurezza

Esiste un limite al numero di regole per gruppo di sicurezza di rete che è possibile creare per ogni sottoscrizione e località di Azure. Per altre informazioni, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Passare alla [portale di Azure](https://portal.azure.com) per visualizzare i gruppi di sicurezza di rete. Cercare e selezionare **gruppi di sicurezza di rete**.

2. Selezionare il nome del gruppo di sicurezza di rete a cui si vuole aggiungere una regola di sicurezza.

3. Nella barra dei menu del gruppo di sicurezza di rete scegliere **regole di sicurezza in ingresso** o regole di **sicurezza**in uscita.

    Sono elencate numerose regole esistenti, incluse alcune potrebbero non essere state aggiunte. Quando si crea un gruppo di sicurezza di rete, vengono create diverse regole di sicurezza predefinite. Per altre informazioni, vedere [Regole di sicurezza predefinite](security-overview.md#default-security-rules).  Non è possibile eliminare le regole di sicurezza predefinite, ma è possibile eseguirne l'override con regole con una priorità più alta.

4. <a name="security-rule-settings"></a>Selezionare **Aggiungi**. Selezionare o aggiungere i valori per le impostazioni seguenti e quindi selezionare **OK**:

    | Impostazione | Valore | Dettagli |
    | ------- | ----- | ------- |
    | **origine** | Uno dei valori possibili:<ul><li>**qualsiasi**</li><li>**Indirizzi IP**</li><li>**Tag servizio** (regola di sicurezza in ingresso) o **virtualnetwork** (regola di sicurezza in uscita)</li><li>**Gruppo&nbsp;di&nbsp;sicurezza delle applicazioni**</li></ul> | <p>Se si scelgono **indirizzi IP**, è necessario specificare anche **indirizzi IP/intervalli CIDR di origine**.</p><p>Se si sceglie il **tag del servizio**, è anche possibile selezionare un tag del servizio di **origine**.</p><p>Se si sceglie **gruppo di sicurezza delle applicazioni**, è necessario selezionare anche un gruppo di sicurezza delle applicazioni esistente. Se si sceglie il **gruppo di sicurezza delle applicazioni** sia per l' **origine** che per la **destinazione**, le interfacce di rete all'interno di entrambi i gruppi di sicurezza delle applicazioni devono trovarsi nella stessa rete virtuale.</p> |
    | **Indirizzi IP/intervalli CIDR di origine** | Elenco delimitato da virgole di indirizzi IP e intervalli di routing interdominio (CIDR) con classi | <p>Questa impostazione viene visualizzata se si modifica l' **origine** in **indirizzi IP**. È necessario specificare un singolo valore o un elenco delimitato da virgole di più valori. Un esempio di più valori è `10.0.0.0/16, 192.188.1.1`. È previsto un limite al numero di valori che è possibile specificare. Per informazioni dettagliate, vedere [limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Se l'indirizzo IP specificato viene assegnato a una macchina virtuale di Azure, specificare l'indirizzo IP privato e non l'indirizzo IP pubblico. Azure elabora le regole di sicurezza dopo aver convertito l'indirizzo IP pubblico in un indirizzo IP privato per le regole di sicurezza in ingresso, ma prima di convertire un indirizzo IP privato in un indirizzo IP pubblico per le regole in uscita. Per altre informazioni sugli indirizzi IP pubblici e privati in Azure, vedere [Tipi di indirizzo IP](virtual-network-ip-addresses-overview-arm.md).</p> |
    | **Tag del servizio di origine** | Un tag di servizio nell'elenco a discesa | Questa impostazione facoltativa viene visualizzata se si imposta il tag **source** to **Service** per una regola di sicurezza in ingresso. Un tag di servizio è un identificatore predefinito per una categoria di indirizzi IP. Per altre informazioni sui tag di servizio disponibili e su ciò che ogni tag rappresenta, vedere [tag di servizio](security-overview.md#service-tags). |
    | **Gruppo di sicurezza dell'applicazione di origine** | Un gruppo di sicurezza delle applicazioni esistente | Questa impostazione viene visualizzata se si imposta l' **origine** sul **gruppo di sicurezza dell'applicazione**. Selezionare un gruppo di sicurezza delle applicazioni presente nella stessa area dell'interfaccia di rete. Come [creare un gruppo di sicurezza delle applicazioni](#create-an-application-security-group). |
    | **Intervalli di porte di origine** | Uno dei valori possibili:<ul><li>Una singola porta, ad esempio`80`</li><li>Un intervallo di porte, ad esempio`1024-65535`</li><li>Elenco delimitato da virgole di porte singole e/o intervalli di porte, ad esempio`80, 1024-65535`</li><li>Un asterisco (`*`) per consentire il traffico su qualsiasi porta</li></ul> | Questa impostazione specifica le porte sulle quali la regola consente o nega il traffico. È previsto un limite al numero di porte che è possibile specificare. Per informazioni dettagliate, vedere [limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Destination** | Uno dei valori possibili:<ul><li>**qualsiasi**</li><li>**Indirizzi IP**</li><li>**Tag servizio** (regola di sicurezza in uscita) o **virtualnetwork** (regola di sicurezza in ingresso)</li><li>**Gruppo&nbsp;di&nbsp;sicurezza delle applicazioni**</li></ul> | <p>Se si scelgono **gli indirizzi IP**, specificare anche **indirizzi IP/intervalli CIDR di destinazione**.</p><p>Se si sceglie **virtualnetwork**, il traffico è consentito a tutti gli indirizzi IP nello spazio degli indirizzi della rete virtuale. **Virtualnetwork** è un tag del servizio.</p><p>Se si seleziona **gruppo di sicurezza delle applicazioni**, è necessario selezionare un gruppo di sicurezza delle applicazioni esistente. Come [creare un gruppo di sicurezza delle applicazioni](#create-an-application-security-group).</p> |
    | **Indirizzi IP/intervalli CIDR di destinazione** | Elenco delimitato da virgole di indirizzi IP e intervalli CIDR | <p>Questa impostazione viene visualizzata se si modifica la **destinazione** in **indirizzi IP**. Analogamente agli indirizzi IP **di origine e** di **origine/intervalli CIDR**, è possibile specificare uno o più indirizzi o intervalli. Sono previsti limiti al numero che è possibile specificare. Per informazioni dettagliate, vedere [limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Se l'indirizzo IP specificato viene assegnato a una macchina virtuale di Azure, assicurarsi di specificare il proprio indirizzo IP privato, non l'indirizzo IP pubblico. Azure elabora le regole di sicurezza dopo aver convertito l'indirizzo IP pubblico in un indirizzo IP privato per le regole di sicurezza in ingresso, ma prima che Azure converta un indirizzo IP privato in un indirizzo IP pubblico per le regole in uscita. Per altre informazioni sugli indirizzi IP pubblici e privati in Azure, vedere [Tipi di indirizzo IP](virtual-network-ip-addresses-overview-arm.md).</p> |
    | **Tag del servizio di destinazione** | Un tag di servizio nell'elenco a discesa | Questa impostazione facoltativa viene visualizzata se si cambia la **destinazione** in **tag di servizio** per una regola di sicurezza in uscita. Un tag di servizio è un identificatore predefinito per una categoria di indirizzi IP. Per altre informazioni sui tag di servizio disponibili e su ciò che ogni tag rappresenta, vedere [tag di servizio](security-overview.md#service-tags). |
    | **Gruppo di sicurezza dell'applicazione di destinazione** | Un gruppo di sicurezza delle applicazioni esistente | Questa impostazione viene visualizzata se si imposta la **destinazione** sul **gruppo di sicurezza dell'applicazione**. Selezionare un gruppo di sicurezza delle applicazioni presente nella stessa area dell'interfaccia di rete. Come [creare un gruppo di sicurezza delle applicazioni](#create-an-application-security-group). |
    | **Intervalli di porte di destinazione** | Uno dei valori possibili:<ul><li>Una singola porta, ad esempio`80`</li><li>Un intervallo di porte, ad esempio`1024-65535`</li><li>Elenco delimitato da virgole di porte singole e/o intervalli di porte, ad esempio`80, 1024-65535`</li><li>Un asterisco (`*`) per consentire il traffico su qualsiasi porta</li></ul> | Come per gli **intervalli di porte di origine**, è possibile specificare una o più porte e intervalli. Sono previsti limiti al numero che è possibile specificare. Per informazioni dettagliate, vedere [limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Protocollo** | **Any**, **TCP**, **UDP**o **ICMP** | È possibile limitare la regola alla Transmission Control Protocol (TCP), al protocollo UDP (User Datagram Protocol) o Internet Control Message Protocol (ICMP). Per impostazione predefinita, la regola viene applicata a tutti i protocolli. |
    | **Azione** | **Consenti** o **Nega** | Questa impostazione specifica se questa regola consente o nega l'accesso per la configurazione di origine e destinazione fornita. |
    | **Priority** | Valore compreso tra 100 e 4096 univoco per tutte le regole di sicurezza all'interno del gruppo di sicurezza di rete | Azure elabora le regole di sicurezza in ordine di priorità. Più è basso il numero, maggiore sarà la priorità. Si consiglia di lasciare uno spazio tra i numeri di priorità quando si creano regole, ad esempio 100, 200 e 300. Lasciando Gap rende più semplice l'aggiunta di regole in futuro, in modo da poter assegnare loro una priorità più alta o più bassa rispetto alle regole esistenti. |
    | **Nome** | Un nome univoco per la regola all'interno del gruppo di sicurezza di rete | Il nome può contenere fino a 80 caratteri. Deve iniziare con una lettera o un numero e deve terminare con una lettera, un numero o un carattere di sottolineatura. Il nome può contenere solo lettere, numeri, caratteri di sottolineatura, punti o trattini. |
    | **Descrizione** | Una descrizione di testo | Facoltativamente, è possibile specificare una descrizione di testo per la regola di sicurezza. |

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Visualizzare tutte le regole di sicurezza

Un gruppo di sicurezza di rete contiene zero o più regole. Per saperne di più sulle informazioni elencate quando vengono visualizzate le regole, vedere [Panoramica dei gruppi di sicurezza di rete](security-overview.md).

1. Passare alla [portale di Azure](https://portal.azure.com) per visualizzare le regole di un gruppo di sicurezza di rete. Cercare e selezionare **gruppi di sicurezza di rete**.

2. Selezionare il nome del gruppo di sicurezza di rete per cui si desidera visualizzare le regole.

3. Nella barra dei menu del gruppo di sicurezza di rete scegliere **regole di sicurezza in ingresso** o regole di **sicurezza**in uscita.

L'elenco contiene le regole create e le [regole di sicurezza predefinite](security-overview.md#default-security-rules)del gruppo di sicurezza di rete.

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Visualizzare i dettagli di una regola di sicurezza

1. Passare alla [portale di Azure](https://portal.azure.com) per visualizzare le regole di un gruppo di sicurezza di rete. Cercare e selezionare **gruppi di sicurezza di rete**.

2. Selezionare il nome del gruppo di sicurezza di rete per cui si desidera visualizzare i dettagli di una regola.

3. Nella barra dei menu del gruppo di sicurezza di rete scegliere **regole di sicurezza in ingresso** o regole di **sicurezza**in uscita.

4. Selezionare la regola di cui si vogliono visualizzare i dettagli. Per una spiegazione di tutte le impostazioni, vedere [impostazioni delle regole di sicurezza](#security-rule-settings).

    > [!NOTE]
    > Questa procedura si applica solo a una regola di sicurezza personalizzata. Non funziona se si sceglie una regola di sicurezza predefinita.

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network NSG Rule Show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Modificare una regola di sicurezza

1. Completare i passaggi descritti in [Visualizzare i dettagli di una regola di sicurezza](#view-details-of-a-security-rule).

2. Modificare le impostazioni in base alle esigenze e quindi selezionare **Salva**. Per una spiegazione di tutte le impostazioni, vedere [impostazioni delle regole di sicurezza](#security-rule-settings).

    > [!NOTE]
    > Questa procedura si applica solo a una regola di sicurezza personalizzata. Non è consentito modificare una regola di sicurezza predefinita.

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Eliminare una regola di sicurezza

1. Completare i passaggi descritti in [Visualizzare i dettagli di una regola di sicurezza](#view-details-of-a-security-rule).

2. Selezionare **Elimina** e quindi scegliere **Sì**.

    > [!NOTE]
    > Questa procedura si applica solo a una regola di sicurezza personalizzata. Non è possibile eliminare una regola di sicurezza predefinita.

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network NSG Rule Delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Usare i gruppi di sicurezza delle applicazioni

Un gruppo di sicurezza delle applicazioni contiene zero o più interfacce di rete. Per altre informazioni, vedere [Gruppi di sicurezza delle applicazioni](security-overview.md#application-security-groups). Tutte le interfacce di rete in un gruppo di sicurezza delle applicazioni devono trovarsi nella stessa rete virtuale. Per informazioni su come aggiungere un'interfaccia di rete a un gruppo di sicurezza delle applicazioni, vedere [Aggiungere un'interfaccia di rete a un gruppo di sicurezza delle applicazioni](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Creare un gruppo di sicurezza delle applicazioni

1. Nel menu del [portale di Azure](https://portal.azure.com) o dalla pagina **Home** selezionare **Crea una risorsa**.

2. Nella casella di ricerca immettere *gruppo di sicurezza dell'applicazione*.

3. Nella pagina **gruppo di sicurezza delle applicazioni** selezionare **Crea**.

4. Nella pagina **Crea un gruppo di sicurezza delle applicazioni** , nella scheda **nozioni di base** , impostare i valori per le impostazioni seguenti:

    | Impostazione | Azione |
    | --- | --- |
    | **Sottoscrizione** | Scegliere la propria sottoscrizione. |
    | **Gruppo di risorse** | Scegliere un gruppo di risorse esistente oppure selezionare **Crea nuovo** per creare un nuovo gruppo di risorse. |
    | **Nome** | Immettere una stringa di testo univoca all'interno di un gruppo di risorse. |
    | **Area** | Scegliere il percorso desiderato. |

5. Selezionare **Rivedi e crea**.

6. Nella scheda **Verifica e crea** , dopo aver visualizzato il messaggio **convalida superata** , selezionare **Crea**.

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network ASG create](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Visualizzare tutti i gruppi di sicurezza delle applicazioni

Passare alla [portale di Azure](https://portal.azure.com) per visualizzare i gruppi di sicurezza delle applicazioni. Cercare e selezionare **gruppi di sicurezza delle applicazioni**. Il portale di Azure Visualizza un elenco dei gruppi di sicurezza dell'applicazione.

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network ASG List](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Visualizzare i dettagli di un gruppo di sicurezza delle applicazioni specifico

1. Passare alla [portale di Azure](https://portal.azure.com) per visualizzare un gruppo di sicurezza dell'applicazione. Cercare e selezionare **gruppi di sicurezza delle applicazioni**.

2. Selezionare il nome del gruppo di sicurezza delle applicazioni per cui si desidera visualizzare i dettagli.

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network ASG Show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Modificare un gruppo di sicurezza delle applicazioni

1. Passare alla [portale di Azure](https://portal.azure.com) per visualizzare un gruppo di sicurezza dell'applicazione. Cercare e selezionare **gruppi di sicurezza delle applicazioni**.

2. Selezionare il nome del gruppo di sicurezza delle applicazioni che si desidera modificare.

3. Selezionare **modifica** accanto all'impostazione che si desidera modificare. Ad esempio, è possibile aggiungere o rimuovere **tag**oppure modificare il **gruppo di risorse** o la **sottoscrizione**.

    > [!NOTE]
    > Non è possibile modificare il percorso.

    Nella barra dei menu è anche possibile selezionare **controllo di accesso (IAM)**. Nella pagina **controllo di accesso (IAM)** è possibile assegnare o rimuovere le autorizzazioni per il gruppo di sicurezza dell'applicazione.

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network ASG Update](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Nessun cmdlet di PowerShell |

### <a name="delete-an-application-security-group"></a>Eliminare un gruppo di sicurezza delle applicazioni

Non è possibile eliminare un gruppo di sicurezza dell'applicazione se contiene interfacce di rete. Per rimuovere tutte le interfacce di rete dal gruppo di sicurezza delle applicazioni, modificare le impostazioni dell'interfaccia di rete o eliminare le interfacce di rete. Per altre informazioni, vedere [aggiungere o rimuovere da gruppi di sicurezza delle applicazioni](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) o [eliminare un'interfaccia di rete](virtual-network-network-interface.md#delete-a-network-interface).

1. Passare alla [portale di Azure](https://portal.azure.com) per gestire i gruppi di sicurezza delle applicazioni. Cercare e selezionare **gruppi di sicurezza delle applicazioni**.

2. Selezionare il nome del gruppo di sicurezza dell'applicazione che si desidera eliminare.

3. Selezionare **Elimina**, quindi selezionare **Sì** per eliminare il gruppo di sicurezza dell'applicazione.

#### <a name="commands"></a>Comandi:

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [AZ Network ASG Delete](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Autorizzazioni

Per eseguire attività sui gruppi di sicurezza di rete, le regole di sicurezza e i gruppi di sicurezza delle applicazioni, l'account deve essere assegnato al ruolo [collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le autorizzazioni appropriate, come indicato nelle tabelle seguenti:

### <a name="network-security-group"></a>Gruppo di sicurezza di rete

| Azione                                                        |   Nome                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Ottenere un gruppo di sicurezza di rete                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Creare o aggiornare un gruppo di sicurezza di rete                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Eliminare un gruppo di sicurezza di rete                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Assegnare un gruppo di sicurezza di rete a una subnet o un'interfaccia di rete 

### <a name="network-security-group-rule"></a>Regola del gruppo di sicurezza di rete

| Azione                                                        |   Nome                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Ottenere una regola                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Creare o aggiornare una regola                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Eliminare una regola                                                         |

### <a name="application-security-group"></a>Gruppo di sicurezza dell'applicazione

| Azione                                                                     | Nome                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Aggiungere una configurazione IP a gruppi di sicurezza dell'applicazione|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Aggiungere una regola di sicurezza a un gruppo di sicurezza dell'applicazione    |
| Microsoft.Network/applicationSecurityGroups/read                           | Ottenere un gruppo di sicurezza dell'applicazione                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Creare o aggiornare un gruppo di sicurezza delle applicazioni           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Eliminare un gruppo di sicurezza delle applicazioni                     |

## <a name="next-steps"></a>Passaggi successivi

- Creare un gruppo di sicurezza di rete o di applicazioni usando gli script di esempio di [PowerShell](powershell-samples.md) o dell'interfaccia della riga [Gestione risorse](template-samples.md) di comando di [Azure](cli-samples.md)
- Creare e applicare i [criteri di Azure](policy-samples.md) per le reti virtuali
