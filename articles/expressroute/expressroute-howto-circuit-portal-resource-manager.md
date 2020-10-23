---
title: 'Avvio rapido: Creare e modificare un circuito con ExpressRoute - Portale di Azure'
description: Questa guida di avvio rapido illustra le procedure di creazione, provisioning, verifica, aggiornamento, eliminazione e deprovisioning di un circuito ExpressRoute tramite il portale di Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/21/2020
ms.author: duau
ms.openlocfilehash: b0fd844021e7398e061072d7939b782616580c1c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368690"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit"></a>Avvio rapido: Creare e modificare un circuito ExpressRoute

Questa guida di avvio rapido illustra come creare un circuito ExpressRoute di Azure usando il portale di Azure e il modello di distribuzione di Azure Resource Manager. È anche possibile controllare lo stato, aggiornare, eliminare o effettuare il deprovisioning di un circuito.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Prima di iniziare la configurazione, verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).
* È possibile [visualizzare un video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) prima di iniziare, per comprendere meglio la procedura.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Creare un circuito ExpressRoute ed eseguirne il provisioning

### <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

### <a name="create-a-new-expressroute-circuit"></a>Creare un nuovo circuito ExpressRoute

> [!IMPORTANT]
> Il circuito ExpressRoute viene addebitato dal momento in cui viene emessa una chiave di servizio. Verificare che l'operazione venga eseguita quando il provider di connettività è pronto a effettuare il provisioning del circuito.

È possibile creare un circuito ExpressRoute selezionando l'opzione che consente di creare una nuova risorsa. 

1. Nel menu del portale di Azure selezionare **Crea una risorsa**. Fare clic su **Rete** > **ExpressRoute**, come illustrato nell'immagine seguente:

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="Creare un circuito ExpressRoute":::

2. Dopo aver selezionato **ExpressRoute**, verrà visualizzata la pagina **Crea ExpressRoute**. Specificare **Gruppo di risorse**, **Area** e **Nome** per il circuito. Selezionare quindi **Next: Configurazione >** .

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Creare un circuito ExpressRoute":::

3. Quando si inseriscono i valori in questa pagina, assicurarsi di specificare il livello di SKU (Locale, Standard o Premium) e il modello di fatturazione della misurazione dei dati (Senza limiti o A consumo) corretti.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Creare un circuito ExpressRoute":::
    
    * L'opzione **Tipo di porta** determina se ci si connette a un provider di servizi o direttamente alla rete globale di Microsoft in una posizione di peering.
    * L'opzione **Crea nuovo o importa dalla versione classica** determina se viene creato un nuovo circuito o viene eseguita la migrazione di un circuito classico ad Azure Resource Manager.
    * **Provider** è il provider di servizi Internet a cui verrà richiesto il servizio.
    * **Località peer** è la posizione fisica del peering con Microsoft.

    > [!IMPORTANT]
    > La località peer indica la [posizione fisica](expressroute-locations.md) di peering con Microsoft. Questo percorso **non** è collegato alla proprietà "Location", ovvero all'area geografica in cui si trova il provider di risorse di rete di Azure. Dal momento che non sono collegati, è consigliabile scegliere un provider di risorse di rete geograficamente vicino alla posizione di peering del circuito.

    * **SKU** determina se deve essere abilitato il componente aggiuntivo ExpressRoute Locale, Standard o Premium. È possibile specificare **Locale** per ottenere lo SKU locale, **Standard** per ottenere lo SKU Standard o **Premium** per il componente aggiuntivo Premium. È possibile cambiare lo SKU per abilitare il componente aggiuntivo Premium.
    > [!IMPORTANT]
    > Non è possibile cambiare lo SKU da **Standard/Premium** a **Locale**.
    
    * L'opzione **Modello di fatturazione** determina il tipo di fatturazione. È possibile specificare **A consumo** per un piano dati a consumo e **Senza limiti** per un piano dati illimitato. È possibile cambiare il tipo di fatturazione da **A consumo** a **Senza limiti**.

    > [!IMPORTANT]
    > Non è possibile modificare il tipo da **Illimitato** ad **A consumo**.

    * **Consenti operazioni classiche** consentirà di collegare reti virtuali classiche al circuito.

### <a name="view-the-circuits-and-properties"></a>Visualizzare circuiti e proprietà

**Visualizzare tutti i circuiti**

