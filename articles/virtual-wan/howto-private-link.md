---
title: Condividere un servizio di collegamento privato attraverso la rete WAN virtuale
titleSuffix: Azure Virtual WAN
description: Passaggi per la configurazione del collegamento privato nella rete WAN virtuale
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: cc8e7314c941035207ecf809a9d85ef46bd58379
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913756"
---
# <a name="use-private-link-in-virtual-wan"></a>USA collegamento privato nella rete WAN virtuale

Il [collegamento privato di Azure](../private-link/private-link-overview.md) è una tecnologia che consente di connettere le offerte della piattaforma distribuita come servizio di Azure con la connettività degli indirizzi IP privati esponendo gli [endpoint privati](../private-link/private-endpoint-overview.md). Con la rete WAN virtuale di Azure è possibile distribuire un endpoint privato in una delle reti virtuali connesse a qualsiasi hub virtuale. In questo modo viene fornita la connettività a qualsiasi altra rete virtuale o ramo connesso alla stessa rete WAN virtuale.

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia già stata distribuita una rete WAN virtuale con uno o più hub, oltre ad almeno due reti virtuali connesse alla rete WAN virtuale.

Per creare una nuova rete WAN virtuale e un nuovo hub, attenersi ai passaggi descritti negli articoli seguenti:

* [Creare una rete WAN virtuale](virtual-wan-site-to-site-portal.md#openvwan)
* [Creare un hub](virtual-wan-site-to-site-portal.md#hub)
* [Connettere una rete virtuale a un hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>Creare un endpoint di collegamento privato

È possibile creare un endpoint di collegamento privato per molti servizi diversi. In questo esempio verrà usato il database SQL di Azure. Per altre informazioni su come creare un endpoint privato per un database SQL di Azure, vedere [Guida introduttiva: creare un endpoint privato usando il portale di Azure](../private-link/create-private-endpoint-portal.md). La figura seguente mostra la configurazione di rete del database SQL di Azure:

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="Crea collegamento privato" lightbox="./media/howto-private-link/create-private-link.png":::

Dopo aver creato il database SQL di Azure, è possibile verificare l'indirizzo IP dell'endpoint privato che Esplora gli endpoint privati:

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="Crea collegamento privato" lightbox="./media/howto-private-link/endpoints.png":::

Facendo clic sull'endpoint privato creato, dovrebbe essere visualizzato l'indirizzo IP privato e il nome di dominio completo (FQDN). Si noti che l'endpoint privato ha un indirizzo IP compreso nell'intervallo di VNet in cui è stato distribuito (10.1.3.0/24):

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="Crea collegamento privato" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>Verificare la connettività dallo stesso VNet

In questo esempio verrà verificata la connettività al database SQL di Azure da una macchina virtuale Ubuntu con gli strumenti di MS SQL installati. Il primo passaggio consiste nel verificare che la risoluzione DNS funzioni e che il nome di dominio completo del database SQL di Azure venga risolto in un indirizzo IP privato, nella stessa VNet in cui è stato distribuito l'endpoint privato (10.1.3.0/24):

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Come si può notare nell'output precedente, il nome di dominio completo `wantest.database.windows.net` viene mappato a `wantest.privatelink.database.windows.net` , che la zona DNS privata creata lungo l'endpoint privato verrà risolta nell'indirizzo IP privato `10.1.3.228` . Esaminando la zona DNS privata verrà verificato che sia presente un record A per l'endpoint privato di cui è stato eseguito il mapping all'indirizzo IP privato:

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="Crea collegamento privato" lightbox="./media/howto-private-link/dns-zone.png":::

Dopo aver verificato la risoluzione DNS corretta, è possibile tentare di connettersi al database:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

Come si può notare, viene usata una speciale query SQL che fornisce l'indirizzo IP di origine visualizzato da SQL Server dal client. In questo caso il server vede il client con l'indirizzo IP privato ( `10.1.3.75` ), il che significa che il traffico passa dalla VNet direttamente all'endpoint privato.

Si noti che è necessario impostare le variabili `username` e `password` per trovare la corrispondenza con le credenziali definite nel database SQL di Azure per fare in modo che gli esempi di questa guida funzionino.

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>Connettersi da una VNet diversa

Ora che un VNet in una rete WAN virtuale di Azure dispone di connettività all'endpoint privato, tutti gli altri reti virtuali e rami connessi alla rete WAN virtuale possono accedervi. Per denominare due esempi, è necessario fornire connettività tramite uno dei modelli supportati dalla rete WAN virtuale di Azure, ad esempio lo [scenario any-to-any](scenario-any-to-any.md) o lo [scenario VNet di servizi condivisi](scenario-shared-services-vnet.md).

Quando si dispone della connettività tra il VNet o il ramo di VNet in cui è stato distribuito l'endpoint privato, è necessario configurare la risoluzione DNS:

* Se ci si connette all'endpoint privato da un VNet, è possibile usare la stessa zona privata creata con il database SQL di Azure.
* Se ci si connette all'endpoint privato da un ramo (VPN da sito a sito, VPN da punto a sito o ExpressRoute), è necessario usare la risoluzione DNS locale.

In questo esempio verrà stabilita la connessione da un VNet diverso, quindi la zona DNS privata verrà collegata alla nuova VNet, in modo che i carichi di lavoro possano risolvere il nome di dominio completo del database SQL di Azure nell'indirizzo IP privato. Questa operazione viene eseguita tramite il collegamento della zona DNS privata alla nuova VNet:

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="Crea collegamento privato" lightbox="./media/howto-private-link/dns-link.png":::

A questo punto, tutte le macchine virtuali nel VNet collegato devono risolvere correttamente il nome di dominio completo del database SQL di Azure nell'indirizzo IP privato del collegamento privato:

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Per verificare che questo VNet (10.1.1.0/24) disponga della connettività al VNet originale in cui è stato configurato l'endpoint privato (10.1.3.0/24), è possibile verificare la tabella di route valida in qualsiasi macchina virtuale nell'VNet:

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="Crea collegamento privato" lightbox="./media/howto-private-link/effective-routes.png":::

Come si può notare, esiste una route che punta alla VNet 10.1.3.0/24 inserita dai gateway di rete virtuale nella rete WAN virtuale di Azure. A questo punto è possibile testare la connettività al database:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

In questo esempio è stato illustrato come la creazione di un endpoint privato in uno dei reti virtuali collegati a una rete WAN virtuale fornisca connettività al resto di reti virtuali e rami nella rete WAN virtuale.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
