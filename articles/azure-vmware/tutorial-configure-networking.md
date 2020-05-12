---
title: Esercitazione - Configurare la rete per il cloud privato VMWare in Azure
description: Informazioni su come creare e configurare la rete necessaria per distribuire il cloud privato in Azure
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 41043b98a6e270d8d9f4373de7876b3fcae86747
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837618"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Esercitazione: Configurare la rete per il cloud privato VMWare in Azure

Un cloud privato della soluzione Azure VMware (AVS) richiede una rete virtuale. Poiché la soluzione AVS non supporta un'istanza locale di vCenter durante l'anteprima, è necessario completare altri passaggi per l'integrazione con l'ambiente locale. È inoltre necessario impostare un circuito ExpressRoute e un gateway di rete virtuale, come illustrato in questa esercitazione.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una rete virtuale
> * Creare un gateway di rete virtuale
> * Connettere il circuito ExpressRoute al gateway
> * Individuare gli URL per vCenter e NSX Manager

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Passare al gruppo di risorse creato nell'[esercitazione precedente](tutorial-create-private-cloud.md) e selezionare **+ Aggiungi** per definire una nuova risorsa.

Nella casella di testo **Cerca nel Marketplace** digitare **Rete virtuale**. Individuare la risorsa Rete virtuale e selezionarla.

Nella pagina Rete virtuale selezionare **Crea** per configurare la rete virtuale per il cloud privato.

Nella pagina **Crea rete virtuale** immettere i dettagli appropriati per la rete virtuale. La tabella seguente include una descrizione delle proprietà:

> [!IMPORTANT]
> È necessario usare uno spazio indirizzi che **non** si sovrappone a quello usato per creare il cloud privato nell'esercitazione precedente.

Nella scheda **Informazioni di base** immettere un nome per la rete virtuale e selezionare l'area appropriata, quindi selezionare **Avanti: Indirizzi IP**

Nella scheda **Indirizzi IP**, in **Spazio indirizzi IPv4**, immettere lo spazio indirizzi creato nell'esercitazione precedente.

Selezionare **+ Aggiungi subnet**, quindi nella pagina **Aggiungi subnet** assegnare alla subnet un nome e un intervallo di indirizzi appropriato. Al termine selezionare **Aggiungi**.

Selezionare **Rivedi e crea**

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Creare una rete virtuale" border="true":::

Verificare le informazioni e selezionare **Crea**. Al termine della distribuzione, la rete virtuale verrà visualizzata nel gruppo di risorse.

## <a name="create-a-virtual-network-gateway"></a>Creare un gateway di rete virtuale

Nella sezione precedente è stata creata una rete virtuale. Verrà ora creato un gateway di rete virtuale.

Nel gruppo di risorse selezionare **+ Aggiungi** per aggiungere una nuova risorsa.

Nella casella di testo **Cerca nel Marketplace** digitare **Gateway di rete virtuale**. Individuare la risorsa Rete virtuale e selezionarla.

Nella pagina **Gateway di rete virtuale** selezionare **Crea**.

Nella scheda Informazioni di base della pagina **Crea gateway di rete virtuale** specificare i valori per i campi. Le descrizioni dei campi sono riportate nella tabella seguente:

| Campo | valore |
| --- | --- |
| **Sottoscrizione** | Questo valore è già popolato con la sottoscrizione a cui appartiene il gruppo di risorse. |
| **Gruppo di risorse** | Questo valore è già popolato con il gruppo di risorse corrente. Dovrebbe essere il gruppo di risorse creato nella sezione precedente. |
| **Nome** | Immettere un nome univoco per il gateway di rete virtuale. |
| **Area** | Selezionare la posizione geografica del gateway di rete virtuale. |
| **Tipo di gateway** | selezionare **ExpressRoute**. |
| **Tipo di VPN** | Selezionare **Basato su route**. |
| **SKU** | Lasciare il valore predefinito **Standard**. |
| **Rete virtuale** | Selezionare la rete virtuale creata in precedenza. Se la rete virtuale non è visibile, assicurarsi che l'area del gateway corrisponda all'area della rete virtuale. |
| **Intervallo di indirizzi subnet del gateway** | Questo valore viene popolato quando si seleziona la rete virtuale. Non modificare il valore predefinito. |
| **Indirizzo IP pubblico** | Selezionare **Crea nuovo**. |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Creare un gateway" border="true":::

Selezionare **Verifica e crea**, nella pagina successiva verificare che i dettagli siano corretti e selezionare **Crea** per avviare la distribuzione del gateway di rete virtuale. Al termine della distribuzione, passare alla sezione successiva di questa esercitazione per connettere ExpressRoute alla rete virtuale che contiene il cloud privato.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Connettere ExpressRoute al gateway di rete virtuale

Questa sezione illustra come aggiungere una connessione tra il cloud privato di AVS e il gateway di rete virtuale creato.

Passare al cloud privato creato nell'esercitazione precedente e selezionare **Connettività** in **Gestisci**, quindi selezionare la scheda **ExpressRoute**.

Copiare la chiave di autorizzazione. Se non è presente una chiave di autorizzazione, è necessario crearne una. A questo scopo, selezionare **+ Chiave di autorizzazione**

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="Richiedere una chiave di autorizzazione" border="true":::

Passare al gateway di rete virtuale creato nel passaggio precedente e quindi, in **Impostazioni**, selezionare **Connessioni**. Nella pagina **Connessioni** selezionare **+ Aggiungi**.

Nella pagina **Aggiungi connessione** specificare i valori per i campi. Le descrizioni dei campi sono riportate nella tabella seguente:

| Campo | valore |
| --- | --- |
| **Nome**  | Immettere un nome per la connessione.  |
| **Tipo di connessione**  | selezionare **ExpressRoute**.  |
| **Riscatta autorizzazione**  | Assicurarsi che questa casella sia selezionata.  |
| **Gateway di rete virtuale** | Il gateway di rete virtuale creato in precedenza  |
| **Chiave di autorizzazione**  | Copiare a incollare la chiave di autorizzazione dalla scheda ExpressRoute per il gruppo di risorse. |
| **URI del circuito peer**  | Copiare e incollare l'ID ExpressRoute dalla scheda ExpressRoute per il gruppo di risorse.  |

Selezionare **OK**. Viene creata la connessione tra il circuito ExpressRoute e la rete virtuale.

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="Aggiungere una connessione" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Individuare gli URL per vCenter e NSX Manager

Per accedere a vCenter e NSX Manager sono necessari gli URL del client Web vCenter e del sito NSX-T Manager. Per trovare gli URL:

Passare al cloud privato di AVS e quindi, in **Gestisci**, selezionare **Identità** per trovare le informazioni necessarie.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="Individuare gli URL di vCenter" border="true":::

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare una rete virtuale
> * Creare un gateway di rete virtuale
> * Connettere il circuito ExpressRoute al gateway
> * Individuare gli URL per vCenter e NSX Manager

Passare all'esercitazione successiva per informazioni su come creare un jumpbox usato per connettersi all'ambiente in modo da gestire il cloud privato in locale.

> [!div class="nextstepaction"]
> [Accedere al cloud privato](tutorial-access-private-cloud.md)
