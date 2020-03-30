---
title: 'Azure ExpressRoute: spostare un peering pubblico nel peering MicrosoftAzure ExpressRoute: Move a public peering to Microsoft peering'
description: Questo articolo illustra i passaggi per spostare il peering pubblico nel peering Microsoft su ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 48ecfcc0d6241e7926892a3ca1c9925b0dc07241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436848"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Spostare un peering pubblico nel peering Microsoft

Questo articolo descrive come spostare una configurazione di peering pubblico nel peering Microsoft senza tempi di inattività. ExpressRoute supporta ora i servizi PaaS Azure, come l'archiviazione di Azure e il database SQL di Azure, usando il peering Microsoft con i filtri di route. È ora necessario un solo dominio di routing per accedere ai servizi PaaS e SaaS Microsoft. È possibile usare i filtri di route per annunciare in modo selettivo i prefissi dei servizi PaaS per le aree di Azure che si vuole usare.

Il peering pubblico di Azure ha 1 indirizzo IP NAT associato a ogni sessione BGP. Il peering Microsoft consente di configurare allocazioni NAT personalizzate e di usare filtri di route per annunci di prefisso selettivo. Il peering pubblico è un servizio unidirezionale che usa il quale la connettività viene sempre avviata dalla rete WAN ai servizi di Microsoft Azure.Public Peering is a unidirectional service using which Connectivity is always initiated from your WAN to Microsoft Azure services. I servizi di Microsoft Azure non potranno attivare connessioni alla rete dell'utente tramite questo dominio di routing.

Dopo l'abilitazione del peering pubblico, è possibile connettersi a tutti i servizi di Azure. Non è consentito scegliere in modo selettivo i servizi per cui vengono annunciate route. Mentre il peering Microsoft è una connettività bidirezionale in cui la connessione può essere avviata dal servizio Microsoft Azure insieme alla rete WAN. Per altre informazioni sul routing dei domini e sul peering, vedere [Circuiti ExpressRoute e domini di routing.](expressroute-circuit-peerings.md)

## <a name="before-you-begin"></a><a name="before"></a>Prima di iniziare

Per connettersi al peering Microsoft, è necessario configurare e gestire i requisiti NAT. Il provider di connettività può configurare e gestire NAT come servizio gestito. Se si intende accedere ai servizi PaaS e SaaS Azure nel peering Microsoft, è importante ridimensionare il pool di indirizzi IP NAT correttamente. Per altre informazioni su NAT per ExpressRoute, vedere i [requisiti NAT per il peering Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering). Quando ci si connette a Microsoft tramite Azure ExpressRoute (peering Microsoft), sono disponibili più collegamenti a Microsoft.When you connect to Microsoft through Azure ExpressRoute(Microsoft peering), you have multiple links to Microsoft. Un collegamento è rappresentato dalla connessione Internet esistente, mentre l'altro avviene tramite ExpressRoute. Parte del traffico diretto a Microsoft può passare per Internet ma tornare tramite ExpressRoute o viceversa.