È possibile visualizzare tutti i circuiti creati selezionando **Tutti i servizi > Rete > Circuiti ExpressRoute** nel menu a sinistra.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="Creare un circuito ExpressRoute":::

Tutti i circuiti ExpressRoute creati nella sottoscrizione verranno visualizzati qui.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="Creare un circuito ExpressRoute":::

**Visualizzare le proprietà**

Selezionare il circuito desiderato per visualizzare le relative proprietà. Nella pagina **Panoramica** per il circuito la chiave del servizio viene visualizzata nel campo della chiave del servizio. Fare riferimento alla chiave del servizio per il circuito e fornirla al provider di servizi per completare il processo di provisioning. La chiave del servizio è specifica del circuito.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Creare un circuito ExpressRoute":::

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Inviare la chiave di servizio al provider di connettività per il provisioning

In questa pagina **Stato provider** indica lo stato corrente del provisioning sul lato provider di servizi. **Stato circuito** indica lo stato sul lato Microsoft. Per altre informazioni sullo stato di provisioning dei circuiti, vedere l'articolo relativo ai [flussi di lavoro](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando si crea un nuovo circuito ExpressRoute, il circuito ha lo stato seguente:

Stato provider: **Senza provisioning**<BR>
Stato circuito: **Enabled**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Creare un circuito ExpressRoute":::

Il circuito passa allo stato seguente quando è in corso l'abilitazione da parte del provider di connettività:

Stato provider: **Provisioning**<BR>
Stato circuito: **Enabled**

Per poterlo usare, il circuito ExpressRoute deve avere lo stato seguente:

Stato provider: **Provisioning eseguito**<BR>
Stato circuito: **Enabled**

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Controllare periodicamente lo stato e la condizione della chiave del circuito

Selezionare il circuito desiderato per visualizzare le relative proprietà. Prima di continuare, controllare che **Stato provider** sia passato a **Provisioning eseguito**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Creare un circuito ExpressRoute":::

### <a name="create-your-routing-configuration"></a>Creare la configurazione di routing

Per istruzioni dettagliate, vedere l'articolo relativo alla [configurazione del routing per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) per creare e modificare i peering del circuito.

> [!IMPORTANT]
> Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (di solito una VPN IP, come MPLS), il provider di connettività configura e gestisce il routing per conto dell'utente.

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Collegare una rete virtuale a un circuito ExpressRoute

Collegare quindi una rete virtuale al circuito ExpressRoute. Fare riferimento all'articolo [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md) quando si usa il modello di distribuzione di Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Ottenere lo stato di un circuito ExpressRoute

È possibile visualizzare lo stato di un circuito selezionando il circuito e visualizzando la pagina Panoramica.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modifica di un circuito ExpressRoute

È possibile modificare determinate proprietà di un circuito ExpressRoute senza conseguenze per la connettività. È possibile modificare la larghezza di banda, lo SKU e il modello di fatturazione e consentire operazioni classiche nella pagina **Configurazione**. Per informazioni sui limiti e le limitazioni, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).

È possibile eseguire le attività seguenti senza tempi di inattività:

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

Per modificare un circuito ExpressRoute, selezionare **Configurazione**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Creare un circuito ExpressRoute":::

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning di un circuito ExpressRoute

Se lo stato di provisioning del provider del servizio del circuito ExpressRoute è **Provisioning in corso** o **Provisioning eseguito**, è necessario collaborare con il provider di servizi per eseguire il deprovisioning del circuito sul lato del provider. Le risorse continuano a essere riservate e la fatturazione continuerà a essere applicata finché il provider di servizi non avrà completato il deprovisioning del circuito e inviato una notifica a Microsoft.

> [!NOTE]
>* È necessario scollegare *tutte le reti virtuali* dal circuito ExpressRoute prima di effettuare il deprovisioning. Se l'operazione non riesce, controllare se sono presenti reti virtuali collegate al circuito.
>* Se il provider di servizi ha eseguito il deprovisioning del circuito, ovvero lo stato di provisioning del provider di servizi è impostato su **Senza provisioning**, è possibile eliminare il circuito. Viene così interrotta la fatturazione per il circuito.


## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eliminare il circuito ExpressRoute selezionando l'icona **Elimina**. Verificare che lo stato del provider sia *Senza provisioning* prima di procedere.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Creare un circuito ExpressRoute":::

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il circuito, continuare con i passaggi seguenti:

> [!div class="nextstepaction"]
> [Creare e modificare il routing per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
