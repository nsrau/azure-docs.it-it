<properties 
   pageTitle="IPv6 Overview | Microsoft Azure"
   description="Learn about IPv6 addressing in Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/04/2016"
   ms.author="telmos" />

# Supporto IPv6 in Azure

Il protocollo IPv6 è stato inizialmente introdotto nel 1998 dalla IETF (Internet Engineering Task Force) per risolvere il limite degli indirizzi IPv4 a 32 bit sulla rete Internet pubblica. Con il maggior numero di dispositivi mobili connessi a Internet e l'avvento di Internet of Things (IoT), gli indirizzi IPv6 sono sempre più frequenti sulla rete Internet pubblica.

>[AZURE.IMPORTANT] IPv6 in Azure è attualmente previsto in anteprima privata e non è disponibile per tutti i clienti. Questa pagina verrà aggiornata dopo che la funzionalità diventerà disponibile per tutti i clienti.

È possibile utilizzare gli indirizzi IPv6 nelle VM IaaS Azure nelle distribuzioni di Resource Manager per la connettività in ingresso e in uscita alla rete Internet pubblica. L'elenco seguente descrive le funzionalità di IPv6 in Azure.

- **VM IaaS in Resource Manager**
	- Le VM dispongono sia di un endpoint IPv4 sia di un endpoint IPv6.
	- Le VM possono connettersi a Internet tramite un IP pubblico del servizio di bilanciamento del carico per connessioni in entrata (bilanciamento del carico e NAT) e connessioni uscita (SNAT).
	- Le VM si connettono a tutti i servizi Azure tramite i rispettivi endpoint IPv4.
	- Le VM utilizzano solo endpoint IPv4 per la comunicazione East-West.
- **DNS di Azure**
	- DNS di Azure supporta IPv6 e fornisce record AAAA, insieme a record A IPv4.
	- DNS di Azure risponde alle query con i record AAAA e i record A per le ricerche, quando entrambi sono presenti. 
- **Office365**
	- I servizi Office365 supportano IPv6.
	- È possibile connettersi dalla rete locale alle risorse IPv6 in Office365 tramite un circuito ExpressRoute.

## VM IaaS in Resource Manager

È possibile sfruttare la connettività IPv6 solo utilizzando il modello di distribuzione di gestione delle risorse. Sebbene ogni VM in una distribuzione di Resource Manager disponga di endpoint IPv4 e IPv6, l'endpoint IPv6 può essere utilizzato solo per la connessione alla rete Internet pubblica attraverso un servizio di bilanciamento del carico come illustrato di seguito.

![IPv6 connectivity](./media/virtual-network-ipv6-overview/figure1.png)

## DNS di Azure

È possibile ospitare i record AAAA IPv6 in DNS di Azure. Quando DNS di Azure riceve una query per una risorsa, risponde con tutti i record disponibili per il nome host. Ad esempio, se si dispone di un record A e di un record AAAA per un determinato nome host (ad esempio www.contoso.com), DNS di Azure invia entrambi gli indirizzi come risposta alla query. È compito del client DNS decidere se utilizzare l'endpoint IPv4 o IPv6 per la connettività dopo aver ricevuto la risposta dal servizio DNS di Azure o da qualsiasi altro server DNS che supporti i record AAAA.

>[AZURE.NOTE] I computer Windows tentano sempre di connettersi tramite IPv6 prima di tentare una connessione IPv4.

## Office 365

È possibile utilizzare la connettività IPv6 tra la rete locale e Office365 tramite un circuito ExpressRoute e le impostazioni peer Microsoft. Assicurarsi di comprendere [il funzionamento di ExpressRoute](../expressroute/expressroute-introduction.md) in relazione alle impostazioni peer Microsoft.

## Passaggi successivi

- Informazioni su [DNS di Azure](../dns/dns-overview.md).
- Informazioni su [ExpressRoute](../expressroute/expressroute-introduction.md).

<!---HONumber=AcomDC_0309_2016-->