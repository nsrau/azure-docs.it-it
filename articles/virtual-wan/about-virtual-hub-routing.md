---
title: Informazioni sul routing dell'hub virtuale
titleSuffix: Azure Virtual WAN
description: Questo articolo descrive il routing dell'hub virtuale
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 368440976558730bd7bb9600a0bf45c56d1ee300
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147385"
---
# <a name="about-virtual-hub-routing"></a>Informazioni sul routing dell'hub virtuale

Le funzionalità di routing in un hub virtuale sono fornite da un router che gestisce tutto il routing tra i gateway usando Border Gateway Protocol (BGP). Un hub virtuale può contenere più gateway, ad esempio un gateway VPN da sito a sito, un gateway ExpressRoute, un gateway da punto a sito, un firewall di Azure. Questo router fornisce anche la connettività di transito tra le reti virtuali che si connettono a un hub virtuale e può supportare fino a una velocità effettiva aggregata di 50 Gbps. Queste funzionalità di routing si applicano ai clienti WAN virtuali standard.

Per configurare il routing, vedere [How to Configure Virtual Hub routing](how-to-virtual-hub-routing.md).

## <a name="routing-concepts"></a><a name="concepts"></a>Concetti di routing

Le sezioni seguenti descrivono i concetti chiave del routing dell'hub virtuale.

> [!NOTE]
> Alcuni di questi nuovi concetti per la tabella di route Hub, l'associazione, la propagazione e le route statiche in una connessione di rete virtuale potrebbero ancora essere implementati e previsti per il completamento nella settimana del 3 agosto.
>

### <a name="hub-route-table"></a><a name="hub-route"></a>Tabella di route Hub

Una tabella di route dell'hub virtuale può contenere una o più route. Una route include il nome, un'etichetta, un tipo di destinazione, un elenco di prefissi di destinazione e informazioni di hop successivo per un pacchetto da indirizzare. Una **connessione** in genere avrà una configurazione di routing che viene associata o propagata a una tabella di route

### <a name="connection"></a><a name="connection"></a>Connessione

Le connessioni sono Gestione risorse risorse che dispongono di una configurazione di routing. I quattro tipi di connessioni sono:

* **Connessione VPN**: connette un sito VPN a un gateway VPN dell'hub virtuale.
* **Connessione ExpressRoute**: connette un circuito ExpressRoute a un gateway ExpressRoute dell'hub virtuale.
* **Connessione configurazione P2S**: connette una configurazione VPN utente (da punto a sito) a un gateway VPN utente (da punto a sito) dell'hub virtuale.
* **Connessione alla rete virtuale dell'hub**: connette le reti virtuali a un hub virtuale.

È possibile configurare la configurazione di routing per una connessione di rete virtuale durante l'installazione. Per impostazione predefinita, tutte le connessioni vengono associate e propagate alla tabella di route predefinita.

### <a name="association"></a><a name="association"></a>Associazione

Ogni connessione è associata a una tabella di route. L'associazione di una connessione a una tabella di route consente l'invio del traffico alla destinazione indicata come route nella tabella di route. La configurazione di routing della connessione visualizzerà la tabella di route associata.  È possibile associare più connessioni alla stessa tabella di route. Tutte le connessioni VPN, ExpressRoute e VPN utente sono associate alla stessa tabella di route (predefinita).

Per impostazione predefinita, tutte le connessioni sono associate a una **tabella di route predefinita** in un hub virtuale. Ogni hub virtuale ha una propria tabella di route predefinita, che può essere modificata per aggiungere una o più route statiche. Le route aggiunte staticamente hanno la precedenza su route apprese dinamicamente per gli stessi prefissi.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="Associazione":::

### <a name="propagation"></a><a name="propagation"></a>Propagazione

Le connessioni propagano dinamicamente le route a una tabella di route. Con una connessione VPN, una connessione ExpressRoute o una connessione di configurazione P2S, le route vengono propagate dall'hub virtuale al router locale usando BGP. Le route possono essere propagate a una o più tabelle di route.

Per ogni hub virtuale è disponibile anche una **tabella di route None** . La propagazione alla tabella di route None implica che non è necessario propagare le route dalla connessione. VPN, ExpressRoute e connessioni VPN utente propagano le route allo stesso set di tabelle di route.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="Propagazione":::

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>Configurazione delle route statiche in una connessione di rete virtuale

La configurazione delle route statiche fornisce un meccanismo per gestire il traffico attraverso un indirizzo IP hop successivo, che può essere un appliance virtuale di rete sottoposta a provisioning in una VNet spoke collegata a un hub virtuale. La route statica è costituita da un nome di route, da un elenco di prefissi di destinazione e da un indirizzo IP hop successivo.

## <a name="route-tables-in-basic-and-standard-virtual-wans-prior-to-the-feature-set-of-association-and-propagation"></a><a name="route"></a>Eseguire il routing delle tabelle nelle WAN virtuali Basic e standard prima del set di funzionalità di associazione e propagazione

Le tabelle di route ora includono funzionalità per l'associazione e la propagazione. Una tabella di route preesistente è una tabella di route che non dispone di queste funzionalità. Se si dispone di route preesistenti nel routing Hub e si desidera utilizzare le nuove funzionalità, tenere presente quanto segue:

* **Clienti WAN virtuali standard con route preesistenti nell'hub virtuale**:

Per usare le nuove funzionalità della tabella di route, attendere fino alla settimana del 3 agosto per il completamento della distribuzione in Azure. Se sono presenti route preesistenti nella sezione di routing per l'hub in portale di Azure, sarà necessario prima eliminarle e quindi provare a creare nuove tabelle di route (disponibili nella sezione tabelle di route per l'hub in portale di Azure)

* **Clienti WAN virtuali di base con route preesistenti nell'hub virtuale**: per usare le nuove funzionalità della tabella di route, attendere fino alla settimana del 3 agosto per il completamento della distribuzione in Azure. Se sono presenti route preesistenti nella sezione di routing per l'hub in portale di Azure, sarà necessario prima eliminarle, quindi **aggiornare** la rete WAN virtuale di base alla rete WAN virtuale standard. Vedere [aggiornare una rete WAN virtuale da Basic a standard](upgrade-virtual-wan.md).

## <a name="next-steps"></a>Passaggi successivi

Per configurare il routing, vedere [How to Configure Virtual Hub routing](how-to-virtual-hub-routing.md).

Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
