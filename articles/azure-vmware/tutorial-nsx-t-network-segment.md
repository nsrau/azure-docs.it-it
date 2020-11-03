---
title: Esercitazione - Creare un segmento di rete NSX-T nella soluzione Azure VMware
description: Informazioni su come creare i segmenti di rete NSX-T usati per le macchine virtuali in vCenter
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: f88f186d2af10bcc114d64920a3ac489ef7be54f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367670"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>Esercitazione: Creare un segmento di rete NSX-T nella soluzione Azure VMware

Le macchine virtuali (VM) create in vCenter vengono posizionate sui segmenti di rete creati in NSX-T e sono visibili in vCenter.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Spostarsi in NSX-T Manager per aggiungere segmenti di rete
> * Aggiungere un nuovo segmento di rete
> * Osservare il nuovo segmento di rete in vCenter

## <a name="prerequisites"></a>Prerequisiti

È necessario avere un cloud privato della soluzione Azure VMware con accesso alle interfacce di gestione di vCenter e NSX-T Manager. Per altre informazioni, vedere l'esercitazione [Configurare la rete](tutorial-configure-networking.md).

## <a name="provision-a-network-segment-in-nsx-t"></a>Effettuare il provisioning di un segmento di rete in NSX-T

1. In vCenter per il cloud privato selezionare **SDDC-Datacenter > Networks** (Data center-Data center software defined > Reti) e osservare che non sono ancora presenti reti.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="In vCenter per il cloud privato selezionare SDDC-Datacenter > Networks (Data center-Data center software defined > Reti) e osservare che non sono ancora presenti reti.":::

1. In NSX-T Manager per il cloud privato selezionare **Networking** (Rete).

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="In NSX-T Manager per il cloud privato selezionare Networking (Rete).":::

1. Selezionare **Segmenti**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="Selezionare Segmenti nella pagina Panoramica della rete.":::

1. Nella pagina di panoramica dei segmenti NSX-T selezionare **AGGIUNGI SEGMENTO**. Vengono creati tre segmenti come parte del provisioning del cloud privato e non possono essere usati per le macchine virtuali.  A tale scopo, sarà necessario aggiungere un nuovo segmento di rete.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="Nella pagina di panoramica dei segmenti NSX-T selezionare ADD SEGMENT (AGGIUNGI SEGMENTO).":::

1. Assegnare un nome al segmento, scegliere il gateway Tier1 preconfigurato (TNTxx-T1) come **Gateway connesso** , lasciare il valore di **Tipo** come Flessibile, scegliere la **zona di trasporto** overlay preconfigurata (TNTxx-OVERLAY-TZ), quindi selezionare Set Subnets (Imposta subnet). Per tutte le altre impostazioni in questa sezione, per **PORTE** e per **SEGMENT PROFILES** (PROFILI SEGMENTO) è possibile lasciare i valori predefiniti, così come la configurazione.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="Impostare Nome del segmento, Gateway connesso, Tipo e Zona di trasporto, quindi selezionare&quot;Imposta subnet.":::

1. Impostare l'indirizzo IP del gateway per il nuovo segmento e quindi selezionare **AGGIUNGI**. L'indirizzo IP usato deve trovarsi in un blocco di indirizzi RFC1918 non sovrapposto, che garantisce la possibilità di connettersi alle macchine virtuali nel nuovo segmento.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="Impostare l'indirizzo IP del gateway per il nuovo segmento e quindi selezionare ADD (AGGIUNGI).":::

1. Applicare il nuovo segmento di rete selezionando **APPLICA** e quindi fare clic su **SALVA** per salvare la configurazione.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="Fare clic su APPLICA per applicare il nuovo segmento di rete alla configurazione NSX-T.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="Fare clic su SALVA per salvare il nuovo segmento di rete nella configurazione NSX-T.":::

1. A questo punto, il nuovo segmento di rete è stato creato e si rifiuterà l'opzione per continuare a configurare il segmento selezionando **NO**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Rifiutare di configurare ulteriormente il segmento di rete appena creato selezionando NO.":::

1. Verificare che il nuovo segmento di rete sia presente in NSX-T selezionando **Networking > Segments** (Rete > Segmenti) e assicurarsi che il nuovo segmento sia visualizzato nell'elenco (in questo caso, "ls01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Verificare che il nuovo segmento di rete sia presente in NSX-T.":::

1. Verificare che il nuovo segmento di rete sia presente in vCenter selezionando **Networking > SDDC-Datacenter** (Rete > Data center- Data center software-defined) e assicurarsi che il nuovo segmento sia visualizzato nell'elenco (in questo caso, "ls01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Verificare che il nuovo segmento di rete sia presente in vCenter.":::

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati i segmenti di rete NSX-T usati per le macchine virtuali in vCenter. A questo punto è possibile [creare una raccolta contenuto per distribuire macchine virtuali nella soluzione Azure VMware](deploy-vm-content-library.md). È anche possibile effettuare il provisioning di una VM nella rete creata in questa esercitazione.

Se non si vuole eseguire questa operazione, passare all'esercitazione successiva per ottenere informazioni su come creare il peering di Copertura globale ExpressRoute a un cloud privato in una soluzione Azure VMware.

> [!div class="nextstepaction"]
> [Associare gli ambienti locali a un cloud privato](tutorial-expressroute-global-reach-private-cloud.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
