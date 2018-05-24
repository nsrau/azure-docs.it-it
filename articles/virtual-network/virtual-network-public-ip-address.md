---
title: Creare, modificare o eliminare un indirizzo IP pubblico di Azure | Microsoft Docs
description: Informazioni su come creare, modificare o eliminare un indirizzo IP pubblico.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 8c052b45a0db42e2220c052b03f53f538de107ab
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895047"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Creare, modificare o eliminare un indirizzo IP pubblico

Informazioni su un indirizzo IP pubblico e su come crearlo, modificarlo ed eliminarlo. Un indirizzo IP pubblico è una risorsa con impostazioni proprie configurabili. L'assegnazione di un indirizzo IP pubblico ad altre risorse di Azure abilita le funzionalità seguenti:
- Connettività Internet in ingresso a risorse quali macchine virtuali di Azure, set di scalabilità di macchine virtuali di Azure, gateway VPN di Azure, gateway applicazione e servizi Azure Load Balancer con connessione Internet. Le risorse di Azure non possono ricevere comunicazioni in ingresso da Internet senza un indirizzo IP pubblico assegnato. Mentre alcune risorse di Azure sono intrinsecamente accessibili tramite indirizzi IP pubblici, altre devono avere indirizzi IP pubblici assegnati perché siano accessibili da Internet.
- Connettività in uscita verso Internet tramite un indirizzo IP prevedibile. Una macchina virtuale può ad esempio comunicare in uscita con Internet senza avere un indirizzo IP pubblico assegnato: il relativo indirizzo sarà l'indirizzo di rete convertito da Azure in un indirizzo pubblico non prevedibile. L'assegnazione di un indirizzo IP pubblico a una risorsa consente di conoscere l'indirizzo IP usato per la connessione in uscita. Anche se prevedibile, l'indirizzo può essere modificato, a seconda del metodo di assegnazione scelto. Per altre informazioni, vedere [Creare un indirizzo IP pubblico](#create-a-public-ip-address). Per altre informazioni sulle connessioni in uscita dalle risorse di Azure, leggere l'articolo [Informazioni sulle connessioni in uscita](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Prima di iniziare

Prima di completare i passaggi di qualsiasi sezione di questo articolo, eseguire le attività seguenti:

- Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usa il portale, aprire https://portal.azure.com e accedere con l'account Azure.
- Se si usano i comandi di PowerShell per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/powershell) o tramite PowerShell dal computer in uso. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Questa esercitazione richiede il modulo Azure PowerShell 5.7.0 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.
- Se si usano i comandi dell'interfaccia della riga di comando di Azure per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/bash) o tramite l'interfaccia della riga di comando dal computer in uso. Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.31 o versioni successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

L'account con cui si accede o con cui ci si collega ad Azure deve essere assegnato al ruolo [collaboratore di rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le operazioni appropriate elencate nelle [Autorizzazioni](#permissions).

Per gli indirizzi IP pubblici è previsto un addebito nominale. Per visualizzare i prezzi, consultare la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses). 

## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

