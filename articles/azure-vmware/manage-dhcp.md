---
title: Come gestire DHCP
description: Questo articolo illustra come gestire DHCP in una soluzione VMWare di Azure (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ccf28c94e1991681c238f51847fe228313abe29e
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740447"
---
# <a name="how-to-manage-dhcp-in-azure-vmware-solution-avs-preview"></a>Come gestire DHCP in Azure (AVS) Preview della soluzione VMWare

NSX-T offre la possibilità di configurare DHCP per il cloud privato. Se si prevede di usare NSX-T per ospitare il server DHCP, vedere [creare un server DHCP](#create-dhcp-server). In caso contrario, se si dispone di un server DHCP esterno di terze parti nella rete e si desidera inoltrare le richieste a tale server DHCP, vedere [creare un servizio di inoltro DHCP](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Crea server DHCP

Usare la procedura seguente per configurare un server DHCP in NSX-T.

Da gestione NSX, passare alla scheda **rete** e selezionare **DHCP** in **Gestione indirizzi IP**. Selezionare il pulsante **Aggiungi server** . Specificare quindi il nome del server e l'indirizzo IP del server. Al termine, selezionare **Salva**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Aggiungi server DHCP" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Connettere il server DHCP al gateway di primo livello.

Selezionare **gateway di livello 1**, selezionare il gateway e scegliere **modifica** .

:::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Selezionare il gateway da usare" border="true":::

Aggiungere una subnet selezionando **nessun set di allocazione IP**

:::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="aggiungere una subnet" border="true":::

Nella schermata successiva selezionare **server locale DHCP** dall'elenco a discesa **tipo** . Per **server DHCP**, selezionare **DHCP predefinito** e selezionare **Salva**.

:::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="Selezionare le opzioni per il server DHCP" border="true":::

Nella finestra del **gateway di primo livello** selezionare **Salva**. Nella schermata successiva verranno visualizzate **le modifiche salvate**, selezionare **Chiudi modifica** per terminare.

### <a name="add-a-network-segment"></a>Aggiungere un segmento di rete

Dopo aver creato il server DHCP, è necessario aggiungervi segmenti di rete.

In NSX-T Selezionare la scheda **rete** e selezionare **segmenti** in **connettività**. Selezionare **Aggiungi segmento**. Denominare il segmento e la connessione al gateway di primo livello. Selezionare quindi **imposta subnet** per configurare una nuova subnet. 

:::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="aggiungere un nuovo segmento di rete" border="true":::

Nella finestra **imposta subnet** selezionare **Aggiungi Subnet**. Immettere l'indirizzo IP del gateway e l'intervallo DHCP, quindi selezionare **Aggiungi** e quindi **applica** .

:::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="Aggiungi segmento di rete" border="true":::

Al termine, selezionare **Salva** per completare l'aggiunta di un segmento di rete.

:::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="segmenti completati" border="true":::

## <a name="create-dhcp-relay-service"></a>Crea servizio di inoltro DHCP

Nella finestra NXT-T Selezionare la scheda **rete** e in **gestione IP**Selezionare **DHCP**. Selezionare **Aggiungi server**. Scegliere inoltro DHCP per il **tipo di server** e immettere il nome del server e l'indirizzo IP per il server di inoltro. Fare clic su **Salva** per salvare le modifiche.

:::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Crea server di inoltro DHCP" border="true":::

Selezionare **gateway di primo livello** in **connettività**. Selezionare i puntini di sospensione verticali sul gateway di primo livello e scegliere **modifica**.

:::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="modificare il gateway di livello 1" border="true":::

Selezionare **nessun set di allocazione IP** per definire l'allocazione degli indirizzi IP.

:::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="modifica allocazione indirizzi IP" border="true":::

Nella finestra di dialogo selezionare **server di inoltro DHCP**per **tipo**. Nell'elenco a discesa **inoltro DHCP** selezionare il server di inoltro DHCP. Al termine, selezionare **Salva** .

:::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="configurare la gestione degli indirizzi IP" border="true":::

Specificare un indirizzo IP dell'intervallo DHCP nel segmento:

> [!NOTE]
> Questa configurazione è necessaria per realizzare la funzionalità di inoltro DHCP nel segmento client DHCP. 

In **connettività**selezionare **segmenti**. Selezionare i puntini di sospensione verticali e scegliere **modifica**. Se invece si desidera aggiungere un nuovo segmento, è possibile selezionare **Aggiungi segmento** per creare un nuovo segmento.

:::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="modificare una subnet di rete" border="true":::

Aggiungere i dettagli sul segmento. Selezionare il valore in **subnet** o **imposta Subnet** per aggiungere o modificare la subnet.

:::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="segmenti di rete" border="true":::

Selezionare i puntini di sospensione verticali e scegliere **modifica**. Se è necessario creare una nuova subnet, selezionare **Aggiungi subnet** per creare un gateway e configurare un intervallo DHCP. Specificare l'intervallo del pool IP e selezionare **applica**, quindi selezionare **Salva** .

:::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="modifica subnet" border="true":::

A questo punto viene assegnato un pool di server DHCP al segmento.

:::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Pool di server DHCP assegnato a segmento" border="true":::
