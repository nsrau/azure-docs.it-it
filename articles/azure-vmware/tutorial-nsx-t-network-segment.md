---
title: Esercitazione - Creare un segmento di rete NSX-T nella soluzione Azure VMware
description: Informazioni su come creare i segmenti di rete NSX-T usati per le macchine virtuali in vCenter
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 780cac15efc043b9ae44b77af1234adca3fec5a1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254534"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>Esercitazione: Creare un segmento di rete NSX-T nella soluzione Azure VMware

I segmenti di rete creati in NSX-T Manager vengono usati come reti per le macchine virtuali in vCenter. Le macchine virtuali create in vCenter vengono posizionate sui segmenti di rete creati in NSX-T e sono visibili in vCenter.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Spostarsi in NSX-T Manager per aggiungere segmenti di rete
> * Aggiungere un nuovo segmento di rete
> * Osservare il nuovo segmento di rete in vCenter

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario un cloud privato della soluzione Azure VMware con accesso alle interfacce di gestione vCenter e NSX-T Manager. Per altre informazioni, vedere l'esercitazione [Configurare la rete per il cloud privato VMware in Azure](tutorial-configure-networking.md).

## <a name="provision-a-network-segment-in-nsx-t"></a>Effettuare il provisioning di un segmento di rete in NSX-T

1. In vCenter per il cloud privato selezionare **SDDC-Datacenter > Networks** (Data center-Data center software defined > Reti) e osservare che non sono ancora presenti reti.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="In vCenter per il cloud privato selezionare SDDC-Datacenter > Networks (Data center-Data center software defined > Reti) e osservare che non sono ancora presenti reti.":::

1. In NSX-T Manager per il cloud privato selezionare **Networking** (Rete).

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="In vCenter per il cloud privato selezionare SDDC-Datacenter > Networks (Data center-Data center software defined > Reti) e osservare che non sono ancora presenti reti.":::

1. Selezionare **Segmenti**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="In vCenter per il cloud privato selezionare SDDC-Datacenter > Networks (Data center-Data center software defined > Reti) e osservare che non sono ancora presenti reti.":::

1. Nella pagina di panoramica dei segmenti NSX-T selezionare **AGGIUNGI SEGMENTO**. Vengono creati tre segmenti come parte del provisioning del cloud privato e non possono essere usati per le macchine virtuali.  A tale scopo, sarà necessario aggiungere un nuovo segmento di rete.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="In vCenter per il cloud privato selezionare SDDC-Datacenter > Networks (Data center-Data center software defined > Reti) e osservare che non sono ancora presenti reti.":::

1. Assegnare un nome al segmento, scegliere il gateway Tier1 preconfigurato (TNTxx-T1) come **Gateway connesso**, lasciare il valore di **Tipo** come Flessibile, scegliere la **zona di trasporto** overlay preconfigurata (TNTxx-OVERLAY-TZ), quindi selezionare Set Subnets (Imposta subnet). Per tutte le altre impostazioni in questa sezione, per **PORTE** e per **SEGMENT PROFILES** (PROFILI SEGMENTO) è possibile lasciare i valori predefiniti, così come la configurazione.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="In vCenter per il cloud privato selezionare SDDC-Datacenter > Networks (Data center-Data center software defined > Reti) e osservare che non sono ancora presenti reti.":::

1. Impostare l'indirizzo IP del gateway per il nuovo segmento e quindi selezionare **AGGIUNGI**. L'indirizzo IP usato deve trovarsi in un blocco di indirizzi RFC1918 non sovrapposto, che garantisce la possibilità di connettersi alle macchine virtuali nel nuovo segmento.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="In vCenter per il cloud privato selezionare SDDC-Datacenter > Networks (Data center-Data center software defined > Reti) e osservare che non sono ancora presenti reti.":::

1. Applicare il nuovo segmento di rete selezionando **APPLICA** e quindi fare clic su **SALVA** per salvare la configurazione.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="In vCenter per il cloud privato selezionare SDDC-Datacenter > Networks (Data center-Data center software defined > Reti) e osservare che non sono ancora presenti reti.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="In vCenter per il cloud privato selezionare SDDC-Datacenter > Networks (Data center-Data center software defined > Reti) e osservare che non sono ancora presenti reti.":::

1. A questo punto, il nuovo segmento di rete è stato creato e si rifiuterà l'opzione per continuare a configurare il segmento selezionando **NO**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="In vCenter per il cloud privato selezionare SDDC-Datacenter > Networks (Data center-Data center software defined > Reti) e osservare che non sono ancora presenti reti.":::

1. Verificare che il nuovo segmento di rete sia presente in NSX-T selezionando **Networking > Segments** (Rete > Segmenti) e assicurarsi che il nuovo segmento sia visualizzato nell'elenco (in questo caso, "ls01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="In vCenter per il cloud privato selezionare SDDC-Datacenter > Networks (Data center-Data center software defined > Reti) e osservare che non sono ancora presenti reti.":::

1. Verificare che il nuovo segmento di rete sia presente in vCenter selezionando **Networking > SDDC-Datacenter** (Rete > Data center- Data center software-defined) e assicurarsi che il nuovo segmento sia visualizzato nell'elenco (in questo caso, "ls01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="In vCenter per il cloud privato selezionare SDDC-Datacenter > Networks (Data center-Data center software defined > Reti) e osservare che non sono ancora presenti reti.":::

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati i segmenti di rete NSX-T usati per le macchine virtuali in vCenter. È ora possibile [creare una raccolta contenuto per distribuire le macchine virtuali nella soluzione Azure VMware](deploy-vm-content-library.md) ed effettuare il provisioning di una macchina virtuale nella rete creata in questa esercitazione.

Se non si vuole eseguire questa operazione, passare all'esercitazione successiva per ottenere informazioni su come creare il peering di Copertura globale ExpressRoute a un cloud privato in una soluzione Azure VMware.

> [!div class="nextstepaction"]
> [Associare gli ambienti locali a un cloud privato](tutorial-expressroute-global-reach-private-cloud.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