1. Nell'angolo superiore sinistro del portale selezionare **+ Crea una risorsa**.
2. Immettere *indirizzo ip pubblico* nella casella *Cerca nel Marketplace*. Selezionare la voce **Indirizzo IP pubblico** visualizzata nei risultati della ricerca.
3. In **Indirizzo IP pubblico**, selezionare **Crea**.
4. In **Crea indirizzi IP pubblici** immettere o selezionare i valori per le impostazioni seguenti, quindi selezionare **Crea**:

    |Impostazione|Obbligatorio?|Dettagli|
    |---|---|---|
    |NOME|Sì|Il nome deve essere univoco all'interno del gruppo di risorse selezionato.|
    |SKU|Sì|Tutti gli indirizzi IP pubblici creati prima dell'introduzione degli SKU sono indirizzi IP pubblici con SKU **Basic**.  Dopo aver creato l'indirizzo IP pubblico, non è possibile modificare lo SKU. Una macchina virtuale autonoma, le macchine virtuali all'interno di un set di disponibilità o i set di scalabilità di macchine virtuali possono usare SKU Basic o Standard.  Non è consentito combinare SKU tra macchine virtuali all'interno di set di disponibilità o di set di scalabilità. SKU **Basic**: se si sta creando un indirizzo IP pubblico in un'area che supporta zone di disponibilità, l'opzione **Zona di disponibilità** è impostata su *Nessuna* per impostazione predefinita. È possibile scegliere di selezionare una zona di disponibilità per garantire una zona specifica per l'indirizzo IP pubblico. SKU **Standard**: un indirizzo IP pubblico con SKU Standard può essere associato a una macchina virtuale o a un front-end di bilanciamento del carico. Se si sta creando un indirizzo IP pubblico in un'area che supporta zone di disponibilità, l'opzione **Zona di disponibilità** è impostata su *Con ridondanza della zona* per impostazione predefinita. Per altre informazioni sulle zone di disponibilità, vedere l'impostazione **Zona di disponibilità**. Lo SKU Standard è necessario se si associa l'indirizzo a un bilanciamento del carico Standard. Per saperne di più sui servizi di bilanciamento del carico standard, vedere [Azure load balancer standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (SKU Standard per il bilanciamento del carico di Azure). Quando si assegna un indirizzo IP pubblico con SKU Standard all'interfaccia di rete di una macchina virtuale, è necessario consentire in modo esplicito il traffico previsto con un [gruppo di sicurezza di rete](security-overview.md#network-security-groups). La comunicazione con la risorsa non riesce finché non si crea e si associa un gruppo di sicurezza di rete e si consente in modo esplicito il traffico desiderato.|
    |Versione indirizzo IP|Sì| Selezionare IPv4 o IPv6. Mentre gli indirizzi IPv4 pubblici possono essere assegnati a diverse risorse di Azure, un indirizzo IP pubblico IPv6 può essere assegnato solo a un servizio di bilanciamento del carico con connessione Internet. Il servizio di bilanciamento del carico può bilanciare il carico del traffico IPv6 verso le macchine virtuali di Azure. Altre informazioni sul [bilanciamento del carico del traffico IPv6 verso le macchine virtuali](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se è stato selezionato lo **SKU Standard**, non è possibile selezionare *IPv6*. È possibile creare un indirizzo IPv4 solo quando si usa lo **SKU Standard**.|
    |Assegnazione indirizzi IP|Sì|**Dinamico**: gli indirizzi dinamici vengono assegnati solo dopo che l'indirizzo IP pubblico è associato a un'interfaccia di rete collegata a una macchina virtuale e che tale macchina virtuale viene avviata per la prima volta. Gli indirizzi dinamici possono cambiare se la macchina virtuale alla quale è collegata l'interfaccia di rete viene arrestata (deallocata). L'indirizzo rimane invariato se la macchina virtuale viene riavviata o arrestata, ma non deallocata. **Statico**: gli indirizzi statici vengono assegnati al momento della creazione dell'indirizzo IP pubblico. Non cambiano anche se la macchina virtuale viene arrestata (deallocata). L'indirizzo viene rilasciato solo quando viene eliminata l'interfaccia di rete. È possibile modificare il metodo di assegnazione dopo aver creato la l'interfaccia di rete. Se si seleziona *IPv6* per **Versione indirizzo IP**, il metodo di assegnazione è *Dinamico*. Se si seleziona *Standard* per **SKU**, il metodo di assegnazione è *Statico*.|
    |Timeout di inattività (minuti)|No |Il numero di minuti in cui la connessione TCP o HTTP resta aperta senza affidarsi ai client per l'invio di messaggi Keep-Alive. Se si seleziona IPv6 in **Versione indirizzo IP**, questo valore non può essere modificato. |
    |Etichetta del nome DNS|No |Deve essere univoca all'interno della località di Azure nella quale viene creato il nome (incluse tutte le sottoscrizioni e tutti i clienti). Azure registra automaticamente il nome e l'indirizzo IP nel proprio DNS, consentendo la connessione a una risorsa con tale nome. Per creare il nome DNS completo, Azure aggiunge una subnet predefinita come *location.cloudapp.azure.com* al nome specificato, dove location è la località selezionata. Se si sceglie di creare entrambe le versioni dell'indirizzo, lo stesso nome DNS viene assegnato sia all'indirizzo IPv4 che all'indirizzo IPv6. Il DNS predefinito di Azure contiene i record dei nomi AAAA sia per IPv4 che per IPv6 e risponde con entrambi i record quando viene eseguita una ricerca per il nome DNS. Il client sceglie con quale indirizzo comunicare, IPv4 o IPv6. Anziché (oppure oltre a) usare l'etichetta del nome DNS con il suffisso predefinito, è possibile usare il servizio DNS di Azure per configurare un nome DNS con un suffisso personalizzato che viene risolto nell'indirizzo IP pubblico. Per altre informazioni, vedere [Usare DNS di Azure con un indirizzo IP pubblico di Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
    |Crea un indirizzo IPv6 (o IPv4)|No | A seconda del valore selezionato in **Versione indirizzo IP**, viene visualizzato l'indirizzo IPv6 o IPv4. Se ad esempio si seleziona **IPv4** per **Versione indirizzo IP**, qui viene visualizzato **IPv6**. Se si seleziona *Standard* per **SKU**, non è possibile creare un indirizzo IPv6.
    |Nome (visibile solo se è stata selezionata la casella di controllo **Crea un indirizzo IPv6 (o IPv4)**)|Sì, se si seleziona la casella di controllo **Crea un indirizzo IPv6** (o IPv4).|Il nome deve essere diverso da quello immesso come primo **nome** in questo elenco. Se si sceglie di creare sia un indirizzo IPv4 che un indirizzo IPv6, il portale crea due risorse indirizzo IP pubblico separate, a ognuna delle quali è assegnata una versione dell'indirizzo IP.|
    |Assegnazione indirizzi IP (visibile solo se è stata selezionata la casella di controllo **Crea un indirizzo IPv6 (o IPv4)**)|Sì, se si seleziona la casella di controllo **Crea un indirizzo IPv6** (o IPv4).|Se la casella di controllo visualizzata è **Crea un indirizzo IPv4**, è possibile selezionare un metodo di assegnazione. Se la casella di controllo visualizzata è **Crea un indirizzo IPv6**, non è possibile selezionare un metodo di assegnazione, perché deve essere **Dinamico**.|
    |Sottoscrizione|Sì|Deve essere inclusa nella stessa [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) della risorsa cui si vuole associare l'indirizzo IP pubblico.|
    |Gruppo di risorse|Sì|Può trovarsi nello stesso [gruppo di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) della risorsa cui si vuole associare l'indirizzo IP pubblico oppure in un gruppo di risorse diverso.|
    |Località|Sì|Deve trovarsi nella stessa [località](https://azure.microsoft.com/regions), chiamata anche area, di quella della risorsa cui si vuole associare l'indirizzo IP pubblico.|
    |Zona di disponibilità| No  | Questa impostazione viene visualizzata solo se si seleziona una località supportata. Per un elenco di località supportate, vedere [Panoramica di Zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se è stato selezionato lo SKU **Basic**, viene selezionata automaticamente l'opzione *Nessuna*. Se si preferisce garantire una zona specifica, è possibile selezionarne una. Entrambe le opzioni non prevedono la ridondanza della zona. Se è stato selezionato lo SKU **Standard**: viene selezionata automaticamente l'opzione con ridondanza della zona e il percorso dei dati è resiliente agli errori a livello di zona. Se si preferisce garantire una zona specifica, non resiliente agli errori a livello di zona, è possibile selezionarne una.

**Comandi**

Anche se il portale consente di creare due risorse indirizzo IP pubblico (IPv4 e IPv6), i comandi dell'interfaccia della riga di comando e di PowerShell seguenti creano una sola risorsa con un indirizzo per una versione IP o per l'altra. Per creare due risorse indirizzo IP pubblico, una per ogni versione IP, è necessario eseguire il comando due volte, specificando nomi e versioni diverse per le risorse indirizzo IP pubblico.

|Strumento|Comando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Visualizzare un indirizzo IP pubblico, modificarne le impostazioni o eliminarlo

1. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *indirizzo ip pubblico*. Selezionare la voce **Indirizzi IP pubblici** visualizzata nei risultati della ricerca.
2. Selezionare dall'elenco il nome dell'indirizzo IP pubblico da visualizzare, eliminare o di cui modificare le impostazioni.
3. Completare una delle seguenti opzioni a seconda che si desideri visualizzare, eliminare o modificare l'indirizzo IP pubblico.
    - **Visualizza**: nella sezione **Panoramica** vengono visualizzate le impostazioni chiave per l'indirizzo IP pubblico, come l'interfaccia di rete alla quale è associato (se impostata). Il portale non visualizza la versione dell'indirizzo (IPv4 o IPv6). Per visualizzare le informazioni sulla versione, usare il comando di PowerShell o dell'interfaccia della riga di comando per visualizzare l'indirizzo IP pubblico. Se la versione dell'indirizzo IP è IPv6, l'indirizzo assegnato non viene visualizzato dal portale, da PowerShell o dall'interfaccia della riga di comando.
    - **Elimina**: per eliminare l'indirizzo IP pubblico, selezionare **Elimina** nella sezione **Panoramica**. Se l'indirizzo è associato a una configurazione IP, non può essere eliminato. Se l'indirizzo è associato a una configurazione, selezionare **Annulla associazione** per annullare l'associazione alla configurazione IP.
    - **Modifica**: selezionare **Configurazione**. Modificare le impostazioni usando le informazioni fornite nel passaggio 4 di [Creare un indirizzo IP pubblico](#create-a-public-ip-address). Per modificare l'assegnazione per un indirizzo IPv4 da statico a dinamico, prima è necessario annullare l'associazione tra l'indirizzo IPv4 pubblico e la configurazione IP. È quindi possibile modificare il metodo di assegnazione in dinamico e selezionare **Associa** per associare l'indirizzo IP alla stessa configurazione IP o a una configurazione diversa. È anche possibile lasciare l'indirizzo senza associazione. Per annullare l'associazione di un indirizzo IP pubblico, nella sezione **Panoramica** selezionare **Annulla associazione**.

    >[!WARNING]
    >Quando si modifica il metodo di assegnazione da statico a dinamico, l'indirizzo IP assegnato all'indirizzo IP pubblico viene perso. Mentre i server DNS pubblici Azure mantengono un mapping tra gli indirizzi statici o dinamici e qualsiasi etichetta del nome DNS (se ne è stato definito uno), un indirizzo IP dinamico può essere modificato quando la macchina virtuale viene avviata dopo essere stata arrestata (deallocata). Per evitare la modifica dell'indirizzo, assegnare un indirizzo IP statico.

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network public-ip-list](/cli/azure/network/public-ip#az_network_public_ip_list) per elencare gli indirizzi IP pubblici, [az network public-ip-show](/cli/azure/network/public-ip#az_network_public_ip_show) per visualizzare le impostazioni; [az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update) per aggiornare; [az network public-ip delete](/cli/azure/network/public-ip#az_network_public_ip_delete) per eliminare|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) per recuperare un oggetto indirizzo IP pubblico e visualizzarne le impostazioni, [Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress) per aggiornare le impostazioni; [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) per eliminare|

## <a name="permissions"></a>Autorizzazioni

Per eseguire attività negli indirizzi IP pubblici, l'account deve essere assegnato al ruolo [collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un ruolo [personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le operazioni appropriate elencate nella tabella seguente:

| Azione                                                             | NOME                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Leggere un indirizzo IP pubblico                                          |
| Microsoft.Network/publicIPAddresses/write                          | Creare o aggiornare un indirizzo IP pubblico                           |
| Microsoft.Network/publicIPAddresses/delete                         | Eliminare un indirizzo IP pubblico                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Associare un indirizzo IP pubblico a una risorsa                    |

## <a name="next-steps"></a>Passaggi successivi

- Creare un indirizzo IP pubblico usando gli script di esempio di [PowerShell](powershell-samples.md) o dell'[interfaccia della riga di comando di Azure](cli-samples.md) oppure i [modelli di Resource Manager](template-samples.md)
- Creare e applicare i [criteri di Azure](policy-samples.md) per gli indirizzi IP pubblici
- Assegnare indirizzi IP pubblici durante la creazione di una macchina virtuale di Azure [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), un [Bilanciamento del carico di Azure con connessione Internet Azure](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), un [gateway applicazione di Azure](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), una [connessione da sito a sito tramite un Gateway VPN di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o un [set di scalabilità della macchina virtuale di Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
