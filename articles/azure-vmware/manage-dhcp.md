---
title: Gestire DHCP per la soluzione VMware di Azure
description: Informazioni su come creare e gestire DHCP per il cloud privato della soluzione VMware di Azure.
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: 9143a8544fe1b98262c3e990ccdf56f5d5f65957
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335954"
---
# <a name="manage-dhcp-for-azure-vmware-solution"></a>Gestire DHCP per la soluzione VMware di Azure

Le applicazioni e i carichi di lavoro in esecuzione in un ambiente cloud privato richiedono servizi DHCP per le assegnazioni di indirizzi IP.  Questo articolo illustra come creare e gestire DHCP in una soluzione VMware di Azure in due modi:

- Se si usa NSX-T per ospitare il server DHCP, è necessario [creare un server DHCP](#create-a-dhcp-server) e [inoltrarlo a tale server](#create-dhcp-relay-service). Quando si crea il server DHCP, si aggiungerà anche un segmento di rete e si specificherà l'intervallo di indirizzi IP DHCP.   

- Se si usa un server DHCP esterno di terze parti nella rete, sarà necessario [creare il servizio di inoltro DHCP](#create-dhcp-relay-service). Quando si crea un inoltro a un server DHCP, se si usa NSX-T o una terza parte per ospitare il server DHCP, è necessario specificare l'intervallo di indirizzi IP DHCP.

>[!IMPORTANT]
>DHCP non funziona per le macchine virtuali (VM) nella rete di estensione VMware HCX L2 quando il server DHCP si trova nel Data Center locale.  NSX, per impostazione predefinita, impedisce a tutte le richieste DHCP di attraversare l'estensione L2. Per la soluzione, vedere la procedura [inviare richieste DHCP al server DHCP locale](#send-dhcp-requests-to-the-on-premises-dhcp-server) .


## <a name="create-a-dhcp-server"></a>Creazione di un server DHCP

Se si vuole usare NSX-T per ospitare il server DHCP, verrà creato un server DHCP. Si aggiungerà quindi un segmento di rete e si specificherà l'intervallo di indirizzi IP DHCP.

1. In NSX-T Manager selezionare **rete**  >  **DHCP** , quindi selezionare **Aggiungi server**.

1. Selezionare **DHCP** per il **tipo di server** , specificare il nome del server e l'indirizzo IP e quindi selezionare **Salva**.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Aggiungi server DHCP" border="true":::

1. Selezionare **gateway di livello 1** , selezionare i puntini di sospensione verticali sul gateway di primo livello e quindi scegliere **modifica**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Selezionare il gateway da usare" border="true":::

1. Selezionare **nessun set di allocazione IP** per aggiungere una subnet.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="aggiungere una subnet" border="true":::

1. Per **tipo** selezionare **server locale DHCP**. 
   
1. Per il **server DHCP** , selezionare **DHCP predefinito** e quindi fare clic su **Salva**.

1. Selezionare di nuovo **Salva** e quindi selezionare **Chiudi modifica**.

### <a name="add-a-network-segment"></a>Aggiungere un segmento di rete

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>Crea servizio di inoltro DHCP

Se si vuole usare un server DHCP esterno di terze parti, è necessario creare un servizio di inoltro DHCP. Specificare anche l'intervallo di indirizzi IP DHCP in NSX-T Manager. 

1. In NSX-T Manager selezionare **rete**  >  **DHCP** , quindi selezionare **Aggiungi server**.

1. Selezionare **inoltro DHCP** per il **tipo di server** , specificare il nome del server e l'indirizzo IP e quindi selezionare **Salva**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Crea servizio di inoltro DHCP" border="true":::

1. Selezionare **gateway di livello 1** , selezionare i puntini di sospensione verticali sul gateway di primo livello e quindi scegliere **modifica**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="modificare il gateway di livello 1" border="true":::

1. Selezionare **nessun set di allocazione IP** per definire l'allocazione degli indirizzi IP.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="modifica allocazione indirizzi IP" border="true":::

1. Per **tipo** selezionare **server DHCP**. 
   
1. Per il **server DHCP** , selezionare **inoltro DHCP** , quindi selezionare **Salva**.

1. Selezionare di nuovo **Salva** e quindi selezionare **Chiudi modifica**.


## <a name="specify-the-dhcp-ip-address-range"></a>Specificare l'intervallo di indirizzi IP DHCP

1. In NSX-T Manager selezionare segmenti di **rete**  >  **Segments**. 
   
1. Selezionare i puntini di sospensione verticali sul nome del segmento e scegliere **modifica**.
   
1. Selezionare **imposta subnet** per specificare l'indirizzo IP DHCP per la subnet. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="segmenti di rete" border="true":::
      
1. Modificare l'indirizzo IP del gateway, se necessario, e immettere l'indirizzo IP dell'intervallo DHCP. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="modifica subnet" border="true":::
      
1. Selezionare **applica** , quindi **Salva**. Al segmento viene assegnato un pool di server DHCP.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Pool di server DHCP assegnato a segmento" border="true":::


## <a name="send-dhcp-requests-to-the-on-premises-dhcp-server"></a>Inviare richieste DHCP al server DHCP locale

Se si desidera inviare richieste DHCP dalle macchine virtuali della soluzione VMware di Azure nel segmento L2 esteso al server DHCP locale, verrà creato un profilo di segmento di sicurezza. 

1. Accedere all'istanza locale di vCenter e in **Home** selezionare **HCX**.

1. Selezionare l' **estensione di rete** in **Servizi**.

1. Selezionare l'estensione di rete a cui si vogliono supportare le richieste DHCP dalla soluzione VMware di Azure in locale. 

1. Prendere nota del nome della rete di destinazione.  

   :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="Screenshot di un'estensione di rete nel client VMware vSphere" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. Nella soluzione VMware di Azure NSX-T Manager selezionare **Network**  >  **Segments**  >  **Segment Profiles**. 

1. Selezionare **Aggiungi profilo segmento** e quindi **sicurezza segmento**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="Screenshot dell'aggiunta di un profilo di segmento in NSX-T" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. Specificare un nome e un tag, quindi impostare il **filtro BPDU** su on e tutti gli elementi DHCP attivati.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="Screenshot che mostra il filtro BPDU attivato e l'opzione DHCP disattiva" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::

1. Rimuovere tutti gli eventuali indirizzi MAC nell' **elenco Consenti filtro BPDU**.  Selezionare quindi **Salva**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-allow-list.png" alt-text="Screenshot che Mostra gli indirizzi MAC nell'elenco Consenti filtro BPDU":::

1. **Networking**  >  **Segments**  >  In **segmenti** di rete segmenti, nell'area di ricerca, immettere il nome di rete della definizione.

   :::image type="content" source="media/manage-dhcp/networking-segments-search.png" alt-text="Screenshot del campo di filtro dei segmenti di > di rete":::

1. Selezionare i puntini di sospensione verticali sul nome del segmento e scegliere **modifica**.

   :::image type="content" source="media/manage-dhcp/edit-network-segment.png" alt-text="Screenshot del pulsante modifica per il segmento" lightbox="media/manage-dhcp/edit-network-segment.png":::

1. Modificare la **sicurezza del segmento** sul profilo del segmento creato in precedenza.

   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="Screenshot del campo di sicurezza segmento" lightbox="media/manage-dhcp/edit-segment-security.png":::

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [gestione del ciclo di vita e della manutenzione degli host](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management).