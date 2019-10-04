---
title: Spostare un peering pubblico nel peering Microsoft - Azure ExpressRoute | Microsoft Docs
description: Questo articolo illustra i passaggi per spostare il peering pubblico nel peering Microsoft su ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 5581e2a5c2fe2ee5e154870f7ffc2ab1c3c0f3b4
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592109"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Spostare un peering pubblico nel peering Microsoft

Questo articolo descrive come spostare una configurazione di peering pubblico nel peering Microsoft senza tempi di inattività. ExpressRoute supporta ora i servizi PaaS Azure, come l'archiviazione di Azure e il database SQL di Azure, usando il peering Microsoft con i filtri di route. È ora necessario un solo dominio di routing per accedere ai servizi PaaS e SaaS Microsoft. È possibile usare i filtri di route per annunciare in modo selettivo i prefissi dei servizi PaaS per le aree di Azure che si vuole usare.

Peering pubblico di Azure ha 1 indirizzo IP NAT associato a ogni sessione BGP. Il peering Microsoft consente di configurare proprie allocazioni NAT, nonché di utilizzare i filtri di route per gli annunci di prefisso selettiva. Peering pubblico è un servizio unidirezionale con cui la connettività viene sempre avviato dalla rete WAN in servizi di Microsoft Azure. I servizi di Microsoft Azure non potranno attivare connessioni alla rete dell'utente tramite questo dominio di routing.

Dopo l'abilitazione del peering pubblico, è possibile connettersi a tutti i servizi di Azure. Non è consentito scegliere in modo selettivo i servizi per cui vengono annunciate route. Mentre il peering Microsoft è una connettività bidirezionale dove connessione può essere avviata dal servizio di Microsoft Azure insieme a una rete WAN. Per altre informazioni sui domini di routing e peering, vedere [ExpressRoute circuiti e domini di routing](expressroute-circuit-peerings.md).

## <a name="before"></a>Prima di iniziare

Per connettersi al peering Microsoft, è necessario configurare e gestire i requisiti NAT. Il provider di connettività può configurare e gestire NAT come servizio gestito. Se si intende accedere ai servizi PaaS e SaaS Azure nel peering Microsoft, è importante ridimensionare il pool di indirizzi IP NAT correttamente. Per altre informazioni su NAT per ExpressRoute, vedere i [requisiti NAT per il peering Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering). Quando ci si connette a Microsoft attraverso Azure ExpressRoute(Microsoft peering), sono presenti più collegamenti a Microsoft. Un collegamento è rappresentato dalla connessione Internet esistente, mentre l'altro avviene tramite ExpressRoute. Parte del traffico diretto a Microsoft può passare per Internet ma tornare tramite ExpressRoute o viceversa.

![Connettività bidirezionale](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> Il pool IP NAT annunciato per Microsoft non deve essere annunciato per Internet, altrimenti verrebbe interrotta la connettività con altri servizi Microsoft.

Fare riferimento a [routing asimmetrico con più percorsi di rete](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) per avvertenze del routing asimmetrico prima di configurare il peering Microsoft.

* Se si usa il peering pubblico e si dispone di regole di rete IP per gli indirizzi IP pubblici che vengono usati per accedere all'[Archiviazione di Azure](../storage/common/storage-network-security.md) o al [database SQL di Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), è necessario assicurarsi che il pool di indirizzi IP NAT configurato con il peering Microsoft sia incluso nell'elenco di indirizzi IP pubblici per l'account di archiviazione di Azure o per l'account SQL di Azure.<br>
* Per passare nel peering Microsoft senza tempi di inattività, usare i passaggi in questo articolo nell'ordine in cui sono elencati.

## <a name="create"></a>1. Creare il peering Microsoft

Se il peering Microsoft non è stato creato, consultare uno qualsiasi dei seguenti articoli per creare il peering Microsoft. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di abilitare il peering Microsoft per il circuito.

Se il livello 3 verrà gestito dall'utente le informazioni seguenti sono necessarie prima di procedere:

* Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR. Da questa subnet si assegnerà al router il primo indirizzo IP utilizzabile, poiché il secondo indirizzo IP utilizzabile per il router viene usato da Microsoft.<br>
* Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR. Da questa subnet si assegnerà al router il primo indirizzo IP utilizzabile, poiché il secondo indirizzo IP utilizzabile per il router viene usato da Microsoft.<br>
* Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN. Per i collegamenti primario e secondario, è necessario usare lo stesso ID VLAN.<br>
* Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.<br>
* Prefissi annunciati: è necessario fornire un elenco di tutti i prefissi che si intende annunciare nella sessione BGP. Sono accettati solo prefissi di indirizzi IP pubblici. Se si intende inviare un set di prefissi, è possibile creare un elenco delimitato da virgole. Questi prefissi devono essere intestati all'utente in un registro RIR o IRR.<br>
* Nome del registro di routing: è possibile specificare il registro RIR/IRR in cui sono registrati il numero AS e i prefissi.

* **Facoltativo** -ASN cliente: se si annunciano prefissi non registrati nel numero AS del peering, è possibile specificare il numero AS in cui sono registrati.<br>
* **Facoltativo** -un hash MD5 se si sceglie di usarne uno.

Istruzioni dettagliate per abilitare il peering Microsoft sono disponibili negli articoli seguenti:

* [Creare il peering Microsoft usando il Portale di Azure](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Creare il peering Microsoft usando Azure Powershell](expressroute-howto-routing-arm.md#msft)<br>
* [Creare il peering Microsoft usando l'interfaccia della riga di comando di Azure](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Verificare che il peering Microsoft sia abilitato.

Verificare che il peering Microsoft sia abilitato e che i prefissi pubblici annunciati si trovino nello stato configurato.

* [Portale di Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Interfaccia della riga di comando di Azure](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Configurare e associare un filtro di route al circuito

Per impostazione predefinita, nuova il peering Microsoft non annunciano i prefissi fino a quando non viene associato un filtro di route al circuito. Quando si crea una regola di filtro di route, è possibile specificare l'elenco di community di servizio per le aree di Azure che si vuole usare per i servizi PaaS di Azure. Questo offre la flessibilità necessaria per filtrare le route in base alle esigenze, come illustrato nello screenshot seguente:

![Unire il peering pubblico](./media/how-to-move-peering/routefilter.jpg)

Usare uno dei seguenti articoli per configurare i filtri di route:

* [Configurare i filtri di route per il peering Microsoft usando il Portale di Azure](how-to-routefilter-portal.md)<br>
* [Configurare i filtri di route per il peering Microsoft usando Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Configurare i filtri di route per il peering Microsoft usando l'interfaccia della riga di comando di Azure](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Eliminare il peering pubblico.

Dopo avere verificato la corretta configurazione del peering Microsoft e il corretto annuncio dei prefissi da usare nel peering Microsoft, sarà possibile eliminare il peering pubblico. Per eliminare il peering pubblico, leggere uno dei seguenti articoli:

* [Eliminare il peering pubblico di Azure usando il Portale di Azure](expressroute-howto-routing-portal-resource-manager.md#deletepublic)<br>
* [Eliminare il peering pubblico di Azure usando Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)<br>
* [Eliminare il peering pubblico di Azure usando l'interfaccia della riga di comando](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Visualizzare i peering
  
È possibile visualizzare un elenco di tutti i circuiti ExpressRoute e i peering nel portale di Azure. Per altre informazioni, vedere [Per visualizzare i dettagli del peering Microsoft](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
