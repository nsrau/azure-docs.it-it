---
title: Creare, modificare o eliminare una rete virtuale di Azure | Microsoft Docs
description: Informazioni su come creare, modificare o eliminare una rete virtuale in Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: bb481b1aade29130e01a9d15c6dcf4a3a68e194e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="create-change-or-delete-a-virtual-network"></a>Creare, modificare o eliminare una rete virtuale

Informazioni su come creare ed eliminare una rete virtuale e su come modificare le impostazioni, ad esempio i server DNS e gli spazi indirizzi IP, di una rete virtuale esistente.

Una rete virtuale è una rappresentazione della rete dell'utente nel cloud. È una parte logicamente isolata del cloud di Azure dedicata alla sottoscrizione di Azure dell'utente. Per ogni rete virtuale creata, è possibile:
- Scegliere lo spazio indirizzi da assegnare. Uno spazio indirizzi è costituito da uno o più intervalli di indirizzi, definiti tramite la notazione CIDR (Classless Inter-Domain Routing), ad esempio 10.0.0.0/16.
- Scegliere se usare il server DNS fornito da Azure o il proprio server DNS. Per la risoluzione dei nomi all'interno della rete virtuale, a tutte le risorse connesse alla rete virtuale viene assegnato questo server DNS.
- Segmentare la rete virtuale in subnet, ognuna con il proprio intervallo di indirizzi, all'interno dello spazio indirizzi della rete virtuale. Per informazioni su come creare, modificare ed eliminare subnet, vedere [Creare, modificare o eliminare le subnet di rete virtuale](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Prima di iniziare

Prima di completare i passaggi di qualsiasi sezione di questo articolo, eseguire le attività seguenti:

- Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usa il portale, aprire https://portal.azure.com e accedere con l'account Azure.
- Se si usano i comandi di PowerShell per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/powershell) o tramite PowerShell dal computer in uso. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Questa esercitazione richiede il modulo di Azure PowerShell 5.2.0 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.
- Se si usano i comandi dell'interfaccia della riga di comando di Azure per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/bash) o tramite l'interfaccia della riga di comando dal computer in uso. Questa esercitazione richiede l'interfaccia della riga di comando di Azure versione 2.0.26 o successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.
2. Immettere o selezionare i valori per le impostazioni seguenti e quindi selezionare **Crea**:
    - **Nome**: il nome deve essere univoco nell'ambito del [gruppo di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) selezionato per la creazione della rete virtuale al suo interno. Dopo la creazione della rete virtuale non è possibile modificarne il nome. È possibile creare più reti virtuali in momenti diversi. Per alcuni suggerimenti sull'assegnazione del nome, vedere [Naming conventions](/azure/architecture/best-practices/naming-conventions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) (Convenzioni di denominazione). Il rispetto di una convenzione di denominazione consente una gestione più semplice di più reti virtuali.
    - **Spazio indirizzi**: lo spazio indirizzi per una rete virtuale è costituito da uno o più intervalli di indirizzi non sovrapposti, specificati con la notazione CIDR. L'intervallo di indirizzi definito può essere pubblico o privato (RFC 1918). Indipendentemente dalla sua definizione come pubblico o privato, l'intervallo di indirizzi è raggiungibile solo all'interno della rete virtuale, da reti virtuali interconnesse e da eventuali reti locali connesse alla rete virtuale. Non è possibile aggiungere gli intervalli di indirizzi seguenti:
        - 224.0.0.0/4 (multicast)
        - 255.255.255.255/32 (broadcast)
        - 127.0.0.0/8 (loopback)
        - 169.254.0.0/16 (locale rispetto al collegamento)
        - 168.63.129.16/32 (DNS interno)

      Al momento della creazione della rete virtuale è possibile definire un solo intervallo di indirizzi. Dopo la creazione della rete virtuale, tuttavia, è possibile aggiungerne altri. Per informazioni su come aggiungere un intervallo di indirizzi a una rete virtuale esistente, vedere [Aggiungere o rimuovere un intervallo di indirizzi](#add-or-remove-an-address-range).

      >[!WARNING]
      >Se gli intervalli di indirizzi di una rete virtuale si sovrappongono a quelli di un'altra rete virtuale o di una rete locale, non è possibile connettere le due reti. Prima di definire un intervallo di indirizzi, tenere conto della possibilità che in futuro sia necessario connettere la rete virtuale ad altre reti virtuali o locali.
      >
      >

    - **Nome subnet**: il nome della subnet deve essere univoco all'interno della rete virtuale. Dopo la creazione della subnet non è possibile modificarne il nome. Al momento della creazione di una rete virtuale il portale richiede la definizione di una subnet, anche se una rete virtuale non deve necessariamente avere una o più subnet. Quando si crea una rete virtuale nel portale, è possibile definire una sola subnet. Dopo la creazione della rete virtuale è possibile aggiungerne altre. Per aggiungere una subnet a una rete virtuale, vedere [Aggiungere, modificare o eliminare le subnet di rete virtuale](virtual-network-manage-subnet.md). Per creare una rete virtuale con più subnet, è possibile usare l'interfaccia della riga di comando di Azure o PowerShell.

      >[!TIP]
      >Talvolta gli amministratori creano subnet diverse per filtrare o controllare il routing del traffico tra queste. Prima di definire le subnet, tenere conto dell'eventuale necessità di filtrare e instradare il traffico tra le subnet stesse. Per altre informazioni sull'applicazione di filtri al traffico tra le subnet, vedere [Gruppi di sicurezza di rete](security-overview.md). Azure effettua il routing automatico del traffico tra le subnet. È tuttavia possibile eseguire l'override delle route predefinite di Azure. Per altre informazioni sul routing del traffico delle subnet predefinito, vedere [Routing del traffico di rete virtuale](virtual-networks-udr-overview.md).
      >

    - **Intervallo di indirizzi subnet**: l'intervallo deve essere compreso nello spazio indirizzi immesso in precedenza per la rete virtuale. L'intervallo più piccolo che è possibile specificare è /29, che fornisce otto indirizzi IP per subnet. Azure riserva il primo e l'ultimo indirizzo in ogni subnet per conformità al protocollo. Altri tre indirizzi sono riservati per l'uso da parte del servizio di Azure. Di conseguenza, una rete virtuale con un intervallo di indirizzi di subnet /29 ha solo tre indirizzi IP utilizzabili. Se si prevede di connettere una rete virtuale a un gateway VPN, è necessario creare una subnet per il gateway. Altre informazioni su [considerazioni specifiche sugli intervalli di indirizzi per le subnet di gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Dopo aver creato la subnet, in determinate condizioni è possibile modificare l'intervallo di indirizzi. Per informazioni su come modificare un intervallo di indirizzi della subnet, vedere [Aggiungere, modificare o eliminare le subnet di rete virtuale](virtual-network-manage-subnet.md).
    - **Sottoscrizione**: selezionare una [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Non è possibile usare la stessa rete virtuale in più sottoscrizioni di Azure. È tuttavia possibile connettere una rete virtuale in una sottoscrizione a reti virtuali in altre sottoscrizioni con il [peering di reti virtuali](virtual-network-peering-overview.md). Qualsiasi risorsa di Azure da connettere alla rete virtuale deve essere compresa nella sottoscrizione della rete virtuale stessa.
    - **Gruppo di risorse**: selezionare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) esistente o crearne uno nuovo. Una risorsa di Azure da connettere alla rete virtuale può trovarsi nello stesso gruppo di risorse della rete virtuale o in un gruppo di risorse diverso.
    - **Località**: selezionare una [località](https://azure.microsoft.com/regions/) di Azure. Le località sono dette anche aree. Una rete virtuale può trovarsi in una sola località di Azure. È tuttavia possibile connettere una rete virtuale in una località con una rete virtuale in un'altra località tramite un gateway VPN. Qualsiasi risorsa di Azure da connettere alla rete virtuale deve trovarsi nella località della rete virtuale stessa.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network vnet create](/cli/azure/network/vnet)
- PowerShell: [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Visualizzare le reti virtuali e le impostazioni

1. Nella casella di ricerca nella parte superiore del portale immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
2. Nell'elenco delle reti virtuali selezionare la rete virtuale di cui si vogliono visualizzare le impostazioni.
3. Per la rete virtuale selezionata sono elencate le impostazioni seguenti:
    - **Panoramica**: offre informazioni sulla rete virtuale, ad esempio lo spazio indirizzi e i server DNS. Lo screenshot seguente illustra le impostazioni di panoramica per una rete virtuale con nome **MyVNet**:

        ![Panoramica dell'interfaccia di rete](./media/manage-virtual-network/vnet-overview.png)

      È possibile spostare una rete virtuale in una sottoscrizione o in un gruppo di risorse diverso selezionando **Modifica** accanto a **Gruppo di risorse** o **Nome sottoscrizione**. Per informazioni su come spostare una rete virtuale, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). L'articolo elenca i prerequisiti e descrive come spostare le risorse tramite il portale di Azure, PowerShell e l'interfaccia della riga di comando di Azure. Tutte le risorse connesse alla rete virtuale devono essere spostate con la rete virtuale stessa.
    - **Spazio indirizzi**: elenca gli spazi indirizzi assegnati alla rete virtuale. Per informazioni su come aggiungere e rimuovere un intervallo di indirizzi nello spazio indirizzi, completare la procedura descritta in [Aggiungere o rimuovere uno spazio indirizzi](#add-or-remove-an-address-range).
    - **Dispositivi connessi**: elenca tutte le risorse connesse alla rete virtuale. Nello screenshot precedente, alla rete virtuale sono connessi tre interfacce di rete e un bilanciamento del carico. Nell'elenco sono presenti tutte le nuove risorse create e connesse alla rete virtuale. Se si elimina una risorsa connessa alla rete virtuale, la risorsa non compare più nell'elenco.
    - **Subnet**: presenta l'elenco delle subnet esistenti all'interno della rete virtuale. Per informazioni su come aggiungere e rimuovere una subnet, vedere [Aggiungere, modificare o eliminare le subnet di rete virtuale](virtual-network-manage-subnet.md).
    - **Server DNS**: consente di specificare se la risoluzione dei nomi per i dispositivi connessi alla rete virtuale deve essere eseguita dal server DNS interno di Azure o da un server DNS personalizzato. Per impostazione predefinita, quando si crea una rete virtuale tramite il Portale di Azure, per la risoluzione dei nomi all'interno di una rete virtuale vengono usati i server DNS di Azure. Per modificare i server DNS, completare la procedura descritta in [Modificare i server DNS](#change-dns-servers) in questo articolo.
    - **Peering**: se presenti nella sottoscrizione, i peering sono elencati qui. È possibile visualizzare le impostazioni per i peering esistenti o creare, modificare ed eliminare peering. Per altre informazioni sui peering, vedere [Peering di rete virtuale](virtual-network-peering-overview.md).
    - **Proprietà**: visualizza le impostazioni della rete virtuale, inclusi l'ID risorsa della rete virtuale e la sottoscrizione di Azure in cui si trova.
    - **Diagramma**: il diagramma offre una rappresentazione visiva di tutti i dispositivi connessi alla rete virtuale con alcune informazioni chiave sui dispositivi stessi. Per gestire un dispositivo in questa visualizzazione, selezionare il dispositivo nel diagramma.
    - **Impostazioni comuni di Azure**: per altre informazioni sulle impostazioni comuni di Azure, vedere le informazioni seguenti:
        *   [Log attività](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [Controllo di accesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Tag](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [Blocchi](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Script di automazione](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Comandi**

- Interfaccia della riga di comando di Azure: [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show)
- PowerShell: [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Aggiungere o rimuovere un intervallo di indirizzi

È possibile aggiungere e rimuovere intervalli di indirizzi per una rete virtuale. L'intervallo di indirizzi deve essere specificato con la notazione CIDR e non può sovrapporsi ad altri intervalli di indirizzi all'interno della stessa rete virtuale. Gli intervalli di indirizzi definiti possono essere pubblici o privati (RFC 1918). Indipendentemente dalla sua definizione come pubblico o privato, l'intervallo di indirizzi è raggiungibile solo all'interno della rete virtuale, da reti virtuali interconnesse e da eventuali reti locali connesse alla rete virtuale. Non è possibile aggiungere gli intervalli di indirizzi seguenti:

- 224.0.0.0/4 (multicast)
- 255.255.255.255/32 (broadcast)
- 127.0.0.0/8 (loopback)
- 169.254.0.0/16 (locale rispetto al collegamento)
- 168.63.129.16/32 (DNS interno)

Per aggiungere o rimuovere un intervallo di indirizzi:

1. Nella casella di ricerca nella parte superiore del portale immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
2. Nell'elenco delle reti virtuali selezionare la rete virtuale per cui si vuole aggiungere o rimuovere un intervallo di indirizzi.
3. Fare clic su **Spazio indirizzi** in **Impostazioni**.
4. Compilare le informazioni per una delle opzioni seguenti:
    - **Add an address range** (Aggiungere un intervallo di indirizzi): immettere il nuovo intervallo di indirizzi. L'intervallo di indirizzi non può sovrapporsi a un intervallo di indirizzi esistente definito per la rete virtuale.
    - **Remove an address range** (Rimuovere un intervallo di indirizzi): a destra dell'intervallo di indirizzi da rimuovere, selezionare **...**  e quindi selezionare **Rimuovi**. Se è presente una subnet nell'intervallo di indirizzi, non è possibile rimuoverlo. Prima di rimuovere un intervallo di indirizzi è necessario eliminare tutte le subnet presenti nell'intervallo e le eventuali risorse nelle subnet.
5. Selezionare **Salva**.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network vnet update](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="change-dns-servers"></a>Modificare i server DNS

Tutte le macchine virtuali connesse alla rete virtuale vengono registrate presso uno dei server DNS specificati per la rete virtuale. Il server DNS specificato viene usato da queste macchine virtuali anche per la risoluzione dei nomi. Ogni scheda di interfaccia di rete di una VM può avere le proprie impostazioni per i server DNS. Se per una scheda di interfaccia di rete sono definite impostazioni specifiche per i server DNS, queste sostituiscono le impostazioni relative ai server DNS per la rete virtuale. Per altre informazioni sulle impostazioni DNS delle schede di interfaccia di rete, vedere [attività e impostazioni relative all'interfaccia di rete](virtual-network-network-interface.md#change-dns-servers). Per altre informazioni sulla risoluzione dei nomi per le macchine virtuali e sulle istanze del ruolo in Servizi cloud di Microsoft Azure, vedere [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](virtual-networks-name-resolution-for-vms-and-role-instances.md). Per aggiungere, modificare o rimuovere un server DNS:

1. Nella casella di ricerca nella parte superiore del portale immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
2. Nell'elenco delle reti virtuali selezionare la rete virtuale per cui si vogliono modificare i server DNS.
3.  Selezionare **Server DNS** in **Impostazioni**.
4. Selezionare una delle opzioni seguenti:
    - **Predefinito (fornito da Azure)**: tutti i nomi delle risorse e gli indirizzi IP privati vengono registrati automaticamente presso i server DNS di Azure. È possibile risolvere i nomi tra tutte le risorse connesse alla stessa rete virtuale. Non è possibile usare questa opzione per la risoluzione dei nomi tra reti virtuali diverse. Per risolvere i nomi tra reti virtuali diverse è necessario usare un server DNS personalizzato.
    - **Personalizzato**: è possibile aggiungere uno o più server, fino al limite di Azure per le reti virtuali. Per altre informazioni sui limiti dei server DNS, vedere i [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). L'utente ha a disposizione le seguenti opzioni:
        - **Aggiungere un indirizzo**: consente di aggiungere un server all'elenco dei server DNS della rete virtuale. Questa opzione consente anche di registrare il server DNS in Azure. Se un server DNS è già stato registrato in Azure, è possibile selezionarlo nell'elenco.
        - **Rimuovere un indirizzo**: accanto al server che si vuole rimuovere selezionare **...** e quindi **Rimuovi**. Questa operazione rimuove il server solo dall'elenco della rete virtuale attiva. Il server DNS resta registrato in Azure in modo che venga usato dalle altre reti virtuali.
        - **Riordinare gli indirizzi dei server DNS**: è importante verificare che siano elencati nell'ordine corretto per l'ambiente. Gli elenchi di server DNS vengono usati nell'ordine in cui sono specificati e non rappresentano una configurazione di tipo round robin. Se il primo server DNS nell'elenco è raggiungibile, il client lo usa, indipendentemente dal fatto che funzioni correttamente o meno. Rimuovere tutti i server DNS elencati e quindi aggiungerli di nuovo nell'ordine desiderato.
        - **Modificare un indirizzo**: evidenziare il server DNS nell'elenco e quindi immettere il nuovo indirizzo.
5. Selezionare **Salva**.
6. Riavviare le macchine virtuali connesse alla rete virtuale in modo che vengano loro assegnate le nuove impostazioni del server DNS. Le VM continuano a usare le impostazioni DNS correnti finché non vengono riavviate.

**Comandi**

- Interfaccia della riga di comando di Azure: [az network vnet update](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Eliminare una rete virtuale

È possibile eliminare una rete virtuale solo se a questa non sono connesse risorse. Se una o più risorse sono connesse a una qualsiasi subnet all'interno della rete virtuale, è prima necessario eliminare le risorse connesse a tutte le subnet della rete virtuale stessa. La procedura necessaria per l'eliminazione di una risorsa è diversa a seconda della risorsa. Per informazioni su come eliminare le risorse connesse a una subnet, leggere la documentazione relativa a ogni tipo di risorsa che si vuole eliminare. Per eliminare una rete virtuale:

1. Nella casella di ricerca nella parte superiore del portale immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
2. Nell'elenco delle reti virtuali selezionare la rete virtuale che si vuole eliminare.
3. Verificare che non siano presenti dispositivi connessi alla rete virtuale selezionando **Dispositivi connessi** in **Impostazioni**. Se sono presenti dispositivi connessi, è necessario eliminarli prima di eliminare la rete virtuale. Se non sono presenti dispositivi connessi, selezionare **Panoramica**.
4. Selezionare **Elimina**.
5. Per confermare l'eliminazione della rete virtuale, selezionare **Sì**.

**Comandi**

- Interfaccia della riga di comando di Azure: [azure network vnet delete](/cli/azure/network/vnet#az_network_vnet_delete)
- PowerShell: [Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork)

## <a name="permissions"></a>Autorizzazioni

Per eseguire attività nelle reti virtuali, l'account deve essere assegnato al ruolo [Collaboratore Rete](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un ruolo [personalizzato](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le autorizzazioni appropriate elencate nella tabella seguente:

|Operazione                                    |   Nome operazione                    |
|-------------------------------------------  |   --------------------------------  |
|Microsoft.Network/virtualNetworks/read       |   Ottieni rete virtuale               |
|Microsoft.Network/virtualNetworks/write      |   Crea o aggiorna la macchina virtuale  |
|Microsoft.Network/virtualNetworks/delete     |   Elimina rete virtuale            |

## <a name="next-steps"></a>Passaggi successivi

- Per creare una macchina virtuale e quindi connetterla a una rete virtuale, vedere [Creare una rete virtuale e connettere macchine virtuali](quick-create-portal.md#create-virtual-machines).
- Per filtrare il traffico di rete tra subnet all'interno di una rete virtuale, vedere [Creare gruppi di sicurezza di rete](virtual-networks-create-nsg-arm-pportal.md).
- Per effettuare il peering di una rete virtuale con un'altra, vedere [Create a virtual network peering](tutorial-connect-virtual-networks-portal.md) (Creare un peering di reti virtuali).
- Per informazioni sulle opzioni per la connessione di una rete virtuale a una rete locale, vedere [Informazioni sul gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).
