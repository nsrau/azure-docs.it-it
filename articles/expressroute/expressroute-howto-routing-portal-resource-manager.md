---
title: 'Configurare il peering per un circuito-ExpressRoute: Azure | Microsoft Docs'
description: Questo articolo descrive i passaggi per la creazione e il provisioning di ExpressRoute privato e peering Microsoft. Questo articolo illustra anche come controllare lo stato, aggiornare o eliminare i peering per un circuito.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 30b330d60d75896406cbdf2d1eb41537960c5be5
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965295"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Creare e modificare i peering per un circuito ExpressRoute

Questo articolo illustra come creare e gestire la configurazione di routing per un circuito ExpressRoute di Azure Resource Manager (ARM), usando il portale di Azure. La procedura seguente mostra anche come controllare lo stato e aggiornare, eliminare o effettuare il deprovisioning dei peering per un circuito ExpressRoute. Se si vuole usare un metodo diverso per eseguire operazioni nel circuito, selezionare l'articolo appropriato nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [interfaccia della riga di comando di Azure](howto-routing-cli.md)
> * [Video - Peering privato](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - Peering pubblico](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Peering Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (classico)](expressroute-howto-routing-classic.md)
> 

È possibile configurare il peering privato di Azure e il peering Microsoft per un circuito ExpressRoute (il peering pubblico di Azure è deprecato per i nuovi circuiti). È possibile configurare i peering nell'ordine desiderato. assicurandosi, tuttavia, di completare la configurazione di un peering per volta. Per altre informazioni sul routing dei domini e i peering, vedere [About circuits and peerings (Informazioni su circuiti e peering)](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione

* Prima di iniziare la configurazione, assicurarsi di aver letto le pagine relative ai [prerequisiti](expressroute-prerequisites.md), ai [requisiti per il routing](expressroute-routing.md) e ai [flussi di lavoro](expressroute-workflows.md).
* È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e fare in modo che venga abilitato dal provider di connettività prima di procedere. Per configurare i peering, il circuito ExpressRoute deve avere uno stato di provisioning e abilitazione. 
* Se si prevede di usare un hash MD5/chiave condivisa, assicurarsi di usarlo su entrambi i lati del tunnel e limitare il numero di caratteri alfanumerici a un massimo di 25. I caratteri speciali non sono supportati. 

Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (di solito una VPN IP, come MPLS), il provider di connettività configura e gestisce il routing per conto dell'utente. 

> [!IMPORTANT]
> Attualmente non vengono annunciati peering configurati da provider di servizi tramite il portale di gestione del servizio. L'abilitazione di questa funzionalità sarà presto disponibile. Rivolgersi al provider di servizi prima di configurare peering BGP.
> 
> 

## <a name="msft"></a>Peering Microsoft

Questa sezione consente di creare, ottenere, aggiornare ed eliminare la configurazione del peering Microsoft per un circuito ExpressRoute.

> [!IMPORTANT]
> Per i circuiti ExpressRoute configurati prima del 1 agosto 2017 tutti i prefissi di servizio saranno annunciati tramite il peering Microsoft, anche se non sono definiti i filtri di route. Il peering Microsoft dei circuiti ExpressRoute che vengono configurati dopo il 1° agosto 2017 non avrà alcun prefisso annunciato fino a quando non viene associato un filtro di route per il circuito. Per altre informazioni, vedere [Configure a route filter for Microsoft peering](how-to-routefilter-powershell.md) (Configurare un filtro di route per il peering Microsoft).
> 
> 

### <a name="to-create-microsoft-peering"></a>Per creare il peering Microsoft

