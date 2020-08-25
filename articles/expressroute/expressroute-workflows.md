---
title: 'Azure ExpressRoute: flusso di lavoro di configurazione del circuito'
description: Questa pagina mostra il flusso di lavoro per la configurazione di circuiti e peering ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: cherylmc
ms.custom: contperfq1
ms.openlocfilehash: 229b7c145fa38443d2bc5f99005078ffa7f77065
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814077"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Flussi di lavoro ExpressRoute per provisioning di un circuito e stati di circuito

Questo articolo illustra i flussi di lavoro di configurazione del provisioning e del routing del servizio a un livello elevato. Le sezioni seguenti descrivono le attività per eseguire il provisioning di un circuito ExpressRoute end-to-end.

## <a name="workflow-steps"></a>Passaggi del flusso di lavoro

### <a name="1-prerequisites"></a>1. Prerequisiti

Verificare che i prerequisiti siano soddisfatti. Per un elenco completo, vedere [prerequisiti ed](expressroute-prerequisites.md)elenco di controllo.

* È stata creata una sottoscrizione di Azure.
* La connettività fisica è stata stabilita con il partner ExpressRoute o configurata tramite ExpressRoute Direct. Vedere località [e partner](expressroute-locations-providers.md#partners) per visualizzare il partner ExpressRoute e la connettività diretta ExpressRoute tra le località di peering.

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2. ordinare la connettività o configurare ExpressRoute Direct

Ordinare la connettività dal provider di servizi o configurare ExpressRoute Direct.

#### <a name="expressroute-partner-model"></a>Modello di partner ExpressRoute

Ordinare la connettività dal provider di connettività. Questo processo varia. Per altre informazioni su come ordinare la connettività, contattare il provider di connettività.

* Selezionare il partner ExpressRoute
* Selezionare la località di peering
* Selezionare la larghezza di banda
* Selezionare il modello di fatturazione
* Selezionare il componente aggiuntivo standard o Premium

#### <a name="expressroute-direct-model"></a>Modello diretto di ExpressRoute

* Visualizza la capacità diretta ExpressRoute disponibile tra le località di peering.
* Riservare le porte mediante la creazione della risorsa ExpressRoute Direct nella sottoscrizione di Azure.
* Richiedere e ricevere la lettera di autorizzazione e ordinare le connessioni fisiche incrociate dal provider della località di peering.
* Abilitare lo stato amministratore e visualizzare i livelli di luce e il collegamento fisico usando [monitoraggio di Azure](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics).

### <a name="3-create-an-expressroute-circuit"></a>3. creare un circuito ExpressRoute

#### <a name="expressroute-partner-model"></a>Modello di partner ExpressRoute

Verificare che il partner ExpressRoute sia pronto per il provisioning della connettività. Il circuito ExpressRoute viene addebitato dal momento in cui viene emessa una chiave di servizio. Usare le istruzioni in [creare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) per creare il circuito.

#### <a name="expressroute-direct-model"></a>Modello diretto di ExpressRoute

Verificare che il collegamento fisico e lo stato amministratore siano abilitati in entrambi i collegamenti. Vedere [come configurare ExpressRoute Direct](how-to-expressroute-direct-portal.md) per informazioni aggiuntive. Il circuito ExpressRoute viene addebitato dal momento in cui viene emessa una chiave di servizio. Usare le istruzioni in [creare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) per creare il circuito.

### <a name="4-service-provider-provisions-connectivity"></a>4. il provider di servizi effettua la connettività

Questa sezione riguarda solo il modello di connettività partner ExpressRoute:

* Fornire la chiave di servizio (s-Key) al provider di connettività.
* Fornire informazioni aggiuntive necessarie per il provider di connettività (ad esempio, l'ID VPN).
* Se il provider gestisce la configurazione di routing, fornire i dettagli necessari.

È possibile verificare che sia stato eseguito correttamente il provisioning del circuito verificando lo stato di provisioning del circuito ExpressRoute usando PowerShell, il portale di Azure o l'interfaccia della riga di comando.

### <a name="5-configure-routing-domains"></a>5. configurare i domini di routing

Configurare i domini di routing. Se il provider di connettività gestisce la configurazione di livello 3, configurerà il routing per il circuito. Se il provider di connettività offre solo servizi di livello 2 o se si usa ExpressRoute Direct, è necessario configurare il routing in base alle linee guida descritte negli articoli [relativi ai requisiti di routing](expressroute-routing.md) e alla configurazione del [routing](expressroute-howto-routing-classic.md) .

#### <a name="for-azure-private-peering"></a>Per il peering privato di Azure

Consentire il peering privato per connettersi alle macchine virtuali e ai servizi cloud distribuiti nella rete virtuale di Azure.

* Subnet di peering per il percorso 1 (/30)
* Subnet di peering per il percorso 2 (/30)
* ID VLAN per il peering
* ASN per il peering
* ASN ExpressRoute = 12076
* Hash MD5 (facoltativo)

#### <a name="for-microsoft-peering"></a>Per il peering Microsoft

Consente di accedere a Microsoft Servizi online, ad esempio Office 365. Inoltre, tutti i servizi PaaS di Azure sono accessibili tramite il peering Microsoft. È necessario assicurarsi di usare un proxy/Edge separato per connettersi a Microsoft rispetto a quello usato per Internet. Se si usa lo stesso server perimetrale per ExpressRoute e Internet, si verificherà un routing asimmetrico con interruzioni della connettività di rete.

* Subnet di peering per il percorso 1 (/30). deve essere un indirizzo IP pubblico
* Subnet di peering per il percorso 2 (/30)-deve essere un indirizzo IP pubblico
* ID VLAN per il peering
* ASN per il peering
* Prefissi annunciati: devono essere prefissi IP pubblici
* ASN del cliente (facoltativo se diverso dal peering ASN)
* RIR/IRR per la convalida IP e ASN
* ASN ExpressRoute = 12076
* Hash MD5 (facoltativo)

### <a name="6-start-using-the-expressroute-circuit"></a>6. iniziare a usare il circuito ExpressRoute

* È possibile collegare le reti virtuali di Azure al circuito ExpressRoute per abilitare la connettività dall'ambiente locale alla rete virtuale di Azure. Per istruzioni, vedere l'articolo [collegare un VNet a un circuito](expressroute-howto-linkvnet-arm.md) . Queste reti virtuali possono trovarsi nella stessa sottoscrizione di Azure del circuito ExpressRoute oppure in una sottoscrizione diversa.
* Connettersi ai servizi di Azure e ai servizi cloud Microsoft tramite il peering Microsoft.

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>Stati del provisioning del circuito partner ExpressRoute

La sezione seguente descrive i diversi Stati del circuito ExpressRoute per il modello di connettività partner di ExpressRoute.
Ogni circuito partner ExpressRoute ha due stati:

* **ServiceProviderProvisioningState** rappresenta lo stato sul lato del provider di connettività. Può essere impostato su *NotProvisioned*, *Provisioning* o *Provisioned*. Per configurare il peering, il circuito ExpressRoute deve essere in uno stato di provisioning. **Questo stato riguarda solo i circuiti partner ExpressRoute e non viene visualizzato nelle proprietà di un circuito ExpressRoute Direct**.

* **Lo** stato rappresenta lo stato di provisioning Microsoft. Questa proprietà è impostata su Enabled quando si crea un circuito ExpressRoute

### <a name="possible-states-of-an-expressroute-circuit"></a>Stati possibili di un circuito ExpressRoute

Questa sezione descrive gli stati possibili di un circuito ExpressRoute creato nel modello di connettività partner di ExpressRoute.

**Al momento della creazione**

Il circuito ExpressRoute segnalerà gli Stati seguenti durante la creazione della risorsa.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**Quando il provider di connettività è in fase di provisioning del circuito**

Il circuito ExpressRoute segnala gli Stati seguenti mentre il provider di connettività sta lavorando per eseguire il provisioning del circuito.

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**Quando il provider di connettività ha completato il processo di provisioning**

Quando il provider di connettività ha effettuato correttamente il provisioning del circuito, il circuito ExpressRoute segnalerà gli Stati seguenti.

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**Quando il provider di connettività esegue il deprovisioning del circuito**

Se il circuito ExpressRoute deve essere sottoposta a deprovisioning, il circuito segnalerà gli Stati seguenti quando il provider di servizi ha completato il processo di deprovisioning.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

È possibile scegliere di abilitarlo nuovamente, se necessario, o eseguire i cmdlet di PowerShell per eliminare il circuito.  

> [!IMPORTANT]
> Non è possibile eliminare un circuito quando viene eseguito il provisioning o il provisioning di ServiceProviderProvisioningState. Il provider di connettività deve effettuare il deprovisioning del circuito prima che sia possibile eliminarlo. Microsoft continuerà a fatturare il circuito fino a quando la risorsa del circuito ExpressRoute non viene eliminata in Azure.
> 

## <a name="routing-session-configuration-state"></a>Stato di configurazione della sessione di routing

Lo stato di provisioning BGP segnala se la sessione BGP è stata abilitata in Microsoft Edge. Lo stato deve essere abilitato per l'uso del peering privato o Microsoft.

È importante controllare lo stato della sessione BGP soprattutto per il peering Microsoft. Oltre allo stato di provisioning BGP, esiste un altro stato denominato *prefissi pubblici annunciati*. Lo stato dei prefissi pubblici annunciati deve essere nello stato *configurato* , sia per la sessione BGP che per il routing per il funzionamento end-to-end. 

Se invece è impostato lo stato *validation needed* , la sessione BGP non è abilitata, poiché i prefissi annunciati non corrispondono al numero AS nei registri di routing.

> [!IMPORTANT]
> Se lo stato dei prefissi pubblici annunciati è in uno stato di *convalida manuale* , è necessario aprire un ticket di supporto con il [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e fornire l'evidenza che si è proprietari degli indirizzi IP annunciati insieme al numero di sistema autonomo associato.
> 
> 

## <a name="next-steps"></a>Passaggi successivi

* Configurare la connessione ExpressRoute.
  
  * [Creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configurare il routing](expressroute-howto-routing-arm.md)
  * [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)