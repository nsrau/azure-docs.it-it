---
title: 'Esercitazione: Creare e modificare un circuito con ExpressRoute'
description: Questa esercitazione illustra le procedure di creazione, provisioning, verifica, aggiornamento, eliminazione e deprovisioning di un circuito ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: duau
ms.openlocfilehash: e0ba14cd5db47c12435b2de35d0753b402c947ea
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566263"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Esercitazione: Creare e modificare un circuito ExpressRoute

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfaccia della riga di comando di Azure](howto-circuit-cli.md)
> * [Modello di Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Portale di Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (classic)](expressroute-howto-circuit-classic.md) (PowerShell (classico))
>

Questa esercitazione mostra come creare un circuito ExpressRoute di Azure usando il portale di Azure e il modello di distribuzione di Azure Resource Manager. È anche possibile controllare lo stato, aggiornare, eliminare o effettuare il deprovisioning di un circuito.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un circuito ExpressRoute
> * Ottenere lo stato corrente di un circuito
> * Modificare un circuito
> * Effettuare il deprovisioning ed eliminare un circuito

## <a name="before-you-begin"></a>Prima di iniziare

* Prima di iniziare la configurazione, verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).
* Verificare di avere accesso al [portale di Azure](https://portal.azure.com).
* Verificare di avere le autorizzazioni necessarie per creare nuove risorse di rete. Se non si hanno le autorizzazioni appropriate, contattare l'amministratore dell'account.
* È possibile [visualizzare un video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) prima di iniziare, per ottenere una comprensione migliore della procedura.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Creare un circuito ExpressRoute ed eseguirne il provisioning

### <a name="1-sign-in-to-the-azure-portal"></a>1. Accedere al portale di Azure

In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Creare un nuovo circuito ExpressRoute

> [!IMPORTANT]
> Il circuito ExpressRoute viene addebitato dal momento in cui viene emessa una chiave di servizio. Verificare che l'operazione venga eseguita quando il provider di connettività è pronto a effettuare il provisioning del circuito.

È possibile creare un circuito ExpressRoute selezionando l'opzione che consente di creare una nuova risorsa. 

1. Nel menu del portale di Azure selezionare **Crea una risorsa**. Fare clic su **Rete** > **ExpressRoute**, come illustrato nell'immagine seguente:

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="Creare un circuito ExpressRoute":::

2. Dopo aver fatto clic su **ExpressRoute** verrà visualizzata la pagina **Crea ExpressRoute**. Specificare **Gruppo di risorse**, **Area** e **Nome** per il circuito. Fare quindi clic su **Avanti: Configurazione >** .

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Configurare il gruppo di risorse e l'area":::

3. Quando si inseriscono i valori in questa pagina, assicurarsi di specificare il livello di SKU (Locale, Standard o Premium) e il modello di fatturazione della misurazione dei dati (Senza limiti o A consumo) corretti.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Configurare il circuito":::
    
    * L'opzione **Tipo porta** determina se ci si connette a un provider di servizi o direttamente alla rete globale di Microsoft in una posizione di peering.
    * L'opzione **Crea nuovo o importa dalla versione classica** determina se si intende creare un nuovo circuito o se si sta eseguendo la migrazione di un circuito classico ad ARM.
    * **Provider** è il provider di servizi Internet a cui verrà richiesto il servizio.
    * **Località peer** è la posizione fisica di peering con Microsoft.

    > [!IMPORTANT]
    > La località peer indica la [posizione fisica](expressroute-locations.md) di peering con Microsoft. Questo percorso **non** è collegato alla proprietà "Location", ovvero all'area geografica in cui si trova il provider di risorse di rete di Azure. Dal momento che non sono collegati, è consigliabile scegliere un provider di risorse di rete geograficamente vicino alla posizione di peering del circuito.

    * **SKU** determina se deve essere abilitato il componente aggiuntivo ExpressRoute Locale, Standard o Premium. È possibile specificare **Locale** per ottenere lo SKU locale, **Standard** per ottenere lo SKU Standard o **Premium** per il componente aggiuntivo Premium. Si noti che è possibile cambiare lo SKU per abilitare il componente aggiuntivo Premium.
    > [!IMPORTANT]
    > Non è possibile cambiare lo SKU da **Standard/Premium** a **Locale**.
    
    * L'opzione **Modello di fatturazione** determina il tipo di fatturazione. È possibile specificare **A consumo** per un piano dati a consumo e **Senza limiti** per un piano dati illimitato. Si noti che è possibile modificare il tipo di fatturazione da **A consumo** in **Illimitato**.

    > [!IMPORTANT]
    > Non è possibile modificare il tipo da **Illimitato** ad **A consumo**.

    * **Consenti operazioni classiche** consentirà di collegare reti virtuali classiche al circuito.

### <a name="3-view-the-circuits-and-properties"></a>3. Visualizzare circuiti e proprietà

**Visualizzare tutti i circuiti**

È possibile visualizzare tutti i circuiti creati selezionando **Tutti i servizi > Rete > Circuiti ExpressRoute** nel menu a sinistra.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="Menu dei circuiti ExpressRoute":::

Tutti i circuiti Expressroute creati nella sottoscrizione verranno visualizzati qui.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="Elenco dei circuiti ExpressRoute":::

**Visualizzare le proprietà**

Selezionare il circuito desiderato per visualizzare le relative proprietà. Nella pagina **Panoramica** per il circuito la chiave del servizio viene visualizzata nel campo della chiave del servizio. Fare riferimento alla chiave del servizio per il circuito e fornirla al provider di servizi per completare il processo di provisioning. La chiave del servizio è specifica del circuito.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Visualizzare le proprietà":::

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Inviare la chiave di servizio al provider di connettività per il provisioning

In questa pagina, **Stato provider** offre informazioni sullo stato attuale di provisioning sul lato provider del servizio. **Stato circuito** indica lo stato sul lato Microsoft. Per altre informazioni sullo stato di provisioning dei circuiti, vedere l'articolo relativo ai [flussi di lavoro](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando si crea un nuovo circuito ExpressRoute, il circuito ha lo stato seguente:

Stato provider: **Senza provisioning**<BR>
Stato circuito: **Enabled**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Avvio del processo di provisioning":::

Il circuito passa allo stato seguente quando è in corso l'abilitazione da parte del provider di connettività:

Stato provider: **Provisioning**<BR>
Stato circuito: **Enabled**

Per poterlo usare, un circuito ExpressRoute deve avere lo stato seguente:

Stato provider: **Provisioning eseguito**<BR>
Stato circuito: **Enabled**

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Controllare periodicamente lo stato e la condizione della chiave del circuito

Selezionare il circuito desiderato per visualizzare le relative proprietà. Prima di continuare, controllare che **Stato provider** sia passato a **Provisioning eseguito**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Stato del circuito e del provider":::

### <a name="6-create-your-routing-configuration"></a>6. Creare la configurazione di routing

Per istruzioni dettagliate, vedere l'articolo relativo alla [configurazione del routing per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) per creare e modificare i peering del circuito.

> [!IMPORTANT]
> Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (di solito una VPN IP, come MPLS), il provider di connettività configura e gestisce il routing per conto dell'utente.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Collegare una rete virtuale a un circuito ExpressRoute

Collegare quindi una rete virtuale al circuito ExpressRoute. Fare riferimento all'articolo [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md) quando si usa il modello di distribuzione di Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Ottenere lo stato di un circuito ExpressRoute

È possibile visualizzare lo stato di un circuito selezionando il circuito e visualizzando la pagina Panoramica.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modifica di un circuito ExpressRoute

È possibile modificare determinate proprietà di un circuito ExpressRoute senza conseguenze per la connettività. È possibile modificare la larghezza di banda, lo SKU e il modello di fatturazione e consentire operazioni classiche nella pagina **Configurazione**. Per informazioni sui limiti e le limitazioni, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).

