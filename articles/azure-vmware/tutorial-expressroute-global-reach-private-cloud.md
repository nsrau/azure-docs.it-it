---
title: Esercitazione - Associare gli ambienti locali a un cloud privato
description: Informazioni su come creare il peering di Copertura globale ExpressRoute a un cloud privato in una soluzione Azure VMware.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 07b7e1c2636f3754eda56af574586a1027403d3e
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576728"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Esercitazione: Associare gli ambienti locali a un cloud privato

Copertura globale ExpressRoute connette l'ambiente locale al cloud privato della soluzione Azure VMware. La connessione Copertura globale ExpressRoute viene stabilita tra un circuito ExpressRoute del cloud privato e una connessione ExpressRoute esistente agli ambienti locali. 

Il circuito ExpressRoute che si usa quando si [configura la rete da Azure a cloud privato](tutorial-configure-networking.md) richiede la creazione e l'uso di chiavi di autorizzazione per il peering dei gateway ExpressRoute o di altri circuiti ExpressRoute con Copertura globale. È già stata usata una chiave di autorizzazione dal circuito ExpressRoute e in questa esercitazione ne verrà creata una seconda per eseguire il peering con il circuito ExpressRoute locale.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una seconda chiave di autorizzazione per _circuito 2_, il circuito ExpressRoute del cloud privato
> * Usare il [portale di Azure](#azure-portal-method) o l'[interfaccia della riga di comando di Azure in un metodo di Cloud Shell](#azure-cli-in-a-cloud-shell-method) nella sottoscrizione del _circuito 1_ per abilitare il peering da locale a cloud privato di Copertura globale ExpressRoute


## <a name="before-you-begin"></a>Prima di iniziare

Prima di abilitare la connettività tra due circuiti ExpressRoute usando Copertura globale ExpressRoute, vedere la documentazione su come [abilitare la connettività in sottoscrizioni di Azure diverse](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Prerequisiti

1. Connettività stabilita verso e da un cloud privato della soluzione Azure VMware con peering del circuito ExpressRoute con un gateway ExpressRoute in una rete virtuale di Azure, ovvero _circuito 2_ delle procedure di peering.  
1. Un circuito ExpressRoute separato e funzionante usato per connettere gli ambienti locali ad Azure, ovvero il _circuito 1_ dal punto di vista delle procedure di peering.
1. Un [blocco di indirizzi di rete](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) non sovrapposti /29 per il peering Copertura globale ExpressRoute.

> [!TIP]
> Nel contesto di questi prerequisiti, il circuito ExpressRoute locale è _circuito 1_ e il circuito ExpressRoute del cloud privato si trova in una sottoscrizione diversa ed è contrassegnato come _circuito 2_. 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>Creare una chiave di autorizzazione ExpressRoute nel cloud privato

1. Nella scheda **Panoramica** del cloud privato in Gestisci selezionare **Connettività > ExpressRoute > Richiedi una chiave di autorizzazione**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Selezionare Connettività > ExpressRoute > Richiedi una chiave di autorizzazione per avviare una nuova richiesta.":::

2. Immettere il nome per la chiave di autorizzazione e selezionare **Crea**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Selezionare Connettività > ExpressRoute > Richiedi una chiave di autorizzazione per avviare una nuova richiesta.":::

   Una volta creata, la nuova chiave viene visualizzata nell'elenco delle chiavi di autorizzazione per il cloud privato. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Selezionare Connettività > ExpressRoute > Richiedi una chiave di autorizzazione per avviare una nuova richiesta.":::

3. Prendere nota della chiave di autorizzazione e dell'ID ExpressRoute, insieme al blocco di indirizzi /29. Verranno usati nel passaggio successivo per completare il peering. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Eseguire il peering del cloud privato all'ambiente locale usando la chiave di autorizzazione

Ora che è stata creata una chiave di autorizzazione per il circuito ExpressRoute del cloud privato, è possibile associarla al circuito ExpressRoute locale.  Il peering viene eseguito dal punto di vista del circuito ExpressRoute locale nel [portale di Azure](#azure-portal-method) o tramite l'[interfaccia della riga di comando di Azure in un'istanza di Cloud Shell](#azure-cli-in-a-cloud-shell-method). Per completare il peering con entrambi i metodi verranno usati l'ID della risorsa e la chiave di autorizzazione del circuito ExpressRoute del cloud privato, creati nei passaggi precedenti.

### <a name="azure-portal-method"></a>Metodo basato sul portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) usando la stessa sottoscrizione del circuito ExpressRoute locale.

1. Nella scheda **Panoramica** del cloud privato in Gestisci selezionare **Connettività > Copertura globale ExpressRoute > Aggiungi**.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Selezionare Connettività > ExpressRoute > Richiedi una chiave di autorizzazione per avviare una nuova richiesta.":::

1. È possibile creare una connessione cloud locale eseguendo una di queste operazioni:

   - Selezionare il circuito ExpressRoute dall'elenco.
   - Se è disponibile un ID circuito, copiarlo e incollarlo.

1. Selezionare **Connetti**. La nuova connessione viene visualizzata nell'elenco di connessioni cloud locali.  

>[!TIP]
>È possibile eliminare o disconnettere una connessione dall'elenco selezionando **Altro**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Selezionare Connettività > ExpressRoute > Richiedi una chiave di autorizzazione per avviare una nuova richiesta.":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Metodo basato sull'interfaccia della riga di comando di Azure in un'istanza di Cloud Shell

I [comandi dell'interfaccia della riga di comando](../expressroute/expressroute-howto-set-global-reach-cli.md) sono stati ampliati con dettagli specifici ed esempi che consentono di configurare il peering Copertura globale ExpressRoute tra ambienti locali a un cloud privato della soluzione Azure VMware.  

> [!TIP]  
> Per brevità, nell'output del comando dell'interfaccia della riga di comando di Azure queste istruzioni possono usare un argomento [`–query` per eseguire una query JMESPath per visualizzare solo i risultati necessari](https://docs.microsoft.com/cli/azure/query-azure-cli).


1. Accedere al portale di Azure usando la stessa sottoscrizione del circuito ExpressRoute locale e aprire un'istanza di Cloud Shell. Lasciare la shell come Bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Selezionare Connettività > ExpressRoute > Richiedi una chiave di autorizzazione per avviare una nuova richiesta.":::
 
2. Nella riga di comando immettere il comando dell'interfaccia della riga di comando di Azure per creare il peering, usando le informazioni specifiche e l'ID risorsa, la chiave di autorizzazione e il blocco di rete CIDR /29. 

   Di seguito è riportato un esempio del comando che verrà usato e l'output che indica un peering riuscito. Il comando di esempio è basato sul comando usato nel [passaggio 3 di "Abilitare la connettività tra circuiti ExpressRoute in sottoscrizioni di Azure diverse"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Selezionare Connettività > ExpressRoute > Richiedi una chiave di autorizzazione per avviare una nuova richiesta.":::
 
   A questo punto dovrebbe essere possibile connettersi dagli ambienti locali al cloud privato tramite il peering Copertura globale ExpressRoute.

> [!TIP]
> È possibile eliminare il peering appena creato seguendo le istruzioni per [disabilitare la connettività tra le reti locali](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks).


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come creare una seconda chiave di autorizzazione per il circuito ExpressRoute del cloud privato ed è stato abilitato il peering di Copertura globale di ExpressRoute da locale al cloud privato. 

Per informazioni su come distribuire e configurare una soluzione VMware HCX per il cloud privato della soluzione Azure VMware, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Distribuire e configurare VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
