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
ms.custom: fasttrack-edit
ms.openlocfilehash: f5116b3dfd53c5272ef7bb11d1719b7ee62416c4
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055793"
---
# <a name="about-virtual-hub-routing"></a>Informazioni sul routing dell'hub virtuale

Le funzionalità di routing in un hub virtuale sono fornite da un router che gestisce tutto il routing tra i gateway usando Border Gateway Protocol (BGP). Un hub virtuale può contenere più gateway, ad esempio un gateway VPN da sito a sito, un gateway ExpressRoute, un gateway da punto a sito, un firewall di Azure. Questo router fornisce anche la connettività di transito tra le reti virtuali che si connettono a un hub virtuale e può supportare fino a una velocità effettiva aggregata di 50 Gbps. Queste funzionalità di routing si applicano ai clienti WAN virtuali standard. 

Per configurare il routing, vedere [How to Configure Virtual Hub routing](how-to-virtual-hub-routing.md).

## <a name="routing-concepts"></a><a name="concepts"></a>Concetti di routing

Le sezioni seguenti descrivono i concetti chiave del routing dell'hub virtuale.

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

### <a name="labels"></a><a name="static"></a>Etichette
Le etichette forniscono un meccanismo per raggruppare logicamente le tabelle di route. Questa operazione è particolarmente utile durante la propagazione delle route dalle connessioni a più tabelle di route. Ad esempio, la tabella di route predefinita include un'etichetta incorporata denominata ' default '. Quando gli utenti propagano le route di connessione all'etichetta ' default ', si applica automaticamente a tutte le tabelle di route predefinite in ogni hub della rete WAN virtuale. 

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>Configurazione delle route statiche in una connessione di rete virtuale

La configurazione delle route statiche fornisce un meccanismo per gestire il traffico attraverso un indirizzo IP hop successivo, che può essere un appliance virtuale di rete sottoposta a provisioning in una VNet spoke collegata a un hub virtuale. La route statica è costituita da un nome di route, da un elenco di prefissi di destinazione e da un indirizzo IP hop successivo.

## <a name="reset-hub"></a><a name="route"></a>Reimposta Hub
Disponibile solo nella portale di Azure, questa opzione consente all'utente di portare le risorse non riuscite, ad esempio le tabelle di route, il router hub o la risorsa dell'hub virtuale, tornando allo stato di provisioning legittimo. Si tratta di un'opzione aggiuntiva che l'utente deve prendere in considerazione prima di contattare Microsoft per ottenere assistenza. Questa operazione non Reimposta alcun gateway in un hub virtuale. 

## <a name="route-tables-in-basic-and-standard-virtual-wans-prior-to-the-feature-set-of-association-and-propagation"></a><a name="route"></a>Eseguire il routing delle tabelle nelle WAN virtuali Basic e standard prima del set di funzionalità di associazione e propagazione

Le tabelle di route includono ora funzionalità per l'associazione e la propagazione. Le tabelle di route preesistenti non hanno queste funzionalità. Se si dispone di route preesistenti nel routing dell'hub e si vogliono usare le nuove funzionalità, considerare le opzioni seguenti:

* **Clienti WAN virtuali standard con route preesistenti nell'hub virtuale**:

Se sono presenti route preesistenti nella sezione di routing per l'hub in portale di Azure, sarà necessario prima eliminarle e quindi provare a creare nuove tabelle di route (disponibili nella sezione tabelle di route per l'hub in portale di Azure)

* **Clienti WAN virtuali di base con route preesistenti nell'hub virtuale**: se sono presenti route preesistenti nella sezione di routing per l'hub in portale di Azure, sarà necessario prima eliminarle, quindi **aggiornare** la rete WAN virtuale di base alla rete WAN virtuale standard. Vedere [Aggiornare una rete WAN virtuale da Basic a Standard](upgrade-virtual-wan.md).

## <a name="virtual-wan-routing-considerations"></a><a name="considerations"></a>Considerazioni sul routing della rete WAN virtuale

Quando si configura il routing WAN virtuale, tenere presente quanto segue:

* Tutte le connessioni a rami (da punto a sito, da sito a sito e ExpressRoute) devono essere associate alla tabella di route predefinita. In questo modo, tutti i rami apprenderanno gli stessi prefissi.
* Tutte le connessioni a rami devono propagare le route allo stesso set di tabelle di route. Se, ad esempio, si decide che i rami devono propagarsi alla tabella di route predefinita, questa configurazione deve essere coerente in tutti i rami. Di conseguenza, tutte le connessioni associate alla tabella di route predefinita saranno in grado di raggiungere tutti i rami.
* Il ramo da ramo a ramo tramite il firewall di Azure non è attualmente supportato.
* Quando si usa il firewall di Azure in più aree, tutte le reti virtuali spoke devono essere associate alla stessa tabella di route. Ad esempio, se si ha un subset di reti virtuali che passano attraverso il firewall di Azure mentre altri reti virtuali ignorano il firewall di Azure nello stesso hub virtuale, non è possibile.
* È possibile configurare un singolo IP hop successivo per ogni connessione VNet.
* Hub virtuale non supporta la route statica per 0.0.0.0/0 e la connessione di rete virtuale hop successivo (o un indirizzo IP di un dispositivo nella connessione VNet)
* Tutte le informazioni relative alla Route 0.0.0.0/0 sono confinate alla tabella di route di un Hub locale. Questa route non viene propagata tra gli hub.

## <a name="next-steps"></a>Passaggi successivi

Per configurare il routing, vedere [How to Configure Virtual Hub routing](how-to-virtual-hub-routing.md).

Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