È possibile eseguire le attività seguenti evitando tempi di inattività:

* Abilitare o disabilitare un componente aggiuntivo ExpressRoute Premium per il circuito ExpressRoute.

> [!IMPORTANT]
  > Il cambiamento dello SKU da **Standard/Premium** a **Locale** non è supportato.

* Aumentare la larghezza di banda del circuito ExpressRoute, a condizione che nella porta sia disponibile capacità.

  > [!IMPORTANT]
  > Il downgrade della larghezza di banda di un circuito non è supportato.

* Modificare il piano di misurazione da *Dati a consumo* a *Dati senza limiti*.

  > [!IMPORTANT]
  > La modifica del piano di misurazione da **Dati senza limiti** a **Dati a consumo** non è supportata.

* È possibile abilitare e disabilitare l'opzione *Consenti operazioni classiche*.
  > [!IMPORTANT]
  > Se la capacità sulla porta esistente non è sufficiente, potrebbe essere necessario ricreare il circuito ExpressRoute. Il circuito non può essere aggiornato se in tale posizione non è disponibile capacità aggiuntiva.
  >
  > Anche se è possibile aggiornare facilmente la banda larga, non è possibile ridurre la larghezza di banda di un circuito ExpressRoute senza interruzioni. Il downgrade della larghezza di banda richiede il deprovisioning del circuito ExpressRoute e quindi il provisioning di un nuovo circuito ExpressRoute.
  >
  > L'operazione di disabilitazione del componente aggiuntivo Premium può avere esito negativo se si usano risorse superiori a quelle consentite per il circuito standard.

Per modificare un circuito ExpressRoute, fare clic su **Configurazione**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Modificare il circuito":::

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning ed eliminazione di un circuito ExpressRoute

Se lo stato di provisioning del provider del servizio del circuito ExpressRoute è **Provisioning in corso** o **Provisioning eseguito**, è necessario collaborare con il provider di servizi per eseguire il deprovisioning del circuito sul lato del provider. Le risorse continuano a essere riservate e la fatturazione continuerà a essere applicata finché il provider di servizi non avrà completato il deprovisioning del circuito e inviato una notifica a Microsoft.

> [!NOTE]
>* È necessario scollegare *tutte le reti virtuali* dal circuito ExpressRoute prima di effettuare il deprovisioning. Se l'operazione non riesce, controllare se sono presenti reti virtuali collegate al circuito.
>* Se il provider di servizi ha eseguito il deprovisioning del circuito, ovvero lo stato di provisioning del provider di servizi è impostato su **Senza provisioning**, è possibile eliminare il circuito. Viene così interrotta la fatturazione per il circuito.

È possibile eliminare il circuito ExpressRoute selezionando l'icona **Elimina**. 

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Eliminare il circuito":::

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il circuito, continuare con i passaggi seguenti:

* [Creare e modificare il routing per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Collegare la rete virtuale al circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
