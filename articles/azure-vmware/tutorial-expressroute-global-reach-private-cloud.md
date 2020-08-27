---
title: Associare gli ambienti locali a un cloud privato
description: In questa esercitazione per la soluzione Azure VMware verrà creato un peering con Copertura globale ExpressRoute a un cloud privato in una soluzione Azure VMware.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: db3f5988cb8c07d9b6e80f500ac6aff8f96dfded
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750441"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Esercitazione: Associare gli ambienti locali a un cloud privato

Copertura globale ExpressRoute connette l'ambiente locale ai cloud privati. La connessione Copertura globale ExpressRoute viene stabilita tra un circuito ExpressRoute del cloud privato e una connessione ExpressRoute esistente agli ambienti locali.  Sono disponibili istruzioni per la configurazione di Copertura globale ExpressRoute con l'interfaccia della riga di comando di Azure e PowerShell e sono stati ampliati i [comandi dell'interfaccia della riga di comando](../expressroute/expressroute-howto-set-global-reach-cli.md) con dettagli specifici ed esempi che consentono di configurare il peering Copertura globale ExpressRoute tra ambienti locali a un cloud privato della soluzione Azure VMware.   

Prima di abilitare la connettività tra due circuiti ExpressRoute usando Copertura globale ExpressRoute, vedere la documentazione su come [abilitare la connettività in sottoscrizioni di Azure diverse](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  Il circuito ExpressRoute che si usa quando si [configura la rete da Azure a cloud privato](tutorial-configure-networking.md) richiede la creazione e l'uso di chiavi di autorizzazione per il peering dei gateway ExpressRoute o di altri circuiti ExpressRoute con Copertura globale. È già stata usata una chiave di autorizzazione dal circuito ExpressRoute e ne verrà creata una seconda per eseguire il peering con il circuito ExpressRoute locale.

> [!TIP]
> Nel contesto di queste istruzioni, il circuito ExpressRoute locale è _circuito 1_ e il circuito ExpressRoute del cloud privato si trova in una sottoscrizione diversa ed è contrassegnato come _circuito 2_. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Sfruttare le istruzioni esistenti per la gestione dei circuiti ExpressRoute e dei peering Copertura globale ExpressRoute
> * Creare una chiave di autorizzazione per _circuito 2_, il circuito ExpressRoute del cloud privato
> * Usare l'interfaccia della riga di comando di Azure in un'istanza di Cloud Shell nella sottoscrizione di _circuito 1_ per abilitare il peering Copertura globale ExpressRoute dall'ambiente locale al cloud privato

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per questa esercitazione sono:
- Un cloud privato con il relativo circuito ExpressRoute associato a un gateway ExpressRoute in una rete virtuale di Azure: si tratta del _circuito 2_ dal punto di vista delle procedure di peering.
- Un circuito ExpressRoute separato e funzionante usato per connettere gli ambienti locali ad Azure: si tratta di _circuito 1_ dal punto di vista delle procedure di peering.
- Un [blocco di indirizzi di rete](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) non sovrapposti /29 per il peering Copertura globale ExpressRoute.

## <a name="create-an-expressroute-authorization-key-in-the-azure-vmware-solution-private-cloud"></a>Creare una chiave di autorizzazione ExpressRoute nel cloud privato della soluzione Azure VMware

1. Nella scheda **Panoramica** del cloud privato in Gestisci selezionare **Connettività > ExpressRoute > Richiedi una chiave di autorizzazione**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Selezionare Connettività > ExpressRoute > Richiedi una chiave di autorizzazione per avviare una nuova richiesta.":::

2. Immettere il nome per la chiave di autorizzazione e selezionare **Crea**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Fare clic su Crea per creare una nuova chiave di autorizzazione. ":::

   Una volta creata, la nuova chiave viene visualizzata nell'elenco delle chiavi di autorizzazione per il cloud privato. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Verificare che la nuova chiave di autorizzazione sia visualizzata nell'elenco di chiavi per il cloud privato. ":::

3. Prendere nota della chiave di autorizzazione e dell'ID ExpressRoute, insieme al blocco di indirizzi /29. Verranno usati nel passaggio successivo per completare il peering. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Eseguire il peering del cloud privato all'ambiente locale usando la chiave di autorizzazione

Ora che è stata creata una chiave di autorizzazione per il circuito ExpressRoute del cloud privato, è possibile associarla al circuito ExpressRoute locale.  Il peering viene eseguito dal circuito ExpressRoute locale usando l'interfaccia della riga di comando di Azure in un'istanza di Cloud Shell e l'ID risorsa e la chiave di autorizzazione del circuito ExpressRoute del cloud privato, creati nei passaggi precedenti.

> [!TIP]  
> Per brevità, nell'output del comando dell'interfaccia della riga di comando di Azure queste istruzioni possono usare un argomento [`–query` per eseguire una query JMESPath per visualizzare solo i risultati necessari](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).


1. Accedere al portale di Azure usando la stessa sottoscrizione del circuito ExpressRoute locale e aprire un'istanza di Cloud Shell. Lasciare la shell come Bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Accedere al portale di Azure e aprire un'istanza di Cloud Shell.":::
 
2. Nella riga di comando immettere il comando dell'interfaccia della riga di comando di Azure per creare il peering, usando le informazioni specifiche e l'ID risorsa, la chiave di autorizzazione e il blocco di rete CIDR /29. 

   Di seguito è riportato un esempio del comando che verrà usato e l'output che indica un peering riuscito. Il comando di esempio è basato sul comando usato nel [passaggio 3 di "Abilitare la connettività tra circuiti ExpressRoute in sottoscrizioni di Azure diverse"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Creare il peering Copertura globale ExpressRoute con il comando dell'interfaccia della riga di comando di Azure in un'istanza di Cloud Shell.":::
 
   A questo punto dovrebbe essere possibile connettersi dagli ambienti locali al cloud privato tramite il peering Copertura globale ExpressRoute.

> [!TIP]
> È possibile eliminare il peering appena creato seguendo le istruzioni per [disabilitare la connettività tra le reti locali](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks).


## <a name="next-steps"></a>Passaggi successivi

Se si prevede di usare Hybrid Cloud Extension (HCX) per eseguire la migrazione dei carichi di lavoro delle macchine virtuali nel cloud privato, usare la procedura [Installare HCX per la soluzione Azure VMware](hybrid-cloud-extension-installation.md).


<!-- LINKS - external-->

<!-- LINKS - internal -->