![Connettività bidirezionale](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> Il pool IP NAT annunciato per Microsoft non deve essere annunciato per Internet, altrimenti verrebbe interrotta la connettività con altri servizi Microsoft.

Fare riferimento a [Routing asimmetrico con più percorsi](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) di rete per le avvertenze del routing asimmetrico prima di configurare il peering Microsoft.

* Se si usa il peering pubblico e si dispone di regole di rete IP per gli indirizzi IP pubblici che vengono usati per accedere all'[Archiviazione di Azure](../storage/common/storage-network-security.md) o al [database SQL di Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), è necessario assicurarsi che il pool di indirizzi IP NAT configurato con il peering Microsoft sia incluso nell'elenco di indirizzi IP pubblici per l'account di archiviazione di Azure o per l'account SQL di Azure.<br>
* Per passare nel peering Microsoft senza tempi di inattività, usare i passaggi in questo articolo nell'ordine in cui sono elencati.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. Creare un peering Microsoft

Se il peering Microsoft non è stato creato, consultare uno qualsiasi dei seguenti articoli per creare il peering Microsoft. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di abilitare il peering Microsoft per il circuito.

Se il livello 3 è gestito dall'utente, le seguenti informazioni sono necessarie prima di procedere:

* Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR. Da questa subnet si assegnerà al router il primo indirizzo IP utilizzabile, poiché il secondo indirizzo IP utilizzabile per il router viene usato da Microsoft.<br>
* Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR. Da questa subnet si assegnerà al router il primo indirizzo IP utilizzabile, poiché il secondo indirizzo IP utilizzabile per il router viene usato da Microsoft.<br>
* Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN. Per i collegamenti primario e secondario, è necessario usare lo stesso ID VLAN.<br>
* Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.<br>
* Advertised prefixes: è necessario fornire un elenco di tutti i prefissi che si intende pubblicizzare nella sessione BGP. Sono accettati solo prefissi di indirizzi IP pubblici. Se si intende inviare un set di prefissi, è possibile creare un elenco delimitato da virgole. Questi prefissi devono essere intestati all'utente in un registro RIR o IRR.<br>
* Routing Registry Name: è possibile specificare il registro RIR/IRR in cui sono registrati il numero AS e i prefissi.

* **Facoltativo** - ASN cliente: se si annunciano prefissi non registrati nel numero AS di peering, è possibile specificare il numero AS in cui sono registrati.<br>
* **Facoltativo:** un hash MD5 se si sceglie di usarne uno.

Istruzioni dettagliate per abilitare il peering Microsoft sono disponibili negli articoli seguenti:Detailed instructions to enable Microsoft peering can get found in the following articles:

* [Creare il peering Microsoft usando il Portale di Azure](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Creare il peering Microsoft usando Azure Powershell](expressroute-howto-routing-arm.md#msft)<br>
* [Creare il peering Microsoft usando l'interfaccia della riga di comando di Azure](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. Verificare che il peering Microsoft sia abilitato

Verificare che il peering Microsoft sia abilitato e che i prefissi pubblici annunciati si trovino nello stato configurato.

* [Portale di Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Interfaccia della riga di comando di AzureAzure](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. Configurare e collegare un filtro di percorso al circuito

Per impostazione predefinita, il nuovo peering Microsoft non annuncia alcun prefisso fino a quando non viene collegato un filtro di route al circuito. Quando si crea una regola di filtro di route, è possibile specificare l'elenco delle comunità di servizi per le aree di Azure che si vuole usare per i servizi di Azure PaaS.When you create a route filter rule, you can specify the list of service communities for Azure regions that you want to consume for Azure PaaS services. Ciò offre la flessibilità necessaria per filtrare i percorsi in base alle proprie esigenze, come illustrato nella schermata seguente:This provides you the flexibility to filter the routes as by your requirement, as shown in the following screenshot:

![Unire il peering pubblico](./media/how-to-move-peering/routefilter.jpg)

Usare uno dei seguenti articoli per configurare i filtri di route:

* [Configurare i filtri di route per il peering Microsoft usando il Portale di Azure](how-to-routefilter-portal.md)<br>
* [Configurare i filtri di route per il peering Microsoft usando Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Configurare i filtri di route per il peering Microsoft usando l'interfaccia della riga di comando di Azure](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. Eliminare il peering pubblico

Dopo avere verificato la corretta configurazione del peering Microsoft e il corretto annuncio dei prefissi da usare nel peering Microsoft, sarà possibile eliminare il peering pubblico. Per eliminare il peering pubblico, leggere uno dei seguenti articoli:

* [Eliminare il peering pubblico di Azure usando Azure PowerShell](about-public-peering.md#powershell)
* [Eliminare il peering pubblico di Azure usando l'interfaccia della riga di comando](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. Visualizzare i peering
  
È possibile visualizzare un elenco di tutti i circuiti ExpressRoute e i peering nel portale di Azure. Per altre informazioni, vedere [Per visualizzare i dettagli del peering Microsoft](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