1. Configurare il circuito ExpressRoute. Verificare lo **stato del provider** per assicurarsi che il provisioning del circuito venga effettuato completamente dal provider di connettività prima di continuare.

   Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di abilitare il peering Microsoft per l'utente. In tal caso, non è necessario seguire le istruzioni elencate nelle sezioni successive. Tuttavia, se il provider di connettività non gestisce il routing per l'utente, dopo aver creato il circuito, procedere con questa procedura.

   **Stato del provider del circuito: senza provisioning**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Stato del provider del circuito: provisioning eseguito**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Configurare il peering Microsoft per il circuito. Prima di procedere, verificare quanto segue:

   * Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR. Da questa subnet si assegnerà al router il primo indirizzo IP utilizzabile, poiché il secondo indirizzo IP utilizzabile per il router viene usato da Microsoft.
   * Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR. Da questa subnet si assegnerà al router il primo indirizzo IP utilizzabile, poiché il secondo indirizzo IP utilizzabile per il router viene usato da Microsoft.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN. Per i collegamenti primario e secondario, è necessario usare lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.
   * Advertised prefixes: è necessario fornire un elenco di tutti i prefissi che si intende pubblicizzare nella sessione BGP. Sono accettati solo prefissi di indirizzi IP pubblici. Se si intende inviare un set di prefissi, è possibile creare un elenco delimitato da virgole. Questi prefissi devono essere intestati all'utente in un registro RIR o IRR.
   * **Facoltativo -** ASN cliente: se si annunciano prefissi non registrati nel numero AS di peering, è possibile specificare il numero AS in cui sono registrati.
   * Routing Registry Name: è possibile specificare il registro RIR/IRR in cui sono registrati il numero AS e i prefissi.
   * **Facoltativo:** un hash MD5, se si sceglie di usarne uno.
