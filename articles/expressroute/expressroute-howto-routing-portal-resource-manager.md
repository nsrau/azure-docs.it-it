---
title: Come configurare il routing per un circuito ExpressRoute tramite il portale di Azure| Microsoft Docs
description: Questo articolo descrive come creare ed eseguire il provisioning di un circuito ExpressRoute per il peering privato, il peering pubblico e il peering Microsoft. Questo articolo mostra anche come controllare lo stato e aggiornare o eliminare i peering per un circuito.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: expressroute
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc

---
# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Creare e modificare il routing per un circuito ExpressRoute
> [!div class="op_single_selector"]
> [Portale di Azure - Resource Manager](expressroute-howto-routing-portal-resource-manager.md)
> [PowerShell - Resource Manager](expressroute-howto-routing-arm.md)
> [PowerShell - Classico](expressroute-howto-routing-classic.md)
> 
> 

Questo articolo descrive le procedure per creare e gestire la configurazione di routing per un circuito ExpressRoute usando il portale di Azure e il modello di distribuzione Resource Manager.

**Informazioni sui modelli di distribuzione di Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione
* Prima di iniziare la configurazione, assicurarsi di aver letto le pagine relative ai [prerequisiti](expressroute-prerequisites.md), ai [requisiti per il routing](expressroute-routing.md) e ai [flussi di lavoro](expressroute-workflows.md).
* È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e fare in modo che venga abilitato dal provider di connettività prima di procedere. Per poter eseguire i cmdlet descritti di seguito deve essere stato effettuato il provisioning del circuito ExpressRoute e lo stato del circuito deve essere abilitato.

Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (di solito un IPVPN, come MPLS), il provider di connettività configurerà e gestirà il routing per conto dell'utente. 

> [!IMPORTANT]
> Attualmente non vengono annunciati peering configurati da provider di servizi tramite il portale di gestione del servizio. L'abilitazione di questa funzionalità sarà presto disponibile. Rivolgersi al provider di servizi prima di configurare peering BGP.
> 
> 

Per un circuito ExpressRoute è possibile configurare uno, due o tutti e tre i peering (peering privato di Azure, peering pubblico di Azure e peering Microsoft). È possibile configurare i peering nell'ordine desiderato. assicurandosi, tuttavia, di completare la configurazione di un peering per volta. 

## <a name="azure-private-peering"></a>Peering privato di Azure
Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering privato di Azure per un circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Per creare un peering privato di Azure
1. Configurare il circuito ExpressRoute. Prima di continuare, assicurarsi che il provider di connettività abbia effettuato il provisioning completo del circuito.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configurare il peering privato di Azure per il circuito. Prima di procedere con i passaggi successivi, verificare che siano presenti gli elementi seguenti:
   
   * Una subnet /30 per il collegamento primario. Non deve far parte di alcuno spazio indirizzi riservato per le reti virtuali.
   * Una subnet /30 per il collegamento secondario. Non deve far parte di alcuno spazio indirizzi riservato per le reti virtuali.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte. È possibile usare il numero AS privato per questo peering. Assicurarsi di non usare il numero 65515.
   * Un hash MD5, se si sceglie di usarne uno. **Facoltativo**.
3. Selezionare la riga del peering privato di Azure, come illustrato di seguito.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Configurare il peering privato. La figura seguente mostra un esempio di configurazione.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Salvare la configurazione dopo aver specificato tutti i parametri. Una volta che la configurazione è stata accettata, verrà visualizzata una schermata simile all'esempio seguente.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-view-azure-private-peering-details"></a>Per visualizzare i dettagli relativi al peering privato di Azure
È possibile visualizzare le proprietà del peering privato di Azure selezionandolo.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-update-azure-private-peering-configuration"></a>Per aggiornare la configurazione del peering privato di Azure
È possibile selezionare la riga per il peering e modificare le relative proprietà. 

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="to-delete-azure-private-peering"></a>Per eliminare un peering privato di Azure
È possibile rimuovere la configurazione del peering facendo clic sull'icona Elimina, come illustrato di seguito.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="azure-public-peering"></a>Peering pubblico di Azure
Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering pubblico di Azure per un circuito ExpressRoute. 

