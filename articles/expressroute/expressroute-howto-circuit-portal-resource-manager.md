---
title: 'Creare e modificare un circuito ExpressRoute: Portale di Azure| Microsoft Docs'
description: Questo articolo descrive le procedure di creazione, provisioning, verifica, aggiornamento, eliminazione e deprovisioning di un circuito ExpressRoute.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68d59d59-ed4d-482f-9cbc-534ebb090613
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/07/2017
ms.author: cherylmc;ganesr
ms.openlocfilehash: e3721cd3c031622788f553e71a6555b844f3f7dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Creare e modificare un circuito ExpressRoute
> [!div class="op_single_selector"]
> * [Portale di Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfaccia della riga di comando di Azure](howto-circuit-cli.md)
> * [Video - Portale di Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (classico)](expressroute-howto-circuit-classic.md)
>

Questo articolo descrive la procedura di creazione di un circuito ExpressRoute di Azure usando il portale di Azure e il modello di distribuzione di Azure Resource Manager. I passaggi seguenti descrivono anche come controllare lo stato del circuito ed eseguirne l'aggiornamento, l'eliminazione e il deprovisioning.


## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).
* Verificare di avere accesso al [portale di Azure](https://portal.azure.com).
* Verificare di avere le autorizzazioni necessarie per creare nuove risorse di rete. Se non si hanno le autorizzazioni appropriate, contattare l'amministratore dell'account.
* È possibile [visualizzare un video](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) prima di iniziare, per ottenere una comprensione migliore della procedura.

## <a name="create-and-provision-an-expressroute-circuit"></a>Creare un circuito ExpressRoute ed eseguirne il provisioning
### <a name="1-sign-in-to-the-azure-portal"></a>1. Accedere al portale di Azure
In un browser passare al [portale di Azure](http://portal.azure.com) e accedere con l'account Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Creare un nuovo circuito ExpressRoute
> [!IMPORTANT]
> Il circuito ExpressRoute viene addebitato dal momento in cui emessa una chiave di servizio. Verificare che l'operazione venga eseguita quando il provider di connettività è pronto a effettuare il provisioning del circuito.
> 
> 

1. È possibile creare un circuito ExpressRoute selezionando l'opzione che consente di creare una nuova risorsa. Fare clic su **Nuovo** > **Rete** > **ExpressRoute**, come illustrato nell'immagine seguente:
   
    ![Creare un circuito ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Dopo aver fatto clic su **ExpressRoute**, verrà visualizzato il pannello **Crea un circuito ExpressRoute**. Quando si compila questo pannello, verificare che siano specificati i valori corretti per il livello SKU e la misurazione dei dati.
   
   * **livello** determina se è abilitato un componente aggiuntivo ExpressRoute Standard o ExpressRoute Premium. È possibile specificare **Standard** per ottenere lo SKU Standard o **Premium** per il componente aggiuntivo Premium.
   * **misurazione dei dati** determina il tipo di fatturazione. È possibile specificare **A consumo** per un piano dati a consumo e **Senza limiti** per un piano dati illimitato. Si noti che è possibile modificare il tipo di fatturazione da **A consumo** a **Senza limiti**, ma non è possibile passare da **Senza limiti** ad **A consumo**.
     
     ![Configurazione del livello SKU e misurazione dei dati](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)

> [!IMPORTANT]
> Tenere presente che il percorso di Peering indica la [posizione fisica](expressroute-locations.md) in cui si esegue il peering con Microsoft. Questo percorso **non** è collegato alla proprietà "Location", ovvero all'area geografica in cui si trova il provider di risorse di rete di Azure. Dal momento che non sono collegati, è consigliabile scegliere un provider di risorse di rete geograficamente vicino alla posizione di peering del circuito. 
> 
> 

### <a name="3-view-the-circuits-and-properties"></a>3. Visualizzare circuiti e proprietà
**Visualizzare tutti i circuiti**

È possibile visualizzare tutti i circuiti creati selezionando **Tutte le risorse** dal menu a sinistra.

![Visualizzazione dei circuiti](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Visualizzare le proprietà**

    You can view the properties of the circuit by selecting it. On this blade, note the service key for the circuit. You must copy the circuit key for your circuit and pass it down to the service provider to complete the provisioning process. The circuit key is specific to your circuit.

![Visualizza proprietà](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Inviare la chiave di servizio al provider di connettività per il provisioning
In questo pannello **Stato provider** offre informazioni sullo stato di provisioning corrente sul lato provider del servizio. **Stato circuito** indica lo stato sul lato Microsoft. Per altre informazioni sullo stato di provisioning dei circuiti, vedere l'articolo relativo ai [flussi di lavoro](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando si crea un nuovo circuito ExpressRoute, il circuito ha lo stato seguente:

Stato provider: Senza provisioning<BR>
Stato circuito: Abilitato

![Avvio del processo di provisioning](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)

Il circuito passa allo stato seguente quando è in corso l'abilitazione da parte del provider di connettività:

Stato provider: Provisioning in corso<BR>
Stato circuito: Abilitato

Per poterlo usare, un circuito ExpressRoute deve avere lo stato seguente:

Stato provider: Provisioning eseguito<BR>
Stato circuito: Abilitato

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Controllare periodicamente lo stato e la condizione della chiave del circuito
Selezionare il circuito desiderato per visualizzare le relative proprietà. Prima di continuare, controllare che **Stato provider** sia passato a **Provisioning eseguito**.

![Stato del circuito e del provider](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Creare la configurazione di routing
Per istruzioni dettagliate, vedere l'articolo relativo alla [configurazione del routing per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) per creare e modificare i peering del circuito.

> [!IMPORTANT]
> Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (di solito un IP VPN, come MPLS), il provider di connettività configurerà e gestirà il routing per conto dell'utente.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Collegare una rete virtuale a un circuito ExpressRoute
Collegare quindi una rete virtuale al circuito ExpressRoute. Fare riferimento all'articolo [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md) quando si usa il modello di distribuzione di Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Ottenere lo stato di un circuito ExpressRoute
Selezionare un circuito per visualizzarne lo stato. 

![Stato di un circuito ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

## <a name="modifying-an-expressroute-circuit"></a>Modifica di un circuito ExpressRoute
È possibile modificare determinate proprietà di un circuito ExpressRoute senza conseguenze per la connettività.

È possibile eseguire le operazioni seguenti senza tempi di inattività:

* Abilitare o disabilitare un componente aggiuntivo ExpressRoute Premium per il circuito ExpressRoute.
* Aumentare la larghezza di banda del circuito ExpressRoute, a condizione che sulla porta sia disponibile capacità. Si noti che il downgrade della larghezza di banda di un circuito non è supportato. 
* Modificare il piano di misurazione da Dati a consumo a Dati senza limiti. Si noti che la modifica del piano di misurazione da Dati senza limiti a Dati a consumo non è supportata.
* È possibile abilitare e disabilitare l'opzione *Consenti operazioni classiche*.

Per altre informazioni su limiti e limitazioni, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).

Per modificare un circuito ExpressRoute, fare clic su **Configurazione**, come illustrato nella figura seguente.

![Modificare il circuito](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

È possibile modificare larghezza di banda, SKU e modello di fatturazione e consentire operazioni classiche nel pannello di configurazione.

> [!IMPORTANT]
> Se la capacità sulla porta esistente non è sufficiente, potrebbe essere necessario ricreare il circuito ExpressRoute. Il circuito non può essere aggiornato se in tale posizione non è disponibile capacità aggiuntiva.
>
> Non è possibile ridurre la larghezza di banda di un circuito ExpressRoute senza interruzioni. Il downgrade della larghezza di banda richiede il deprovisioning del circuito ExpressRoute e quindi il provisioning di un nuovo circuito ExpressRoute.
> 
> La disabilitazione dell'operazione aggiuntiva premium può avere esito negativo se si usano più risorse di quelle consentite per il circuito standard.
> 
> 

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Deprovisioning ed eliminazione di un circuito ExpressRoute
È possibile eliminare il circuito ExpressRoute selezionando l'icona di **eliminazione** . Tenere presente quanto segue:

* È necessario scollegare tutte le reti virtuali dal circuito ExpressRoute. Se l'operazione non riesce, controllare se sono presenti reti virtuali collegate al circuito.
* Se lo stato di provisioning del provider del servizio del circuito ExpressRoute è **Provisioning in corso** o **Provisioning eseguito**, è necessario collaborare con il provider di servizi per eseguire il deprovisioning del circuito sul lato del provider. Le risorse continueranno a essere riservate e la fatturazione continuerà a essere applicata finché il provider di servizi non avrà completato il deprovisioning del circuito e inviato una notifica a Microsoft.
* Se il provider di servizi ha eseguito il deprovisioning del circuito, ovvero lo stato di provisioning del provider di servizi è impostato su **Senza provisioning**, è possibile eliminare il circuito. Non verrà più applicata la fatturazione corrispondente.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato il circuito, verificare di eseguire le operazioni seguenti:

* [Creare e modificare il routing per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Collegare la rete virtuale al circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

