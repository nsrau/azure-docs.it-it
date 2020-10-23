---
title: Come creare e gestire DHCP
description: Questo articolo illustra come gestire DHCP in una soluzione VMware di Azure.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb74ed4474cc14081e59142f2f60915fccd47559
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461057"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>Come creare e gestire DHCP in una soluzione VMware di Azure

NSX-T offre la possibilità di configurare DHCP per il cloud privato. Se si usa NSX-T per ospitare il server DHCP, vedere [creare un server DHCP](#create-dhcp-server). In caso contrario, se si dispone di un server DHCP esterno di terze parti nella rete, vedere [creare un servizio di inoltro DHCP](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Crea server DHCP

Usare la procedura seguente per configurare un server DHCP in NSX-T.

1. In gestione NSX, passare alla scheda **rete** e selezionare **DHCP**. 
1. Selezionare **Aggiungi server** , quindi specificare il nome del server e l'indirizzo IP. 
1. Selezionare **Salva**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Aggiungi server DHCP" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Connettere il server DHCP al gateway di primo livello.

1. Selezionare **gateway di livello 1**, il gateway dall'elenco e quindi fare clic su **modifica**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Aggiungi server DHCP" border="true":::

1. Selezionare **nessun set di allocazione IP** per aggiungere una subnet.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Aggiungi server DHCP" border="true":::

1. Selezionare **DHCP local server** per il **tipo**. 
1. Selezionare **DHCP predefinito** per il **server DHCP** e quindi fare clic su **Salva**.


1. Nella finestra del **gateway di primo livello** selezionare **Salva**. 
1. Selezionare **Chiudi modifica** per terminare.

### <a name="add-a-network-segment"></a>Aggiungere un segmento di rete

Dopo aver creato il server DHCP, è necessario aggiungervi segmenti di rete.

1. In NSX-T Selezionare la scheda **rete** e selezionare **segmenti** in **connettività**. 
1. Selezionare **Aggiungi segmento** e denominare il segmento e la connessione al gateway di primo livello. 
1. Selezionare **imposta subnet** per configurare una nuova subnet. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="Aggiungi server DHCP" border="true":::

1. Nella finestra **imposta subnet** selezionare **Aggiungi Subnet**. 
1. Immettere l'indirizzo IP del gateway e l'intervallo DHCP, quindi selezionare **Aggiungi** e quindi **applica** .

1. Selezionare **Save (Salva** ) per aggiungere il nuovo segmento di rete.

## <a name="create-dhcp-relay-service"></a>Crea servizio di inoltro DHCP

1. Selezionare la scheda **rete** e in **gestione IP**Selezionare **DHCP**. 
1. Selezionare **Aggiungi server**. 
1. Selezionare inoltro DHCP per il **tipo di server** e immettere il nome del server e l'indirizzo IP per il server di inoltro. 
1. Selezionare **Salva**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Aggiungi server DHCP" border="true":::

1. Selezionare **gateway di primo livello** in **connettività**. 
1. Selezionare i puntini di sospensione verticali sul gateway di primo livello e selezionare **modifica**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Aggiungi server DHCP" border="true":::

1. Selezionare **nessun set di allocazione IP** per definire l'allocazione degli indirizzi IP.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="Aggiungi server DHCP" border="true":::

1. Selezionare **server di inoltro DHCP** per **tipo**.
1. Selezionare il server di inoltro DHCP per l' **inoltro DHCP**. 
1. Selezionare **Salva**.


## <a name="specify-a-dhcp-range-ip-on-a-segment"></a>Specificare un indirizzo IP dell'intervallo DHCP in un segmento

> [!NOTE]
> Questa configurazione è necessaria per realizzare la funzionalità di inoltro DHCP nel segmento client DHCP. 

1. In **connettività**selezionare **segmenti**. 
1. Selezionare i puntini di sospensione verticali e scegliere **modifica**. 

   >[!TIP]
   >Se si desidera aggiungere un nuovo segmento, selezionare **Aggiungi segmento**.

1. Aggiungere i dettagli sul segmento. 
1. Selezionare il valore in **imposta subnet** per aggiungere o modificare la subnet.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="Aggiungi server DHCP" border="true":::

1. Selezionare i puntini di sospensione verticali e scegliere **modifica**. Se è necessario creare una nuova subnet, selezionare **Aggiungi subnet** per creare un gateway e configurare un intervallo DHCP. 
1. Specificare l'intervallo del pool IP e selezionare **applica**, quindi selezionare **Salva** .

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Aggiungi server DHCP" border="true":::

   Al segmento viene assegnato un pool di server DHCP.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Aggiungi server DHCP" border="true":::