3. È possibile selezionare il peering che si vuole configurare, come illustrato nell'esempio seguente. Selezionare la riga del peering Microsoft.

   [![Selezionare la riga del peering Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Selezionare la riga del peering Microsoft")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Configurare il peering Microsoft. **Salvare** la configurazione dopo aver specificato tutti i parametri. Nell'immagine seguente viene illustrata una configurazione di esempio:

   ![Configurare il peering Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Microsoft verifica se i ' prefissi pubblici annunciati ' è peer ASN ' (o ' Customer ASN ') specificati sono assegnati all'utente nel registro di sistema di routing Internet. Se si ricevono i prefissi pubblici da un'altra entità e se l'assegnazione non viene registrata con il registro di routing, la convalida automatica non verrà completata e richiederà la convalida manuale. Se la convalida automatica ha esito negativo, verrà visualizzato il messaggio "convalida necessaria". 
>
> Se viene visualizzato il messaggio "convalida richiesta", raccogliere i documenti che mostrano i prefissi pubblici assegnati all'organizzazione dall'entità elencata come proprietario dei prefissi nel registro di sistema di routing e inviare questi documenti per la convalida manuale da apertura di un ticket di supporto, come illustrato di seguito. 
>

   Se il circuito raggiunge uno stato di convalida necessario, è necessario aprire un ticket di supporto per mostrare la prova di proprietà dei prefissi al team di supporto. È possibile aprire un ticket di supporto direttamente dal portale, come mostrato nell'esempio seguente:

   ![Convalida necessaria-ticket di supporto](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Dopo che la configurazione è stata accettata correttamente, verrà visualizzata una schermata simile all'immagine seguente:

   ![Stato peering: configurato](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Stato peering: configurato")]

### <a name="getmsft"></a>Per visualizzare i dettagli del peering Microsoft

È possibile visualizzare le proprietà del peering Microsoft selezionando la riga per il peering.

[![Visualizzare le proprietà del peering Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Visualizza proprietà")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Per aggiornare la configurazione del peering Microsoft

È possibile selezionare la riga per il peering che si vuole modificare, quindi modificare le proprietà del peering e salvare le modifiche.

![Seleziona riga peering](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>Per eliminare il peering Microsoft

È possibile rimuovere la configurazione del peering facendo clic sull'icona Elimina, come illustrato nell'immagine seguente:

![Elimina peering](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Peering privato di Azure

Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering privato di Azure per un circuito ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Per creare un peering privato di Azure

1. Configurare il circuito ExpressRoute. Prima di continuare, assicurarsi che il provider di connettività abbia effettuato il provisioning completo del circuito. 

   Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di connettività di abilitare il peering privato di Azure per l'utente. In tal caso, non è necessario seguire le istruzioni elencate nelle sezioni successive. Tuttavia, se il provider di connettività non gestisce il routing per l'utente, dopo aver creato il circuito, procedere con i passaggi successivi.

   **Stato del provider del circuito: senza provisioning**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Stato del provider del circuito: provisioning eseguito**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Configurare il peering privato di Azure per il circuito. Prima di procedere con i passaggi successivi, verificare che siano presenti gli elementi seguenti:

   * Una subnet /30 per il collegamento primario. La subnet non deve far parte di alcuno spazio indirizzi riservato per le reti virtuali. Da questa subnet si assegnerà al router il primo indirizzo IP utilizzabile, poiché il secondo indirizzo IP utilizzabile per il router viene usato da Microsoft.
   * Una subnet /30 per il collegamento secondario. La subnet non deve far parte di alcuno spazio indirizzi riservato per le reti virtuali. Da questa subnet si assegnerà al router il primo indirizzo IP utilizzabile, poiché il secondo indirizzo IP utilizzabile per il router viene usato da Microsoft.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN. Per i collegamenti primario e secondario, è necessario usare lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte. È possibile usare un numero AS privato per questo peering, ad eccezione dell'intervallo da 65515 a 65520, inclusi questi numeri.
   * È necessario annunciare le route dal router perimetrale locale ad Azure tramite BGP quando si configura il peering privato.
   * **Facoltativo:** un hash MD5, se si sceglie di usarne uno.
3. Selezionare la riga del peering privato di Azure, come illustrato nell'esempio seguente:

   [![Selezionare la riga peering privato](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Selezionare la riga peering privato")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Configurare il peering privato. **Salvare** la configurazione dopo aver specificato tutti i parametri.

   ![Configurare il peering privato](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Dopo che la configurazione è stata accettata, viene visualizzata una schermata simile all'esempio seguente:

   ![peering privato salvato](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>Per visualizzare i dettagli relativi al peering privato di Azure

È possibile visualizzare le proprietà del peering privato di Azure selezionandolo.

[![Visualizzare le proprietà del peering privato](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Visualizzare le proprietà del peering privato")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Per aggiornare la configurazione del peering privato di Azure

È possibile selezionare la riga per il peering e modificare le relative proprietà. Dopo l'aggiornamento, salvare le modifiche.

![Aggiornare il peering privato](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Per eliminare un peering privato di Azure

È possibile rimuovere la configurazione del peering facendo clic sull'icona Elimina, come illustrato nell'immagine seguente:

> [!WARNING]
> Prima di eseguire questo esempio, verificare che tutte le reti virtuali e le connessioni di Copertura globale di ExpressRoute siano state rimosse. 
> 
> 

![Eliminare il peering privato](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="public"></a>Peering pubblico di Azure

Questa sezione consente di creare, ottenere, aggiornare ed eliminare la configurazione del peering pubblico di Azure per un circuito ExpressRoute.

> [!Note]
> Il peering pubblico di Azure è deprecato per i nuovi circuiti. Per altre informazioni, vedere [peering di ExpressRoute](expressroute-circuit-peerings.md).
>

### <a name="getpublic"></a>Per visualizzare i dettagli relativi al peering pubblico di Azure

Visualizzare le proprietà del peering pubblico di Azure selezionando il peering.

### <a name="updatepublic"></a>Per aggiornare la configurazione del peering pubblico di Azure

Selezionare la riga per il peering, quindi modificare le proprietà del peering.

### <a name="deletepublic"></a>Per eliminare un peering pubblico di Azure

Rimuovere la configurazione del peering selezionando l'icona Elimina.

## <a name="next-steps"></a>Passaggi successivi

Successivamente, [collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Per ulteriori informazioni sui flussi di lavoro ExpressRoute, vedere [Flussi di lavoro ExpressRoute](expressroute-workflows.md).
* Per altre informazioni sul peering del circuito, vedere l'articolo relativo ai [circuiti ExpressRoute e domini di routing](expressroute-circuit-peerings.md)
* Per ulteriori informazioni sull’uso delle reti virtuali, vedere [Panoramica sulla rete virtuale](../virtual-network/virtual-networks-overview.md).