### <a name="to-create-azure-public-peering"></a>Per creare un peering pubblico di Azure
1. Configurare il circuito ExpressRoute. Prima di continuare, assicurarsi che il provider di connettività abbia effettuato il provisioning completo del circuito.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configurare il peering pubblico di Azure per il circuito. Prima di procedere con i passaggi successivi, verificare che siano presenti gli elementi seguenti:
   
   * Una subnet /30 per il collegamento primario. 
   * Una subnet /30 per il collegamento secondario. 
   * Tutti gli indirizzi IP usati per configurare questo peering devono essere indirizzi IPv4 pubblici validi.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.
   * Un hash MD5, se si sceglie di usarne uno. **Facoltativo**.
3. Selezionare la riga del peering pubblico di Azure, come illustrato di seguito.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Configurare il peering pubblico. La figura seguente mostra un esempio di configurazione.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Salvare la configurazione dopo aver specificato tutti i parametri. Una volta che la configurazione è stata accettata, verrà visualizzata una schermata simile all'esempio seguente.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-view-azure-public-peering-details"></a>Per visualizzare i dettagli relativi al peering pubblico di Azure
È possibile visualizzare le proprietà del peering pubblico di Azure selezionandolo.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-update-azure-public-peering-configuration"></a>Per aggiornare la configurazione del peering pubblico di Azure
È possibile selezionare la riga per il peering e modificare le relative proprietà. 

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="to-delete-azure-public-peering"></a>Per eliminare un peering pubblico di Azure
È possibile rimuovere la configurazione del peering facendo clic sull'icona Elimina, come illustrato di seguito.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="microsoft-peering"></a>Peering Microsoft
Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering Microsoft per un circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Per creare il peering Microsoft
1. Configurare il circuito ExpressRoute. Prima di continuare, assicurarsi che il provider di connettività abbia effettuato il provisioning completo del circuito.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configurare il peering Microsoft per il circuito. Prima di procedere, verificare quanto segue:
   
   * Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR.
   * Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.
   * **Advertised prefixes:** : è necessario fornire un elenco di tutti i prefissi che si intende pubblicizzare nella sessione BGP. Sono accettati solo prefissi di indirizzi IP pubblici. Per inviare un set di prefissi, è possibile creare un elenco con valori delimitati da virgole. Questi prefissi devono essere intestati all'utente in un registro RIR o IRR.
   * **ASN cliente** : se si annunciano prefissi registrati nel numero AS del peering, è possibile specificare il numero AS in cui sono registrati. **Facoltativo**.
   * **Routing Registry Name:** : è possibile specificare il registro RIR/IRR in cui sono registrati il numero AS e i prefissi. **Facoltativo.**
   * Hash MD5, se si sceglie di usarne uno. **Facoltativo.**
3. È possibile selezionare il peering che si vuole configurare come illustrato di seguito. Selezionare la riga del peering Microsoft.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Configurare il peering Microsoft. La figura seguente mostra un esempio di configurazione.
   
   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Salvare la configurazione dopo aver specificato tutti i parametri. 
   
    Se il circuito raggiunge uno stato di convalida richiesto, come illustrato di seguito, si dovrà aprire un ticket di supporto per fornire la prova della proprietà dei prefissi al team di supporto.  
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

    È possibile aprire un ticket di supporto direttamente dal portale come mostrato di seguito   

    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. Una volta che la configurazione è stata accettata, verrà visualizzata una schermata simile all'esempio seguente.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-view-microsoft-peering-details"></a>Per visualizzare i dettagli del peering Microsoft
È possibile visualizzare le proprietà del peering pubblico di Azure selezionandolo.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="to-update-microsoft-peering-configuration"></a>Per aggiornare la configurazione del peering Microsoft
È possibile selezionare la riga per il peering e modificare le relative proprietà. 

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-delete-microsoft-peering"></a>Per eliminare il peering Microsoft
È possibile rimuovere la configurazione del peering facendo clic sull'icona Elimina, come illustrato di seguito.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="next-steps"></a>Passaggi successivi
Successivamente, [collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Per ulteriori informazioni sui flussi di lavoro ExpressRoute, vedere [Flussi di lavoro ExpressRoute](expressroute-workflows.md).
* Per altre informazioni sul peering del circuito, vedere l'articolo relativo ai [circuiti ExpressRoute e domini di routing](expressroute-circuit-peerings.md)
* Per ulteriori informazioni sull’uso delle reti virtuali, vedere [Panoramica sulla rete virtuale](../virtual-network/virtual-networks-overview.md).

<!--HONumber=Oct16_HO2-->


