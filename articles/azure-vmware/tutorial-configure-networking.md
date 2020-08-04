---
title: 'Esercitazione: Configurare la rete per il cloud privato VMware in Azure'
description: Informazioni su come creare e configurare la rete necessaria per distribuire il cloud privato in Azure
ms.topic: tutorial
ms.date: 07/22/2020
ms.openlocfilehash: aa4247f60c3e1ec54bfcde336d1ae8c8f70ff7a8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079428"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Esercitazione: Configurare la rete per il cloud privato VMware in Azure

Un cloud privato della soluzione Azure VMware (AVS) richiede una rete virtuale di Azure. Poiché la soluzione Azure VMware non supporta un'istanza locale di vCenter durante l'anteprima, è necessario completare altri passaggi per l'integrazione con l'ambiente locale. È inoltre necessario impostare un circuito ExpressRoute e un gateway di rete virtuale, come illustrato in questa esercitazione.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Crea rete virtuale
> * Creare un gateway di rete virtuale
> * Connettere il circuito ExpressRoute al gateway
> * Individuare gli URL per vCenter e NSX Manager

## <a name="prerequisites"></a>Prerequisiti 
Prima di poter creare una rete virtuale, assicurarsi di aver creato un [Cloud privato AVS](tutorial-create-private-cloud.md). 

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare al gruppo di risorse creato nell'[esercitazione sulla creazione di un cloud privato](tutorial-create-private-cloud.md) e selezionare **+ Aggiungi** per definire una nuova risorsa. 

1. Nella casella di testo **Cerca nel Marketplace** digitare **Rete virtuale**. Individuare la risorsa Rete virtuale e selezionarla.

1. Nella pagina **Rete virtuale** selezionare **Crea** per configurare la rete virtuale per il cloud privato.

1. Nella pagina **Crea rete virtuale** specificare i dettagli per la rete virtuale.

1. Nella scheda **Informazioni di base** immettere un nome per la rete virtuale e selezionare l'area appropriata, quindi selezionare **Avanti: Indirizzi IP**.

1. Nella scheda **Indirizzi IP**, in **Spazio indirizzi IPv4**, immettere lo spazio indirizzi creato nell'esercitazione precedente.

   > [!IMPORTANT]
   > È necessario usare uno spazio indirizzi che **non** si sovrappone a quello usato per creare il cloud privato nell'esercitazione precedente.

1. Selezionare **+ Aggiungi subnet**, quindi nella pagina **Aggiungi subnet** assegnare alla subnet un nome e un intervallo di indirizzi appropriato. Al termine selezionare **Aggiungi**.

1. Selezionare **Rivedi e crea**.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Creare una rete virtuale" border="true":::

1. Verificare le informazioni e selezionare **Crea**. Al termine della distribuzione, la rete virtuale verrà visualizzata nel gruppo di risorse.

## <a name="create-a-virtual-network-gateway"></a>Creare un gateway di rete virtuale

Dopo aver creato la rete virtuale, si procederà alla creazione di un gateway di rete virtuale.

1. Nel gruppo di risorse selezionare **+ Aggiungi** per aggiungere una nuova risorsa.

1. Nella casella di testo **Cerca nel Marketplace** digitare **Gateway di rete virtuale**. Individuare la risorsa Rete virtuale e selezionarla.

1. Nella pagina **Gateway di rete virtuale** selezionare **Crea**.

1. Nella scheda Informazioni di base della pagina **Crea gateway di rete virtuale** specificare i valori per i campi e quindi selezionare **Rivedi e crea**. 

   | Campo | valore |
   | --- | --- |
   | **Sottoscrizione** | Questo valore è già popolato con la sottoscrizione a cui appartiene il gruppo di risorse. |
   | **Gruppo di risorse** | Questo valore è già popolato con il gruppo di risorse corrente. Dovrebbe essere il gruppo di risorse creato nella sezione precedente. |
   | **Nome** | Immettere un nome univoco per il gateway di rete virtuale. |
   | **Area** | Selezionare la posizione geografica del gateway di rete virtuale. |
   | **Tipo di gateway** | selezionare **ExpressRoute**. |
   | **SKU** | Lasciare il valore predefinito **Standard**. |
   | **Rete virtuale** | Selezionare la rete virtuale creata in precedenza. Se la rete virtuale non è visibile, assicurarsi che l'area del gateway corrisponda all'area della rete virtuale. |
   | **Intervallo di indirizzi subnet del gateway** | Questo valore viene popolato quando si seleziona la rete virtuale. Non modificare il valore predefinito. |
   | **Indirizzo IP pubblico** | Selezionare **Crea nuovo**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Creare un gateway" border="true":::

1. Verificare che i dettagli siano corretti e selezionare **Crea** per avviare la distribuzione del gateway di rete virtuale. 
1. Al termine della distribuzione, passare alla sezione successiva per connettere ExpressRoute al gateway di rete virtuale che contiene il cloud privato AVS.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Connettere ExpressRoute al gateway di rete virtuale

Ora che è stato distribuito un gateway di rete virtuale, verrà aggiunta una connessione tra il gateway e il cloud privato AVS.

1. Passare al cloud privato creato nell'esercitazione precedente e selezionare **Connettività** in **Gestisci**, quindi selezionare la scheda **ExpressRoute**.

1. Copiare la chiave di autorizzazione. Se non è presente una chiave di autorizzazione, è necessario crearne una. A questo scopo, selezionare **+ Chiave di autorizzazione**

   :::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="Richiedere una chiave di autorizzazione" border="true":::

1. Passare al gateway di rete virtuale creato nel passaggio precedente e quindi, in **Impostazioni**, selezionare **Connessioni**. Nella pagina **Connessioni** selezionare **+ Aggiungi**.

1. Nella pagina **Aggiungi connessione** specificare i valori per i campi e fare clic su **OK**. 

   | Campo | valore |
   | --- | --- |
   | **Nome**  | Immettere un nome per la connessione.  |
   | **Tipo di connessione**  | selezionare **ExpressRoute**.  |
   | **Riscatta autorizzazione**  | Assicurarsi che questa casella sia selezionata.  |
   | **Gateway di rete virtuale** | Gateway di rete virtuale creato in precedenza.  |
   | **Chiave di autorizzazione**  | Copiare a incollare la chiave di autorizzazione dalla scheda ExpressRoute per il gruppo di risorse. |
   | **URI del circuito peer**  | Copiare e incollare l'ID ExpressRoute dalla scheda ExpressRoute per il gruppo di risorse.  |

   :::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="Aggiungere una connessione" border="true":::

Viene creata la connessione tra il circuito ExpressRoute e la rete virtuale.



## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Individuare gli URL per vCenter e NSX Manager

Per accedere a vCenter e NSX Manager sono necessari gli URL del client Web vCenter e del sito NSX-T Manager. 

Passare al cloud privato di AVS e quindi, in **Gestisci**, selezionare **Identità** per trovare le informazioni necessarie.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="Individuare gli URL di vCenter" border="true":::

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Crea rete virtuale
> * Creare un gateway di rete virtuale
> * Connettere il circuito ExpressRoute al gateway
> * Individuare gli URL per vCenter e NSX Manager

Passare all'esercitazione successiva per informazioni su come creare un jumpbox usato per connettersi all'ambiente in modo da gestire il cloud privato in locale.

> [!div class="nextstepaction"]
> [Accedere al cloud privato](tutorial-access-private-cloud.md)
